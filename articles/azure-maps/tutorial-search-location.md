---
title: 'Självstudiekurs: Sök efter platser i närheten på en karta | Microsoft Azure Maps'
description: I den här självstudien får du lära dig hur du söker efter intressanta platser på en karta med Hjälp av Microsoft Azure Maps.
author: philmea
ms.author: philmea
ms.date: 1/15/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 0b0cb92cd6b4918e28e143178a5cdbbbb19ac9af
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80333620"
---
# <a name="tutorial-search-nearby-points-of-interest-using-azure-maps"></a>Självstudiekurs: Sök i närheten av intressanta platser med Hjälp av Azure Maps

Den här självstudiekursen visar hur du skapar ett konto med Azure Maps och sedan använder API:er för Maps för att söka efter en orienteringspunkt. I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Skapa ett Azure Maps-konto
> * Hämta primärnyckeln för ditt Maps-konto
> * Skapa en ny webbsida med API:n för kartkontroll
> * Använda Maps-söktjänsten för att hitta orienteringspunkter i närheten

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com).

<a id="createaccount"></a>

## <a name="create-an-account-with-azure-maps"></a>Skapa ett konto med Azure Maps

Skapa ett nytt Maps-konto med följande steg:

1. Klicka på **Skapa en resurs** längst upp till vänster i [Azure Portal](https://portal.azure.com).
2. Skriv **Maps** i rutan *Sök på Marketplace*.
3. Bland *resultaten* väljer du **Maps**. Klicka på knappen **Skapa** som visas nedanför kartan.
4. Ange följande värden på sidan **Skapa Maps-konto**:
    * Den *Prenumeration* som ska användas för det här kontot.
    * Namnet på *Resursgrupp* för kontot. Du kan välja att *skapa ny* eller *använda befintlig* resursgrupp.
    * *Namn* för ditt nya konto.
    * *Prisnivån* för det här kontot.
    * Läs *licensen* och *sekretesspolicy* och markera kryssrutan för att godkänna villkoren.
    * Klicka på knappen **Skapa**.

![Skapa Azure Maps-konto i Azure Portal](./media/tutorial-search-location/create-account.png)

<a id="getkey"></a>

## <a name="get-the-primary-key-for-your-account"></a>Hitta primärnyckeln för ditt konto

När ditt Maps-konto har skapats hämtar du nyckeln som gör att du kan fråga API:et Kartkontroll. Vi rekommenderar att du använder kontots primära nyckel som prenumerationsnyckel när du anropar Azure Maps-tjänster.

1. Öppna ditt Maps-konto i portalen.
2. Välj **Autentisering**i inställningsavsnittet .
3. Kopiera **Primärnyckel** till Urklipp. Spara den lokalt för senare användning i den här självstudien.

![Hämta primärnyckel i Azure-portalen](./media/tutorial-search-location/get-key.png)

Mer information om autentisering i Azure Maps finns [i hantera autentisering i Azure Maps](how-to-manage-authentication.md).

<a id="createmap"></a>

## <a name="create-a-new-map"></a>Skapa en ny karta

Api:et för kartkontroll är ett praktiskt klientbibliotek. Med det här API:et kan du enkelt integrera Kartor i ditt webbprogram. Det döljer komplexiteten i de nakna REST-servicesamtalen och ökar produktiviteten med anpassningsbara komponenter. Följande steg visar hur du skapar en statisk HTML-sida inbäddad med API:et Kartkontroll.

1. Skapa en ny fil på den lokala datorn och ge den namnet **MapSearch.html**.
2. Lägg till följande HTML-komponenter i filen:

   ```HTML
    <!DOCTYPE html>
    <html>
    <head>
        <title>Map Search</title>
        <meta charset="utf-8">
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

        <!-- Add a reference to the Azure Maps Services Module JavaScript file. -->
        <script src="https://atlas.microsoft.com/sdk/javascript/service/2/atlas-service.min.js"></script>

        <script>
        function GetMap(){
            //Add Map Control JavaScript code here.
        }
        </script>

        <style>
            html,
            body {
                width: 100%;
                height: 100%;
                padding: 0;
                margin: 0;
            }

            #myMap {
                width: 100%;
                height: 100%;
            }
        </style>
    </head>
    <body onload="GetMap()">
        <div id="myMap"></div>
    </body>
    </html>
    ```

   Observera att HTML-huvudet innehåller CSS- och JavaScriptresursfiler som med Azure Kartkontroll-biblioteket som värd. Observera `onload`-händelsen i innehållet på sidan, som anropar funktionen `GetMap` när sidans innehåll har lästs in. Funktionen `GetMap` innehåller den infogade JavaScript-koden för att komma åt Azure Maps API:er.

3. Lägg till följande JavaScript-kod i HTML-filens `GetMap`-funktion. Ersätt strängen `<Your Azure Maps Key>` med den primära nyckeln som du kopierade från ditt Maps-konto.

    ```JavaScript
    //Instantiate a map object
    var map = new atlas.Map("myMap", {
        //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
        authOptions: {
            authType: 'subscriptionKey',
            subscriptionKey: '<Your Azure Maps Key>'
        }
    });
    ```

   Det här segmentet initierar API:et Kartkontroll för din Azure Maps-kontonyckel. `atlas`är namnområdet som innehåller API:et och relaterade visuella komponenter. `atlas.Map`ger kontroll för en visuell och interaktiv webbkarta.

4. Spara dina ändringar i filen och öppna HTML-sidan i en webbläsare. Kartan som visas är den mest grundläggande `atlas.Map` kartan som du kan göra genom att ringa med hjälp av din kontonyckel.

   ![Visa kartan](./media/tutorial-search-location/basic-map.png)

5. I funktionen `GetMap`, när du har initierat kartan, lägger du till följande JavaScript-kod.

    ```JavaScript
    //Wait until the map resources are ready.
    map.events.add('ready', function() {

        //Create a data source and add it to the map.
        datasource = new atlas.source.DataSource();
        map.sources.add(datasource);

        //Add a layer for rendering point data.
        var resultLayer = new atlas.layer.SymbolLayer(datasource, null, {
            iconOptions: {
                image: 'pin-round-darkblue',
                anchor: 'center',
                allowOverlap: true
            },
            textOptions: {
                anchor: "top"
            }
        });

        map.layers.add(resultLayer);
    });
    ```

   I det här `ready` kodsegmentet läggs en händelse till på kartan, som startas när kartresurserna har lästs in och kartan är klar att nås. I karthändelsehanteraren `ready` skapas en datakälla för att lagra resultatdata. Ett symbollager skapas och ansluts till datakällan. Det här lagret anger hur resultatdata i datakällan ska återges. I det här fallet återges resultatet med en mörkblå rund stiftikon, centrerad över resultatkoordinaten och gör att andra ikoner överlappar varandra. Resultatlagret läggs till i kartlagren.

<a id="usesearch"></a>

## <a name="add-search-capabilities"></a>Lägga till sökfunktioner

I det här avsnittet visas hur du använder [API:et för maps-sökning](https://docs.microsoft.com/rest/api/maps/search) för att hitta en intressant punkt på kartan. Det är ett RESTful-API för utvecklare för att söka efter adresser, orienteringspunkter och annan geografisk information. Search Service tilldelar en angiven adress latitud- och longitudinformation. **Tjänstemodulen** som beskrivs nedan kan användas till att söka efter en plats med hjälp av API:et för kartsökning.

### <a name="service-module"></a>Tjänstmodul

1. I karthändelsehanteraren `ready` konstruerar du söktjänstens URL genom att lägga till följande Javascript-kod.

    ```JavaScript
   // Use SubscriptionKeyCredential with a subscription key
   var subscriptionKeyCredential = new atlas.service.SubscriptionKeyCredential(atlas.getSubscriptionKey());

   // Use subscriptionKeyCredential to create a pipeline
   var pipeline = atlas.service.MapsURL.newPipeline(subscriptionKeyCredential);

   // Construct the SearchURL object
   var searchURL = new atlas.service.SearchURL(pipeline); 
   ```

   Skapar `SubscriptionKeyCredential` en `SubscriptionKeyCredentialPolicy` att autentisera HTTP-begäranden till Azure Maps med prenumerationsnyckeln. Tar `atlas.service.MapsURL.newPipeline()` i `SubscriptionKeyCredential` principen och skapar en [Pipeline-instans.](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-maps-typescript-latest) Representerar `searchURL` en URL till Azure Maps [Search-åtgärder.](https://docs.microsoft.com/rest/api/maps/search)

2. Lägg därefter till följande skriptblock för att skapa sökfrågan. Den använder det enkla söknings-API:et i Search Service, som kallas Fuzzy Search. Fuzzy Search-tjänsten hanterar de flesta fuzzy-indata som adresser, platser och platser av intresse (POI). Denna kod söker efter närliggande bensinstationer inom den angivna radien för den angivna latitud och longitud. En GeoJSON-funktionssamling från svaret extraheras sedan med `geojson.getFeatures()` hjälp av metoden och läggs till datakällan, vilket automatiskt resulterar i att data återges på kartan via symbollagret. Den sista delen av skriptet ställer in kameravyn med hjälp av avgränsningsrektangeln för resultat med kartans [setCamera](/javascript/api/azure-maps-control/atlas.map#setcamera-cameraoptions---cameraboundsoptions---animationoptions-)-egenskap.

    ```JavaScript
    var query =  'gasoline-station';
    var radius = 9000;
    var lat = 47.64452336193245;
    var lon = -122.13687658309935;

    searchURL.searchPOI(atlas.service.Aborter.timeout(10000), query, {
        limit: 10,
        lat: lat,
        lon: lon,
        radius: radius
    }).then((results) => {

        // Extract GeoJSON feature collection from the response and add it to the datasource
        var data = results.geojson.getFeatures();
        datasource.add(data);

        // set camera to bounds to show the results
        map.setCamera({
            bounds: data.bbox,
            zoom: 10
        });
    });
    ```

3. Spara filen **MapSearch.html** och uppdatera webbläsaren. Du bör se kartan centrerad på Seattle med runda blå stift för platser för bensinstationer i området.

   ![Visa kartan med sökresultat](./media/tutorial-search-location/pins-map.png)

4. Du kan visa rådata som kartan återger genom att ange följande HTTP-förfrågan i webbläsaren. Ersätt \<din Azure Maps-nyckel\> med din primärnyckel.

   ```http
   https://atlas.microsoft.com/search/poi/json?api-version=1.0&query=gasoline%20station&subscription-key=<subscription-key>&lat=47.6292&lon=-122.2337&radius=100000
   ```

I det här läget kan MapSearch-sidan visa orienteringspunkterna som returneras från en fuzzy-sökfråga. Lägg till vissa interaktiva funktioner och mer information om platser.

## <a name="add-interactive-data"></a>Lägga till interaktiva data

Karta som har vi gjort tittar hittills bara på longitud-/latituddata för sökresultaten. Den råa JSON som tjänsten Maps Search returnerar innehåller dock ytterligare information om varje bensinstation. Inklusive namn och gatuadress. Du kan införliva dessa data i kartan med interaktiva popup-rutor.

1. Lägg till följande kodrader `ready` i karthändelsehanteraren efter att koden har frågat den suddiga söktjänsten. Den här koden skapar en förekomst av en Popup-händelse och lägger till en mouseover-händelse i symbollagret.

    ```JavaScript
   //Create a popup but leave it closed so we can update it and display it later.
    popup = new atlas.Popup();

    //Add a mouse over event to the result layer and display a popup when this event fires.
    map.events.add('mouseover', resultLayer, showPopup);
    ```

    API:et `*atlas.Popup` tillhandahåller ett informationsfönster som är förankrat på önskad position på kartan. 

2. Lägg till följande `GetMap` kod i funktionen, för att visa moused över resultatinformation i popup.

    ```JavaScript
    function showPopup(e) {
        //Get the properties and coordinates of the first shape that the event occurred on.

        var p = e.shapes[0].getProperties();
        var position = e.shapes[0].getCoordinates();

        //Create HTML from properties of the selected result.
        var html = `
          <div style="padding:5px">
            <div><b>${p.poi.name}</b></div>
            <div>${p.address.freeformAddress}</div>
            <div>${position[1]}, ${position[0]}</div>
          </div>`;

        //Update the content and position of the popup.
        popup.setPopupOptions({
            content: html,
            position: position
        });

        //Open the popup.
        popup.open(map);
    }
    ```

3. Spara filen och uppdatera webbläsaren. Nu visar kartan i webbläsaren när du hovrar över någon av sökningskartnålarna.

    ![Azure Kartkontroll och Search Service](./media/tutorial-search-location/popup-map.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Skapa ett konto med Azure Maps
> * Hitta primärnyckeln för ditt konto
> * Skapa ny webbsida med API:et Kartkontroll
> * Använda Search Service för att hitta orienteringspunkter i närheten

> [!div class="nextstepaction"]
> [Visa fullständig källkod](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/search.html)

> [!div class="nextstepaction"]
> [Visa direktexempel](https://azuremapscodesamples.azurewebsites.net/?sample=Search%20for%20points%20of%20interest)

Nästa självstudie demonstrerar hur du visar en väg mellan två platser.

> [!div class="nextstepaction"]
> [Dirigera mot ett mål](./tutorial-route-location.md)
