---
title: ta med fil
description: ta med fil
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/05/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: e90e750bf248bdcc8e50c6ddc6e9fa0273660195
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79136026"
---
Azure Blob Storage är Microsofts objektlagringslösning för molnet. Blobblagring är optimerat för att lagra stora mängder ostrukturerade data. Ostrukturerade data är data som inte följer en viss datamodell eller definition, till exempel text eller binära data.

## <a name="about-blob-storage"></a>Om blobblagring

Blobblagring är utformat för att:

* Leverera bilder eller dokument direkt till en webbläsare.
* Lagra filer för distribuerad åtkomst.
* Direktuppspelning av video och ljud.
* Skriva till loggfiler.
* Lagra data för säkerhetskopiering och återställning, haveriberedskap och arkivering.
* Lagra data för analys av en tjänst som kan vara lokal eller Azure-baserad.

Användare eller klientprogram kan komma åt objekt i blobblagring via HTTP/HTTPS från var som helst i världen. Objekt i blobblagring nås via [Azure Storage REST API](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api), [Azure PowerShell](https://docs.microsoft.com/powershell/module/azure.storage), [Azure CLI](https://docs.microsoft.com/cli/azure/storage) eller ett Azure Storage-klientbibliotek. Klientbibliotek är tillgängliga för olika språk, inklusive:

* [.NET](/dotnet/api/overview/azure/storage?view=azure-dotnet)
* [Java](https://docs.microsoft.com/java/api/overview/azure/storage)
* [Node.js](https://azure.github.io/azure-storage-node) 
* [Python](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-python)
* [Kör](https://github.com/azure/azure-storage-blob-go/)
* [Php](https://azure.github.io/azure-storage-php/)
* [Ruby](https://azure.github.io/azure-storage-ruby)

## <a name="about-azure-data-lake-storage-gen2"></a>Om Azure Data Lake Storage Gen2

Blobblagring stöder Azure Data Lake Storage Gen2, Microsofts lösning för stordataanalys för företag i molnet. Azure Data Lake Storage Gen2 erbjuder ett hierarkiskt filsystem samt fördelarna med Blob-lagring, inklusive:

* Billig lagringsnivå på nivå
* Hög tillgänglighet
* Stark konsekvens
* Funktioner för katastrofåterställning

Mer information om Data Lake Storage Gen2 finns i [Introduktion till Azure Data Lake Storage Gen2](../articles/storage/data-lake-storage/introduction.md).
