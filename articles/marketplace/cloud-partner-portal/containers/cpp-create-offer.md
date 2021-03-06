---
title: Skapa ett Azure Containers-erbjudande | Azure Marketplace
description: Så här publicerar du ett nytt behållarerbjudande för Marketplace..
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: dsindona
ms.openlocfilehash: 25d678ad7b8c2b786239b294de873bc1d98fa5cb
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81270340"
---
# <a name="create-a-new-container-offer-with-the-cloud-partner-portal"></a>Skapa ett nytt behållarerbjudande med Cloud Partner Portal

> [!IMPORTANT]
> Från och med den 13 april 2020 börjar vi flytta hanteringen av dina Azure Container-erbjudanden till Partner Center. Efter migreringen skapar och hanterar du dina erbjudanden i Partner center. Följ instruktionerna i [Skapa ett Azure Container-erbjudande](https://aka.ms/CreateContainerOffer) för att hantera dina migrerade erbjudanden.

I den här artikeln beskrivs hur du skapar och publicerar en containererbjudandepost för Azure Marketplace. Varje erbjudande visas som sin egen enhet på Azure Marketplace och är associerat med en eller flera SKU:er.  Ett containererbjudande består av följande grupperingar av tillgångar och stödtjänster:

|  **Tillgångsgrupp**   |  **Beskrivning**  |
|  ---------------   |  ---------------  |
|    SKU: er            |  Den minsta deployable enheten i ett erbjudande. Ett enda erbjudande (produktklass) kan ha flera SKU:er kopplade till erbjudandet. Du kan använda SKU:er för att skilja mellan funktioner som stöds och faktureringsmodeller. |
|  Marketplace       | Innehåller marknadsförings-, legal- och lead management-tillgångar och specifikationer.  <ul><li> Marknadsföringstillgångar inkluderar erbjudandenamn, beskrivning och logotyper</li> <li> Juridiska tillgångar inkluderar en sekretesspolicy, användarvillkor och annan juridisk dokumentation</li>  <li> Med leadhanteringsprincipen kan du ange hur leads ska hanteras från slutanvändarens portal på Azure Marketplace.</li> </ul> |
| Support            | Innehåller supportkontakt- och policyinformation |


## <a name="new-offer-form"></a>Formulär för nytt erbjudande 

Logga in på [Cloud Partner Portal](https://cloudpartner.azure.com/)och välj sedan + Nytt **erbjudande** i den vänstra menyraden. På menyn Nytt erbjudande väljer du **Behållare** för att visa formuläret **Nytt erbjudande** och starta processen med att definiera tillgångar för ett nytt behållarerbjudande.

![Välj behållaralternativ för nytt erbjudande](./media/azure-container-offer.png)

## <a name="next-steps"></a>Nästa steg

På sidan **Nytt erbjudande** för behållarerbjudandet finns en uppsättning flikar och formulärfält som du ska använda för att skapa ett nytt erbjudande. Var och en av följande artiklar förklarar hur du använder fliken för att definiera tillgångsgrupper och stödtjänster för ditt nya behållarerbjudande.

- [Fliken Erbjudandeinställningar](./cpp-offer-settings-tab.md)
- [Fliken SKU:er](./cpp-skus-tab.md)
- [Fliken Marketplace](./cpp-marketplace-tab.md)
- [Fliken Stöd](./cpp-support-tab.md)
