---
title: Konfigurera en VPN-tunnel som alltid är på
titleSuffix: Azure VPN Gateway
description: Steg för att konfigurera Alltid på VPN-tunnel för VPN Gateway
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 03/12/2020
ms.author: cherylmc
ms.openlocfilehash: 9036992a354b41cd75735e5ccd895d7287a00e91
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371789"
---
# <a name="configure-an-always-on-vpn-device-tunnel"></a>Konfigurera en VPN-enhetstunnel för AlwaysOn

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

## <a name="configure-the-gateway"></a>Konfigurera en gateway

Konfigurera VPN-gatewayen så att den använder IKEv2 och certifikatbaserad autentisering med hjälp av artikeln [Konfigurera en punkt-till-plats-VPN-anslutning.](vpn-gateway-howto-point-to-site-resource-manager-portal.md)

## <a name="configure-the-device-tunnel"></a>Konfigurera enhetstunneln

[!INCLUDE [device tunnel](../../includes/vpn-gateway-vwan-always-on-device.md)]

## <a name="to-remove-a-profile"></a>Så här tar du bort en profil

Om du vill ta bort profilen kör du följande kommando:

![Rensa](./media/vpn-gateway-howto-always-on-device-tunnel/cleanup.png)

## <a name="next-steps"></a>Nästa steg

Felsökning finns i [Azure point-to-site-anslutningsproblem](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)
