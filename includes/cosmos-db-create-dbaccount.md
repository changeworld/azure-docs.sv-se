---
title: ta med fil
description: ta med fil
services: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.topic: include
ms.date: 04/08/2019
ms.custom: include file
ms.openlocfilehash: 9c6ce482a4f6464008f4be0ecd94455f0239ede2
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81275021"
---
1. Gå till [Microsoft Azure-portalen](https://portal.azure.com/) för att skapa ett Azure Cosmos DB-konto. Sök efter och välj **Azure Cosmos DB**.

   ![Azure Portal-databasfönstret](./media/cosmos-db-create-dbaccount/create-nosql-db-databases-json-tutorial-1.png)

1. Välj **Lägg till**.
1. Ange de grundläggande inställningarna för det nya Azure Cosmos-kontot på sidan **Skapa Azure Cosmos DB-konto**. 

    |Inställning|Värde|Beskrivning |
    |---|---|---|
    |Prenumeration|Prenumerationens namn|Välj den Azure-prenumeration som ska användas för det här Azure Cosmos-kontot. |
    |Resursgrupp|Namn på resursgrupp|Välj en resursgrupp eller välj **Skapa ny** och ange sedan ett unikt namn för den nya resursgruppen. |
    |Kontonamn|Ett unikt namn|Ange ett namn som identifierar ditt Azure Cosmos-konto. Eftersom*documents.azure.com* läggs till det namn du anger för att skapa din URI måste du använda ett unikt namn.<br><br>Namnet får endast innehålla gemener, siffror och bindestreck (-). Det måste innehålla 3–31 tecken.|
    |API|Typ av konto som skapas|Välj **Core (SQL)** för att skapa en dokumentdatabas och kör frågor med hjälp av SQL-syntax. <br><br>API:n avgör vilken typ av konto som skapas. Azure Cosmos DB innehåller fem API:er: Core (SQL) och MongoDB för dokumentdata, Gremlin för diagramdata, Azure Table och Cassandra. För närvarande måste du skapa ett separat konto för varje API. <br><br>[Läs mer om SQL API](../articles/cosmos-db/documentdb-introduction.md).|
    |Tillämpa rabatt på kostnadsfri nivå|Ansök eller Gäller inte|Med Azure Cosmos DB-kostnadsfri nivå får du de första 400 RU/s och 5 GB lagringsutrymme som är gratis på ett konto. Läs mer om [den kostnadsfria nivån](https://azure.microsoft.com/pricing/details/cosmos-db/).|
    |Location|Den region som är närmast dina användare|Välj en geografisk plats som värd för ditt Azure Cosmos DB-konto. Använd den plats som är närmast dina användare för att ge dem så snabb åtkomst till data som möjligt.|
    |Typ av konto|Produktion eller icke-produktion|Välj **Produktion** om kontot ska användas för en produktionsarbetsbelastning. Välj **Icke-produktion** om kontot ska användas för icke-produktion, t.ex. Det här är en Azure-resurstagginställning som trimmar portalupplevelsen men inte påverkar det underliggande Azure Cosmos DB-kontot. Du kan ändra det här värdet när som helst.|

    > [!NOTE]
    > Du kan ha upp till en kostnadsfri nivå Azure Cosmos DB-konto per Azure-prenumeration och måste anmäla dig när du skapar kontot. Om du inte ser alternativet att tillämpa rabatten på den kostnadsfria nivån innebär det att ett annat konto i prenumerationen redan har aktiverats med den kostnadsfria nivån.
   
   ![Den nya kontosidan för Azure Cosmos DB](./media/cosmos-db-create-dbaccount/azure-cosmos-db-create-new-account-detail.png)

1. Välj **Granska + skapa**. Du kan hoppa över avsnitten **Nätverk** och **Taggar**.

1. Granska kontoinställningarna och välj sedan **Skapa**. Det tar några minuter att skapa kontot. Vänta tills portalsidan visar meddelandet **Distributionen är klar**. 

    ![Meddelandefönstret i Azure-portalen](./media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created.png)

1. Välj **Gå till resurs** för att gå till sidan för Azure Cosmos DB-kontot. 

    ![Sidan för Azure Cosmos DB-kontot](./media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created-2.png)
