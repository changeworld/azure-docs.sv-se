---
title: Skapa eller infoga data i Azure Cosmos DB Cassandra API från Spark
description: I den hÃ¤r artikeln beskrivs hur du infogar exempeldata i Azure Cosmos DB Cassandra API-tabeller
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 3eb23a3d8b1098110bd8b75faa22cc483637d183
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75442129"
---
# <a name="createinsert-data-into-azure-cosmos-db-cassandra-api-from-spark"></a>Skapa/infoga data i Azure Cosmos DB Cassandra API från Spark
 
I den här artikeln beskrivs hur du infogar exempeldata i en tabell i Azure Cosmos DB Cassandra API från Spark.

## <a name="cassandra-api-configuration"></a>Cassandra API-konfiguration

```scala
import org.apache.spark.sql.cassandra._
//Spark connector
import com.datastax.spark.connector._
import com.datastax.spark.connector.cql.CassandraConnector

//CosmosDB library for multiple retry
import com.microsoft.azure.cosmosdb.cassandra

//Connection-related
spark.conf.set("spark.cassandra.connection.host","YOUR_ACCOUNT_NAME.cassandra.cosmosdb.azure.com")
spark.conf.set("spark.cassandra.connection.port","10350")
spark.conf.set("spark.cassandra.connection.ssl.enabled","true")
spark.conf.set("spark.cassandra.auth.username","YOUR_ACCOUNT_NAME")
spark.conf.set("spark.cassandra.auth.password","YOUR_ACCOUNT_KEY")
spark.conf.set("spark.cassandra.connection.factory", "com.microsoft.azure.cosmosdb.cassandra.CosmosDbConnectionFactory")
//Throughput-related...adjust as needed
spark.conf.set("spark.cassandra.output.batch.size.rows", "1")
spark.conf.set("spark.cassandra.connection.connections_per_executor_max", "10")
spark.conf.set("spark.cassandra.output.concurrent.writes", "1000")
spark.conf.set("spark.cassandra.concurrent.reads", "512")
spark.conf.set("spark.cassandra.output.batch.grouping.buffer.size", "1000")
spark.conf.set("spark.cassandra.connection.keep_alive_ms", "600000000")
```
## <a name="dataframe-api"></a>Api för dataram

### <a name="create-a-dataframe-with-sample-data"></a>Skapa en dataram med exempeldata

```scala
// Generate a dataframe containing five records
val booksDF = Seq(
   ("b00001", "Arthur Conan Doyle", "A study in scarlet", 1887),
   ("b00023", "Arthur Conan Doyle", "A sign of four", 1890),
   ("b01001", "Arthur Conan Doyle", "The adventures of Sherlock Holmes", 1892),
   ("b00501", "Arthur Conan Doyle", "The memoirs of Sherlock Holmes", 1893),
   ("b00300", "Arthur Conan Doyle", "The hounds of Baskerville", 1901)
).toDF("book_id", "book_author", "book_name", "book_pub_year")

//Review schema
booksDF.printSchema

//Print
booksDF.show
```

> [!NOTE]
> Funktionen "Skapa om det inte finns" stöds ännu inte på radnivå.

### <a name="persist-to-azure-cosmos-db-cassandra-api"></a>Beständighet i Azure Cosmos DB Cassandra API

När du sparar data kan du också ange inställningar för tid att leva och konsekvensprincipinställningar som visas i följande exempel:

```scala
//Persist
booksDF.write
  .mode("append")
  .format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks", "output.consistency.level" -> "ALL", "ttl" -> "10000000"))
  .save()
```

> [!NOTE]
> TTL på kolumnnivå stöds inte ännu.

#### <a name="validate-in-cqlsh"></a>Validera i cqlsh

```sql
use books_ks;
select * from books;
```

## <a name="resilient-distributed-database-rdd-api"></a>API för fjädrande distribuerad databas (RDD)

### <a name="create-a-rdd-with-sample-data"></a>Skapa en RDD med exempeldata
```scala
//Delete records created in the previous section 
val cdbConnector = CassandraConnector(sc)
cdbConnector.withSessionDo(session => session.execute("delete from books_ks.books where book_id in ('b00300','b00001','b00023','b00501','b09999','b01001','b00999','b03999','b02999');"))

//Create RDD
val booksRDD = sc.parallelize(Seq(
   ("b00001", "Arthur Conan Doyle", "A study in scarlet", 1887),
   ("b00023", "Arthur Conan Doyle", "A sign of four", 1890),
   ("b01001", "Arthur Conan Doyle", "The adventures of Sherlock Holmes", 1892),
   ("b00501", "Arthur Conan Doyle", "The memoirs of Sherlock Holmes", 1893),
   ("b00300", "Arthur Conan Doyle", "The hounds of Baskerville", 1901)
))

//Review
booksRDD.take(2).foreach(println)
```

> [!NOTE]
> Det finns ännu inga funktioner för att skapa om det inte finns.

### <a name="persist-to-azure-cosmos-db-cassandra-api"></a>Beständighet i Azure Cosmos DB Cassandra API

När du sparar data i Cassandra API kan du också ange principinställningar för tid till live och konsekvens som visas i följande exempel:

```scala
import com.datastax.spark.connector.writer._

//Persist
booksRDD.saveToCassandra("books_ks", "books", SomeColumns("book_id", "book_author", "book_name", "book_pub_year"),writeConf = WriteConf(ttl = TTLOption.constant(900000),consistencyLevel = ConsistencyLevel.ALL))
```

#### <a name="validate-in-cqlsh"></a>Validera i cqlsh

```sql
use books_ks;
select * from books;
```

## <a name="next-steps"></a>Nästa steg

När du har infogat data i azure Cosmos DB Cassandra API-tabellen fortsätter du till följande artiklar för att utföra andra åtgärder på data som lagras i Cosmos DB Cassandra API:
 
* [Läs operationer](cassandra-spark-read-ops.md)
* [Upsert-operationer](cassandra-spark-upsert-ops.md)
* [Ta bort åtgärder](cassandra-spark-delete-ops.md)
* [Sammansättningsåtgärder](cassandra-spark-aggregation-ops.md)
* [Tabellkopieringsåtgärder](cassandra-spark-table-copy-ops.md)

