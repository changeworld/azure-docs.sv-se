---
title: Team Data Science Process för dataforskare
description: Vägledning om en uppsättning mål som vanligtvis används för att implementera omfattande datavetenskapslösningar med Azure-teknik med hjälp av Team Data Science Process och Azure Machine Learning.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 2c0f07f11fc3b87d08f8f7f87c1b2342a824beec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75976065"
---
# <a name="team-data-science-process-for-data-scientists"></a>Team Data Science Process för dataforskare

Den här artikeln innehåller vägledning för en uppsättning mål som vanligtvis används för att implementera omfattande datavetenskapslösningar med Azure-teknik. Du guidas genom:

- förstå en analysarbetsbelastning
- använda teamdatavetenskapsprocessen
- använda Azure Machine Learning
- grunderna för dataöverföring och lagring
- tillhandahålla dokumentation för datakälla
- använda verktyg för analysbearbetning

Dessa utbildningsmaterial är relaterade till Team Data Science Process (TDSP) och Microsoft och programvara och verktygslådor med öppen källkod, som är användbara för att föreställa sig, köra och leverera datavetenskapslösningar.

## <a name="lesson-path"></a>Lektionsväg
Du kan använda objekten i följande tabell för att styra din egen självstudie. Läs kolumnen *Beskrivning* för att följa sökvägen, klicka på *ämneslänkarna* för studiereferenser och kontrollera dina kunskaper med hjälp av kolumnen *Kunskapskontroll.*

| **Mål**                                                                             | **Hjälpavsnitt**                                                                                                                                            | **Beskrivning**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               | **Kontroll av kunskaper**                                                                                                                                                                                                                                                                                                                                                                                                                                             |
|-------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Förstå processerna för att utveckla analytiska projekt                                 | [En introduktion till teamdatavetenskapsprocessen](overview.md)       | Vi börjar med att täcka en översikt över Team Data Science Process - TDSP. Den här processen guidar dig genom varje steg i ett analysprojekt. Läs igenom vart och ett av dessa avsnitt om du vill veta mer om processen och hur du kan implementera den.                                                                                                                                                                                                                                                                                                                                                                                        | Granska och [hämta artefakterna för TDSP-projektstrukturen till din lokala dator](https://github.com/Azure/Azure-TDSP-ProjectTemplate) för projektet.                                                                                                                                                                                                                                                                                                         |
|                                                                                           | [Agil utveckling](https://www.visualstudio.com/agile/)                                                                                             | Team Data Science Process fungerar bra med många olika programmeringsmetoder. I denna utbildningsväg använder vi agila mjukvaruutveckling. Läs igenom "Vad är agile utveckling?" och "Building Agile Culture" artiklar, som täcker grunderna i att arbeta med Agile. Det finns också andra referenser på denna webbplats där du kan läsa mer.                                                                                                                                                                                                                                                                               | Förklara kontinuerlig integration och kontinuerlig leverans till en kollega.                                                                                                                                                                                                                                                                                                                                                                                          |
|                                                                                           | [DevOps för datavetenskap](https://mva.microsoft.com/training-courses/devops-an-it-pro-guide-8286?l=GVFXzCXy_8104984382)                        | Developer Operations (DevOps) involverar personer, processer och plattformar som du kan använda för att arbeta igenom ett projekt och integrera din lösning i en organisations standard-IT. Denna integration är nödvändig för adoption, säkerhet och säkerhet. I den här onlinekursen får du lära dig mer om DevOps-metoder samt förstå några av de verktygskedja alternativ du har.                                                                                                                                                                                                                                                                               | Förbered en 30-minuters presentation för en teknisk publik om hur DevOps är viktigt för analysprojekt.                                                                                                                                                                                                                                                                                                                                                     |
| Förstå teknikerna för datalagring och databehandling                               | [Microsoft Business Analytics och AI](https://www.microsoft.com/cloud-platform/what-is-cortana-intelligence)                                   | Vi fokuserar på några tekniker i den här utbildningsvägen som du kan använda för att skapa en analyslösning, men Microsoft har många fler. För att förstå vilka alternativ du har är det viktigt att granska de plattformar och funktioner som är tillgängliga i Microsoft Azure, Azure Stack och lokala alternativ. Granska den här resursen om du vill lära dig de olika verktyg som du har tillgängliga för att besvara analysfrågan.                                                                                                                                                                                                                                   | [Ladda ner och granska presentationsmaterial från denna workshop](https://info.microsoft.com/CO-Azure-CNTNT-FY16-Oct2015-Cortana-Registration.html).                                                                                                                                                                                                                                                                                                          |
| Konfigurera och konfigurera tränings-, utvecklings- och produktionsmiljöer               | [Microsoft Azure](https://azure.microsoft.com/training/learning-paths/azure-solution-architect/)                                               | Nu ska vi skapa ett konto i Microsoft Azure för utbildning och lära sig att skapa utvecklings- och testmiljöer. Dessa kostnadsfria utbildningsresurser kommer igång. Fyll i "Nybörjare" och "Intermediate" vägar.                                                                                                                                                                                                                                                                                                                                                                                                                 | [Om du inte har ett Azure-konto skapar du ett](https://azure.microsoft.com/free/?v=17.39&WT.srch=1&WT.mc_id=AID559320_SEM_2kAfgmyQ&lnkd=Bing_Azure_Brand). Logga in på Microsoft Azure-portalen och [skapa en resursgrupp](../../azure-resource-manager/management/manage-resource-groups-portal.md) för utbildning.                                                                                                                     |
|                                                                                           | [MICROSOFT Azure Command-Line Interface (CLI)](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest)      | Det finns flera sätt att arbeta med Microsoft Azure – från grafiska verktyg som VSCode och Visual Studio till webbgränssnitt som Azure-portalen och från kommandoraden, till exempel Azure PowerShell-kommandon och funktioner. I den här artikeln täcker vi COMMAND-Line Interface (CLI), som du kan använda lokalt på din arbetsstation, i Windows och andra operativsystem samt i Azure-portalen.                                                                                                                                                                                                                   | [Ange din standardprenumeration med Azure CLI](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest).                                                                                                                                                                                                                                                                                                      |
|                                                                                           | [Microsoft Azure Storage](../../storage/common/storage-introduction.md)                                                | Du behöver en plats för att lagra dina data. I den här artikeln får du lära dig mer om Microsoft Azures lagringsalternativ, hur du skapar ett lagringskonto och hur du kopierar eller flyttar data till molnet. Läs igenom den här introduktionen för att lära dig mer.                                                                                                                                                                                                                                                                                                                                                                                                        | [Skapa ett lagringskonto i din resursgrupp för utbildning, skapa en behållare för ett Blob-objekt och ladda upp och hämta data.](../../storage/blobs/storage-quickstart-blobs-cli.md)                                                                                                                                                                                                                                              |
|                                                                                           | [Microsoft Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis)                                  | Microsoft Azure Active Directory (AAD) ligger till grund för att skydda ditt program. I den här artikeln får du lära dig mer om konton, rättigheter och behörigheter. Active Directory och säkerhet är komplexa ämnen, så läs bara igenom den här resursen för att förstå grunderna.                                                                                                                                                                                                                                                                                                                                                  | [Lägg till en användare i Azure Active Directory](../../active-directory/fundamentals/add-users-azure-active-directory.md). Du kanske inte har behörighet för den här åtgärden om du inte är administratör för prenumerationen. Om så är fallet, [helt enkelt granska den här guiden för att lära dig mer](../../active-directory/fundamentals/add-users-azure-active-directory.md).                                                        |
|                                                                                           | [Virtuella datorn för Microsoft Azure Data Science](../data-science-virtual-machine/overview.md)    | Du kan installera verktygen för att arbeta med Data Science lokalt på flera operativsystem. Men Microsoft Azure Data Science Virtual Machine (DSVM) innehåller alla verktyg du behöver och massor av projektexempel att arbeta med. I den här artikeln lär du dig mer om DVSM och hur du arbetar igenom dess exempel. Den här resursen förklarar den virtuella datorn för datavetenskap, hur du kan skapa en och några alternativ för att utveckla kod med den. Den innehåller också all programvara du behöver för att slutföra denna utbildningsväg - så se till att du slutför kunskapsvägen för det här avsnittet. | [Skapa en virtuell dator för datavetenskap och arbeta igenom minst ett labb](../data-science-virtual-machine/provision-vm.md).                                                                                                                                                                                                                                                                                   |
| Installera och förstå verktyg och tekniker för att arbeta med Data Science-lösningar
| [Arbeta med git](https://mva.microsoft.com/training-courses/github-for-windows-users-16749?l=KTNeW39wC_6006218965)                           | För att följa vår DevOps-process med TDSP måste vi ha ett versionskontrollsystem. Microsoft Azure Machine Learning använder git, ett populärt distribuerat databassystem med öppen källkod. I den här artikeln får du lära dig mer om hur du installerar, konfigurerar och arbetar med git och en central databas – GitHub.                                                                                                                                                                                                                                                                                                                      | [Klona det här GitHub-projektet för projektstrukturen för utbildningsväg](https://github.com/Azure/Azure-TDSP-ProjectTemplate).                                                                                                                                                                                                                                                                                                                                      |
|                                                                                           | [VSCode](https://code.visualstudio.com/docs/getstarted/introvideos)                                                                                  | VSCode är en plattformsoberoende integrerad utvecklingsmiljö (IDE) som du kan använda med flera språk och Azure-verktyg. Du kan använda den här enskilda miljön för att skapa hela din lösning. Titta på dessa inledande videor för att komma igång.                                                                                                                                                                                                                                                                                                                                                                                             | Installera VSCode och [arbeta igenom VS-kodfunktionerna på den interaktiva redigeraren Playground](https://code.visualstudio.com/docs/introvideos/basics).                                                                                                                                                                                                                                                                                                            |
|                                                                                           | [Programmering med Python](https://docs.python.org/3/tutorial/index.html)                                                                             | I den här lösningen använder vi Python, ett av de mest populära språken inom datavetenskap. Den här artikeln beskriver grunderna i att skriva analytisk kod med Python och resurser för att lära dig mer. Arbeta igenom avsnitt 1-9 i denna referens, kontrollera sedan dina kunskaper.                                                                                                                                                                                                                                                                                                                                                                            | [Lägg till en entitet i en Azure-tabell med Python](../../cosmos-db/table-storage-how-to-use-python.md).                                                                                                                                                                                                                                                                                                                               |
|                                                                                           | [Arbeta med anteckningsböcker](https://jupyter-notebook.readthedocs.io/en/latest/notebook.html#introduction)                                               | Anteckningsböcker är ett sätt att introducera text och kod i samma dokument. Azure Machine Learning fungerar med anteckningsböcker, så det är fördelaktigt att förstå hur du använder dem. Läs igenom den här guiden och ge det ett försök i avsnittet Kunskapskontroll.                                                                                                                                                                                                                                                                                                                                                                | [Öppna den här sidan](https://try.jupyter.org/)och klicka på länken "Välkommen till Python.ipynb". Gå igenom exemplen på den sidan.                                                                                                                                                                                                                                                                                                                            |
|                                                                                           | [Maskininlärning](https://mva.microsoft.com/training-courses/data-science-and-machine-learning-essentials-14100?l=UyhoTxWdB_3505050723)       
| Att skapa avancerade analytiska lösningar innebär att arbeta med data, med hjälp av Machine Learning, som också ligger till grund för att arbeta med artificiell intelligens och djupinlärning. Den här kursen lär dig mer om maskininlärning. [För en omfattande kurs om datavetenskap, kolla in denna certifiering](https://academy.microsoft.com/professional-program/tracks/data-science/).                                                                                                                                                                                                                                                | Hitta en resurs för maskininlärningsalgoritmer. (Tips: Sök på "azure machine learning algorithm cheat sheet")                                                                                                                                                                                                                                                                                                                                              |
|                                                                                           | [scikit-lära](https://scikit-learn.org/stable/tutorial/basic/tutorial.html)                                                                          | Med scikit-learn-verktygen kan du utföra datavetenskapsuppgifter i Python. Vi använder denna ram i vår lösning. Den här artikeln beskriver grunderna och förklarar var du kan läsa mer.                                                                                                                                                                                                                                                                                                                                                                                                                                              | Med hjälp av Iris-datauppsättningen kvarstår en SVM-modell med Pickle.                                                                                                                                                                                                                                                                                                                                                                                                      |
|                                                                                           | [Arbeta med Docker](https://docs.microsoft.com/dotnet/standard/microservices-architecture/container-docker-introduction/docker-defined)      | Docker är en distribuerad plattform som används för att skapa, leverera och köra program och används ofta i Azure Machine Learning. Den här artikeln beskriver grunderna i denna teknik och förklarar var du kan gå för att lära dig mer.                                                                                                                                                                                                                                                                                                                                                                                                           | Öppna Visual Studio-kod och [installera Docker-tillägget](https://code.visualstudio.com/Docs/languages/dockerfile). [Skapa en enkel Node Docker-behållare](https://blogs.msdn.microsoft.com/vscode/2015/08/11/getting-started-with-docker/).                                                                                                                                                                                                                 |
|                                                                                           | [HDInsight](../../hdinsight/hdinsight-hadoop-introduction.md)                                                          | HDInsight är Hadoops infrastruktur med öppen källkod, tillgänglig som en tjänst i Microsoft Azure. Dina Machine Learning-algoritmer kan omfatta stora uppsättningar data, och HDInsight har möjlighet att lagra, överföra och bearbeta data i stor skala. Den här artikeln handlar om att arbeta med HDInsight.                                                                                                                                                                                                                                                                                                                                                   | [Skapa ett litet HDInsight-kluster](../../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md). Använd HiveQL-satser [för att projicera kolumner på en /example/data/sample.log-fil](../../hdinsight/hdinsight-use-hive.md). Alternativt [kan du slutföra denna kunskapskontroll på ditt lokala system.](../../hdinsight/hdinsight-hadoop-emulator-get-started.md) |
| Skapa ett databehandlingsflöde från affärskrav                                  | [Fastställande av frågan, efter TDSP](https://buckwoody.wordpress.com/2017/08/31/the-keys-to-effective-data-science-projects-the-question/) | Med utvecklingsmiljön installerad och konfigurerad, och förståelsen för de tekniker och processer på plats, är det dags att sätta ihop allt med hjälp av TDSP för att utföra en analys. Vi måste börja med att definiera frågan, välja datakällor och resten av stegen i Team Data Science Process. Tänk på DevOps-processen när vi arbetar igenom den här processen. I den här artikeln får du lära dig hur du tar kraven från din organisation och skapar en dataflödeskarta genom ditt program för att definiera din lösning med hjälp av Team Data Science Process.
| Hitta en resurs på "[De 5 datavetenskapsfrågorna](../studio/data-science-for-beginners-the-5-questions-data-science-answers.md)" och beskriv en fråga som din organisation kan ha inom dessa områden. Vilka algoritmer ska du fokusera på för den frågan?                                                                                                                                            |
| Använd Azure Machine Learning för att skapa en förutsägande lösning                       | [Azure Machine Learning](../overview-what-is-azure-ml.md)                         | Microsoft Azure Machine Learning använder AI för datakäbbel och funktionsteknik, hanterar experiment och spårar modellkörningar. Allt detta fungerar i en enda miljö och de flesta funktioner kan köras lokalt eller i Azure. Du kan använda PyTorch, TensorFlow och andra ramverk för att skapa dina experiment. I den här artikeln fokuserar vi på ett komplett exempel på den här processen, med allt du har lärt dig hittills.                                                                                                                                                   |                                                                                                                                                                                                                                             |
| Använda Power BI för att visualisera resultat                                                         | [Power BI](https://powerbi.microsoft.com/guided-learning/)                                                                                     | Power BI är Microsofts datavisualiseringsverktyg. Den är tillgänglig på flera plattformar från webben till mobila enheter och stationära datorer. I den här artikeln får du lära dig hur du arbetar med utdata från den lösning du har skapat genom att komma åt resultaten från Azure-lagring och skapa visualiseringar med Power BI.                                                                                                                                                                                                                                                                                                                             | [Slutför den här självstudien om Power BI.](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/) Anslut sedan Power BI till Blob CSV som skapats i en experimentkörning.                                                                                                                                                                                                                                                                       |
| Övervaka din lösning                                                                     | [Application Insights](../../azure-monitor/app/app-insights-overview.md)                                            | Det finns flera verktyg som du kan använda för att övervaka din slutlösning. Azure Application Insights gör det enkelt att integrera inbyggd övervakning i din lösning.                                                                                                                                                                                                                                                                                                                                                                                                                                                                              | [Konfigurera Programstatistik för att övervaka ett program](https://cmatskas.com/visual-studio-code-integration-with-azure-application-insights/).                                                                                                                                                                                                                                                                                                                  |
|                                                                                           | [Azure Monitor-loggar](../../log-analytics/log-analytics-overview.md)                                                   | En annan metod för att övervaka ditt program är att integrera det i din DevOps-process. Azure Monitor-loggsystemet innehåller en omfattande uppsättning funktioner som hjälper dig att titta på dina analytiska lösningar när du har distribuerat dem.                                                                                                                                                                                                                                                                                                                                                                                                                       | [Slutför den här självstudien](https://docs.loganalytics.io/docs/Learn/Getting-Started/Getting-started-with-the-Analytics-portal) om hur du använder Azure Monitor-loggar.                                                                                                                                                                                                                                                                                                       |
| Slutför den här utbildningsvägen                                                               |                                                                                                                           | Grattis! Du har slutfört den här utbildningsvägen.                                                                                                                                                                                                                                                                                                                                            |

## <a name="next-steps"></a>Nästa steg
[Team Data Science Process för utvecklaroperationer](team-data-science-process-for-devops.md) I den här artikeln beskrivs utvecklarfunktionerna (DevOps) som är specifika för en implementering av avancerad analys och kognitiva tjänster.
