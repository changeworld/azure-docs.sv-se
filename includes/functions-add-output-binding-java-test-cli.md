---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 6a359cdd44cc0c0cfbd93bd23b69a67a641c7fbb
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673291"
---
## <a name="update-the-tests"></a>Uppdatera testerna

Eftersom arketypen också skapar en uppsättning tester måste du uppdatera `msg` dessa tester `run` för att hantera den nya parametern i metodsignaturen.  

Bläddra till platsen för testkoden under _src/test/java_, öppna *function.java-projektfilen* `//Invoke` och ersätt kodraden under med följande kod.

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/test/java/com/function/FunctionTest.java" range="48-50":::