---
title: Förbereda webb-API:er för att ringa upp webb-api:er för mobilapp | Azure
titleSuffix: Microsoft identity platform
description: Läs om hur du skapar en mobilapp som anropar webb-API:er. (Förbered appar för produktion.)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 28ace84f9a80b71209d7963d02b66317292b151b
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80882547"
---
# <a name="prepare-mobile-apps-for-production"></a>Förbereda mobilappar för produktion

Den här artikeln innehåller information om hur du förbättrar mobilappens kvalitet och tillförlitlighet innan du flyttar den till produktion.

## <a name="handle-errors"></a>Hantera fel

När du förbereder en mobilapp för produktion kan flera felvillkor uppstå. De viktigaste fallen som du ska hantera är tysta fel och återgångar till interaktion. Andra villkor som du bör överväga är nätverkssituationer, avbrott i tjänsten, krav för administratörsmedgivande och andra scenariospecifika ärenden.

För varje MSAL-typ (Microsoft Authentication Library) hittar du exempelkod och wiki-innehåll som beskriver hur du hanterar feltillstånd:

- [MSAL Android wiki](https://github.com/AzureAD/microsoft-authentication-library-for-android)
- [MSAL iOS wiki](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki)
- [MSAL.NET wiki](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki)

## <a name="mitigate-and-investigate-issues"></a>Minska och undersöka problem

Samla in data för att bättre diagnostisera problem i appen. Information om vilka typer av data som du kan samla in finns [i Loggning i MSAL-program](https://docs.microsoft.com/azure/active-directory/develop/msal-logging).

Här är några förslag på datainsamling:

- Användare kan be om hjälp när de har problem. En bra metod är att fånga och tillfälligt lagra loggar. Ange en plats där användarna kan ladda upp loggarna. MSAL tillhandahåller loggningstillägg för att samla in detaljerad information om autentisering.

- Om telemetri är tillgängligt aktiverar du den via MSAL för att samla in data om hur användare loggar in på din app.

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [Common steps to move to production](../../../includes/active-directory-develop-scenarios-production.md)]

Information om hur du provar ytterligare exempel finns i [Skrivbords- och mobilwebbwebbklientappar](sample-v2-code.md#desktop-and-mobile-public-client-apps).
