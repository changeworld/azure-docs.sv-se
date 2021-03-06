---
title: Skicka snabbstart för telemetri till Azure IoT Hub (CLI)
description: Den här snabbstarten visar utvecklare som är nya för IoT Hub hur du kommer igång med att använda Azure CLI för att skapa en IoT-hubb, skicka telemetri och visa meddelanden mellan en enhet och navet.
ms.service: iot-hub
ms.topic: quickstart
ms.custom:
- iot-send-telemetry-cli
- iot-p0-scenario
ms.author: timlt
author: timlt
ms.date: 11/06/2019
ms.openlocfilehash: 711e15986265324bbb353fb2b4404cbfeb48dc84
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "78851413"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-and-monitor-it-with-the-azure-cli"></a>Snabbstart: Skicka telemetri från en enhet till en IoT-hubb och övervaka den med Azure CLI

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

IoT Hub är en Azure-tjänst som gör att du kan mata in stora mängder telemetri från IoT-enheter i molnet för lagring eller bearbetning. I den här snabbstarten använder du Azure CLI för att skapa en IoT-hubb och en simulerad enhet, skicka enhetstelemetri till navet och skicka ett meddelande från molnet till enheten. Du använder också Azure-portalen för att visualisera enhetsmått. Detta är ett grundläggande arbetsflöde för utvecklare som använder CLI för att interagera med ett IoT Hub-program.

## <a name="prerequisites"></a>Krav
- Om du inte har en Azure-prenumeration [skapar du en gratis](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.
- Azure CLI. Du kan köra alla kommandon i den här snabbstarten med Azure Cloud Shell, ett interaktivt CLI-skal som körs i din webbläsare. Om du använder Cloud Shell behöver du inte installera något. Om du föredrar att använda CLI lokalt kräver den här snabbstarten Azure CLI version 2.0.76 eller senare. Kör az --version för att se versionen. Information om hur du installerar eller uppgraderar finns i [Installera Azure CLI]( /cli/azure/install-azure-cli).

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal
Logga in på Azure Portal på https://portal.azure.com.

Oavsett om du kör CLI lokalt eller i Cloud Shell, håll portalen öppen i din webbläsare.  Du använder den senare i den här snabbstarten.

## <a name="launch-the-cloud-shell"></a>Starta Cloud Shell
I det här avsnittet startar du en instans av Azure Cloud Shell. Om du använder CLI lokalt går du till avsnittet [Förbered två CLI-sessioner](#prepare-two-cli-sessions).

Så här startar du Cloud Shell:

1. Välj **knappen Cloud Shell** längst upp till höger i Azure-portalen. 

    ![Knappen Molnskal i Azure Portal](media/quickstart-send-telemetry-cli/cloud-shell-button.png)

    > [!NOTE]
    > Om det är första gången du använder Cloud Shell uppmanas du att skapa lagringsutrymme, vilket krävs för att använda Cloud Shell.  Välj en prenumeration för att skapa ett lagringskonto och Microsoft Azure Files-resurs. 

2. Välj önskad CLI-miljö i listrutan **Välj miljö.** Den här snabbstarten använder **Bash-miljön.** Alla följande CLI-kommandon fungerar även i Powershell-miljön. 

    ![Välj CLI-miljö](media/quickstart-send-telemetry-cli/cloud-shell-environment.png)

## <a name="prepare-two-cli-sessions"></a>Förbered två CLI-sessioner

I det här avsnittet förbereder du två Azure CLI-sessioner. Om du använder Cloud Shell kör du de två sessionerna på separata webbläsarflikar. Om du använder en lokal CLI-klient kör du två separata CLI-instanser. Du använder den första sessionen som en simulerad enhet och den andra sessionen för att övervaka och skicka meddelanden. Om du vill köra ett kommando väljer du **Kopiera** om du vill kopiera ett kodblock i den här snabbstarten, klistra in det i skalsessionen och köra det.

Azure CLI kräver att du är inloggad på ditt Azure-konto. All kommunikation mellan din Azure CLI-skalsession och din IoT-hubb autentiseras och krypteras. Därför behöver den här snabbstarten inte ytterligare autentisering som du skulle använda med en riktig enhet, till exempel en anslutningssträng.

*  Kör kommandot [az extension add](https://docs.microsoft.com/cli/azure/extension?view=azure-cli-latest#az-extension-add) för att lägga till Microsoft Azure IoT Extension för Azure CLI i cli-gränssnittet. IOT-tillägget lägger till IoT Hub, IoT Edge och IoT Device Provisioning Service (DPS) till Azure CLI.

   ```azurecli
   az extension add --name azure-iot
   ```
   
   När du har installerat Azure IOT-tillägget behöver du inte installera det igen i någon Cloud Shell-session. 

   [!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

*  Öppna en andra CLI-session.  Om du använder Cloud Shell väljer du **Öppna ny session**. Om du använder CLI lokalt öppnar du en andra instans. 

    >[!div class="mx-imgBorder"]
    >![Öppna den nya Cloud Shell-sessionen](media/quickstart-send-telemetry-cli/cloud-shell-new-session.png)

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub
I det här avsnittet använder du Azure CLI för att skapa en resursgrupp och en IoT Hub.  En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. En IoT Hub fungerar som en central meddelandehubb för dubbelriktad kommunikation mellan ditt IoT-program och enheterna. 

> [!TIP]
> Du kan också skapa en Azure-resursgrupp, en IoT-hubb och andra resurser med hjälp av [Azure-portalen,](iot-hub-create-through-portal.md) [Visual Studio Code](iot-hub-create-use-iot-toolkit.md)eller andra programmatiska metoder.  

1. Kör kommandot [az-grupp skapa](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create) för att skapa en resursgrupp. Följande kommando skapar en resursgrupp med namnet *MyResourceGroup* på *eastus-platsen.* 

    ```azurecli
    az group create --name MyResourceGroup --location eastus
    ```

1. Kör kommandot [az iot hub create](https://docs.microsoft.com/cli/azure/iot/hub?view=azure-cli-latest#az-iot-hub-create) för att skapa en IoT-hubb. Det kan ta några minuter att skapa en IoT-hubb. 

    *YourIotHubName*. Ersätt platshållaren nedan med det namn som du har valt för din IoT-hubb. Ett IoT-hubbnamn måste vara globalt unikt i Azure. Den här platshållaren används i resten av den här snabbstarten för att representera ditt IoT-hubbnamn.

    ```azurecli
    az iot hub create --resource-group MyResourceGroup --name {YourIoTHubName}
    ```

## <a name="create-and-monitor-a-device"></a>Skapa och övervaka en enhet
I det här avsnittet skapar du en simulerad enhet i den första CLI-sessionen. Den simulerade enheten skickar enhetstelemetri till din IoT-hubb. I den andra CLI-sessionen övervakar du händelser och telemetri och skickar ett meddelande från molnet till enheten till den simulerade enheten.

Så här skapar och startar du en simulerad enhet:
1. Kör kommandot [az iot hub device-identity create](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-create) i den första CLI-sessionen. Detta skapar den simulerade enhetsidentiteten. 

    *YourIotHubName*. Ersätt platshållaren nedan med det namn som du har valt för din IoT-hubb. 

    *simDevice*. Du kan använda det här namnet direkt för den simulerade enheten i resten av den här snabbstarten. Du kan också använda ett annat namn. 

    ```azurecli
    az iot hub device-identity create --device-id simDevice --hub-name {YourIoTHubName} 
    ```

1. Kör kommandot [az iot-enhet simulera](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/device?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-device-simulate) i den första CLI-sessionen.  Detta startar den simulerade enheten. Enheten skickar telemetri till din IoT-hubb och tar emot meddelanden från den.  

    *YourIotHubName*. Ersätt platshållaren nedan med det namn som du har valt för din IoT-hubb. 

    ```azurecli
    az iot device simulate -d simDevice -n {YourIoTHubName}
    ```

Så här övervakar du en enhet:
1. I den andra CLI-sessionen kör du kommandot [az iot hub monitor-events.](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-monitor-events) Detta börjar övervaka den simulerade enheten. Utdata visar telemetri som den simulerade enheten skickar till IoT-hubben.

    *YourIotHubName*. Ersätt platshållaren nedan med det namn som du har valt för din IoT-hubb. 

    ```azurecli
    az iot hub monitor-events --output table --hub-name {YourIoTHubName}
    ```

    ![Molnskal övervaka händelser](media/quickstart-send-telemetry-cli/cloud-shell-monitor.png)

1. När du har övervakat den simulerade enheten i den andra CLI-sessionen trycker du på Ctrl+C för att stoppa övervakningen. 

## <a name="use-the-cli-to-send-a-message"></a>Använda CLI för att skicka ett meddelande
I det här avsnittet använder du den andra CLI-sessionen för att skicka ett meddelande till den simulerade enheten.

1. I den första CLI-sessionen bekräftar du att den simulerade enheten körs. Om enheten har stoppats kör du följande kommando för att starta den:

    *YourIotHubName*. Ersätt platshållaren nedan med det namn som du har valt för din IoT-hubb. 

    ```azurecli
    az iot device simulate -d simDevice -n {YourIoTHubName}
    ```

1. I den andra CLI-sessionen kör du kommandot [az iot device c2d-message send.](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/device/c2d-message?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-device-c2d-message-send) Detta skickar ett meddelande från molnet till enheten från din IoT-hubb till den simulerade enheten. Meddelandet innehåller en sträng och två nyckel-värde-par.  

    *YourIotHubName*. Ersätt platshållaren nedan med det namn som du har valt för din IoT-hubb. 

    ```azurecli
    az iot device c2d-message send -d simDevice --data "Hello World" --props "key0=value0;key1=value1" -n {YourIoTHubName}
    ```
    Du kan också skicka meddelanden från molnet till enheten med hjälp av Azure-portalen. Det gör du genom att bläddra till översiktssidan för din IoT-hubb, välja **IoT-enheter,** välja den simulerade enheten och välj **Meddelande till enhet**. 

1. I den första CLI-sessionen bekräftar du att den simulerade enheten tog emot meddelandet. 

    ![Moln Shell-meddelande från molnet till enheten](media/quickstart-send-telemetry-cli/cloud-shell-receive-message.png)

1. När du har visat meddelandet stänger du den andra CLI-sessionen. Håll den första CLI-sessionen öppen. Du använder den för att rensa resurser i ett senare steg.

## <a name="view-messaging-metrics-in-the-portal"></a>Visa meddelandemått i portalen
Med Azure-portalen kan du hantera alla aspekter av din IoT-hubb och dina enheter. I ett typiskt IoT Hub-program som intar telemetri från enheter kanske du vill övervaka enheter eller visa mått på enhetstelemetri. 

Så här visualiserar du meddelandemått i Azure-portalen:
1. Välj **Alla resurser**i den vänstra navigeringsmenyn på portalen . Detta visar alla resurser i din prenumeration, inklusive IoT-hubben som du skapade. 

1. Markera länken på IoT-hubben som du skapade. Portalen visar översiktssidan för navet.

1. Välj **Mått** i den vänstra rutan i IoT Hub. 

    ![Mätvärden för IoT Hub-hubbmeddelanden](media/quickstart-send-telemetry-cli/iot-hub-portal-metrics.png)

1. Ange ditt IoT-hubbnamn i **Scope**.

2. Välj Standardmått för *Iot Hub* i **Metrisknamnområde**.

3. Välj *Totalt antal meddelanden som används* i **Mått**. 

4. Håll muspekaren över det område på tidslinjen där enheten skickade meddelanden. Det totala antalet meddelanden vid en tidpunkt visas i det nedre vänstra hörnet av tidslinjen.

    ![Visa Azure IoT Hub-mått](media/quickstart-send-telemetry-cli/iot-hub-portal-view-metrics.png)

5. Du kan också använda listrutan **Mått för** att visa andra mått på din simulerade enhet. *C2d-meddelandeleveranser har* till exempel slutförts eller *Totalt antal enheter (förhandsgranskning)*. 

## <a name="clean-up-resources"></a>Rensa resurser
Om du inte längre behöver Azure-resurser som skapats i den här snabbstarten kan du använda Azure CLI för att ta bort dem.

Om du fortsätter till nästa rekommenderade artikel kan du behålla de resurser som du redan har skapat och återanvända dem. 

> [!IMPORTANT]
> Att ta bort en resursgrupp kan inte ångras. Resursgruppen och alla resurser som ingår i den tas bort permanent. Kontrollera att du inte av misstag tar bort fel resursgrupp eller resurser. 

Ta bort en resursgrupp med namnet:
1. Kör kommandot [az group delete.](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete) Detta tar bort resursgruppen, IoT Hub och enhetsregistreringen som du skapade.

    ```azurecli
    az group delete --name MyResourceGroup
    ```
1. Kör kommandot [för az-grupplista](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-list) för att bekräfta att resursgruppen tas bort.  

    ```azurecli
    az group list
    ```

## <a name="next-steps"></a>Nästa steg
I den här snabbstarten använde du Azure CLI för att skapa en IoT-hubb, skapa en simulerad enhet, skicka telemetri, övervaka telemetri, skicka ett meddelande från molnet till enheten och rensa resurser. Du använde Azure-portalen för att visualisera meddelandemått på din enhet.

Om du är en enhetsutvecklare är det föreslagna nästa steget att se snabbstarten för telemetri som använder Azure IoT Device SDK för C. Du kan också se en av de tillgängliga snabbstartsartiklarna för Azure IoT Hub-telemetri på önskat språk eller SDK.

> [!div class="nextstepaction"]
> [Snabbstart: Skicka telemetri från en enhet till en IoT-hubb (C)](quickstart-send-telemetry-c.md)
