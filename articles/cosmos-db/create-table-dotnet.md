---
title: 'Snabbstart: Tabell-API med .NET – Azure Cosmos DB'
description: Den här snabbstarten visar hur du använder tabell-API i Azure Cosmos DB för att skapa ett program med Azure-portalen och .NET
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 05/21/2019
ms.author: sngun
ms.openlocfilehash: 629adfe558aec71e156e50c75aa0891eac5a8bcf
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "79240172"
---
# <a name="quickstart-build-a-table-api-app-with-net-sdk-and-azure-cosmos-db"></a>Snabbstart: Skapa en tabell-API-app med .NET SDK och Azure Cosmos DB 

> [!div class="op_single_selector"]
> * [.NET](create-table-dotnet.md)
> * [Java](create-table-java.md)
> * [Node.js](create-table-nodejs.md)
> * [Python](create-table-python.md)
>  

Den här snabbstarten visar hur du använder [tabell-API](table-introduction.md) i .NET och Azure Cosmos DB för att skapa en app genom att klona ett exempel från GitHub. Den här snabbstarten visar även hur du skapar ett Azure Cosmos DB-konto och hur du använder Datautforskaren för att skapa tabeller och entiteter i den webbaserade Azure-portalen.

## <a name="prerequisites"></a>Krav

Om du inte redan har Installerat Visual Studio 2019 kan du ladda ned och använda den **kostnadsfria** [Visual Studio 2019 Community Edition.](https://www.visualstudio.com/downloads/) Se till att du aktiverar **Azure-utveckling** under installationen av Visual Studio.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>Skapa ett databaskonto

[!INCLUDE [cosmos-db-create-dbaccount-table](../../includes/cosmos-db-create-dbaccount-table.md)]

## <a name="add-a-table"></a>Lägg till en tabell

[!INCLUDE [cosmos-db-create-table](../../includes/cosmos-db-create-table.md)]

## <a name="add-sample-data"></a>Lägga till exempeldata

[!INCLUDE [cosmos-db-create-table-add-sample-data](../../includes/cosmos-db-create-table-add-sample-data.md)]

## <a name="clone-the-sample-application"></a>Klona exempelprogrammet

Nu ska vi klona en Table-app från GitHub, ange anslutningssträngen och köra appen. Du kommer att se hur lätt det är att arbeta med data programmässigt. 

1. Öppna en kommandotolk, skapa en ny mapp som heter git-samples och stäng sedan kommandotolken.

   ```bash
   md "C:\git-samples"
   ```

2. Öppna ett git-terminalfönster, t.ex. git bash, och använd kommandot `cd` för att ändra till den nya mappen där du vill installera exempelappen.

   ```bash
   cd "C:\git-samples"
   ```

3. Klona exempellagringsplatsen med följande kommando. Detta kommando skapar en kopia av exempelappen på din dator.

   ```bash
   git clone https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started.git
   ```

## <a name="open-the-sample-application-in-visual-studio"></a>Öppna exempelappen i Visual Studio

1. I Visual Studio: Välj **Öppna** på **Arkiv**-menyn och välj sedan **Projekt/lösning**. 

   ![Öppna lösningen](media/create-table-dotnet/azure-cosmosdb-open-solution.png) 

2. Navigera till mappen där du klonade exempelprogrammet och öppnar filen TableStorage.sln.

## <a name="update-your-connection-string"></a>Uppdatera din anslutningssträng

Gå nu tillbaka till Azure Portal för att hämta information om din anslutningssträng och kopiera den till appen. På så vis kan appen kommunicera med den värdbaserade databasen. 

1. I [Azure-portalen](https://portal.azure.com/) klickar du på **Anslutningssträng**. Använd knapparna på höger sida av fönstret för att kopiera **PRIMÄR ANSLUTNINGSSTRÄNG**.

   ![Visa och kopiera PRIMÄR ANSLUTNINGSSTRÄNG i fönstret Anslutningssträng](./media/create-table-dotnet/connection-string.png)

2. Öppna filen **Settings.json** i Visual Studio. 

3. Klistra in **den primära anslutningssträngen** från portalen i storageconnectionstring-värdet. Klistra in strängen innanför citattecknen.

   ```csharp
   {
      "StorageConnectionString": "<Primary connection string from Azure portal>"
   }
   ```

4. Spara filen **Settings.json** genom att trycka på CTRL+S.

Du har nu uppdaterat appen med all information som behövs för kommunikation med Azure Cosmos DB. 

## <a name="build-and-deploy-the-app"></a>Skapa och distribuera appen

1. Högerklicka på **Projektet CosmosTableSamples** i Solution **Explorer** i Visual Studio och klicka sedan på **Hantera NuGet-paket**. 

   ![Hantera NuGet-paket](media/create-table-dotnet/azure-cosmosdb-manage-nuget.png)

2. Skriv Microsoft.Azure.Cosmos.Table i rutan NuGet **Bläddra.** Därmed hittas klientbiblioteket Cosmos DB Table API. Observera att det här biblioteket för närvarande är tillgängligt för .NET Framework och .NET Standard. 
   
   ![Fliken Bläddra i NuGet](media/create-table-dotnet/azure-cosmosdb-nuget-browse.png)

3. Klicka på **Installera** om du vill installera biblioteket **Microsoft.Azure.Cosmos.Table.** Därmed installeras Azure Cosmos DB Table API-paketet och alla beroenden.

4. När du kör hela appen infogas exempeldata i tabellentiteten och tas bort i slutet så att du inte ser några data infogas om du kör hela exemplet. Du kan dock infoga vissa brytpunkter för att visa data. Öppna BasicSamples.cs fil och högerklicka på rad 52, välj **Brytpunkt**och välj sedan **Infoga brytpunkt**. Infoga en annan brytpunkt på rad 55.

   ![Lägga till en brytpunkt](media/create-table-dotnet/azure-cosmosdb-breakpoint.png) 

5. Tryck på F5 för att köra appen. Konsolfönstret visar namnet på den nya tabelldatabasen (i det här fallet demoa13b1) i Azure Cosmos DB. 
    
   ![Konsolutdata](media/create-table-dotnet/azure-cosmosdb-console.png)

   När du kommer till den första brytpunkten går du tillbaka till Datautforskaren i Azure Portal. Klicka på **Uppdatera**, expandera demotabellen* och klicka på **Entiteter**. Fliken **Entiteter** till höger visar den nya entiteten som har lagts till för Walter Harp. Lägg märke till att telefonnumret för den nya entiteten är 425-555-0101.

   ![Ny entitet](media/create-table-dotnet/azure-cosmosdb-entity.png)
    
   Om du får ett felmeddelande om att filen Settings.json inte kan hittas när du kör projektet kan du lösa det genom att lägga till följande XML-post i projektinställningarna. Högerklicka på CosmosTableSamples, välj Redigera CosmosTableSamples.csproj och lägg till följande itemGroup: 

   ```csharp
     <ItemGroup>
       <None Update="Settings.json">
         <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
       </None>
     </ItemGroup>
   ```

6. Stäng fliken **Entiteter** i Data Explorer.
    
7. Tryck på F5 för att köra appen till nästa brytpunkt. 

    När du kommer till brytpunkten växlar du tillbaka till Azure Portal och klickar på **Entiteter** igen för att öppna fliken **Entiteter**. Du kan nu se att telefonnumret har uppdaterats till 425-555-0105.

8. Tryck på F5 för att köra appen. 
 
   Appen lägger till entiteter som kan användas i en avancerad exempelapp som tabell-API:t inte stöder för närvarande. Appen tar sedan bort tabellen som skapats av exempelappen.

9. I konsolfönstret trycker du på Retur för att avsluta körningen av appen. 
  

## <a name="review-slas-in-the-azure-portal"></a>Granska serviceavtal i Azure-portalen

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du fått lära dig att skapa ett Azure Cosmos DB-konto, skapa en tabell med datautforskaren och att köra en app.  Du kan nu ställa frågor mot dina data med tabell-API:t.  

> [!div class="nextstepaction"]
> [Importera tabelldata till tabell-API](table-import.md)

