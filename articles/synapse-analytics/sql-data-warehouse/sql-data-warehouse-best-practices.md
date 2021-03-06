---
title: Metodtips för Synapse SQL-pool i Azure Synapse Analytics (tidigare SQL DW)
description: Rekommendationer och metodtips för att utveckla lösningar för SQL-pool i Azure Synapse Analytics (tidigare SQL DW).
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 4462bf0fc2057922340eb01cb8c786dbc63ce290
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80745353"
---
# <a name="best-practices-for-synapse-sql-pool-in-azure-synapse-analytics-formerly-sql-dw"></a>Metodtips för Synapse SQL-pool i Azure Synapse Analytics (tidigare SQL DW)

Den här artikeln är en samling metodtips som hjälper dig att uppnå optimala prestanda från distributionen av [SQL-pooler.](sql-data-warehouse-overview-what-is.md)  Syftet med den här artikeln är att ge dig grundläggande vägledning och lyfta fram viktiga fokusområden.  

## <a name="reduce-cost-with-pause-and-scale"></a>Minska kostnaderna genom att pausa och skala

Läs mer om att minska kostnaderna genom att pausa och skala i [Hantera beräkning](sql-data-warehouse-manage-compute-overview.md).

## <a name="maintain-statistics"></a>Underhålla statistik

SQL-pool kan konfigureras för att automatiskt identifiera och skapa statistik för kolumner.  De frågeplaner som skapats av optimeraren är bara lika bra som den tillgängliga statistiken.  

Vi rekommenderar att du aktiverar AUTO_CREATE_STATISTICS för dina databaser och håller statistiken uppdaterad dagligen eller efter varje inläsning för att säkerställa att statistik över kolumner som används i dina frågor alltid är uppdaterad.

Om det tar för lång tid att uppdatera all statistik kan du försöka vara mer selektiv om vilka kolumner som kräver frekventa statistikuppdateringar. Du kanske till exempel vill uppdatera datumkolumner, där nya värden kan läggas till, varje dag.

> [!TIP]
> Du kommer att få mest nytta av att ha uppdaterad statistik om kolumner inblandade i kopplingar, kolumner som används i WHERE-satsen och kolumner som finns i GROUP BY.

Se även [Hantera tabellstatistik](sql-data-warehouse-tables-statistics.md), [SKAPA STATISTIK](/sql/t-sql/statements/create-statistics-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)och [UPPDATERA STATISTIK](/sql/t-sql/statements/update-statistics-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="use-dmvs-to-monitor-and-optimize-your-queries"></a>Använda DMV:er för att övervaka och optimera frågor

SQL-poolen har flera DMVs som kan användas för att övervaka körning av frågor.  I artikeln Övervaka din arbetsbelastning med hjälp av dmvs-artikel beskrivs steg-för-steg-instruktioner om hur du tittar på information om en körningsfråga.  

För att snabbt hitta frågor i dessa DMV:er kan det vara bra att använda alternativet LABEL med dina frågor.

Se även [Övervaka din arbetsbelastning med hjälp av DMV:er](sql-data-warehouse-manage-monitor.md), [ETIKETT](sql-data-warehouse-develop-label.md), [ALTERNATIV](/sql/t-sql/queries/option-clause-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), [sys.dm_exec_sessions](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), [sys.dm_pdw_sql_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), [sys.dm_pdw_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), [DBCC PDW_SHOWEXECUTIONPLAN](/sql/t-sql/database-console-commands/dbcc-pdw-showexecutionplan-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)och [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="tune-query-performance-with-new-product-enhancements"></a>Justera frågeprestanda med nya produktförbättringar

- [Prestandajustering med materialiserade vyer](performance-tuning-materialized-views.md)
- [Prestandajustering med grupperade kolumnlagringsindex](performance-tuning-ordered-cci.md)
- [Prestandajustering med cachelagring av resultatuppsättningar](performance-tuning-result-set-caching.md)

## <a name="group-insert-statements-into-batches"></a>Gruppera INSERT-satser i batchar

En engångsbelastning till ett litet bord med en INSERT-sats eller till och med en periodisk `INSERT INTO MyLookup VALUES (1, 'Type 1')`omladdning av en look-up kan fungera bra för dina behov med ett uttalande som .  

Men om du behöver läsa in flera tusen eller flera miljoner rader under en dag kanske du märker att singleton-infogningar med INSERT inte hänger med.  I så fall utvecklar du i stället dina processer så att de skriver till en fil och så att en annan process regelbundet körs och läser in filen.

Se även [INSERT](/sql/t-sql/statements/insert-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="use-polybase-to-load-and-export-data-quickly"></a>Använda PolyBase för att snabbt läsa in och exportera data

SQL-pool stöder inläsning och export av data via flera verktyg, inklusive Azure Data Factory, PolyBase och BCP.  För små datamängder där prestanda inte är viktigt räcker alla verktygen för dina behov.  Om du däremot läser in eller exporterar stora mängder data eller om snabba prestanda krävs är PolyBase det bästa valet.  

PolyBase är utformad för att utnyttja MPP-arkitekturen (Massively Parallel Processing) och kommer att läsa in och exportera data magnituder snabbare än något annat verktyg.  PolyBase-inläsningar kan utföras med hjälp av CTAS eller INSERT INTO.  

> [!TIP]
> CTAS minimerar transaktionsloggningen och är det snabbaste sättet att läsa in data.

Azure Data Factory stöder också PolyBase-belastningar och kan uppnå liknande prestanda som CTAS.  PolyBase stöder olika filformat, inklusive Gzip-filer.  
  
> [!NOTE]
> För att maximera dataflödet när du använder gzip-textfiler, dela upp filer i 60 eller fler filer för att maximera parallellismen i din belastning.  För snabbare totalt genomflöde bör du överväga att använda samtidig inläsning av data.

Se även [Läs in data](design-elt-data-loading.md), Guide för användning av [PolyBase,](guidance-for-loading-data.md) [SQL-poolinläsningsmönster och strategier](https://blogs.msdn.microsoft.com/sqlcat/20../../), Läs in data med Azure Data [Factory]( ../../data-factory/load-azure-sql-data-warehouse.md), Flytta data med Azure [Data Factory](../../data-factory/transform-data-using-machine-learning.md), SKAPA [EXTERNT FILFORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)och [Skapa tabell som välj (CTAS)](sql-data-warehouse-develop-ctas.md).

## <a name="load-then-query-external-tables"></a>Läsa in och sedan fråga externa tabeller

Även om Polybase, även kallat externa tabeller, kan vara det snabbaste sättet att läsa in data, är det inte optimalt för frågor. Polybase-tabeller stöder för närvarande endast Azure blob-filer och Azure Data Lake-lagring. Dessa filer har inte några beräkningsresurser som backar upp dem.  

Därför kan SQL-poolen inte avlasta det här arbetet och måste därför läsa hela filen genom att läsa in den till tempdb för att läsa data.  Om du har flera frågor som ska köras mot dessa data är det därför bättre att läsa in dem en gång och låta frågorna använda den lokala tabellen.

Se även [Guide för att använda PolyBase](guidance-for-loading-data.md).

## <a name="hash-distribute-large-tables"></a>Hash-distribuera stora tabeller

Tabeller distribueras som standard med resursallokering (Round Robin).  Detta gör det enkelt för användarna att börja skapa tabeller utan att de behöver bestämma hur tabellerna ska distribueras.  Resursallokeringstabeller kan prestera bra för vissa arbetsbelastningar, men i de flesta fall blir prestanda bättre om du väljer en distributionskolumn.  

Det vanligaste exemplet på när en tabell som distribueras med en kolumn presterar avsevärt mycket bättre än en resursallokeringstabell är när två stora faktatabeller kopplas.  

Om du till exempel har en ordertabell, som distribueras efter order_id, och en transaktionstabell, som också distribueras efter order_id, och du kopplar ordertabellen till transaktionstabellen baserat på order_id, så blir den här frågan en direktfråga, vilket innebär att vi eliminerar dataflyttningsåtgärder.  Färre steg innebär en snabbare fråga.  Mindre dataflyttning gör också att frågor körs snabbare.  

> [!TIP]
> När du läser in en distribuerad tabell ser du till att inkommande data inte sorteras baserat på distributionsnyckeln eftersom det gör att inläsningarna tar längre tid.  

Se följande länkar för mer information om hur val av en distributionskolumn kan förbättra prestanda samt hur du definierar en distribuerad tabell i WITH-satsen i CREATE TABLE-satsen.

Se även [Tabellöversikt,](sql-data-warehouse-tables-overview.md) [Tabellfördelning,](sql-data-warehouse-tables-distribute.md) [Välja tabellfördelning,](https://blogs.msdn.microsoft.com/sqlcat/20../../choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service/) [SKAPA TABELL](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), SKAPA TABELL [SOM SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="do-not-over-partition"></a>Överpartitionera inte

Medan partitionering data kan vara effektivt för att underhålla dina data genom partitionsväxling eller optimera genomsökningar med partition eliminering, med för många partitioner kan sakta ner dina frågor.  Ofta en hög granularitet partitionering strategi, som kan fungera bra på SQL Server kanske inte fungerar bra i SQL pool.  

För många partitioner kan också minska effektiviteten i grupperade columnstore-index om varje partition har färre än 1 miljoner rader.  Tänk på att bakom kulisserna partitionerar SQL-poolen dina data åt dig i 60 databaser, så om du skapar en tabell med 100 partitioner resulterar detta faktiskt i 6000 partitioner.  

Alla arbetsbelastningar är olika så det bästa rådet är att experimentera med partitioneringen för att se vad som fungerar bäst med din arbetsbelastning.  Överväg att använda lägre granularitet än vad som har fungerat i SQL Server.  Överväg exempelvis att använda veckovisa eller månadsvisa partitioneringar i stället för dagliga.

Se även [Tabellpartitionering](sql-data-warehouse-tables-partition.md).

## <a name="minimize-transaction-sizes"></a>Minimera transaktionsstorlekar

INSERT-, UPDATE- och DELETE-instruktioner körs i en transaktion och när de misslyckas måste de återställas.  Du kan minimera risken för en lång återställning genom att minska transaktionsstorlekarna om det går.  Du kan göra det genom att dela upp INSERT-, UPDATE- och DELETE-instruktioner i flera delar.  

Om du till exempel har en INSERT som du förväntar dig att ta 1 timme, om möjligt, dela upp INSERT i fyra delar, som var och en körs i 15 minuter.  Utnyttja särskilda minimala loggningsärenden, till exempel CTAS, TRUNKATE, DROP TABLE eller INSERT till tomma tabeller, för att minska återställningsrisken.  

Ett annat sätt att eliminera återställningar är att använda åtgärder med endast metadata, t.ex. med partitionsväxling, för datahantering.  I stället för att köra en DELETE-sats för att ta bort alla rader i en tabell där order_date var i oktober 2001 kan du partitionera dina data varje månad och sedan växla ut partitionen med data för en tom partition från en annan tabell (se [ALTER TABLE-exempel).](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)  

För opartitionerade tabeller bör du överväga att använda en CTAS för att skriva de data som du vill behålla i en tabell i stället för att använda DELETE.  Om en CTAS tar lika lång tid är det en mycket säkrare åtgärd att köra eftersom den har minimal transaktionsloggning och kan avbrytas snabbt om det behövs.

Se även [Förstå transaktioner](sql-data-warehouse-develop-transactions.md), [Optimera transaktioner,](sql-data-warehouse-develop-best-practices-transactions.md) [Tabellpartitionering,](sql-data-warehouse-tables-partition.md) [TRUNKERA TABELL](/sql/t-sql/statements/truncate-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), ALTER [TABLE](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)och Skapa tabell som [select (CTAS)](sql-data-warehouse-develop-ctas.md).

## <a name="reduce-query-result-sizes"></a>Minska frågeresultatstorlekar

Det här steget hjälper dig att undvika problem på klientsidan som orsakas av stort frågeresultat.  Du kan redigera frågan för att minska antalet returnerade rader. Med vissa frågegenereringsverktyg kan du lägga till syntaxen "topp N" i varje fråga.  Du kan också CETAS frågeresultatet till en temporär tabell och sedan använda PolyBase-export för bearbetning på nednivå.

## <a name="use-the-smallest-possible-column-size"></a>Använda minsta möjliga kolumnstorlek

När du definierar din DDL, med hjälp av den minsta datatyp som stöder dina data kommer att förbättra frågeprestanda.  Detta tillvägagångssätt är särskilt viktigt för CHAR- och VARCHAR-kolumner.  

Om det längsta värdet i en kolumn är 25 tecken definierar du kolumnen som VARCHAR(25).  Undvik att definiera alla teckenkolumner med en stor standardlängd.  Definiera också kolumner som VARCHAR när det är allt som krävs i stället för att använda NVARCHAR.

Se även [Tabellöversikt,](sql-data-warehouse-tables-overview.md) [Tabelldatatyper](sql-data-warehouse-tables-data-types.md), [SKAPA TABELL](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="use-temporary-heap-tables-for-transient-data"></a>Använda tillfälliga heap-tabeller för tillfälliga data

När du tillfälligt landar data kan det hända att det går snabbare att använda en heap-tabell.  Om du bara läser in data för att mellanlagra dem innan du kör fler transformationer går det mycket snabbare om du läser in tabellen till en heap-tabell än om du läser in data till en grupperad columnstore-tabell.  

Om du läser in data till en temporär tabell går inläsningen dessutom mycket fortare än om du läser in en tabell till permanent lagring.  Temporära tabeller börjar med ett "#" och är endast tillgängliga för den session som skapade den, så de kan bara fungera i begränsade scenarier.

Heap-tabeller definieras i WITH-satsen i en CREATE TABLE-instruktion.  Om du använder en temporär tabell måste du också komma ihåg att skapa statistik för den temporära tabellen.

Se även [Temporära tabeller](sql-data-warehouse-tables-temporary.md), [SKAPA TABELL](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), SKAPA TABELL [SOM SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="optimize-clustered-columnstore-tables"></a>Optimera grupperade columnstore-tabeller

Klustrade columnstore-index är ett av de mest effektiva sätten att lagra data i SQL-poolen.  Som standard skapas tabeller i SQL-poolen som Clustered ColumnStore.  Det är viktigt att segmentkvaliteten är bra för att uppnå bästa prestanda med frågor mot columnstore-tabeller.  

När rader skrivs till columnstore-tabeller när minnet är hårt belastat, kan columnstore-segmentens kvalitet påverkas.  Segmentkvaliteten kan mätas utifrån antalet rader i en komprimerad radgrupp.  Se [orsakerna till dålig columnstore-indexkvalitet](sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality) i artikeln [Tabellindex](sql-data-warehouse-tables-index.md) för steg-för-steg-instruktioner om hur du identifierar och förbättrar segmentkvaliteten för klustrade columnstore-tabeller.  

Eftersom högkvalitativa columnstore-segment är viktiga är det en bra idé att använda användar-ID:er som tillhör den medelstora eller stora resursklassen för inläsning av data. Om du använder lägre [informationslagerenheter](what-is-a-data-warehouse-unit-dwu-cdwu.md) innebär du att du vill tilldela en större resursklass till din inläsningsanvändare.

Eftersom columnstore-tabeller i allmänhet inte kommer att skicka data till ett komprimerat columnstore-segment förrän det finns mer än 1 miljon rader per tabell och varje SQL-biljardtabell är uppdelad i 60 tabeller, som en tumregel, kommer columnstore-tabeller inte att gynna en fråga om inte tabellen har mer än 60 miljoner rader.  För tabeller med färre än 60 miljoner rader kanske det inte tjänar något till att ha ett columnstore-index.  Men det skadar inte heller.  

Om du partitionerar data bör du dessutom tänka på att varje partition måste innehålla 1 miljon rader för att kunna dra nytta av ett grupperat columnstore-index.  Om en tabell har 100 partitioner måste den ha minst 6 miljarder rader för att dra nytta av ett klusterkolumnerarklagre (60 distributioner *100 partitioner* 1 miljon rader).  

Om din tabell inte innehåller 6 miljarder rader i det här exemplet minskar du antalet partitioner eller överväger att använda en heap-tabell i stället.  Det kan också vara värt att experimentera och se om du kan förbättra prestanda med en heap-tabell med sekundära index i stället för en columnstore-tabell.

> [!TIP]
> När du kör frågor mot en columnstore-tabell körs frågorna snabbare om du bara väljer de kolumner som du behöver.  

Se även [Table indexes](sql-data-warehouse-tables-index.md) (Tabellindex), [Columnstore indexes guide](/sql/relational-databases/indexes/columnstore-indexes-overview?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) (Guide för columnstore-index), [Rebuilding columnstore indexes](sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality) (Återskapa columnstore-index)

## <a name="use-larger-resource-class-to-improve-query-performance"></a>Använda en större resursklass för att förbättra frågeprestanda

SQL-pool använder resursgrupper som ett sätt att allokera minne till frågor.  Utanför i rutan tilldelas alla användare till den lilla resursklassen, som ger 100 MB minne per distribution.  Eftersom det alltid är 60 distributioner och varje distribution tilldelas minst 100 MB, är den totala minnesallokeringen i systemet 6 000 MB, eller strax under 6 GB.  

Vissa frågor, t.ex. stora kopplingar eller inläsningar till grupperade columnstore-tabeller, kan dra nytta av större minnesallokeringar.  Vissa frågor, som rena skanningar, ger ingen nytta.  Om du använder större resursklasser minskar dock samtidigheten, så du vill ta hänsyn till den här effekten innan du flyttar alla användare till en stor resursklass.

Se även [Resursklasser för arbetsbelastningshantering](resource-classes-for-workload-management.md).

## <a name="use-smaller-resource-class-to-increase-concurrency"></a>Använda den mindre resursklassen för bättre prestanda vid samtidig körning

Om du märker att användarfrågor verkar ha en lång fördröjning kan det bero på att användarna körs i större resursklasser och förbrukar många samtidighetsplatser som orsakar andra frågor att köa.  Du kan se om användarnas frågor köas genom att köra `SELECT * FROM sys.dm_pdw_waits` för att se om några rader returneras.

Se även [Resursklasser för arbetsbelastningshantering](resource-classes-for-workload-management.md), [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="other-resources"></a>Andra resurser

Information om vanliga problem och lösningar finns i vår [felsökningsartikel](sql-data-warehouse-troubleshoot.md).

Om du inte hittar det du letar efter i den här artikeln kan du prova att använda "Sök efter dokument" till vänster på den här sidan för att söka igenom alla Azure Synapse-dokument.  [Azure Synapse Forum](https://social.msdn.microsoft.com/Forums/sqlserver/home?forum=AzureSQLDataWarehouse) är en plats där du kan ställa frågor till andra användare och till Azure Synapse-produktgruppen. Vi övervakar aktivt detta forum för att kontrollera att dina frågor besvaras antingen av en annan användare eller av någon av oss.  

Om du föredrar att ställa dina frågor om Stack Overflow har vi också ett [Azure Synapse Stack Overflow Forum](https://stackoverflow.com/questions/tagged/azure-sqldw).

Använd sidan [Azure Synapse Feedback](https://feedback.azure.com/forums/307516-sql-data-warehouse) för att göra funktionsförfrågningar.  Genom att skicka in dina egna önskemål eller rösta fram andras förfrågningar hjälper du oss att prioritera funktioner.
