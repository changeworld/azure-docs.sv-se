---
title: Konfigurera webbappsanalyser för ASP.NET med Application Insights | Microsoft Docs
description: Konfigurera prestanda- och tillgänglighets- och användarbeteendeanalysverktyg för din ASP.NET webbplats, som finns lokalt eller i Azure.
ms.topic: conceptual
ms.date: 05/08/2019
ms.openlocfilehash: 0843d6c04bf6fc9bab07207072990fb3fb8f1844
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77665926"
---
# <a name="set-up-application-insights-for-your-aspnet-website"></a>Konfigurera Application Insights för din ASP.NET-webbplats

Den här proceduren konfigurerar din ASP.NET-webbapp för att skicka telemetri till tjänsten [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md). Den fungerar för ASP.NET-appar som antingen ligger på din egen IIS-server lokalt eller i molnet. Du får tillgång till diagram och ett kraftfullt frågespråket som hjälper dig att förstå hur din app presterar och hur människor använder den, plus automatiska aviseringar om fel eller prestandaproblem. Många utvecklare tycker att de här funktionerna är mycket bra som de är, men du kan också utöka och anpassa telemetrin om du behöver.

Installationen kräver bara några klick i Visual Studio. Du har möjlighet att undvika kostnader genom att begränsa mängden telemetri. Med den här funktionen kan du experimentera och felsöka eller övervaka en webbplats med inte många användare. Om du sedan vill gå vidare och övervaka din produktionsplats, är det lätt att höja gränsen vid ett senare tillfälle.

## <a name="prerequisites"></a>Krav
När du ska lägga till Application Insights för din ASP.NET-webbplats måste du göra följande:

- Installera [Visual Studio 2019 för Windows](https://www.visualstudio.com/downloads/) med följande arbetsbelastningar:
    - ASP.NET och webbutveckling (Avmarkera inte de valfria komponenterna)
    - Azure Development

Om du inte har en Azure-prenumeration skapar du ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="step-1-add-the-application-insights-sdk"></a><a name="ide"></a> Steg 1: Lägg till Application Insights SDK

> [!IMPORTANT]
> Skärmbilderna i det här exemplet är baserade på Visual Studio 2017 version 15.9.9 och senare. Erfarenheten av att lägga till Application Insights varierar mellan olika versioner av Visual Studio samt efter ASP.NET malltyp. Äldre versioner kan ha alternativ text som "Konfigurera programstatistik".

Högerklicka på webbappnamnet i Lösningsutforskaren och välj **Lägg till** > **programstatistik Telemetri**

![Skärmbild av Solution Explorer med Configure Application Insights markerat](./media/asp-net/add-telemetry-new.png)

(Beroende på Application Insights SDK-version kan du uppmanas att uppgradera till den senaste versionen av SDK:t. Om det händer väljer du **Uppdatera SDK**.)

![Skärmbild: En ny version av Microsoft Application Insights SDK är tillgänglig. Update SDK är markerat](./media/asp-net/0002-update-sdk.png)

Konfigurationsskärmen för Application Insights:

Välj **Kom igång**.

![Skärmbild av sidan Register your app with Application Insights (Registrera din app med Application Insights)](./media/asp-net/00004-start-free.png)

Om du vill ange resursgruppen eller den plats där dina data är lagrade klickar du på **Konfigurera inställningar**. Resursgrupper används för att styra dataåtkomst. Om du exempelvis har flera appar som ingår i samma system kan du placera deras Application Insights-data i samma resursgrupp.

 Välj **Registrera**.

![Skärmbild av sidan Register your app with Application Insights (Registrera din app med Application Insights)](./media/asp-net/00005-register-ed.png)

 Välj **Project** > **Manage NuGet Paket** > **paket källa: nuget.org** > Bekräfta att du har den senaste stabila versionen av Application Insights SDK.

 Telemetri kommer att skickas till [Azure Portal](https://portal.azure.com), både under felsökningen och när du har publicerat din app.
> [!NOTE]
> Om du inte vill skicka telemetri till portalen medan du felsöker, lägger du bara till Application Insights SDK i din app men konfigurerar inte en resurs i portalen. Du kan se telemetrin i Visual Studio medan du felsöker. Du kan senare återvända till den här konfigurationssidan eller vänta tills du har distribuerat appen och [aktivera telemetri vid körtid](../../azure-monitor/app/monitor-performance-live-website-now.md).

## <a name="step-2-run-your-app"></a><a name="run"></a> Steg 2: Kör appen
Kör din app med F5. Öppna olika sidor för att skapa viss telemetri.

Du ser hur många händelser som har loggats i Visual Studio.

![Skärmbild av Visual Studio. Knappen Application Insights visas under felsökning.](./media/asp-net/00006-Events.png)

## <a name="step-3-see-your-telemetry"></a>Steg 3: Visa din telemetri
Du kan se din telemetri antingen i Visual Studio eller i Application Insights-webbportalen. Sök telemetri i Visual Studio för att felsöka din app. Övervaka prestanda och användning i webbportalen när systemet är aktivt. 

### <a name="see-your-telemetry-in-visual-studio"></a>Visa din telemetri i Visual Studio

Visa Application Insights-data i Visual Studio.  Välj Anslutna tjänster för **lösningsutforskare** > **Connected Services** > högerklicka på **Application Insights**och klicka sedan på **Sök live-telemetri**.

I Visual Studio Application Insights-sökfönstret visas data från programmet för telemetri som genereras på serversidan för appen. Experimentera med filtren och klicka på en händelse om du vill visa mer information.

![Skärmbild av data från felsökningssessionsvyn i fönstret Application Insights.](./media/asp-net/55.png)

> [!Tip]
> Om du inte ser några data kontrollerar du att tidsintervallet är korrekt och klickar på sökikonen.

[Lär dig mer om Application Insights-verktygen i Visual Studio](../../azure-monitor/app/visual-studio.md).

<a name="monitor"></a>
### <a name="see-telemetry-in-web-portal"></a>Visa telemetri i webbportalen

Du kan även visa telemetrin i Application Insights-webbportalen (såvida du inte väljer att installera enbart SDK:n). Portalen innehåller fler diagram, analysverktyg och komponentöverskridande vyer än Visual Studio. Portalen visar även aviseringar.

Öppna Application Insights-resursen. Logga antingen in på [Azure-portalen](https://portal.azure.com/) och sök efter den där, eller välj **Solution Explorer** > **Anslutna tjänster** > högerklicka på **Application Insights** > **Open Application Insights Portal** (Öppna Application Insights-portalen) för att komma dit.

Portalen öppnas i en vy över telemetrin från appen.

![Skärmbild av Application Insights-översiktssidan](./media/asp-net/007.png)

Klicka på valfri ikon eller valfritt diagram i portalen för att visa mer information.

## <a name="step-4-publish-your-app"></a>Steg 4: Publicera appen
Publicera din app på din IIS-server eller i Azure. Bevaka [Live Metrics Stream](../../azure-monitor/app/metrics-explorer.md#live-metrics-stream) och se om allt fungerar som det ska.

Din telemetri byggs upp i application insights-portalen, där du kan övervaka mätvärden, genom att söka i din telemetri. Du kan också använda det kraftfulla [Kusto-frågespråket](/azure/kusto/query/) för att analysera användning och prestanda, eller för att hitta specifika händelser.

Du kan också fortsätta att analysera din telemetri i [Visual Studio](../../azure-monitor/app/visual-studio.md) med verktyg som diagnossökning och [trender](../../azure-monitor/app/visual-studio-trends.md).

> [!NOTE]
> Om din app skickar så mycket telemetri att det närmar sig [begränsningsgränserna](../../azure-monitor/app/pricing.md#limits-summary) aktiveras [sampling](../../azure-monitor/app/sampling.md) automatiskt. Sampling minskar mängden telemetri som skickas från din app, samtidigt som korrelerade informationen bevaras i diagnossyfte.
>
>

## <a name="youre-all-set"></a><a name="land"></a>Du är redo

Grattis! Du har installerat Application Insights-paketet i din app och konfigurerat det för att skicka telemetri till Application Insights-tjänsten på Azure.

Azure-resursen som tar emot din apps telemetri identifieras med en *instrumenteringsnyckel*. Du hittar den här nyckeln i filen ApplicationInsights.config.config.


## <a name="upgrade-to-future-sdk-versions"></a>Uppgradera till framtida SDK-versioner
Om du vill uppgradera till en [ny SDK-version](https://github.com/Microsoft/ApplicationInsights-dotnet-server/releases) öppnar du **NuGet-pakethanteraren** och filtrerar på installerade paket. Välj **Microsoft.ApplicationInsights.Web**och välj **Uppgradera**.

Om du har gjort anpassningar i ApplicationInsights.config sparar du en kopia av den innan du uppgraderar. Sammanfogar sedan dina ändringar i den nya versionen.

## <a name="video"></a>Video

* Extern steg-för-steg-video om [att konfigurera Application Insights med ett .NET-program från grunden](https://www.youtube.com/watch?v=blnGAVgMAfA).

## <a name="next-steps"></a>Nästa steg

Det finns andra artiklar som du kan läsa om du är intresserad av följande:

* [Instrumentering av en webbapp under körning](../../azure-monitor/app/monitor-performance-live-website-now.md)
* [Azure Cloud Services](../../azure-monitor/app/cloudservices.md)

### <a name="more-telemetry"></a>Mer telemetri

* **[Webbläsare och webbsidesinläsning](../../azure-monitor/app/javascript.md)** – Infoga ett kodfragment på dina webbsidor.
* **[Få mer detaljerad beroende- och undantagsövervakning](../../azure-monitor/app/monitor-performance-live-website-now.md)** – Installera Status Monitor på servern.
* **[Koda anpassade händelser](../../azure-monitor/app/api-custom-events-metrics.md)** för att räkna, tid eller mäta användaråtgärder.
* **[Hämta loggdata](../../azure-monitor/app/asp-net-trace-logs.md)** – Korrelera loggdata med din telemetri.

### <a name="analysis"></a>Analys

* **[Arbeta med Application Insights i Visual Studio](../../azure-monitor/app/visual-studio.md)**<br/>Innehåller information om att felsöka med telemetri, köra diagnostiksökningar och gå igenom koden.
* **[Analytics](../../azure-monitor/log-query/get-started-portal.md)** – Kraftfullt frågespråk.

### <a name="alerts"></a>Aviseringar

* [Tillgänglighetstester](../../azure-monitor/app/monitor-web-app-availability.md): Skapa tester som kan användas för att kontrollera att webbplatsen visas på webben.
* [Smart diagnostik](../../azure-monitor/app/proactive-diagnostics.md): De här testerna körs automatiskt, så du behöver inte göra något för att konfigurera dem. De berättar om din app har ett ovanligt antal misslyckade begäranden.
* [Måttaviseringar](../../azure-monitor/app/alerts.md): Ställ in aviseringar för att varna dig om ett mått överskrider ett tröskelvärde. Du kan ställa in dem för anpassade mätningar som du kodar i din app.

### <a name="automation"></a>Automation

* [Automatisera skapandet av en Application Insights-resurs](../../azure-monitor/app/powershell.md)
