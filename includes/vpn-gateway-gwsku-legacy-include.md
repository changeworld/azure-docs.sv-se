---
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/09/2018
ms.author: cherylmc
ms.openlocfilehash: 622a2f69c2e7b82f2df10d6120ee2dd466961915
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77211407"
---
De äldre SKU:erna (gamla) för VPN-gatewayer är:

* Standard (Grundläggande)
* Standard
* HighPerformance

VPN Gateway använder inte SKU för UltraPerformance-gateway. Information om UltraPerformance SKU finns i [ExpressRoute](../articles/expressroute/expressroute-about-virtual-network-gateways.md)-dokumentationen.

Tänk på följande när du arbetar med de äldre SKU:erna:

* Om du vill använda en PolicyBased VPN-typ måste du använda Basic SKU:n. PolicyBased VPN (tidigare kallade statisk routning) stöds inte på andra SKU:er.
* BGP stöds inte på Basic-SKU:n.
* ExpressRoute VPN Gateway samexisterande konfigurationer stöds inte på Basic-SKU:n.
* S2S VPN Gateway-anslutningar av typen aktiv-aktiv kan enbart konfigureras på HighPerformance SKU.