---
title: 'Konfigurera en VPN-gateway-anslutning för VNet till VNet: Azure-portal'
description: Skapa en VPN-gateway-anslutning mellan virtuella nätverk med hjälp av Resource Manager och Azure Portal.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 03/05/2020
ms.author: cherylmc
ms.openlocfilehash: 3d91203253c08acdaa159fc70f7a34fa7fca20c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78674159"
---
# <a name="configure-a-vnet-to-vnet-vpn-gateway-connection-by-using-the-azure-portal"></a>Konfigurera en VPN-gatewayanslutning mellan virtuella nätverk med hjälp av Azure-portalen

Den här artikeln hjälper dig ansluta virtuella nätverk via VNet-till-VNet-anslutningstypen. Virtuella nätverk kan finnas i olika regioner och i samma eller olika prenumerationer. När du ansluter virtuella nätverk från olika prenumerationer, behöver inte prenumerationerna vara associerade med samma Active Directory-klient. 

![v2v-diagram](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/v2vrmps.png)

Anvisningarna i den här artikeln gäller för Azure Resource Manager-distributionsmodellen och användning av Azure-portalen. Du kan skapa konfigurationen med ett annat distributionsverktyg eller en annan distributionsmodell med hjälp av alternativen som beskrivs i följande artiklar:

> [!div class="op_single_selector"]
> * [Azure-portal](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [Powershell](vpn-gateway-vnet-vnet-rm-ps.md)
> * [Azure CLI](vpn-gateway-howto-vnet-vnet-cli.md)
> * [Azure Portal (klassisk)](vpn-gateway-howto-vnet-vnet-portal-classic.md)
> * [Ansluta olika distributionsmodeller – Azure Portal](vpn-gateway-connect-different-deployment-models-portal.md)
> * [Ansluta olika distributionsmodeller – PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
>
>

## <a name="about-connecting-vnets"></a>Om att ansluta virtuella nätverk

I följande avsnitt beskrivs olika sätt att ansluta till virtuella nätverk.

### <a name="vnet-to-vnet"></a>VNet-till-VNet

Att konfigurera en anslutning mellan virtuella nätverk är ett enkelt sätt att ansluta virtuella nätverk. När du ansluter ett virtuellt nätverk till ett annat virtuellt nätverk med VNet-till-VNet-anslutningstypen (VNet2VNet) på nästan samma sätt som du skapar en plats-till-plats-IPsec-anslutning till en lokal plats. Båda typerna av anslutning använder en VPN-gateway för att få en säker tunnel med IPsec/IKE, och båda fungerar på samma sätt vid kommunikation. Men de skiljer sig åt i hur den lokala nätverksgatewayen konfigureras. 

När du skapar en anslutning mellan virtuella nätverk skapas och fylls adressutrymmet för den lokala nätverksgatewayen i automatiskt. Om du uppdaterar adressutrymmet för ett virtuellt nätverk dirigerar det andra virtuella nätverket automatiskt till det uppdaterade adressutrymmet. Det går normalt snabbare och enklare att skapa en VNet-till-VNet-anslutning än en plats-till-plats-anslutning.

### <a name="site-to-site-ipsec"></a>Plats-till-plats (IPsec)

Om du arbetar med komplicerade nätverkskonfigurationer kan du överväga att ansluta dina virtuella nätverk med hjälp av anvisningarna för [plats-till-plats-anslutning](vpn-gateway-howto-site-to-site-resource-manager-portal.md) i stället. När du följer anvisningarna för plats-till-plats-IPsec skapar och konfigurerar du de lokala nätverksgatewayerna manuellt. Den lokala nätverksgatewayen för varje virtuellt nätverk behandlar det andra virtuella nätverket som en lokal plats. Med de här stegen kan du ange ytterligare adressutrymme för den lokala nätverksgatewayen för att dirigera trafik. Om adressutrymmet för ett virtuellt nätverk ändras måste du uppdatera den motsvarande lokala nätverksgatewayen för att avspegla det.

### <a name="vnet-peering"></a>VNet-peering

Du kan också ansluta dina virtuella nätverk med hjälp av VNet-peering. VNET-peering använder ingen VPN-gateway och har även andra restriktioner. Dessutom beräknas [prissättningen för VNet-peering](https://azure.microsoft.com/pricing/details/virtual-network) på ett annat sätt jämfört med [prissättningen för VPN Gateway mellan virtuella nätverk](https://azure.microsoft.com/pricing/details/vpn-gateway). Mer information finns i [VNet peering (Vnet-peering)](../virtual-network/virtual-network-peering-overview.md).

## <a name="why-create-a-vnet-to-vnet-connection"></a>Varför ska jag skapa en anslutning mellan virtuella nätverk?

Du kan vilja ansluta virtuella nätverk med en anslutning mellan virtuella nätverk av följande skäl:

### <a name="cross-region-geo-redundancy-and-geo-presence"></a>Geografisk redundans i flera regioner och geografisk närvaro

  * Du kan ange din egna geografiska replikering eller synkronisering med en säker anslutning, utan att passera några Internet-slutpunkter.
  * Med Azure Traffic Manager och Azure Load Balancer kan du konfigurera arbetsbelastning med hög tillgänglighet och geografisk redundans över flera Azure-regioner. Du kan till exempel konfigurera SQL Server Always On-Tillgänglighetsgrupper i flera Azure-regioner.

### <a name="regional-multi-tier-applications-with-isolation-or-administrative-boundaries"></a>Regionala flernivåprogram med isolering eller administrativa gränser

  * Inom samma region kan du konfigurera flernivåprogram med flera virtuella nätverk som är anslutna till varandra på grund av isolering eller administrativa krav.

VNet-till-VNet-kommunikation kan kombineras med konfigurationer för flera platser. Med de här konfigurationerna kan du etablera nätverkstopologier som kombinerar anslutningar mellan olika anläggningar med virtuell nätverksanslutning enligt följande diagram:

![Om anslutningar](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/aboutconnections.png "Om anslutningar")

Den här artikeln visar hur du ansluter virtuella nätverk via VNet-till-VNet-anslutningstypen. När du följer dessa steg som en övning kan du använda följande exempel på inställningsvärden. I det här exemplet är de virtuella nätverken i samma prenumeration, men i olika resursgrupper. Observera att du inte skapar anslutningen i portalen om dina virtuella nätverk finns i olika prenumerationer. Använd [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md) eller [CLI](vpn-gateway-howto-vnet-vnet-cli.md) istället. Mer information om anslutningar mellan virtuella nätverk finns i [Vanliga frågor om VNet-till-VNet](#vnet-to-vnet-faq).

### <a name="example-settings"></a>Exempelinställningar

**Värden för VNet1:**

- **Inställningar för virtuella nätverk**
    - **Namn**: VNet1
    - **Adressutrymme**: 10.1.0.0/16
    - **Prenumeration**: Välj den prenumeration du vill använda.
    - **Resursgrupp**: TestRG1
    - **Plats**: Östra USA
    - **Undernät**
        - **Namn**: FrontEnd
        - **Adressintervall**: 10.1.0.0/24
    - **Gateway-undernät:**
        - **Namn**: *GatewaySubnet* fylls i automatiskt
        - **Adressintervall**: 10.1.255.0/27

- **Inställningar för virtuell nätverksgateway**
    - **Namn**: VNet1GW
    - **Gatewaytyp**: välj **VPN**.
    - **VPN-typ**: Välj **Ruttbaserad**.
    - **SKU:** Välj den gateway SKU som du vill använda.
    - **Namn på offentlig IP-adress**: VNet1GWpip
    - **Anslutning**
       - **Namn**: VNet1toVNet4
       - **Delad nyckel**: Du kan skapa den delade nyckeln själv. När du skapar anslutningen mellan de virtuella nätverken måste värdena matcha. Använd abc123 för den här övningen.

**Värden för VNet4:**

- **Inställningar för virtuella nätverk**
   - **Namn**: VNet4
   - **Adressutrymme**: 10.41.0.0/16
   - **Prenumeration**: Välj den prenumeration du vill använda.
   - **Resursgrupp**: TestRG4
   - **Plats**: Västra USA
   - **Undernät** 
      - **Namn**: FrontEnd
      - **Adressintervall**: 10.41.0.0/24
   - **GatewaySubnet** 
      - **Namn**: *GatewaySubnet* fylls i automatiskt
      - **Adressintervall**: 10.41.255.0/27

- **Inställningar för virtuell nätverksgateway** 
    - **Namn**: VNet4GW
    - **Gatewaytyp**: välj **VPN**.
    - **VPN-typ**: Välj **Ruttbaserad**.
    - **SKU:** Välj den gateway SKU som du vill använda.
    - **Namn på offentlig IP-adress**: VNet4GWpip
    - **Anslutning** 
       - **Namn**: VNet4toVNet1
       - **Delad nyckel**: Du kan skapa den delade nyckeln själv. När du skapar anslutningen mellan de virtuella nätverken måste värdena matcha. Använd abc123 för den här övningen.

## <a name="create-and-configure-vnet1"></a>Skapa och konfigurera VNet1
Om du redan har ett VNet, kontrollerar du att inställningarna är kompatibla med din VPN-gatewaydesign. Var särskilt noga med alla undernät som överlappar med andra nätverk. Om du har överlappande undernät fungerar inte anslutningen ordentligt.

### <a name="to-create-a-virtual-network"></a>Så här skapar du ett virtuellt nätverk
[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="create-the-vnet1-gateway"></a>Skapa VNet1-gatewayen
I det här steget ska du skapa den virtuella nätverksgatewayen för ditt virtuella nätverk. Att skapa en gateway kan ofta ta 45 minuter eller mer, beroende på vald gateway-SKU. Om du skapar den här konfigurationen för att öva dig kan du se [exempelinställningarna](#example-settings).

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-portal-include.md)]

### <a name="to-create-a-virtual-network-gateway"></a>Så här skapar du en virtuell nätverksgateway
[!INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

## <a name="create-and-configure-vnet4"></a>Skapa och konfigurera VNet4
När du har konfigurerat VNet1 skapar du VNet4 och VNet4-gatewayen genom att upprepa föregående steg och ersätta värdena med VNet4-värden. Du behöver inte vänta tills den virtuella nätverksgatewayen för VNet1 har skapats innan du konfigurerar VNet4. Om du använder egna värden måste du kontrollera att adressutrymmena inte överlappar några av de virtuella nätverk som du vill ansluta till.

## <a name="configure-the-vnet1-gateway-connection"></a>Konfigurera VNet1-gatewayanslutningen
När de virtuella nätverksgatewayerna för både VNet1 och VNet4 har slutförts kan du skapa dina virtuella nätverksgatewayanslutningar. I det här avsnittet skapar du en anslutning från VNet1 till VNet4. De här stegen fungerar endast för olika VNet i samma prenumeration. Om dina VNet finns i olika prenumerationer, måste du använda [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md) för att ansluta. Om dina VNets är i olika resursgrupper i samma prenumeration kan du emellertid ansluta dem med hjälp av portalen.

1. På Azure-portalen väljer du **Alla resurser**, anger *virtuell nätverksgateway* i sökrutan och går sedan till den virtuella nätverksgatewayen för ditt virtuella nätverk. Till exempel **VNet1GW**. Välj den gateway som ska öppnas för **gatewaysidan för virtuellt nätverk.** Under **Inställningar**väljer du **Anslutningar**.

   ![Sidan Anslutningar](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/connections.png "Sidan Anslutningar")
2. Välj **+Lägg** till om du vill öppna sidan **Lägg till anslutning.**

   ![Lägg till anslutning](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/vnet1-vnet4-connection.png "Lägg till en anslutning")
3. Fyll i värdena för anslutningen på sidan **Lägg till anslutning**:

   - **Namn**: Ange ett namn för anslutningen. Till exempel *VNet1toVNet4*.

   - **Anslutningstyp**: Välj **VNet-till-VNet** i listrutan.

   - **Första virtuella nätverksgateway:** Det här fältvärdet fylls i automatiskt eftersom du skapar den här anslutningen från den angivna virtuella nätverksgatewayen.

   - **Andra virtuella nätverksgateway:** Det här fältet är den virtuella nätverksgatewayen för det virtuella nätverk som du vill skapa en anslutning till. Markera **Välj en annan virtuell nätverksgateway** för att öppna sidan **Välj en virtuell nätverksgateway**.

     - Visa de virtuella nätverksgatewayer som anges på den här sidan. Observera att endast virtuella nätverksgatewayer som ingår i din prenumeration visas. Om du vill ansluta till en virtuell nätverksgateway som inte ingår i din prenumeration kan du använda [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md).

     - Välj den virtuella nätverksgatewayen som du vill ansluta till.

     - **Delad nyckel (PSK)**: Ange en delad nyckel för anslutningen i det här fältet. Du kan generera eller skapa den här nyckeln själv. I en plats-till-plats-anslutning är nyckeln du använder densamma som för din lokala enhet och anslutningen via din virtuella nätverksgateway. Konceptet är i princip samma här, förutom att du istället för att ansluta till en VPN-enhet ansluter till en annan virtuell nätverksgateway.
    
4. Spara ändringarna genom att välja **OK**.

## <a name="configure-the-vnet4-gateway-connection"></a>Konfigurera VNet4-gatewayanslutningen
Skapa sedan en anslutning från VNet4 till VNet1. Leta reda på den virtuella nätverksgateway som är associerad med VNet4 i portalen. Följ stegen från föregående avsnitt och ersätt värdena för att skapa en anslutning från VNet4 till VNet1. Kontrollera att du använder samma delad nyckel.

## <a name="verify-your-connections"></a>Verifiera dina anslutningar

1. Leta rätt på den virtuella nätverksgatewayen på Azure-portalen. 
2. På sidan för den **virtuella nätverksgatewayen** väljer du **Anslutningar** för att visa **anslutningssidan** för den virtuella nätverksgatewayen. När anslutningen har upprättats visas **statusvärdena** till **Ansluten**.

   ![Verifiera anslutningar](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/view-connections.png "Verifiera anslutningar")
3. Markera en av anslutningarna under kolumnen **Namn** om du vill visa mer information. När data börjar flöda ser du värdena för **Data in** och **Data ut**.

   ![Status](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/status.png "Status")

## <a name="add-additional-connections"></a>Lägga till ytterligare anslutningar

Om du vill lägga till ytterligare anslutningar går du till den virtuella nätverksgatewayen som du vill skapa anslutningen från och väljer **Anslutningar**. Du kan skapa ytterligare en VNet-till-VNet-anslutning, eller skapa en IPsec Plats-till-plats-anslutning till en lokal plats. Se till att justera **Anslutningstyp** så att den överensstämmer med den anslutningstyp du vill skapa. Innan du skapar fler anslutningar bör du kontrollera att adressutrymmet för ditt virtuella nätverk inte överlappar med något av de andra adressutrymmena du vill ansluta till. Instruktioner om att skapa en Plats-till-plats-anslutning finns i [Skapa en plats-till-plats-anslutning](vpn-gateway-howto-site-to-site-resource-manager-portal.md).

## <a name="vnet-to-vnet-faq"></a>Vanliga frågor och svar om VNet-till-VNet
Visa vanliga frågor och svar om du vill ha mer information om anslutningar mellan virtuella nätverk.

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]

## <a name="next-steps"></a>Nästa steg

Mer information om hur du begränsar nätverkstrafiken till resurser i ett virtuellt nätverk finns i [Nätverkssäkerhet](../virtual-network/security-overview.md).

Mer information om hur Azure dirigerar trafik mellan Azure, lokala och Internet-resurser finns i [Trafikdirigering i virtuella nätverk](../virtual-network/virtual-networks-udr-overview.md).
