---
title: 'Självstudiekurs: Avvikelseidentifiering vid direktuppspelning av data med Hjälp av Azure Databricks'
titleSuffix: Azure Cognitive Services
description: Lär dig hur du använder API:et för avvikelsedetektor och Azure Databricks för att övervaka avvikelser i dina data.
titlesuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: tutorial
ms.date: 03/05/2020
ms.author: aahi
ms.openlocfilehash: e0df0773daf8f9be21ac70d8390013adfd93483a
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "78402667"
---
# <a name="tutorial-anomaly-detection-on-streaming-data-using-azure-databricks"></a>Självstudiekurs: Avvikelseidentifiering vid direktuppspelning av data med Hjälp av Azure Databricks

[Azure Databricks](https://azure.microsoft.com/services/databricks/) är en snabb, enkel och samarbetsinriktad Apache Spark-baserad analystjänst. Api:et för avvikelsedetektor, en del av Azure Cognitive Services, är ett sätt att övervaka dina tidsseriedata. Använd den här självstudien för att köra avvikelseidentifiering på en dataström i nära realtid med Azure Databricks. Du matar in Twitter-data med Azure Event Hubs och importerar dem till Azure Databricks med Spark Event Hubs-kopplingen. Efteråt ska du använda API:et för att identifiera avvikelser på strömmade data. 

Följande bild visar programflödet:

![Azure Databricks med eventhubbar och kognitiva tjänster](../media/tutorials/databricks-cognitive-services-tutorial.png "Azure Databricks med eventhubbar och kognitiva tjänster")

Den här självstudien omfattar följande uppgifter:

> [!div class="checklist"]
> * Skapa en Azure Databricks-arbetsyta
> * Skapa ett Spark-kluster i Azure Databricks
> * Skapa en Twitter-app om du vill ha tillgång strömmande data
> * Skapa anteckningsböcker i Azure Databricks
> * Bifoga bibliotek för Event Hubs och Twitter-API
> * Skapa en avvikelsedetektorresurs och hämta åtkomstnyckeln
> * Skicka tweets till Event Hubs
> * Läs tweets från Event Hubs
> * Kör avvikelseidentifiering på tweets

> [!Note]
> * Den här självstudien introducerar en metod för att implementera den rekommenderade [lösningsarkitekturen](https://azure.microsoft.com/solutions/architecture/anomaly-detector-process/) för API:et för avvikelsedetektor.
> * Den här självstudien kan inte slutföras med en kostnadsfri utvärderingsversion för API:et för avvikelsedetektorn eller Azure Databricks. 

Skapa en [Azure-prenumeration](https://azure.microsoft.com/free/) om du inte har en.

## <a name="prerequisites"></a>Krav

- Ett namnområde och händelsenav för [Azure-händelsehubben.](https://docs.microsoft.com/azure/event-hubs/event-hubs-create)

- [Anslutningssträngen](../../../event-hubs/event-hubs-get-connection-string.md) för att komma åt namnområdet Event Hubs. Anslutningssträngen bör ha ett liknande format som:

    `Endpoint=sb://<namespace>.servicebus.windows.net/;SharedAccessKeyName=<key name>;SharedAccessKey=<key value>`. 

- Principnamnet och principnyckeln för delad åtkomst för händelsehubbar.

Mer information om hur du skapar ett namnområde och en händelsenav finns i [snabbstarten](../../../event-hubs/event-hubs-create.md) för Azure Event Hubs.

## <a name="create-an-azure-databricks-workspace"></a>Skapa en Azure Databricks-arbetsyta

I det här avsnittet skapar du en Azure Databricks-arbetsyta med Hjälp av [Azure-portalen](https://portal.azure.com/).

1. I Azure-portalen väljer du **Skapa en resurs** > **Analytics** > **Azure Databricks**.

    ![Databricks på Azure-portalen](../media/tutorials/azure-databricks-on-portal.png "Databricks på Azure-portalen")

3. Under **Azure Databricks-tjänst** anger du följande värden för att skapa en Databricks-arbetsyta:


    |Egenskap  |Beskrivning  |
    |---------|---------|
    |**Namn på arbetsyta**     | Ange ett namn för Databricks-arbetsytan        |
    |**Prenumeration**     | I listrutan väljer du din Azure-prenumeration.        |
    |**Resursgrupp**     | Ange om du vill skapa en ny resursgrupp eller använda en befintlig. En resursgrupp är en container som innehåller relaterade resurser för en Azure-lösning. Mer information finns i [översikten över Azure-resursgrupper](../../../azure-resource-manager/management/overview.md). |
    |**Location**     | Välj **Östra USA 2** eller någon annan tillgänglig region. Se [Azure-tjänster som är tillgängliga per region](https://azure.microsoft.com/regions/services/) för regiontillgänglighet.        |
    |**Prisnivå**     |  Välj mellan **Standard** och **Premium**. Välj INTE **Rättegång**. Mer information om de här nivåerna finns på [prissättningssidan för Databricks](https://azure.microsoft.com/pricing/details/databricks/).       |

    Välj **Skapa**.

4. Det tar några minuter att skapa arbetsytan. 

## <a name="create-a-spark-cluster-in-databricks"></a>Skapa ett Spark-kluster i Databricks

1. I Azure-portalen går du till Databricks-arbetsytan som du skapade. Välj sedan **Starta arbetsyta**.

2. Du omdirigeras till Azure Databricks-portalen. Välj **Nytt kluster**i portalen .

    ![Databricks på Azure](../media/tutorials/databricks-on-azure.png "Databricks på Azure")

3. Ange de värden som ska skapa ett kluster på sidan **Nytt kluster.**

    ![Skapa Databricks Spark-kluster på Azure](../media/tutorials/create-databricks-spark-cluster.png "Skapa Databricks Spark-kluster på Azure")

    Godkänn alla övriga standardvärden, förutom följande:

   * Ange ett namn för klustret.
   * Skapa ett kluster med **5.2-körning** i den här artikeln. Välj INTE **5,3** körning.
   * Kontrollera att kryssrutan **Avsluta efter \_ \_ minuter av inaktivitet** är markerad. Ange en varaktighet (i minuter) för att avsluta klustret, om klustret inte används.

     Välj **Skapa kluster**. 
4. Det tar flera minuter att skapa klustret. När klustret körs kan du ansluta anteckningsböcker till klustret och köra Spark-jobb.

## <a name="create-a-twitter-application"></a>Skapa ett Twitter-program

Om du vill få en dataström med tweets måste du skapa ett program i Twitter. Följ anvisningarna för att skapa ett Twitter-program och registrera de värden som du behöver för självstudien.

1. Från en webbläsare går du till [Twitter Application Management](https://apps.twitter.com/) (Twitter-programhantering) och väljer **Skapa ny app**.

    ![Skapa Twitter-program](../media/tutorials/databricks-create-twitter-app.png "Skapa Twitter-program")

2. På sidan **Skapa ett program** anger du information om den nya appen. Välj sedan **Create your Twitter application** (Skapa ditt Twitter-program).

    ![Twitter ansökan detaljer](../media/tutorials/databricks-provide-twitter-app-details.png "Twitter ansökan detaljer")

3. På programsidan väljer du fliken med **nycklar och åtkomsttoken** och kopierar värdena för den **använda nyckeln** och **konsumenthemligheten**. Välj också **Skapa min åtkomsttoken** för att generera åtkomsttoken. Kopiera värdena för **åtkomsttoken** och **åtkomsttokenhemligheten**.

    ![Twitter ansökan detaljer](../media/tutorials/twitter-app-key-secret.png "Twitter ansökan detaljer")

Spara de värden som du hämtade för Twitter-programmet. Du behöver dem senare i självstudien.

## <a name="attach-libraries-to-spark-cluster"></a>Bifoga bibliotek till Spark-kluster

I den här självstudien använder du Twitter-API:er för att skicka tweets till Event Hubs. Du använder också [Apache Spark Event Hubs-anslutningsprogram](https://github.com/Azure/azure-event-hubs-spark) för att läsa och skriva data till Azure Event Hubs. Använd dessa API:er som en del av klustret, lägg till dem som bibliotek i Azure Databricks och koppla dem sedan till ditt Spark-kluster. Följande instruktioner visar hur du lägger till biblioteken i mappen **Delad** på arbetsytan.

1. I Azure Databricks-arbetsytan väljer du **Arbetsyta** och högerklickar sedan på **Delade**. Välj **Skapa** > **bibliotek**på snabbmenyn .

   ![Dialogrutan Lägg till bibliotek](../media/tutorials/databricks-add-library-option.png "Dialogrutan Lägg till bibliotek")

2. Välj **Maven**för **Käll** på sidan Nytt bibliotek . För **Koordinater**anger du koordinaten för det paket som du vill lägga till. Här är Maven-koordinaterna för de bibliotek som används i självstudien:

   * Spark Event Hubs-anslutningsprogram – `com.microsoft.azure:azure-eventhubs-spark_2.11:2.3.10`
   * Twitter-API – `org.twitter4j:twitter4j-core:4.0.7`

     ![Ge Maven-koordinater](../media/tutorials/databricks-eventhub-specify-maven-coordinate.png "Ge Maven-koordinater")

3. Välj **Skapa**.

4. Välj den mapp där du lade till biblioteket och välj sedan biblioteksnamnet.

    ![Välj bibliotek som ska läggas till](../media/tutorials/select-library.png "Välj bibliotek som ska läggas till")

5. Om det inte finns något kluster på bibliotekssidan väljer du Kluster och kör **klustret** som du har skapat. Vänta tills tillståndet visar "Löpning" och gå sedan tillbaka till bibliotekssidan.
På bibliotekssidan väljer du det kluster där du vill använda biblioteket och väljer sedan **Installera**. När biblioteket har associerats med klustret ändras statusen omedelbart till **Installerat**.

    ![Installera bibliotek i klustret](../media/tutorials/databricks-library-attached.png "Installera bibliotek i klustret")

6. Upprepa dessa steg för Twitter-paketet `twitter4j-core:4.0.7`.

## <a name="get-a-cognitive-services-access-key"></a>Hämta en Cognitive Services-åtkomstnyckel

I den här självstudien använder du [Azure Cognitive Services Anomaly Detector API:er](../overview.md) för att köra avvikelseidentifiering på en ström av tweets i nära realtid. Innan du använder API:erna måste du skapa en avvikelsedetektorresurs på Azure och hämta en åtkomstnyckel för att använda API:erna för avvikelsedetektor.

1. Logga in på [Azure-portalen](https://portal.azure.com/).

2. Välj **+ Skapa en resurs**.

3. Under Azure**Marketplace,** > Välj **AI + Machine Learning** > Se alla**kognitiva tjänster - Mer** > **Avvikelse Detector**. Eller så kan du använda [den här länken](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector) för att gå direkt till dialogrutan **Skapa.**

    ![Skapa resurs för avvikelsedetektor](../media/tutorials/databricks-cognitive-services-anomaly-detector.png "Skapa resurs för avvikelsedetektor")

4. I dialogrutan **Skapa** anger du följande värden:

    |Värde |Beskrivning  |
    |---------|---------|
    |Namn     | Ett namn på resursen Avvikelsedetektor.        |
    |Prenumeration     | Azure-prenumerationen som resursen kommer att associeras med.        |
    |Location     | En Azure-plats.        |
    |Prisnivå     | En prisnivå för tjänsten. Mer information om priser för avvikelsedetektor finns på [prissidan](https://azure.microsoft.com/pricing/details/cognitive-services/anomaly-detector/).        |
    |Resursgrupp     | Ange om du vill skapa en ny resursgrupp eller välja en befintlig.        |


     Välj **Skapa**.

5. När resursen har skapats kopierar och sparar du **slutpunkts-URL:en** på fliken **Översikt,** vilket visas i skärmbilden. Välj sedan **Visa åtkomstnycklar**.

    ![Visa åtkomstnycklar](../media/tutorials/cognitive-services-get-access-keys.png "Visa åtkomstnycklar")

6. Under **Nycklar**väljer du kopieringsikonen mot den nyckel du vill använda. Spara åtkomstnyckeln.

    ![Kopiera åtkomstnycklar](../media/tutorials/cognitive-services-copy-access-keys.png "Kopiera åtkomstnycklar")

## <a name="create-notebooks-in-databricks"></a>Skapa anteckningsböcker i Databricks

I det här avsnittet skapar du två anteckningsböcker i Databricks-arbetsytan med följande namn

- **SendTweetsToEventHub** – En producentanteckningsbok som du använder för att få tweets från Twitter och strömma dem till Event Hubs.
- **AnalyzeTweetsFromEventHub** - En anteckningsbok för konsumenter som du använder för att läsa tweets från eventhubbar och köra avvikelseidentifiering.

1. Välj **Arbetsyta i** den vänstra rutan i Azure Databricks- arbetsytan. I listrutan **Arbetsyta** väljer du **Skapa** och sedan **Anteckningsbok**.

    ![Skapa anteckningsbok i Databricks](../media/tutorials/databricks-create-notebook.png "Skapa anteckningsbok i Databricks")

2. I dialogrutan **Skapa anteckningsbok** anger du **SendTweetsToEventHub** som namn, väljer **Scala** som språk och väljer spark-klustret som du skapade tidigare.

    ![Skapa anteckningsbok i Databricks](../media/tutorials/databricks-notebook-details.png "Skapa anteckningsbok i Databricks")

    Välj **Skapa**.

3. Upprepa stegen för att skapa anteckningsboken **AnalyzeTweetsFromEventHub**.

## <a name="send-tweets-to-event-hubs"></a>Skicka tweets till Event Hubs

I anteckningsboken **SendTweetsToEventHub** klistrar du in följande kod och ersätter platshållarna med värden för Event Hubs-namnrymden och det Twitter-program som du skapade tidigare. Den här anteckningsboken extraherar skapande tid och antal "Gilla"-tal från tweets med nyckelordet "Azure" och strömma dem som händelser till Event Hubs i realtid.

```scala
//
// Send Data to Eventhub
//

import scala.collection.JavaConverters._
import com.microsoft.azure.eventhubs._
import java.util.concurrent._
import com.google.gson.{Gson, GsonBuilder, JsonParser}
import java.util.Date
import scala.util.control._
import twitter4j._
import twitter4j.TwitterFactory
import twitter4j.Twitter
import twitter4j.conf.ConfigurationBuilder

// Event Hub Config
val namespaceName = "[Placeholder: EventHub namespace]"
val eventHubName = "[Placeholder: EventHub name]"
val sasKeyName = "[Placeholder: EventHub access key name]"
val sasKey = "[Placeholder: EventHub access key key]"
val connStr = new ConnectionStringBuilder()
  .setNamespaceName(namespaceName)
  .setEventHubName(eventHubName)
  .setSasKeyName(sasKeyName)
  .setSasKey(sasKey)

// Connect to the Event Hub
val pool = Executors.newScheduledThreadPool(1)
val eventHubClient = EventHubClient.create(connStr.toString(), pool)

def sendEvent(message: String) = {
  val messageData = EventData.create(message.getBytes("UTF-8"))
  eventHubClient.get().send(messageData)
  System.out.println("Sent event: " + message + "\n")
}

case class MessageBody(var timestamp: Date, var favorite: Int)
val gson: Gson = new GsonBuilder().setDateFormat("yyyy-MM-dd'T'HH:mm:ss.SSS'Z'").create()

val twitterConsumerKey = "[Placeholder: Twitter consumer key]"
val twitterConsumerSecret = "[Placeholder: Twitter consumer seceret]"
val twitterOauthAccessToken = "[Placeholder: Twitter oauth access token]"
val twitterOauthTokenSecret = "[Placeholder: Twitter oauth token secret]"

val cb = new ConfigurationBuilder()
cb.setDebugEnabled(true)
  .setOAuthConsumerKey(twitterConsumerKey)
  .setOAuthConsumerSecret(twitterConsumerSecret)
  .setOAuthAccessToken(twitterOauthAccessToken)
  .setOAuthAccessTokenSecret(twitterOauthTokenSecret)

val twitterFactory = new TwitterFactory(cb.build())
val twitter = twitterFactory.getInstance()

// Getting tweets with keyword "Azure" and sending them to the Event Hub in realtime!

val query = new Query(" #Azure ")
query.setCount(100)
query.lang("en")

var finished = false
var maxStatusId = Long.MinValue
var preMaxStatusId = Long.MinValue
val innerLoop = new Breaks
while (!finished) {
  val result = twitter.search(query)
  val statuses = result.getTweets()
  var lowestStatusId = Long.MaxValue
  innerLoop.breakable {
    for (status <- statuses.asScala) {
      if (status.getId() <= preMaxStatusId) {
        preMaxStatusId = maxStatusId
        innerLoop.break
      }
      if(!status.isRetweet()) {
        sendEvent(gson.toJson(new MessageBody(status.getCreatedAt(), status.getFavoriteCount())))
      }
      lowestStatusId = Math.min(status.getId(), lowestStatusId)
      maxStatusId = Math.max(status.getId(), maxStatusId)
    }
  }
  
  if (lowestStatusId == Long.MaxValue) {
    preMaxStatusId = maxStatusId
  }
  Thread.sleep(10000)
  query.setMaxId(lowestStatusId - 1)
}

// Close connection to the Event Hub
eventHubClient.get().close()
pool.shutdown()
```

Om du vill köra anteckningsboken trycker du på **SKIFT + RETUR**. Du ser utdata som liknar följande kodfragment. Varje händelse i utdata är en kombination av tidsstämpel och antalet "Like" som matas in i händelsehubbar.

    Sent event: {"timestamp":"2019-04-24T09:39:40.000Z","favorite":0}

    Sent event: {"timestamp":"2019-04-24T09:38:48.000Z","favorite":1}

    Sent event: {"timestamp":"2019-04-24T09:38:36.000Z","favorite":0}

    Sent event: {"timestamp":"2019-04-24T09:37:27.000Z","favorite":0}

    Sent event: {"timestamp":"2019-04-24T09:37:00.000Z","favorite":2}

    Sent event: {"timestamp":"2019-04-24T09:31:11.000Z","favorite":0}

    Sent event: {"timestamp":"2019-04-24T09:30:15.000Z","favorite":0}

    Sent event: {"timestamp":"2019-04-24T09:30:02.000Z","favorite":1}

    ...
    ...

## <a name="read-tweets-from-event-hubs"></a>Läs tweets från Event Hubs

I anteckningsboken **AnalyzeTweetsFromEventHub** klistrar du in följande kod och ersätter platshållaren med värden för avvikelsedetektorresursen som du skapade tidigare. Den här anteckningsboken läser de tweets som du tidigare strömmade till Event Hubs med hjälp av anteckningsboken **SendTweetsToEventHub**.

Skriv först en klient för att anropa Avvikelsedetektorn. 
```scala

//
// Anomaly Detection Client
//

import java.io.{BufferedReader, DataOutputStream, InputStreamReader}
import java.net.URL
import java.sql.Timestamp

import com.google.gson.{Gson, GsonBuilder, JsonParser}
import javax.net.ssl.HttpsURLConnection

case class Point(var timestamp: Timestamp, var value: Double)
case class Series(var series: Array[Point], var maxAnomalyRatio: Double, var sensitivity: Int, var granularity: String)
case class AnomalySingleResponse(var isAnomaly: Boolean, var isPositiveAnomaly: Boolean, var isNegativeAnomaly: Boolean, var period: Int, var expectedValue: Double, var upperMargin: Double, var lowerMargin: Double, var suggestedWindow: Int)
case class AnomalyBatchResponse(var expectedValues: Array[Double], var upperMargins: Array[Double], var lowerMargins: Array[Double], var isAnomaly: Array[Boolean], var isPositiveAnomaly: Array[Boolean], var isNegativeAnomaly: Array[Boolean], var period: Int)

object AnomalyDetector extends Serializable {

  // Cognitive Services API connection settings
  val subscriptionKey = "[Placeholder: Your Anomaly Detector resource access key]"
  val endpoint = "[Placeholder: Your Anomaly Detector resource endpoint]"
  val latestPointDetectionPath = "/anomalydetector/v1.0/timeseries/last/detect"
  val batchDetectionPath = "/anomalydetector/v1.0/timeseries/entire/detect";
  val latestPointDetectionUrl = new URL(endpoint + latestPointDetectionPath)
  val batchDetectionUrl = new URL(endpoint + batchDetectionPath)
  val gson: Gson = new GsonBuilder().setDateFormat("yyyy-MM-dd'T'HH:mm:ss.SSS'Z'").setPrettyPrinting().create()

  def getConnection(path: URL): HttpsURLConnection = {
    val connection = path.openConnection().asInstanceOf[HttpsURLConnection]
    connection.setRequestMethod("POST")
    connection.setRequestProperty("Content-Type", "text/json")
    connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey)
    connection.setDoOutput(true)
    return connection
  }

  // Handles the call to Cognitive Services API.
  def processUsingApi(request: String, path: URL): String = {
    println(request)
    val encoded_text = request.getBytes("UTF-8")
    val connection = getConnection(path)
    val wr = new DataOutputStream(connection.getOutputStream())
    wr.write(encoded_text, 0, encoded_text.length)
    wr.flush()
    wr.close()

    val response = new StringBuilder()
    val in = new BufferedReader(new InputStreamReader(connection.getInputStream()))
    var line = in.readLine()
    while (line != null) {
      response.append(line)
      line = in.readLine()
    }
    in.close()
    return response.toString()
  }

  // Calls the Latest Point Detection API.
  def detectLatestPoint(series: Series): Option[AnomalySingleResponse] = {
    try {
      println("Process Timestamp: " + series.series.apply(series.series.length-1).timestamp.toString + ", size: " + series.series.length)
      val response = processUsingApi(gson.toJson(series), latestPointDetectionUrl)
      println(response)
      // Deserializing the JSON response from the API into Scala types
      val anomaly = gson.fromJson(response, classOf[AnomalySingleResponse])
      Thread.sleep(5000)
      return Some(anomaly)
    } catch {
      case e: Exception => {
        println(e)
        e.printStackTrace()
        return None
      }
    }
  }

  // Calls the Batch Detection API.
  def detectBatch(series: Series): Option[AnomalyBatchResponse] = {
    try {
      val response = processUsingApi(gson.toJson(series), batchDetectionUrl)
      println(response)
      // Deserializing the JSON response from the API into Scala types
      val anomaly = gson.fromJson(response, classOf[AnomalyBatchResponse])
      Thread.sleep(5000)
      return Some(anomaly)
    } catch {
      case e: Exception => {
        println(e)
        return None
      }
    }
  }
}
```

Om du vill köra anteckningsboken trycker du på **SKIFT + RETUR**. Du ser utdata som liknar följande kodfragment.

    import java.io.{BufferedReader, DataOutputStream, InputStreamReader}
    import java.net.URL
    import java.sql.Timestamp
    import com.google.gson.{Gson, GsonBuilder, JsonParser}
    import javax.net.ssl.HttpsURLConnection
    defined class Point
    defined class Series
    defined class AnomalySingleResponse
    defined class AnomalyBatchResponse
    defined object AnomalyDetector

Förbered sedan en aggregeringsfunktion för framtida användning.
```scala
//
// User Defined Aggregation Function for Anomaly Detection
//

import org.apache.spark.sql.Row
import org.apache.spark.sql.expressions.{MutableAggregationBuffer, UserDefinedAggregateFunction}
import org.apache.spark.sql.types.{StructType, TimestampType, FloatType, MapType, BooleanType, DataType}
import scala.collection.immutable.ListMap

class AnomalyDetectorAggregationFunction extends UserDefinedAggregateFunction {
  override def inputSchema: StructType = new StructType().add("timestamp", TimestampType).add("value", FloatType)
  
  override def bufferSchema: StructType = new StructType().add("point", MapType(TimestampType, FloatType))
  
  override def dataType: DataType = BooleanType
  
  override def deterministic: Boolean = false
  
  override def initialize(buffer: MutableAggregationBuffer): Unit = {
    buffer(0) = Map()
  }
  
  override def update(buffer: MutableAggregationBuffer, input: Row): Unit = {
    buffer(0) = buffer.getAs[Map[java.sql.Timestamp, Float]](0) + (input.getTimestamp(0) -> input.getFloat(1))
  }
  
  override def merge(buffer1: MutableAggregationBuffer, buffer2: Row): Unit = {
    buffer1(0) = buffer1.getAs[Map[java.sql.Timestamp, Float]](0) ++ buffer2.getAs[Map[java.sql.Timestamp, Float]](0)
  }
  
  override def evaluate(buffer: Row): Any = {
    val points = buffer.getAs[Map[java.sql.Timestamp, Float]](0)
    if (points.size > 12) {
      val sorted_points = ListMap(points.toSeq.sortBy(_._1.getTime):_*)
      var detect_points: List[Point] = List()
      sorted_points.keys.foreach {
        key => detect_points = detect_points :+ new Point(key, sorted_points(key))
      }
      
      
      // 0.25 is maxAnomalyRatio. It represents 25%, max anomaly ratio in a time series.
      // 95 is the sensitivity of the algorithms.
      // Check Anomaly detector API reference (https://aka.ms/anomaly-detector-rest-api-ref)
      
      val series: Series = new Series(detect_points.toArray, 0.25, 95, "hourly")
      val response: Option[AnomalySingleResponse] = AnomalyDetector.detectLatestPoint(series)
      if (!response.isEmpty) {
        return response.get.isAnomaly
      }
    }
    
    return None
  }
}

```

Om du vill köra anteckningsboken trycker du på **SKIFT + RETUR**. Du ser utdata som liknar följande kodfragment.

    import org.apache.spark.sql.Row
    import org.apache.spark.sql.expressions.{MutableAggregationBuffer, UserDefinedAggregateFunction}
    import org.apache.spark.sql.types.{StructType, TimestampType, FloatType, MapType, BooleanType, DataType}
    import scala.collection.immutable.ListMap
    defined class AnomalyDetectorAggregationFunction

Läs sedan in data från händelsehubben för avvikelseidentifiering. Ersätt platshållaren med värden för dina Azure Event Hubs som du skapade tidigare.

```scala
//
// Load Data from Eventhub
//

import org.apache.spark.eventhubs._
import org.apache.spark.sql.types._
import org.apache.spark.sql.functions._

val connectionString = ConnectionStringBuilder("[Placeholder: EventHub namespace connection string]")
  .setEventHubName("[Placeholder: EventHub name]")
  .build

val customEventhubParameters =
  EventHubsConf(connectionString)
  .setConsumerGroup("$Default")
  .setMaxEventsPerTrigger(100)

val incomingStream = spark.readStream.format("eventhubs").options(customEventhubParameters.toMap).load()

val messages =
  incomingStream
  .withColumn("enqueuedTime", $"enqueuedTime".cast(TimestampType))
  .withColumn("body", $"body".cast(StringType))
  .select("enqueuedTime", "body")

val bodySchema = new StructType().add("timestamp", TimestampType).add("favorite", IntegerType)

val msgStream = messages.select(from_json('body, bodySchema) as 'fields).select("fields.*")

msgStream.printSchema

display(msgStream)

```

Utdata liknar nu följande bild. Observera att ditt datum i tabellen kan skilja sig från datumet i den här självstudien eftersom data är i realtid.
![Läs in data från händelsehubben](../media/tutorials/load-data-from-eventhub.png "Läsa in data från händelsehubb")

Nu har du strömmat data från Azure Event Hubs till Azure Databricks nästan i realtid med hjälp av Event Hubs-anslutningsappen för Apache Spark. Mer information om hur du använder Event Hubs-anslutningsprogrammet för Apache Spark finns i [dokumentationen till anslutningsprogrammet](https://github.com/Azure/azure-event-hubs-spark/tree/master/docs).



## <a name="run-anomaly-detection-on-tweets"></a>Kör avvikelseidentifiering på tweets

I det här avsnittet kör du avvikelseidentifiering på de tweets som tagits emot med hjälp av API:et för avvikelsedetektorn. I det här avsnittet lägger du till kodfragmenten till samma **AnalyzeTweetsFromEventHub**-anteckningsbok.

För att göra avvikelseidentifiering måste du först aggregera antalet mått per timme.
```scala
//
// Aggregate Metric Count by Hour
//

// If you want to change granularity, change the groupBy window. 
val groupStream = msgStream.groupBy(window($"timestamp", "1 hour"))
  .agg(avg("favorite").alias("average"))
  .withColumn("groupTime", $"window.start")
  .select("groupTime", "average")

groupStream.printSchema

display(groupStream)
```
Utdata liknar nu följande utdrag.
```
groupTime                       average
2019-04-23T04:00:00.000+0000    24
2019-04-26T19:00:00.000+0000    47.888888888888886
2019-04-25T12:00:00.000+0000    32.25
2019-04-26T09:00:00.000+0000    63.4
...
...

```

Hämta sedan det aggregerade utdataresultatet till Delta. Eftersom avvikelseidentifiering kräver ett längre historikfönster använder vi Delta för att behålla historikdata för den punkt du vill identifiera. Ersätt "[Platshållare: tabellnamn]" med ett kvalificerat Delta-tabellnamn som ska skapas (till exempel "tweets"). Ersätt "[Platshållare: mappnamn för kontrollpunkter]" med ett strängvärde som är unikt varje gång du kör den här koden (till exempel "etl-from-eventhub-20190605").
Mer information om Delta Lake på Azure Databricks finns i [Delta Lake Guide](https://docs.azuredatabricks.net/delta/index.html)


```scala
//
// Output Aggregation Result to Delta
//

groupStream.writeStream
  .format("delta")
  .outputMode("complete")
  .option("checkpointLocation", "/delta/[Placeholder: table name]/_checkpoints/[Placeholder: folder name for checkpoints]")
  .table("[Placeholder: table name]")

```

Ersätt "[Platshållare: tabellnamn]" med samma Delta-tabellnamn som du har markerat ovan.
```scala
//
// Show Aggregate Result
//

val twitterCount = spark.sql("SELECT COUNT(*) FROM [Placeholder: table name]")
twitterCount.show()

val twitterData = spark.sql("SELECT * FROM [Placeholder: table name] ORDER BY groupTime")
twitterData.show(200, false)

display(twitterData)
```
Utgången enligt nedan: 
```
groupTime                       average
2019-04-08T01:00:00.000+0000    25.6
2019-04-08T02:00:00.000+0000    6857
2019-04-08T03:00:00.000+0000    71
2019-04-08T04:00:00.000+0000    55.111111111111114
2019-04-08T05:00:00.000+0000    2203.8
...
...

```

Nu intas de aggregerade tidsseriedata kontinuerligt in i Delta. Sedan kan du schemalägga ett timjobb för att upptäcka anomalin hos den senaste punkten. Ersätt "[Platshållare: tabellnamn]" med samma Delta-tabellnamn som du har markerat ovan.

```scala
//
// Anomaly Detection
//

import java.time.Instant
import java.time.format.DateTimeFormatter
import java.time.ZoneOffset
import java.time.temporal.ChronoUnit

val detectData = spark.read.format("delta").table("[Placeholder: table name]")

// You could use Databricks to schedule an hourly job and always monitor the latest data point
// Or you could specify a const value here for testing purpose
// For example, val endTime = Instant.parse("2019-04-16T00:00:00Z")
val endTime = Instant.now()

// This is when your input of anomaly detection starts. It is hourly time series in this tutorial, so 72 means 72 hours ago from endTime.
val batchSize = 72
val startTime = endTime.minus(batchSize, ChronoUnit.HOURS)

val DATE_TIME_FORMATTER = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss").withZone(ZoneOffset.UTC);

val series = detectData.filter($"groupTime" <= DATE_TIME_FORMATTER.format(endTime))
  .filter($"groupTime" > DATE_TIME_FORMATTER.format(startTime))
  .sort($"groupTime")

series.createOrReplaceTempView("series")

//series.show()

// Register the function to access it
spark.udf.register("anomalydetect", new AnomalyDetectorAggregationFunction)

val adResult = spark.sql("SELECT '" + endTime.toString + "' as datetime, anomalydetect(groupTime, average) as anomaly FROM series")
adResult.show()
```
Resultat enligt nedan: 

```
+--------------------+-------+
|           timestamp|anomaly|
+--------------------+-------+
|2019-04-16T00:00:00Z|  false|
+--------------------+-------+
```

Klart! Med Hjälp av Azure Databricks har du strömmat data till Azure Event Hubs, förbrukat dataströmdata med hjälp av Event Hubs-kopplingen och sedan köra avvikelseidentifiering på strömmande data i nära realtid.
Även i den här självstudien är granulariteten varje timme, kan du alltid ändra granulariteten för att möta dina behov. 

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar med självstudien kan du avsluta klustret. För att göra det, på Arbetsytan Azure Databricks, väljer **du Kluster** från den vänstra rutan. För det kluster som du vill avsluta flyttar du markören över ellipsen under **Åtgärdskolumnen** och väljer ikonen **Avsluta** och väljer sedan **Bekräfta**.

![Stoppa ett Databricks-kluster](../media/tutorials/terminate-databricks-cluster.png "Stoppa ett Databricks-kluster")

Om du inte avslutar klustret manuellt stoppas det automatiskt, förutsatt att du har markerat kryssrutan **Avsluta efter \_ \_ minuter av inaktivitet** när du skapar klustret. I dessa fall stoppas klustret automatiskt om det har varit inaktivt under den angivna tiden.

## <a name="next-steps"></a>Nästa steg

I självstudien har du lärt dig hur du använder Azure Databricks till att strömma data till Azure Event Hubs och sedan läsa strömmande data från Event Hubs i realtid. Gå vidare till nästa självstudiekurs för att lära dig hur du anropar API:et för avvikelsedetektor och visualiserar avvikelser med Power BI-skrivbordet. 

> [!div class="nextstepaction"]
>[Identifiering av batchanvikaring med Power BI-skrivbordet](batch-anomaly-detection-powerbi.md)
