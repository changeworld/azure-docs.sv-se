---
title: Översikt över Azure Compute Unit | Microsoft-dokument
description: Översikt över konceptet för Azure-beräkningsenheter. ACU är ett sätt att jämföra CPU-prestanda över Azure SKU:er.
services: virtual-machines
documentationcenter: ''
author: mimckitt
manager: vashan
editor: ''
tags: azure-resource-manager,azure-service-management
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: mimckitt
ms.openlocfilehash: e344d09497a30dec546dfaedd3d78f30c7d214d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79535705"
---
# <a name="azure-compute-unit-acu"></a>Azure-beräkningsenhet (ACU)

Konceptet för Azure Compute Unit (ACU) är ett sätt att jämföra processorprestanda (Compute) över Azure SKU:er. På så sätt blir det lättare att identifiera vilken SKU som bäst uppfyller dina behov. ACU är för närvarande standardiserat på en liten virtuell dator (Standard_A1) och är 100, och alla andra SKU:er representerar ungefär hur mycket snabbare den SKU:n kan köra ett benchmark-standardtest.

> [!IMPORTANT]
> ACU är endast en riktlinje. Resultatet med din arbetsbelastning kan variera.
<br>

| SKU-familj | ACU\vCPU | vCPU: Kärna ur |
| --- | --- |---|
| [A0](sizes-previous-gen.md) |50 | 1:1 |
| [A1 - A4](sizes-previous-gen.md) |100 | 1:1 |
| [A5 - A7](sizes-previous-gen.md) |100 | 1:1 |
| [A1_v2 - A8_v2](sizes-general.md) |100 | 1:1 |
| [A2m_v2 - A8m_v2](sizes-general.md) |100 | 1:1 |
| [A8 - A11](sizes-previous-gen.md) |225* | 1:1 |
| [D1 - D14](sizes-previous-gen.md) |160 - 250 | 1:1 |
| [D1_v2 - D15_v2](dv2-dsv2-series.md) |210 - 250* | 1:1 |
| [DS1 - DS14](sizes-previous-gen.md) |160 - 250 | 1:1 |
| [DS1_v2 - DS15_v2](dv2-dsv2-series.md) |210 - 250* | 1:1 |
| [D_v3](dv3-dsv3-series.md) |160 - 190* | 2:1\*\*\* |
| [Ds_v3](dv3-dsv3-series.md) |160 - 190* | 2:1\*\*\* |
| [E_v3](ev3-esv3-series.md) |160 - 190* | 2:1\*\*\*|
| [Es_v3](ev3-esv3-series.md) |160 - 190* | 2:1\*\*\* |
| [F2s_v2 - F72s_v2](fsv2-series.md) |195 - 210* | 2:1\*\*\* |
| [F1 - F16](sizes-previous-gen.md) |210 - 250* | 1:1 |
| [F1s - F16s](sizes-previous-gen.md) |210 - 250* | 1:1 |
| [G1 - G5](sizes-previous-gen.md) |180 - 240* | 1:1 |
| [GS1 - GS5](sizes-previous-gen.md) |180 - 240* | 1:1 |
| [H](h-series.md) |290 - 300* | 1:1 |
| [HB](hb-series.md) |199 - 216** | 1:1 |
| [Hc](hc-series.md) |297 - 315* | 1:1 |
| [L4s - L32s](sizes-previous-gen.md) |180 - 240* | 1:1 |
| [L8s_v2 - L80s_v2](lsv2-series.md) |150 - 175** | 2:1 |
| [M](m-series.md) | 160 - 180 | 2:1\*\*\* |

* Nätaggregat använder Intel® Turbo-teknik för att öka CPU-frekvens och ge en prestandaökning.  Mängden prestandaökning kan variera beroende på den virtuella datorns storlek, arbetsbelastning och andra arbetsbelastningar som körs på samma värd.
** ACUs använder AMD® Boost-teknik för att öka CPU-frekvensen och ge en prestandaökning.  Mängden prestandaökning kan variera beroende på den virtuella datorns storlek, arbetsbelastning och andra arbetsbelastningar som körs på samma värd.
Hyper-threaded och kan köra kapslad virtualisering

Här är länkar till mer information om de olika storlekarna:

- [General-purpose](sizes-general.md)
- [Minnesoptimerad](sizes-memory.md)
- [Beräkningsoptimerad](sizes-compute.md)
- [GPU-optimerad](sizes-gpu.md)
- [Databehandling med höga prestanda](sizes-hpc.md)
- [Lagringsoptimerad](sizes-storage.md)
