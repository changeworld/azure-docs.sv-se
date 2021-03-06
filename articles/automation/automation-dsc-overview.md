---
title: Översikt över statuskonfiguration
description: En översikt över DSC (Azure Automation State Configuration), dess villkor och kända problem
keywords: powershell dsc, önskad tillståndskonfiguration, powershell dsc azure
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.date: 11/06/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 02e540c9ac2654be97ff247749d1ed18573cfc24
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/10/2020
ms.locfileid: "81010264"
---
# <a name="state-configuration-overview"></a>Översikt över statuskonfiguration

Azure Automation State Configuration är en Azure-tjänst som låter dig skriva, hantera och kompilera DSC-konfiguration [(.](/powershell/scripting/dsc/configurations/configurations) Tjänsten importerar också [DSC-resurser](/powershell/scripting/dsc/resources/resources)och tilldelar konfigurationer till målnoder, allt i molnet.

## <a name="why-use-azure-automation-state-configuration"></a>Varför använda Azure Automation State Configuration

Azure Automation State Configuration ger flera fördelar jämfört med att använda DSC utanför Azure.

### <a name="built-in-pull-server"></a>Inbyggd pull-server

Azure Automation State Configuration tillhandahåller en DSC-pull-server som liknar [Windows Feature DSC-Service](/powershell/scripting/dsc/pull-server/pullserver). Målnoder kan automatiskt ta emot konfigurationer, överensstämma med önskat tillstånd och rapportera om deras efterlevnad. Den inbyggda pull-servern i Azure Automation eliminerar behovet av att konfigurera och underhålla din egen pull-server. Azure Automation kan rikta virtuella eller fysiska Windows- eller Linux-datorer, i molnet eller lokalt.

### <a name="management-of-all-your-dsc-artifacts"></a>Hantering av alla dina DSC-artefakter

Azure Automation State Configuration ger samma hanteringslager till [PowerShell önskad tillståndskonfiguration](/powershell/scripting/dsc/overview/overview) som den erbjuder för PowerShell-skript. Från Azure-portalen eller från PowerShell kan du hantera alla dina DSC-konfigurationer, resurser och målnoder.

![Skärmbild av sidan Azure Automation](./media/automation-dsc-overview/azure-automation-blade.png)

### <a name="import-of-reporting-data-into-azure-monitor-logs"></a>Importera rapporteringsdata till Azure Monitor-loggar

Noder som hanteras med Azure Automation State Configuration skickar detaljerade statusdata för rapportering till den inbyggda pull-servern. Du kan konfigurera Azure Automation State Configuration för att skicka dessa data till din Log Analytics-arbetsyta. Se [Rapportera rapporteringsdata för Azure Automation-tillståndskonfiguration till Azure Monitor-loggar](automation-dsc-diagnostics.md).

## <a name="prerequisites-for-using-azure-automation-state-configuration"></a>Förutsättningar för att använda Azure Automation State Configuration

Tänk på följande krav när du använder Azure Automation State Configuration för DSC.

### <a name="operating-system-requirements"></a>Operativsystemskrav

För noder som kör Windows stöds följande versioner:

- Windows Server 2019
- Windows Server 2016
- Windows Server 2012R2
- Windows Server 2012
- Windows Server 2008 R2 SP1
- Windows 10
- Windows 8.1
- Windows 7

>[!NOTE]
>Den fristående produkten SKU för [Microsoft Hyper-V Server](/windows-server/virtualization/hyper-v/hyper-v-server-2016) innehåller ingen implementering av DSC. Det kan därför inte hanteras av PowerShell DSC eller Azure Automation State Configuration.

För noder som kör Linux stöder DSC Linux-tillägget alla Linux-distributioner som anges under [Linux-distributioner som stöds](https://github.com/Azure/azure-linux-extensions/tree/master/DSC#4-supported-linux-distributions).

### <a name="dsc-requirements"></a>DSC-krav

För alla Windows-noder som körs i Azure installeras [WMF 5.1](https://docs.microsoft.com/powershell/scripting/wmf/setup/install-configure) under introduktionen. För noder som kör Windows Server 2012 och Windows 7 är [WinRM](https://docs.microsoft.com/powershell/scripting/dsc/troubleshooting/troubleshooting#winrm-dependency) aktiverat.

För alla Linux-noder som körs i Azure installeras [PowerShell DSC för Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux) under introduktionen.

### <a name="configuration-of-private-networks"></a><a name="network-planning"></a>Konfiguration av privata nätverk

Om noderna finns i ett privat nätverk krävs följande port och webbadresser. Dessa resurser tillhandahåller nätverksanslutning för den hanterade noden och tillåter DSC att kommunicera med Azure Automation.

* Port: Endast TCP 443 krävs för utgående internetåtkomst
* Global URL: ***.azure-automation.net**
* Global WEBBADRESS för US Gov Virginia: ***.azure-automation.us**
* Agenttjänst: **https://\<arbetsytaId\>.agentsvc.azure-automation.net**

Om du använder DSC-resurser som kommunicerar mellan noder, till exempel [WaitFor*-resurserna,](https://docs.microsoft.com/powershell/scripting/dsc/reference/resources/windows/waitForAllResource)måste du också tillåta trafik mellan noder. Se dokumentationen för varje DSC-resurs för att förstå dessa nätverkskrav.

#### <a name="proxy-support"></a>Stöd för proxy

Proxystöd för DSC-agenten finns i Windows version 1809 och senare. Det här alternativet aktiveras genom `ProxyURL` `ProxyCredential` att ange värden för och i [det metakonfigurationsskript](automation-dsc-onboarding.md#generating-dsc-metaconfigurations) som används för att registrera noder.

>[!NOTE]
>Azure Automation State Configuration ger inte DSC-proxystöd för tidigare versioner av Windows.

För Linux-noder stöder DSC-agenten `http_proxy` proxy och använder variabeln för att fastställa URL:en.

#### <a name="azure-automation-state-configuration-network-ranges-and-namespace"></a>Nätverksområden och namnområde för Konfiguration av Azure Automation-tillstånd

Vi rekommenderar att du använder adresserna nedan när undantag definieras. För IP-adresser kan du hämta [Microsoft Azure Datacenter IP Ranges](https://www.microsoft.com/download/details.aspx?id=41653). Den här filen uppdateras varje vecka och har de för närvarande distribuerade intervallen och eventuella kommande ändringar av IP-intervallen.

Om du har ett Automation-konto som har definierats för en viss region kan du begränsa kommunikationen till det regionala datacentret. I följande tabell visas DNS-posten för varje region:

| **Regionen** | **DNS-post** |
| --- | --- |
| USA, västra centrala | wcus-jobruntimedata-prod-su1.azure-automation.net</br>wcus-agentservice-prod-1.azure-automation.net |
| USA, södra centrala |scus-jobruntimedata-prod-su1.azure-automation.net</br>scus-agentservice-prod-1.azure-automation.net |
| USA, östra    | eus-jobruntimedata-prod-su1.azure-automation.net</br>eus-agentservice-prod-1.azure-automation.net |
| USA, östra 2 |eus2-jobruntimedata-prod-su1.azure-automation.net</br>eus2-agentservice-prod-1.azure-automation.net |
| Kanada, centrala |cc-jobruntimedata-prod-su1.azure-automation.net</br>cc-agentservice-prod-1.azure-automation.net |
| Europa, västra |we-jobruntimedata-prod-su1.azure-automation.net</br>we-agentservice-prod-1.azure-automation.net |
| Europa, norra |ne-jobruntimedata-prod-su1.azure-automation.net</br>ne-agentservice-prod-1.azure-automation.net |
| Sydostasien |sea-jobruntimedata-prod-su1.azure-automation.net</br>sea-agentservice-prod-1.azure-automation.net|
| Indien, centrala |cid-jobruntimedata-prod-su1.azure-automation.net</br>cid-agentservice-prod-1.azure-automation.net |
| Japan, östra |jpe-jobruntimedata-prod-su1.azure-automation.net</br>jpe-agentservice-prod-1.azure-automation.net |
| Sydöstra Australien |ase-jobruntimedata-prod-su1.azure-automation.net</br>ase-agentservice-prod-1.azure-automation.net |
| Storbritannien, södra | uks-jobruntimedata-prod-su1.azure-automation.net</br>uks-agentservice-prod-1.azure-automation.net |
| US Gov, Virginia | usge-jobruntimedata-prod-su1.azure-automation.us<br>usge-agentservice-prod-1.azure-automation.us |

För en lista över region-IP-adresser i stället för regionnamn hämtar du [XML-filen för Azure Datacenter IP-adress](https://www.microsoft.com/download/details.aspx?id=41653) från Microsoft Download Center.

> [!NOTE]
> XML-filen För Azure Datacenter IP-adress visar de IP-adressintervall som används i Microsoft Azure-datacenter. Filen innehåller beräknings-, SQL- och lagringsområden.
>
>En uppdaterad fil publiceras varje vecka. Filen återspeglar de för närvarande distribuerade intervallen och eventuella kommande ändringar av IP-intervallen. Nya intervall som visas i filen används inte i datacenter i minst en vecka. Det är en bra idé att ladda ner den nya XML-filen varje vecka. Uppdatera sedan din webbplats för att korrekt identifiera tjänster som körs i Azure. 

Azure ExpressRoute-användare bör notera att den här filen används för att uppdatera BGP-annonseret (Border Gateway Protocol) för Azure-utrymme under den första veckan i varje månad.

## <a name="next-steps"></a>Nästa steg

- Information om hur du kommer igång med DSC i Azure Automation State Configuration finns [i Komma igång med Azure Automation State Configuration](automation-dsc-getting-started.md).
- Mer information om hur du kontrollerar nas inbyggda noder finns i [Onboarding-datorer för hantering av Azure Automation State Configuration](automation-dsc-onboarding.md).
- Mer information om hur du kompilerar DSC-konfigurationer så att du kan tilldela dem till målnoder finns [i Kompilera konfigurationer i Azure Automation State Configuration](automation-dsc-compile.md).
- Cmdlet-referens för PowerShell finns i [cmdlets för Azure Automation State Configuration](/powershell/module/azurerm.automation/#automation).
- Prisinformation finns i [prissättningen för Azure Automation State Configuration](https://azure.microsoft.com/pricing/details/automation/).
- Information om hur du använder Azure Automation State Configuration i en pipeline för kontinuerlig distribution finns i [Kontinuerlig distribution med Azure Automation State Configuration och Chocolatey](automation-dsc-cd-chocolatey.md).
