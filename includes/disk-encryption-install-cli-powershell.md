---
title: ta med fil
description: ta med fil
services: virtual-machines
author: msmbaldwin
ms.service: virtual-machines
ms.topic: include
ms.date: 10/06/2019
ms.author: mbaldwin
ms.custom: include file
ms.openlocfilehash: 05794a046fdcb15a91145a75717a6a454d15a8da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "72511527"
---
Azure Disk Encryption kan aktiveras och hanteras via [Azure CLI](/cli/azure) och [Azure PowerShell](/powershell/azure/new-azureps-module-az). För att göra det måste du installera verktygen lokalt och ansluta till din Azure-prenumeration.

### <a name="azure-cli"></a>Azure CLI

[Azure CLI 2.0](/cli/azure) är ett kommandoradsverktyg för hantering av Azure-resurser. CLI är utformad för att flexibelt fråga data, stödja tidskrävande åtgärder som icke-blockerande processer och göra skripting enkelt. Du kan installera den lokalt genom att följa stegen i [Installera Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).

Om du vill [logga in på ditt Azure-konto med Azure CLI](/cli/azure/authenticate-azure-cli)använder du kommandot az [login.](/cli/azure/reference-index?view=azure-cli-latest#az-login)

```azurecli
az login
```

Om du vill välja en klient att logga in under använder du:
    
```azurecli
az login --tenant <tenant>
```

Om du har flera prenumerationer och vill ange en specifik prenumerationslista med [az-kontolista](/cli/azure/account#az-account-list) och ange med [az-kontouppsättning](/cli/azure/account#az-account-set).
     
```azurecli
az account list
az account set --subscription "<subscription name or ID>"
```

Mer information finns i [Komma igång med Azure CLI 2.0](/cli/azure/get-started-with-azure-cli). 

### <a name="azure-powershell"></a>Azure PowerShell
[Azure PowerShell az-modulen](/powershell/azure/new-azureps-module-az) innehåller en uppsättning cmdlets som använder [Azure Resource Manager-modellen](/azure/azure-resource-manager/resource-group-overview) för att hantera dina Azure-resurser. Du kan använda den i din webbläsare med [Azure Cloud Shell](/azure/cloud-shell/overview)eller installera den på din lokala dator med hjälp av instruktionerna i Installera Azure [PowerShell-modulen](/powershell/azure/install-az-ps). 

Om du redan har den installerad lokalt kontrollerar du att du använder den senaste versionen av Azure PowerShell SDK-versionen för att konfigurera Azure Disk Encryption. Hämta den senaste versionen av [Azure PowerShell-versionen](https://github.com/Azure/azure-powershell/releases).

Om du vill [logga in på ditt Azure-konto med Azure PowerShell](/powershell/azure/authenticate-azureps?view=azps-2.5.0)använder du cmdleten [Connect-AzAccount.](/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0)

```powershell
Connect-AzAccount
```

Om du har flera prenumerationer och vill ange en använder du cmdleten [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription) för att lista dem, följt av cmdleten [Set-AzContext:](/powershell/module/az.accounts/set-azcontext?view=azps-2.5.0)

```powershell
Set-AzContext -Subscription -Subscription <SubscriptionId>
```

Om du kör cmdleten [Get-AzContext](/powershell/module/Az.Accounts/Get-AzContext) verifieras att rätt prenumeration har valts.

Om du vill bekräfta att azure diskkrypterings-cmdlets är installerade använder du cmdleten [Get-command:](/powershell/module/microsoft.powershell.core/get-command?view=powershell-6)
     
```powershell
Get-command *diskencryption*
```
Mer information finns i [Komma igång med Azure PowerShell](/powershell/azure/get-started-azureps). 