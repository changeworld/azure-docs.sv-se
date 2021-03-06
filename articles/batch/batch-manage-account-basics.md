---
title: Hantera ditt konto- Azure Batch | Microsoft-dokument
description: Läs om vad som består av ett Azure Batch-konto
services: batch
documentationcenter: ''
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: 3fbae545-245f-4c66-aee2-e25d7d5d36db
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: labrenne
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ecae47f6aa0ab3f179632467b7da7805f06162d6
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2020
ms.locfileid: "80397271"
---
# <a name="manage-your-batch-account"></a>Hantera ditt batchkonto

Ett Batch-konto är en unikt identifierad entitet i Batch-tjänsten. All bearbetning är associerad med ett Batch-konto.

Du kan skapa ett Azure Batch-konto med [Azure Portal](batch-account-create-portal.md) eller programmässigt som med [Batch Management .NET-biblioteket](batch-management-dotnet.md). När du skapar kontot kan du koppla ett Azure-lagringskonto för att lagra jobbrelaterade in- och utdata eller program.

Du kan köra flera Batch-arbetsbelastningar i samma Batch-konto eller distribuera dina arbetsbelastningar mellan Batch-konton som är i samma prenumeration, men i olika Azure-regioner.

## <a name="components-of-the-batch-account"></a>Komponenter i batchkontot

Med batch-kontot kan du köra storskaliga globala och högpresterande datorjobb (HPC) effektivt i Azure. Inom kontot du hanterar:

- De program du kör

- Allokering av pooler och noder i pooler

- Antal och typer av uppgifter 

- Indata och utdata. Du behöver inte installera ytterligare programvara för att hantera uppgifter.

- När du skapar batchkontot uppmanas du att tilldela det ett namn. Det här namnet är dess ID och när det har tilldelats kan det inte ändras.

- Om du vill ändra namnet på ett konto måste du ta bort det och skapa ett nytt batchkonto.

- Kontot skapas i den prenumeration du vill använda.

- Använd kontot för att identifiera och hämta primära och sekundära kontonycklar från alla Batch-konton i din prenumeration.

- Kontot upprätthåller information om poolallokering och kärnkvoter.  

- Kontot innehåller platsinformation.

- Kontot identifierar ditt lagringskonto.

## <a name="next-steps"></a>Nästa steg

- Skapa ett batchkonto med [Azure-portalen](batch-account-create-portal.md).
- Skapa ett batchkonto programmässigt, till exempel med [batchhantering .NET-biblioteket](batch-management-dotnet.md).
- [Konfigurera eller inaktivera fjärråtkomst för beräkningsnoder i en Azure Batch-pool](pool-endpoint-configuration.md).
- [Kör jobbförberedelse- och jobbfrisättningsuppgifter på batchberäkningsnoder](batch-job-prep-release.md)

