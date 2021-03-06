---
title: FedRAMP Måttlig skiss exempelkontroller
description: Kontrollkartläggning av FedRAMP Måttlig skiss exempel. Varje kontroll mappas till en eller flera Azure-principer som hjälper till med utvärdering.
ms.date: 10/31/2019
ms.topic: sample
ms.openlocfilehash: 3fd6762f4f0a76b560a37dd1ed4f727aa76385fd
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "77150486"
---
# <a name="control-mapping-of-the-fedramp-moderate-blueprint-sample"></a>Kontroll kartläggning av FedRAMP Måttlig skiss prov

I följande artikel beskrivs hur Azure Blueprints FedRAMP Modererat skissprov mappar till FedRAMP-moderkontrollkontrollerna. Mer information om kontrollerna finns i [FedRAMP Security Controls Baseline](https://www.fedramp.gov/).

Följande mappningar är till **FedRAMP Måttlig kontroller.** Använd navigeringen till höger för att hoppa direkt till en specifik kontrollmappning. Många av de mappade kontrollerna implementeras med ett [Azure Policy-initiativ.](../../../policy/overview.md) Om du vill granska hela initiativet öppnar du **Princip** i Azure-portalen och väljer sidan **Definitioner.** Leta sedan reda på och välj ** \[förhandsversionen:\]Granska FedRAMP-moderata kontroller och distribuera specifika VM-tillägg för att stödja inbyggda** principinitiativ för granskningskrav.

> [!IMPORTANT]
> Varje kontroll nedan är associerad med en eller flera [Azure-principdefinitioner.](../../../policy/overview.md) Dessa policyer kan hjälpa dig att [bedöma efterlevnaden](../../../policy/how-to/get-compliance-data.md) av kontrollen. Det finns dock ofta inte en 1:1 eller fullständig matchning mellan en kontroll och en eller flera principer. Som sådan refererar **kompatibel** i Azure-princip endast till principerna själva. Detta säkerställer inte att du är helt kompatibel med alla krav på en kontroll. Dessutom innehåller efterlevnadsstandarden kontroller som inte åtgärdas av några Azure-principdefinitioner just nu. Därför är efterlevnad i Azure Policy bara en partiell bild av din övergripande efterlevnadsstatus. Associationerna mellan kontroller och Azure-principdefinitioner för det härmplet för efterlevnadsritning kan ändras med tiden. Om du vill visa ändringshistoriken läser du [GitHub Commit-historiken](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/fedramp-m/control-mapping.md).

## <a name="ac-2-account-management"></a>AC-2 Kontohantering

Den här skissen hjälper dig att granska konton som kanske inte uppfyller organisationens kontohanteringskrav. Den här skissen tilldelar [Azure Policy-definitioner](../../../policy/overview.md) som granskar externa konton med läs-, skriv- och ägarbehörigheter för en prenumeration och föråldrade konton. Genom att granska de räkenskaper som granskas av dessa principer kan du vidta lämpliga åtgärder för att säkerställa att kontohanteringskraven uppfylls.

- Inaktuella konton ska tas bort från din prenumeration
- Inaktuella konton med ägarbehörigheter ska tas bort från din prenumeration
- Externa konton med ägarbehörigheter ska tas bort från din prenumeration
- Externa konton med läsbehörighet bör tas bort från din prenumeration
- Externa konton med skrivbehörighet ska tas bort från din prenumeration

## <a name="ac-2-7-account-management--role-based-schemes"></a>AC-2 (7) Kontohantering | Rollbaserade system

Azure implementerar [rollbaserad åtkomstkontroll](../../../../role-based-access-control/overview.md) (RBAC) som hjälper dig att hantera vem som har åtkomst till resurser i Azure. Med hjälp av Azure-portalen kan du granska vem som har åtkomst till Azure-resurser och deras behörigheter. Den här skissen tilldelar också [Azure-principdefinitioner](../../../policy/overview.md) för granskning av Azure Active Directory-autentisering för SQL-servrar och tjänstinfrastruktur. Med Hjälp av Azure Active Directory-autentisering möjliggör förenklad behörighetshantering och centraliserad identitetshantering för databasanvändare och andra Microsoft-tjänster. Dessutom tilldelar den här skissen en Azure-principdefinition för att granska användningen av anpassade RBAC-regler. Att förstå var anpassade RBAC-regler implementeras kan hjälpa dig att verifiera behov och korrekt implementering, eftersom anpassade RBAC-regler är felbenägna.

- En Azure Active Directory-administratör bör etableras för SQL-servrar
- Granskningsanvändning av anpassade RBAC-regler
- Service Fabric-kluster bör endast använda Azure Active Directory för klientautentisering

## <a name="ac-2-12-account-management--account-monitoring--atypical-usage"></a>AC-2 (12) Kontohantering | Kontoövervakning / Atypisk användning

Åtkomst till virtuella datorer (JUST-in-time) låser sig för inkommande trafik till virtuella Azure-datorer, vilket minskar exponeringen för attacker samtidigt som enkel åtkomst till virtuella datorer vid behov kan anslutas till virtuella datorer. Alla JIT-begäranden om åtkomst till virtuella datorer loggas i aktivitetsloggen så att du kan övervaka för atypisk användning. Den här skissen tilldelar en [Azure Policy-definition](../../../policy/overview.md) som hjälper dig att övervaka virtuella datorer som kan stödja just-in-time-åtkomst men som ännu inte har konfigurerats.

- Just-in-time-kontroller av nätverksåtkomst ska tillämpas på virtuella datorer

## <a name="ac-4-information-flow-enforcement"></a>AC-4 Information Flow Verkställighet

Korsursprungsresursdelning (CORS) kan tillåta att App Services-resurser begärs från en extern domän. Microsoft rekommenderar att du endast tillåter nödvändiga domäner att interagera med dina API-, funktions- och webbprogram. Den här skissen tilldelar en [Azure-principdefinition](../../../policy/overview.md) som hjälper dig att övervaka BEGRÄNSNINGAR för ÅTKOMST TILL CORS-resurser i Azure Security Center.
Genom att förstå CORS-implementeringar kan du kontrollera att informationsflödeskontroller implementeras.

- CORS bör inte tillåta alla resurser att komma åt ditt webbprogram

## <a name="ac-5-separation-of-duties"></a>AC-5 Tulldelning

Att bara ha en Azure-prenumerationsägare tillåter inte administrativ redundans. Omvänt kan för många Azure-prenumerationsägare öka risken för ett intrång via ett komprometterat ägarkonto. Den här skissen hjälper dig att underhålla ett lämpligt antal Azure-prenumerationsägare genom att tilldela [Azure Policy-definitioner](../../../policy/overview.md) som granskar antalet ägare för Azure-prenumerationer. Den här skissen tilldelar också Azure-principdefinitioner som hjälper dig att kontrollera medlemskap i gruppen Administratörer på virtuella Datorer i Windows. Genom att hantera administratörsbehörighet och administratörsbehörighet för virtuella datorer kan du implementera lämplig åtskillnad mellan uppgifter.

- Högst 3 ägare bör utses för din prenumeration
- Granska virtuella Windows-datorer där gruppen Administratörer innehåller någon av de angivna medlemmarna
- Granska virtuella windows-datorer där gruppen Administratörer inte innehåller alla angivna medlemmar
- Distribuera förutsättningar för granskning av virtuella Windows-datorer där gruppen Administratörer innehåller någon av de angivna medlemmarna
- Distribuera förutsättningar för granskning av virtuella Windows-datorer där gruppen Administratörer inte innehåller alla angivna medlemmar
- Det bör finnas fler än en ägare som tilldelats din prenumeration

## <a name="ac-17-1-remote-access--automated-monitoring--control"></a>AC-17 (1) Fjärråtkomst | Automatiserad övervakning / kontroll

Den här skissen hjälper dig att övervaka och styra fjärråtkomst genom att tilldela [Azure Policy-definitioner](../../../policy/overview.md) för att övervaka att fjärrfelsökning för Azure App Service-programmet är inaktiverat och principdefinitioner som granskar virtuella Linux-datorer som tillåter fjärranslutningar från konton utan lösenord. Den här skissen tilldelar också en Azure-principdefinition som hjälper dig att övervaka obegränsad åtkomst till lagringskonton. Genom att övervaka dessa indikatorer kan du se till att fjärråtkomstmetoderna följer din säkerhetsprincip.

- \[Preview\]: Granska virtuella Linux-datorer som tillåter fjärranslutningar från konton utan lösenord
- \[Preview\]: Distribuera krav för att granska virtuella Linux-datorer som tillåter fjärranslutningar från konton utan lösenord
- Granska obegränsad nätverksåtkomst till lagringskonton
- Fjärrfelsökning bör inaktiveras för API-app
- Fjärrfelsökning bör inaktiveras för Funktionsapp
- Fjärrfelsökning bör inaktiveras för webbprogram

## <a name="au-5-response-to-audit-processing-failures"></a>AU-5 Svar på fel i granskningsbehandling

Den här skissen tilldelar [Azure Policy-definitioner](../../../policy/overview.md) som övervakar gransknings- och händelseloggningskonfigurationer. Övervakning av dessa konfigurationer kan ge en indikator på ett granskningssystemfel eller felkonfiguration och hjälpa dig att vidta korrigerande åtgärder.

- Granska diagnostikinställning
- Granskning på SQL-server bör aktiveras
- Avancerad datasäkerhet bör aktiveras på dina hanterade instanser
- Avancerad datasäkerhet bör aktiveras på dina SQL-servrar

## <a name="au-12-audit-generation"></a>AU-12 Audit Generation

Den här skissen hjälper dig att se till att systemhändelser loggas genom att tilldela [Azure Policy-definitioner](../../../policy/overview.md) som granskar logginställningar på Azure-resurser. Dessa principdefinitioner granskar och tillämpar distribution av Log Analytics-agenten på virtuella Azure-datorer och konfiguration av granskningsinställningar för andra Azure-resurstyper. Dessa principdefinitioner granskar också konfigurationen av diagnostikloggar för att ge insikt i åtgärder som utförs inom Azure-resurser. Dessutom konfigureras granskning och avancerad datasäkerhet på SQL-servrar.

- \[Förhandsversion:\]Distribution av granskningslogganalysagent – OS-avbildning (VM)
- \[Förhandsversion:\]Distribution av granskningslogganalysagent i VMSS - VM-avbildning (OS) olistad
- \[Förhandsversion:\]Arbetsyta för granskningslogganalys för virtuell dator – rapportfelmatchning
- \[Förhandsversion:\]Distribuera logganalysagent för VMSS (Linux VMSS)
- \[Preview\]: Distribuera LoggAnalys agent för Linux virtuella datorer
- \[Förhandsversion:\]Distribuera logganalysagent för VMSS (Windows VMSS)
- \[Förhandsgranskning:\]Distribuera logganalysagent för virtuella Windows-datorer
- Granska diagnostikinställning
- Granskning på SQL-server bör aktiveras
- Avancerad datasäkerhet bör aktiveras på dina hanterade instanser
- Avancerad datasäkerhet bör aktiveras på dina SQL-servrar
- Distribuera avancerad datasäkerhet på SQL-servrar
- Distribuera granskning på SQL-servrar
- Distribuera diagnostikinställningar för nätverkssäkerhetsgrupper

## <a name="cm-7-2-least-functionality--prevent-program-execution"></a>CM-7 (2) Minst funktionalitet | Förhindra programkörning

Adaptiv programkontroll i Azure Security Center är en intelligent, automatiserad heltäckande lösning för vitlistning av program som kan blockera eller förhindra att viss programvara körs på dina virtuella datorer. Programkontroll kan köras i ett tvingande läge som förhindrar att ett icke-godkänt program körs. Den här skissen tilldelar en Azure Policy-definition som hjälper dig att övervaka virtuella datorer där ett program vitlista rekommenderas men ännu inte har konfigurerats.

- Adaptiva programkontroller bör aktiveras på virtuella datorer

## <a name="cm-7-5-least-functionality--authorized-software--whitelisting"></a>CM-7 (5) Minst funktionalitet | Auktoriserad programvara / vitlistning

Adaptiv programkontroll i Azure Security Center är en intelligent, automatiserad heltäckande lösning för vitlistning av program som kan blockera eller förhindra att viss programvara körs på dina virtuella datorer. Programkontroll hjälper dig att skapa godkända programlistor för dina virtuella datorer. Den här skissen tilldelar en [Azure Policy-definition](../../../policy/overview.md) som hjälper dig att övervaka virtuella datorer där ett program vitlista rekommenderas men ännu inte har konfigurerats.

- Adaptiva programkontroller bör aktiveras på virtuella datorer

## <a name="cm-11-user-installed-software"></a>CM-11 Programvara för användarinstallerad programvara

Adaptiv programkontroll i Azure Security Center är en intelligent, automatiserad heltäckande lösning för vitlistning av program som kan blockera eller förhindra att viss programvara körs på dina virtuella datorer. Programkontroll kan hjälpa dig att genomdriva och övervaka efterlevnaden av principer för begränsning av programvara. Den här skissen tilldelar en [Azure Policy-definition](../../../policy/overview.md) som hjälper dig att övervaka virtuella datorer där ett program vitlista rekommenderas men ännu inte har konfigurerats.

- Adaptiva programkontroller bör aktiveras på virtuella datorer

## <a name="cp-7-alternate-processing-site"></a>Alternativ bearbetningsplats för CP-7

Azure Site Recovery replikerar arbetsbelastningar som körs på virtuella datorer från en primär plats till en sekundär plats. Om ett avbrott inträffar på den primära platsen misslyckas arbetsbelastningen över den sekundära platsen. Den här skissen tilldelar en [Azure-principdefinition](../../../policy/overview.md) som granskar virtuella datorer utan haveriberedskap konfigurerad. Övervakning av den här indikatorn kan hjälpa dig att se till att nödvändiga beredskapskontroller finns på plats.

- Granska virtuella datorer utan haveriberedskap konfigurerad

## <a name="ia-2-1-identification-and-authentication-organizational-users--network-access-to-privileged-accounts"></a>IA-2 (1) Identifiering och autentisering (organisatoriska användare) | Nätverksåtkomst till privilegierade konton

Den här skissen hjälper dig att begränsa och kontrollera privilegierad åtkomst genom att tilldela [Azure Policy-definitioner](../../../policy/overview.md) för att granska konton med ägare och/eller skrivbehörighet som inte har multifaktorautentisering aktiverad. Multifaktorautentisering hjälper till att skydda konton även om en del av autentiseringsinformationen äventyras. Genom att övervaka konton utan multifaktorautentisering aktiverat kan du identifiera konton som kan vara mer benägna att komprometteras.

- MFA ska aktiveras på konton med ägarbehörighet för din prenumeration
- MFA ska aktiveras på konton med skrivbehörighet för din prenumeration

## <a name="ia-2-2-identification-and-authentication-organizational-users--network-access-to-non-privileged-accounts"></a>IA-2 (2) Identifiering och autentisering (organisatoriska användare) | Nätverksåtkomst till konton som inte är privilegierade

Den här skissen hjälper dig att begränsa och kontrollera åtkomst genom att tilldela en [Azure-principdefinition](../../../policy/overview.md) för granskning av konton med läsbehörigheter som inte har multifaktorautentisering aktiverad. Multifaktorautentisering hjälper till att skydda konton även om en del av autentiseringsinformationen äventyras. Genom att övervaka konton utan multifaktorautentisering aktiverat kan du identifiera konton som kan vara mer benägna att komprometteras.

- MFA ska aktiveras på konton med läsbehörighet för din prenumeration

## <a name="ia-5-authenticator-management"></a>IA-5 Autentiseringshantering

Den här skissen tilldelar [Azure Policy-definitioner](../../../policy/overview.md) som granskar virtuella Linux-datorer som tillåter fjärranslutningar från konton utan lösenord och/eller har felaktiga behörigheter för passwd-filen. Den här skissen tilldelar också principdefinitioner som granskar konfigurationen av lösenordskrypteringstypen för virtuella Windows-datorer. Genom att övervaka dessa indikatorer kan du se till att systemautentiseringspersonerna följer organisationens identifierings- och autentiseringsprincip.

- \[Preview\]: Audit Linux virtuella datorer som inte har passwd filbehörigheter inställd på 0644
- \[Preview:\]Granska virtuella Linux-datorer som har konton utan lösenord
- \[Preview\]: Granska virtuella Windows-datorer som inte lagrar lösenord med reversibla kryptering
- \[Förhandsversion\]: Distribuera krav för granskning av virtuella Linux-datorer som inte har passwd-filbehörigheterna inställda på 0644
- \[Preview\]: Distribuera krav för att granska virtuella Linux-datorer som har konton utan lösenord
- \[Preview\]: Distribuera krav för granskning av virtuella Windows-datorer som inte lagrar lösenord med reversibla kryptering

## <a name="ia-5-1-authenticator-management--password-based-authentication"></a>IA-5 (1) Förvaltning av autentiseringsperson | Lösenordsbaserad autentisering

Den här skissen hjälper dig att framtvinga starka lösenord genom att tilldela [Azure Policy-definitioner](../../../policy/overview.md) som granskar virtuella Windows-datorer som inte tillämpar minsta styrka och andra lösenordskrav. Medvetenhet om virtuella datorer i strid med principen om lösenordsstyrka hjälper dig att vidta korrigerande åtgärder för att säkerställa att lösenord för alla användarkonton för virtuella datorer följer organisationens lösenordsprincip.

- \[Preview\]: Granska virtuella windows-datorer som tillåter återanvändning av de tidigare 24 lösenorden
- \[Preview\]: Granska virtuella windows-datorer som inte har en maximal lösenordsålder på 70 dagar
- \[Preview\]: Granska virtuella windows-datorer som inte har en lägsta lösenordsålder på 1 dag
- \[Förhandsgranskning\]: Granska virtuella windows-datorer som inte har inställningen för lösenordskomplexitet aktiverad
- \[Preview\]: Granska virtuella windows-datorer som inte begränsar den minsta lösenordslängden till 14 tecken
- \[Preview\]: Granska virtuella Windows-datorer som inte lagrar lösenord med reversibla kryptering
- \[Förhandsversion\]: Distribuera krav för granskning av virtuella Windows-datorer som tillåter återanvändning av de tidigare 24 lösenorden
- \[Förhandsversion\]: Distribuera krav för granskning av virtuella Windows-datorer som inte har en maximal lösenordsålder på 70 dagar
- \[Förhandsversion\]: Distribuera krav för granskning av virtuella Windows-datorer som inte har en lägsta lösenordsålder på 1 dag
- \[Förhandsversion\]: Distribuera krav för granskning av virtuella Windows-datorer som inte har inställningen för lösenordskomplexitet aktiverad
- \[Förhandsgranskning\]: Distribuera krav för granskning av virtuella Windows-datorer som inte begränsar den minsta lösenordslängden till 14 tecken
- \[Preview\]: Distribuera krav för granskning av virtuella Windows-datorer som inte lagrar lösenord med reversibla kryptering

## <a name="ra-5-vulnerability-scanning"></a>RA-5 Sårbarhetsskanning

Den här skissen hjälper dig att hantera sårbarheter i informationssystem genom att tilldela [Azure Policy-definitioner](../../../policy/overview.md) som övervakar sårbarheter i operativsystemet, SQL-sårbarheter och sårbarheter för virtuella datorer i Azure Security Center. Azure Security Center tillhandahåller rapporteringsfunktioner som gör att du kan ha insikt i realtid om säkerhetstillståndet för distribuerade Azure-resurser. Den här skissen tilldelar också principdefinitioner som granskar och framtvingar avancerad datasäkerhet på SQL-servrar. Avancerad datasäkerhet inkluderade sårbarhetsbedömning och avancerade hotskyddsfunktioner som hjälper dig att förstå sårbarheter i dina distribuerade resurser.

- Avancerad datasäkerhet bör aktiveras på dina hanterade instanser
- Avancerad datasäkerhet bör aktiveras på dina SQL-servrar
- Distribuera avancerad datasäkerhet på SQL-servrar
- Säkerhetsproblem i säkerhetskonfigurationen på dina skaluppsättningar för virtuella datorer bör åtgärdas
- Sårbarheter i säkerhetskonfigurationen på dina virtuella datorer bör åtgärdas
- Säkerhetsproblem i DINA SQL-databaser bör åtgärdas
- Sårbarheter bör åtgärdas med en sårbarhetsbedömningslösning

## <a name="sc-5-denial-of-service-protection"></a>SC-5 Skydd över överbelastning

Azures ddos-standardnivå (Distributed Denial of Service) ger ytterligare funktioner och begränsningsfunktioner över den grundläggande tjänstnivån. Dessa ytterligare funktioner inkluderar Azure Monitor-integrering och möjligheten att granska rapporter efter attacken. Den här skissen tilldelar en [Azure-principdefinition](../../../policy/overview.md) som granskar om DDoS-standardnivån är aktiverad. Genom att förstå kapacitetsskillnaden mellan tjänstnivåerna kan du välja den bästa lösningen för att hantera överbelastningsskydd för din Azure-miljö.

- DDoS Protection Standard bör aktiveras

## <a name="sc-7-boundary-protection"></a>Gränsskydd för SC-7

Den här skissen hjälper dig att hantera och styra systemgränsen genom att tilldela en [Azure Policy-definition](../../../policy/overview.md) som övervakar för rekommendationer för nätverkssäkerhetsgrupper som härdar i Azure Security Center. Azure Security Center analyserar trafikmönster för Internet som är vänt mot virtuella datorer och tillhandahåller regelrekommendationer för nätverkssäkerhetsgrupper för att minska den potentiella angreppsytan.
Dessutom tilldelar den här skissen också principdefinitioner som övervakar oskyddade slutpunkter, program och lagringskonton. Slutpunkter och program som inte skyddas av en brandvägg och lagringskonton med obegränsad åtkomst kan ge oavsiktlig åtkomst till information som finns i informationssystemet.

- Regler för nätverkssäkerhetsgrupp för internetvända virtuella datorer bör skärpas
- Åtkomst via internetvänd slutpunkt bör begränsas
- Webbportar bör vara begränsade i nätverkssäkerhetsgrupper som är associerade till den virtuella datorn
- Granska obegränsad nätverksåtkomst till lagringskonton

## <a name="sc-7-3-boundary-protection--access-points"></a>SC-7 (3) Gränsskydd | Åtkomstpunkter

Åtkomst till virtuella datorer (JUST-in-time) låser sig för inkommande trafik till virtuella Azure-datorer, vilket minskar exponeringen för attacker samtidigt som enkel åtkomst till virtuella datorer vid behov kan anslutas till virtuella datorer. JIT virtuell dator åtkomst hjälper dig att begränsa antalet externa anslutningar till dina resurser i Azure. Den här skissen tilldelar en [Azure Policy-definition](../../../policy/overview.md) som hjälper dig att övervaka virtuella datorer som kan stödja just-in-time-åtkomst men som ännu inte har konfigurerats.

- Just-in-time-kontroller av nätverksåtkomst ska tillämpas på virtuella datorer

## <a name="sc-7-4-boundary-protection--external-telecommunications-services"></a>SC-7 (4) Gränsskydd | Externa telekommunikationstjänster

Åtkomst till virtuella datorer (JUST-in-time) låser sig för inkommande trafik till virtuella Azure-datorer, vilket minskar exponeringen för attacker samtidigt som enkel åtkomst till virtuella datorer vid behov kan anslutas till virtuella datorer. ÅTKOMST till virtuella datorer för JIT hjälper dig att hantera undantag från din trafikflödesprincip genom att underlätta åtkomstbegäran och godkännandeprocesser. Den här skissen tilldelar en [Azure Policy-definition](../../../policy/overview.md) som hjälper dig att övervaka virtuella datorer som kan stödja just-in-time-åtkomst men som ännu inte har konfigurerats.

- Just-in-time-kontroller av nätverksåtkomst ska tillämpas på virtuella datorer

## <a name="sc-8-1-transmission-confidentiality-and-integrity--cryptographic-or-alternate-physical-protection"></a>SC-8 (1) Sekretess och integritet för överföring | Kryptografiskt eller alternativt fysiskt skydd

Den här skissen hjälper dig att skydda den konfidentiella och integriteten hos överförd information genom att tilldela [Azure Policy-definitioner](../../../policy/overview.md) som hjälper dig att övervaka kryptografisk mekanism som implementeras för kommunikationsprotokoll. Om du säkerställer att kommunikationen är korrekt krypterad kan du uppfylla organisationens krav eller skydda information från obehörigt röjande och ändring.

- API-app bör endast vara tillgänglig via HTTPS
- Granska Windows-webbservrar som inte använder säkra kommunikationsprotokoll
- Distribuera krav för granskning av Windows-webbservrar som inte använder säkra kommunikationsprotokoll
- Funktionsappen ska endast vara tillgänglig via HTTPS
- Endast säkra anslutningar till Redis-cachen ska aktiveras
- Säker överföring till lagringskonton ska vara aktiverat
- Webbprogrammet ska endast vara tillgängligt via HTTPS

## <a name="sc-28-1-protection-of-information-at-rest--cryptographic-protection"></a>SC-28 (1) Skydd av information i vila | Kryptografiskt skydd

Den här skissen hjälper dig att tillämpa din princip om användningen av kryptografkontroller för att skydda information i vila genom att tilldela [Azure Policy-definitioner](../../../policy/overview.md) som tillämpar specifika kryptografkontroller och granskningsanvändning av svaga kryptografiska inställningar. Om du förstår var dina Azure-resurser kan ha icke-optimala kryptografiska konfigurationer kan du vidta korrigerande åtgärder för att säkerställa att resurser konfigureras i enlighet med din informationssäkerhetsprincip. De principdefinitioner som tilldelas av den här skissen kräver kryptering för lagringskonton för datasjöar. kräver transparent datakryptering i SQL-databaser. och granska kryptering som saknas i SQL-databaser, diskar för virtuella datorer och automatiseringskonto.

- Avancerad datasäkerhet bör aktiveras på dina hanterade instanser
- Avancerad datasäkerhet bör aktiveras på dina SQL-servrar
- Distribuera avancerad datasäkerhet på SQL-servrar
- Distribuera SQL DB-transparent datakryptering
- Diskkryptering bör tillämpas på virtuella datorer
- Kräv kryptering på DataSjölagringskonton
- Transparent datakryptering i SQL-databaser bör aktiveras

## <a name="si-2-flaw-remediation"></a>SI-2 Fel sanering

Den här skissen hjälper dig att hantera brister i informationssystemet genom att tilldela [Azure-principdefinitioner](../../../policy/overview.md) som övervakar systemuppdateringar som saknas, sårbarheter i operativsystemet, SQL-sårbarheter och sårbarheter för virtuella datorer i Azure Security Center. Azure Security Center tillhandahåller rapporteringsfunktioner som gör att du kan ha insikt i realtid om säkerhetstillståndet för distribuerade Azure-resurser. Den här skissen tilldelar också en principdefinition som säkerställer korrigering av operativsystemet för skalningsuppsättningar för virtuella datorer.

- Kräv automatisk OS-bildkorrigering på skalningsuppsättningar för virtuella datorer
- Systemuppdateringar på skalningsuppsättningar för virtuella datorer bör installeras
- Systemuppdateringar bör installeras på dina virtuella datorer
- Säkerhetsproblem i säkerhetskonfigurationen på dina skaluppsättningar för virtuella datorer bör åtgärdas
- Sårbarheter i säkerhetskonfigurationen på dina virtuella datorer bör åtgärdas
- Säkerhetsproblem i DINA SQL-databaser bör åtgärdas
- Sårbarheter bör åtgärdas med en sårbarhetsbedömningslösning

## <a name="si-3-malicious-code-protection"></a>SI-3 Skydd mot skadlig kod

Den här skissen hjälper dig att hantera slutpunktsskydd, inklusive skadligt kodskydd, genom att tilldela [Azure Policy-definitioner](../../../policy/overview.md) som övervakar saknade slutpunktsskydd på virtuella datorer i Azure Security Center och framtvingar Microsofts lösning mot skadlig kod på virtuella Windows-datorer.

- Distribuera standardtillägget Microsoft IaaSAntimalware för Windows Server
- Slutpunktsskyddslösningen bör installeras på skalningsuppsättningar för virtuella datorer
- Övervaka saknade slutpunktsskydd i Azure Security Center

## <a name="si-3-1-malicious-code-protection--central-management"></a>SI-3 (1) Skydd mot skadlig kod | Central ledning

Den här skissen hjälper dig att hantera slutpunktsskydd, inklusive skadligt kodskydd, genom att tilldela [Azure-principdefinitioner](../../../policy/overview.md) som övervakar saknade slutpunktsskydd på virtuella datorer i Azure Security Center. Azure Security Center tillhandahåller centraliserade hanterings- och rapporteringsfunktioner som gör att du kan ha insikt i realtid om säkerhetstillståndet för distribuerade Azure-resurser.

- Slutpunktsskyddslösningen bör installeras på skalningsuppsättningar för virtuella datorer
- Övervaka saknade slutpunktsskydd i Azure Security Center

## <a name="si-4-information-system-monitoring"></a>ÖVERVAKNING AV INFORMATIONSSYSTEM I SI-4

Den här skissen hjälper dig att övervaka ditt system genom att granska och upprätthålla loggning och datasäkerhet över Azure-resurser. Närmare bestämt de principer som tilldelats granskning och framtvinga distribution av Log Analytics-agenten och förbättrade säkerhetsinställningar för SQL-databaser, lagringskonton och nätverksresurser. Dessa funktioner kan hjälpa dig att upptäcka avvikande beteende och indikatorer på attacker så att du kan vidta lämpliga åtgärder.

- \[Förhandsversion:\]Distribution av granskningslogganalysagent – OS-avbildning (VM)
- \[Förhandsversion:\]Distribution av granskningslogganalysagent i VMSS - VM-avbildning (OS) olistad
- \[Förhandsversion:\]Arbetsyta för granskningslogganalys för virtuell dator – rapportfelmatchning
- \[Förhandsversion:\]Distribuera logganalysagent för VMSS (Linux VMSS)
- \[Preview\]: Distribuera LoggAnalys agent för Linux virtuella datorer
- \[Förhandsversion:\]Distribuera logganalysagent för VMSS (Windows VMSS)
- \[Förhandsgranskning:\]Distribuera logganalysagent för virtuella Windows-datorer
- Avancerad datasäkerhet bör aktiveras på dina hanterade instanser
- Avancerad datasäkerhet bör aktiveras på dina SQL-servrar
- Distribuera avancerad datasäkerhet på SQL-servrar
- Distribuera avancerat skydd mot hot på lagringskonton
- Distribuera granskning på SQL-servrar
- Distribuera nätverksbevakare när virtuella nätverk skapas
- Distribuera hotidentifiering på SQL-servrar

> [!NOTE]
> Tillgängligheten för specifika Azure-principdefinitioner kan variera i Azure Government och andra nationella moln. 

## <a name="next-steps"></a>Nästa steg

Nu när du har granskat kontrollmappningen av FedRAMP Moderbutorn kan du besöka följande artiklar om hur du beskriver skissen och hur du distribuerar det här exemplet:

> [!div class="nextstepaction"]
> [FedRAMP Måttlig plan - Översikt](./index.md)
> [FodRAMP Måttlig plan - Distribuera steg](./deploy.md)

Ytterligare artiklar om skisser och hur de används:

- Läs mer om [skisslivscykeln](../../concepts/lifecycle.md).
- Förstå hur du använder [statiska och dynamiska parametrar](../../concepts/parameters.md).
- Lär dig att anpassa [ordningsföljden för skisssekvensering](../../concepts/sequencing-order.md).
- Ta reda på hur du använder [skiss resurs låsning](../../concepts/resource-locking.md).
- Lär dig hur du [uppdaterar befintliga tilldelningar](../../how-to/update-existing-assignments.md).