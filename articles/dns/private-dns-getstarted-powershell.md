---
title: Snabbstart – Skapa en privat Azure DNS-zon med Azure PowerShell
description: I den här artikeln skapar och testar du en privat DNS-zon och spelar in i Azure DNS. Det här är en steg-för-steg-guide om hur du skapar och hanterar din första privata DNS-zon och DNS-post med Azure PowerShell.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: quickstart
ms.date: 10/05/2019
ms.author: rohink
ms.openlocfilehash: 0db53bcd6516bd52e2796deaa49fe0dd582e0588
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "76939395"
---
# <a name="quickstart-create-an-azure-private-dns-zone-using-azure-powershell"></a>Snabbstart: Skapa en privat Azure DNS-zon med Azure PowerShell

Den här artikeln visar hur du skapar din första privata DNS-zon och DNS-post med Azure PowerShell.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

En DNS-zon används som värd åt DNS-posterna för en viss domän. Om du vill låta Azure DNS vara värd för din domän så måste du skapa en DNS-zon för det domännamnet. Varje DNS-post för din domän skapas sedan i den här DNS-zonen. Om du vill publicera en privat DNS-zon i det virtuella nätverket anger du den lista över virtuella nätverk som får lösa poster i zonen.  Dessa kallas *länkade* virtuella nätverk. När autoregistration är aktiverat uppdaterar Azure DNS också zonposterna när en virtuell dator skapas, ändrar sin IP-adress eller tas bort.

I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Skapa en privat DNS-zon
> * Skapa virtuella testdatorer
> * Skapa en ytterligare DNS-post
> * Testa den privata zonen

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du inte har en Azure-prenumeration skapar du ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

Om du vill kan du slutföra den här snabbstarten med [Azure CLI](private-dns-getstarted-cli.md).

## <a name="create-the-resource-group"></a>Skapa en resursgrupp

Först skapar du en resursgrupp som ska innehålla DNS-zonen: 

```azurepowershell
New-AzResourceGroup -name MyAzureResourceGroup -location "eastus"
```

## <a name="create-a-private-dns-zone"></a>Skapa en privat DNS-zon

En DNS-zon skapas med hjälp av cmdleten `New-AzPrivateDnsZone`.

I följande exempel skapas ett virtuellt nätverk med namnet **myAzureVNet**. Sedan skapas en DNS-zon med namnet **private.contoso.com** i resursgruppen **MyAzureResourceGroup,** länkar DNS-zonen till **myAzureVnets** virtuella nätverk och möjliggör automatisk registrering.

```azurepowershell
Install-Module -Name Az.PrivateDns -force

$backendSubnet = New-AzVirtualNetworkSubnetConfig -Name backendSubnet -AddressPrefix "10.2.0.0/24"
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName MyAzureResourceGroup `
  -Location eastus `
  -Name myAzureVNet `
  -AddressPrefix 10.2.0.0/16 `
  -Subnet $backendSubnet

$zone = New-AzPrivateDnsZone -Name private.contoso.com -ResourceGroupName MyAzureResourceGroup

$link = New-AzPrivateDnsVirtualNetworkLink -ZoneName private.contoso.com `
  -ResourceGroupName MyAzureResourceGroup -Name "mylink" `
  -VirtualNetworkId $vnet.id -EnableRegistration
```

Om du vill skapa en zon bara för namnmatchning (ingen `-EnableRegistration` automatisk registrering av värdnamn) kan du utelämna parametern.

### <a name="list-dns-private-zones"></a>Lista privata DNS-zoner

Genom att utelämna zonnamnet från `Get-AzPrivateDnsZone` kan du räkna upp alla zoner i en resursgrupp. Den här åtgärden returnerar en matris med zonobjekt.

```azurepowershell
$zones = Get-AzPrivateDnsZone -ResourceGroupName MyAzureResourceGroup
$zones
```

Genom att utelämna både zonnamnet och resursgruppsnamnet från `Get-AzPrivateDnsZone` kan du räkna upp alla zoner i Azure-prenumerationen.

```azurepowershell
$zones = Get-AzPrivateDnsZone
$zones
```

## <a name="create-the-test-virtual-machines"></a>Skapa de virtuella testdatorerna

Nu skapar du två virtuella datorer så att du kan testa din privata DNS-zon:

```azurepowershell
New-AzVm `
    -ResourceGroupName "myAzureResourceGroup" `
    -Name "myVM01" `
    -Location "East US" `
    -subnetname backendSubnet `
    -VirtualNetworkName "myAzureVnet" `
    -addressprefix 10.2.0.0/24 `
    -OpenPorts 3389

New-AzVm `
    -ResourceGroupName "myAzureResourceGroup" `
    -Name "myVM02" `
    -Location "East US" `
    -subnetname backendSubnet `
    -VirtualNetworkName "myAzureVnet" `
    -addressprefix 10.2.0.0/24 `
    -OpenPorts 3389
```

Det här kan ta några minuter.

## <a name="create-an-additional-dns-record"></a>Skapa en ytterligare DNS-post

Du skapar postuppsättningar med hjälp av cmdleten `New-AzPrivateDnsRecordSet`. I följande exempel skapas en post med det relativa namnet **db** i DNS-zonen **private.contoso.com**i resursgruppen **MyAzureResourceGroup**. Det fullständigt kvalificerade namnet på postuppsättningen är **db.private.contoso.com**. Posttypen är ”A” med IP-adressen ”10.2.0.4”, och TTL är 3600 sekunder.

```azurepowershell
New-AzPrivateDnsRecordSet -Name db -RecordType A -ZoneName private.contoso.com `
   -ResourceGroupName MyAzureResourceGroup -Ttl 3600 `
   -PrivateDnsRecords (New-AzPrivateDnsRecordConfig -IPv4Address "10.2.0.4")
```

### <a name="view-dns-records"></a>Visa DNS-poster

Om du vill visa en lista med DNS-poster i din zon kör du:

```azurepowershell
Get-AzPrivateDnsRecordSet -ZoneName private.contoso.com -ResourceGroupName MyAzureResourceGroup
```

## <a name="test-the-private-zone"></a>Testa den privata zonen

Nu kan du testa namnmatchningen för din **private.contoso.com** privata zon.

### <a name="configure-vms-to-allow-inbound-icmp"></a>Konfigurera virtuella datorer för att tillåta inkommande ICMP

Du kan använda ping-kommandot för att testa namnmatchning. Konfigurera därför brandväggen på båda de virtuella datorerna att tillåta inkommande ICMP-paket.

1. Anslut till myVM01 och öppna ett Windows PowerShell-fönster med administratörsbehörighet.
2. Kör följande kommando:

   ```powershell
   New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
   ```

Upprepa för myVM02.

### <a name="ping-the-vms-by-name"></a>Pinga de virtuella datorerna efter namn

1. Från Windows PowerShell-kommandotolken för myVM02 pingar du myVM01 med hjälp av det automatiskt registrerade värddatornamnet:

   ```
   ping myVM01.private.contoso.com
   ```

   Du bör se utdata som liknar följande:

   ```
   PS C:\> ping myvm01.private.contoso.com

   Pinging myvm01.private.contoso.com [10.2.0.4] with 32 bytes of data:
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time=1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128

   Ping statistics for 10.2.0.4:
       Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
   Approximate round trip times in milli-seconds:
       Minimum = 0ms, Maximum = 1ms, Average = 0ms
   PS C:\>
   ```

2. Nu pingar du det **db**-namn som du skapade tidigare:

   ```
   ping db.private.contoso.com
   ```

   Du bör se utdata som liknar följande:

   ```
   PS C:\> ping db.private.contoso.com

   Pinging db.private.contoso.com [10.2.0.4] with 32 bytes of data:
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128

   Ping statistics for 10.2.0.4:
       Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
   Approximate round trip times in milliseconds:
       Minimum = 0ms, Maximum = 0ms, Average = 0ms
   PS C:\>
   ```

## <a name="delete-all-resources"></a>Ta bort alla resurser

När det inte längre behövs tar du bort resursgruppen **MyAzureResourceGroup** för att ta bort de resurser som skapas i den här artikeln.

```azurepowershell
Remove-AzResourceGroup -Name MyAzureResourceGroup
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Scenarier med Azure DNS Private Zones](private-dns-scenarios.md)
