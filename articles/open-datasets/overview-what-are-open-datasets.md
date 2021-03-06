---
title: Vad är öppna datauppsättningar? Kurerade offentliga datauppsättningar
titleSuffix: Azure Open Datasets
description: Lär dig mer om Azure Open Datasets, kurerade datauppsättningar från det offentliga rummet som väder, folkräkning, helgdagar och plats för att berika förutsägande lösningar.
ms.service: open-datasets
ms.topic: overview
author: cjgronlund
ms.author: cgronlun
ms.date: 11/04/2019
ms.openlocfilehash: fd5697f9c325dc4ad866c333ce1b20e008ebfa24
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "73606161"
---
# <a name="what-are-azure-open-datasets-and-how-can-you-use-them"></a>Vad är Azure Open Dataset och hur kan du använda dem?

[Azure Open Datasets](https://azure.microsoft.com/services/open-datasets/) är kurerade offentliga datauppsättningar som du kan använda för att lägga till scenariospecifika funktioner i maskininlärningslösningar för mer exakta modeller. Öppna datauppsättningar finns i molnet på Microsoft Azure och är integrerade i Azure Machine Learning och är lätt tillgängliga för Azure Databricks och Machine Learning Studio (klassisk). Du kan också komma åt datauppsättningarna via API:er och använda dem i andra produkter, till exempel Power BI och Azure Data Factory.

Datauppsättningar innehåller offentliga data för väder, folkräkning, helgdagar, allmän säkerhet och plats som hjälper dig att träna maskininlärningsmodeller och berika prediktiva lösningar. Du kan också dela dina offentliga datauppsättningar på Azure Open Datasets. 

![Azure Open Datasets-komponenter](./media/overview-what-are-open-datasets/open-datasets-components.png)

## <a name="curated-prepared-datasets"></a>Kurerade, förberedda datauppsättningar
Kurerade öppna offentliga datauppsättningar i Azure Open Datasets är optimerade för förbrukning i machine learning-arbetsflöden. 

Dataforskare ägnar ofta större delen av sin tid åt att rensa och förbereda data för avancerad analys. Öppna datauppsättningar kopieras till Azure-molnet och förbearbetas för att spara tid. Med jämna mellanrum hämtas data från källorna, till exempel genom en FTP-anslutning till National Oceanic and Atmospheric Administration (NOAA). Därefter tolkas data i ett strukturerat format och berikas sedan på lämpligt sätt med funktioner som postnummer eller plats för närmaste väderstation.

Datauppsättningar är cohosted med moln beräkning i Azure gör åtkomst och manipulering enklare.  

Följande är exempel på tillgängliga datauppsättningar. 

### <a name="weather-data"></a>Väderdata
 
|Datauppsättning         | Notebook-filer     | Beskrivning                                    |
|----------------|---------------|------------------------------------------------|
|[NOAA-integrerade ytdata (ISD)](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/) | [Azure Notebooks](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/?tab=data-access#AzureNotebooks) <br> [Azure Databricks](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/?tab=data-access#AzureDatabricks) | Över hela världen väderdata från NOAA med den bästa rumsliga täckningen i Nordamerika, Europa, Australien och delar av Asien. Uppdateras dagligen. |
|[NOAA Globalt prognossystem (GFS)](https://azure.microsoft.com/services/open-datasets/catalog/noaa-global-forecast-system/) | [Azure Notebooks](https://azure.microsoft.com/services/open-datasets/catalog/noaa-global-forecast-system/?tab=data-access#AzureNotebooks) <br> [Azure Databricks](https://azure.microsoft.com/services/open-datasets/catalog/noaa-global-forecast-system/?tab=data-access#AzureDatabricks) | 15-dagars amerikanska väderprognosdata per timme från NOAA. Uppdateras dagligen. |

### <a name="calendar-data"></a>Kalenderdata

|Datauppsättning         | Notebook-filer     | Beskrivning                                    |
|----------------|---------------|------------------------------------------------|
|[Helgdagar](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/) | [Azure Notebooks](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/?tab=data-access#AzureNotebooks) <br> [Azure Databricks](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/?tab=data-access#AzureDatabricks) | Uppgifter om allmän helgdag över hela världen, som omfattar 41 länder eller regioner från 1970 till 2099. Inkluderar land och om de flesta människor har betalat ledighet. |

## <a name="access-to-datasets"></a>Tillgång till datauppsättningar  
Med ett Azure-konto kan du komma åt öppna datauppsättningar med hjälp av kod eller via Azure-tjänstgränssnittet. Data samlokaliseras med Azure-molnberäkningsresurser för användning i din machine learning-lösning.  

Öppna datauppsättningar är tillgängliga via Azure Machine Learning UI och SDK. Open Datasets tillhandahåller också Azure-anteckningsböcker och Azure Databricks-anteckningsböcker som du kan använda för att ansluta data till Azure Machine Learning och Azure Databricks. Datauppsättningar kan också nås via en Python SDK. 

Du behöver dock inte ett Azure-konto för att komma åt öppna datauppsättningar. du kan komma åt dem från alla Python-miljöer med eller utan Spark.

## <a name="request-or-contribute-datasets"></a>Begära eller bidra med datauppsättningar

Om du inte hittar de data du vill ha skickar du ett e-postmeddelande till oss för att [begära en datauppsättning](mailto:aod@microsoft.com?Subject=Request%20dataset%3A%20%3Creplace%20with%20dataset%20name%3E&Body=%0AYour%20name%20and%20institution%3A%20%0A%0ADataset%20name%3A%0A%20%0ADataset%20description%3A%20%0A%3Cfill%20in%20a%20brief%20description%20and%20share%20any%20web%20links%20of%20the%20dataset%3E%20%0A%0ADataset%20size%3A%20%0A%3Chow%20much%20space%20does%20the%20dataset%20need%20today%20and%20how%20much%20is%20it%20expected%20to%20grow%20each%20year%3E%20%0A%0ADataset%20file%20formats%3A%20%0A%3Ccurrent%20dataset%20file%20formats%2C%20and%20optionally%2C%20any%20formats%20that%20the%20dataset%20must%20be%20transformed%20to%20for%20easy%20access%3E%0A%0ALicense%3A%20%0A%3Cwhat%20is%20the%20license%20or%20terms%20and%20conditions%20governing%20the%20distribution%20of%20this%20dataset%3E%0A%0AUse%20cases%3A%20%0A%3CExplain%20some%20common%20use%20of%20the%20dataset.%20E.g.%20weather%20dataset%20can%20be%20useful%20in%20demand%20forecasting%20and%20predictive%20maintenance%20scenarios%3E%20%0A%0AAny%20additional%20information%20you%20want%20us%20to%20know%3A%0A) eller [bidra med en datauppsättning](mailto:aod@microsoft.com?Subject=Contribute%20dataset%3A%20%3Creplace%20with%20dataset%20name%3E&Body=%0AYour%20name%20and%20institution%3A%20%0A%0ADataset%20name%3A%0A%20%0ADataset%20description%3A%20%0A%3Cfill%20in%20a%20brief%20description%20and%20share%20any%20web%20links%20of%20the%20dataset%3E%20%0A%0ADataset%20size%3A%20%0A%3Chow%20much%20space%20does%20the%20dataset%20need%20today%20and%20how%20much%20is%20it%20expected%20to%20grow%20each%20year%3E%20%0A%0ADataset%20file%20formats%3A%20%0A%3Ccurrent%20dataset%20file%20formats%2C%20and%20optionally%2C%20any%20formats%20that%20the%20dataset%20must%20be%20transformed%20to%20for%20easy%20access%3E%0A%0ALicense%3A%20%0A%3Cwhat%20is%20the%20license%20or%20terms%20and%20conditions%20governing%20the%20distribution%20of%20this%20dataset%3E%0A%0AUse%20cases%3A%20%0A%3CExplain%20some%20common%20use%20of%20the%20dataset.%20E.g.%20weather%20dataset%20can%20be%20useful%20in%20demand%20forecasting%20and%20predictive%20maintenance%20scenarios%3E%20%0A%0AAny%20additional%20information%20you%20want%20us%20to%20know%3A%0A). 

## <a name="next-steps"></a>Nästa steg
* [Exempel på anteckningsbok](samples.md)
* [Handledning: Regression modellering med NY taxidata](/azure/machine-learning/service/tutorial-auto-train-models?context=azure/open-datasets/context/open-datasets-context)
* [Python SDK för öppna datauppsättningar](/python/api/azureml-opendatasets/?view=azure-ml-py)
