---
title: Snabbstart för Bing Web Search Python-klientbibliotek
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/05/2020
ms.author: aahi
ms.openlocfilehash: b6ab34019207ccbeec5448b848e501e063707d16
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81384903"
---
Bing Web Search-klientbiblioteket gör det enkelt att integrera Bing Web Search i python-programmet. I den här snabbstarten lär du dig att skicka en begäran, ta emot ett JSON-svar och filtrera och parsa resultaten.

Vill du se koden på en gång? Exempel för [Bing Search-klientbiblioteken för Python](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/tree/master/samples/search) finns på GitHub.


## <a name="prerequisites"></a>Krav
API för webbsökning i Bing är kompatibelt med Python 2.7, 3.3, 3.4, 3.5 och 3.6. Vi rekommenderar att du använder en virtuell miljö för den här snabbstarten.

* Python 2.7, 3.3, 3.4, 3.5 eller 3.6
* [virtualenv](https://docs.python.org/3/tutorial/venv.html) för Python 2.7
* [venv](https://pypi.python.org/pypi/virtualenv) för Python 3.x

[!INCLUDE [bing-web-search-quickstart-signup](~/includes/bing-web-search-quickstart-signup.md)]

## <a name="create-and-configure-your-virtual-environment"></a>Skapa och konfigurera den virtuella miljön

Anvisningarna för att installera och konfigurera en virtuell miljö är olika för Python 2.x och Python 3.x. Följ stegen nedan för att skapa och initiera den virtuella miljön.

### <a name="python-2x"></a>Python 2.x

Skapa en virtuell miljö med `virtualenv` för Python 2.7:

```console
virtualenv mytestenv
```

Aktivera miljön:

```console
cd mytestenv
source bin/activate
```

Installera beroenden för SDK för Webbsökning i Bing:

```console
python -m pip install azure-cognitiveservices-search-websearch
```

### <a name="python-3x"></a>Python 3.x

Skapa en virtuell miljö med `venv` för Python 3.x:

```console
python -m venv mytestenv
```

Aktivera miljön:

```console
mytestenv\Scripts\activate.bat
```

Installera beroenden för SDK för Webbsökning i Bing:

```console
cd mytestenv
python -m pip install azure-cognitiveservices-search-websearch
```

## <a name="create-a-client-and-print-your-first-results"></a>Skapa en klient och skriva ut dina första resultat

Nu när du har konfigurerat den virtuella miljön och installerat beroenden tar vi och skapar en klient. Klienten hanterar begäranden till och svar från API för webbsökning i Bing.

Om svaret innehåller webbplatser, bilder, nyheter eller videor skrivs det första resultatet för vardera ut.

1. Skapa ett nytt Python-projekt med valfri IDE eller redigeringsprogram.

1. Kopiera den här exempelkoden till projektet. `endpoint`kan vara den globala slutpunkten nedan eller den [anpassade underdomänslutpunkten](~/articles/cognitive-services/cognitive-services-custom-subdomains.md) som visas i Azure-portalen för din resurs.:  

    ```python
    # Import required modules.
    from azure.cognitiveservices.search.websearch import WebSearchClient
    from azure.cognitiveservices.search.websearch.models import SafeSearch
    from msrest.authentication import CognitiveServicesCredentials

    # Replace with your subscription key.
    subscription_key = "YOUR_SUBSCRIPTION_KEY"

    # Instantiate the client and replace with your endpoint.
    client = WebSearchClient(endpoint="YOUR_ENDPOINT", credentials=CognitiveServicesCredentials(subscription_key))

    # Make a request. Replace Yosemite if you'd like.
    web_data = client.web.search(query="Yosemite")
    print("\r\nSearched for Query# \" Yosemite \"")

    '''
    Web pages
    If the search response contains web pages, the first result's name and url
    are printed.
    '''
    if hasattr(web_data.web_pages, 'value'):

        print("\r\nWebpage Results#{}".format(len(web_data.web_pages.value)))

        first_web_page = web_data.web_pages.value[0]
        print("First web page name: {} ".format(first_web_page.name))
        print("First web page URL: {} ".format(first_web_page.url))

    else:
        print("Didn't find any web pages...")

    '''
    Images
    If the search response contains images, the first result's name and url
    are printed.
    '''
    if hasattr(web_data.images, 'value'):

        print("\r\nImage Results#{}".format(len(web_data.images.value)))

        first_image = web_data.images.value[0]
        print("First Image name: {} ".format(first_image.name))
        print("First Image URL: {} ".format(first_image.url))

    else:
        print("Didn't find any images...")

    '''
    News
    If the search response contains news, the first result's name and url
    are printed.
    '''
    if hasattr(web_data.news, 'value'):

        print("\r\nNews Results#{}".format(len(web_data.news.value)))

        first_news = web_data.news.value[0]
        print("First News name: {} ".format(first_news.name))
        print("First News URL: {} ".format(first_news.url))

    else:
        print("Didn't find any news...")

    '''
    If the search response contains videos, the first result's name and url
    are printed.
    '''
    if hasattr(web_data.videos, 'value'):

        print("\r\nVideos Results#{}".format(len(web_data.videos.value)))

        first_video = web_data.videos.value[0]
        print("First Videos name: {} ".format(first_video.name))
        print("First Videos URL: {} ".format(first_video.url))

    else:
        print("Didn't find any videos...")
    ```

1. Ersätt `SUBSCRIPTION_KEY` med en giltig prenumerationsnyckel.

1. Ersätt `YOUR_ENDPOINT` med slutpunktsadressen i portalen.

1. Kör programmet. Till exempel: `python your_program.py`.

## <a name="define-functions-and-filter-results"></a>Definiera funktioner och filtrera resultat

Nu när du har ringt ditt första samtal till API:et för webbsökning på Bing ska vi titta på några funktioner. I följande avsnitt framhävs SDK-funktioner för att förfina frågor och filtrera resultat. Varje funktion kan läggas till i Python-programmet som du skapade i föregående avsnitt.

### <a name="limit-the-number-of-results-returned-by-bing"></a>Begränsa antalet resultat som returneras av Bing

Det här `count` exemplet `offset` använder parametrarna och för att begränsa antalet [ `search` ](https://docs.microsoft.com/python/api/azure-cognitiveservices-search-websearch/azure.cognitiveservices.search.websearch.operations.weboperations?view=azure-python)resultat som returneras med hjälp av SDK:s metod . `name` och `url` för det första resultatet skrivs ut.

1. Lägg till den här koden i Python-projektet:

   ```python
    # Declare the function.
    def web_results_with_count_and_offset(subscription_key):
        client = WebSearchAPI(CognitiveServicesCredentials(subscription_key))

        try:
            '''
            Set the query, offset, and count using the SDK's search method. See:
            https://docs.microsoft.com/python/api/azure-cognitiveservices-search-websearch/azure.cognitiveservices.search.websearch.operations.weboperations?view=azure-python.
            '''
            web_data = client.web.search(query="Best restaurants in Seattle", offset=10, count=20)
            print("\r\nSearching for \"Best restaurants in Seattle\"")

            if web_data.web_pages.value:
                '''
                If web pages are available, print the # of responses, and the first and second
                web pages returned.
                '''
                print("Webpage Results#{}".format(len(web_data.web_pages.value)))

                first_web_page = web_data.web_pages.value[0]
                print("First web page name: {} ".format(first_web_page.name))
                print("First web page URL: {} ".format(first_web_page.url))

            else:
                print("Didn't find any web pages...")

        except Exception as err:
            print("Encountered exception. {}".format(err))
    ```

1. Kör programmet.

### <a name="filter-for-news-and-freshness"></a>Filtrera efter nyheter och aktualitet

Det här `response_filter` exemplet `freshness` använder parametrarna och för att [ `search` ](/python/api/azure-cognitiveservices-search-websearch/azure.cognitiveservices.search.websearch.operations.weboperations)filtrera sökresultat med hjälp av SDK:s metod . De sökresultat som returneras är begränsade till nyhetsartiklar och sidor som Bing har identifierat under de senaste 24 timmarna. `name` och `url` för det första resultatet skrivs ut.

1. Lägg till den här koden i Python-projektet:

    ```python
    # Declare the function.
    def web_search_with_response_filter(subscription_key):
        client = WebSearchAPI(CognitiveServicesCredentials(subscription_key))
        try:
            '''
            Set the query, response_filter, and freshness using the SDK's search method. See:
            https://docs.microsoft.com/python/api/azure-cognitiveservices-search-websearch/azure.cognitiveservices.search.websearch.operations.weboperations?view=azure-python.
            '''
            web_data = client.web.search(query="xbox",
                response_filter=["News"],
                freshness="Day")
            print("\r\nSearching for \"xbox\" with the response filter set to \"News\" and freshness filter set to \"Day\".")

            '''
            If news articles are available, print the # of responses, and the first and second
            articles returned.
            '''
            if web_data.news.value:

                print("# of news results: {}".format(len(web_data.news.value)))

                first_web_page = web_data.news.value[0]
                print("First article name: {} ".format(first_web_page.name))
                print("First article URL: {} ".format(first_web_page.url))

                print("")

                second_web_page = web_data.news.value[1]
                print("\nSecond article name: {} ".format(second_web_page.name))
                print("Second article URL: {} ".format(second_web_page.url))

            else:
                print("Didn't find any news articles...")

        except Exception as err:
            print("Encountered exception. {}".format(err))

    # Call the function.
    web_search_with_response_filter(subscription_key)
    ```

1. Kör programmet.

### <a name="use-safe-search-answer-count-and-the-promote-filter"></a>Använd säker sökning, svarsantal och befordringsfiltret

I `answer_count`det här `promote`exemplet `safe_search` används , och parametrar för att filtrera sökresultat med hjälp av SDK:s [ `search` metod](https://docs.microsoft.com/python/api/azure-cognitiveservices-search-websearch/azure.cognitiveservices.search.websearch.operations.weboperations?view=azure-python). `name` och `url` för det första resultatet visas.

1. Lägg till den här koden i Python-projektet:

    ```python
    # Declare the function.
    def web_search_with_answer_count_promote_and_safe_search(subscription_key):

        client = WebSearchAPI(CognitiveServicesCredentials(subscription_key))

        try:
            '''
            Set the query, answer_count, promote, and safe_search parameters using the SDK's search method. See:
            https://docs.microsoft.com/python/api/azure-cognitiveservices-search-websearch/azure.cognitiveservices.search.websearch.operations.weboperations?view=azure-python.
            '''
            web_data = client.web.search(
                query="Niagara Falls",
                answer_count=2,
                promote=["videos"],
                safe_search=SafeSearch.strict  # or directly "Strict"
            )
            print("\r\nSearching for \"Niagara Falls\"")

            '''
            If results are available, print the # of responses, and the first result returned.
            '''
            if web_data.web_pages.value:

                print("Webpage Results#{}".format(len(web_data.web_pages.value)))

                first_web_page = web_data.web_pages.value[0]
                print("First web page name: {} ".format(first_web_page.name))
                print("First web page URL: {} ".format(first_web_page.url))

            else:
                print("Didn't see any Web data..")

        except Exception as err:
            print("Encountered exception. {}".format(err))
    ```

1. Kör programmet.

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar med det här projektet bör du ta bort din prenumerationsnyckel från programmets kod och inaktivera den virtuella miljön.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Exempel med Cognitive Services SDK för Python](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)

## <a name="see-also"></a>Se även

* [Azure Python SDK-referens](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/websearch)
