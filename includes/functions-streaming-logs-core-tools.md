---
author: ggailey777
ms.author: glenga
ms.date: 7/24/2019
ms.topic: include
ms.service: azure-functions
ms.openlocfilehash: 1928a8238cd73087e3c199675574dd1395f4d76d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "68881337"
---
#### <a name="built-in-log-streaming"></a>Inbyggd loggströmning

Använd `logstream` alternativet för att börja ta emot direktuppspelningsloggar för en specifik funktionsapp som körs i Azure, som i följande exempel:

```bash
func azure functionapp logstream <FunctionAppName>
```

#### <a name="live-metrics-stream"></a>Live-ström med mätvärden

Du kan också visa Live Metrics Stream för din [funktionsapp](../articles/azure-monitor/app/live-stream.md) i ett nytt webbläsarfönster genom att inkludera `--browser` alternativet, som i följande exempel:

```bash
func azure functionapp logstream <FunctionAppName> --browser
```
