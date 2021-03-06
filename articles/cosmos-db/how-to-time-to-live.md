---
title: Konfigurera och hantera tid att leva i Azure Cosmos DB
description: Lär dig hur du konfigurerar och hanterar tid att leva på en behållare och ett objekt i Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/27/2020
ms.author: anfeldma
ms.openlocfilehash: 72653a3b28181316a2bf7dd7e73f2685c3afcf73
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2020
ms.locfileid: "80384270"
---
# <a name="configure-time-to-live-in-azure-cosmos-db"></a>Konfigurera Time to Live i Azure Cosmos DB

Du kan välja att konfigurera Time to Live (TTL) på containernivån i Azure Cosmos DB, eller så kan du åsidosätta det på en objektnivå när du har ställt in containern. Du kan konfigurera TTL för en container med hjälp av Azure-portalen eller språkspecifika SDK:er. Åsidosättningar av TTL på objektnivå kan konfigureras med hjälp av SDK:erna.

## <a name="enable-time-to-live-on-a-container-using-azure-portal"></a>Aktivera TTL för en container med hjälp av Azure-portalen

Använd följande steg för att aktivera TTL för en container utan upphörande. Aktivera det här alternativet om du vill tillåta att TTL åsidosätts på objektnivå. Du kan också ange TTL genom att ange ett annat värde än noll för sekunder.

1. Logga in på [Azure-portalen](https://portal.azure.com/).

2. Skapa ett nytt Azure Cosmos DB-konto eller välj ett befintligt konto.

3. Öppna fönstret **Datautforskaren**.

4. Välj en befintlig container, expandera den och ändra följande värden:

   * Öppna fönstret **Scale & Settings** (Skalning och inställningar).
   * Under **Inställning** letar du upp **Time to Live**.
   * Välj **På (inte standard)** eller välj **På** och ange ett TTL-värde
   * Klicka på **Spara** för att spara ändringarna.

   ![Konfigurera Time to Live i Azure-portalen](./media/how-to-time-to-live/how-to-time-to-live-portal.png)

* När DefaultTimeToLive är null är din tid att leva avstängd
* När DefaultTimeToLive är -1 är inställningen Tid till Live På (Ingen standard)
* När DefaultTimeToLive har något annat Int-värde (förutom 0) är inställningen Tid till Live På

## <a name="enable-time-to-live-on-a-container-using-azure-cli-or-powershell"></a>Aktivera tid för att leva på en behållare med Azure CLI eller PowerShell

Om du vill skapa eller aktivera TTL på en behållare finns

* [Skapa en behållare med TTL med Azure CLI](manage-with-cli.md#create-a-container-with-ttl)
* [Skapa en behållare med TTL med PowerShell](manage-with-powershell.md#create-container-unique-key-ttl)

## <a name="enable-time-to-live-on-a-container-using-sdk"></a>Aktivera TTL för en container med hjälp av SDK

### <a name="net-sdk-v2-microsoftazuredocumentdb"></a><a id="dotnet-enable-noexpiry"></a>.NET SDK V2 (Microsoft.Azure.DocumentDB)

```csharp
// Create a new container with TTL enabled and without any expiration value
DocumentCollection collectionDefinition = new DocumentCollection();
collectionDefinition.Id = "myContainer";
collectionDefinition.PartitionKey.Paths.Add("/myPartitionKey");
collectionDefinition.DefaultTimeToLive = -1; //(never expire by default)

DocumentCollection ttlEnabledCollection = await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    collectionDefinition);
```

### <a name="net-sdk-v3-microsoftazurecosmos"></a><a id="dotnet-enable-noexpiry"></a>.NET SDK V3 (Microsoft.Azure.Cosmos)

```csharp
// Create a new container with TTL enabled and without any expiration value
await client.GetDatabase("database").CreateContainerAsync(new ContainerProperties
{
    Id = "container",
    PartitionKeyPath = "/myPartitionKey",
    DefaultTimeToLive = -1 //(never expire by default)
});
```

### <a name="java-sdk-v4-maven-comazureazure-cosmos"></a><a id="java4-enable-noexpiry"></a>Java SDK V4 (Maven com.azure::azure-cosmos)

```java
CosmosAsyncContainer container;

// Create a new container with TTL enabled and without any expiration value
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");
containerProperties.setDefaultTimeToLiveInSeconds(-1);
container = database.createContainerIfNotExists(containerProperties, 400).block().getContainer();
```

### <a name="java-sdk-v3-maven-commicrosoftazureazure-cosmos"></a><a id="java3-enable-noexpiry"></a>Java SDK V3 (Maven com.microsoft.azure::azure-cosmos)

```java
CosmosContainer container;

// Create a new container with TTL enabled and without any expiration value
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");
containerProperties.defaultTimeToLive(-1);
container = database.createContainerIfNotExists(containerProperties, 400).block().container();
```

## <a name="set-time-to-live-on-a-container-using-sdk"></a>Ställa in TTL för en container med hjälp av SDK

Om du vill ställa in Time to Live för en container måste du ange ett positivt tal som inte är noll som anger tidsperioden i sekunder. Baserat på det konfigurerade TTL-värde tas alla objekt i containern efter den senast ändrade tidsstämpeln för objektet `_ts` bort.

### <a name="net-sdk-v2-microsoftazuredocumentdb"></a><a id="dotnet-enable-withexpiry"></a>.NET SDK V2 (Microsoft.Azure.DocumentDB)

```csharp
// Create a new container with TTL enabled and a 90 day expiration
DocumentCollection collectionDefinition = new DocumentCollection();
collectionDefinition.Id = "myContainer";
collectionDefinition.PartitionKey.Paths.Add("/myPartitionKey");
collectionDefinition.DefaultTimeToLive = 90 * 60 * 60 * 24 // expire all documents after 90 days

DocumentCollection ttlEnabledCollection = await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    collectionDefinition;
```

### <a name="net-sdk-v3-microsoftazurecosmos"></a><a id="dotnet-enable-withexpiry"></a>.NET SDK V3 (Microsoft.Azure.Cosmos)

```csharp
// Create a new container with TTL enabled and a 90 day expiration
await client.GetDatabase("database").CreateContainerAsync(new ContainerProperties
{
    Id = "container",
    PartitionKeyPath = "/myPartitionKey",
    DefaultTimeToLive = 90 * 60 * 60 * 24; // expire all documents after 90 days
});
```

### <a name="nodejs-sdk"></a><a id="nodejs-enable-withexpiry"></a>NodeJS SDK

```javascript
const containerDefinition = {
          id: "sample container1",
        };

async function createcontainerWithTTL(db: Database, containerDefinition: ContainerDefinition, collId: any, defaultTtl: number) {
      containerDefinition.id = collId;
      containerDefinition.defaultTtl = defaultTtl;
      await db.containers.create(containerDefinition);
}
```

### <a name="java-sdk-v4-maven-comazureazure-cosmos"></a><a id="java4-enable-defaultexpiry"></a>Java SDK V4 (Maven com.azure::azure-cosmos)

```java
CosmosAsyncContainer container;

// Create a new container with TTL enabled with default expiration value
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");
containerProperties.setDefaultTimeToLiveInSeconds(90 * 60 * 60 * 24);
container = database.createContainerIfNotExists(containerProperties, 400).block().getContainer();
```

### <a name="java-sdk-v3-maven-commicrosoftazureazure-cosmos"></a><a id="java3-enable-defaultexpiry"></a>Java SDK V3 (Maven com.microsoft.azure::azure-cosmos)

```java
CosmosContainer container;

// Create a new container with TTL enabled with default expiration value
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");
containerProperties.defaultTimeToLive(90 * 60 * 60 * 24);
container = database.createContainerIfNotExists(containerProperties, 400).block().container();
```

## <a name="set-time-to-live-on-an-item"></a>Ställa in TTL-värde på ett objekt

Förutom att ställa in ett standard-TTL-värde i en container kan du ange du en tid för ett objekt. När du ställer in Time to Live på objektnivå åsidosätts standard-TTL för det objektet i containern.

* Om du vill ställa in TTL-värdet för ett objekt måste du ange ett positivt tal som inte är noll, vilket anger inom vilken period i sekunder som objektet ska upphöra att gälla efter den senast ändrade tidsstämpeln för objektet `_ts`.

* Om objektet inte har något TTL-fält tillämpas som standard TTL-värdet som angetts för containern på objektet.

* Om TTL är inaktiverat på containernivå ignoreras TTL-fältet på objektet tills TTL återaktiverats för containern.

### <a name="azure-portal"></a><a id="portal-set-ttl-item"></a>Azure-portal

Gör så här för att aktivera tid för att leva på ett objekt:

1. Logga in på [Azure-portalen](https://portal.azure.com/).

2. Skapa ett nytt Azure Cosmos DB-konto eller välj ett befintligt konto.

3. Öppna fönstret **Datautforskaren**.

4. Välj en befintlig container, expandera den och ändra följande värden:

   * Öppna fönstret **Scale & Settings** (Skalning och inställningar).
   * Under **Inställning** letar du upp **Time to Live**.
   * Välj **På (ingen standard)** eller välj **På** och ange ett TTL-värde. 
   * Klicka på **Spara** för att spara ändringarna.

5. Nästa navigera till det objekt som du vill ange `ttl` tid för att leva, lägga till egenskapen och välj **Uppdatera**. 

   ```json
   {
    "id": "1",
    "_rid": "Jic9ANWdO-EFAAAAAAAAAA==",
    "_self": "dbs/Jic9AA==/colls/Jic9ANWdO-E=/docs/Jic9ANWdO-EFAAAAAAAAAA==/",
    "_etag": "\"0d00b23f-0000-0000-0000-5c7712e80000\"",
    "_attachments": "attachments/",
    "ttl": 10,
    "_ts": 1551307496
   }
   ```

### <a name="net-sdk-any"></a><a id="dotnet-set-ttl-item"></a>.NET SDK (valfritt)

```csharp
// Include a property that serializes to "ttl" in JSON
public class SalesOrder
{
    [JsonProperty(PropertyName = "id")]
    public string Id { get; set; }
    [JsonProperty(PropertyName="cid")]
    public string CustomerId { get; set; }
    // used to set expiration policy
    [JsonProperty(PropertyName = "ttl", NullValueHandling = NullValueHandling.Ignore)]
    public int? ttl { get; set; }

    //...
}
// Set the value to the expiration in seconds
SalesOrder salesOrder = new SalesOrder
{
    Id = "SO05",
    CustomerId = "CO18009186470",
    ttl = 60 * 60 * 24 * 30;  // Expire sales orders in 30 days
};
```

### <a name="nodejs-sdk"></a><a id="nodejs-set-ttl-item"></a>NodeJS SDK

```javascript
const itemDefinition = {
          id: "doc",
          name: "sample Item",
          key: "value",
          ttl: 2
        };
```

### <a name="java-sdk-v4-maven-comazureazure-cosmos"></a><a id="java4-enable-itemexpiry"></a>Java SDK V4 (Maven com.azure::azure-cosmos)

```java
// Include a property that serializes to "ttl" in JSON
public class SalesOrder
{
    private String id;
    private String customerId;
    private Integer ttl;

    public SalesOrder(String id, String customerId, Integer ttl) {
        this.id = id;
        this.customerId = customerId;
        this.ttl = ttl;
    }

    public String getId() {return this.id;}
    public void setId(String new_id) {this.id = new_id;}
    public String getCustomerId() {return this.customerId;}
    public void setCustomerId(String new_cid) {this.customerId = new_cid;}
    public Integer getTtl() {return this.ttl;}
    public void setTtl(Integer new_ttl) {this.ttl = new_ttl;}

    //...
}

// Set the value to the expiration in seconds
SalesOrder salesOrder = new SalesOrder(
    "SO05",
    "CO18009186470",
    60 * 60 * 24 * 30  // Expire sales orders in 30 days
);

```

### <a name="java-sdk-v3-maven-commicrosoftazureazure-cosmos"></a><a id="java3-enable-itemexpiry"></a>Java SDK V3 (Maven com.microsoft.azure::azure-cosmos)

```java
// Include a property that serializes to "ttl" in JSON
public class SalesOrder
{
    private String id;
    private String customerId;
    private Integer ttl;

    public SalesOrder(String id, String customerId, Integer ttl) {
        this.id = id;
        this.customerId = customerId;
        this.ttl = ttl;
    }

    public String id() {return this.id;}
    public void id(String new_id) {this.id = new_id;}
    public String customerId() {return this.customerId;}
    public void customerId(String new_cid) {this.customerId = new_cid;}
    public Integer ttl() {return this.ttl;}
    public void ttl(Integer new_ttl) {this.ttl = new_ttl;}

    //...
}

// Set the value to the expiration in seconds
SalesOrder salesOrder = new SalesOrder(
    "SO05",
    "CO18009186470",
    60 * 60 * 24 * 30  // Expire sales orders in 30 days
);

```

## <a name="reset-time-to-live"></a>Återställa Time to live

Du kan återställa time to live på ett objekt genom att utföra en skriv- eller uppdateringsåtgärd för objektet. Skriv- eller uppdateringsåtgärden ställer in `_ts` på den aktuella tiden och TTL-värdet för objektet som ska upphöra att gälla påbörjas igen. Om du vill ändra TTL-värdet för ett objekt kan du uppdatera fältet precis som du uppdaterar andra fält.

### <a name="net-sdk-v2-microsoftazuredocumentdb"></a><a id="dotnet-extend-ttl-item"></a>.NET SDK V2 (Microsoft.Azure.DocumentDB)

```csharp
// This examples leverages the Sales Order class above.
// Read a document, update its TTL, save it.
response = await client.ReadDocumentAsync(
    "/dbs/salesdb/colls/orders/docs/SO05"),
    new RequestOptions { PartitionKey = new PartitionKey("CO18009186470") });

Document readDocument = response.Resource;
readDocument.ttl = 60 * 30 * 30; // update time to live
response = await client.ReplaceDocumentAsync(readDocument);
```

### <a name="net-sdk-v3-microsoftazurecosmos"></a><a id="dotnet-extend-ttl-item"></a>.NET SDK V3 (Microsoft.Azure.Cosmos)

```csharp
// This examples leverages the Sales Order class above.
// Read a document, update its TTL, save it.
ItemResponse<SalesOrder> itemResponse = await client.GetContainer("database", "container").ReadItemAsync<SalesOrder>("SO05", new PartitionKey("CO18009186470"));

itemResponse.Resource.ttl = 60 * 30 * 30; // update time to live
await client.GetContainer("database", "container").ReplaceItemAsync(itemResponse.Resource, "SO05");
```

### <a name="java-sdk-v4-maven-comazureazure-cosmos"></a><a id="java4-enable-modifyitemexpiry"></a>Java SDK V4 (Maven com.azure::azure-cosmos)

```java
// This examples leverages the Sales Order class above.
// Read a document, update its TTL, save it.
CosmosAsyncItemResponse<SalesOrder> itemResponse = container.readItem("SO05", new PartitionKey("CO18009186470"), SalesOrder.class)
        .flatMap(readResponse -> {
            SalesOrder salesOrder = readResponse.getItem();
            salesOrder.setTtl(60 * 30 * 30);
            return container.createItem(salesOrder);
}).block();
```

### <a name="java-sdk-v3-maven-commicrosoftazureazure-cosmos"></a><a id="java3-enable-modifyitemexpiry"></a>Java SDK V3 (Maven com.microsoft.azure::azure-cosmos)

```java
// This examples leverages the Sales Order class above.
// Read a document, update its TTL, save it.
container.getItem("SO05", new PartitionKey("CO18009186470")).read()
        .flatMap(readResponse -> {
            SalesOrder salesOrder = null;
            try {
                salesOrder = readResponse.properties().getObject(SalesOrder.class);
            } catch (Exception err) {

            }
            salesOrder.ttl(60 * 30 * 30);
            return container.createItem(salesOrder);
}).block();
```

## <a name="turn-off-time-to-live"></a>Stäng av Time to Live

Om Time to Live har angetts för ett objekt och du inte längre vill att objektet ska att upphöra att gälla kan du sedan hämta objektet, ta bort TTL-fältet och ersätta objektet på servern. När TTL-fältet tas bort från objektet tillämpas standard-TTL-värdet som tilldelats containern på objektet. Ställ i TTL-värdet på -1 för att förhindra att ett objekt upphör att gälla och för att inte ärva TTL-värdet från containern.

### <a name="net-sdk-v2-microsoftazuredocumentdb"></a><a id="dotnet-turn-off-ttl-item"></a>.NET SDK V2 (Microsoft.Azure.DocumentDB)

```csharp
// This examples leverages the Sales Order class above.
// Read a document, turn off its override TTL, save it.
response = await client.ReadDocumentAsync(
    "/dbs/salesdb/colls/orders/docs/SO05"),
    new RequestOptions { PartitionKey = new PartitionKey("CO18009186470") });

Document readDocument = response.Resource;
readDocument.ttl = null; // inherit the default TTL of the container

response = await client.ReplaceDocumentAsync(readDocument);
```

### <a name="net-sdk-v3-microsoftazurecosmos"></a><a id="dotnet-turn-off-ttl-item"></a>.NET SDK V3 (Microsoft.Azure.Cosmos)

```csharp
// This examples leverages the Sales Order class above.
// Read a document, turn off its override TTL, save it.
ItemResponse<SalesOrder> itemResponse = await client.GetContainer("database", "container").ReadItemAsync<SalesOrder>("SO05", new PartitionKey("CO18009186470"));

itemResponse.Resource.ttl = null; // inherit the default TTL of the container
await client.GetContainer("database", "container").ReplaceItemAsync(itemResponse.Resource, "SO05");
```

### <a name="java-sdk-v4-maven-comazureazure-cosmos"></a><a id="java4-enable-itemdefaultexpiry"></a>Java SDK V4 (Maven com.azure::azure-cosmos)

```java
// This examples leverages the Sales Order class above.
// Read a document, update its TTL, save it.
CosmosAsyncItemResponse<SalesOrder> itemResponse = container.readItem("SO05", new PartitionKey("CO18009186470"), SalesOrder.class)
        .flatMap(readResponse -> {
            SalesOrder salesOrder = readResponse.getItem();
            salesOrder.setTtl(null);
            return container.createItem(salesOrder);
}).block();
```

### <a name="java-sdk-v3-maven-commicrosoftazureazure-cosmos"></a><a id="java3-enable-itemdefaultexpiry"></a>Java SDK V3 (Maven com.microsoft.azure::azure-cosmos)

```java
// This examples leverages the Sales Order class above.
// Read a document, update its TTL, save it.
container.getItem("SO05", new PartitionKey("CO18009186470")).read()
        .flatMap(readResponse -> {
            SalesOrder salesOrder = null;
            try {
                salesOrder = readResponse.properties().getObject(SalesOrder.class);
            } catch (Exception err) {

            }
            salesOrder.ttl(null);
            return container.createItem(salesOrder);
}).block();
```

## <a name="disable-time-to-live"></a>Inaktivera Time to live

Om du vill inaktivera Time to Live för en container och förhindra att bakgrundsprocessen söker efter objekt som har upphört att gälla ska du ta bort egenskapen `DefaultTimeToLive` för containern. Att ta bort den här egenskapen skiljer sig från att ställa in den på -1. När du ställer in den på -1 finns nya objekt som läggs till i containern kvar för evigt, men du kan åsidosätta det här värdet för specifika objekt i containern. När du tar bort TTL-egenskapen från behållaren upphör artiklarna aldrig att upphöra att gälla, även om det finns de uttryckligen har åsidosatt det tidigare standard-TTL-värdet.

### <a name="net-sdk-v2-microsoftazuredocumentdb"></a><a id="dotnet-disable-ttl"></a>.NET SDK V2 (Microsoft.Azure.DocumentDB)

```csharp
// Get the container, update DefaultTimeToLive to null
DocumentCollection collection = await client.ReadDocumentCollectionAsync("/dbs/salesdb/colls/orders");
// Disable TTL
collection.DefaultTimeToLive = null;
await client.ReplaceDocumentCollectionAsync(collection);
```

### <a name="net-sdk-v3-microsoftazurecosmos"></a><a id="dotnet-disable-ttl"></a>.NET SDK V3 (Microsoft.Azure.Cosmos)

```csharp
// Get the container, update DefaultTimeToLive to null
ContainerResponse containerResponse = await client.GetContainer("database", "container").ReadContainerAsync();
// Disable TTL
containerResponse.Resource.DefaultTimeToLive = null;
await client.GetContainer("database", "container").ReplaceContainerAsync(containerResponse.Resource);
```

### <a name="java-sdk-v4-maven-comazureazure-cosmos"></a><a id="java4-enable-disableexpiry"></a>Java SDK V4 (Maven com.azure::azure-cosmos)

```java
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");
// Disable TTL
containerProperties.setDefaultTimeToLiveInSeconds(null);
// Update container settings
container.replace(containerProperties).block();
```

### <a name="java-sdk-v3-maven-commicrosoftazureazure-cosmos"></a><a id="java3-enable-disableexpiry"></a>Java SDK V3 (Maven com.microsoft.azure::azure-cosmos)

```java
CosmosContainer container;

// Create a new container with TTL enabled and without any expiration value
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");
// Disable TTL
containerProperties.defaultTimeToLive(null);
// Update container settings
container = database.createContainerIfNotExists(containerProperties, 400).block().container();
```

## <a name="next-steps"></a>Nästa steg

Läs mer om Time to Live i följande artikel:

* [Dags att leva](time-to-live.md)
