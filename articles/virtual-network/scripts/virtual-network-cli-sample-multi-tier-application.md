---
title: Skapa ett VNet för program på flera nivåer - Azure CLI-skriptexempel
description: Azure CLI-skriptexempel – Skapa ett virtuellt nätverk för flernivåprogram.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/20/2018
ms.author: kumud
ms.openlocfilehash: 39a993324a476f0e1f1d17858ae126742e4f69f9
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "74083847"
---
# <a name="create-a-network-for-multi-tier-applications-script-sample"></a>Skapa ett nätverk för flernivåprogram – skriptexempel

Det här skriptexemplet skapar ett virtuellt nätverk med klient- och serverdelsundernät. Trafik till klientdelsundernätet är begränsad till HTTP och SSH, medan trafik till serverdelsundernätet är begränsad till MySQL, port 3306. När du kört skriptet har du två virtuella datorer, en i varje undernät, som du kan distribuera webbservern och MySQL-programvaran till.

Du kan köra skriptet från Azure [Cloud Shell](https://shell.azure.com/bash) eller från en lokal installation av Azure CLI. Om du använder CLI lokalt kräver skriptet att du kör version 2.0.28 eller senare. Kör `az --version` för att hitta den installerade versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI](/cli/azure/install-azure-cli). Om du kör CLI lokalt måste du också köra `az login` för att skapa en anslutning till Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


## <a name="sample-script"></a>Exempelskript


[!code-azurecli-interactive[main](../../../cli_scripts/virtual-network/virtual-network-multi-tier-application/virtual-network-multi-tier-application.sh  "Virtual network for multi-tier application")]

## <a name="clean-up-deployment"></a>Rensa distribution 

Kör följande kommando för att ta bort resursgruppen, den virtuella datorn och alla relaterade resurser:

```azurecli
az group delete --name MyResourceGroup --yes
```

## <a name="script-explanation"></a>Förklaring av skript

I det här skriptet används följande kommandon för att skapa en resursgrupp, ett virtuellt nätverk och nätverkssäkerhetsgrupper. Varje kommando i följande tabell länkar till kommandospecifik dokumentation:

| Kommando | Anteckningar |
|---|---|
| [az group create](/cli/azure/group) | Skapar en resursgrupp där alla resurser lagras. |
| [az network vnet create](/cli/azure/network/vnet) | Skapar ett virtuellt Azure-nätverk och klientdelsundernät. |
| [az network subnet create](/cli/azure/network/vnet/subnet) | Skapar ett serverdelsundernät. |
| [az network public-ip create](/cli/azure/network/public-ip) | Skapar en offentlig IP-adress för åtkomst till den virtuella datorn från Internet. |
| [az network nic create](/cli/azure/network/nic) | Skapar virtuella nätverksgränssnitt och kopplar dem till det virtuella nätverkets klient- och serverdelsundernät. |
| [az network nsg create](/cli/azure/network/nsg) | Skapar nätverkssäkerhetsgrupper (NSG) som är kopplade till klient- och serverdelsundernäten. |
| [az network nsg rule create](/cli/azure/network/nsg/rule) |Skapar NSG-regler som tillåter eller blockerar specifika portar till specifika undernät. |
| [az vm create](/cli/azure/vm) | Skapar virtuella datorer och kopplar ett nätverkskort till varje virtuell dator. Det här kommandot anger även den virtuella datoravbildning som ska användas samt administrativa autentiseringsuppgifter. |
| [az group delete](/cli/azure/group) | Tar bort en resursgrupp och alla resurser den innehåller. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](/cli/azure).

Ytterligare CLI-skriptexempel för virtuella nätverk finns i [CLI-exempel för virtuella nätverk](../cli-samples.md).
