---
title: Översikt över användare, grupper, licensiering och roller – Azure AD | Microsoft-dokument
description: Relationen mellan tilldelade användare och licenser, administratörsroller, gruppmedlemskap i Azure Active Directory
keywords: ''
author: curtand
manager: daveba
ms.author: curtand
ms.reviewer: vincesm
ms.date: 11/08/2019
ms.topic: overview
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
services: active-directory
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: a3cc2de5a2f297e8133011905ff2961b44476d6b
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "77046325"
---
# <a name="users-groups-licensing-and-roles-for-large-organizations"></a>Användare, grupper, licensiering och roller för stora organisationer

Den här artikeln ger Azure AD-administratören en introduktion till relationen mellan de vanligaste uppgifterna för [identitetshantering](/azure/active-directory/fundamentals/identity-fundamentals?context=azure/active-directory/users-groups-roles/context/ugr-context) för användare när det gäller deras grupper, licenser, distribuerade företagsappar och administratörsroller. I takt med att organisationen växer kan du använda Azure AD-grupper och administratörsroller för att:

* Tilldela licenser till grupper i stället för individer
* Delegera behörigheter för att distribuera arbetet med Azure AD-hantering till mindre privilegierade roller
* Tilldela grupper åtkomst till företagsprogram

## <a name="assign-users-to-groups"></a>Tilldela användare grupper

Du kan använda grupper i Azure AD för att tilldela licenser till ett stort antal användare eller tilldela användaråtkomst till distribuerade företagsprogram. Du kan använda grupper för att tilldela alla administratörsroller utom Global administratör i Azure AD, eller så kan du bevilja åtkomst till externa resurser, till exempel SaaS-program eller SharePoint-webbplatser.

För ytterligare flexibilitet och för att minska arbetet med att hantera gruppmedlemskap kan du använda [dynamiska grupper](groups-create-rule.md) i Azure AD för att expandera och begränsa gruppmedlemskap automatiskt. Du behöver en Azure AD Premium P1-licens för varje unik användare som är medlem i en eller flera dynamiska grupper.

## <a name="assign-licenses-to-groups"></a>Tilldela grupper licenser

Att lägga till eller ta bort licenser från en användare i taget kräver mycket tid och arbete. Om du [tilldelar grupper licenser](/azure/active-directory/fundamentals/license-users-groups?context=azure/active-directory/users-groups-roles/context/ugr-context) i stället kan du göra dina storskaliga licenshantering enklare.

I Azure AD, när användare går med i en licensierad grupp, tilldelas de automatiskt de lämpliga licenserna. När användarna lämnar gruppen tas deras licenstilldelningar i Azure AD bort. Utan Azure AD-grupper skulle du behöva skriva ett PowerShell-skript eller använda Graph API om du vill lägga till eller ta bort flera användarlicenser för användare som ansluter till eller lämnar organisationen.

Om det inte finns tillräckligt många tillgängliga licenser, eller om ett problem uppstår, som serviceplaner som inte kan tilldelas på samma gång, kan du se statusen för alla licensieringsproblem för gruppen i Azure-portalen.

>[!NOTE]
>Den här gruppbaserade licensieringsfunktionen är för närvarande tillgänglig i en offentlig förhandsversion. Under förhandsvisningen är funktionen tillgänglig med valfri betald Azure AD-licensplan eller utvärderingsperiod (Azure Active Directory).

## <a name="delegate-administrator-roles"></a>Delegera administratörsroller

Många stora organisationer vill ha alternativ för att kunna ge användarna tillräckligt omfattande behörighet till arbetsuppgifter utan att tilldela den övergripande rollen Global administratör till, exempelvis, användare som behöver registrera program. Här är ett exempel på nya Azure AD-administratörsroller som hjälper dig att fördela arbetet med programhantering med större precision:

 Rollnamn | Sammanfattning av behörigheter
 --------- | -------------------
 **Programadministratör** | Kan lägga till och hantera företagsprogram och programregistreringar och konfigurera inställningar för proxyprogram. Programadministratörer kan visa principer och enheter för villkorlig åtkomst, men inte hantera dem.
 **Molnprogramadministratör** | Kan lägga till och hantera företagsprogram och registreringar av företagsprogram. Den här rollen har samtliga programadministratörsbehörigheter, förutom att den inte kan hantera proxyinställningarna för programmet.
**Programutvecklare** | Kan lägga till och uppdatera programregistreringar, men kan inte hantera företagsprogram eller konfigurera inställningar för programproxy.

Nya Azure AD-administratörsroller läggs till. Kontrollera Azure-portalen eller [behörighetsreferensen för administratörsrollen](directory-assign-admin-roles.md) för aktuella tillgängliga roller.

## <a name="assign-app-access"></a>Tilldela appåtkomst

Du kan använda Azure AD för att tilldela gruppåtkomst till företagsapparna [som har distribuerats i din Azure AD-klient](/azure/active-directory/manage-apps/methods-for-assigning-users-and-groups?context=azure/active-directory/users-groups-roles/context/ugr-context). Om du vill kombinera dynamiska grupper med grupptilldelning till appar kan du automatisera dina åtkomsttilldelningar för användarappar när organisationen växer. Du behöver en Azure Active Directory Premium P1- eller Premium P2-licens för att tilldela åtkomst till företagsappar.

Azure AD ger dig även större kontroll över de data som flödar mellan appen och de grupper som du tilldelat åtkomst. I [företagsprogram](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps) öppnar du en app och väljer **etablering** för att:

* Konfigurera automatisk etablering för appar som stöder det
* Ange autentiseringsuppgifter för att ansluta till appens API för användarhantering
* Konfigurera mappningarna som styr vilka användarattribut som flödar mellan Azure AD och appen när användarkonton etableras eller uppdateras
* Starta och stoppa Azure AD-etableringstjänsten för en app, rensa cacheminnet för etablering eller starta om tjänsten
* Visa **rapporten för etableringsaktivitet** som tillhandahåller en logg över alla användare och grupper som skapas, uppdateras och tas bort mellan Azure AD och appen, och **rapporten för etableringsfel** som ger mer detaljerade felmeddelanden

## <a name="next-steps"></a>Nästa steg

Om du är ny som Azure AD-administratör får du en bra grundläggande översikt i [Grunderna i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/index).

Alternativt kan du börja att [skapa grupper](/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal?context=azure/active-directory/users-groups-roles/context/ugr-context), [tilldela licenser](/azure/active-directory/fundamentals/license-users-groups?context=azure/active-directory/users-groups-roles/context/ugr-context), [tilldela appåtkomst](/azure/active-directory/manage-apps/methods-for-assigning-users-and-groups?context=azure/active-directory/users-groups-roles/context/ugr-context) eller [tilldela administratörsroller](directory-assign-admin-roles.md).
