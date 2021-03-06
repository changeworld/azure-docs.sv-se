---
title: 'Snabbstart: Fråga Apache HBase i Azure HDInsight - HBase Shell'
description: I den här snabbstarten får du lära dig hur du använder Apache HBase Shell för att köra Apache HBase-frågor.
keywords: hdinsight, hadoop, HBase
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: quickstart
ms.date: 06/12/2019
ms.author: hrasheed
ms.openlocfilehash: 572262cbece26171f9a67bf073906fa2dfd4d8e1
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "79371077"
---
# <a name="quickstart-query-apache-hbase-in-azure-hdinsight-with-hbase-shell"></a>Snabbstart: Fråga Apache HBase i Azure HDInsight med HBase Shell

I den här snabbstarten får du lära dig hur du använder Apache HBase Shell för att skapa en HBase-tabell, infoga data och sedan fråga tabellen.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

## <a name="prerequisites"></a>Krav

* Ett Apache HBase-kluster. Se [Skapa kluster](../hadoop/apache-hadoop-linux-tutorial-get-started.md) för att skapa ett HDInsight-kluster.  Se till **HBase** att du väljer HBase-klustertypen.

* En SSH-klient. Mer information finns i [Ansluta till HDInsight (Apache Hadoop) med hjälp av SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="create-a-table-and-manipulate-data"></a>Skapa en tabell och ändra data

För de flesta visas data i tabellformat:

![Tabelldata för HDInsight Apache HBase](./media/query-hbase-with-hbase-shell/hdinsight-hbase-contacts-tabular.png)

I HBase (en implementering av [Cloud BigTable](https://cloud.google.com/bigtable/)) ser samma data ut:

![HDInsight Apache HBase BigTable-data](./media/query-hbase-with-hbase-shell/hdinsight-hbase-contacts-bigtable.png)

Du kan använda SSH för att ansluta till HBase-kluster och sedan använda Apache HBase Shell för att skapa HBase-tabeller, infoga data och frågedata.

1. Använd `ssh` kommandot för att ansluta till HBase-klustret. Redigera kommandot nedan `CLUSTERNAME` genom att ersätta med namnet på klustret och ange sedan kommandot:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Använd `hbase shell` kommandot för att starta det interaktiva HBase-skalet. Ange följande kommando i SSH-anslutningen:

    ```bash
    hbase shell
    ```

3. Använd `create` kommandot för att skapa en HBase-tabell med familjer med två kolumner. Ange följande kommando:

    ```hbase
    create 'Contacts', 'Personal', 'Office'
    ```

4. Använd `list` kommandot för att lista alla tabeller i HBase. Ange följande kommando:

    ```hbase
    list
    ```

5. Använd `put` kommandot för att infoga värden vid en angiven kolumn på en angiven rad i en viss tabell. Ange följande kommando:

    ```hbase
    put 'Contacts', '1000', 'Personal:Name', 'John Dole'
    put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
    put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
    put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
    ```

6. Använd `scan` kommandot för att `Contacts` skanna och returnera tabelldata. Ange följande kommando:

    ```hbase
    scan 'Contacts'
    ```

7. Använd `get` kommandot för att hämta innehållet i en rad. Ange följande kommando:

    ```hbase
    get 'Contacts', '1000'
    ```

    Du ser liknande resultat `scan` som att använda kommandot eftersom det bara finns en rad.

8. Använd `delete` kommandot för att ta bort ett cellvärde i en tabell. Ange följande kommando:

    ```hbase
    delete 'Contacts', '1000', 'Office:Address'
    ```

9. Använd `disable` kommandot för att inaktivera tabellen. Ange följande kommando:

    ```hbase
    disable 'Contacts'
    ```

10. Använd `drop` kommandot för att släppa en tabell från HBase. Ange följande kommando:

    ```hbase
    drop 'Contacts'
    ```

11. Använd `exit` kommandot för att stoppa det interaktiva HBase-skalet. Ange följande kommando:

    ```hbase
    exit
    ```

Mer information om HBase-tabellschemat finns i [Introduktion till Apache HBase Schemadesign](http://0b4af6cdc2f0c5998459-c0245c5c937c5dedcca3f1764ecc9b2f.r43.cf2.rackcdn.com/9353-login1210_khurana.pdf). Fler HBase-kommandon finns i [referensguiden för Apache HBase](https://hbase.apache.org/book.html#quickstart).

## <a name="clean-up-resources"></a>Rensa resurser

När du har slutfört snabbstarten kanske du vill ta bort klustret. Med HDInsight lagras dina data i Azure Storage så att du på ett säkert sätt kan ta bort ett kluster när det inte används. Du debiteras också för ett HDInsight-kluster, även när det inte används. Eftersom avgifterna för klustret är flera gånger större än avgifterna för lagring är det ekonomiskt sett bra att ta bort kluster när de inte används.

Information om hur du tar bort ett kluster finns i [Ta bort ett HDInsight-kluster med webbläsaren, PowerShell eller Azure CLI](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten lärde du dig hur du använder Apache HBase Shell för att skapa en HBase-tabell, infoga data och sedan fråga tabellen. Om du vill veta mer om data som lagras i HBase visar nästa artikel hur du kör frågor med Apache Spark.

> [!div class="nextstepaction"]
> [Använda Apache Spark för att läsa och skriva Apache HBase-data](../hdinsight-using-spark-query-hbase.md)