---
title: Snabbstart för Bing Video Search Python-klientbibliotek
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/19/2020
ms.author: aahi
ms.openlocfilehash: 7a9fab8ba8bb9d21c9284cbf14bc67226d2ef9d3
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80289744"
---
Använd den här snabbstarten för att börja söka efter nyheter med klientbiblioteket Bing Video Search för Python. Bing Video Search har ett REST API som är kompatibelt med de flesta programmeringsspråk, men klientbiblioteket är ett enkelt sätt att integrera tjänsten i dina program. Källkoden för det här exemplet finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/video_search_samples.py) med ytterligare anteckningar och funktioner.

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](~/includes/cognitive-services-bing-video-search-signup-requirements.md)]

## <a name="prerequisites"></a>Krav

- [Python (svenska)](https://www.python.org/) 2.x eller 3.x
- Klientbiblioteket Bing Video Search för python

Vi rekommenderar att du använder en [virtuell Python-miljö](https://docs.python.org/3/tutorial/venv.html). Du kan installera och initiera den virtuella miljön med [venv-modulen](https://pypi.python.org/pypi/virtualenv). Installera virtualenv för Python 2.7 med:

```console
python -m venv mytestenv
```

Installera klientbiblioteket för Bing Video Search med:

```console
cd mytestenv
python -m pip install azure-cognitiveservices-search-videosearch
```

## <a name="create-and-initialize-the-application"></a>Skapa och initiera appen

1. Skapa en ny Python-fil i valfri IDE eller redigeringsprogram och lägg till följande importinstruktioner. 

    ```python
    from azure.cognitiveservices.search.videosearch import VideoSearchClient
    from azure.cognitiveservices.search.videosearch.models import VideoPricing, VideoLength, VideoResolution, VideoInsightModule
    from msrest.authentication import CognitiveServicesCredentials
    ```

2. Skapa en variabel för din prenumerationsnyckel. 

    ```python
    subscription_key = "YOUR-SUBSCRIPTION-KEY"
    endpoint = "YOUR-ENDPOINT"
    ```

## <a name="create-the-search-client"></a>Skapa sökklienten

Skapa en instans av `CognitiveServicesCredentials` och instantiera klienten:

```python
client = VideoSearchAPI(endpoint, CognitiveServicesCredentials(subscription_key))
```

## <a name="send-a-search-request-and-get-a-response"></a>Skicka en förfrågan och få ett svar

1. Använd `client.videos.search()` med sökfrågan för att skicka en förfrågan till API:t Videosökning i Bing och få ett svar.

    ```python
    video_result = client.videos.search(query="SwiftKey")
    ```

2. Om svaret innehåller sökresultat tar du det första och skriver ut ID, namn och webbadress.

    ```python
    if video_result.value:
        first_video_result = video_result.value[0]
        print("Video result count: {}".format(len(video_result.value)))
        print("First video id: {}".format(first_video_result.video_id))
        print("First video name: {}".format(first_video_result.name))
        print("First video url: {}".format(first_video_result.content_url))
    else:
        print("Didn't see any video result data..")
    ```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa en webbapp med en sida](../../tutorial-bing-video-search-single-page-app.md)

## <a name="see-also"></a>Se även 

- [Vad är API:et för videosökning i Bing?](../../overview.md)
- [Kognitiva tjänster .NET SDK-exempel](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)
