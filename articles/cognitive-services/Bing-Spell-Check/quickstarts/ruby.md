---
title: 'Snabbstart: Kontrollera stavning med REST API och Ruby - Bing Stavningskontroll'
titleSuffix: Azure Cognitive Services
description: Kom igång med REST-API:et för stavningskontroll för stavningskontroll för att kontrollera stavning och grammatik med den här snabbstarten.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 12/16/2019
ms.author: aahi
ms.openlocfilehash: 89a2a345e2a4e3ca1be31297e614e86f800e6316
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75448422"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-ruby"></a>Snabbstart: Kontrollera stavning med Bing Stavningskontroll REST API och Ruby

Använd den här snabbstarten för att skicka ditt första anrop till REST API för stavningskontroll i Bing med hjälp av Ruby. Det här enkla programmet skickar en begäran till API:et och returnerar en lista över ord som det inte kände igen följt av föreslagna korrigeringar. Även om det här programmet är skrivet i Ruby, är API:n en RESTful-webbtjänst som är kompatibel med de flesta programmeringsspråk. Källkoden till det här programmet finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/ruby/Search/BingSpellCheckv7.rb)

## <a name="prerequisites"></a>Krav

* [Ruby 2.4](https://www.ruby-lang.org/en/downloads/) eller senare.

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]


## <a name="create-and-initialize-the-application"></a>Skapa och initiera appen

1. Skapa en ny Ruby-fil i valfri IDE eller redigeringsprogram och lägg till följande krav. 

    ```ruby
    require 'net/http'
    require 'uri'
    require 'json'
    ```

2. Skapa variabler för din prenumerationsnyckel, slutpunkts-URI och sökväg. Skapa dina begärandeparametrar genom att lägga till parametern `mkt=` i din marknad samt `&mode` till bevisläget `proof`. Du kan använda den globala slutpunkten nedan eller den [anpassade underdomänslutpunkten](../../../cognitive-services/cognitive-services-custom-subdomains.md) som visas i Azure-portalen för din resurs.

    ```ruby
    key = 'ENTER YOUR KEY HERE'
    uri = 'https://api.cognitive.microsoft.com'
    path = '/bing/v7.0/spellcheck?'
    params = 'mkt=en-us&mode=proof'
    ```

## <a name="send-a-spell-check-request"></a>Skicka en begäran om stavningskontroll

1. Skapa en URI från din värd-URI, sökväg och parametersträng. Ange att frågan ska innehålla den text som du vill stavningskontrollen.

   ```ruby
   uri = URI(uri + path + params)
   uri.query = URI.encode_www_form({
      # Request parameters
   'text' => 'Hollo, wrld!'
   })
   ```

2. Skapa en begäran med hjälp av den URI som skapats ovan. Lägg till nyckeln till `Ocp-Apim-Subscription-Key`-rubriken.

    ```ruby
    request = Net::HTTP::Post.new(uri)
    request['Content-Type'] = "application/x-www-form-urlencoded"
    request['Ocp-Apim-Subscription-Key'] = key
    ```

3. Skicka begäran.

    ```ruby
    response = Net::HTTP.start(uri.host, uri.port, :use_ssl => uri.scheme == 'https') do |http|
        http.request(request)
    end
    ```

4. Hämta JSON-svaret och skriv ut det till konsolen. 

    ```ruby
    result = JSON.pretty_generate(JSON.parse(response.body))
    puts result
    ```

## <a name="run-the-application"></a>Köra appen

Bygg och kör ditt projekt.

Om du använder kommandoraden använder du följande kommando för att köra programmet.

```bash
ruby <FILE_NAME>.rb
```

## <a name="example-json-response"></a>Exempel på JSON-svar

Ett svar som anger att åtgärden lyckades returneras i JSON, som du ser i följande exempel: 

```json
{
   "_type": "SpellCheck",
   "flaggedTokens": [
      {
         "offset": 0,
         "token": "Hollo",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "Hello",
               "score": 0.9115257530801
            },
            {
               "suggestion": "Hollow",
               "score": 0.858039839213461
            },
            {
               "suggestion": "Hallo",
               "score": 0.597385084464481
            }
         ]
      },
      {
         "offset": 7,
         "token": "wrld",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "world",
               "score": 0.9115257530801
            }
         ]
      }
   ]
}
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa en webbapp med en sida](../tutorials/spellcheck.md)

- [Vad är API:et för stavningskontroll i Bing?](../overview.md)
- [API-referens för stavningskontroll i Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference)
