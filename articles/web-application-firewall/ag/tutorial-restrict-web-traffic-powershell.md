---
title: Begränsa webbtrafiken med PowerShell
titleSuffix: Azure Web Application Firewall
description: Lär dig hur du begränsar webbtrafik med en brandvägg för webbprogram i en programgateway med Azure PowerShell.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 11/14/2019
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: a57c5b155f7ab00f781236cfceea59a4277ff06a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74046276"
---
# <a name="enable-web-application-firewall-using-azure-powershell"></a>Aktivera brandvägg för webbprogram med Azure PowerShell

Du kan begränsa trafiken på en programgateway med en [brandvägg för webbprogram](ag-overview.md) (WAF). I brandväggen används [OWASP](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project)-regler till att skydda programmet. De här reglerna kan exempelvis skydda mot attacker som SQL-inmatning, skriptattacker mellan webbplatser och sessionskapningar. 

I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Konfigurera nätverket
> * Skapa en programgateway med WAF aktiverat
> * Skapa en VM-skalningsuppsättning
> * Skapa ett lagringskonto och konfigurera diagnostik

![Exempel på brandvägg för webbaserade program](../media/tutorial-restrict-web-traffic-powershell/scenario-waf.png)

Om du vill kan du fylla i den här artikeln med [Azure-portalen](application-gateway-web-application-firewall-portal.md) eller [Azure CLI](tutorial-restrict-web-traffic-cli.md).

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda PowerShell lokalt kräver den här artikeln Azure PowerShell-modul version 1.0.0 eller senare. Kör `Get-Module -ListAvailable Az` för att hitta versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-az-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Login-AzAccount` för att skapa en anslutning till Azure.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

En resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. Skapa en Azure-resursgrupp med [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup).  

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupAG -Location eastus
```

## <a name="create-network-resources"></a>Skapa nätverksresurser 

Skapa undernätskonfigurationerna *myBackendSubnet* och *myAGSubnet* med [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig). Skapa det virtuella nätverket *myVNet* med [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) med undernätskonfigurationerna. Och slutligen, skapa den offentliga IP-adressen som heter *myAGPublicIPAddress* med [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress). De här resurserna används för att ge nätverksanslutning till programgatewayen och tillhörande resurser.

```azurepowershell-interactive
$backendSubnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name myBackendSubnet `
  -AddressPrefix 10.0.1.0/24

$agSubnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name myAGSubnet `
  -AddressPrefix 10.0.2.0/24

$vnet = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myVNet `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $backendSubnetConfig, $agSubnetConfig

$pip = New-AzPublicIpAddress `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myAGPublicIPAddress `
  -AllocationMethod Static `
  -Sku Standard
```

## <a name="create-an-application-gateway"></a>Skapa en programgateway

I det här avsnittet skapar du resurser som stöder programgatewayen och sedan slutligen skapar den och en WAF. Du skapar bland annat följande resurser:

- *IP-konfigurationer och en klientdelsport* – associerar det undernät du skapade tidigare till programgatewayen och tilldelar en port för åtkomst till den.
- *Standardpool* – alla programgatewayer måste ha minst en serverpool i serverdelen.
- *Standardlyssnare och -regel* – standardlyssnaren lyssnar efter trafik på den tilldelade porten och standardregeln skickar trafik till standardpoolen.

### <a name="create-the-ip-configurations-and-frontend-port"></a>Skapa IP-konfigurationerna och klientdelsporten

Associera *myAGSubnet* som du tidigare har skapat till programgatewayen med [New-AzApplicationGatewayIPConfiguration](/powershell/module/az.network/new-azapplicationgatewayipconfiguration). Tilldela *myAGPublicIPAddress* till programgatewayen med [New-AzApplicationGatewayFrontendIPConfig](/powershell/module/az.network/new-azapplicationgatewayfrontendipconfig).

```azurepowershell-interactive
$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Name myVNet

$subnet=$vnet.Subnets[1]

$gipconfig = New-AzApplicationGatewayIPConfiguration `
  -Name myAGIPConfig `
  -Subnet $subnet

$fipconfig = New-AzApplicationGatewayFrontendIPConfig `
  -Name myAGFrontendIPConfig `
  -PublicIPAddress $pip

$frontendport = New-AzApplicationGatewayFrontendPort `
  -Name myFrontendPort `
  -Port 80
```

### <a name="create-the-backend-pool-and-settings"></a>Skapa serverdelspoolen och tillhörande inställningar

Skapa backend-poolen med namnet *appGatewayBackendPool* för programgatewayen med [New-AzApplicationGatewayBackendAddressPool](/powershell/module/az.network/new-azapplicationgatewaybackendaddresspool). Konfigurera inställningarna för serverdelsadresspoolerna med [New-AzApplicationGatewayBackendHttpSettings](/powershell/module/az.network/new-azapplicationgatewaybackendhttpsetting).

```azurepowershell-interactive
$defaultPool = New-AzApplicationGatewayBackendAddressPool `
  -Name appGatewayBackendPool

$poolSettings = New-AzApplicationGatewayBackendHttpSettings `
  -Name myPoolSettings `
  -Port 80 `
  -Protocol Http `
  -CookieBasedAffinity Enabled `
  -RequestTimeout 120
```

### <a name="create-the-default-listener-and-rule"></a>Skapa standardlyssnare och -regel

Du behöver en lyssnare så att programgatewayen kan dirigera trafiken till serverdelens adresspooler på rätt sätt. I det här exemplet skapar du en grundläggande lyssnare som lyssnar efter trafik vid rotadressen. 

Skapa en lyssnare med namnet *mydefaultListener* med [New-AzApplicationGatewayHttpListener](/powershell/module/az.network/new-azapplicationgatewayhttplistener) med frontend-konfigurationen och frontend-porten som du tidigare skapade. Du måste ange en regel för lyssnaren som anger vilken serverdelspool som ska användas för inkommande trafik. Skapa en grundregel med namnet *rule1* med [New-AzApplicationGatewayRequestRoutingRule](/powershell/module/az.network/new-azapplicationgatewayrequestroutingrule).

```azurepowershell-interactive
$defaultlistener = New-AzApplicationGatewayHttpListener `
  -Name mydefaultListener `
  -Protocol Http `
  -FrontendIPConfiguration $fipconfig `
  -FrontendPort $frontendport

$frontendRule = New-AzApplicationGatewayRequestRoutingRule `
  -Name rule1 `
  -RuleType Basic `
  -HttpListener $defaultlistener `
  -BackendAddressPool $defaultPool `
  -BackendHttpSettings $poolSettings
```

### <a name="create-the-application-gateway-with-the-waf"></a>Skapa programgatewayen med brandväggen för webbaserade program

Nu när du har skapat nödvändiga stödresurser anger du parametrar för programgatewayen med [New-AzApplicationGatewaywaySku](/powershell/module/az.network/new-azapplicationgatewaysku). Ange brandväggsprincipen med [New-AzApplicationGatewayFirewallPolicy](/powershell/module/az.network/new-azapplicationgatewayfirewallpolicy). Och sedan skapa ansökan gateway som heter *myAppGateway* med [New-AzApplicationGateway](/powershell/module/az.network/new-azapplicationgateway).

```azurepowershell-interactive
$sku = New-AzApplicationGatewaySku `
  -Name WAF_v2 `
  -Tier WAF_v2 `
  -Capacity 2

$policySetting = New-AzApplicationGatewayFirewallPolicySetting -Mode Prevention -State Enabled -MaxRequestBodySizeInKb 100 -MaxFileUploadInMb 256

$wafPolicy = New-AzApplicationGatewayFirewallPolicy -Name wafpolicyNew -ResourceGroup $rgname -Location $location -PolicySetting $PolicySetting

$appgw = New-AzApplicationGateway `
  -Name myAppGateway `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -BackendAddressPools $defaultPool `
  -BackendHttpSettingsCollection $poolSettings `
  -FrontendIpConfigurations $fipconfig `
  -GatewayIpConfigurations $gipconfig `
  -FrontendPorts $frontendport `
  -HttpListeners $defaultlistener `
  -RequestRoutingRules $frontendRule `
  -Sku $sku `
  -FirewallPolicy $wafPolicy
```

## <a name="create-a-virtual-machine-scale-set"></a>Skapa en VM-skalningsuppsättning

I det här exemplet skapar du en VM-skalningsuppsättning som ska tillhandahålla servrar till serverdelspoolen i programgatewayen. Du tilldelar skalningsuppsättningen till serverdelspoolen när du konfigurerar IP-inställningarna.

```azurepowershell-interactive
$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Name myVNet

$appgw = Get-AzApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway

$backendPool = Get-AzApplicationGatewayBackendAddressPool `
  -Name appGatewayBackendPool `
  -ApplicationGateway $appgw

$ipConfig = New-AzVmssIpConfig `
  -Name myVmssIPConfig `
  -SubnetId $vnet.Subnets[1].Id `
  -ApplicationGatewayBackendAddressPoolsId $backendPool.Id

$vmssConfig = New-AzVmssConfig `
  -Location eastus `
  -SkuCapacity 2 `
  -SkuName Standard_DS2 `
  -UpgradePolicyMode Automatic

Set-AzVmssStorageProfile $vmssConfig `
  -ImageReferencePublisher MicrosoftWindowsServer `
  -ImageReferenceOffer WindowsServer `
  -ImageReferenceSku 2016-Datacenter `
  -ImageReferenceVersion latest `
  -OsDiskCreateOption FromImage

Set-AzVmssOsProfile $vmssConfig `
  -AdminUsername azureuser `
  -AdminPassword "Azure123456!" `
  -ComputerNamePrefix myvmss

Add-AzVmssNetworkInterfaceConfiguration `
  -VirtualMachineScaleSet $vmssConfig `
  -Name myVmssNetConfig `
  -Primary $true `
  -IPConfiguration $ipConfig

New-AzVmss `
  -ResourceGroupName myResourceGroupAG `
  -Name myvmss `
  -VirtualMachineScaleSet $vmssConfig
```

### <a name="install-iis"></a>Installera IIS

```azurepowershell-interactive
$publicSettings = @{ "fileUris" = (,"https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/appgatewayurl.ps1"); 
  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File appgatewayurl.ps1" }

$vmss = Get-AzVmss -ResourceGroupName myResourceGroupAG -VMScaleSetName myvmss

Add-AzVmssExtension -VirtualMachineScaleSet $vmss `
  -Name "customScript" `
  -Publisher "Microsoft.Compute" `
  -Type "CustomScriptExtension" `
  -TypeHandlerVersion 1.8 `
  -Setting $publicSettings

Update-AzVmss `
  -ResourceGroupName myResourceGroupAG `
  -Name myvmss `
  -VirtualMachineScaleSet $vmss
```

## <a name="create-a-storage-account-and-configure-diagnostics"></a>Skapa ett lagringskonto och konfigurera diagnostik

I den här artikeln använder programgatewayen ett lagringskonto för att lagra data för identifiering och förebyggande ändamål. Du kan även använda Azure Monitor-loggar eller Event Hub till att registrera data.

### <a name="create-the-storage-account"></a>Skapa lagringskontot

Skapa ett lagringskonto med namnet *myagstore1* med [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount).

```azurepowershell-interactive
$storageAccount = New-AzStorageAccount `
  -ResourceGroupName myResourceGroupAG `
  -Name myagstore1 `
  -Location eastus `
  -SkuName "Standard_LRS"
```

### <a name="configure-diagnostics"></a>Konfigurera diagnostiken

Konfigurera diagnostik för att registrera data i loggarna ApplicationGatewayAccessLog, ApplicationGatewayPerformanceLog och ApplicationGatewayFirewallLog med [Set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting).

```azurepowershell-interactive
$appgw = Get-AzApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway

$store = Get-AzStorageAccount `
  -ResourceGroupName myResourceGroupAG `
  -Name myagstore1

Set-AzDiagnosticSetting `
  -ResourceId $appgw.Id `
  -StorageAccountId $store.Id `
  -Categories ApplicationGatewayAccessLog, ApplicationGatewayPerformanceLog, ApplicationGatewayFirewallLog `
  -Enabled $true `
  -RetentionEnabled $true `
  -RetentionInDays 30
```

## <a name="test-the-application-gateway"></a>Testa programgatewayen

Du kan använda [Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress) för att få den offentliga IP-adressen för programgatewayen. Kopiera den offentliga IP-adressen och klistra in den i webbläsarens adressfält.

```azurepowershell-interactive
Get-AzPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress
```

![Testa basadressen i programgatewayen](../media/tutorial-restrict-web-traffic-powershell/application-gateway-iistest.png)

## <a name="clean-up-resources"></a>Rensa resurser

När det inte längre behövs tar du bort resursgruppen, programgatewayen och alla relaterade resurser med [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup).

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroupAG
```

## <a name="next-steps"></a>Nästa steg

[Anpassa regler för brandväggen för webbaserade program](application-gateway-customize-waf-rules-portal.md)
