---
title: Skapa en Azure Active Directory-klient
description: Lär dig hur du skapar en Azure AD-klientorganisation att använda för att registrera och skapa appar.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: quickstart
ms.date: 03/12/2020
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev, identityplatformtop40, fasttrack-edit
ms.openlocfilehash: 0e2247e94b20846f19c2ed26c96a5dc53972e770
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80883821"
---
# <a name="quickstart-set-up-a-tenant"></a>Snabbstart: Konfigurera en klient

Med Microsoft Identity-plattformen kan utvecklare skapa appar som riktar sig till flera olika anpassade Microsoft 365-miljöer och -identiteter. För att komma igång med att använda Microsoft Identity-plattform så behöver du åtkomst till en miljö, även kallad en Azure AD-klientorganisation, som kan registrera och hantera appar, ha åtkomst till Microsoft 365-data och distribuera villkorsstyrd åtkomst och klientorganisationsbegränsningar.

En klientorganisation är en representation av en organisation. Det är en dedikerad instans av Azure AD som en organisation eller apputvecklare får när organisationen eller apputvecklaren skapar en relation med Microsoft – som att registrera sig för Azure, Microsoft Intune eller Microsoft 365.

Varje Azure AD-klientorganisation skiljer sig från andra Azure AD-klientorganisationer och har en egen representation av arbets- och skolidentiteter (om det är en Azure AD B2C-klientorganisation) och appregistreringar. En appregistrering i din klientorganisation kan bara tillåta autentiseringar från konton i din klientorganisation eller alla klientorganisationer.

## <a name="determining-environment-type"></a>Fastställa miljötyp

Det finns två typer av miljöer som du kan skapa. Att bestämma vilken du behöver baseras helt på vilka typer av användare som din app autentiserar.

* Arbets- och skolkonton (Azure AD-konton) eller Microsof-konton (t.ex. outlook.com och live.com)
* Sociala och lokala konton (Azure AD B2C)

Snabbstarten är indelad i två scenarier beroende på vilken app du vill skapa. Om du behöver hjälp med rikta in dig på en identitetstyp kan du ta en titt på avsnittet [om Microsoft Identity-plattformen](about-microsoft-identity-platform.md)

## <a name="work-and-school-accounts-or-personal-microsoft-accounts"></a>Arbets- och skolkonton eller personliga Microsoft-konton

### <a name="use-an-existing-tenant"></a>Använda en befintlig klientorganisation

Många utvecklare har redan klienter via tjänster eller prenumerationer som är knutna till Azure AD-klienter (till exempel: Microsoft 365- eller Azure-prenumerationer).

1. Om du vill kontrollera klientorganisationen loggar du in på [Azure-portalen](https://portal.azure.com) med det konto du vill använda för att hantera din app.
1. Se det övre högra hörnet. Om du har en klient kommer du automatiskt att loggas in och kan se klientorganisationens namn direkt under namnet på ditt konto.
   * Hovra över ditt kontonamn längst upp till höger i Azure-portalen så visas namn, e-post, katalog/klient-ID (ett GUID) och domän.
   * Om ditt konto är kopplat till flera klienter måste du välja namnet på ditt konto för att öppna en meny där du kan växla mellan klienter. Varje klient har sitt eget klient-ID.

> [!TIP]
> Om du behöver hitta klient-ID:t kan du:
> * Hovra över kontonamnet för att hämta katalogen/klient-ID:t eller
> * Välj **Azure Active Directory > Egenskaper > Katalog-ID** i Azure-portalen

Om du inte har en befintlig klient som är kopplad till ditt konto kan du ser ett GUID under namnet på ditt konto och kommer inte att kunna utföra åtgärder som att registrera appar förrän följer stegen i nästa avsnitt.

### <a name="create-a-new-azure-ad-tenant"></a>Skapa en ny Azure AD-klient

Om du inte redan har en Azure AD-klient eller vill skapa en ny för utveckling läser du [snabbstarten](../fundamentals/active-directory-access-create-new-tenant.md) eller följer helt enkelt [upplevelsen för att skapa katalogen](https://portal.azure.com/#create/Microsoft.AzureActiveDirectory). Du måste ange följande information för att skapa den nya klientorganisationen:

- **Organisationens namn**
- **Initial domän** – kommer att vara en del av *.onmicrosoft.com. Du kan anpassa domänen mer senare.
- **Land eller region**

> [!NOTE]
> När du namnger din klientorganisation ska du använda alfanumeriska tecken. Specialtecken är inte tillåtna. Namnet får inte överskrida 256 tecken.

## <a name="social-and-local-accounts"></a>Sociala och lokala konton

Om du vill börja skapa appar som loggar in sociala och lokala konton måste du skapa en Azure AD B2C-klientorganisation. Börja genom att följa avsnittet om att [skapa en Azure AD B2C-klientorganisation](../../active-directory-b2c/tutorial-create-tenant.md).

## <a name="next-steps"></a>Nästa steg

* [Registrera en app](quickstart-register-app.md) och integrera med Microsofts identitetsplattform. 
* Lär dig grunderna i [autentisering](authentication-scenarios.md).
* Se [Hur Azure-prenumerationer associeras med Azure Active Directory](../fundamentals/active-directory-how-subscriptions-associated-directory.md) för information om relationen mellan prenumerationer och en Azure AD-klientorganisation.
