---
title: Lucene-frågesyntaxen
titleSuffix: Azure Cognitive Search
description: Referens för den fullständiga Lucene-frågesyntaxen, som används i Azure Cognitive Search for wildcard, fuzzy search, RegEx och andra avancerade frågekonstruktioner.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/10/2020
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: f4c3330b23b8b724cdbf5d7e09eec8a8dd5b8cfa
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81258991"
---
# <a name="lucene-query-syntax-in-azure-cognitive-search"></a>Lucene-frågesyntax i Azure Cognitive Search

Du kan skriva frågor mot Azure Cognitive Search baserat på den omfattande [Lucene Query Parser-syntaxen](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html) för specialiserade frågeformulär: jokertecken, fuzzy search, närhetssökning, reguljära uttryck är några exempel. Mycket av Lucene Query Parser-syntaxen [implementeras intakt i Azure Cognitive Search](search-lucene-query-architecture.md), med undantag för *intervallsökningar* som är konstruerade i Azure Cognitive Search via `$filter` uttryck. 

> [!NOTE]
> Den fullständiga Lucene-syntaxen används för **search** frågeuttryck som skickas i sökparametern för [API:et för sökdokument,](https://docs.microsoft.com/rest/api/searchservice/search-documents) som inte ska förväxlas med [syntaxen OData](query-odata-filter-orderby-syntax.md) som används för [parametern $filter](search-filters.md) för det API:et. Dessa olika syntaxer har sina egna regler för att konstruera frågor, fly strängar och så vidare.

## <a name="invoke-full-parsing"></a>Anropa fullständig tolkning

Ange `queryType` sökparametern för att ange vilken tolk som ska användas. Giltiga värden `simple|full`inkluderar `simple` , med `full` som standard och för Lucene. 

<a name="bkmk_example"></a> 

### <a name="example-showing-full-syntax"></a>Exempel som visar fullständig syntax

I följande exempel hittas dokument i indexet med hjälp `queryType=full` av lucene-frågesyntaxen, som är tydlig i parametern. Den här frågan returnerar hotell där kategorifältet innehåller termen "budget" och alla sökbara fält som innehåller frasen "nyligen renoverad". Dokument som innehåller frasen "nyrenoverad" rankas högre som ett resultat av termen boost värde (3).  

Parametern `searchMode=all` är relevant i det här exemplet. När operatorer finns på frågan bör `searchMode=all` du i allmänhet ställa in för att säkerställa att *alla* villkor matchas.

```
GET /indexes/hotels/docs?search=category:budget AND \"recently renovated\"^3&searchMode=all&api-version=2019-05-06&querytype=full
```

 Alternativt kan du använda POST:  

```
POST /indexes/hotels/docs/search?api-version=2019-05-06
{
  "search": "category:budget AND \"recently renovated\"^3",
  "queryType": "full",
  "searchMode": "all"
}
```

Ytterligare exempel finns i [Lucene-frågesyntaxexempel för att skapa frågor i Azure Cognitive Search](search-query-lucene-examples.md). Mer information om hur du anger den fullständiga kontingenten av frågeparametrar finns i [Sökdokument &#40;Azure Cognitive Search REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents).

> [!NOTE]  
>  Azure Cognitive Search stöder också [enkel frågesyntax](query-simple-syntax.md), ett enkelt och robust frågespråk som kan användas för enkel nyckelordssökning.  

##  <a name="syntax-fundamentals"></a><a name="bkmk_syntax"></a>Syntax fundamenta  

Följande syntaxgrunder gäller för alla frågor som använder syntaxen Lucene.  

### <a name="operator-evaluation-in-context"></a>Bedömning av operatörer i sitt sammanhang

Placering avgör om en symbol tolkas som en operator eller bara ett annat tecken i en sträng.

I lucene-hela syntax används tilde (~) till exempel för både fuzzy search och proximity search. När den placeras efter en citerad fras, ~ åberopar närhet sökning. När den placeras i slutet av en term, ~ åberopar luddiga sökning.

Inom en term, till exempel "business~analyst", utvärderas inte tecknet som en operator. I det här fallet, förutsatt att frågan är en term eller fras fråga, [fulltext sökning](search-lucene-query-architecture.md) med [lexikal analys](search-lucene-query-architecture.md#stage-2-lexical-analysis) remsor ut ~ och bryter termen "business ~ analytiker" i två: business OR analytiker.

Exemplet ovan är tilde (~), men samma princip gäller för alla operatorer.

### <a name="escaping-special-characters"></a>Fly bort specialtecken

För att kunna använda någon av sökoperatorerna som en del av söktexten, escape`\`tecknet genom att prefixa det med ett enda omvänt snedstreck ( ). För en jokerteckensökning på `https://`, `://` där är en del av `search=https\:\/\/*`frågesträngen, anger du till exempel . På samma sätt kan ett förrymt `\+1 \(800\) 642\-7676`telefonnummermönster se ut så här.

Specialtecken som kräver att du flyr innehåller följande:  
`+ - & | ! ( ) { } [ ] ^ " ~ * ? : \ /`  

> [!NOTE]  
> Även om flykten håller token tillsammans, [lexikal analys](search-lucene-query-architecture.md#stage-2-lexical-analysis) under indexering kan ta bort dem. Den vanliga Lucene-analysatorn bryter till exempel ord på bindestreck, blanktecken och andra tecken. Om du behöver specialtecken i frågesträngen kan du behöva en analysator som bevarar dem i indexet. Några alternativ är Microsofts av [analysatorer för](index-add-language-analyzers.md)naturligt språk , som bevarar avstavade ord eller en anpassad analysator för mer komplexa mönster. Mer information finns i [Partiella termer, mönster och specialtecken](search-query-partial-matching.md).

### <a name="encoding-unsafe-and-reserved-characters-in-urls"></a>Koda osäkra och reserverade tecken i webbadresser

Kontrollera att alla osäkra och reserverade tecken kodas i en URL. #' är till exempel ett osäkert tecken eftersom det är en fragment-/ankaridentifierare i en URL. Tecknet måste kodas `%23` till om det används i en URL. '&' och '=' är exempel på reserverade tecken när de avgränsar parametrar och anger värden i Azure Cognitive Search. Mer information finns i [RFC1738: Url (Uniform Resource Locators)](https://www.ietf.org/rfc/rfc1738.txt) för mer information.

Osäkra tecken ``" ` < > # % { } | \ ^ ~ [ ]``är . Reserverade `; / ? : @ = + &`tecken är .

###  <a name="query-size-limits"></a><a name="bkmk_querysizelimits"></a>Begränsningar för frågestorlek

 Det finns en gräns för storleken på frågor som du kan skicka till Azure Cognitive Search. Specifikt kan du ha högst 1024-satser (uttryck åtskilda av OCH, ELLER och så vidare). Det finns också en gräns på cirka 32 KB för storleken på en enskild term i en fråga. Om ditt program genererar sökfrågor programmässigt rekommenderar vi att du utformar det på ett sådant sätt att det inte genererar frågor av obegränsad storlek.  

### <a name="precedence-operators-grouping"></a>Prioritetsoperatorer (gruppering)

 Du kan använda parenteser för att skapa underfrågor, inklusive operatorer i den överordnade satsen. Till exempel `motel+(wifi||luxury)` kommer att söka efter dokument som innehåller "motell" sikt och antingen "wifi" eller "lyx" (eller båda).

Fältgruppering är liknande men omfattar grupperingen till ett enda fält. Till exempel `hotelAmenities:(gym+(wifi||pool))` söker fältet "hotelAmenities" för "gym" och "wifi", eller "gym" och "pool".  

##  <a name="boolean-search"></a><a name="bkmk_boolean"></a>Boolesk sökning

 Ange alltid booleska textoperatorer (OCH, ELLER, INTE) i versaler.  

### <a name="or-operator-or-or-"></a>ELLER-operatör `OR` eller`||`

OPERATORn ELLER är ett lodrät fält eller pipe-tecken. Till exempel: `wifi || luxury` kommer att söka efter dokument som innehåller antingen "wifi" eller "lyx" eller båda. Eftersom ELLER är standardsammansättningsoperatorn kan `wifi luxury` du också `wifi || luxury`utelämna det, så det motsvarar .

### <a name="and-operator-and--or-"></a>OCH-operatör `AND`, `&&` eller`+`

OPERATORn AND är ett et-tecken eller ett plustecken. Till exempel: `wifi && luxury` kommer att söka efter dokument som innehåller både "wifi" och "lyx". Plustecknet (+) används för obligatoriska termer. Anger till `+wifi +luxury` exempel att båda termerna måste visas någonstans i fältet för ett enda dokument.

### <a name="not-operator-not--or--"></a>`NOT`INTE-operator `!` , eller`-`

OPERATORN NOT är ett minustecken. Till exempel `wifi –luxury` kommer att söka `wifi` efter dokument som har `luxury`termen och /eller inte har .

Parametern **searchMode** på en frågebegäran styr om en term med operatorn INTE är ANDed eller `+` ORed med andra termer i frågan (förutsatt att det inte finns någon eller `|` operator på de andra termerna). Giltiga värden `any` `all`inkluderar eller .

`searchMode=any`ökar återkallandet av frågor genom att inkludera `-` fler resultat, och som standard tolkas som "ELLER INTE". Till exempel `wifi -luxury` matchar dokument som antingen `wifi` innehåller termen eller de `luxury`som inte innehåller termen .

`searchMode=all`ökar precisionen i frågor genom att inkludera färre resultat, och som standard - kommer att tolkas som "OCH INTE". Till exempel `wifi -luxury` matchar dokument som `wifi` innehåller termen och inte innehåller termen "lyx". Detta är utan tvekan ett `-` mer intuitivt beteende för operatören. Därför bör du `searchMode=all` överväga `searchMode=any` att använda i stället för om du vill optimera sökningar `-` efter precision i stället för återkallande, *och* användarna använder ofta operatören i sökningar.

När du bestämmer dig för en **searchMode-inställning** bör du tänka på användarinteraktionsmönster för frågor i olika program. Användare som söker efter information är mer benägna att inkludera en operatör i en fråga, i motsats till e-handelswebbplatser som har mer inbyggda navigeringsstrukturer.

##  <a name="fielded-search"></a><a name="bkmk_fields"></a>Sökning i fält

Du kan definiera en fältsökåtgärd med syntaxen, `fieldName:searchExpression` där sökuttrycket kan vara ett enda ord eller en fras, eller ett mer komplext uttryck inom parenteser, eventuellt med booleska operatorer. Några exempel är följande:  

- genre:jazz INTE historia  

- artister:("Miles Davis" "John Coltrane")

Var noga med att placera flera strängar inom citattecken om du vill att båda strängarna ska utvärderas som en enda entitet, i det här fallet söker efter två olika artister i `artists` fältet.  

Det fält som `fieldName:searchExpression` anges `searchable` i måste vara ett fält.  Mer information om hur indexattribut används i fältdefinitioner finns i [Skapa index.](https://docs.microsoft.com/rest/api/searchservice/create-index)  

> [!NOTE]
> När du använder fältade sökuttryck behöver `searchFields` du inte använda parametern eftersom varje fältsökuttryck har ett fältnamn uttryckligen angivet. Du kan dock fortfarande `searchFields` använda parametern om du vill köra en fråga där vissa delar är begränsade till ett visst fält, och resten kan gälla för flera fält. Frågan `search=genre:jazz NOT history&searchFields=description` matchar till exempel `jazz` bara `genre` fältet, medan den `NOT history` `description` matchar fältet. Fältnamnet som `fieldName:searchExpression` anges i har `searchFields` alltid företräde framför parametern, vilket är anledningen till att vi i det här exemplet inte behöver inkludera `genre` i parametern. `searchFields`

##  <a name="fuzzy-search"></a><a name="bkmk_fuzzy"></a>Fuzzy sökning

En suddig sökning hittar matchningar i termer som har en liknande konstruktion, utöka en term upp till maximalt 50 termer som uppfyller avståndskriterierna för två eller mindre. Mer information finns i [Fuzzy search](search-query-fuzzy.md).

 Om du vill göra en suddig sökning använder du tilde "~"-symbolen i slutet av ett enda ord med en valfri parameter, ett tal mellan 0 och 2 (standard), som anger redigeringsavståndet. Till exempel skulle "blå~" eller "blå~1" returnera "blå", "blues" och "lim".

 Luddig sökning kan bara tillämpas på termer, inte fraser, men du kan lägga tilde till varje term individuellt i ett flerdelat namn eller en fras. Till exempel skulle "Unviersty~ av ~ "Wshington ~" matcha på "University of Washington".
 
##  <a name="proximity-search"></a><a name="bkmk_proximity"></a>Sök efter närhet

Närhetssökningar används för att hitta termer som är nära varandra i ett dokument. Infoga en tilde "~"-symbol i slutet av en fras följt av antalet ord som skapar närhetsgränsen. Till exempel `"hotel airport"~5` hittar termerna "hotell" och "flygplats" inom 5 ord från varandra i ett dokument.  


##  <a name="term-boosting"></a><a name="bkmk_termboost"></a>Term öka

Termökning refererar till rangordning av ett dokument högre om det innehåller den marknadsförda termen, i förhållande till dokument som inte innehåller termen. Detta skiljer sig från bedömningsprofiler genom att bedömningsprofiler marknadsför vissa fält i stället för specifika termer.  

Följande exempel illustrerar skillnaderna. Anta att det finns en poängprofil som ökar matchningar inom ett visst område, säger *genren* i [exemplet musicstoreindex](index-add-scoring-profiles.md#bkmk_ex). Term öka kan användas för att ytterligare öka vissa söktermer högre än andra. Till exempel `rock^2 electronic` kommer att öka dokument som innehåller söktermer i genrefältet högre än andra sökbara fält i indexet. Vidare kommer dokument som innehåller *sökordsstenen* att rankas högre än den andra *sökordsen elektronisk* som ett resultat av termen boost-värde (2).  

 Om du vill öka en term använder du careten "^", symbol med en boostfaktor (ett tal) i slutet av termen du söker. Du kan också öka fraser. Ju högre boostfaktor, desto mer relevant kommer termen att vara i förhållande till andra söktermer. Som standard är boostfaktorn 1. Även om boostfaktorn måste vara positiv kan den vara mindre än 1 (till exempel 0,20).  

##  <a name="regular-expression-search"></a><a name="bkmk_regex"></a>Sökning i reguljära uttryck  
 En sökning i reguljära uttryck hittar en matchning baserat på innehållet mellan snedstrecken "/", som dokumenteras i [klassen RegExp](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/util/automaton/RegExp.html).  

 Om du till exempel vill hitta dokument som innehåller `/[mh]otel/`"motell" eller "hotell" anger du . Sökningar med reguljära uttryck matchas mot enstaka ord.

Vissa verktyg och språk ställer ytterligare krav på escape-tecken. För JSON, strängar som innehåller en framåt snedstreck undgås med en `search=/.*microsoft.com\/azure\/.*/` `search=/.* <string-placeholder>.*/` bakåtstjuv: `microsoft.com\/azure\/` "microsoft.com/azure/" blir där ställer in det reguljära uttrycket, och är strängen med en förrymd framåt snedstreck.

##  <a name="wildcard-search"></a><a name="bkmk_wildcard"></a>Jokertecken sökning  

Du kan använda allmänt erkända syntax för jokerteckensökningar med flera (*) eller enstaka (?). Den Lucene-frågetolken stöder användningen av dessa symboler med en enda term och inte en fras.

Prefixsökning använder också asterisktecknet (`*`). Ett frågeuttryck för `search=note*` returnerar till exempel "anteckningsbok" eller "anteckningsblock". Fullständig Lucene-syntax krävs inte för prefixsökning. Den enkla syntaxen stöder det här scenariot.

Suffixsökning, `*` där `?` eller föregår strängen, kräver fullständig Lucene-syntax och ett reguljärt uttryck (du kan inte använda en * eller ? som det första tecknet i en sökning). Med tanke på termen "alfanumerisk"`search=/.*numeric.*/`hittar ett frågeuttryck för ( ) matchningen.

> [!NOTE]  
> Under frågetolkning skickas frågor som är formulerade som prefix, suffix, jokertecken eller reguljära uttryck som är till frågeträdet och kringgår [lexikal analys](search-lucene-query-architecture.md#stage-2-lexical-analysis). Matchningar hittas bara om indexet innehåller strängarna i det format som frågan anger. I de flesta fall behöver du en alternativ analysator under indexering som bevarar strängintegritet så att partiell term- och mönstermatchning lyckas. Mer information finns [i Partiell termsökning i Azure Cognitive Search-frågor](search-query-partial-matching.md).

##  <a name="scoring-wildcard-and-regex-queries"></a><a name="bkmk_searchscoreforwildcardandregexqueries"></a>Bedömning av jokertecken och regex-frågor

Azure Cognitive Search använder frekvensbaserad bedömning ([TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf)) för textfrågor. Men för jokertecken och regex frågor där omfattningen av termer kan potentiellt vara bred, är frekvensfaktorn ignoreras för att förhindra rangordningen från partiskhet mot matcher från ovanligare termer. Alla matchningar behandlas lika för jokertecken och regex-sökningar.

## <a name="see-also"></a>Se även

+ [Frågeexempel för enkel sökning](search-query-simple-examples.md)
+ [Frågeexempel för fullständig Lucene-sökning](search-query-lucene-examples.md)
+ [Sök dokument](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
+ [OData-uttryckssyntax för filter och sortering](query-odata-filter-orderby-syntax.md)   
+ [Enkel frågesyntax i Azure Cognitive Search](query-simple-syntax.md)   
