---
title: Prestandarekommendationer - Azure Database för MariaDB
description: I den här artikeln beskrivs funktionen Prestandarekommendation i Azure Database för MariaDB
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 838a4123bd5007f987f27674862409445967a2d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79528106"
---
# <a name="performance-recommendations-in-azure-database-for-mariadb"></a>Prestandarekommendationer i Azure Database for MariaDB

**Gäller:** Azure-databas för MariaDB 10.2

Funktionen Prestandarekommendationer analyserar dina databaser för att skapa anpassade förslag på bättre prestanda. För att ta fram rekommendationerna tittar analysen på olika databasegenskaper, inklusive schema. Aktivera [Query Store](concepts-query-store.md) på servern för att fullt ut utnyttja funktionen Prestandarekommendationer. Om prestandaschemat är AV aktiverar du Query Store performance_schema och en delmängd av instrument för prestandaschema som krävs för funktionen. När du har implementerat en prestandarekommendation bör du testa prestanda för att utvärdera effekten av dessa ändringar.

## <a name="permissions"></a>Behörigheter

Behörighet som **ägare** eller **deltagare** krävs för att köra analys med funktionen Prestandarekommendationer.

## <a name="performance-recommendations"></a>Prestandarekommendationer

Funktionen [Prestandarekommendationer](concepts-performance-recommendations.md) analyserar arbetsbelastningar på din server för att identifiera index med potential för att förbättra prestandan.

Öppna **prestandarekommendationer** från avsnittet **Intelligent prestanda** i menyraden på Azure-portalsidan för din MariaDB-server.

![Landningssida för prestandarekommendationer](./media/concepts-performance-recommendations/performance-recommendations-page.png)

Välj **Analysera** och välj en databas som ska påbörja analysen. Beroende på din arbetsbelastning kan det ta flera minuter att slutföra analysen. När analysen är klar, visas ett meddelande i portalen. Analys utför en djup undersökning av din databas. Vi rekommenderar att du utför analyser under lågtrafikperioder.

Fönstret **Rekommendationer** visar en lista med eventuella rekommendationer och det relaterade fråge-ID som genererade rekommendationen. Med fråge-ID:t kan du använda [vyn mysql.query_store](concepts-query-store.md#mysqlquery_store) för att lära dig mer om frågan.

![Nytt sidsida för prestandarekommendationer](./media/concepts-performance-recommendations/performance-recommendations-result.png)

Rekommendationer tillämpas inte automatiskt. Om du vill använda rekommendationen kopierar du frågetexten och kör den från valfri klient. Kom ihåg att testa och övervaka för att utvärdera rekommendationen.

## <a name="recommendation-types"></a>Typ av rekommendation

För närvarande stöds endast *Skapa indexrekommendationer.*

### <a name="create-index-recommendations"></a>Skapa indexrekommendationer

*Skapa indexrekommendationer* föreslår nya index för att påskynda de vanligaste körnings- eller tidskrävande frågorna i arbetsbelastningen. Den här rekommendationstypen kräver att [Query Store](concepts-query-store.md) aktiveras. Query Store samlar in frågeinformation och tillhandahåller detaljerad frågekörnings- och frekvensstatistik som analysen använder för att göra rekommendationen.

## <a name="next-steps"></a>Nästa steg

- Läs mer om [övervakning och justering](concepts-monitoring.md) i Azure Database för MariaDB.