---
title: 'Skapa en ruttbaserad VPN-gateway: portal'
titleSuffix: Azure VPN Gateway
description: Skapa en ruttbaserad VPN-gateway med Azure-portalen
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 03/03/2020
ms.author: cherylmc
ms.openlocfilehash: 6156d689a29ee348f9b1974d1520eb7d186a8d8d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78331357"
---
# <a name="create-a-route-based-vpn-gateway-using-the-azure-portal"></a>Skapa en ruttbaserad VPN-gateway med Azure-portalen

Den här artikeln hjälper dig att snabbt skapa en ruttbaserad Azure VPN-gateway med Azure-portalen.  En VPN-gateway används när du skapar en VPN-anslutning till ditt lokala nätverk. Du kan också använda en VPN-gateway för att ansluta virtuella nätverk. 

Stegen i den här artikeln skapar ett virtuellt nätverk, ett undernät, ett gateway-undernät och en ruttbaserad VPN-gateway (virtuell nätverksgateway). När gatewayen har skapats kan du skapa anslutningar. Dessa steg kräver en Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

## <a name="create-a-virtual-network"></a><a name="vnet"></a>Skapa ett virtuellt nätverk

[!INCLUDE [Basic Point-to-Site VNet](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="configure-and-create-the-gateway"></a><a name="gwvalues"></a>Konfigurera och skapa gatewayen

I det här steget ska du skapa den virtuella nätverksgatewayen för ditt virtuella nätverk. Att skapa en gateway kan ofta ta 45 minuter eller mer, beroende på vald gateway-SKU.

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-portal-include.md)]

[!INCLUDE [Create a gateway](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

>[!NOTE]
>Basic gateway SKU stöder inte IKEv2- eller RADIUS-autentisering. Om du planerar att låta Mac-klienter ansluta till det virtuella nätverket ska du inte använda basic SKU.

[!INCLUDE [NSG warning](../../includes/vpn-gateway-no-nsg-include.md)]

## <a name="view-the-vpn-gateway"></a><a name="viewgw"></a>Visa VPN-gatewayen

1. När gatewayen har skapats navigerar du till VNet1 i portalen. VPN-gatewayen visas på översiktssidan som en ansluten enhet.

   ![Anslutna enheter](./media/create-routebased-vpn-gateway-portal/view-connected-devices.png "Anslutna enheter")

2. Klicka på **VNet1GW** i enhetslistan om du vill visa mer information.

   ![Visa VPN-gateway](./media/create-routebased-vpn-gateway-portal/view-gateway.png "Visa VPN-gateway")

## <a name="next-steps"></a>Nästa steg

När gatewayen har skapats kan du skapa en anslutning mellan det virtuella nätverket och ett annat virtuellt nätverk. Du kan också skapa en anslutning mellan det virtuella nätverket och en lokal plats.

> [!div class="nextstepaction"]
> [Skapa en plats-till-plats-anslutning](vpn-gateway-howto-site-to-site-resource-manager-portal.md)<br><br>
> [Skapa en punkt-till-plats-anslutning](vpn-gateway-howto-point-to-site-resource-manager-portal.md)<br><br>
> [Skapa en anslutning till ett annat virtuella nätverk](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
