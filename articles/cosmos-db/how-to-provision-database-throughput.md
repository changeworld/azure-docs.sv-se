---
title: Etablera dataflöde för en databas i Azure Cosmos DB
description: Lär dig hur du etablerar dataflöde på databasnivå i Azure Cosmos DB med Hjälp av Azure Portal, CLI, PowerShell och flera andra SDK:er.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/28/2019
ms.author: mjbrown
ms.openlocfilehash: ef7d06dfb074a3453f5589284cbdaf079c48d111
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78933777"
---
# <a name="provision-throughput-on-a-database-in-azure-cosmos-db"></a>Etablera dataflöde i en databas i Azure Cosmos DB

I den här artikeln beskrivs hur du etablerar dataflöde i en databas i Azure Cosmos DB. Du kan etablera dataflöde för en enskild [behållare](how-to-provision-container-throughput.md)eller för en databas och dela dataflödet mellan behållarna i den. Mer information om när du ska använda dataflöde på behållarnivå och databasnivå finns i artikeln [Använd fall för etablering av dataflöde på behållare och databaser.](set-throughput.md) Du kan etablera dataflöde på databasnivå med hjälp av Azure-portalen eller Azure Cosmos DB-SDK:er.

## <a name="provision-throughput-using-azure-portal"></a>Etablera dataflöde med hjälp av Azure-portalen

### <a name="sql-core-api"></a><a id="portal-sql"></a>SQL-API (Core)

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. [Skapa ett nytt Azure Cosmos-konto](create-sql-api-dotnet.md#create-account)eller välj ett befintligt Azure Cosmos-konto.

1. Öppna rutan **Data Explorer** och välj **Ny databas**. Ange följande information:

   * Ange ett databas-ID.
   * Välj **Etablera dataflöde**.
   * Ange ett dataflöde (till exempel 1000 RU:er).
   * Välj **OK**.

    ![Skärmbild av dialogrutan Ny databas](./media/how-to-provision-database-throughput/provision-database-throughput-portal-all-api.png)

## <a name="provision-throughput-using-azure-cli-or-powershell"></a>Etablera dataflöde med Azure CLI eller PowerShell

Om du vill skapa en databas med delat dataflöde finns

* [Skapa en databas med Azure CLI](manage-with-cli.md#create-a-database-with-shared-throughput)
* [Skapa en databas med Powershell](manage-with-powershell.md#create-db-ru)

## <a name="provision-throughput-using-net-sdk"></a>Etablera dataflöde med hjälp av .NET SDK

> [!Note]
> Du kan använda Cosmos SDK:er för SQL API för att etablera dataflöde för alla API:er. Om du vill kan du även använda följande exempel för API för Cassandra.

### <a name="all-apis"></a><a id="dotnet-all"></a>Alla API:er

### <a name="net-v2-sdk"></a>.Net V2 SDK

```csharp
//set the throughput for the database
RequestOptions options = new RequestOptions
{
    OfferThroughput = 500
};

//create the database
await client.CreateDatabaseIfNotExistsAsync(
    new Database {Id = databaseName},  
    options);
```

### <a name="net-v3-sdk"></a>.Net V3 SDK

[!code-csharp[](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos/tests/Microsoft.Azure.Cosmos.Tests/SampleCodeForDocs/DatabaseDocsSampleCode.cs?name=DatabaseCreateWithThroughput)]

### <a name="cassandra-api"></a><a id="dotnet-cassandra"></a>API för Cassandra
Liknande kommando kan utföras via alla CQL-kompatibla drivrutiner. 
```csharp
// Create a Cassandra keyspace and provision throughput of 400 RU/s
session.Execute("CREATE KEYSPACE IF NOT EXISTS myKeySpace WITH cosmosdb_provisioned_throughput=400");
```
 
## <a name="next-steps"></a>Nästa steg

Se följande artiklar om hur du gör det information om etablerat dataflöde i Azure Cosmos DB:

* [Skala etablerat dataflöde globalt](scaling-throughput.md)
* [Etablera dataflöde på containrar och databaser](set-throughput.md)
* [Etablera dataflöde för en container](how-to-provision-container-throughput.md)
* [Begärandeenheter och dataflöde i Azure Cosmos DB](request-units.md)
