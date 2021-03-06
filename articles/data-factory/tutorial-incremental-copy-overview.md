---
title: Kopiera data stegvis efter
description: Dessa självstudier visar hur du inkrementellt kopierar data från ett källdatalager till ett måldatalager. Den första kopierar data från en tabell.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
manager: anandsub
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 01/22/2018
ms.openlocfilehash: e87f8d648a617aab24008bd63596db31dd24ab68
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "74926438"
---
# <a name="incrementally-load-data-from-a-source-data-store-to-a-destination-data-store"></a>Läsa in data stegvis från ett källdatalager till ett måldatalager

I en dataintegrationslösning är stegvis inläsning av data (eller deltadata) efter den första fullständiga datainläsningen ett vanligt scenario. Självstudierna i det här avsnittet visar olika sätt att läsa in data inkrementellt med Azure Data Factory.

## <a name="delta-data-loading-from-database-by-using-a-watermark"></a>Deltadatainläsning från databas med vattenstämpel
I det här fallet definierar du en vattenstämpel i din källdatabas. En vattenstämpel är en kolumn som har den senast uppdaterade tidsstämpeln eller en stegvis ökande nyckel. Lösningen för deltainläsning läser in de ändrade data mellan en gammal och en ny vattenstämpel. Arbetsflödet för den här metoden illustreras i följande diagram: 

![Arbetsflöde för att använda en vattenstämpel](media/tutorial-incremental-copy-overview/workflow-using-watermark.png)

Stegvisa instruktioner finns i följande självstudier: 
- [Kopiera data stegvis från en tabell i Azure SQL Database till Azure Blob Storage](tutorial-incremental-copy-powershell.md)
- [Kopiera data stegvist från flera tabeller i en lokal SQL Server till Azure SQL Database](tutorial-incremental-copy-multiple-tables-powershell.md)

För mallar finns i följande:
- [Deltakopiera med kontrolltabellen](solution-template-delta-copy-with-control-table.md)

## <a name="delta-data-loading-from-sql-db-by-using-the-change-tracking-technology"></a>Inläsning av deltadata från SQL DB med tekniken Ändringsspårning
Tekniken för ändringsspårning är en enkel lösning i SQL Server och Azure SQL Database som tillhandahåller en effektiv ändringsspårningsmekanism för program. Det gör att ett program enkelt kan identifiera data som har infogats, uppdaterats eller tagits bort. 

Arbetsflödet för den här metoden illustreras i följande diagram:

![Arbetsflöde för att använda Ändringsspårning](media/tutorial-incremental-copy-overview/workflow-using-change-tracking.png)

Stegvisa instruktioner finns i följande självstudie: <br/>
- [Kopiera data stegvis från Azure SQL Database till Azure Blob Storage med ändringsspårningsteknik](tutorial-incremental-copy-change-tracking-feature-powershell.md)

## <a name="loading-new-and-changed-files-only-by-using-lastmodifieddate"></a>Läsa in endast nya och ändrade filer med hjälp av LastModifiedDate
Du kan bara kopiera de nya och ändrade filerna genom att använda LastModifiedDate i målarkivet. ADF kommer att skanna alla filer från källarkivet, tillämpa filfiltret med deras LastModifiedDate och bara kopiera den nya och uppdaterade filen sedan förra gången till målarkivet.  Tänk på om du låter ADF skanna enorma mängder filer men bara kopiera några filer till destinationen, skulle du fortfarande förvänta dig den långa varaktigheten på grund av filskanning är tidskrävande också.   

Stegvisa instruktioner finns i följande självstudie: <br/>
- [Kopiera stegvis nya och ändrade filer baserat på LastModifiedDate från Azure Blob Storage till Azure Blob Storage](tutorial-incremental-copy-lastmodified-copy-data-tool.md)

För mallar finns i följande:
- [Kopiera nya filer genom LastModifiedDate](solution-template-copy-new-files-lastmodifieddate.md)

## <a name="loading-new-files-only-by-using-time-partitioned-folder-or-file-name"></a>Läsa in endast nya filer med hjälp av tidspartitionerat mapp- eller filnamn.
Du kan kopiera endast nya filer, där filer eller mappar redan har tidspartitionerats med tidssektorinformation som en del av fil- eller mappnamnet (till exempel /åååå/mm/dd/fil.csv). Det är den mest högpresterande metoden för att stegvis läsa in nya filer. 

Stegvisa instruktioner finns i följande självstudie: <br/>
- [Kopiera stegvis nya filer baserat på tidspartitionerat mapp- eller filnamn från Azure Blob Storage till Azure Blob Storage](tutorial-incremental-copy-partitioned-file-name-copy-data-tool.md)

## <a name="next-steps"></a>Nästa steg
Fortsätt till följande självstudie: 

> [!div class="nextstepaction"]
>[Kopiera data stegvis från en tabell i Azure SQL Database till Azure Blob Storage](tutorial-incremental-copy-powershell.md)
