---
title: Dirigera nätverkstrafik Azure PowerShell | Microsoft-dokument
description: I den här artikeln får du lära dig hur du dirigerar nätverkstrafik med en rutttabell med PowerShell.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
Customer intent: I want to route traffic from one subnet, to a different subnet, through a network virtual appliance.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/13/2018
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: 986371e479f7718fff2e1699401987cb0ca8f623
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73163984"
---
# <a name="route-network-traffic-with-a-route-table-using-powershell"></a>Dirigera nätverkstrafik med en rutttabell med PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Azure dirigerar automatiskt trafik mellan alla undernät inom ett virtuella nätverk som standard. Du kan skapa egna vägar för att åsidosätta Azures standardroutning. Möjligheten att skapa anpassade vägar är användbar om du exempelvis vill dirigera trafik mellan undernät via en virtuell nätverksinstallation (NVA). I den här artikeln kan du se hur du:

* Skapa en routningstabell
* Skapa en väg
* Skapa ett virtuellt nätverk med flera undernät
* Associera en routningstabell till ett undernät
* Skapa en NVA som dirigerar trafik
* Distribuera virtuella datorer till olika undernät
* Dirigera trafik från ett undernät till ett annat via en NVA

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda PowerShell lokalt kräver den här artikeln Azure PowerShell-modul version 1.0.0 eller senare. Kör `Get-Module -ListAvailable Az` för att hitta den installerade versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-az-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Connect-AzAccount` för att skapa en anslutning till Azure.

## <a name="create-a-route-table"></a>Skapa en routningstabell

Innan du kan skapa en flödestabell skapar du en resursgrupp med [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). I följande exempel skapas en resursgrupp med namnet *myResourceGroup* för alla resurser som skapas i den här artikeln.

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

Skapa en rutttabell med [New-AzRouteTable](/powershell/module/az.network/new-azroutetable). I följande exempel skapas en vägtabell med namnet *myRouteTablePublic*.

```azurepowershell-interactive
$routeTablePublic = New-AzRouteTable `
  -Name 'myRouteTablePublic' `
  -ResourceGroupName myResourceGroup `
  -location EastUS
```

## <a name="create-a-route"></a>Skapa en väg

Skapa en rutt genom att hämta flödestabellobjektet med [Get-AzRouteTable](/powershell/module/az.network/get-azroutetable), skapa en rutt med [Add-AzRouteConfig](/powershell/module/az.network/add-azrouteconfig)och skriv sedan flödeskonfigurationen till flödestabellen med [Set-AzRouteTable](/powershell/module/az.network/set-azroutetable).

```azurepowershell-interactive
Get-AzRouteTable `
  -ResourceGroupName "myResourceGroup" `
  -Name "myRouteTablePublic" `
  | Add-AzRouteConfig `
  -Name "ToPrivateSubnet" `
  -AddressPrefix 10.0.1.0/24 `
  -NextHopType "VirtualAppliance" `
  -NextHopIpAddress 10.0.2.4 `
 | Set-AzRouteTable
```

## <a name="associate-a-route-table-to-a-subnet"></a>Associera en routningstabell till ett undernät

Innan du kan associera en routningstabell till ett undernät måste du skapa ett virtuellt nätverk och ett undernät. Skapa ett virtuellt nätverk med hjälp av [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). I följande exempel skapas ett virtuellt nätverk med namnet *myVirtualNetwork* med adressprefixet *10.0.0.0/16*.

```azurepowershell-interactive
$virtualNetwork = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

Skapa tre undernät genom att skapa tre undernätskonfigurationer med [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig). I följande exempel skapas tre undernätskonfigurationer för *offentliga,* *privata*och *DMZ-undernät:*

```azurepowershell-interactive
$subnetConfigPublic = Add-AzVirtualNetworkSubnetConfig `
  -Name Public `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork

$subnetConfigPrivate = Add-AzVirtualNetworkSubnetConfig `
  -Name Private `
  -AddressPrefix 10.0.1.0/24 `
  -VirtualNetwork $virtualNetwork

$subnetConfigDmz = Add-AzVirtualNetworkSubnetConfig `
  -Name DMZ `
  -AddressPrefix 10.0.2.0/24 `
  -VirtualNetwork $virtualNetwork
```

Skriv undernätskonfigurationerna till det virtuella nätverket med [Set-AzVirtualNetwork](/powershell/module/az.network/Set-azVirtualNetwork), som skapar undernäten i det virtuella nätverket:

```azurepowershell-interactive
$virtualNetwork | Set-AzVirtualNetwork
```

Associera vägtabellen *myRouteTablePublic* till det *offentliga* undernätet med [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) och skriv sedan undernätskonfigurationen till det virtuella nätverket med [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork).

```azurepowershell-interactive
Set-AzVirtualNetworkSubnetConfig `
  -VirtualNetwork $virtualNetwork `
  -Name 'Public' `
  -AddressPrefix 10.0.0.0/24 `
  -RouteTable $routeTablePublic | `
Set-AzVirtualNetwork
```

## <a name="create-an-nva"></a>Skapa en NVA

En NVA är en virtuell dator som utför en nätverksfunktion, som routning, brandvägg eller WAN-optimering.

Skapa ett nätverksgränssnitt innan du skapar en virtuell dator.

### <a name="create-a-network-interface"></a>Skapa ett nätverksgränssnitt

Innan du skapar ett nätverksgränssnitt måste du hämta det virtuella nätverks-ID med [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork)och sedan undernäts-ID med [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig). Skapa ett nätverksgränssnitt med [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) i *DMZ-undernätet* med IP-vidarebefordran aktiverat:

```azurepowershell-interactive
# Retrieve the virtual network object into a variable.
$virtualNetwork=Get-AzVirtualNetwork `
  -Name myVirtualNetwork `
  -ResourceGroupName myResourceGroup

# Retrieve the subnet configuration into a variable.
$subnetConfigDmz = Get-AzVirtualNetworkSubnetConfig `
  -Name DMZ `
  -VirtualNetwork $virtualNetwork

# Create the network interface.
$nic = New-AzNetworkInterface `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name 'myVmNva' `
  -SubnetId $subnetConfigDmz.Id `
  -EnableIPForwarding
```

### <a name="create-a-vm"></a>Skapa en virtuell dator

Om du vill skapa en virtuell dator och koppla ett befintligt nätverksgränssnitt till den måste du först skapa en [VM-konfiguration med New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig). Konfigurationen innehåller nätverksgränssnittet som skapades i föregående steg. När du uppmanas att ange ett användarnamn och lösenord väljer du det användarnamn och lösenord som du vill logga in på den virtuella datorn med.

```azurepowershell-interactive
# Create a credential object.
$cred = Get-Credential -Message "Enter a username and password for the VM."

# Create a VM configuration.
$vmConfig = New-AzVMConfig `
  -VMName 'myVmNva' `
  -VMSize Standard_DS2 | `
  Set-AzVMOperatingSystem -Windows `
    -ComputerName 'myVmNva' `
    -Credential $cred | `
  Set-AzVMSourceImage `
    -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer `
    -Skus 2016-Datacenter `
    -Version latest | `
  Add-AzVMNetworkInterface -Id $nic.Id
```

Skapa den virtuella datorn med vm-konfigurationen med [New-AzVM](/powershell/module/az.compute/new-azvm). I följande exempel skapas en virtuell dator med namnet *myVmNva*.

```azurepowershell-interactive
$vmNva = New-AzVM `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -VM $vmConfig `
  -AsJob
```

Alternativet `-AsJob` skapar den virtuella datorn i bakgrunden, så att du kan fortsätta till nästa steg.

## <a name="create-virtual-machines"></a>Skapa virtuella datorer

Skapa två virtuella datorer i det virtuella nätverket så att du kan verifiera att trafiken från det *offentliga* undernätet dirigeras till det *privata* undernätet via den virtuella nätverksinstallationen i ett senare steg.

Skapa en virtuell dator i det *offentliga* undernätet med [New-AzVM](/powershell/module/az.compute/new-azvm). I följande exempel skapas en virtuell dator med namnet *myVmPublic* i det *offentliga* undernätet i det virtuella *nätverket myVirtualNetwork.*

```azurepowershell-interactive
New-AzVm `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -VirtualNetworkName "myVirtualNetwork" `
  -SubnetName "Public" `
  -ImageName "Win2016Datacenter" `
  -Name "myVmPublic" `
  -AsJob
```

Skapa en virtuell dator i undernätet *Privat.*

```azurepowershell-interactive
New-AzVm `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -VirtualNetworkName "myVirtualNetwork" `
  -SubnetName "Private" `
  -ImageName "Win2016Datacenter" `
  -Name "myVmPrivate"
```

Det tar några minuter att skapa den virtuella datorn. Fortsätt inte med nästa steg förrän den virtuella datorn har skapats och Azure returnerar utdata till PowerShell.

## <a name="route-traffic-through-an-nva"></a>Dirigera trafik via NVA

Använd [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) för att returnera den offentliga IP-adressen för *den virtuella datorn myVmPrivate.* I följande exempel returneras den offentliga IP-adressen för den virtuella datorn *myVmPrivate:*

```azurepowershell-interactive
Get-AzPublicIpAddress `
  -Name myVmPrivate `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

Använd följande kommando för att skapa en fjärrskrivbordssession med *den virtuella datorn myVmPrivate* från den lokala datorn. Ersätt `<publicIpAddress>` med IP-adressen som returnerades från föregående kommando.

```
mstsc /v:<publicIpAddress>
```

Öppna den nedladdade RDP-filen. Välj **Anslut** om du uppmanas att göra det.

Ange användarnamnet och lösenordet du angav när du skapade den virtuella datorn (du kanske måste välja **Fler alternativ** och sedan **Använd ett annat konto** för att ange autentiseringsuppgifterna du angav när du skapade den virtuella datorn) och välj **OK**. Du kan få en certifikatvarning under inloggningen. Välj **Ja** för att fortsätta med anslutningen.

I ett senare `tracert.exe` steg används kommandot för att testa routning. Tracert använder ICMP (Internet Control Message Protocol), som nekas via Windows-brandväggen. Aktivera ICMP via Windows-brandväggen genom att ange följande kommando från PowerShell på den virtuella datorn *myVmPrivate*:

```powershell
New-NetFirewallRule -DisplayName "Allow ICMPv4-In" -Protocol ICMPv4
```

Även om spårningsvägen används för att testa routning i den här artikeln, rekommenderas inte att tillåta ICMP via Windows-brandväggen för produktionsdistributioner.

Du aktiverade IP-vidarebefordran inom Azure för de virtuella datorernas nätverksgränssnitt i Aktivera IP-vidarebefordran. I den virtuella datorn måste operativsystemet, eller ett program som körs i den virtuella datorn, kunna vidarebefordra nätverkstrafik. Aktivera IP-vidarebefordran i operativsystemet *i myVmNva*.

Från en kommandotolk på *myVmPrivate* VM, fjärrskrivbord till *myVmNva:*

``` 
mstsc /v:myvmnva
```

Om du vill aktivera IP-vidarebefordran inom operativsystemet anger du följande kommando i PowerShell från den virtuella datorn *myVmNva*:

```powershell
Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters -Name IpEnableRouter -Value 1
```

Starta om den virtuella datorn *myVmNva*, vilket även kopplar från fjärrskrivbordssessionen.

När du fortfarande är ansluten till den virtuella datorn *myVmPrivate* ska du skapa en fjärrskrivbordssession till *myVmPublic*när *myVmNva* har startats om:

``` 
mstsc /v:myVmPublic
```

Aktivera ICMP via Windows-brandväggen genom att ange följande kommando från PowerShell på den virtuella datorn *myVmPublic*:

```powershell
New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
```

Om du vill testa att dirigera nätverkstrafik till den virtuella datorn *myVmPrivate* från *myVmPublic* anger du följande kommando från PowerShell på *myVmPublic*:

```
tracert myVmPrivate
```

Svaret liknar följande exempel:

```
Tracing route to myVmPrivate.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.1.4]
over a maximum of 30 hops:

1    <1 ms     *        1 ms  10.0.2.4
2     1 ms     1 ms     1 ms  10.0.1.4

Trace complete.
```

Du kan se att det första hoppet är 10.0.2.4, som är NVA-enhetens privata IP-adress. Det andra hoppet är is 10.0.1.4, som är den privata IP-adressen för den virtuella datorn *myVmPrivate*. Vägen som har lagts till i routningstabellen *myRouteTablePublic* och associerats till det *offentliga* undernätet gjorde så att Azure dirigerade trafiken via NVA istället för direkt till det *privata* undernätet.

Stäng fjärrskrivbordssession för den virtuella datorn *myVmPublic*. Du är fortfarande ansluten till *myVmPrivate*.

Om du vill testa att dirigera nätverkstrafik till den virtuella datorn *myVmPublic* från *myVmPrivate* anger du följande kommando från en kommandotolk på *myVmPrivate*:

```
tracert myVmPublic
```

Svaret liknar följande exempel:

```
Tracing route to myVmPublic.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.0.4]
over a maximum of 30 hops:

1     1 ms     1 ms     1 ms  10.0.0.4

Trace complete.
```

Du ser att trafik vidarebefordras direkt från *myVmPrivate* till the *myVmPublic*. Som standard dirigerar Azure trafik direkt mellan undernät.

Stäng fjärrskrivbordssessionen för den virtuella datorn *myVmPrivate*.

## <a name="clean-up-resources"></a>Rensa resurser

När det inte längre behövs använder du [Ta bort AzResourcegroup](/powershell/module/az.resources/remove-azresourcegroup) för att ta bort resursgruppen och alla resurser som den innehåller.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln skapade du en flödestabell och associerade den till ett undernät. Du har skapat en enkel virtuell nätverksinstallation som dirigerade trafik från ett offentligt undernät till ett privat undernät. Distribuera en mängd förkonfigurerade virtuella nätverksinstallationer som utför nätverksfunktioner som brandvägg och WAN-optimering från [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking). Mer information om routning finns i [routningsöversikten](virtual-networks-udr-overview.md) och [Hantera en routningstabell](manage-route-table.md).

Du kan distribuera många Azure-resurser inom ett virtuellt nätverk, men resurser för vissa Azure PaaS-tjänster går inte att distribuera till ett virtuellt nätverk. Du kan fortfarande begränsa åtkomsten för resurserna i vissa Azure PaaS-tjänster till trafik enbart från ett undernät för ett virtuell dator. Mer information finns i [Begränsa nätverksåtkomst till PaaS-resurser](tutorial-restrict-network-access-to-resources-powershell.md).