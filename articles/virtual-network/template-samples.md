---
title: Azure Resource Manager-exempelmallar för virtuella nätverk | Microsoft Docs
description: Lär dig om olika Azure Resource Manager-mallar som du kan distribuera virtuella Azure-nätverk med.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: twooley
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 04/22/2019
ms.author: kumud
ms.openlocfilehash: 5971b3251812ea4193e71173943ffc0d48207567
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "75975356"
---
# <a name="azure-resource-manager-template-samples-for-virtual-network"></a>Azure Resource Manager-exempelmallar för virtuella nätverk

Följande tabell innehåller länkar till Azure Resource Manager-exempelmallar. Du kan distribuera mallar med Azure [Portal](../azure-resource-manager/templates/deploy-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json), Azure [CLI](../azure-resource-manager/templates/deploy-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json) eller Azure [PowerShell](../azure-resource-manager/templates/deploy-powershell.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Information om hur du skapar egna mallar finns i [Skapa din första mall](../azure-resource-manager/resource-manager-create-first-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json) och [Förstå strukturen och syntaxen för Azure Resource Manager-mallar](../azure-resource-manager/templates/template-syntax.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Den JSON-syntax och de egenskaper som ska användas i mallar finns i avsnittet om [Microsoft.Network-resurstyper](/azure/templates/microsoft.network/allversions).


| Aktivitet | Beskrivning |
|----|----|
|[Skapa ett virtuellt nätverk med två undernät](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vnet-two-subnets)| Skapar ett virtuellt nätverk med två undernät.|
|[Dirigera trafik via en virtuell nätverksinstallation](https://github.com/Azure/azure-quickstart-templates/tree/master/201-userdefined-routes-appliance)| Skapar ett virtuellt nätverk med tre undernät. Distribuerar en virtuell dator i varje undernät. Skapar en routingtabell som innehåller vägar för att dirigera trafik från ett undernät till ett annat genom den virtuella datorn i det tredje undernätet. Associerar routningstabellen med ett av undernäten.|
|[ Skapa en tjänstslutpunkt för virtuellt nätverk för Azure Storage](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vnet-2subnets-service-endpoints-storage-integration)|Skapar ett nytt virtuellt nätverk med två undernät och ett nätverksgränssnitt i varje undernät. Aktiverar en tjänstslutpunkt till Azure Storage för ett av undernäten och kopplar ett nytt lagringskonto till det undernätet.|
|[Ansluta två virtuella nätverk](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vnet-to-vnet-peering)| Skapar två virtuella nätverk och peering mellan dem.|
|[Skapa en virtuell dator med flera IP-adresser](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-multiple-ipconfig)| Skapar en virtuell Windows- eller Linux-dator med flera IP-adresser.|
|[Konfigurera virtuellt nätverk för IPv4 + IPv6-dubbel stack](https://github.com/Azure/azure-quickstart-templates/tree/master/ipv6-in-vnet)|Distribuerar virtuella nätverk med dubbla stackar (IPv4+IPv6) med två virtuella datorer och en Azure Basic Load Balancer med offentliga IP-adresser iPv4 och IPv6. |
