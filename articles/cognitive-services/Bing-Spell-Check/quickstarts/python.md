---
title: 'Snabbstart: Kontrollera stavning med REST API och Python - Bing Stavningskontroll'
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
ms.openlocfilehash: 6b0977628f7c3d971804d8597f42425608028081
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75448462"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-python"></a>Snabbstart: Kontrollera stavning med Bing Spell Check REST API och Python

Använd den här snabbstarten för att göra ditt första anrop till REST API för stavningskontroll i Bing. Det här enkla Python-programmet skickar en begäran till API:et och returnerar en lista över föreslagna korrigeringar. Även om det här programmet är skrivet i Python, är API:n en RESTful-webbtjänst som är kompatibel med de flesta programmeringsspråk. Källkoden till det här programmet finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingEntitySearchv7.py)

## <a name="prerequisites"></a>Krav

* Python [3.x](https://www.python.org)

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]

## <a name="initialize-the-application"></a>Initiera programmet

1. Skapa en ny Python-fil i valfri IDE eller redigeringsprogram och lägg till följande importinstruktion.

   ```python
   import requests
   import json
   ```

2. Skapa variabler för den text som du vill stavningskontrollera, prenumerationsnyckeln och slutpunkten för stavningskontroll i Bing. Du kan använda den globala slutpunkten nedan eller den [anpassade underdomänslutpunkten](../../../cognitive-services/cognitive-services-custom-subdomains.md) som visas i Azure-portalen för din resurs.

    ```python
    api_key = "<ENTER-KEY-HERE>"
    example_text = "Hollo, wrld" # the text to be spell-checked
    endpoint = "https://api.cognitive.microsoft.com/bing/v7.0/SpellCheck"
    ```

## <a name="create-the-parameters-for-the-request"></a>Skapa parametrarna för begäran

1. Skapa en ny ordlista med `text` som nyckel och texten som värde.

    ```python
    data = {'text': example_text}
    ```

2. Lägg till parametrarna för begäran. Lägg till din `mkt=`marknadskod efter . Marknadskoden är det land du gör begäran från. Lägg också till ditt stavningskontrollläge efter `&mode=`. Läget är `proof` antingen (fångar de flesta stav-/grammatikfel) eller `spell` (fångar mest stavning men inte så många grammatikfel).

    ```python
    params = {
        'mkt':'en-us',
        'mode':'proof'
        }
    ```

3. Lägg till ett `Content-Type`-huvud och din prenumerationsnyckel i `Ocp-Apim-Subscription-Key`-huvudet.

    ```python
    headers = {
        'Content-Type': 'application/x-www-form-urlencoded',
        'Ocp-Apim-Subscription-Key': api_key,
        }
    ```

## <a name="send-the-request-and-read-the-response"></a>Skicka begäran och läsa svaret

1. Skicka POST-begäran med hjälp av biblioteket för begäranden.

    ```python
    response = requests.post(endpoint, headers=headers, params=params, data=data)
    ```

2. Hämta JSON-svaret och skriv ut det.

    ```python
    json_response = response.json()
    print(json.dumps(json_response, indent=4))
    ```


## <a name="run-the-application"></a>Köra appen

Om du använder kommandoraden använder du följande kommando för att köra programmet.

```bash
python <FILE_NAME>.py
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
