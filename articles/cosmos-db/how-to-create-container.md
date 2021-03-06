---
title: Skapa en container i Azure Cosmos DB
description: Lär dig hur du skapar en behållare i Azure Cosmos DB med hjälp av Azure Portal, .Net, Java, Python, Node.js och andra SDK:er.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: 4eaa2974817bfcd8bef83e5139d75a2d4c2ec107
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74873717"
---
# <a name="create-an-azure-cosmos-container"></a>Skapa en Azure Cosmos-container

I den här artikeln beskrivs olika sätt att skapa en Azure Cosmos-behållare (samling, tabell eller diagram). Du kan använda Azure Portal, Azure CLI eller SDK:er som stöds för detta. Den här artikeln visar hur du skapar en container, anger partitionsnyckeln och etablerar dataflöde.

## <a name="create-a-container-using-azure-portal"></a>Skapa en container med hjälp av Azure-portalen

### <a name="sql-api"></a><a id="portal-sql"></a>SQL-API

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. [Skapa ett nytt Azure Cosmos-konto](create-sql-api-dotnet.md#create-account)eller välj ett befintligt konto.

1. Öppna fönstret **Data explorer** och välj **Ny behållare**. Ange därefter följande information:

   * Ange om du skapar en ny databas eller använder en befintlig.
   * Ange ett behållar-ID.
   * Ange en partitionsnyckel.
   * Ange ett dataflöde som ska etableras (till exempel 1000 ru:er).
   * Välj **OK**.

    ![Skärmbild av fönstret Data explorer, med ny behållare markerad](./media/how-to-create-container/partitioned-collection-create-sql.png)

### <a name="azure-cosmos-db-api-for-mongodb"></a><a id="portal-mongodb"></a>API för Azure Cosmos DB för MongoDB

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. [Skapa ett nytt Azure Cosmos-konto](create-mongodb-dotnet.md#create-a-database-account)eller välj ett befintligt konto.

1. Öppna fönstret **Data explorer** och välj **Ny behållare**. Ange därefter följande information:

   * Ange om du skapar en ny databas eller använder en befintlig.
   * Ange ett behållar-ID.
   * Ange en shardnyckel.
   * Ange ett dataflöde som ska etableras (till exempel 1000 ru:er).
   * Välj **OK**.

    ![Skärmbild av Azure Cosmos DB API för MongoDB, Dialogrutan Lägg till behållare](./media/how-to-create-container/partitioned-collection-create-mongodb.png)

### <a name="cassandra-api"></a><a id="portal-cassandra"></a>API för Cassandra

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. [Skapa ett nytt Azure Cosmos-konto](create-cassandra-dotnet.md#create-a-database-account)eller välj ett befintligt konto.

1. Öppna rutan **Datautforskaren** och välj **Ny tabell**. Ange därefter följande information:

   * Ange om du skapar ett nytt nyckelutrymme eller använder ett befintligt.
   * Ange ett tabellnamn.
   * Ange egenskaper och ange en primärnyckel.
   * Ange ett dataflöde som ska etableras (till exempel 1000 ru:er).
   * Välj **OK**.

    ![Skärmbild av API för Cassandra, dialogrutan Lägg till tabell](./media/how-to-create-container/partitioned-collection-create-cassandra.png)

> [!NOTE]
> För API för Cassandra används den primära nyckeln som partitionsnyckel.

### <a name="gremlin-api"></a><a id="portal-gremlin"></a>Gremlin-API

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. [Skapa ett nytt Azure Cosmos-konto](create-graph-dotnet.md#create-a-database-account)eller välj ett befintligt konto.

1. Öppna rutan **Datautforskaren** och välj **Ny graf**. Ange därefter följande information:

   * Ange om du skapar en ny databas eller använder en befintlig.
   * Ange ett diagram-ID.
   * Välj **obegränsad** lagringskapacitet.
   * Ange en partitionsnyckel för hörn.
   * Ange ett dataflöde som ska etableras (till exempel 1000 ru:er).
   * Välj **OK**.

    ![Skärmbild av API för Gremlin, dialogrutan Lägg till diagram](./media/how-to-create-container/partitioned-collection-create-gremlin.png)

### <a name="table-api"></a><a id="portal-table"></a>Table API

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. [Skapa ett nytt Azure Cosmos-konto](create-table-dotnet.md#create-a-database-account)eller välj ett befintligt konto.

1. Öppna rutan **Datautforskaren** och välj **Ny tabell**. Ange därefter följande information:

   * Ange ett tabell-ID.
   * Ange ett dataflöde som ska etableras (till exempel 1000 ru:er).
   * Välj **OK**.

    ![Skärmbild av Tabell-API, dialogrutan Lägg till tabell](./media/how-to-create-container/partitioned-collection-create-table.png)

> [!Note]
> För tabell-API anges partitionsnyckeln varje gång du lägger till en ny rad.

## <a name="create-a-container-using-azure-cli"></a>Skapa en behållare med Azure CLI<a id="cli-sql"></a><a id="cli-mongodb"></a><a id="cli-cassandra"></a><a id="cli-gremlin"></a><a id="cli-table"></a>

Länkarna nedan visar hur du skapar behållarresurser för Azure Cosmos DB med Azure CLI.

För en lista över alla Azure CLI-exempel på alla Azure Cosmos DB API:er finns [i SQL API](cli-samples.md), [Cassandra API](cli-samples-cassandra.md), [MongoDB API](cli-samples-mongodb.md), [Gremlin API](cli-samples-gremlin.md)och [Tabell-API](cli-samples-table.md)

* [Skapa en behållare med Azure CLI](manage-with-cli.md#create-a-container)
* [Skapa en samling för Azure Cosmos DB för MongoDB API med Azure CLI](./scripts/cli/mongodb/create.md)
* [Skapa en Cassandra-tabell med Azure CLI](./scripts/cli/cassandra/create.md)
* [Skapa ett Gremlin-diagram med Azure CLI](./scripts/cli/gremlin/create.md)
* [Skapa en tabell-API-tabell med Azure CLI](./scripts/cli/table/create.md)

## <a name="create-a-container-using-powershella-idps-mongodba-idps-gremlin"></a>Skapa en behållare med PowerShell<a id="ps-sql"></a><a id="ps-mongodb"><a id="ps-cassandra"></a><a id="ps-gremlin"><a id="ps-table"></a>

Länkarna nedan visar hur du skapar behållarresurser för Azure Cosmos DB med PowerShell.

För en lista över alla Azure CLI-exempel på alla Azure Cosmos DB API:er finns [i SQL API](powershell-samples-sql.md), [Cassandra API](powershell-samples-cassandra.md), [MongoDB API](powershell-samples-mongodb.md), [Gremlin API](powershell-samples-gremlin.md)och [Tabell-API](powershell-samples-table.md)

* [Skapa en behållare med Powershell](manage-with-powershell.md#create-container)
* [Skapa en samling för Azure Cosmos DB för MongoDB API med Powershell](./scripts/powershell/mongodb/ps-mongodb-create.md)
* [Skapa en Cassandra-tabell med Powershell](./scripts/powershell/cassandra/ps-cassandra-create.md)
* [Skapa ett Gremlin-diagram med Powershell](./scripts/powershell/gremlin/ps-gremlin-create.md)
* [Skapa en tabell-API-tabell med Powershell](./scripts/powershell/table/ps-table-create.md)

## <a name="create-a-container-using-net-sdk"></a>Skapa en container med hjälp av .NET SDK

### <a name="sql-api-and-gremlin-api"></a><a id="dotnet-sql-graph"></a>SQL-API och Gremlin-API

```csharp
// Create a container with a partition key and provision 1000 RU/s throughput.
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "myContainerName";
myCollection.PartitionKey.Paths.Add("/myPartitionKey");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    myCollection,
    new RequestOptions { OfferThroughput = 1000 });
```

### <a name="azure-cosmos-db-api-for-mongodb"></a><a id="dotnet-mongodb"></a>API för Azure Cosmos DB för MongoDB

```csharp
// Create a collection with a partition key by using Mongo Shell:
db.runCommand( { shardCollection: "myDatabase.myCollection", key: { myShardKey: "hashed" } } )
```

> [!Note]
> MongoDB tråd protokoll förstår inte begreppet [begäran enheter](request-units.md). Om du vill skapa en ny samling med etablerat dataflöde på den använder du Azure-portalen eller Cosmos DB SDK:er för SQL API.

### <a name="cassandra-api"></a><a id="dotnet-cassandra"></a>API för Cassandra

```csharp
// Create a Cassandra table with a partition/primary key and provision 1000 RU/s throughput.
session.Execute(CREATE TABLE myKeySpace.myTable(
    user_id int PRIMARY KEY,
    firstName text,
    lastName text) WITH cosmosdb_provisioned_throughput=1000);
```

## <a name="next-steps"></a>Nästa steg

* [Partitionering i Azure Cosmos DB](partitioning-overview.md)
* [Enheter för programbegäran i Azure Cosmos DB](request-units.md)
* [Etablera dataflöde på containrar och databaser](set-throughput.md)
* [Arbeta med ett Azure Cosmos-konto](account-overview.md)
