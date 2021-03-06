---
title: Självstudiekurs - Skapa och hantera regler i ditt Azure IoT Central-program
description: Den här självstudien visar hur Azure IoT Central-regler gör att du kan övervaka dina enheter i nära realtid och automatiskt anropa åtgärder, till exempel skicka ett e-postmeddelande, när regeln utlöser.
author: dominicbetts
ms.author: dobett
ms.date: 04/06/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 555da74da65f3b1897a276cf819a263334cfa053
ms.sourcegitcommit: 25490467e43cbc3139a0df60125687e2b1c73c09
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2020
ms.locfileid: "80999046"
---
# <a name="tutorial-create-a-rule-and-set-up-notifications-in-your-azure-iot-central-application"></a>Självstudiekurs: Skapa en regel och konfigurera meddelanden i ditt Azure IoT Central-program

*Den här artikeln gäller för operatörer, kompilerare och administratörer.*

Du kan använda Azure IoT Central för att fjärrövervaka dina anslutna enheter. Med Azure IoT Central-reglerna kan du övervaka dina enheter i nära realtid och automatiskt anropa åtgärder, till exempel skicka ett e-postmeddelande. I den här artikeln beskrivs hur du skapar regler för att övervaka den telemetri som dina enheter skickar.

Enheter använder telemetri för att skicka numeriska data från enheten. En regel utlöses när den valda enhetstelemetrin korsar ett angivet tröskelvärde.

I den här självstudien skapar du en regel för att skicka ett e-postmeddelande när temperaturen i en simulerad miljösensorenhet överstiger 70&deg; F.

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
>
> * Skapa en regel
> * Lägga till en e-poståtgärd

## <a name="prerequisites"></a>Krav

Innan du börjar slutför du [create an Azure IoT Central application](./quick-deploy-iot-central.md) and Add a [simulated device to your IoT Central application](./quick-create-simulated-device.md) quickstarts to create the **MXChip IoT DevKit** device template to work with.

## <a name="create-a-rule"></a>Skapa en regel

Om du vill skapa en telemetriregel måste enhetsmallen innehålla minst ett telemetrivärde. Den här självstudien använder en simulerad **MXChip IoT DevKit-enhet** som skickar telemetri för temperatur och luftfuktighet. Du har lagt till den här enhetsmallen och skapat en simulerad enhet i [snabbstarten Lägg till en simulerad enhet i snabbstarten för IoT Central-programmet.](./quick-create-simulated-device.md) Regeln övervakar den temperatur som rapporteras av enheten och skickar ett e-postmeddelande när det går över 70 grader.

1. Välj **Regler**i den vänstra rutan .

1. Om du inte har skapat några regler ännu visas följande skärm:

    ![Inga regler än](media/tutorial-create-telemetry-rules/rules-landing-page1.png)

1. Markera **+** det här om du vill lägga till en ny regel.

1. Ange namnet _Temperaturövervakare_ för att identifiera regeln och tryck på Retur.

1. Välj **MXChip IoT** DevKit-enhetsmallen. Som standard gäller regeln automatiskt för alla enheter som är associerade med enhetsmallen. Om du vill filtrera efter en delmängd av enheterna väljer du **+ Filtrera** och använder enhetsegenskaper för att identifiera enheterna. Om du vill inaktivera regeln växlar du knappen **Aktiverad/inaktiverad** i regelhuvudet:

    ![Filtrerar och aktiverar](media/tutorial-create-telemetry-rules/device-filters.png)

### <a name="configure-the-rule-conditions"></a>Konfigurera regelvillkoren

Villkor definierar de kriterier som regeln övervakar. I den här självstudien konfigurerar du regeln så&deg; att den avfyras när temperaturen överstiger 70 F.

1. Välj **Temperatur** i listrutan **Telemetri.**

1. Välj sedan **Är större än** som **operator** och anger _70_ som **värde**.

    ![Villkor](media/tutorial-create-telemetry-rules/condition-filled-out1.png)

1. Du kan också ange en **tidsaggregering**. När du väljer en tidsaggregering måste du också välja en aggregeringstyp, till exempel medelvärde eller summa i listrutan aggregering.

    * Utan aggregering utlöser regeln för varje telemetridatapunkt som uppfyller villkoret. Om du till exempel konfigurerar regeln så att den utlöses när temperaturen är över 70 utlöses regeln nästan omedelbart när enhetstemperaturen överskrider det här värdet.
    * Med aggregering utlöses regeln om det sammanlagda värdet för telemetridatapunkterna i tidsfönstret uppfyller villkoret. Om du till exempel konfigurerar regeln för att utlösa när temperaturen är över 70 och med en genomsnittlig tidsaggregering på 10 minuter, utlöser regeln när enheten rapporterar en medeltemperatur som är högre än 70, beräknad över ett 10-minutersintervall.

     ![Aggregerat tillstånd](media/tutorial-create-telemetry-rules/aggregate-condition-filled-out1.png)

Du kan lägga till flera villkor i en regel genom att välja **+ Villkor**. När flera villkor anges måste alla villkor vara uppfyllda för att regeln ska utlösas. Varje villkor sammanfogas `AND` av en implicit sats. Om du använder tidsaggregering med flera villkor måste alla telemetrivärden aggregeras.

### <a name="configure-actions"></a>Konfigurera åtgärder

När du har definierat villkoret ställer du in de åtgärder som ska vidtas när regeln utlöses. Åtgärder anropas när alla villkor som anges i regeln utvärderas till true.

1. Välj **+ E-post** i avsnittet **Åtgärder.**

1. Ange _temperaturvarning_ som visningsnamn för åtgärden, din e-postadress i fältet **Till** och du bör _kontrollera enheten!_ som en anteckning som ska visas i brödtexten i e-postmeddelandet.

    > [!NOTE]
    > E-postmeddelanden skickas endast till de användare som har lagts till i programmet och har loggat in minst en gång. Läs mer om [användarhantering](howto-administer.md) i Azure IoT Central.

   ![Konfigurera åtgärd](media/tutorial-create-telemetry-rules/configure-action1.png)

1. Om du vill spara åtgärden väljer du **Klar**. Du kan lägga till flera åtgärder i en regel.

1. Om du vill spara regeln väljer du **Spara**. Regeln går live inom några minuter och börjar övervaka telemetri som skickas till ditt program. När villkoret som anges i regeln uppfylls utlöser regeln den konfigurerade e-poståtgärden.

Efter ett tag får du ett e-postmeddelande när regeln utlöses:

![Exempel på e-post](media/tutorial-create-telemetry-rules/email.png)

## <a name="delete-a-rule"></a>Ta bort en regel

Om du inte längre behöver en regel tar du bort den genom att öppna regeln och välja **Ta bort**.

## <a name="enable-or-disable-a-rule"></a>Aktivera eller inaktivera en regel

Välj den regel som du vill aktivera eller inaktivera. Växla knappen **Aktiverad/inaktiverad** i regeln för att aktivera eller inaktivera regeln för alla enheter som är begränsade i regeln.

## <a name="enable-or-disable-a-rule-for-specific-devices"></a>Aktivera eller inaktivera en regel för specifika enheter

Välj den regel som du vill anpassa. Använd ett eller flera filter i avsnittet **Målenheter** för att begränsa regelns omfattning till de enheter som du vill övervaka.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

* Skapa en telemetribaserad regel
* Lägga till en åtgärd

Nu när du har definierat en tröskelbaserad regel är det föreslagna nästa steget att lära dig hur du:

> [!div class="nextstepaction"]
> [Konfigurera kontinuerlig dataexport](./howto-export-data.md).
