---
title: Skapa ett mappningsdataflöde
description: Skapa ett dataflöde för Azure Data Factory-mappning
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/12/2019
ms.openlocfilehash: 2eb455ba6fa40538bfa03018be47232066036c23
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74930431"
---
# <a name="create-azure-data-factory-data-flow"></a>Skapa Azure Data Factory-dataflöde



Mappning av dataflöden i ADF är ett sätt att omvandla data i stor skala utan att någon kodning krävs. Du kan utforma ett dataomvandlingsjobb i dataflödesdesignern genom att skapa en serie omvandlingar. Börja med valfritt antal källomvandlingar följt av dataomvandlingssteg. Slutför sedan dataflödet med diskbänk för att landa dina resultat i en destination.

Kom igång genom att först skapa en ny V2 Data Factory från Azure-portalen. När du har skapat din nya fabrik klickar du på panelen "Författare & Monitor" för att starta datafabrikens användargränssnitt.

![Alternativ för dataflöde](media/data-flow/v2portal.png "dataflödet skapar")

När du är i datafabrikens användargränssnitt kan du använda exempeldataflöden. Exemplen är tillgängliga från ADF-mallgalleriet. Skapa "Pipeline från mall" i ADF och välj kategorin Dataflöde i mallgalleriet.

![Alternativ för dataflöde](media/data-flow/template.png "dataflödet skapar")

Du uppmanas att ange din Azure Blob Storage-kontoinformation.

![Alternativ för dataflöde](media/data-flow/template2.png "dataflödet skapa 2")

[De uppgifter som används för dessa prover finns här](https://github.com/kromerm/adfdataflowdocs/tree/master/sampledata). Hämta exempeldata och lagra filerna i dina Azure Blob-lagringskonton så att du kan köra exemplen.

## <a name="create-new-data-flow"></a>Skapa nytt dataflöde

Använd knappen Skapa resurs "plus sign" i ADF-användargränssnittet för att skapa dataflöden.

![Alternativ för dataflöde](media/data-flow/newresource.png "Ny resurs")

## <a name="next-steps"></a>Nästa steg

Börja bygga dataomvandlingen med en [källomvandling](data-flow-source.md).
