---
title: Frågeslutpunkt för behållare för text till tal
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: b2a621a23a81e4fb4f47e7c99d780211973e30a0
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81275585"
---
Behållaren tillhandahåller [REST-baserade endpoint API:er](../rest-text-to-speech.md). Det finns många [exempel på källkodsprojekt](https://github.com/Azure-Samples/Cognitive-Speech-TTS) för plattforms-, ram- och språkvariationer.

Med standardbehållaren *för text till tal* bör du förlita dig på språken och rösten för den bildtagg du hämtade. Om du till exempel `latest` hämtade taggen är `en-US` standardspråket och rösten. `JessaRUS` Argumentet `{VOICE_NAME}` skulle då [`en-US-JessaRUS`](../language-support.md#standard-voices)vara . Se exemplet SSML nedan:

```xml
<speak version="1.0" xml:lang="en-US">
    <voice name="en-US-JessaRUS">
        This text will get converted into synthesized speech.
    </voice>
</speak>
```

Men för *anpassad text-till-tal* måste du hämta **Voice / modellen** från den anpassade [röstportalen](https://aka.ms/custom-voice-portal). Det anpassade modellnamnet är synonymt med röstnamnet. Navigera till **sidan Utbildning** och kopiera **voice/-modellen** som ska användas som `{VOICE_NAME}` argument.
<br><br>
:::image type="content" source="../media/custom-voice/custom-voice-model-voice-name.png" alt-text="Anpassad röstmodell - röstnamn":::

Se exemplet SSML nedan:

```xml
<speak version="1.0" xml:lang="en-US">
    <voice name="custom-voice-model">
        This text will get converted into synthesized speech.
    </voice>
</speak>
```

Låt oss konstruera en HTTP POST-begäran, med några rubriker och en datanyttolast. Ersätt `{VOICE_NAME}` platshållaren med ditt eget värde.

```curl
curl -s -v -X POST http://localhost:5000/speech/synthesize/cognitiveservices/v1 \
 -H 'Accept: audio/*' \
 -H 'Content-Type: application/ssml+xml' \
 -H 'X-Microsoft-OutputFormat: riff-16khz-16bit-mono-pcm' \
 -d '<speak version="1.0" xml:lang="en-US"><voice name="{VOICE_NAME}">This is a test, only a test.</voice></speak>'
```

Det här kommandot:

* Konstruerar en HTTP `speech/synthesize/cognitiveservices/v1` POST-begäran för slutpunkten.
* Anger ett `Accept` huvud på`audio/*`
* Anger ett `Content-Type` huvud `application/ssml+xml`i , mer information, se [begärandetext](../rest-text-to-speech.md#request-body).
* Anger ett `X-Microsoft-OutputFormat` huvud `riff-16khz-16bit-mono-pcm`i , för fler alternativ se [ljudutdata](../rest-text-to-speech.md#audio-outputs).
* Skickar [SSML-begäran (Speech Synthesis Markup Language)](../speech-synthesis-markup.md) som ges `{VOICE_NAME}` till slutpunkten.