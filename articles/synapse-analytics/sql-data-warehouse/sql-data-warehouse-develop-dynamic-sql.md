---
title: Använda dynamisk SQL
description: Tips för utvecklingslösningar med dynamisk SQL i Synapse SQL-pool.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: a9280bb8153204f86096cf8249ff053bee3f71cc
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633527"
---
# <a name="dynamic-sql-in-synapse-sql-pool"></a>Dynamisk SQL i Synapse SQL-pool

I den här artikeln finns tips för utvecklingslösningar med dynamisk SQL i SQL-pool.

## <a name="dynamic-sql-example"></a>Dynamiskt SQL-exempel

När du utvecklar programkod för SQL-pool kan du behöva använda dynamisk SQL för att leverera flexibla, generiska och modulära lösningar. SQL-poolen stöder inte blob-datatyper just nu.

Att inte stödja blob-datatyper kan begränsa storleken på dina strängar eftersom blob-datatyper innehåller både varchar(max) och nvarchar(max) typer.

Om du har använt dessa typer i programkoden för att skapa stora strängar måste du dela upp koden i segment och använda EXEC-satsen i stället.

Ett enkelt exempel:

```sql
DECLARE @sql_fragment1 VARCHAR(8000)=' SELECT name '
,       @sql_fragment2 VARCHAR(8000)=' FROM sys.system_views '
,       @sql_fragment3 VARCHAR(8000)=' WHERE name like ''%table%''';

EXEC( @sql_fragment1 + @sql_fragment2 + @sql_fragment3);
```

Om strängen är kort kan du använda [sp_executesql](/sql/relational-databases/system-stored-procedures/sp-executesql-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) som vanligt.

> [!NOTE]
> Satser som körs som dynamisk SQL kommer fortfarande att omfattas av alla T-SQL-valideringsregler.

## <a name="next-steps"></a>Nästa steg

Fler utvecklingstips finns i [utvecklingsöversikt](sql-data-warehouse-overview-develop.md).
