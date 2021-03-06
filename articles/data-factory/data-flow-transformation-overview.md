---
title: Översikt över dataflödesomvandling
description: En översikt över de olika omvandlingar som finns tillgängliga i mappning av dataflöde
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 94f7277c1b741fb5ef6d2807fabbb266c0109415
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79086922"
---
# <a name="mapping-data-flow-transformation-overview"></a>Översikt över dataflödesomvandling

Nedan finns en lista över de omvandlingar som för närvarande stöds vid mappning av dataflöde. Klicka på varje omvandlingar för att lära dig dess konfigurationsinformation.

| Namn | Kategori | Beskrivning |
| ---- | -------- | ----------- |
| [Sammanlagda](data-flow-aggregate.md) | Schemamodifierare | Definiera olika typer av aggregeringar som SUMMA, MIN, MAX och ANTAL grupperade efter befintliga eller beräknade kolumner. | 
| [Ändra rad](data-flow-alter-row.md) | Radeifierare för rad | Ange principer för infoga, ta bort, uppdatera och upsert på rader. |
| [Villkorsstyrd delning](data-flow-conditional-split.md) | Flera ingångar/utdata | Dirigera rader med data till olika strömmar baserat på matchande villkor. |
| [Härledd kolumn](data-flow-derived-column.md) | Schemamodifierare | generera nya kolumner eller ändra befintliga fält med hjälp av dataflödesuttrycksspråket. | 
| [Finns](data-flow-exists.md) | Flera ingångar/utdata | Kontrollera om dina data finns i en annan källa eller ström. | 
| [Filter](data-flow-filter.md) | Radeifierare för rad | Filtrera en rad baserat på ett villkor. |
| [Platta](data-flow-flatten.md) | Schemamodifierare |  Ta matrisvärden inuti hierarkiska strukturer som JSON och rulla ut dem i enskilda rader. |
| [Anslut](data-flow-join.md) | Flera ingångar/utdata |  Kombinera data från två källor eller strömmar. |
| [Sökning](data-flow-lookup.md) | Flera ingångar/utdata | Referensdata från en annan källa. |
| [Ny gren](data-flow-new-branch.md) | Flera ingångar/utdata | Använd flera uppsättningar åtgärder och omvandlingar mot samma dataström. |
| [Pivotera](data-flow-pivot.md) | Schemamodifierare | En aggregering där en eller flera grupperingskolumner har sina distinkta radvärden omdömda till enskilda kolumner. |
| [Välj](data-flow-select.md) | Schemamodifierare | Aliaskolumner och flödesnamn samt släpp eller ändra ordning på kolumner |
| [Kanalmottagare](data-flow-sink.md) | - | En slutdestination för dina data |
| [Sortera](data-flow-sort.md) | Radeifierare för rad | Sortera inkommande rader i den aktuella dataströmmen |
| [Källkod](data-flow-source.md) | - | En datakälla för dataflödet |
| [Surrogatnyckel](data-flow-surrogate-key.md) | Schemamodifierare | Lägga till ett stegvis godtyckligt nyckelvärde som inte är affärsmässigt |
| [Union](data-flow-union.md) | Flera ingångar/utdata | Kombinera flera dataströmmar lodrätt |
| [Normalisera](data-flow-unpivot.md) | Schemamodifierare | Pivotera kolumner till radvärden |
| [Fönster](data-flow-window.md) | Schemamodifierare |  Definiera fönsterbaserade aggregeringar av kolumner i dina dataströmmar. |
