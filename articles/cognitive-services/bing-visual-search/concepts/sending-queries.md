---
title: Skicka sökfrågor till API:et för visuell sökning i Bing
titleSuffix: Azure Cognitive Services
description: I den här artikeln beskrivs parametrar och attribut för begäranden som skickas till API:et för visuell sökning i Bing samt svarsobjektet.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: conceptual
ms.date: 01/08/2019
ms.author: aahi
ms.openlocfilehash: 5d27aa80a63232694e1c9951f98b2191ba575e74
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "75913073"
---
# <a name="sending-search-queries-to-the-bing-visual-search-api"></a>Skicka sökfrågor till API:et för visuell sökning i Bing

I den här artikeln beskrivs parametrar och attribut för begäranden som skickas till API:et för visuell sökning i Bing samt svarsobjektet. 

Du kan få insikter om en bild på tre sätt:

- Använda en insights-token som du får från en bild i ett tidigare anrop till en av [slutpunkterna för API för Bing-bildsökning.](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)
- Skickar webbadressen till en bild.
- Ladda upp en bild (i binärt format).

## <a name="bing-visual-search-requests"></a>Bing-sökningar i visuell sökning

Om du skickar en bildtoken eller URL till Visuell sökning visas följande kodavsnitt det JSON-objekt som du måste inkludera i brödtexten i POST:

```json
{
    "imageInfo" : {
        "url" : "",
        "imageInsightsToken" : "",
        "cropArea" : {
            "top" : 0.1,
            "left" : 0.5,
            "right" : 0.9,
            "bottom" : 0.9
        }
    },
    "knowledgeRequest" : {
      "filters" : {
        "site" : ""
      }
    }
}
```

Objektet `imageInfo` måste innehålla antingen fältet `url` eller `imageInsightsToken`, men inte båda. Ange `url` fältet till URL:en för en internettillgänglig bild. Den maximala storleken som stöds är 1 MB.

En insiktstoken måste anges för `imageInsightsToken`. Anropa API för Bing-bild för att få en token för insikter. Svaret innehåller en lista över `Image` objekt. Varje `Image`-objekt innehåller ett `imageInsightsToken`-fält som innehåller token.

Fältet `cropArea` är valfritt. Beskärningsområdet anger det övre vänstra hörnet och det nedre högra hörnet i ett område av intresse. Ange värden i intervallet 0.0 till och med 1.0. Värdena är en del av den totala bredden eller höjden. Till exempel markerar exemplet ovan höger halva av bilden som det intressanta området. Inkludera den om du vill begränsa insiktsbegäran till det intressanta området.

Objektet `filters` innehåller ett platsfilter (se fältet `site`) som du kan använda för att begränsa liknande bilder och liknande produktresultat till en specifik domän. Om bilden till exempel är av en Surface `site` `www.microsoft.com`Book kan du ställa in den .

Om du vill hämta insikter om en lokal kopia av en bild kan du överföra bilden som binära data.

Mer information om att inkludera dessa alternativ i brödtexten i POST finns i [Typer av innehållsformulär](#content-form-types).

### <a name="search-endpoint"></a>Slutpunkt för sök

Slutpunkten för visuell sökning är: https:\/\/api.cognitive.microsoft.com/bing/v7.0/images/visualsearch.

Begäranden måste skickas som HTTP POST-begäranden.

### <a name="query-parameters"></a>Frågeparametrar

Här följer frågeparametrarna som din begäran bör ange. Du bör åtminstone inkludera `mkt` frågeparametern:

| Namn | Värde | Typ | Krävs |
| --- | --- | --- | --- |
| <a name="cc" />cc  | En landskod med två tecken som representerar var resultaten kommer ifrån.<br /><br /> Om du anger parametern måste du även ange huvudet [Accept-Language](#acceptlanguage). Bing använder det första språket som stöds på listan över språk och kombinerar språket med landskoden som du anger för att fastställa vilken marknad som resultatet som returneras från. Om listan över språk inte innehåller något språk som stöds hittar Bing det närmaste språket och marknaden som har stöd för begäran. Eller så kan den använda en sammansatt eller standardmarknad för resultatet i stället för den angivna.<br /><br /> Du bör endast använda den här frågeparametern och `Accept-Language`-frågeparametern om du anger flera språk. I annat fall bör du använda frågeparametrarna `mkt` och `setLang`.<br /><br /> Den här parametern och [mkt](#mkt)-frågeparametern utesluter varandra&mdash;ange inte båda två. | String | Inga       |
| <a name="mkt" />mkt   | Marknaden som resultatet kommer från. <br /><br /> **OBS:** Du bör alltid ange marknaden, om den är känd. Om du anger marknaden gör det enklare för Bing att dirigera begäran och returnera ett lämpligt och optimalt svar.<br /><br /> Den här parametern och [cc](#cc)-frågeparametern utesluter varandra&mdash;ange inte båda två. | String | Ja      |
| <a name="safesearch" />safeSearch | Ett filter för barnförbjudet innehåll. Här följer de möjliga skiftlägeskänsliga filtervärdena.<br /><ul><li>Av&mdash;Returnerar inte webbsidor med text eller bilder med innehåll som är olämpligt för barn.<br /><br/></li><li>Måttlig&mdash;Returnera webbsidor med vuxet innehåll, men inte vuxna avbildningar.<br /><br/></li><li>Strikt&mdash;Returnerar inte webbsidor med text eller bilder med innehåll som är olämpligt för barn.</li></ul><br /> Standardinställningen är Måttlig.<br /><br /> **Obs!** Om förfrågan kommer från en marknad för vilken Bings princip för vuxet innehåll kräver att `safeSearch` ska vara inställd på Strikt ignorerar Bing värdet `safeSearch` och använder Strikt.<br/><br/>**OBS:** Om du `site:` använder frågeoperatorn finns det en risk att svaret `safeSearch` kan innehålla barnförbjudet innehåll oavsett vad frågeparametern är inställd på. Använd endast `site:` om du är medveten om innehållet på webbplatsen och ditt scenario tillåter möjligheten att det förekommer innehåll som är olämpligt för barn.  | String | Inga       |
| <a name="setlang" />setLang  | Språket som ska användas för användargränssnittssträngar. Ange språket med språkkoden ISO 639-1 med två bokstäver. Språkkoden för engelska är till exempel EN. Standardvärdet är EN (engelska).<br /><br /> Även om det är valfritt bör du alltid ange språket. Normalt anger du `setLang` på samma språk som anges av `mkt` om inte användaren vill att gränssnittets strängar ska visas på ett annat språk.<br /><br /> Den här parametern och [Accept-Language](#acceptlanguage)-huvudet utesluter varandra&mdash;ange inte båda två.<br /><br /> En användargränssnittssträng är en sträng som används som en etikett i ett användargränssnitt. Det finns några användargränssnittssträngar i JSON-svarsobjekt. Alla länkar till Bing.com-egenskaper i svarsobjekten använder det angivna språket. | String | Inga   |

## <a name="headers"></a>Rubriker

Här följer huvudena som din begäran bör ange. `Content-Type` Rubrikerna `Ocp-Apim-Subscription-Key` och är de enda rubriker som krävs, `X-MSEdge-ClientIP`men `X-Search-Location`du bör även inkludera `User-Agent`, `X-MSEdge-ClientID`, och .

| Huvud | Beskrivning |
| --- | --- |
| <a name="acceptlanguage" />Accept-Language  | Valfritt begärandehuvud.<br /><br /> En kommaavgränsad lista över språk som ska användas för användargränssnittssträngar. Listan är i fallande prioritetsordning. Mer information, bland annat om det förväntade formatet, finns i [RFC2616](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).<br /><br /> Det är huvudet och [setLang](#setlang)-frågeparametern utesluter varandra&mdash;ange inte båda två.<br /><br /> Om du anger huvudet måste du även ange frågeparametern [cc](#cc). För att fastställa vilken marknad som resultat ska returneras för använder Bing det första språk som stöds på listan och kombinerar det med parametervärdet `cc`. Om listan inte innehåller något språk som stöds hittar Bing det närmaste språket och marknaden som har stöd för begäran, eller så använder Bing en aggregerad eller standardmarknad för resultatet. Se `BingAPIs-Market` rubriken för att fastställa vilken marknad som Bing använde.<br /><br /> Använd enbart det här huvudet och `cc`-frågeparametern om du anger flera språk. Annars kan du använda frågeparametrarna [mkt](#mkt) och [setLang](#setlang).<br /><br /> En användargränssnittssträng är en sträng som används som en etikett i ett användargränssnitt. Det finns några användargränssnittssträngar i JSON-svarsobjekt. Alla länkar till Bing.com-egenskaper i svarsobjekten använder det angivna språket.  |
| <a name="contenttype" />Content-Type  |     |
| <a name="market" />BingAPIs-Market    | Svarshuvud.<br /><br /> Marknaden som används av begäran. Formuläret är \<languageCode\>-\<countryCode\>. Exempel: sv-SE.  |
| <a name="traceid" />BingAPIs-TraceId  | Svarshuvud.<br /><br /> ID för loggposten som innehåller information om begäran. När ett fel uppstår ska du avbilda detta ID. Om det inte går att fastställa och lösa problemet ska du ange ID:t tillsammans med annan information som du ger supportteamet. |
| <a name="subscriptionkey" />Ocp-Apim-Subscription-Key | Begärandehuvud som krävs.<br /><br /> Prenumerationsnyckeln som du fick när du registrerade dig för den här tjänsten i [Cognitive Services](https://www.microsoft.com/cognitive-services/). |
| <a name="pragma" />Pragma |   |
| <a name="useragent" />User-Agent  | Valfritt begärandehuvud.<br /><br /> Användaragenten som skapade begäran. Bing använder användaragenten för att ge mobila användare en optimerad upplevelse. Även om det är valfritt rekommenderar vi även att du alltid anger det här huvudet.<br /><br /> Användaragenten ska vara samma sträng som alla vanliga webbläsare skickar. Information om användaragenter finns i [RFC 2616](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).<br /><br /> Här följer några exempel på användaragentsträngar.<br /><ul><li>Windows Phone&mdash;Mozilla/5.0 (kompatibel; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)<br /><br /></li><li>Android&mdash;Mozilla/5.0 (Linux; U; Android 2.3.5; en-us; SCH-I500 Build/GINGERBREAD) AppleWebKit/533.1 (KHTML; som Gecko) Version/4.0 Mobile Safari/533.1<br /><br /></li><li>iPhone&mdash;Mozilla/5.0 (iPhone; CPU iPhone OS 6_1 som Mac OS X) AppleWebKit/536.26 (KHTML; som Gecko) Mobile/10B142 iPhone4;1 BingWeb/3.03.1428.20120423<br /><br /></li><li>PC&mdash;Mozilla/5.0 (Windows NT 6.3; WOW64; Trident/7.0; Touch; rv:11.0) som Gecko<br /><br /></li><li>iPad&mdash;Mozilla/5.0 (iPad; CPU OS 7_0 som Mac OS X) AppleWebKit/537.51.1 (KHTML, som Gecko) Version/7.0 Mobile/11A465 Safari/9537.53</li></ul>      |
| <a name="clientid" />X-MSEdge-ClientID  | Valfritt huvud för begäran och svar.<br /><br /> Bing använder det här huvudet för att ge användarna konsekvent beteende i Bing API-anrop. Bing ger ofta förhandsversioner av nya funktioner och förbättringar och använder klient-ID som en nyckel för att tilldela trafik till olika förhandsversioner. Om du inte använder samma klient-ID för en användare vid flera förfrågningar kan sedan Bing tilldela användaren flera motstridiga förhandsversioner. Om du tilldelas flera motstridiga förhandsversioner kan det leda till en inkonsekvent användarupplevelse. Om till exempel den andra begäran har en annan förhandsversionstilldelning än den första kan upplevelsen vara oväntad. Bing kan också använda klient-ID för att skräddarsy webbresultatet för klient-ID:ts sökhistorik, vilket ger användaren en mer omfattande upplevelse.<br /><br /> Bing använder också det här huvudet för att förbättra resultatets rangordning genom att analysera aktiviteten som genererats av ett klient-ID. Relevansförbättringarna kan ge bättre resultat som levereras av Bing-API: er, vilka i sin tur möjliggör högre klickfrekvens för API-konsumenten.<br /><br /> **Viktigt!** Även om det är valfritt bör du överväga att använda det här huvudet som krävs. Bestående klient-ID för flera förfrågningar för samma slutanvändare och enhetskombination gör det möjligt 1) för API-konsumenten att få en konsekvent användarupplevelse och 2) att få högre klickfrekvens via resultat av högre kvalitet från Bing-API: er.<br /><br /> Följande är de grundläggande användningsregler som gäller för det här huvudet.<br /><ul><li>Varje användare som använder ditt program på enheten måste ha ett unikt, Bing-genererat klient-ID.<br /><br/>Om du inte använder det här huvudet i begäran genererar Bing ett ID och returnerar det i svarshuvudet X-MSEdge-ClientID. Den enda gången som du inte får inkludera det här huvudet i en begäran är första gången du använder din app på enheten.<br /><br/></li><li>**Obs!** Du måste se till att detta klient-ID inte är länkbart till någon autentiserad användarkontoinformation.</li><li>Använd klient-ID för varje Bing API-begäran som din app gör för den här användaren på enheten.<br /><br/></li><li>Spara klient-ID:t. Om du vill bevara ID:t i en webbläsarapp ska du använda en beständig HTTP-cookie i alla sessioner. Använd inte en sessionscookie. För andra appar, till exempel mobilappar, använder du enhetens beständiga lagring för att bevara ID.<br /><br/>Nästa gång användaren använder appen på enheten ska du hämta klient-ID:t som du sparade.</li></ul><br /> **Obs!** Bing-svar kanske eller kanske inte omfattar det här huvudet. Om svaret innehåller det här huvudet ska du avbilda klient-ID:t och använda det för alla efterföljande Bing-begäranden för användaren på enheten.<br /><br /> **Obs!** Om du inkluderar X-MSEdge-ClientID får du inte ta med cookies i begäran. |
| <a name="clientip" />X-MSEdge-ClientIP   | Valfritt begärandehuvud.<br /><br /> Klientenhetens IPv4- eller IPv6-adress. IP-adressen används för att identifiera användarens plats. Bing använder platsinformationen för att fastställa SafeSearch-beteende.<br /><br /> **Obs!** Även om det är valfritt rekommenderar vi att du alltid anger det här huvudet och huvudet X-Search-Location.<br /><br /> Förvräng inte adressen (till exempel genom att ändra den sista oktetten till 0). Om adressresultatet förvillas på en plats som inte är i närheten av enhetens verkliga plats kan det leda till att Bing presenterar felaktiga resultat. |
| <a name="location" />X-Search-Location   | Valfritt begärandehuvud.<br /><br /> En semikolonavgränsad lista med nyckel/värde-par som beskriver klientens geografiska plats. Bing använder platsinformationen till att fastställa ett säkert sökbeteende och returnera relevant lokalt innehåll. Ange nyckel/värde-par som \<nyckel\>:\<värde\>. Följande är de nycklar som används för att ange användarens plats.<br /><br /><ul><li>lat&mdash;krävs. Latitud för klientens plats i grader. Latituden måste vara större än eller lika med -90.0 och vara mindre än eller lika med +90.0. Negativa värden anger sydliga latituder och positiva värden anger nordliga latituder.<br /><br /></li><li>long&mdash;Required. Longitud för klientens plats i grader. Longituden måste vara större än eller lika med -180.0 och vara mindre än eller lika med +180.0. Negativa värden anger västliga longituder och positiva värden anger östliga longituder.<br /><br /></li><li>re&mdash;Krävs. Radien, i meter, som anger koordinaternas vågräta exakthet. Skicka värdet som returneras av enhetens platstjänst. Typiska värden kan vara 22 m för GPS/Wi-Fi, 380 m för celltornstriangulering och 18 000 m för omvänd IP-sökning.<br /><br /></li><li>ts&mdash;Valfritt. UTC-UNIX-tidsstämpel för när klienten befann sig på platsen. (UNIX-tidsstämpeln är antalet sekunder sedan den 1 januari 1970.)<br /><br /></li><li>head&mdash;Optional. Klientens relativa riktning eller färdriktning. Ange färdriktningen som grader från 0 till 360 där du räknar medurs i förhållande till norr. Ange endast den här nyckeln om nyckeln `sp` är nollskiljd.<br /><br /></li><li>sp&mdash;Optional. Den vågräta hastigheten i meter per sekund som klientenheten färdas.<br /><br /></li><li>alt&mdash;Valfritt. Klientenhetens höjd i meter.<br /><br /></li><li>are&mdash;Valfritt. Radien, i meter, som anger koordinaternas lodräta exakthet. Ange den här nyckeln endast om du anger nyckeln `alt`.<br /><br /></li></ul> **Obs!** Även om många av nycklarna är valfria blir resultatet för platsen mer exakt ju mer information du anger.<br /><br /> **Obs!** Även om det är valfritt rekommenderar vi att du alltid anger användarens geografiska plats. Det är särskilt viktigt att ange plats om klientens IP-adress inte exakt avspeglar användarens fysiska plats (till exempel om klienten använder VPN). För bästa resultat bör du inkludera `X-MSEdge-ClientIP` det här huvudet och huvudet, men du bör åtminstone inkludera det här huvudet.       |

> [!NOTE]
> Kom ihåg att [Bing Search API-användning och visningskrav](../../bing-web-search/use-display-requirements.md) kräver överensstämmelse med alla tillämpliga lagar, inklusive när det gäller användningen av dessa rubriker. I till exempel vissa jurisdiktioner, som Europa, finns det krav på att skaffa användarens medgivande innan du placerar vissa spårningsenheter på användarenheter.

<a name="content-form-types" />

### <a name="content-form-types"></a>Typer av innehållsformulär

Varje begäran måste `Content-Type` innehålla huvudet. Huvudet måste anges till: `multipart/form-data; boundary=\<boundary string\>` \<, där\> gränssträngen är en unik, ogenomskinlig sträng som identifierar gränsen för formulärdata. Till exempel `boundary=boundary_1234-abcd`.

Om du skickar en bildtoken eller URL till Visuell sökning visas följande kodavsnitt vilka formulärdata som du måste inkludera i brödtexten i POST. Formulärdata måste innehålla `Content-Disposition` huvudet och du `name` måste ange dess parameter till "knowledgeRequest". Mer information `imageInfo` om objektet finns i begäran.

```
--boundary_1234-abcd
Content-Disposition: form-data; name="knowledgeRequest"

{
    "imageInfo" : {
        "url" : "https://contoso.com/2018/05/fashion/red.jpg"
    }
}

--boundary_1234-abcd--
```

Du kan också `enableEntityData` ange attributet `true` i sidhuvudet till för detaljerad information om huvudentiteten i bilden du laddar upp, inklusive länkar till webben och attributionsinformation. Det här `false` fältet är som standard.

```
--boundary_1234-abcd
Content-Disposition: form-data; name="knowledgeRequest"

{
  "imageInfo" : {
      "url" : "https://contoso.com/2018/05/fashion/red.jpg"
  },
  "knowledgeRequest" : {
    "invokedSkillsRequestData" : {
        "enableEntityData" : "true"
    }
  }
}

--boundary_1234-abcd--
```

Om du laddar upp en lokal bild visas följande kodavsnitt vilka formulärdata som du måste inkludera i brödtexten i POST. Formulärdata måste innehålla `Content-Disposition` huvudet. Parametern `name` måste anges till ”image” och parametern `filename` kan anges till valfri sträng. Rubriken `Content-Type` kan ställas in på vilken vanlig bild mimtyp som helst. Innehållet i formuläret är den binära data i bilden. Den maximala bildstorlek som du kan ladda upp är 1 MB. Det som är störst av bredden eller höjden ska vara 1 500 bildpunkter eller mindre.

```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"
Content-Type: image/jpeg

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

Följande kodavsnitt visar hur du anger vilken region av intresse en uppladdad bild:

```
--boundary_1234-abcd
Content-Disposition: form-data; name="knowledgeRequest"

{
    "imageInfo" : {
        "cropArea" : {
            "top" : 0.2,
            "left" : 0.3,
            "bottom" : 0.7,
            "right" : 0.6
        }
    }
}

--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="image"
Content-Type: image/jpeg


ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

### <a name="example-request"></a>Exempelbegäran

Följande kodavsnitt visar en fullständig begäran om bildinsikter som skickar en bildtoken och ett område av intresse. Du får insights-token från ett tidigare samtal till /images/search:

```  
POST https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch?mkt=en-us HTTP/1.1  
Content-Type: multipart/form-data; boundary=boundary_1234-abcd
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com 

--boundary_1234-abcd
Content-Disposition: form-data; name="knowledgeRequest"

{
    "imageInfo" : {
        "imageInsightsToken" : "mid_D6426898706EC7..."
        "cropArea" : {
            "top" : 0.1,
            "left" : 0.2,
            "bottom" : 0.7,
            "right" : 0.5
        }
    }
}

--boundary_1234-abcd--
```

## <a name="bing-visual-search-responses"></a>Svar på visuell sökning i Bing


[!INCLUDE [cognitive-services-bing-url-note](../../../../includes/cognitive-services-bing-url-note.md)]

Om det finns insikter för avbildningen innehåller svaret en eller flera `tags` som innehåller insikterna. Fältet `image` innehåller insights-token för indataavbildningen:

```json
{
  "_type" : "ImageKnowledge",
  "tags" : [
    {...},
    {...},
    {...},
    {...},
    {...}
  ],
  "image" : {
    "imageInsightsToken" : "bcid_AF8C9CA409421B..."
  }
}
```

Fältet `tags` innehåller ett visningsnamn och en lista med åtgärder (insikter). En av taggarna innehåller ett `displayName`-fält som har angetts till en tom sträng. Den här taggen innehåller standardinsikterna, till exempel webbsidor som innehåller den bilden, snarlika bilder och shoppingkällor för poster som hittades på bilden. Eftersom hela bilden är av intresse innehåller standardtaggen för insikter inte begränsningsrutor för de regioner av intresse:

```json
{
  "_type" : "ImageKnowledge",
  "tags" : [
    {
      "displayName" : "",
      "actions" : [
        {...},
        {...},
        {...},
        {...}
      ]
    },
    {...},
    {...},
    {...},
    {...}
  ],
  "image" : {
    "imageInsightsToken" : "bcid_AF8C9CA409421B..."
  }
}
```

En lista över standardinsikterna finns i [taggen Standardinsikter](../default-insights-tag.md).

Resten av taggarna innehåller andra insikter som kan vara av intresse för användaren. Om bilden exempelvis innehåller text kan en av taggarna omfatta en TextResults-insikt som innehåller den identifierade texten. Eller, om Bing känner igen en enhet (det vill ha en kulturellt välkänd/populär person, plats eller sak) i bilden, kan en av taggarna identifiera entiteten. Visuell sökning returnerar också en mängd olika villkor (taggar) som härletts från den inmatade bilden. Dessa taggar gör det möjligt för användare att utforska begrepp som finns i bilden. Om bilden till exempel föreställer berömda idrottare kan en av taggarna vara sport, som innehåller länkar till bilder av sport.

Varje tagg innehåller ett visningsnamn som du kan använda för att kategorisera insikter, avgränsningsrektangel som identifierar den intressanta regionen som insikten gäller för och en miniatyrbild av bilden. Om bilden föreställer en person som har på sig en sporttröja kan en av taggarna innehålla en avgränsningsrektangel som en gräns för tröjan och inkludera VisualSearch- och ProductVisualSearch-insikter. Och en annan tagg kan innehålla ImageResults-insikter som innehåller en URL för API-begäran /images/search för att hämta bilder som är relaterade eller en Bing.com-sökwebbadress som leder användaren till bildsökningsresultat för Bing.com.

Alla andra taggar än insiktstaggarna innehåller avgränsningsrektanglar som identifierar områden av intresse på bilden. Om bilden till exempel innehåller flera identifierade personer kan taggarna innehålla avgränsningsrektanglar för var och en av personerna, eller om bilden innehåller identifierade klädesplagg kan taggarna innehålla avgränsningsrektanglar för varje identifierat klädesplagg. Du kan använda avgränsningsrektanglarna för att skapa aktiva punkter över bilden som vid ett klick innehåller information om innehållet för den delen av bilden. Du får inte ta med aktiva punkter i en bild för avgränsningsrektanglar som identifierar hela bilden.

### <a name="text-recognition"></a>Textigenkänning

Om bilden innehåller text som tjänsten känner igen innehåller en av taggarna en TextResults-insikt (åtgärd). Insikten innehåller `displayName` den tolkade texten:

```json
    {
        "image" : {
            "thumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?q=%23%23Text..."
        },
        "displayName" : "##TextRecognition",
        "boundingBox" : {
            "queryRectangle" : {
                "topLeft" : {"x" : 0, "y" : 0},
                "topRight" : {"x" : 1, "y" : 0},
                "bottomRight" : {"x" : 1, "y" : 1},
                "bottomLeft" : {"x" : 0, "y" : 1}
            },
            "displayRectangle" : {
                "topLeft" : {"x" : 0, "y" : 0},
                "topRight" : {"x" : 1, "y" : 0},
                "bottomRight" : {"x" : 1, "y" : 1},
                "bottomLeft" : {"x" : 0, "y" : 1}
            }
        },
        "actions" : [{
            "displayName" : "WALK BIKE ACROSS BRIDGE",
            "actionType" : "TextResults"
        }],
        "sources" : ["OCR"]
    }
```

Eftersom taggens `displayName`-fält innehåller ##TextRecognition ska du inte använda den som ett kategorihuvud i UX. Det gäller alla visningsnamn som börjar med ##. Använd i stället åtgärdens visningsnamn.

Textigenkänning kan också identifiera kontaktuppgifter på visitkort, till exempel telefonnummer och e-postadresser. Avgränsningsrektangeln identifierar platsen för kontaktuppgifterna för kortet.

```json
    {
      "image" : {
        "thumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?q=%23%23TextRecognition..."
      },
      "displayName" : "##TextRecognition",
      "boundingBox" : {
        "queryRectangle" : {
          "topLeft" : {"x" : 0.635, "y" : 0},
          "topRight" : {"x" : 0.77, "y" : 0},
          "bottomRight" : {"x" : 0.77, "y" : 0.4873333},
          "bottomLeft" : {"x" : 0.635, "y" : 0.4873333}
        },
        "displayRectangle" : {
          "topLeft" : {"x" : 0.635, "y" : 0},
          "topRight" : {"x" : 0.77, "y" : 0},
          "bottomRight" : {"x" : 0.77, "y" : 0.4873333},
          "bottomLeft" : {"x" : 0.635, "y" : 0.4873333}
        }
      },
      "actions" : [
        {
          "url" : "tel:888%20555%201212",
          "actionType" : "Uri"
        }
      ],
      "sources" : ["OCR"]
    },
    {
      "image" : {
        "thumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?q=%23%23TextRecognition..."
      },
      "displayName" : "##TextRecognition",
      "boundingBox" : {
        "queryRectangle" : {
          "topLeft" : {"x" : 0.63, "y" : 0},
          "topRight" : {"x" : 0.866, "y" : 0},
          "bottomRight" : {"x" : 0.866, "y" : 0.5553334},
          "bottomLeft" : {"x" : 0.63, "y" : 0.5553334}
        },
        "displayRectangle" : {
          "topLeft" : {"x" : 0.63, "y" : 0},
          "topRight" : {"x" : 0.866, "y" : 0},
          "bottomRight" : {"x" : 0.866, "y" : 0.5553334},
          "bottomLeft" : {"x" : 0.63, "y" : 0.5553334}
        }
      },
      "actions" : [
        {
          "url" : "mailto:someone@outlook.com",
          "actionType" : "Uri"
        }
      ],
      "sources" : ["OCR"]
    },
    {
      "image" : {
        "thumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?q=%23%23TextRecognition..."
      },
      "displayName" : "##TextRecognition",
      "boundingBox" : {
        "queryRectangle" : {
          "topLeft" : {"x" : 0, "y" : 0},
          "topRight" : {"x" : 1, "y" : 0},
          "bottomRight" : {"x" : 1, "y" : 1},
          "bottomLeft" : {"x" : 0, "y" : 1}
        },
        "displayRectangle" : {
          "topLeft" : {"x" : 0, "y" : 0},
          "topRight" : {"x" : 1, "y" : 0},
          "bottomRight" : {"x" : 1, "y" : 1},
          "bottomLeft" : {"x" : 0, "y" : 1}
        }
      },
      "actions" : [
        {
          "displayName" : "CHARLENE WHITNEY Graphic Designer 888 555 1212 someone@outlook.com www.contoso.com",
          "actionType" : "TextResults"
        }
      ],
      "sources" : ["OCR"]
    }
```

Om bilden innehåller en erkänd entitet, till exempel en kulturellt välkänd/populär person, plats eller sak, kan en av taggarna innehålla en entitetsinsikt. `mainEntity` Fälten `data` och är bara `enableEntityData` tillgängliga `Content-Type` om attributet `true`i huvudet är inställt på .

```json
{
  "image" : {
    "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?q=Statue+of+Liberty..."
  },
  "displayName" : "Statue of Liberty",
  "boundingBox" : {
    "queryRectangle" : {
      "topLeft" : {"x" : 0.40625, "y" : 0.1757813},
      "topRight" : {"x" : 0.6171875, "y" : 0.1757813},
      "bottomRight" : {"x" : 0.6171875, "y" : 0.3867188},
      "bottomLeft" : {"x" : 0.40625, "y" : 0.3867188}
    },
    "displayRectangle" : {
      "topLeft" : {"x" : 0.40625, "y" : 0.1757813},
      "topRight" : {"x" : 0.6171875, "y" : 0.1757813},
      "bottomRight" : {"x" : 0.6171875, "y" : 0.3867188},
      "bottomLeft" : {"x" : 0.40625, "y" : 0.3867188}
    }
  },
  "actions" : [
    {
      "_type" : "ImageEntityAction",
      "webSearchUrl" : "https:\/\/www.bing.com\/search?q=Statue+of+Liberty",
      "displayName" : "Statue of Liberty",
      "actionType" : "Entity",
      "mainEntity" : {
        "name" = "Statue of liberty",
        "bingId" : "..."
      },
      "data" : {
        "id" : "https://api.cognitive.microsoft.com/api/v7/entities/...",
        "readLink": "https://www.bingapis.com/api/v7/search?q=...",
        "readLinkPingSuffix": "...",
        "contractualRules": [
          {
            "_type": "ContractualRules/LicenseAttribution",
            "targetPropertyName": "description",
            "mustBeCloseToContent": true,
            "license": {
                "name": "CC-BY-SA",
                "url": "http://creativecommons.org/licenses/by-sa/3.0/",
                "urlPingSuffix": "..."
            },
            "licenseNotice": "Text under CC-BY-SA license"
          },
          {
            "_type": "ContractualRules/LinkAttribution",
            "targetPropertyName": "description",
            "mustBeCloseToContent": true,
            "text": "Wikipedia",
            "url": "http://en.wikipedia.org/wiki/...",
            "urlPingSuffix": "..."
          }
        ],
        "webSearchUrl": "https://www.bing.com/entityexplore?q=...",
        "webSearchUrlPingSuffix": "...",
        "name": "Statue of Liberty",
        "image": {
          "thumbnailUrl": "https://tse1.mm.bing.net/th?id=...",
          "hostPageUrl": "http://upload.wikimedia.org/wikipedia/...",
          "hostPageUrlPingSuffix": "...",
          "width": 50,
          "height": 50,
          "sourceWidth": 474,
          "sourceHeight": 598
        },
        "description" : "...",
        "bingId": "..."
        }
      }
  ]
}
```

## <a name="see-also"></a>Se även

- [Vad är API:et för visuell sökning i Bing?](../overview.md)
- [Självstudiekurs: Skapa en webbsida med ensidig visuell sökning](../tutorial-bing-visual-search-single-page-app.md)
