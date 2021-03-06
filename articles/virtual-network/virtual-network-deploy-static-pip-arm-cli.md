---
title: Skapa en virtuell dator med en statisk offentlig IP-adress - Azure CLI | Microsoft-dokument
description: Lär dig hur du skapar en virtuell dator med en statisk offentlig IP-adress med hjälp av Azure command-line interface (CLI).
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: 55bc21b0-2a45-4943-a5e7-8d785d0d015c
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/08/2018
ms.author: kumud
ms.openlocfilehash: 4b8f91d7d9fc414ed0ae6387c25f71b1601e0b6c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76043407"
---
# <a name="create-a-virtual-machine-with-a-static-public-ip-address-using-the-azure-cli"></a>Skapa en virtuell dator med en statisk offentlig IP-adress med Azure CLI

Du kan skapa en virtuell dator med en statisk offentlig IP-adress. Med en offentlig IP-adress kan du kommunicera med en virtuell dator från Internet. Tilldela en statisk offentlig IP-adress i stället för en dynamisk adress för att säkerställa att adressen aldrig ändras. Läs mer om [statiska offentliga IP-adresser](virtual-network-ip-addresses-overview-arm.md#allocation-method). Information om hur du ändrar en offentlig IP-adress som tilldelats en befintlig virtuell dator från dynamisk till statisk eller för att arbeta med privata IP-adresser finns [i Lägga till, ändra eller ta bort IP-adresser](virtual-network-network-interface-addresses.md). Offentliga IP-adresser har en [nominell avgift](https://azure.microsoft.com/pricing/details/ip-addresses)och det finns en [gräns](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) för antalet offentliga IP-adresser som du kan använda per prenumeration.

## <a name="create-a-virtual-machine"></a>Skapa en virtuell dator

Du kan slutföra följande steg från din lokala dator eller genom att använda Azure Cloud Shell. Om du vill använda den lokala datorn måste du se till att Du har [Azure CLI installerat](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json). Om du vill använda Azure Cloud Shell väljer du **Prova det** i det övre högra hörnet av en kommandoruta som följer. Cloud Shell loggar in dig i Azure.

1. Om du använder Cloud Shell, hoppa till steg 2. Öppna en kommandosession och `az login`logga in på Azure med .
2. Skapa en resursgrupp med kommandot [az group create](/cli/azure/group#az-group-create). I följande exempel skapas en resursgrupp i regionen Östra USA Azure:

   ```azurecli-interactive
   az group create --name myResourceGroup --location eastus
   ```

3. Skapa en virtuell dator med kommandot [az vm create](/cli/azure/vm#az-vm-create). Alternativet `--public-ip-address-allocation=static` tilldelar den virtuella datorn en statisk offentlig IP-adress. I följande exempel skapas en virtuell Ubuntu-dator med en statisk, grundläggande SKU-offentlig IP-adress med namnet *myPublicIpAddress:*

   ```azurecli-interactive
   az vm create \
     --resource-group myResourceGroup \
     --name myVM \
     --image UbuntuLTS \
     --admin-username azureuser \
     --generate-ssh-keys \
     --public-ip-address myPublicIpAddress \
     --public-ip-address-allocation static
   ```

   Om den offentliga IP-adressen måste vara `--public-ip-sku Standard` en standard-SKU lägger du till i föregående kommando. Läs mer om [SKU:er för offentlig IP-adress](virtual-network-ip-addresses-overview-arm.md#sku). Om den virtuella datorn läggs till i backend-poolen för en offentlig Azure Load Balancer måste SKU för den virtuella datorns offentliga IP-adress matcha SKU för belastningsutjämnarens offentliga IP-adress. Mer information finns i [Azure Load Balancer](../load-balancer/concepts-limitations.md#skus).

4. Visa den offentliga IP-adressen som tilldelats och bekräfta att den skapades som en statisk, grundläggande SKU-adress, med [az network public-ip show:](/cli/azure/network/public-ip#az-network-public-ip-show)

   ```azurecli-interactive
   az network public-ip show \
     --resource-group myResourceGroup \
     --name myPublicIpAddress \
     --query [ipAddress,publicIpAllocationMethod,sku] \
     --output table
   ```

   Azure tilldelade en offentlig IP-adress från adresser som används i den region som du skapade den virtuella datorn i. Du kan ladda ned listan över intervall (prefix) för [offentliga](https://www.microsoft.com/download/details.aspx?id=56519) Azure-moln och för Azure-moln för [amerikanska myndigheter](https://www.microsoft.com/download/details.aspx?id=57063), [Kina](https://www.microsoft.com/download/details.aspx?id=57062) eller [Tyskland](https://www.microsoft.com/download/details.aspx?id=57064).

> [!WARNING]
> Ändra inte IP-adressinställningarna i den virtuella datorns operativsystem. Operativsystemet känner inte till offentliga Azure-IP-adresser. Även om du kan lägga till privata IP-adressinställningar i operativsystemet rekommenderar vi att du inte gör det om det inte är nödvändigt, och inte förrän efter att ha läst [Lägg till en privat IP-adress i ett operativsystem](virtual-network-network-interface-addresses.md#private).

## <a name="clean-up-resources"></a>Rensa resurser

När resurserna inte behövs längre kan du använda [az group delete](/cli/azure/group#az-group-delete) för att ta bort resursgruppen och alla relaterade resurser den innehåller:

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Nästa steg

- Läs mer om [offentliga IP-adresser](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) i Azure
- Läs mer om alla [inställningar för offentliga IP-adresser](virtual-network-public-ip-address.md#create-a-public-ip-address)
- Läs mer om [privata IP-adresser](virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) och tilldela en [statisk privat IP-adress](virtual-network-network-interface-addresses.md#add-ip-addresses) till en virtuell Azure-dator
- Läs mer om hur du skapar virtuella [Linux-](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) och [Windows-datorer](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
