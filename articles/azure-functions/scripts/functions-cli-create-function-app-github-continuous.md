---
title: Skapa en funktionsapp med GitHub-distribution - Azure CLI
description: Skapa en funktionsapp och distribuera funktionskoden från en GitHub-lagringsplats med hjälp av Azure Functions.
ms.date: 07/03/2018
ms.topic: sample
ms.custom: mvc
ms.openlocfilehash: e424737b67be58ac0c3880ad2454c87fab803a15
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "75922724"
---
# <a name="create-a-function-app-in-azure-that-is-deployed-from-github"></a>Skapa en funktionsapp i Azure som distribueras från GitHub

Det här exempelskriptet för Azure Functions skapar en funktionsapp med hjälp av [förbrukningsplanen](../functions-scale.md#consumption-plan)tillsammans med dess relaterade resurser. Skriptet konfigurerar också funktionskoden för kontinuerlig distribution från en GitHub-lagringsplats. 

För det här exemplet behöver du följande:

* En GitHub-lagringsplats med funktionskod som du har administratörsbehörighet för.
* En [personlig åtkomsttoken (PAT)](https://help.github.com/articles/creating-an-access-token-for-command-line-use) för ditt GitHub-konto.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du föredrar att använda Azure CLI lokalt måste du installera och använda version 2.0 eller senare. Avgör vilken version av Azure CLI du har genom att köra `az --version`. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Exempelskript

Det här exemplet skapar en funktionsapp och distribuerar funktionskod från GitHub.

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/deploy-function-app-with-function-github-continuous/deploy-function-app-with-function-github-continuous.sh?highlight=3-4 "Azure Service")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Förklaring av skript

Varje kommando i tabellen länkar till kommandospecifik dokumentation. I det här skriptet används följande kommandon:

| Kommando | Anteckningar |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Skapar en resursgrupp där alla resurser lagras. |
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | Skapar lagringskontot för funktionsappen. |
| [az functionapp create](/cli/azure/functionapp#az-functionapp-create) | Skapar en funktionsapp i den serverlösa [förbrukningsplanen](../functions-scale.md#consumption-plan) och associerar den med en Git- eller Mercurial-databas. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](/cli/azure).

Ytterligare CLI-skriptexempel för Azure Functions finns i [Azure Functions-dokumentationen](../functions-cli-samples.md).
