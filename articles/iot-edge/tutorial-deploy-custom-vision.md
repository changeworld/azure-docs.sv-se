---
title: Självstudiekurs - Distribuera klassificerare med anpassade visuella enheter till en enhet med Azure IoT Edge
description: I den här självstudien lär du dig att skapa en modell för visuellt innehåll som körs som en container med hjälp av Custom Vision och IoT Edge.
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/15/2020
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 07350ffe4a57bfe4a79bfce5d821b51535867935
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "76167001"
---
# <a name="tutorial-perform-image-classification-at-the-edge-with-custom-vision-service"></a>Självstudie: Utföra bildklassificering på gränsen med Custom Vision Service

Azure IoT Edge kan göra din IoT-lösning effektivare genom att flytta arbetsbelastningar från molnet till gränsen. Den här funktionen lämpar sig väl för tjänster som bearbetar stora mängder data, till exempel modeller för visuellt innehåll. Med [Custom Vision Service](../cognitive-services/custom-vision-service/home.md) kan du skapa anpassade bildklassificerare och distribuera dem till enheter såsom containrar. Tillsammans gör de här två tjänsterna att du kan få insikter från bilder eller videoströmmar utan att först behöva överföra alla data till annan plats. Custom Vision tillhandahåller en klassificerare som jämför en bild mot en tränad modell att generera insikter.

Till exempel kan Custom Vision på en IoT Edge-enhet avgöra om det förekommer mer eller mindre trafik än normalt på en motorväg eller huruvida det finns lediga platser på en viss sträcka i ett parkeringshus. Dessa insikter kan delas med en annan tjänst i åtgärdssyfte.

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
>
> * Skapa en bildklassificerare med Custom Vision.
> * Utveckla en IoT Edge-modul som kör frågor mot Custom Vision-webbservern på din enhet.
> * Skicka resultaten från bildklassificeraren till IoT Hub.

<center>

![Diagram - Självstudiearkitektur, scen- och distributionsklassificerare](./media/tutorial-deploy-custom-vision/custom-vision-architecture.png)
</center>

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Krav

>[!TIP]
>Den här självstudien är en förenklad version av [Custom Vision och Azure IoT Edge i ett exempelprojekt för Raspberry Pi 3.](https://github.com/Azure-Samples/Custom-vision-service-iot-edge-raspberry-pi) Den här självstudien har utformats för att köras på en virtuell molnd-dator och använder statiska avbildningsbilder för att träna och testa bildklassificeraren, vilket är användbart för någon som precis har börjat utvärdera Anpassad syn på IoT Edge. Exempelprojektet använder fysisk maskinvara och ställer in en livekamerafeed för att träna och testa bildklassificeraren, vilket är användbart för någon som vill prova ett mer detaljerat scenario i verkliga livet.

Innan du påbörjar den här självstudien bör du ha gått igenom den tidigare självstudien för att ställa in din miljö för Linux-containerutveckling: [Utveckla IoT Edge-moduler för Linux-enheter](tutorial-develop-for-linux.md). Genom att fylla i den självstudien bör du ha följande förutsättningar på plats:

* En [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) på kostnadsfri nivå eller standardnivå i Azure.
* En [Linux-enhet som kör Azure IoT Edge](quickstart-linux.md)
* Ett behållarregister, till exempel [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/).
* [Visual Studio-kod](https://code.visualstudio.com/) som konfigurerats med [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* [Docker CE](https://docs.docker.com/install/) konfigurerad för att köra Linux-behållare.

Om du vill utveckla en IoT Edge-modul med tjänsten Custom Vision installerar du följande ytterligare förutsättningar på din utvecklingsmaskin:

* [Python](https://www.python.org/downloads/)
* [Git](https://git-scm.com/downloads)
* [Python-tillägg för Visual Studio-kod](https://marketplace.visualstudio.com/items?itemName=ms-python.python)

## <a name="build-an-image-classifier-with-custom-vision"></a>Skapa en bildklassificerare med Custom Vision

För att skapa en bildklassificerare måste du skapa ett Custom Vision-projekt och förse träningsbilder. Mer information om de steg som du vidtar i det här avsnittet finns på sidan om [hur du skapar en klassificerare med Custom Vision](../cognitive-services/custom-vision-service/getting-started-build-a-classifier.md).

När din bildklassificerare har skapats och tränats kan du exportera den som en Docker-container och distribuera den till en IoT Edge-enhet.

### <a name="create-a-new-project"></a>Skapa ett nytt projekt

1. I din webbläsare navigerar du till [Custom Vision-webbplatsen](https://customvision.ai/).

2. Välj **Logga in** och logga in med samma konto som du använder för att få åtkomst till Azure-resurser.

3. Välj **Nytt projekt**.

4. Skapa ditt projekt med följande värden:

   | Field | Värde |
   | ----- | ----- |
   | Namn | Ange ett namn för projektet, till exempel **EdgeTreeClassifier**. |
   | Beskrivning | Valfri projektbeskrivning. |
   | Resurs | Välj en av dina Azure-resursgrupper som innehåller en Custom Vision Service-resurs eller **skapa nya** om du ännu inte har lagt till en. |
   | Projekttyper | **Klassificering** |
   | Klassificeringstyper | **Multiklass (enskild tagg per bild)** |
   | Domäner | **Allmän (kompakt)** |
   | Exportera funktioner | **Grundläggande plattformar (Tensorflow, CoreML, ONNX, ...)** |

5. Välj **Skapa projekt**.

### <a name="upload-images-and-train-your-classifier"></a>Ladda upp bilder och träna klassificeraren

För att skapa en bildklassificerare krävs en uppsättning träningsbilder samt testbilder.

1. Klona eller ladda ned exempelbilder från lagringsplatsen [Cognitive-CustomVision-Windows](https://github.com/Microsoft/Cognitive-CustomVision-Windows) till din lokala utvecklingsdator.

   ```cmd/sh
   git clone https://github.com/Microsoft/Cognitive-CustomVision-Windows.git
   ```

2. Återgå till Custom Vision-projektet och välj **Lägg till bilder**.

3. Bläddra till den git-lagringsplats som du klonade lokalt och navigera till den första bildmappen, **Cognitive-CustomVision-Windows/Samples/Images/Hemlock**. Välj alla 10 bilder i mappen och sedan **Öppna**.

4. Lägg till taggen **hemlock** (hemlockgran) till den här gruppen med bilder och tryck på **Retur** för att tillämpa taggen.

5. Välj **Ladda upp 10 filer**.

   ![Ladda filer taggade med hemlockgran till Custom Vision](./media/tutorial-deploy-custom-vision/upload-hemlock.png)

6. När bilderna har laddats upp väljer du **Klar**.

7. Välj **Lägg till bilder ** igen.

8. Bläddra till den andra bildmappen, **Cognitive-CustomVision-Windows/Samples/Images/Japanese Cherry**. Välj alla 10 bilder i mappen och sedan **Öppna**.

9. Lägg till taggen **japanese cherry** (japanskt körsbärsträd) till den här gruppen med bilder och tryck på **Retur** för att tillämpa taggen.

10. Välj **Ladda upp 10 filer**. När bilderna har laddats upp väljer du **Klar**.

11. När båda uppsättningarna med bilder har taggats och laddats upp väljer du **Träna** för att träna klassificeraren.

### <a name="export-your-classifier"></a>Exportera klassificeraren

1. När du har tränat klassificeraren väljer du **Exportera** på sidan Prestanda för klassificeraren.

   ![Exportera din tränade bildklassificerare](./media/tutorial-deploy-custom-vision/export.png)

2. Välj **DockerFile** som plattform. 

3. Välj **Linux** som version.  

4. Välj **Exportera**. 

   ![Exportera som DockerFile med Linux-containrar](./media/tutorial-deploy-custom-vision/export-2.png)

5. När exporten är klar väljer du **Ladda ned** och sparar .zip-paketet lokalt på datorn. Extrahera alla filer från paketet. Du använder de här filerna för att skapa en IoT Edge-modul som innehåller bildklassificeringsservern. 

När du nått hit är du klar med att skapa och träna Custom Vision-projektet. Du använder de exporterade filerna i nästa avsnitt, men du är klar med Custom Vision-webbplatsen. 

## <a name="create-an-iot-edge-solution"></a>Skapa en IoT Edge-lösning

Nu har du filerna för en containerversion av bildklassificeraren på din utvecklingsdator. I det här avsnittet konfigurerar du bildklassificerarens container till att köras som IoT Edge-modul. Du kan även skapa en andra modul som distribueras tillsammans med bildklassificeraren. Den andra modulen skickar begäranden till klassificeraren och skickar resultatet som meddelanden till IoT Hub. 

### <a name="create-a-new-solution"></a>Skapa en ny lösning

En lösning är ett logiskt sätt att utveckla och organisera flera moduler för en enskild IoT Edge-distribution. En lösning innehåller kod för en eller flera moduler samt det distributionsmanifest som deklarerar hur de ska konfigureras på en IoT Edge-enhet. 

1. Välj **Visa** > **kommandopalett** för att öppna kommandopaletten VS-kod. 

1. Skriv och kör kommandot **Azure IoT Edge: New IoT Edge solution** (Ny IoT Edge-lösning) på kommandopaletten. Ange följande information i kommandopaletten för att skapa din lösning: 

   | Field | Värde |
   | ----- | ----- |
   | Välj mapp | Välj den plats på utvecklingsdatorn där Visual Studio Code ska skapa lösningsfilerna. |
   | Ange ett namn på lösningen | Ange ett beskrivande namn för lösningen, till exempel **CustomVisionSolution**, eller acceptera standardnamnet. |
   | Välj modulmall | Välj **Python-modul**. |
   | Ange ett modulnamn | Ge modulen namnet **classifier** (klassificerare).<br><br>Det är viktigt att modulnamnet endast innehåller gemener. IoT Edge är skiftlägeskänsligt när det gäller referenser till moduler, och den här lösningen använder ett bibliotek som formaterar alla begäranden som gemener. |
   | Ange Docker-bildlagringsplats för modulen | En bildlagringsplats innehåller namnet på containerregistret och namnet på containeravbildningen. Containeravbildningen har fyllts i från föregående steg. Ersätt **localhost:5000** med värdet för inloggningsservern från ditt Azure-containerregister. Du kan hämta inloggningsservern från sidan Översikt för ditt containerregister på Azure-portalen.<br><br>Den sista strängen ser ut som ** \<\>registernamnet .azurecr.io/classifier**. |
 
   ![Ange lagringsplatsen för Docker-avbildningen](./media/tutorial-deploy-custom-vision/repository.png)

Visual Studio Code-fönstret läser in arbetsytan för IoT Edge-lösningen.

### <a name="add-your-registry-credentials"></a>Lägg till autentiseringsuppgifter för registret

Miljöfilen lagrar autentiseringsuppgifterna för containerregistret och delar dem med körningsmiljön för IoT Edge. Körningen behöver dessa autentiseringsuppgifter för att hämta dina privata avbildningar till IoT Edge-enheten.

1. Öppna .env-filen i VS Code-utforskaren.
2. Uppdatera fälten med det **användarnamn** och **lösenord** som du kopierade från Azure Container-registret.
3. Spara filen.

### <a name="select-your-target-architecture"></a>Välj målarkitektur

För närvarande kan Visual Studio Code utveckla moduler för Linux AMD64 och Linux ARM32v7-enheter. Du måste välja vilken arkitektur du riktar dig till med varje lösning, eftersom behållaren är byggd och körs på olika sätt för varje arkitekturtyp. Standard är Linux AMD64, vilket är vad vi ska använda för den här guiden. 

1. Öppna kommandopaletten och sök efter **Azure IoT Edge: Ange standardmålplattform för edge-lösning**eller välj genvägsikonen i sidofältet längst ned i fönstret. 

2. I kommandopaletten väljer du målarkitekturen i listan med alternativ. För den här guiden använder vi en Ubuntu virtuell maskin som IoT Edge-enhet, så kommer att behålla standard **amd64**. 

### <a name="add-your-image-classifier"></a>Lägga till bildklassificeraren

Python-modulmallen i Visual Studio Code innehåller exempelkod som du kan köra för att testa IoT Edge. Du använder inte den koden i det här scenariot. Använd i stället stegen i det här avsnittet för att ersätta exempelkoden med den bildklassificerarcontainer som du exporterade tidigare. 

1. I Utforskaren bläddrar du till det Custom Vision-paket som du laddade ned och extraherade. Kopiera allt innehåll från det extraherade paketet. Det bör vara två mappar, **app** och **azureml**, och två filer, **Dockerfile** och **README**. 

2. I Utforskaren bläddrar du till den katalog där du angav att Visual Studio Code skulle skapa IoT Edge-lösningen. 

3. Öppna mappen för klassificerarmodulen. Om du använde de föreslagna namnen i föregående avsnitt ser mappstrukturen ut så här: **CustomVisionSolution/modules/classifier**. 

4. Klistra in filerna i mappen **classifier**. 

5. Återgå till Visual Studio Code-fönstret. Lösningens arbetsyta bör nu visa bildklassificerarfilerna i modulmappen. 

   ![Lösningens arbetsyta med bildklassificerarfiler](./media/tutorial-deploy-custom-vision/workspace.png)

6. Öppna filen **module.json** i klassificerarens mapp. 

7. Uppdatera **parametern plattformar** för att peka på den nya Dockerfile som du har lagt till och ta bort alla alternativ förutom AMD64, som är den enda arkitekturen vi använder för den här självstudien. 

   ```json
   "platforms": {
       "amd64": "./Dockerfile"
   }
   ```

8. Spara ändringarna. 

### <a name="create-a-simulated-camera-module"></a>Skapa en modul med en simulerad kamera

I en verklig Custom Vision-distribution skulle du har en kamera som tillhandahåller livebilder eller videoströmmar. För det här scenariot simulerar du kameran genom att skapa en modul som skickar en testbild till bildklassificeraren. 

#### <a name="add-and-configure-a-new-module"></a>Lägga till och konfigurera en ny modul

I det här avsnittet lägger du till en ny modul i samma CustomVisionSolution och anger kod för att skapa den simulerade kameran. 

1. I samma Visual Studio Code-fönster använder du kommandopaletten för att köra **Azure IoT Edge: Lägg till IoT Edge-modul**. I kommandopaletten anger du följande information för den nya modulen: 

   | Uppmaning | Värde | 
   | ------ | ----- |
   | Välj distributionsmallfil | Välj filen deployment.template.json i mappen CustomVisionSolution. |
   | Välj modulmall | Välj **Python-modul** |
   | Ange ett modulnamn | Ge modulen namnet **cameraCapture** |
   | Ange Docker-bildlagringsplats för modulen | Ersätt **localhost:5000** med värdet för inloggningsservern för ditt Azure-containerregister.<br><br>Den sista strängen ser ut som ** \<\>registernamn .azurecr.io/cameracapture**. |

   VS Code-fönstret läser in den nya modulen på lösningens arbetsyta och uppdaterar filen deployment.template.json. Nu bör du se två modulmappar: classifier och cameraCapture. 

2. Öppna filen **main.py** fil i mappen **modules** / **cameraCapture**. 

3. Ersätt hela filen med följande kod. Den här exempelkoden skickar POST-begäranden till den bildbearbetningstjänst som körs i klassificerarmodulen. Vi förser den här modulcontainern med en exempelbild som ska användas i begärandena. Sedan paketerar den svaret som IoT Hub-meddelande och skickar det till en utdatakö.  

    ```python
    # Copyright (c) Microsoft. All rights reserved.
    # Licensed under the MIT license. See LICENSE file in the project root for
    # full license information.

    import time
    import sys
    import os
    import requests
    import json
    from azure.iot.device import IoTHubModuleClient, Message

    # global counters
    SENT_IMAGES = 0

    # global client
    CLIENT = None

    # Send a message to IoT Hub
    # Route output1 to $upstream in deployment.template.json
    def send_to_hub(strMessage):
        message = Message(bytearray(strMessage, 'utf8'))
        CLIENT.send_message_to_output(message, "output1")
        global SENT_IMAGES
        SENT_IMAGES += 1
        print( "Total images sent: {}".format(SENT_IMAGES) )

    # Send an image to the image classifying server
    # Return the JSON response from the server with the prediction result
    def sendFrameForProcessing(imagePath, imageProcessingEndpoint):
        headers = {'Content-Type': 'application/octet-stream'}

        with open(imagePath, mode="rb") as test_image:
            try:
                response = requests.post(imageProcessingEndpoint, headers = headers, data = test_image)
                print("Response from classification service: (" + str(response.status_code) + ") " + json.dumps(response.json()) + "\n")
            except Exception as e:
                print(e)
                print("No response from classification service")
                return None

        return json.dumps(response.json())

    def main(imagePath, imageProcessingEndpoint):
        try:
            print ( "Simulated camera module for Azure IoT Edge. Press Ctrl-C to exit." )

            try:
                global CLIENT
                CLIENT = IoTHubModuleClient.create_from_edge_environment()
            except Exception as iothub_error:
                print ( "Unexpected error {} from IoTHub".format(iothub_error) )
                return

            print ( "The sample is now sending images for processing and will indefinitely.")

            while True:
                classification = sendFrameForProcessing(imagePath, imageProcessingEndpoint)
                if classification:
                    send_to_hub(classification)
                time.sleep(10)

        except KeyboardInterrupt:
            print ( "IoT Edge module sample stopped" )

    if __name__ == '__main__':
        try:
            # Retrieve the image location and image classifying server endpoint from container environment
            IMAGE_PATH = os.getenv('IMAGE_PATH', "")
            IMAGE_PROCESSING_ENDPOINT = os.getenv('IMAGE_PROCESSING_ENDPOINT', "")
        except ValueError as error:
            print ( error )
            sys.exit(1)

        if ((IMAGE_PATH and IMAGE_PROCESSING_ENDPOINT) != ""):
            main(IMAGE_PATH, IMAGE_PROCESSING_ENDPOINT)
        else: 
            print ( "Error: Image path or image-processing endpoint missing" )
    ```

4. Spara filen **main.py**. 

5. Öppna filen **requrements.txt**. 

6. Lägg till en ny rad för ett bibliotek som ska inkluderas i containern.

   ```Text
   requests
   ```

7. Spara filen **requirements.txt**.


#### <a name="add-a-test-image-to-the-container"></a>Lägga till en testbild i containern

I stället för att använda en verklig kamera för att ge en bildfeed för det här scenariot använder vi en enskild testbild. En testbild ingår i den GitHub-lagringsplats som du laddade ned för träningsbilderna tidigare i självstudien. 

1. Navigera till testbilden, som finns på **Cognitive-CustomVision-Windows** / **Samples** / **Images** / **Test**. 

2. Kopiera **test_image.jpg** 

3. Bläddra till ioT Edge-lösningskatalogen och klistra in testbilden i **mappen modulen** / **cameraCapture.** Bilden ska vara i samma mapp som filen main.py, som du redigerade i föregående avsnitt. 

4. I Visual Studio Code öppnar du filen **Dockerfile.amd64** för modulen cameraCapture.

5. Efter den rad som upprättar arbetskatalogen, `WORKDIR /app`, lägger du till följande kodrad:

   ```Dockerfile
   ADD ./test_image.jpg .
   ```

6. Spara Dockerfile.

### <a name="prepare-a-deployment-manifest"></a>Förbereda ett distributionsmanifest

Hittills i den här självstudien har du tränat en Custom Vision-modell att klassificera bilder av träd och paketerat modellen som IoT Edge-modul. Sedan skapade du en andra modul som kan köra frågor mot bildklassificeringsservern och rapporterar resultatet tillbaka till IoT Hub. Nu är du redo att skapa det distributionsmanifest som talar om för en IoT Edge-enhet hur de här två modulerna ska startas och köras tillsammans. 

IoT Edge-tillägget för Visual Studio Code innehåller en mall i varje IoT Edge-lösning som hjälper dig skapa ett distributionsmanifest. 

1. Öppna filen **deployment.template.json** i lösningsmappen. 

2. Leta reda på **modulen** avsnitt, som ska innehålla tre moduler: de två som du skapade, klassificerare och cameraCapture, och en tredje som ingår som standard, SimulatedTemperatureSensor. 

3. Ta bort modulen **SimuleradTemperatureSensor** med alla dess parametrar. Den här modulen ingår för att tillhandahålla exempeldata för testscenarier, men den behövs inte i den här distributionen. 

4. Om du har gett bildklassificeringsmodulen något annat namn än **classifier** kontrollerar du namnet nu och ser till att det endast består av gemener. Modulen cameraCapture anropar klassificerarmodulen med hjälp av ett begärandebibliotek som formaterar alla begäranden med gemener, och IoT Edge är skiftlägeskänsligt. 

5. Uppdatera parametern **createOptions** för modulen cameraCapture med följande JSON. Den här informationen skapar miljövariabler i modulcontainern som hämtas i main.py-processen. Genom att inkludera den här informationen i distributionsmanifestet kan du ändra bild eller slutpunkt utan att behöva återskapa modulbilden. 

    ```json
    "createOptions": "{\"Env\":[\"IMAGE_PATH=test_image.jpg\",\"IMAGE_PROCESSING_ENDPOINT=http://classifier/image\"]}"
    ```

    Om du gav Custom Vision-modulen något annat namn än *classifier* uppdaterar du slutpunktsvärdet för bildbearbetning så att det matchar. 

6. Längst ned i filen uppdaterar du parametern **routes** för modulen $edgeHub. Du behöver dirigera förutsägelseresultaten från cameraCapture till IoT Hub.

    ```json
        "routes": {
          "CameraCaptureToIoTHub": "FROM /messages/modules/cameraCapture/outputs/* INTO $upstream"
        },
    ```

    Om du gav den andra modulen något annat namn än *cameraCapture* uppdaterar du vägvärdet så att det matchar. 

7. Spara filen **deployment.template.json**.

## <a name="build-and-deploy-your-iot-edge-solution"></a>Skapa och distribuera IoT Edge-lösningen

Nu när båda modulerna har skapat s och distributionsmanifestmallen har konfigurerats är du redo att skapa containerbilderna och överföra dem till containerregistret. 

När bilderna finns i registret kan du distribuera lösningen till en IoT Edge-enhet. Du kan ange moduler på en enhet via IoT Hub, men du kan också komma åt din IoT Hub och enheter via Visual Studio Code. I det här avsnittet kan du konfigurera åtkomst till din IoT Hub och sedan använda VS Code för att distribuera din lösning till IoT Edge-enheten.

Först skapar och överför du lösningen till ditt containerregister. 

1. Högerklicka på filen **deployment.template.json** i VS-kodutforskaren och välj **Bygg och push IoT Edge-lösning**. Du kan se förloppet för den här åtgärden i den integrerade terminalen i VS Code. 
2. Observera att en ny mapp har lagts till i din lösning, **config**. Expandera den här mappen och öppna **filen deployment.json** inuti.
3. Granska informationen i filen deployment.json. Filen deployment.json skapas (eller uppdateras) automatiskt baserat på den distributionsmallfil som du konfigurerade samt information från lösningen, inklusive .env-filen och module.json-filerna. 

Välj sedan din enhet och distribuera din lösning.

1. I VS Code-utforskaren expanderar du avsnittet **Azure IoT Hub-enheter**. 
2. Högerklicka på den enhet som du vill ha som mål för distributionen och välj **Skapa distribution för enskild enhet**. 
3. I filutforskaren går du till **config**-mappen i din lösning och väljer **deployment.json**. Klicka på **Välj distributionsmanifest för Edge**. 

Om distributionen lyckas skrivs ett bekräftelsemeddelande ut i utdata för VS Code. I VS Code-Utforskaren expanderar du information om den IoT Edge-enhet som du använde för den här distributionen. Hovra markören på rubriken **Azure IoT Hub Devices** (Azure IoT Hub-enheter) för att aktivera uppdateringsknappen om modulerna inte visas direkt. Det kan ta en liten stund innan modulerna startas och rapporterar tillbaka till IoT Hub. 

Du kan även kontrollera att alla moduler är igång på själva enheten. Kör följande kommando på IoT Edge-enheten för att se status för modulerna. Det kan ta en liten stund innan modulerna startas.

   ```bash
   iotedge list
   ```

## <a name="view-classification-results"></a>Visa klassificeringsresultat

Det finns två sätt att visa resultatet från modulerna, antingen på själva enheten allt eftersom meddelandena genereras och skickas, eller från Visual Studio Code när meddelandena kommer till IoT Hub. 

Från enheten visar du loggarna för modulen cameraCapture för att se meddelandena skickas och bekräftelsen att de togs emot av IoT Hub. 

   ```bash
   iotedge logs cameraCapture
   ```

Från Visual Studio-kod högerklickar du på namnet på din IoT Edge-enhet och väljer **Starta övervakning inbyggd händelseslutpunkt**. 

Resultatet från Custom Vision-modulen, som skickas som meddelanden från modulen cameraCapture, innefattar sannolikheten att bilden föreställer antingen en hemlockgran eller ett körsbärsträd. Eftersom bilden föreställer en hemlockgran bör du se sannolikheten 1.0. 

## <a name="clean-up-resources"></a>Rensa resurser

Om du planerar att fortsätta med nästa rekommenderade artikel kan du behålla de resurser och konfigurationer som du skapat och använda dem igen. Du kan även fortsätta att använda samma IoT Edge-enhet som en testenhet. 

Annars kan du ta bort de lokala konfigurationerna och De Azure-resurser som du använde i den här artikeln för att undvika avgifter. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Nästa steg

I den här självstudien tränade du en Custom Vision-modell och distribuerade den som modul till en IoT Edge-enhet. Sedan skapade du en modul som kan köra frågor mot bildklassificeringstjänsten och rapporterar resultatet tillbaka till IoT Hub. 

Om du vill prova en mer detaljerad version av det här scenariot med en livekamerafeed kan du gå till GitHub-projektet [Custom Vision and Azure IoT Edge on a Raspberry Pi 3](https://github.com/Azure-Samples/Custom-vision-service-iot-edge-raspberry-pi). 

Fortsätt med någon av följande självstudier om du vill veta mer om hur Azure IoT Edge kan hjälpa dig att omvandla dina data till affärsinsikter.

> [!div class="nextstepaction"]
> [Lagra data på gränsen med SQL Server-databaser](tutorial-store-data-sql-server.md)
