---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: 6c72cd270e634ca0aebff7c17d5663241428e182
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "69907103"
---
[!INCLUDE [Prerequisites](prerequisites-python.md)]

[!INCLUDE [Set up and use environment variables](setup-env-variables.md)]

## <a name="create-a-project-and-import-required-modules"></a>Skapa ett projekt och importera nödvändiga moduler

Skapa ett nytt Python-projekt med din favorit-IDE eller redigerare, eller skapa en ny mapp på skrivbordet. Kopiera kodavsnittet till projektet/mappen till en `dictionary-lookup.py`fil med namnet .

```python
# -*- coding: utf-8 -*-
import os, requests, uuid, json
```

> [!NOTE]
> Om du inte har använt de här modulerna behöver du installera dem innan du kör programmet. För att installera de här paketen kör du: `pip install requests uuid`.

Den första kommentaren instruerar Python-tolken att använda UTF-8-kodning. Sedan importeras de moduler som krävs för att läsa prenumerationsnyckeln från en miljövariabel, skapa HTTP-begäran, skapa en unik identifierare samt hantera det JSON-svar som returneras av Translator Text API.

## <a name="set-the-subscription-key-endpoint-and-path"></a>Ange prenumerationsnyckel, slutpunkt och sökväg

Det här exemplet försöker läsa din prenumerationsnyckel för translatortext `TRANSLATOR_TEXT_KEY` `TRANSLATOR_TEXT_ENDPOINT`och slutpunkt från miljövariablerna: och . Om du inte är bekant med miljövariabler kan du ange `subscription_key` och `endpoint` som strängar och kommentera villkorliga satser.

Kopiera den här koden till projektet:

```python
key_var_name = 'TRANSLATOR_TEXT_SUBSCRIPTION_KEY'
if not key_var_name in os.environ:
    raise Exception('Please set/export the environment variable: {}'.format(key_var_name))
subscription_key = os.environ[key_var_name]

endpoint_var_name = 'TRANSLATOR_TEXT_ENDPOINT'
if not endpoint_var_name in os.environ:
    raise Exception('Please set/export the environment variable: {}'.format(endpoint_var_name))
endpoint = os.environ[endpoint_var_name]
```

Den globala slutpunkten för Translator Text anges som `endpoint`. `path` anger `dictionary/lookup`-vägen och identifierar att vi vill nå version 3 av API:et.

`params` används till att ange källan och utdataspråken. I det här exemplet använder vi engelska och spanska: `en` och `es`.

>[!NOTE]
> Mer information om slutpunkter, vägar och begärandeparametrar finns i [Translator Text API 3.0: Ordlista, sökning](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-dictionary-lookup).

```python
path = '/dictionary/lookup?api-version=3.0'
params = '&from=en&to=es'
constructed_url = endpoint + path + params
```

## <a name="add-headers"></a>Lägga till sidhuvuden

Det enklaste sättet att autentisera en begäran är att skicka din prenumerationsnyckel som ett `Ocp-Apim-Subscription-Key`-sidhuvud, vilket är det vi använder i det här exemplet. Alternativt kan du byta din prenumerationsnyckel mot en åtkomsttoken och skicka vidare åtkomsttoken som ett `Authorization`-sidhuvud för att verifiera din begäran. Mer information finns i [Autentisering](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

Kopiera det här kodavsnittet till projektet:

```python
headers = {
    'Ocp-Apim-Subscription-Key': subscription_key,
    'Content-type': 'application/json',
    'X-ClientTraceId': str(uuid.uuid4())
}
```

Om du använder en prenumeration med flera tjänster för `Ocp-Apim-Subscription-Region` Cognitive Services måste du också inkludera parametrarna i din begäran. [Läs mer om att autentisera med prenumerationen med flera tjänster](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

## <a name="create-a-request-to-find-alternate-translations"></a>Skapa en begäran om att hitta alternativa översättningar

Definiera den sträng (eller de strängar) som du vill hitta översättningar för:

```python
# You can pass more than one object in body.
body = [{
    'text': 'Elephants'
}]
```

Nu ska vi skapar en POST-begäran med hjälp av `requests`-modulen. Den tar tre argument: den sammanfogade URL:en, begärandesidhuvudena samt begärandetexten:

```python
request = requests.post(constructed_url, headers=headers, json=body)
response = request.json()
```

## <a name="print-the-response"></a>Skriva ut svaret

Det sista steget är att skriva ut resultatet. Det här kodavsnittet gör resultatet prydligare genom att sortera nycklarna, konfigurera indrag samt deklarera objekt- och nyckelseparatorer.

```python
print(json.dumps(response, sort_keys=True, indent=4,
                 ensure_ascii=False, separators=(',', ': ')))
```

## <a name="put-it-all-together"></a>Färdigställa allt

Det var allt – du har skapat ett enkelt program som anropar Translator Text API och returnerar en JSON-svar. Nu är det dags att köra programmet:

```console
python alt-translations.py
```

Om du vill jämföra din kod med vår finns det fullständiga exemplet på [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Python).

## <a name="sample-response"></a>Exempelsvar

```json
[
    {
        "displaySource": "elephants",
        "normalizedSource": "elephants",
        "translations": [
            {
                "backTranslations": [
                    {
                        "displayText": "elephants",
                        "frequencyCount": 1207,
                        "normalizedText": "elephants",
                        "numExamples": 5
                    }
                ],
                "confidence": 1.0,
                "displayTarget": "elefantes",
                "normalizedTarget": "elefantes",
                "posTag": "NOUN",
                "prefixWord": ""
            }
        ]
    }
]
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du har hårdkodat din prenumerationsnyckel i programmet ser du till att ta bort prenumerationsnyckeln när du är klar med den här snabbstarten.

## <a name="next-steps"></a>Nästa steg

Ta en titt på API-referensen för att förstå allt du kan göra med Translator Text API.

> [!div class="nextstepaction"]
> [API-referens](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
