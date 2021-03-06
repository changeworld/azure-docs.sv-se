---
title: Integrera med andra program - QnA Maker
description: QnA Maker integreras i klientprogram som chattrobotar samt med andra tjänster för bearbetning av naturligt språk, till exempel Språk understanding (LUIS).
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: c1edbfb6badfb73ce08a99709da0f8bfb61b7dc3
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804197"
---
# <a name="design-knowledge-base-for-client-applications"></a>Design kunskapsbas för klientprogram

QnA Maker integreras i klientprogram som chattrobotar samt med andra tjänster för bearbetning av naturligt språk, till exempel Språk understanding (LUIS).

## <a name="integration-with-a-conversational-client"></a>Integrering med en konversationsklient

QnA Maker integreras med konversationsklientprogram som [Microsoft Bot Framework](https://dev.botframework.com/). Texten som skickas till QnA Maker behöver inte rensas eller omvandlas. QnA Maker accepterar naturliga språk och returnerar det bästa svaret.

## <a name="create-a-bot-without-writing-any-code"></a>Skapa en bot utan att skriva någon kod

När du har publicerat kunskapsbasen skapar du en robot från sidan **Publicera** genom att välja knappen **Skapa bot.** Använd [bot handledning](../Quickstarts/create-publish-knowledge-base.md) för att lära dig vad som händer när du väljer knappen.

## <a name="providing-multi-turn-conversations"></a>Tillhandahålla konversationer med flera varv

En bot-klient ger det bästa valda svaret från din kunskapsbas och kan ge uppföljningsansanspråk om svaret är en del av ett QnA-par med flera svängar. Läs om hur du lägger [till](../how-to/multiturn-conversation.md) konversations- och svarsuppsättningar för flera svängar i kunskapsbasen.

## <a name="natural-language-processing"></a>Bearbetning av naturligt språk

Medan QnA Maker bearbetar frågor som använder bearbetning av naturligt språk, kan det också användas som en del av ett större system som besvarar frågor från flera kunskapsbaser. Du kan kombinera QnA Maker med en annan cognitive service, Language Understanding (LUIS), för att tillhandahålla bearbetning av naturligt språk innan du kommer till en viss kunskapsbas. Läs mer om när och hur du använder [LUIS och QnA Maker](../../luis/choose-natural-language-processing-service.md?toc=/azure/cognitive-services/qnamaker/toc.json) tillsammans.

## <a name="next-steps"></a>Nästa steg

Lär dig [utvecklingscykelkoncept](development-lifecycle-knowledge-base.md) för QnA Maker.