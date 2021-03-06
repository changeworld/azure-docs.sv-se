---
title: 'Snabbstart: Skapa en Python-app med Azure Cosmos DB SQL API-konto'
description: Presenterar ett Python-kodexempel som du kan använda för att ansluta till och fråga Azure Cosmos DB SQL API:t
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: python
ms.topic: quickstart
ms.date: 03/09/2020
ms.author: sngun
ms.custom:
- seodec18
- seo-javascript-september2019
- seo-python-october2019
ms.openlocfilehash: 10247e22b3fbe1250a15b06a0cce974905ca6b7f
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "78942628"
---
# <a name="quickstart-build-a-python-application-using-an-azure-cosmos-db-sql-api-account"></a>Snabbstart: Skapa ett Python-program med ett Azure Cosmos DB SQL API-konto

> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

I den här snabbstarten skapar och hanterar du ett Azure Cosmos DB SQL API-konto från Azure-portalen och från Visual Studio Code med en Python-app klonad från GitHub. Azure Cosmos DB är en databastjänst med flera modeller som gör att du snabbt kan skapa och fråga dokument-, tabell-, nyckelvärde- och diagramdatabaser med globala distributions- och vågräta skalfunktioner.

## <a name="prerequisites"></a>Krav

- Ett Azure-konto med en aktiv prenumeration. [Skapa en gratis](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Eller [prova Azure Cosmos DB gratis](https://azure.microsoft.com/try/cosmosdb/) utan en Azure-prenumeration. Du kan också använda [Azure Cosmos DB Emulator](https://aka.ms/cosmosdb-emulator) med en URI av `https://localhost:8081` och nyckeln `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==`.
- [Python 3.6+](https://www.python.org/downloads/), `python` med den `PATH`körbara i din .
- [Visual Studio Code](https://code.visualstudio.com/).
- [Python-tillägget för Visual Studio-kod](https://marketplace.visualstudio.com/items?itemName=ms-python.python#overview).
- [Git](https://www.git-scm.com/downloads). 

## <a name="create-a-database-account"></a>Skapa ett databaskonto

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-container"></a>Lägga till en container

Du kan nu använda datautforskaren verktyget i Azure-portalen för att skapa en databas och behållare. 

1. Välj**ny behållare för** **datautforskaren** > . 
    
    Området **Lägg till behållare** visas längst till höger, du kan behöva rulla åt höger för att se det.

    ![Datautforskaren på Azure-portalen, fönstret Lägg till container](./media/create-sql-api-python/azure-cosmosdb-data-explorer.png)

2. På sidan **Lägg till behållar** anger du inställningarna för den nya behållaren.

    |Inställning|Föreslaget värde|Beskrivning
    |---|---|---|
    |**Databas-ID**|Aktiviteter|Ange *Uppgifter* som namn på den nya databasen. Databasnamn måste innehålla 1–255 tecken och får inte innehålla `/, \\, #, ?`, eller avslutande blanksteg. Kontrollera **alternativet Etablera databasdataflöde,** det låter dig dela dataflödet som etablerats i databasen över alla behållare i databasen. Det här alternativet hjälper också till med kostnadsbesparingar. |
    |**Dataflöde**|400|Lämna dataflödet på 400 begäranheter per sekund (RU/s). Du kan skala upp dataflödet senare om du vill minska svarstiden.| 
    |**Container-ID**|Objekt|Ange *objekt* som namn på den nya behållaren. För container-ID:n gäller samma teckenkrav som för databasnamn.|
    |**Partitionsnyckeln**| /category| I exemplet som beskrivs i den här artikeln används */category* som partitionsnyckel.|
    
    Förutom föregående inställningar kan du också lägga till **unika nycklar** för behållaren. Vi lämnar fältet tomt i det här exemplet. Unika nycklar ger utvecklarna möjlighet att lägga till ett lager med dataintegritet till databasen. Genom att skapa en unik nyckelprincip när du skapar en behållare säkerställer du att ett eller flera värden per partitionsnyckel är unika. Läs mer i artikeln om [unika nycklar i Azure Cosmos DB](unique-keys.md).
    
    Välj **OK**. Datautforskaren visar den nya databasen och containern.

## <a name="add-sample-data"></a>Lägga till exempeldata

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>Fråga dina data

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>Klona exempelprogrammet

Nu ska vi klona vi en SQL API-app från GitHub, ange anslutningssträngen och köra appen. Den här snabbstarten använder version 4 av [Python SDK](https://pypi.org/project/azure-cosmos/#history).

1. Öppna en kommandotolk, skapa en ny mapp som heter git-samples och stäng sedan kommandotolken.

    ```cmd
    md "git-samples"
    ```
   Om du använder en bash-prompt bör du i stället använda följande kommando:

   ```bash
   mkdir "git-samples"
   ```

2. Öppna ett git-terminalfönster, t.ex. git bash, och använd kommandot `cd` för att ändra till den nya mappen där du vill installera exempelappen.

    ```bash
    cd "git-samples"
    ```

3. Klona exempellagringsplatsen med följande kommando. Detta kommando skapar en kopia av exempelappen på din dator. 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-python-getting-started.git
    ```  

## <a name="update-your-connection-string"></a>Uppdatera din anslutningssträng

Gå nu tillbaka till Azure Portal för att hämta information om din anslutningssträng och kopiera den till appen.

1. I ditt Azure Cosmos DB-konto i [Azure-portalen](https://portal.azure.com/)väljer du **Nycklar** från vänster navigering. Använd kopieringsknapparna till höger på skärmen för att kopiera **URI-filen** och **primärnyckeln** till *filen cosmos_get_started.py* i nästa steg.

    ![Hämta en åtkomstnyckel och URI i nycklarna-inställningarna i Azure-portalen](./media/create-sql-api-dotnet/access-key-and-uri-in-keys-settings-in-the-azure-portal.png)

2. Öppna *filen cosmos_get_started.py* i Visual Studio-kod i *\git-samples\azure-cosmos-db-python-getting-started*.

3. Kopiera **URI-värdet** från portalen (med kopieringsknappen) och gör det till värdet för **slutpunktsvariabeln** i *cosmos_get_started.py*. 

    `endpoint = 'https://FILLME.documents.azure.com',`

4. Kopiera sedan ditt **primärnyckelvärde** från portalen och gör det till värdet för **nyckeln** i *cosmos_get_started.py*. Du har nu uppdaterat appen med all information som behövs för kommunikation med Azure Cosmos DB. 

    `key = 'FILLME'`

5. Spara *filen cosmos_get_started.py.*

## <a name="review-the-code"></a>Granska koden

Det här steget är valfritt. Lär dig mer om databasresurserna som skapats i kod eller gå vidare till [Uppdatera anslutningssträngen](#update-your-connection-string).

Följande utdrag hämtas alla från filen *cosmos_get_started.py.*

* CosmosClient initieras. Se till att uppdatera värdena "slutpunkt" och "nyckel" enligt beskrivningen i avsnittet [Uppdatera anslutningssträngen.](#update-your-connection-string) 

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=create_cosmos_client)]

* En ny databas skapas.

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=create_database_if_not_exists)]

* En ny behållare skapas, med 400 RU/s [av etablerat dataflöde](request-units.md). Vi `lastName` väljer som [partitionsnyckel](partitioning-overview.md#choose-partitionkey), vilket gör att vi kan göra effektiva frågor som filtrerar på den här egenskapen. 

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=create_container_if_not_exists)]

* Vissa objekt läggs till i containern. Behållare är en samling objekt (JSON-dokument) som kan ha varierat schema. Hjälpmetoderna ```get_[name]_family_item``` returnerar representationer av en familj som lagras i Azure Cosmos DB som JSON-dokument.

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=create_item)]

* Punktläsningar (nyckelvärdesökningar) utförs med `read_item` metoden. Vi skriver ut [RU-laddningen för](request-units.md) varje operation.

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=read_item)]

* En fråga utförs med hjälp av SQL-frågesyntax. Eftersom vi använder partitionsnyckelvärden ```lastName``` i WHERE-satsen dirigerar Azure Cosmos DB effektivt den här frågan till relevanta partitioner, vilket förbättrar prestanda.

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=query_items)]
   
## <a name="run-the-app"></a>Kör appen

1. Välj **Visa** > **kommandopalett**i Visual Studio-kod . 

2. I kommandotolken anger du **Python: Välj tolk** och välj sedan den version av Python som ska användas.

    Sidfoten i Visual Studio Code uppdateras för att ange vilken tolk som valts. 

3. Välj **Visa** > **integrerad terminal** för att öppna den integrerade terminalen för Visual Studio-kod.

4. I det integrerade terminalfönstret kontrollerar du att du befinner dig i mappen *azure-cosmos-db-python-getting-started.* Om du inte är det kör du följande kommando för att växla till exempelmappen. 

    ```cmd
    cd "\git-samples\azure-cosmos-db-python-getting-started"`
    ```

5. Kör följande kommando för att installera paketet azure-cosmos. 

    ```python
    pip install --pre azure-cosmos
    ```

    Om du får ett felmeddelande om nekad åtkomst när du försöker installera azure-cosmos måste du [köra VS Code som administratör](https://stackoverflow.com/questions/37700536/visual-studio-code-terminal-how-to-run-a-command-with-administrator-rights).

6. Kör följande kommando för att köra exemplet och skapa och lagra nya dokument i Azure Cosmos DB.

    ```python
    python cosmos_get_started.py
    ```

7. Om du vill bekräfta att de nya objekten har skapats och sparats väljer du > **AzureSampleFamilyDatabase** > **Items**i Azure-portalen . **Data Explorer** Visa de objekt som har skapats. Här är till exempel ett exempel på JSON-dokument för familjen Andersen:
   
   ```json
   {
       "id": "Andersen-1569479288379",
       "lastName": "Andersen",
       "district": "WA5",
       "parents": [
           {
               "familyName": null,
               "firstName": "Thomas"
           },
           {
               "familyName": null,
               "firstName": "Mary Kay"
           }
       ],
       "children": null,
       "address": {
           "state": "WA",
           "county": "King",
           "city": "Seattle"
       },
       "registered": true,
       "_rid": "8K5qAIYtZXeBhB4AAAAAAA==",
       "_self": "dbs/8K5qAA==/colls/8K5qAIYtZXc=/docs/8K5qAIYtZXeBhB4AAAAAAA==/",
       "_etag": "\"a3004d78-0000-0800-0000-5d8c5a780000\"",
       "_attachments": "attachments/",
       "_ts": 1569479288
   }
   ```

## <a name="review-slas-in-the-azure-portal"></a>Granska serviceavtal i Azure-portalen

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du lärt dig hur du skapar ett Azure Cosmos DB-konto, skapar en behållare med Data Explorer och kör en Python-app i Visual Studio-kod. Du kan nu importera ytterligare data till ditt Azure Cosmos DB-konto. 

> [!div class="nextstepaction"]
> [Importera data till Azure Cosmos DB för användning med SQL API:t](import-data.md)


