---
title: Vad är Azure AD Connect och Connect Health? | Microsoft Docs
description: Beskriver de verktyg som används för att synkronisera och övervaka din lokala miljö med Azure AD.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 01/08/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1e60c35a32152d4adec72fb507becc0db535036f
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631728"
---
# <a name="what-is-azure-ad-connect"></a>Vad är Azure AD Connect?

Azure AD Connect är Microsoft-verktyget som har utformats för att uppfylla och uppnå dina hybrididentitetsmål.  Den tillhandahåller följande funktioner:
     
- [Synkronisering av lösenordshash](whatis-phs.md) – En inloggningsmetod som synkroniserar en hash av en användares lokala AD-lösenord med Azure AD.
- [Direktautentisering](how-to-connect-pta.md) – En inloggningsmetod som gör att användare kan använda samma lösenord lokalt och i molnet men inte kräver den ytterligare infrastrukturen hos en federerad miljö.
- [Federationsintegrering](how-to-connect-fed-whatis.md) – Federation är en valfri del av Azure AD Connect som kan användas för att konfigurera en hybridmiljö med hjälp av en lokal AD FS-infrastruktur. Det har även AD FS-hanteringsfunktioner som certifikatsförnyelse och ytterligare AD FS-serverdistributioner.
- [Synkronisering](how-to-connect-sync-whatis.md) – Ansvarar för att skapa användare, grupper och andra objekt.  Den ser även till att identitetsinformationen för dina lokala användare och grupper matchar molnet.  Den här synkroniseringen omfattar även lösenordshasher.
- [Hälsoövervakning](whatis-hybrid-identity-health.md) – Azure AD Connect Health kan ge robust övervakning och tillhandahålla en central plats i Azure-portalen för att visa den här aktiviteten. 


![Vad är Azure AD Connect?](./media/whatis-hybrid-identity/arch.png)



## <a name="what-is-azure-ad-connect-health"></a>Vad är Azure AD Connect Health?

Azure Active Directory (Azure AD) Connect Health tillhandahåller gedigen övervakning av den lokala identitetsinfrastrukturen. Det hjälper dig att upprätthålla en tillförlitlig anslutning till Office 365 och Microsoft Online Services.  Den här tillförlitligheten uppnås genom att övervakningsfunktioner tillhandahålls för dina viktiga identitetskomponenter. Dessutom blir de viktiga datapunkterna om dessa komponenter lättillgängliga.

Informationen visas på [Azure AD Connect Health-portalen](https://aka.ms/aadconnecthealth). Använd Azure AD Connect Health-portalen till att visa aviseringar, prestandaövervakning, användningsanalys och annan information. Med Azure AD Connect Health har du åtkomst till all hälsoinformation om dina viktiga identitetskomponenter på samma ställe.

![Vad är Azure AD Connect Health?](./media/whatis-hybrid-identity-health/aadconnecthealth2.png)

## <a name="why-use-azure-ad-connect"></a>Varför ska jag använda Azure AD Connect?
Om du integrerar dina lokala kataloger med Azure AD kan du hjälpa dina användare att bli mer produktiva genom att tillhandahålla en gemensam identitet för åtkomst både till molnet och lokala resurser. Användare och organisationer kan dra nytta av följande:

* Användarna kan använda samma identitet för att komma åt lokala program och molntjänster som Office 365.
* Ett enda verktyg är allt som krävs för att tillhandahålla en enkel distributionsupplevelse för synkronisering och inloggning.
* Innehåller de senaste funktionerna för dina scenarier. Azure AD Connect ersätter äldre versioner av identitetsintegreringsverktyg som DirSync och Azure AD Sync. Mer information finns i [jämförelsen för hybrididentitetskatalogintegrationsverktyg](plan-hybrid-identity-design-considerations-tools-comparison.md).

## <a name="why-use-azure-ad-connect-health"></a>Varför ska jag använda Azure AD Connect Health?
Med Azure AD hjälper du dina användare att bli mer produktiva genom att tillhandahålla en gemensam identitet för åtkomst både till molnet och lokala resurser. Det är en utmaning att göra miljön tillförlitlig så att användarna kan komma åt dessa resurser.  Azure AD Connect Health hjälper till att övervaka och ge insyn i din lokala identitetsinfrastruktur och säkerställer därmed miljöns tillförlitlighet. Det enda du behöver göra är att installera en agent på alla lokala identitetsservrar.

Azure AD Connect Health för AD FS stöder AD FS 2.0 på Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 och Windows Server 2016. Det stöder även övervakning av AD FS-proxyservrar eller webbprogramproxyservrar som tillhandahåller autentiseringsstöd för åtkomst till extranät. Med en enkel och snabb installation av hälsoagenten tillhandahåller Azure AD Connect Health för AD FS dig en uppsättning viktiga funktioner.

Viktiga fördelar och bästa praxis:

|Viktiga fördelar|Metodtips|
|-----|-----|
|Förbättrad säkerhet|[Trender för extranätsutelåsning](how-to-connect-health-adfs.md#usage-analytics-for-ad-fs)</br>[Rapport över misslyckade inloggningar](how-to-connect-health-adfs-risky-ip.md)</br>[I sekretesskompatibel](reference-connect-health-user-privacy.md)|
|Få aviseringar om [alla kritiska ADFS-systemproblem](how-to-connect-health-alert-catalog.md#alerts-for-active-directory-federation-services)|Serverkonfiguration och tillgänglighet</br>[Prestanda och anslutning](how-to-connect-health-adfs.md#performance-monitoring-for-ad-fs)</br>Regelbundet underhåll|
|Lätt att distribuera och hantera|[Snabbagentinstallation](how-to-connect-health-agent-install.md#installing-the-azure-ad-connect-health-agent-for-ad-fs)</br>Automatisk agentuppgradering till senaste version</br>Data tillgängliga i portalen inom några minuter|
Omfattande [användningsstatistik](how-to-connect-health-adfs.md#usage-analytics-for-ad-fs)|Främsta programanvändning</br>Nätverksplatser och TCP-anslutning</br>Tokenbegäranden per server|
|En utmärkt användarupplevelse|Instrumentpanel som Azure-portalen</br>[Aviseringar via e-postmeddelanden](how-to-connect-health-adfs.md#alerts-for-ad-fs)|


## <a name="license-requirements-for-using-azure-ad-connect"></a>Licenskrav för användning av Azure AD Connect

[!INCLUDE [active-directory-free-license.md](../../../includes/active-directory-free-license.md)]

## <a name="license-requirements-for-using-azure-ad-connect-health"></a>Licenskrav för användning av Azure AD Connect Health
[!INCLUDE [active-directory-free-license.md](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>Nästa steg

- [Hårdvara och förutsättningar](how-to-connect-install-prerequisites.md) 
- [Standardinställningar](how-to-connect-install-express.md)
- [Anpassade inställningar](how-to-connect-install-custom.md)
- [Installera Azure AD Connect Health-agenter](how-to-connect-health-agent-install.md) 
