---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/27/2019
ms.author: glenga
ms.openlocfilehash: d697334fe56fb9133a06cee79067c60bc3a37281
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "68639117"
---
Det enklaste sättet att installera bindningstillägg är att aktivera [tilläggspaket](../articles/azure-functions/functions-bindings-register.md#extension-bundles). När du aktiverar paket installeras automatiskt en fördefinierad uppsättning tilläggspaket.

Om du vill aktivera tilläggspaket öppnar du filen host.json och uppdaterar innehållet så att det matchar följande kod:

```json
{
    "version": "2.0",
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[1.*, 2.0.0)"
    }
}
```