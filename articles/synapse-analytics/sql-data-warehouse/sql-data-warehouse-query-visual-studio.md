---
title: Anslut med VSTS
description: Fråga Azure Synapse Analytics med Visual Studio.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 08/15/2019
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: e986515a911cf1bbd88dfc73c56efcc6e81826d6
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351411"
---
# <a name="connect-to-azure-synapse-analytics-with-visual-studio-and-ssdt"></a>Ansluta till Azure Synapse Analytics med Visual Studio och SSDT
> [!div class="op_single_selector"]
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [Sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 
> * [SSMS (SSMS)](sql-data-warehouse-query-ssms.md)
> 
> 

Använd Visual Studio för att fråga en SQL-pool i Azure Synapse på bara några minuter. Den här metoden använder tillägget SQL Server Data Tools (SSDT) i Visual Studio 2019. 

## <a name="prerequisites"></a>Krav
Du behöver följande för att använda de här självstudierna:

* En befintlig SQL-pool. En skapa en finns i [Skapa en SQL-pool](create-data-warehouse-portal.md).
* SSDT för Visual Studio. Om du har Visual Studio har du förmodligen redan SSDT för Visual Studio. Installationsinstruktioner och alternativ finns i [Installera Visual Studio och SSDT](sql-data-warehouse-install-visual-studio.md).
* Det fullständigt kvalificerade servernamnet. Information om hur du hittar den här informationen finns i [Anslut till SQL-poolen](sql-data-warehouse-connect-overview.md).

## <a name="1-connect-to-your-sql-pool"></a>1. Anslut till din SQL-pool
1. Öppna Visual Studio 2019.
2. Öppna UTFORSKARen för SQL Server-objekt genom att välja **Visa** > **SQL Server-objektutforskaren**.
   
    ![SQL Server Object Explorer](./media/sql-data-warehouse-query-visual-studio/open-ssdt.png)
3. Klicka på ikonen **lägg till SQL Server**.
   
    ![Lägg till SQL Server](./media/sql-data-warehouse-query-visual-studio/add-server.png)
4. Fyll i fälten i fönstret anslut till server.
   
    ![Anslut till server](./media/sql-data-warehouse-query-visual-studio/connection-dialog.png)
   
   * **Servernamn**. Ange det **servernamn** som du identifierade tidigare.
   * **Autentisering**. Välj **SQL Server-autentisering** eller **Active Directory-integrerad autentisering**.
   * **Användarnamn** och **lösenord**. Ange användarnamn och lösenord om du valde SQL Server-autentisering ovan.
   * Klicka på **Anslut**.
5. Expandera din Azure SQL-server för att utforska. Du kan se de databaser som är associerade med servern. Expandera AdventureWorksDW för att se tabellerna i din exempeldatabas.
   
    ![Utforska AdventureWorksDW](./media/sql-data-warehouse-query-visual-studio/explore-sample.png)

## <a name="2-run-a-sample-query"></a>2. Kör en exempelfråga
När du nu etablerat en anslutning till din databas, är det dags att skriva en fråga.

1. Högerklicka på din databas i SQL Server Object Explorer.
2. Välj **ny fråga**. Ett nytt frågefönster öppnas.
   
    ![Ny fråga](./media/sql-data-warehouse-query-visual-studio/new-query2.png)
3. Kopiera följande T-SQL-fråga till frågefönstret:
   
    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```
4. Kör frågan genom att klicka på den gröna `CTRL` + `SHIFT` +pilen eller använd följande genväg: `E`.
   
    ![Kör frågan](./media/sql-data-warehouse-query-visual-studio/run-query.png)
5. Titta på frågeresultaten. I det här exemplet har tabellen FactInternetSales 60398 rader.
   
    ![Frågeresultat](./media/sql-data-warehouse-query-visual-studio/query-results.png)

## <a name="next-steps"></a>Nästa steg
Nu när du kan ansluta och fråga kan du prova [att visualisera data med Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md).

Information om hur du konfigurerar din miljö för Azure Active Directory-autentisering finns [i Autentisera till SQL-pool](sql-data-warehouse-authentication.md).