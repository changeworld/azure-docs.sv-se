---
title: Min första PowerShell-arbetsflödeskörningsbok i Azure Automation
description: Självstudiekurs som steg för steg beskriver hur du skapar, testar och publicerar en enkel text-runbook med hjälp av PowerShell Workflow.
keywords: powershell-arbetsflöde, powershell-arbetsflödesexempel, arbetsflöde powershell
services: automation
ms.subservice: process-automation
ms.date: 09/24/2018
ms.topic: conceptual
ms.openlocfilehash: a5a1cad3179063f75a5d9a19567624180b5793a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367269"
---
# <a name="my-first-powershell-workflow-runbook"></a>Min första PowerShell Workflow-runbook

> [!div class="op_single_selector"]
> * [Grafisk](automation-first-runbook-graphical.md)
> * [Powershell](automation-first-runbook-textual-powershell.md)
> * [PowerShell-arbetsflöde](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)

Den här självstudien beskriver steg för steg hur du skapar en [PowerShell Workflow-runbook](automation-runbook-types.md#powershell-workflow-runbooks) i Azure Automation. Börja med en enkel runbook som du testar och publicerar samtidigt som du lär dig hur du spårar status för runbook-jobbet. Ändra sedan runbooken för att faktiskt hantera Azure-resurser, vilket illustreras genom att starta en virtuell Azure-dator. Sist, göra runbook mer robust genom att lägga runbook parametrar.

>[!NOTE]
>Den här artikeln har uppdaterats till att använda den nya Azure PowerShell Az-modulen. Du kan fortfarande använda modulen AzureRM som kommer att fortsätta att ta emot felkorrigeringar fram till december 2020 eller längre. Mer information om den nya Az-modulen och AzureRM-kompatibilitet finns i [Introduktion till den nya Azure PowerShell Az-modulen](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Installationsinstruktioner för Az-modul på hybridkörningsarbetaren finns [i Installera Azure PowerShell-modulen](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). För ditt Automation-konto kan du uppdatera dina moduler till den senaste versionen med [så här uppdaterar du Azure PowerShell-moduler i Azure Automation](automation-update-azure-modules.md).

## <a name="prerequisites"></a>Krav

För att slutföra den här kursen behöver du:

* En Azure-prenumeration. Om du inte redan har ett konto kan du [aktivera dina MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) eller registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Ett [Automation-konto för Azure](automation-offering-get-started.md) som runbooken ska ligga under och som ska användas för autentisering mot Azure-resurser. Det här kontot måste ha behörighet att starta och stoppa den virtuella datorn.
* En virtuell dator i Azure. Eftersom du stoppar och startar den här datorn bör det inte vara en virtuell produktionsdator.

## <a name="step-1---create-new-runbook"></a>Steg 1 – Skapa en ny runbook

Börja med att skapa en enkel `Hello World`runbook som matar ut texten .

1. Öppna ditt Automation-konto på Azure Portal.

   Automation-kontosidan innehåller en snabb översikt över resurserna i det här kontot. Du bör redan ha vissa tillgångar. De flesta av dessa tillgångar är de moduler som automatiskt ingår i ett nytt Automation-konto. Du bör också ha den autentiseringstillgång som är kopplad till din prenumeration.
 
1. Välj **Runbooks** under **Process Automation** om du vill öppna listan över runbooks.
1. Skapa en ny runbook genom att välja **Skapa en runbook**.
1. Ge runbooken namnet **MyFirstRunbook-Workflow**.
1. I det här fallet ska du skapa en [PowerShell Workflow runbook](automation-runbook-types.md#powershell-workflow-runbooks). Välj **PowerShell-arbetsflöde** för **runbooktyp**.
1. Klicka på **Skapa** för att skapa runbooken och öppna textredigeraren.

## <a name="step-2---add-code-to-the-runbook"></a>Steg 2 – Lägga till kod i runbooken

Du kan antingen skriva kod direkt i runbooken eller välja cmdlets, runbooks och assets från bibliotekskontrollen och lägga till dem i runbooken med relaterade parametrar. För den här självstudien skriver du kod direkt i runbooken.

1. Runbooken är för närvarande `Workflow` tom med endast önskat nyckelord, namnet på runbooken och de klammerparenteser som innesluter hela arbetsflödet.

   ```powershell-interactive
   Workflow MyFirstRunbook-Workflow
   {
   }
   ```

1. Skriv `Write-Output "Hello World"` mellan klammerparenteserna.

   ```powershell-interactive
   Workflow MyFirstRunbook-Workflow
   {
   Write-Output "Hello World"
   }
   ```

1. Spara runbooken genom att klicka på **Spara**.

## <a name="step-3---test-the-runbook"></a>Steg 3 – Testa runbooken

Innan du publicerar runbooken för att göra den tillgänglig i produktionen bör du testa den för att se till att den fungerar som den ska. När du testar en runbook körs dess utkastversion och du kan visa dess utdata interaktivt.

1. Välj **Testfönster** för att öppna testfönstret.
1. Klicka på **Start** för att starta testet, med testning det enda aktiverade alternativet.
1. Observera att ett [runbook-jobb](automation-runbook-execution.md) skapas och att dess status visas i fönstret.

   Jobbstatusen börjar `Queued`som , vilket anger att jobbet väntar på att en runbook-arbetare i molnet ska bli tillgänglig. Statusen ändras till `Starting` när en anställd gör anspråk på jobbet. Slutligen blir `Running` statusen när runbooken faktiskt börjar köras.

1. När runbook-jobbet är klart visar testfönstret utdata. I det här `Hello World`fallet ser du .

   ![Hello World](media/automation-first-runbook-textual/test-output-hello-world.png)

1. Gå tillbaka till arbetsytan genom att stänga testfönstret.

## <a name="step-4---publish-and-start-the-runbook"></a>Steg 4 – Publicera och starta runbooken

Runbook som du har skapat är fortfarande i utkastläge. Du måste publicera den innan du kan köra den i produktion. När du publicerar en runbook skriver du över den befintliga publicerade versionen med utkastversionen. I det här fallet har du ingen publicerad version ännu eftersom du precis har skapat runbook-jobbet.

1. Klicka på **Publicera** för att publicera runbooken och sedan på **Ja** när du uppmanas att göra det.
1. Bläddra åt vänster om du vill visa runbooken på sidan **Runbooks** och observera att fältet **Redigeringsstatus** är inställt på **Publicerad**.
1. Bläddra tillbaka till höger för att visa sidan för **MyFirstRunbook-Workflow**.

   Alternativen överst gör att du kan starta runbooken nu, schemalägga en framtida starttid eller skapa en [webhook](automation-webhooks.md) så att runbooken kan startas via ett HTTP-anrop.

1. Välj **Start** och sedan **Ja** när du uppmanas att starta runbooken.

   ![Starta runbooken](media/automation-first-runbook-textual/automation-runbook-controls-start.png)

1. Ett jobbfönster öppnas för det runbook-jobb som har skapats. Lämna i så fall fönstret öppet så att du kan titta på jobbets förlopp.

1. Observera att jobbstatusen visas i **Projektsammanfattning**. Den här statusen matchar de statusar som du såg när du testade runbooken.

   ![Jobbsammanfattning](media/automation-first-runbook-textual/job-pane-status-blade-jobsummary.png)

1. När runbook-statusen visar `Completed`klickar du på **Utdata**. Utdatasidan öppnas, där du `Hello World` kan se ditt meddelande.

   ![Jobbsammanfattning](media/automation-first-runbook-textual/job-pane-status-blade-outputtile.png)

1. Stäng utdatasidan.

1. Klicka på **Alla loggar** för att öppna fönstret Strömmar för runbook-jobbet. Du bör `Hello World` bara se i utdataströmmen. Observera att fönstret Strömmar kan visa andra strömmar för ett runbook-jobb, till exempel utförliga och felströmmar, om runbooken skriver till dem.

   ![Jobbsammanfattning](media/automation-first-runbook-textual/job-pane-status-blade-alllogstile.png)

1. Stäng fönstret Strömmar och jobbfönstret för att återgå till sidan **MyFirstRunbook.**
1. Klicka på **Jobb** under **Resurser** om du vill öppna sidan Jobb för den här runbooken. På den här sidan visas alla jobb som skapas av runbooken. Du bör bara se ett jobb som anges, eftersom du bara har kört jobbet en gång.

   ![Jobb](media/automation-first-runbook-textual/runbook-control-job-tile.png)

1. Klicka på jobbnamnet om du vill öppna samma jobbfönster som du visade när du startade runbooken. Använd det här fönstret om du vill visa information om alla jobb som skapats för runbooken.

## <a name="step-5---add-authentication-to-manage-azure-resources"></a>Steg 5 – Lägga till autentisering för att hantera Azure-resurser

Du har testat och publicerat din runbook, men hittills gör den egentligen inget användbart. Du vill att den ska hantera Azure-resurser. Det kan inte göra det om det inte autentiserar med hjälp av autentiseringsuppgifterna för prenumerationen. Autentisering `Connect-AzAccount` använder cmdlet.

>[!NOTE]
>För `Add-AzAccount` PowerShell-runbooks `Add-AzureRMAccount` och är `Connect-AzAccount`alias för . Du kan använda dessa cmdlets eller så kan du [uppdatera dina moduler](automation-update-azure-modules.md) i ditt Automation-konto till de senaste versionerna. Du kan behöva uppdatera dina moduler även om du just har skapat ett nytt Automation-konto.

1. Navigera till sidan **MyFirstRunbook-Workflow** och öppna textredigeraren genom att klicka på **Redigera**.
2. Ta `Write-Output` bort raden.
3. Placera markören på en tom rad mellan klammerparenteserna.
4. Skriv in eller kopiera och klistra in följande kod, som hanterar autentiseringen med ditt Automation Run As-konto.

   ```powershell-interactive
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID `
   -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $AzureContext = Select-AzSubscription -SubscriptionId $Conn.SubscriptionID
   ```

1. Klicka på **Testfönstret** så att du kan testa runbooken.
1. Starta testet genom att klicka på **Starta**. När den är klar bör du se utdata som liknar följande och visa grundläggande information från ditt konto. Den här åtgärden bekräftar att autentiseringsuppgifterna är giltiga.

   ![Autentisera](media/automation-first-runbook-textual/runbook-auth-output.png)

## <a name="step-6---add-code-to-start-a-virtual-machine"></a>Steg 6 – Lägga till kod för att starta en virtuell dator

Nu när din runbook autentiserar till Azure-prenumerationen kan du hantera resurser. Nu ska vi lägga till ett kommando för att starta en virtuell dator. Du kan välja vilken virtuell dator som helst i din Azure-prenumeration och för tillfället är du hårdkodning det namnet i runbooken. Om du hanterar resurser över flera prenumerationer måste `AzContext` du använda parametern med cmdleten [Get-AzContext.](/powershell/module/az.accounts/get-azcontext)

1. Ange namn- och resursgruppnamnet för den virtuella datorn som ska börja med att ange ett anrop till [Start-AzVM-cmdleten](https://docs.microsoft.com/powershell/module/Az.Compute/Start-AzVM?view=azps-3.5.0
) som visas nedan. 

   ```powershell-interactive
   workflow MyFirstRunbook-Workflow
   {
   # Ensures that you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $AzureContext = Get-AzSubscription -SubscriptionId $Conn.SubscriptionID

   Start-AzVM -Name 'VMName' -ResourceGroupName 'ResourceGroupName' -AzContext $AzureContext
   }
   ```

1. Spara runbooken och klicka sedan på **Testfönstret** så att du kan testa den.
1. Starta testet genom att klicka på **Starta**. När den är klar kontrollerar du att den virtuella datorn har startats.

## <a name="step-7---add-an-input-parameter-to-the-runbook"></a>Steg 7 – Lägga till en indataparameter i runbooken

Runbook startar för närvarande den virtuella datorn som du har hårdkodat i runbooken. Det blir mer användbart om du kan ange den virtuella datorn när runbooken startas. Nu ska vi lägga till indataparametrar i runbooken för att tillhandahålla den funktionen.

1. Lägg till variabler för `VMName` och `ResourceGroupName` parametrar i runbooken `Start-AzVM` och använd variablerna med cmdleten enligt nedan.

   ```powershell-interactive
   workflow MyFirstRunbook-Workflow
   {
    Param(
     [string]$VMName,
     [string]$ResourceGroupName
    )
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   Start-AzVM -Name $VMName -ResourceGroupName $ResourceGroupName
   }
   ```

2. Spara runbooken och öppna Testfönster. Du kan nu ange värden för de två indatavariabler som finns i testet.
3. Stäng Testfönster.
4. Klicka på **Publicera** för att publicera den nya versionen av runbooken.
5. Stoppa den virtuella datorn som du har startat.
6. Starta runbooken genom att klicka på **Starta**. 
7. Skriv in värdena för **VMNAME** och **RESOURCEGROUPNAME** för den virtuella datorn som du ska starta.

   ![Starta runbooken](media/automation-first-runbook-textual/automation-pass-params.png)

8. När runbooken är klar kontrollerar du att den virtuella datorn har startat.

## <a name="next-steps"></a>Nästa steg

* Mer information om PowerShell, inklusive språkreferens- och utbildningsmoduler, finns i [PowerShell Docs](https://docs.microsoft.com/powershell/scripting/overview).
* Kom igång med grafiska runbooks finns i [Min första grafiska runbook](automation-first-runbook-graphical.md).
* Information om hur du kommer igång med PowerShell-runbooks finns i [Min första PowerShell-runbook](automation-first-runbook-textual-powershell.md).
* Mer information om runbook-typer och deras fördelar och begränsningar finns i [Azure Automation-runbooktyper](automation-runbook-types.md).
* Mer information om supportfunktioner för PowerShell-skript finns i [Stöd för Inbyggt PowerShell-skript i Azure Automation](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/).
