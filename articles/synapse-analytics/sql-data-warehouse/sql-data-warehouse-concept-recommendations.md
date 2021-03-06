---
title: Synapse SQL-rekommendationer
description: Läs mer om Synapse SQL-rekommendationer och hur de genereras
services: synapse-analytics
author: kevinvngo
manager: craigg-msft
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/05/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 17877a1ef5d949fbbee080b6157844ac5b516fe7
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633690"
---
# <a name="synapse-sql-recommendations"></a>Synapse SQL-rekommendationer

I den här artikeln beskrivs De Synapse SQL-rekommendationer som visas via Azure Advisor.  

SQL Analytics ger rekommendationer för att säkerställa att din datalagerarbetsbelastning är konsekvent optimerad för prestanda. Rekommendationer är tätt integrerade med [Azure Advisor](../../advisor/advisor-performance-recommendations.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) för att ge dig bästa praxis direkt i [Azure-portalen](https://aka.ms/Azureadvisor). SQL Analytics samlar in telemetri och ytrekommendationer för din aktiva arbetsbelastning på en daglig kadens. De rekommendationsscenarier som stöds beskrivs nedan tillsammans med hur du tillämpar rekommenderade åtgärder.

Du kan [kontrollera dina rekommendationer](https://aka.ms/Azureadvisor) idag! Den här funktionen gäller för närvarande endast för Gen2-informationslager.

## <a name="data-skew"></a>Data skeva

Datasnedställning kan orsaka ytterligare dataförflyttningar eller resursflaskhalsar när du kör din arbetsbelastning. Följande dokumentation beskriver visar att identifiera data skeva och förhindra att det händer genom att välja en optimal distributionsnyckel.

- [Identifiera och ta bort skevning](sql-data-warehouse-tables-distribute.md#how-to-tell-if-your-distribution-column-is-a-good-choice)

## <a name="no-or-outdated-statistics"></a>Ingen eller föråldrad statistik

Med suboptimal statistik kan allvarligt påverka frågeprestanda eftersom det kan orsaka SQL-frågeoptimeraren att generera suboptimala frågeplaner. I följande dokumentation beskrivs de bästa metoderna för att skapa och uppdatera statistik:

- [Skapa och uppdatera tabellstatistik](sql-data-warehouse-tables-statistics.md)

Om du vill se listan över påverkade tabeller med dessa rekommendationer kör du följande [T-SQL-skript](https://github.com/Microsoft/sql-data-warehouse-samples/blob/master/samples/sqlops/MonitoringScripts/ImpactedTables). Advisor kör kontinuerligt samma T-SQL-skript för att generera dessa rekommendationer.

## <a name="replicate-tables"></a>Replikera tabeller

För replikerade tabellrekommendationer identifierar Advisor tabellkandidater baserat på följande fysiska egenskaper:

- Replikerad tabellstorlek
- Antal kolumner
- Typ av tabellfördelning
- Antal partitioner

Advisor utnyttjar kontinuerligt arbetsbelastningsbaserad heuristik, till exempel tabellåtkomstfrekvens, rader som returneras i genomsnitt och tröskelvärden runt datalagerstorlek och aktivitet för att säkerställa att högkvalitativa rekommendationer genereras.

Följande beskriver arbetsbelastningsbaserad heuristik som du kan hitta i Azure-portalen för varje replikerad tabellrekommendation:

- Scan avg- den genomsnittliga procenten av rader som returnerades från tabellen för varje tabellåtkomst under de senaste sju dagarna
- Frekvent läsning, ingen uppdatering - anger att tabellen inte har uppdaterats under de senaste sju dagarna samtidigt som åtkomstaktiviteten visas
- Läs/uppdatera-förhållande - förhållandet mellan hur ofta tabellen användes i förhållande till när den uppdateras under de senaste sju dagarna
- Aktivitet - mäter användningen baserat på åtkomstaktivitet. Detta jämför tabellåtkomstaktiviteten i förhållande till den genomsnittliga tabellåtkomstaktiviteten i informationslagret under de senaste sju dagarna.

För närvarande visas Advisor endast högst fyra replikerade tabellkandidater samtidigt med klustrade columnstore-index som prioriterar den högsta aktiviteten.

> [!IMPORTANT]
> Rekommendationen för replikerad tabell är inte fullständigt korrektur och tar inte hänsyn till dataflyttningar. Vi arbetar på att lägga till detta som en heuristisk men under tiden bör du alltid validera din arbetsbelastning efter att ha tillämpat rekommendationen. Kontakta sqldwadvisor@service.microsoft.com om du upptäcker replikerade tabellrekommendationer som gör att arbetsbelastningen går tillbaka. Mer information om replikerade tabeller finns i följande [dokumentation](design-guidance-for-replicated-tables.md#what-is-a-replicated-table).
