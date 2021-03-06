---
title: Azure CLI-skriptexempel – Hantera webbtrafik | Microsoft Docs
description: Azure CLI-skriptexempel – Hantera webbtrafik med en programgateway och en VM-skalningsuppsättning.
services: application-gateway
documentationcenter: networking
author: vhorne
editor: tysonn
tags: azure-resource-manager
ms.service: application-gateway
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 01/29/2018
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: f1815d3dbf70e5599aa53633b43029b16cf94037
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "76273857"
---
# <a name="manage-web-traffic-using-the-azure-cli"></a>Hantera webbtrafik med hjälp av Azure CLI

Med det här skriptet skapar du en programgateway som använder en VM-skalningsuppsättning för serverdelen. Programgatewayen kan sedan konfigureras för att hantera webbtrafik. När du har kört skriptet kan du testa programgatewayen med hjälp av dess offentliga IP-adress.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exempelskript

[!code-azurecli-interactive[main](../../../cli_scripts/application-gateway/create-vmss/create-vmss.sh "Create application gateway")]

## <a name="clean-up-deployment"></a>Rensa distribution 

Kör följande kommando för att ta bort resursgruppen, programgatewayen och alla relaterade resurser.

```azurecli-interactive 
az group delete --name myResourceGroupAG --yes
```

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon för att skapa distributionen. Varje post i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group) | Skapar en resursgrupp där alla resurser lagras. |
| [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet) | Skapar ett virtuellt nätverk. |
| [az network vnet subnet create](https://docs.microsoft.com/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) | Skapar ett undernät i ett virtuellt nätverk. |
| [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip?view=azure-cli-latest) | Skapar den offentliga IP-adressen för programgatewayen. |
| [az network application-gateway create](https://docs.microsoft.com/cli/azure/network/application-gateway?view=azure-cli-latest) | Skapar en programgateway. |
| [az vmss create](https://docs.microsoft.com/cli/azure/vmss) | Skapar en VM-skalningsuppsättning. |
| [az network public-ip show](https://docs.microsoft.com/cli/azure/network/public-ip) | Hämtar programgatewayens offentliga IP-adress. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](https://docs.microsoft.com/cli/azure/overview).

Du hittar fler CLI-skriptexempel för programgatewayer i [dokumentationen för virtuella Azure Windows-datorer](../cli-samples.md).
