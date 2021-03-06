---
title: Konfigurera meddelanderoutning för Azure IoT Hub med Azure PowerShell
description: Konfigurera meddelanderoutning för Azure IoT Hub med Azure PowerShell. Beroende på egenskaperna i meddelandet går du till antingen ett lagringskonto eller en Service Bus-kö.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: 68338c56419316e561bb072c1a0555e89d3de85b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "74084443"
---
# <a name="tutorial-use-azure-powershell-to-configure-iot-hub-message-routing"></a>Självstudiekurs: Använda Azure PowerShell för att konfigurera IoT Hub-meddelanderoutning

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [iot-hub-include-routing-create-resources](../../includes/iot-hub-include-routing-create-resources.md)]

## <a name="download-the-script-optional"></a>Ladda ner skriptet (valfritt)

För den andra delen av den här självstudien hämtar och kör du ett Visual Studio-program för att skicka meddelanden till IoT Hub. Det finns en mapp i hämtningen som innehåller Mall- och parameterfilen i Azure Resource Manager samt Azure CLI- och PowerShell-skript. 

Om du vill visa det färdiga skriptet laddar du ned [Azure IoT C# Samples](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip). Packa upp filen master.zip. Azure CLI-skriptet finns i /iot-hub/tutorials/routning/simulateddevice/resources/ som **iothub_routing_psh.ps1**.

## <a name="create-your-resources"></a>Skapa dina resurser

Börja med att skapa resurserna med PowerShell.

### <a name="use-powershell-to-create-your-base-resources"></a>Använda PowerShell för att skapa dina basresurser

Kopiera och klistra in skriptet nedan i Cloud Shell och tryck på Retur. Det kör skriptet en rad i taget. I det här första avsnittet i skriptet skapas basresurserna för den här självstudien, inklusive lagringskontot, IoT Hub, Service Bus Namespace och Service Bus-kön. När du går igenom självstudien, kopiera varje block av skript och klistra in den i Cloud Shell för att köra den.

Det finns flera resursnamn som måste vara globalt unika, till exempel IoT Hub-namnet och lagringskontonamnet. För att göra detta enklare läggs dessa resursnamn till med ett slumpmässigt alfanumeriskt värde som kallas *randomValue*. RandomValue genereras en gång högst upp i skriptet och läggs till resursnamnen efter behov i hela skriptet. Om du inte vill att det ska vara slumpmässigt kan du ställa in det på en tom sträng eller till ett visst värde. 

> [!IMPORTANT]
> Variablerna som anges i det ursprungliga skriptet används också av routningsskriptet, så kör alla skript i samma Cloud Shell-session. Om du öppnar en ny session för att köra skriptet för att ställa in operationsföljden, saknas flera av variablerna värden. 
>

```azurepowershell-interactive
# This command retrieves the subscription id of the current Azure account.
# This field is used when setting up the routing queries.
$subscriptionID = (Get-AzContext).Subscription.Id

# Concatenate this number onto the resources that have to be globally unique.
# You can set this to "" or to a specific value if you don't want it to be random.
# This retrieves the first 6 digits of a random value.
$randomValue = "$(Get-Random)".Substring(0,6)

# Set the values for the resource names that don't have to be globally unique.
$location = "West US"
$resourceGroup = "ContosoResources"
$iotHubConsumerGroup = "ContosoConsumers"
$containerName = "contosoresults"

# Create the resource group to be used 
#   for all resources for this tutorial.
New-AzResourceGroup -Name $resourceGroup -Location $location

# The IoT hub name must be globally unique, 
#   so add a random value to the end.
$iotHubName = "ContosoTestHub" + $randomValue
Write-Host "IoT hub name is " $iotHubName

# Create the IoT hub.
New-AzIotHub -ResourceGroupName $resourceGroup `
    -Name $iotHubName `
    -SkuName "S1" `
    -Location $location `
    -Units 1 

# Add a consumer group to the IoT hub for the 'events' endpoint.
Add-AzIotHubEventHubConsumerGroup -ResourceGroupName $resourceGroup `
  -Name $iotHubName `
  -EventHubConsumerGroupName $iotHubConsumerGroup `
  -EventHubEndpointName "events"

# The storage account name must be globally unique, so add a random value to the end.
$storageAccountName = "contosostorage" + $randomValue
Write-Host "storage account name is " $storageAccountName

# Create the storage account to be used as a routing destination.
# Save the context for the storage account 
#   to be used when creating a container.
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
    -Name $storageAccountName `
    -Location $location `
    -SkuName Standard_LRS `
    -Kind Storage
# Retrieve the connection string from the context. 
$storageConnectionString = $storageAccount.Context.ConnectionString
Write-Host "storage connection string = " $storageConnectionString 

# Create the container in the storage account.
New-AzStorageContainer -Name $containerName `
    -Context $storageAccount.Context

# The Service Bus namespace must be globally unique,
#   so add a random value to the end.
$serviceBusNamespace = "ContosoSBNamespace" + $randomValue
Write-Host "Service Bus namespace is " $serviceBusNamespace

# Create the Service Bus namespace.
New-AzServiceBusNamespace -ResourceGroupName $resourceGroup `
    -Location $location `
    -Name $serviceBusNamespace 

# The Service Bus queue name must be globally unique,
#  so add a random value to the end.
$serviceBusQueueName  = "ContosoSBQueue" + $randomValue
Write-Host "Service Bus queue name is " $serviceBusQueueName 

# Create the Service Bus queue to be used as a routing destination.
New-AzServiceBusQueue -ResourceGroupName $resourceGroup `
    -Namespace $serviceBusNamespace `
    -Name $serviceBusQueueName  `
    -EnablePartitioning $False 
```

### <a name="create-a-simulated-device"></a>Skapa en simulerad enhet

[!INCLUDE [iot-hub-include-create-simulated-device-portal](../../includes/iot-hub-include-create-simulated-device-portal.md)]

Nu när basresurserna har ställts in kan du konfigurera meddelanderoutningen.

## <a name="set-up-message-routing"></a>Konfigurera meddelanderedigering

[!INCLUDE [iot-hub-include-create-routing-description](../../includes/iot-hub-include-create-routing-description.md)]

Om du vill skapa en routningsslutpunkt använder du [Add-AzIotHubRoutingEndpoint](/powershell/module/az.iothub/Add-AzIotHubRoutingEndpoint). Om du vill skapa meddelandevägen för slutpunkten använder du [Add-AzIotHubRoute](/powershell/module/az.iothub/Add-AzIoTHubRoute).

### <a name="route-to-a-storage-account"></a>Vägen till ett lagringskonto 

Ställ först in slutpunkten för lagringskontot och skapa sedan meddelandevägen.

[!INCLUDE [iot-hub-include-blob-storage-format](../../includes/iot-hub-include-blob-storage-format.md)]

Det här är de variabler som används av skriptet som måste anges i cloud shell-sessionen:

**resourceGroup**: Det finns två förekomster av det här fältet - ange dem båda till din resursgrupp.

**namn**: Det här fältet är namnet på den IoT-hubb som operationsföljden ska tillämpas på.

**slutpunktNamn**: Det här fältet är namnet som identifierar slutpunkten. 

**endpointType**: Det här fältet är typen av slutpunkt. Det här värdet `azurestoragecontainer`måste `eventhub` `servicebusqueue`anges `servicebustopic`till , , eller . För dina syften här, `azurestoragecontainer`ställ in den på .

**subscriptionID**: Det här fältet är inställt på subscriptionID för ditt Azure-konto.

**storageConnectionString**: Det här värdet hämtas från lagringskontot som ställts in i föregående skript. Den används av routningen för att komma åt lagringskontot.

**containerName**: Det här fältet är namnet på behållaren i lagringskontot som data ska skrivas till.

**Kodning**: Ställ in det `AVRO` `JSON`här fältet på antingen eller . Detta anger formatet på de lagrade data. Standard är AVRO.

**routeName**: Det här fältet är namnet på den väg som du ställer in. 

**villkor:** Det här fältet är den fråga som används för att filtrera för de meddelanden som skickas till den här slutpunkten. Frågevillkoret för de meddelanden som dirigeras till lagring är `level="storage"`.

**aktiverad**: Det här `true`fältet är som standard , vilket anger att meddelandevägen ska aktiveras efter att ha skapats.

Kopiera skriptet och klistra in det i cloud shell-fönstret.

```powershell
##### ROUTING FOR STORAGE #####

$endpointName = "ContosoStorageEndpoint"
$endpointType = "azurestoragecontainer"
$routeName = "ContosoStorageRoute"
$condition = 'level="storage"'
```

Nästa steg är att skapa routningslutpunkten för lagringskontot. Du anger också behållaren där resultaten ska lagras. Behållaren skapades när lagringskontot skapades.

```powershell
# Create the routing endpoint for storage.
# Specify 'AVRO' or 'JSON' for the encoding of the data.
Add-AzIotHubRoutingEndpoint `
  -ResourceGroupName $resourceGroup `
  -Name $iotHubName `
  -EndpointName $endpointName `
  -EndpointType $endpointType `
  -EndpointResourceGroup $resourceGroup `
  -EndpointSubscriptionId $subscriptionId `
  -ConnectionString $storageConnectionString  `
  -ContainerName $containerName `
  -Encoding AVRO
```

Skapa sedan meddelandevägen för lagringsslutpunkten. Meddelandevägen anger var de meddelanden som uppfyller frågespecifikationen ska skickas.

```powershell
# Create the route for the storage endpoint.
Add-AzIotHubRoute `
   -ResourceGroupName $resourceGroup `
   -Name $iotHubName `
   -RouteName $routeName `
   -Source DeviceMessages `
   -EndpointName $endpointName `
   -Condition $condition `
   -Enabled 
```

### <a name="route-to-a-service-bus-queue"></a>Dirigera till en servicebusskö

Konfigurera nu routning för Service Bus-kön. Om du vill hämta anslutningssträngen för servicebusskön måste du skapa en auktoriseringsregel som har rätt rättigheter definierade. Följande skript skapar en auktoriseringsregel `sbauthrule`för den tjänstbusskö som anropas och anger rättigheterna till `Listen Manage Send`. När den här auktoriseringsregeln har konfigurerats kan du använda den för att hämta anslutningssträngen för kön.

```powershell
##### ROUTING FOR SERVICE BUS QUEUE #####

# Create the authorization rule for the Service Bus queue.
New-AzServiceBusAuthorizationRule `
  -ResourceGroupName $resourceGroup `
  -NamespaceName $serviceBusNamespace `
  -Queue $serviceBusQueueName `
  -Name "sbauthrule" `
  -Rights @("Manage","Listen","Send")
```

Använd nu auktoriseringsregeln för att hämta könyckeln Service Bus. Den här auktoriseringsregeln används för att hämta anslutningssträngen senare i skriptet.

```powershell
$sbqkey = Get-AzServiceBusKey `
    -ResourceGroupName $resourceGroup `
    -NamespaceName $serviceBusNamespace `
    -Queue $servicebusQueueName `
    -Name "sbauthrule"
```

Ställ nu in routningsslutpunkten och meddelandevägen för servicebusskön. Det här är de variabler som används av skriptet som måste anges i cloud shell-sessionen:

**slutpunktNamn**: Det här fältet är namnet som identifierar slutpunkten. 

**endpointType**: Det här fältet är typen av slutpunkt. Det här värdet `azurestoragecontainer`måste `eventhub` `servicebusqueue`anges `servicebustopic`till , , eller . För dina syften här, `servicebusqueue`ställ in den på .

**routeName**: Det här fältet är namnet på den väg som du ställer in. 

**villkor:** Det här fältet är den fråga som används för att filtrera för de meddelanden som skickas till den här slutpunkten. Frågevillkoret för de meddelanden som dirigeras till `level="critical"`servicebusskön är .

Här är Azure PowerShell för meddelanderoutningen för servicebusskön.

```powershell
$endpointName = "ContosoSBQueueEndpoint"
$endpointType = "servicebusqueue"
$routeName = "ContosoSBQueueRoute"
$condition = 'level="critical"'

# Add the routing endpoint, using the connection string property from the key.
Add-AzIotHubRoutingEndpoint `
  -ResourceGroupName $resourceGroup `
  -Name $iotHubName `
  -EndpointName $endpointName `
  -EndpointType $endpointType `
  -EndpointResourceGroup $resourceGroup `
  -EndpointSubscriptionId $subscriptionId `
  -ConnectionString $sbqkey.PrimaryConnectionString

# Set up the message route for the Service Bus queue endpoint.
Add-AzIotHubRoute `
   -ResourceGroupName $resourceGroup `
   -Name $iotHubName `
   -RouteName $routeName `
   -Source DeviceMessages `
   -EndpointName $endpointName `
   -Condition $condition `
   -Enabled 
```

### <a name="view-message-routing-in-the-portal"></a>Visa meddelanderoutning i portalen

[!INCLUDE [iot-hub-include-view-routing-in-portal](../../includes/iot-hub-include-view-routing-in-portal.md)]

## <a name="next-steps"></a>Nästa steg

Nu när du har konfigurerat resurserna och meddelandevägarna konfigurerade går du vidare till nästa självstudiekurs för att lära dig hur du skickar meddelanden till IoT-hubben och ser dem dirigeras till de olika destinationerna. 

> [!div class="nextstepaction"]
> [Del 2 - Visa resultatet för meddelanderoutning](tutorial-routing-view-message-routing-results.md)