---
title: Skapa och konfigurera ett nyckelvalv för Azure Disk Encryption
description: Den här artikeln innehåller steg för att skapa och konfigurera ett nyckelvalv för användning med Azure Disk Encryption
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
author: msmbaldwin
ms.author: mbaldwin
ms.date: 10/10/2019
ms.custom: seodec18
ms.openlocfilehash: eec5b42da709ab5e79da42f11600f6ffc81d247e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76279001"
---
# <a name="creating-and-configuring-a-key-vault-for-azure-disk-encryption"></a>Skapa och konfigurera ett nyckelvalv för Azure Disk Encryption

Azure Disk Encryption använder Azure Key Vault för att styra och hantera diskkrypteringsnycklar och hemligheter.  Mer information om nyckelvalv finns i [Komma igång med Azure Key Vault](../key-vault/key-vault-get-started.md) och Säkra ditt [nyckelvalv](../key-vault/key-vault-secure-your-key-vault.md).

Att skapa och konfigurera ett nyckelvalv för användning med Azure Disk Encryption innebär tre steg:

1. Skapa en resursgrupp om det behövs.
2. Skapar ett nyckelvalv. 
3. Ställa in avancerade åtkomstprinciper för nyckelvalv.

De här stegen illustreras i följande snabbstarter:

Du kan också, om du vill, generera eller importera en nyckelkrypteringsnyckel (KEK).

## <a name="install-tools-and-connect-to-azure"></a>Installera verktyg och ansluta till Azure

Stegen i den här artikeln kan slutföras med [Azure CLI](/cli/azure/), [Azure PowerShell Az-modulen](/powershell/azure/overview)eller [Azure-portalen](https://portal.azure.com).

### <a name="connect-to-your-azure-account"></a>Anslut till ditt Azure-konto

Innan du använder Azure CLI eller Azure PowerShell måste du först ansluta till din Azure-prenumeration. Du gör det genom [att logga in med Azure CLI](/cli/azure/authenticate-azure-cli?view=azure-cli-latest), Logga in med Azure [Powershell](/powershell/azure/authenticate-azureps?view=azps-2.5.0)eller ange dina autentiseringsuppgifter till Azure-portalen när du uppmanas.

```azurecli-interactive
az login
```

```azurepowershell-interactive
Connect-AzAccount
```

[!INCLUDE [disk-encryption-key-vault](../../includes/disk-encryption-key-vault.md)]
 
## <a name="next-steps"></a>Nästa steg

- [Översikt över Azure Disk-kryptering](disk-encryption-overview.md)
- [Kryptera en skala för virtuella datorer med Azure CLI](disk-encryption-cli.md)
- [Kryptera en skala för virtuella datorer med Azure PowerShell](disk-encryption-powershell.md)
