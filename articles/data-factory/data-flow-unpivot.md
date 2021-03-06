---
title: Mappa dataflöde Unpivot Transformation
description: Azure Data Factory mappning dataflöde Unpivot Transformation
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/30/2019
ms.openlocfilehash: b207012335e68d389a07b54408e840dbb305a30c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74930138"
---
# <a name="azure-data-factory-unpivot-transformation"></a>Azure Data Factory Unpivot Transformation



Använd Unpivot i ADF-mappningsdataflödet som ett sätt att omvandla en onormaliserad datauppsättning till en mer normaliserad version genom att expandera värden från flera kolumner i en enda post till flera poster med samma värden i en enda kolumn.

![Omvandling av obalans](media/data-flow/unpivot1.png "Alternativ för Unpivot 1")

## <a name="ungroup-by"></a>Dela upp gruppera efter

![Omvandling av obalans](media/data-flow/unpivot5.png "Alternativ för Unpivot 2")

Ange först de kolumner som du vill gruppera efter för pivotaggregering. Ange en eller flera kolumner för att dela upp grupp med +-tecknet bredvid kolumnlistan.

## <a name="unpivot-key"></a>Ta upp en lampa

![Omvandling av obalans](media/data-flow/unpivot6.png "Alternativ för Unpivot 3")

Pivottangenten är den kolumn som ADF ska pivotera från rad till kolumn. Som standard pivoterar varje unikt värde i datauppsättningen för det här fältet till en kolumn. Du kan dock ange värdena från den datauppsättning som du vill pivotera till kolumnvärden.

## <a name="unpivoted-columns"></a>Ej tillspädda kolumner

![Omvandling av obalans](media/data-flow//unpivot7.png "Alternativ för Unpivot 4")

Slutligen väljer du den aggregering som du vill använda för de pivoterade värdena och hur du vill att kolumnerna ska visas i den nya utdataprojektionen från omformningen.

(Valfritt) Du kan ange att ett namngivningsmönster med ett prefix, ett mitten- och suffix ska läggas till i varje nytt kolumnnamn från radvärdena.

Pivotera "Försäljning" efter region ger dig till exempel helt enkelt nya kolumnvärden från varje försäljningsvärde. Till exempel: "25", "50", "1000", ... Men om du anger ett prefixvärde på "Försäljning" kommer "Försäljning" att föregås av värdena.

<img src="media/data-flow/unpivot3.png" width="400">

Om du ställer in kolumnarrangemanget till "Normal" grupperas alla pivoterade kolumner med sina aggregerade värden. Om du ställer in kolumnarrangemanget på "Lateral" växlar mellan kolumn och värde.

![Omvandling av obalans](media/data-flow//unpivot7.png "Alternativ för Unpivot 5")

Den slutliga opivoterade dataresultatuppsättningen visar kolumnsummorna som nu inte har skapats i separata radvärden.

## <a name="next-steps"></a>Nästa steg

Använd [pivotomvandlingen](data-flow-pivot.md) för att pivotera rader till kolumner.
