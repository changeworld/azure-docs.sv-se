---
title: Distribuera hipaa HItrust-ritningsprov
description: Distribuera steg för HIPAA HITRUST-skissexemplet, inklusive information om skissartefaktparameter.
ms.date: 12/03/2019
ms.topic: sample
ms.openlocfilehash: a4277dbbf40a9ab8651598203e9279bca00eb164
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "75472305"
---
# <a name="deploy-the-hipaa-hitrust-blueprint-sample"></a>Distribuera hipaa HItrust-skissprovet

För att distribuera HIPAA HITRUST-skissexemplet måste följande steg vidtas:

> [!div class="checklist"]
> - Skapa en ny skiss från exemplet
> - Markera ditt exemplar av exemplet som **publicerat**
> - Tilldela ditt exemplar av skissen till en befintlig prenumeration

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free) konto innan du börjar.

## <a name="create-blueprint-from-sample"></a>Skapa skiss från exempel

Implementera först skissprovet genom att skapa en ny skiss i din miljö med hjälp av exemplet som en förrätt.

1. Välj **Alla tjänster** och sök efter och välj **Princip** i den vänstra rutan. Välj **Skisser**på sidan **Princip** .

1. På sidan **Komma igång till** vänster väljer du knappen **Skapa** under Skapa _en skiss_.

1. Leta reda på **hipaa** HItrust-skissexemplet under _Andra exempel_ och välj **Använd det här exemplet**.

1. Ange _grunderna_ i skissexemplet:

   - **Skissnamn:** Ange ett namn för ditt exemplar av skissexemplet.
   - **Definitionsplats**: Använd ellipsen och välj hanteringsgruppen för att spara din kopia av exemplet.

1. Välj fliken _Artefakter_ högst upp på sidan eller **Nästa: Artefakter** längst ned på sidan.

1. Granska listan över artefakter som utgör skissexemplet. Många av artefakterna har parametrar som vi definierar senare. Välj **Spara utkast** när du har granskat skissexemplet.

## <a name="publish-the-sample-copy"></a>Publicera exempelkopian

Ditt exemplar av skissexemplet har nu skapats i din miljö. Den skapas i **utkastläge** och måste **publiceras** innan den kan tilldelas och distribueras. Kopian av skissprovet kan anpassas till din miljö och dina behov, men den ändringen kan flytta den bort från standarden.

1. Välj **Alla tjänster** och sök efter och välj **Princip** i den vänstra rutan. Välj **Skisser**på sidan **Princip** .

1. Välj sidan **Skissdefinitioner** till vänster. Använd filtren för att hitta kopian av skissexemplet och välj det sedan.

1. Välj **Publicera skiss** högst upp på sidan. På den nya sidan till höger anger du en **version** för ditt exemplar av skissexemplet. Den här egenskapen är användbar om du gör en ändring senare. Ange **ändringsanteckningar** som "Första versionen som publiceras från hitrust HIPAA-skissexemplet". Välj sedan **Publicera** längst ned på sidan.

## <a name="assign-the-sample-copy"></a>Tilldela exempelkopian

När kopian av skissexemplet har **publicerats**kan den tilldelas en prenumeration inom den hanteringsgrupp som den sparades till. Det här steget är där parametrar tillhandahålls för att göra varje distribution av kopian av skissexemplet unikt.

1. Välj **Alla tjänster** och sök efter och välj **Princip** i den vänstra rutan. Välj **Skisser**på sidan **Princip** .

1. Välj sidan **Skissdefinitioner** till vänster. Använd filtren för att hitta kopian av skissexemplet och välj det sedan.

1. Välj **Tilldela skiss** högst upp på skissdefinitionssidan.

1. Ange parametervärdena för skisstilldelningen:

   - Grundläggande inställningar

     - **Prenumerationer**: Välj en eller flera av prenumerationerna i hanteringsgruppen som du sparade din kopia av skissexemplet till. Om du väljer mer än en prenumeration skapas en tilldelning för var och en med de angivna parametrarna.
     - **Tilldelningsnamn**: Namnet är förifyllt för dig baserat på namnet på skissen.
       Ändra efter behov eller lämna som det är.
     - **Plats**: Välj en region för den hanterade identitet som ska skapas i. Azure Blueprint använder den här hanterade identiteten för att distribuera alla artefakter i den tilldelade skissen. Mer information finns i [hanterade identiteter för Azure-resurser](../../../../active-directory/managed-identities-azure-resources/overview.md).
     - **Blueprint definition version**: Välj en **publicerad** version av ditt exemplar av skissen provet.

   - Lås tilldelning

     Välj inställningen för skisslås för din miljö. Mer information finns i [Låsa skissresurser](../../concepts/resource-locking.md).

   - Hanterad identitet

     Lämna _standardalternativet för tilldelade_ hanterade identiteter.

   - Artefaktparametrar

     De parametrar som definieras i det här avsnittet gäller för den artefakt som det definieras under. Dessa parametrar är [dynamiska parametrar](../../concepts/parameters.md#dynamic-parameters) eftersom de har definierats under tilldelningen av skissen. En fullständig lista eller artefaktparametrar och deras beskrivningar finns i [tabellen Artefaktparametrar](#artifact-parameters-table).

1. När alla parametrar har angetts väljer du **Tilldela** längst ned på sidan. Skisstilldelningen skapas och artefaktdistributionen börjar. Distributionen tar ungefär en timme. Om du vill kontrollera status för distributionen öppnar du skisstilldelningen.

> [!WARNING]
> Azure Blueprints-tjänsten och de inbyggda skissexemplen är **kostnadsfria**. Azure-resurser [prissätts efter produkt](https://azure.microsoft.com/pricing/). Använd [priskalkylatorn](https://azure.microsoft.com/pricing/calculator/) för att uppskatta kostnaden för att köra resurser som distribueras av det här skissexemplet.

## <a name="artifact-parameters-table"></a>Tabellen Artefaktparametrar

Följande tabell innehåller en lista över parametrarna för skissartefakt:

Artefaktnamn|Artefakttyp|Parameternamn|Beskrivning|
|-|-|-|-|
|\[Preview\]: Distribuera LoggAnalys agent för Linux virtuella datorer |Principtilldelning |Log Analytics arbetsyta för virtuella Linux-datorer |Mer information finns [i Skapa en log analytics-arbetsyta i Azure-portalen](../../../../azure-monitor/learn/quick-create-workspace.md). |
|\[Preview\]: Distribuera LoggAnalys agent för Linux virtuella datorer |Principtilldelning |Valfritt: Lista över VM-avbildningar som har stöd för Linux OS för att lägga till i omfång |En tom matris kan användas för att ange inga valfria parametrar:`[]` |
|\[Förhandsgranskning:\]Distribuera logganalysagent för virtuella Windows-datorer |Principtilldelning |Valfritt: Lista över vm-avbildningar som har stöd för Windows OS att lägga till i omfång |En tom matris kan användas för att ange inga valfria parametrar:`[]` |
|\[Förhandsgranskning:\]Distribuera logganalysagent för virtuella Windows-datorer |Principtilldelning |Log Analytics-arbetsyta för virtuella Windows-datorer |Mer information finns [i Skapa en log analytics-arbetsyta i Azure-portalen](../../../../azure-monitor/learn/quick-create-workspace.md). |
|\[Förhandsversion:\]Granska HITRUST HIPAA-kontroller och distribuera specifika VM-tillägg för att stödja granskningskrav |Principtilldelning |Logganalys arbetsyte-ID som virtuella datorer ska konfigureras för |Det här är ID (GUID) på arbetsytan Log Analytics som de virtuella datorerna ska konfigureras för. |
|\[Förhandsversion:\]Granska HITRUST HIPAA-kontroller och distribuera specifika VM-tillägg för att stödja granskningskrav |Principtilldelning |Lista över resurstyper som ska ha diagnostikloggar aktiverade |Lista över resurstyper som ska granskas om diagnostiklogginställningen inte är aktiverad. Acceptabla värden finns i [Azure Monitor diagnostikloggar scheman](../../../../azure-monitor/platform/diagnostic-logs-schema.md#supported-log-categories-per-resource-type). |
|\[Förhandsversion:\]Granska HITRUST HIPAA-kontroller och distribuera specifika VM-tillägg för att stödja granskningskrav |Principtilldelning |Administratörsgruppen |Grupp. Exempel: `Administrator; myUser1; myUser2` |
|\[Förhandsversion:\]Granska HITRUST HIPAA-kontroller och distribuera specifika VM-tillägg för att stödja granskningskrav |Principtilldelning |Lista över användare som ska ingå i gruppen Administratörer för Windows VM |En semikolonavgränsad lista över medlemmar som ska ingå i den lokala gruppen Administratörer. Exempel: `Administrator; myUser1; myUser2` |
|Distribuera avancerat skydd mot hot på lagringskonton |Principtilldelning |Verkan |Information om principeffekter finns på [Förstå Azure-principeffekter](../../../policy/concepts/effects.md). |
|Distribuera granskning på SQL-servrar |Principtilldelning |Värdet i dagar av kvarhållningsperioden (0 anger obegränsad kvarhållning) |Kvarhållningsdagar (valfritt, _180_ dagar om det inte anges) |
|Distribuera granskning på SQL-servrar |Principtilldelning |Resursgruppsnamn för lagringskonto för SQL-servergranskning |Granskning skriver databashändelser till en granskningslogg i ditt Azure Storage-konto (ett lagringskonto skapas i varje region där en SQL Server skapas som delas av alla servrar i den regionen). Viktigt - för korrekt drift av granskning tar inte bort eller byter namn på resursgruppen eller lagringskontona. |
|Distribuera diagnostikinställningar för nätverkssäkerhetsgrupper |Principtilldelning |Prefix för lagringskonto för diagnostik av nätverkssäkerhetsgrupper |Det här prefixet kombineras med nätverkssäkerhetsgruppsplatsen för att skapa det skapade lagringskontonamnet. |
|Distribuera diagnostikinställningar för nätverkssäkerhetsgrupper |Principtilldelning |Resursgruppsnamn för lagringskonto för nätverkssäkerhetsgruppdiagnostik (måste finnas) |Resursgruppen som lagringskontot skapas i. Den här resursgruppen måste redan finnas. |

## <a name="next-steps"></a>Nästa steg

Nu när du har granskat stegen för att distribuera HITRUST HIPAA-exemplet kan du läsa följande artiklar om översikten och kontrollmappningen:

> [!div class="nextstepaction"]
> [HITRUST HIPAA ritningar - Översikt](./index.md)
> [HITRUST HIPAA ritningar - Kontroll kartläggning](./control-mapping.md)

Ytterligare artiklar om skisser och hur de används:

- Läs mer om [skisslivscykeln](../../concepts/lifecycle.md).
- Förstå hur du använder [statiska och dynamiska parametrar](../../concepts/parameters.md).
- Lär dig att anpassa [ordningsföljden för skisssekvensering](../../concepts/sequencing-order.md).
- Ta reda på hur du använder [skiss resurs låsning](../../concepts/resource-locking.md).
- Lär dig hur du [uppdaterar befintliga tilldelningar](../../how-to/update-existing-assignments.md).