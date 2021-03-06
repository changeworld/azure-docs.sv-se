---
title: Inga användare etableras i ett Azure AD Gallery-program
description: Felsöka vanliga problem när du inte ser användare som visas i ett Azure AD Gallery-program som du har konfigurerat för användaretablering med Azure AD
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/03/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9b60261d63e1bcb75aea9d2e8a6b74902520f391
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77522925"
---
# <a name="no-users-are-being-provisioned-to-an-azure-ad-gallery-application"></a>Inga användare etableras i ett Azure AD Gallery-program
När automatisk etablering har konfigurerats för ett program (inklusive att verifiera att appautentiseringsuppgifterna som tillhandahålls till Azure AD för att ansluta till appen är giltiga) etableras användare och/eller grupper till appen. Etableringen bestäms av följande:

-   Vilka användare och grupper som har **tilldelats** programmet. Observera att etablering av kapslade grupper eller Office 365-grupper inte stöds. Mer information om tilldelning finns i [Tilldela en användare eller grupp till en företagsapp i Azure Active Directory](../manage-apps/assign-user-or-group-access-portal.md).
-   Oavsett om **attributmappningar** är aktiverade och konfigurerade för att synkronisera giltiga attribut från Azure AD till appen. Mer information om attributmappningar finns i [Anpassa användaretableringsattributmappningar för SaaS-program i Azure Active Directory](customize-application-attributes.md).
-   Om det finns ett **omfångsfilter** som filtrerar användare baserat på specifika attributvärden. Mer information om omfångsfilter finns i [Attributbaserad programetablering med omfångsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
  
Om du observerar att användare inte etableras läser du [etableringsloggarna (förhandsversionen)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) i Azure AD. Sök efter loggposter för en viss användare.

Du kan komma åt etableringsloggarna i Azure-portalen genom att välja **Azure Active Directory** &gt; **Enterprise Apps** &gt; **Provisioning loggar (förhandsversion)** i avsnittet **Aktivitet.** Du kan söka i etableringsdata baserat på namnet på användaren eller identifieraren i antingen källsystemet eller målsystemet. Mer information finns i [Etableringsloggar (förhandsgranskning)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context). 

Etableringsloggarna registrerar alla åtgärder som utförs av etableringstjänsten, inklusive att fråga Azure AD för tilldelade användare som är i omfång för etablering, frågar målappen för dessa användares existens, jämför användarobjekten mellan systemet. Lägg sedan till, uppdatera eller inaktivera användarkontot i målsystemet baserat på jämförelsen.

## <a name="general-problem-areas-with-provisioning-to-consider"></a>Allmänna problemområden med etablering att överväga
Nedan finns en lista över de allmänna problemområden som du kan borra i om du har en idé om var du ska börja.

- [Etableringstjänsten verkar inte starta](#provisioning-service-does-not-appear-to-start)
- [Etableringsloggar säger att användarna hoppas över och inte etableras, även om de har tilldelats](#provisioning-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned)

## <a name="provisioning-service-does-not-appear-to-start"></a>Etableringstjänsten verkar inte starta
Om du anger att **etableringsstatus** ska vara **på** i avsnittet **Azure Active &gt; Directory Enterprise Apps &gt; \[Application Name\] &gt;Provisioning** i Azure-portalen. Men inga andra statusdetaljer visas på den sidan efter efterföljande omladdningar, är det troligt att tjänsten körs men inte har slutfört en inledande cykel ännu. Kontrollera **de etableringsloggar (förhandsversion)** som beskrivs ovan för att avgöra vilka åtgärder tjänsten utför och om det finns några fel.

>[!NOTE]
>En inledande cykel kan ta allt från 20 minuter till flera timmar, beroende på storleken på Azure AD-katalogen och antalet användare i omfånget för etablering. Efterföljande synkroniseringar efter den inledande cykeln är snabbare, eftersom etableringstjänsten lagrar vattenstämplar som representerar tillståndet för båda systemen efter den inledande cykeln. Den inledande cykeln förbättrar prestanda för efterföljande synkroniseringar.
>


## <a name="provisioning-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned"></a>Etableringsloggar säger att användare hoppas över och inte etableras trots att de har tilldelats

När en användare visas som "hoppas över" i etableringsloggarna är det viktigt att granska fliken **Steg** i loggen för att fastställa orsaken. Nedan finns vanliga orsaker och lösningar:

- **Ett omfångsfilter har konfigurerats** **som filtrerar bort användaren baserat på ett attributvärde**. Mer information om omfångsfilter finns i [omfångsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
- **Användaren är "inte effektivt rätt".** Om det här specifika felmeddelandet visas beror det på att det finns ett problem med användartilldelningsposten som lagras i Azure AD. Lös problemet genom att ta bort tilldelningen av användaren (eller gruppen) från appen och tilldela om den igen. Mer information om tilldelning finns i [Tilldela användar- eller gruppåtkomst](../manage-apps/assign-user-or-group-access-portal.md).
- **Ett obligatoriskt attribut saknas eller fylls inte i för en användare.** En viktig sak att tänka på när du ställer in etablering är att granska och konfigurera attributmappningar och arbetsflöden som definierar vilka användaregenskaper (eller gruppegenskaper) som flödar från Azure AD till programmet. Den här konfigurationen inkluderar att ange den "matchande egenskap" som används för att unikt identifiera och matcha användare/grupper mellan de två systemen. Mer information om den här viktiga processen finns i [Anpassa användares etableringsattributmappningar för SaaS-program i Azure Active Directory](customize-application-attributes.md).
- **Attributmappningar för grupper:** Etablering av gruppnamn och gruppinformation, förutom medlemmarna, om det stöds för vissa program. Du kan aktivera eller inaktivera den här funktionen genom att aktivera eller inaktivera **mappning** för gruppobjekt som visas på fliken **Etablering.** Om etableringsgrupper är aktiverade måste du granska attributmappningarna för att säkerställa att ett lämpligt fält används för "matchande ID". Det matchande ID:t kan vara visningsnamnet eller e-postaliaset. Gruppen och dess medlemmar etableras inte om den matchande egenskapen är tom eller inte fylls i för en grupp i Azure AD.

## <a name="next-steps"></a>Nästa steg

[Synkronisering av Azure AD Connect: Förstå deklarativ etablering](../hybrid/concept-azure-ad-connect-sync-declarative-provisioning.md)
