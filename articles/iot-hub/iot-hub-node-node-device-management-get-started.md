---
title: Komma igång med Azure IoT Hub-enhetshantering (nod) | Microsoft-dokument
description: Så här använder du IoT Hub-enhetshantering för att initiera en omstart av en fjärrenhet. Du använder Azure IoT SDK för Node.js för att implementera en simulerad enhetsapp som innehåller en direkt metod och en tjänstapp som anropar den direkta metoden.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/20/2019
ms.openlocfilehash: 5b2e4c03347020b5d5fc67927165403f06854e0b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77110912"
---
# <a name="get-started-with-device-management-nodejs"></a>Komma igång med enhetshantering (Node.js)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

I den här självstudiekursen lär du dig att:

* Använd [Azure-portalen](https://portal.azure.com) för att skapa en IoT-hubb och skapa en enhetsidentitet i din IoT-hubb.

* Skapa en simulerad enhetsapp som innehåller en direkt metod som startar om enheten. Direkta metoder anropas från molnet.

* Skapa en Node.js-konsolapp som anropar metoden starta om direkt i den simulerade enhetsappen via IoT-hubben.

I slutet av den här självstudien har du två Node.js-konsolappar:

* **dmpatterns_getstarted_device.js**, som ansluter till din IoT-hubb med enhetsidentiteten som skapats tidigare, tar emot en omstart direkt metod, simulerar en fysisk omstart och rapporterar tiden för den senaste omstarten.

* **dmpatterns_getstarted_service.js**, som anropar en direkt metod i den simulerade enhetsappen, visar svaret och visar de uppdaterade rapporterade egenskaperna.

## <a name="prerequisites"></a>Krav

* Node.js version 10.0.x eller senare. [Förbered utvecklingsmiljön](https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md) beskriver hur du installerar Node.js för den här självstudien på antingen Windows eller Linux.

* Ett aktivt Azure-konto. (Om du inte har ett konto kan du skapa ett [kostnadsfritt konto på](https://azure.microsoft.com/pricing/free-trial/) bara några minuter.)

* Kontrollera att port 8883 är öppen i brandväggen. Enhetsexemplet i den här artikeln använder MQTT-protokollet, som kommunicerar över port 8883. Den här porten kan vara blockerad i vissa företags- och utbildningsnätverksmiljöer. Mer information och sätt att lösa problemet finns i [Ansluta till IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrera en ny enhet i IoT-hubben

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>Skapa en simulerad enhetsapp

I det här avsnittet får du:

* Skapa en Node.js-konsolapp som svarar på en direkt metod som anropas via molnet

* Utlösa en simulerad omstart av enheten

* Använd de rapporterade egenskaperna för att aktivera enhetstvillingfrågor för att identifiera enheter och när de senast startades om

1. Skapa en tom mapp med namnet **manageddevice**.  I mappen **manageddevice** skapar du en package.json-fil med hjälp av följande kommando i Kommandotolken.  Acceptera alla standardvärden:

    ```cmd/sh
    npm init
    ```

2. Kör följande kommando i kommandotolken i mappen **manageddevice** för att installera **Azure-iot-device** Device SDK-paketet och **azure-iot-device-mqtt-paketet:**

    ```cmd/sh
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

3. Skapa en **dmpatterns_getstarted_device.js-fil** i mappen **manageddevice** med hjälp av en textredigerare.

4. Lägg till följande "kräv"-satser i början av **filen dmpatterns_getstarted_device.js:**

    ```javascript
    'use strict';

    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```

5. Lägg till en **connectionString**-variabel och använd den för att skapa en **klientinstans**.  Ersätt `{yourdeviceconnectionstring}` platshållarvärdet med den enhetsanslutningssträng som du kopierade tidigare i [Registrera en ny enhet i IoT-hubben](#register-a-new-device-in-the-iot-hub).  

    ```javascript
    var connectionString = '{yourdeviceconnectionstring}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```

6. Lägg till följande funktion för att implementera den direkta metoden på enheten

    ```javascript
    var onReboot = function(request, response) {

        // Respond the cloud app for the direct method
        response.send(200, 'Reboot started', function(err) {
            if (err) {
                console.error('An error occurred when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.');
            }
        });

        // Report the reboot before the physical restart
        var date = new Date();
        var patch = {
            iothubDM : {
                reboot : {
                    lastReboot : date.toISOString(),
                }
            }
        };

        // Get device Twin
        client.getTwin(function(err, twin) {
            if (err) {
                console.error('could not get twin');
            } else {
                console.log('twin acquired');
                twin.properties.reported.update(patch, function(err) {
                    if (err) throw err;
                    console.log('Device reboot twin state reported')
                });  
            }
        });

        // Add your device's reboot API for physical restart.
        console.log('Rebooting!');
    };
    ```

7. Öppna anslutningen till IoT-hubben och starta direktmetodlyssnaren:

    ```javascript
    client.open(function(err) {
        if (err) {
            console.error('Could not open IotHub client');
        }  else {
            console.log('Client opened.  Waiting for reboot method.');
            client.onDeviceMethod('reboot', onReboot);
        }
    });
    ```

8. Spara och stäng **filen dmpatterns_getstarted_device.js.**

> [!NOTE]
> För att göra det så enkelt som möjligt implementerar vi ingen princip för omförsök i den här självstudiekursen. I produktionskoden bör du implementera principer för återförsök (till exempel en exponentiell backoff), som föreslås i artikeln [Transient Fault Handling](/azure/architecture/best-practices/transient-faults).

## <a name="get-the-iot-hub-connection-string"></a>Hämta anslutningssträngen för IoT-hubb

[!INCLUDE [iot-hub-howto-device-management-shared-access-policy-text](../../includes/iot-hub-howto-device-management-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Utlösa en fjärromstart på enheten med en direkt metod

I det här avsnittet skapar du en Node.js-konsolapp som initierar en fjärromstart på en enhet med en direkt metod. Appen använder enhetstvillingfrågor för att identifiera den senaste omstartstiden för den enheten.

1. Skapa en tom mapp med namnet **triggerrebootondevice**. Skapa en package.json-fil med följande kommando i kommandotolken i mappen **triggerrebootondevice.** Acceptera alla standardvärden:

    ```cmd/sh
    npm init
    ```

2. Kör följande kommando i kommandotolken i mappen **triggerrebootondevice** för att installera **azure-iothub** Device SDK-paketet och **azure-iot-device-mqtt-paketet:**

    ```cmd/sh
    npm install azure-iothub --save
    ```

3. Skapa en **dmpatterns_getstarted_service.js-fil** i mappen **triggerrebootondevice** med hjälp av en textredigerare.

4. Lägg till följande "kräv"-satser i början av **filen dmpatterns_getstarted_service.js:**

    ```javascript
    'use strict';

    var Registry = require('azure-iothub').Registry;
    var Client = require('azure-iothub').Client;
    ```

5. Lägg till följande variabeldeklarationer `{iothubconnectionstring}` och ersätt platshållarvärdet med anslutningssträngen för IoT-hubb som du kopierade tidigare i [Hämta anslutningssträngen för IoT-hubben:](#get-the-iot-hub-connection-string)

    ```javascript
    var connectionString = '{iothubconnectionstring}';
    var registry = Registry.fromConnectionString(connectionString);
    var client = Client.fromConnectionString(connectionString);
    var deviceToReboot = 'myDeviceId';
    ```

6. Lägg till följande funktion för att anropa enhetsmetoden för att starta om målenheten:

    ```javascript
    var startRebootDevice = function(twin) {

        var methodName = "reboot";

        var methodParams = {
            methodName: methodName,
            payload: null,
            timeoutInSeconds: 30
        };

        client.invokeDeviceMethod(deviceToReboot, methodParams, function(err, result) {
            if (err) {
                console.error("Direct method error: "+err.message);
            } else {
                console.log("Successfully invoked the device to reboot.");  
            }
        });
    };
    ```

7. Lägg till följande funktion för att fråga efter enheten och få den senaste omstartstiden:

    ```javascript
    var queryTwinLastReboot = function() {

        registry.getTwin(deviceToReboot, function(err, twin){

            if (twin.properties.reported.iothubDM != null)
            {
                if (err) {
                    console.error('Could not query twins: ' + err.constructor.name + ': ' + err.message);
                } else {
                    var lastRebootTime = twin.properties.reported.iothubDM.reboot.lastReboot;
                    console.log('Last reboot time: ' + JSON.stringify(lastRebootTime, null, 2));
                }
            } else 
                console.log('Waiting for device to report last reboot time.');
        });
    };
    ```

8. Lägg till följande kod för att anropa de funktioner som utlöser omstarten direkt metod och fråga för den senaste omstarten tid:

    ```javascript
    startRebootDevice();
    setInterval(queryTwinLastReboot, 2000);
    ```

9. Spara och stäng **filen dmpatterns_getstarted_service.js.**

## <a name="run-the-apps"></a>Kör apparna

Du är nu redo att köra apparna.

1. Kör följande kommando i kommandotolken i mappen **manageddevice** för att börja lyssna efter metoden starta om direkt.

    ```cmd/sh
    node dmpatterns_getstarted_device.js
    ```

2. Vid kommandotolken i mappen **triggerrebootondevice** kör du följande kommando för att utlösa fjärrstarten och fråga efter enhetstvillingen för att hitta den senaste omstartstiden.

    ```cmd/sh
    node dmpatterns_getstarted_service.js
    ```

3. Du ser enhetens svar på metoden reboot direct och omstartsstatusen i konsolen.

   Följande visar enhetens svar på den omstartssvar som skickas av tjänsten:

   ![manageddevice app utgång](./media/iot-hub-node-node-device-management-get-started/device.png)

   Följande visar tjänsten som utlöser omstarten och avsökning av enhetstvillingen för den senaste omstartstiden:

   ![triggerrebootondevice app utgång](./media/iot-hub-node-node-device-management-get-started/service.png)

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]
