---
title: Analysera Twitter-data med Apache Hive - Azure HDInsight
description: Lär dig hur du använder Apache Hive och Apache Hadoop på HDInsight för att omvandla råa TWitter-data till en sökbar Hive-tabell.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.date: 12/16/2019
ms.openlocfilehash: f3705170be28f33e5994bd00e363dc7ec7f94642
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75435619"
---
# <a name="analyze-twitter-data-using-apache-hive-and-apache-hadoop-on-hdinsight"></a>Analysera Twitter-data med Apache Hive och Apache Hadoop på HDInsight

Läs om hur du använder [Apache Hive](https://hive.apache.org/) för att bearbeta Twitter-data. Resultatet är en lista över Twitter-användare som skickade flest tweets som innehåller ett visst ord.

> [!IMPORTANT]  
> Stegen i det här dokumentet testades på HDInsight 3.6.

## <a name="get-the-data"></a>Hämta data

Twitter kan du hämta data för varje tweet som en JavaScript Object Notation (JSON) dokument via en REST API. [OAuth](https://oauth.net) krävs för autentisering till API:et.

### <a name="create-a-twitter-application"></a>Skapa ett Twitter-program

1. Logga in på [https://developer.twitter.com/apps/](https://developer.twitter.com/apps/). Välj länken Registrera dig nu om du inte har ett **Twitter-konto.**

2. Välj **Skapa ny app**.

3. Ange **namn,** **beskrivning**, **webbplats**. Du kan skapa en webbadress för fältet **Webbplats.** I följande tabell visas några exempelvärden som ska användas:

   | Field | Värde |
   |--- |--- |
   | Namn |MyHDInsightApp |
   | Beskrivning |MyHDInsightApp |
   | Webbplats |`https://www.myhdinsightapp.com` |

4. Välj **Ja, jag håller med**och välj sedan Skapa din **Twitter-program**.

5. Välj fliken **Behörigheter.** Standardbehörigheten är **Skrivskyddad**.

6. Välj fliken **Nycklar och åtkomsttokens.**

7. Välj **Skapa min åtkomsttoken**.

8. Välj **Testa OAuth** i det övre högra hörnet på sidan.

9. Skriv ned **konsumentnyckel**, **Konsumenthemlighet,** **Åtkomsttoken**och **Åtkomsttoken hemlighet**.

### <a name="download-tweets"></a>Ladda ner tweets

Följande Python-kod hämtar 10.000 tweets från Twitter och spara dem till en fil som heter **tweets.txt**.

> [!NOTE]  
> Följande steg utförs på HDInsight-klustret, eftersom Python redan är installerat.

1. Använd kommandot ssh för att ansluta till [klustret.](./hdinsight-hadoop-linux-use-ssh-unix.md) Redigera kommandot nedan genom att ersätta CLUSTERNAME med namnet på klustret och ange sedan kommandot:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Använd följande kommandon för att installera [Tweepy,](https://www.tweepy.org/) [Progress bar](https://pypi.python.org/pypi/progressbar/2.2)och andra paket som krävs:

   ```bash
   sudo apt install python-dev libffi-dev libssl-dev
   sudo apt remove python-openssl
   python -m pip install virtualenv
   mkdir gettweets
   cd gettweets
   virtualenv gettweets
   source gettweets/bin/activate
   pip install tweepy progressbar pyOpenSSL requests[security]
   ```

1. Använd följande kommando för att skapa en fil med namnet **gettweets.py:**

   ```bash
   nano gettweets.py
   ```

1. Redigera koden nedan `Your consumer secret`genom `Your consumer key` `Your access token`att `Your access token secret` ersätta , , och med relevant information från din Twitter ansökan. Klistra sedan in den redigerade koden som innehållet i **gettweets.py** filen.

   ```python
   #!/usr/bin/python

   from tweepy import Stream, OAuthHandler
   from tweepy.streaming import StreamListener
   from progressbar import ProgressBar, Percentage, Bar
   import json
   import sys

   #Twitter app information
   consumer_secret='Your consumer secret'
   consumer_key='Your consumer key'
   access_token='Your access token'
   access_token_secret='Your access token secret'

   #The number of tweets we want to get
   max_tweets=100

   #Create the listener class that receives and saves tweets
   class listener(StreamListener):
       #On init, set the counter to zero and create a progress bar
       def __init__(self, api=None):
           self.num_tweets = 0
           self.pbar = ProgressBar(widgets=[Percentage(), Bar()], maxval=max_tweets).start()

       #When data is received, do this
       def on_data(self, data):
           #Append the tweet to the 'tweets.txt' file
           with open('tweets.txt', 'a') as tweet_file:
               tweet_file.write(data)
               #Increment the number of tweets
               self.num_tweets += 1
               #Check to see if we have hit max_tweets and exit if so
               if self.num_tweets >= max_tweets:
                   self.pbar.finish()
                   sys.exit(0)
               else:
                   #increment the progress bar
                   self.pbar.update(self.num_tweets)
           return True

       #Handle any errors that may occur
       def on_error(self, status):
           print status

   #Get the OAuth token
   auth = OAuthHandler(consumer_key, consumer_secret)
   auth.set_access_token(access_token, access_token_secret)
   #Use the listener class for stream processing
   twitterStream = Stream(auth, listener())
   #Filter for these topics
   twitterStream.filter(track=["azure","cloud","hdinsight"])
   ```

    > [!TIP]  
    > Justera ämnesfiltret på den sista raden för att spåra populära sökord. Med hjälp av sökord som är populära när du kör skriptet möjliggör snabbare insamling av data.

1. Använd **Ctrl + X**och sedan **Y** för att spara filen.

1. Använd följande kommando för att köra filen och hämta tweets:

    ```bash
    python gettweets.py
    ```

    En förloppsindikator visas. Det räknas upp till 100% som tweets laddas ner.

   > [!NOTE]  
   > Om det tar lång tid för förloppsindikatorn att avancera bör du ändra filtret för att spåra populära ämnen. När det finns många tweets om ämnet i ditt filter, kan du snabbt få 100 tweets behövs.

### <a name="upload-the-data"></a>Ladda upp data

Om du vill överföra data till HDInsight-lagring använder du följande kommandon:

```bash
hdfs dfs -mkdir -p /tutorials/twitter/data
hdfs dfs -put tweets.txt /tutorials/twitter/data/tweets.txt
```

Dessa kommandon lagrar data på en plats som alla noder i klustret kan komma åt.

## <a name="run-the-hiveql-job"></a>Kör HiveQL-jobbet

1. Använd följande kommando för att skapa en fil som innehåller [HiveQL-satser:](https://cwiki.apache.org/confluence/display/Hive/LanguageManual)

   ```bash
   nano twitter.hql
   ```

    Använd följande text som innehållet i filen:

   ```hiveql
   set hive.exec.dynamic.partition = true;
   set hive.exec.dynamic.partition.mode = nonstrict;
   -- Drop table, if it exists
   DROP TABLE tweets_raw;
   -- Create it, pointing toward the tweets logged from Twitter
   CREATE EXTERNAL TABLE tweets_raw (
       json_response STRING
   )
   STORED AS TEXTFILE LOCATION '/tutorials/twitter/data';
   -- Drop and recreate the destination table
   DROP TABLE tweets;
   CREATE TABLE tweets
   (
       id BIGINT,
       created_at STRING,
       created_at_date STRING,
       created_at_year STRING,
       created_at_month STRING,
       created_at_day STRING,
       created_at_time STRING,
       in_reply_to_user_id_str STRING,
       text STRING,
       contributors STRING,
       retweeted STRING,
       truncated STRING,
       coordinates STRING,
       source STRING,
       retweet_count INT,
       url STRING,
       hashtags array<STRING>,
       user_mentions array<STRING>,
       first_hashtag STRING,
       first_user_mention STRING,
       screen_name STRING,
       name STRING,
       followers_count INT,
       listed_count INT,
       friends_count INT,
       lang STRING,
       user_location STRING,
       time_zone STRING,
       profile_image_url STRING,
       json_response STRING
   );
   -- Select tweets from the imported data, parse the JSON,
   -- and insert into the tweets table
   FROM tweets_raw
   INSERT OVERWRITE TABLE tweets
   SELECT
       cast(get_json_object(json_response, '$.id_str') as BIGINT),
       get_json_object(json_response, '$.created_at'),
       concat(substr (get_json_object(json_response, '$.created_at'),1,10),' ',
       substr (get_json_object(json_response, '$.created_at'),27,4)),
       substr (get_json_object(json_response, '$.created_at'),27,4),
       case substr (get_json_object(json_response,    '$.created_at'),5,3)
           when "Jan" then "01"
           when "Feb" then "02"
           when "Mar" then "03"
           when "Apr" then "04"
           when "May" then "05"
           when "Jun" then "06"
           when "Jul" then "07"
           when "Aug" then "08"
           when "Sep" then "09"
           when "Oct" then "10"
           when "Nov" then "11"
           when "Dec" then "12" end,
       substr (get_json_object(json_response, '$.created_at'),9,2),
       substr (get_json_object(json_response, '$.created_at'),12,8),
       get_json_object(json_response, '$.in_reply_to_user_id_str'),
       get_json_object(json_response, '$.text'),
       get_json_object(json_response, '$.contributors'),
       get_json_object(json_response, '$.retweeted'),
       get_json_object(json_response, '$.truncated'),
       get_json_object(json_response, '$.coordinates'),
       get_json_object(json_response, '$.source'),
       cast (get_json_object(json_response, '$.retweet_count') as INT),
       get_json_object(json_response, '$.entities.display_url'),
       array(
           trim(lower(get_json_object(json_response, '$.entities.hashtags[0].text'))),
           trim(lower(get_json_object(json_response, '$.entities.hashtags[1].text'))),
           trim(lower(get_json_object(json_response, '$.entities.hashtags[2].text'))),
           trim(lower(get_json_object(json_response, '$.entities.hashtags[3].text'))),
           trim(lower(get_json_object(json_response, '$.entities.hashtags[4].text')))),
       array(
           trim(lower(get_json_object(json_response, '$.entities.user_mentions[0].screen_name'))),
           trim(lower(get_json_object(json_response, '$.entities.user_mentions[1].screen_name'))),
           trim(lower(get_json_object(json_response, '$.entities.user_mentions[2].screen_name'))),
           trim(lower(get_json_object(json_response, '$.entities.user_mentions[3].screen_name'))),
           trim(lower(get_json_object(json_response, '$.entities.user_mentions[4].screen_name')))),
       trim(lower(get_json_object(json_response, '$.entities.hashtags[0].text'))),
       trim(lower(get_json_object(json_response, '$.entities.user_mentions[0].screen_name'))),
       get_json_object(json_response, '$.user.screen_name'),
       get_json_object(json_response, '$.user.name'),
       cast (get_json_object(json_response, '$.user.followers_count') as INT),
       cast (get_json_object(json_response, '$.user.listed_count') as INT),
       cast (get_json_object(json_response, '$.user.friends_count') as INT),
       get_json_object(json_response, '$.user.lang'),
       get_json_object(json_response, '$.user.location'),
       get_json_object(json_response, '$.user.time_zone'),
       get_json_object(json_response, '$.user.profile_image_url'),
       json_response
   WHERE (length(json_response) > 500);
   ```

1. Tryck på **Ctrl + X**och tryck sedan på **Y** för att spara filen.

1. Använd följande kommando för att köra HiveQL som finns i filen:

   ```bash
   beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http' -i twitter.hql
   ```

    Det här kommandot kör **filen twitter.hql.** När frågan är klar visas `jdbc:hive2//localhost:10001/>` en fråga.

1. Från beeline-prompten använder du följande fråga för att kontrollera att data har importerats:

   ```hiveql
   SELECT name, screen_name, count(1) as cc
   FROM tweets
   WHERE text like "%Azure%"
   GROUP BY name,screen_name
   ORDER BY cc DESC LIMIT 10;
   ```

    Den här frågan returnerar högst 10 tweets som innehåller ordet **Azure** i meddelandetexten.

    > [!NOTE]  
    > Om du har ändrat `gettweets.py` filtret i skriptet ersätter du **Azure** med ett av de filter som du använde.

## <a name="next-steps"></a>Nästa steg

Du har lärt dig att omvandla en ostrukturerad JSON-datauppsättning till en strukturerad [Apache Hive-tabell.](https://hive.apache.org/) Mer information om Hive på HDInsight finns i följande dokument:

* [Kom igång med HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Analysera flygfördröjningsdata med HDInsight](/azure/hdinsight/interactive-query/interactive-query-tutorial-analyze-flight-data)
