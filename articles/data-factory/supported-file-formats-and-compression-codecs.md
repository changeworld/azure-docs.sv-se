---
title: Filformat som stöds i Azure Data Factory
description: I det här avsnittet beskrivs de filformat och komprimeringskoder som stöds av filbaserade kopplingar i Azure Data Factory.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: jingwang
ms.openlocfilehash: 6855eea5939419c9a0a867de4e0621b4d4ae02b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75439576"
---
# <a name="supported-file-formats-and-compression-codecs-in-azure-data-factory"></a>Filformat och komprimeringskoder som stöds i Azure Data Factory

*Den här artikeln gäller följande kopplingar: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), File [System](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [HTTP](connector-http.md)och [SFTP](connector-sftp.md).*

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Du kan använda [kopieringsaktiviteten](copy-activity-overview.md) för att kopiera filer som de är mellan två filbaserade datalager, i vilket fall data kopieras effektivt utan serialisering eller deserialisering. 

Dessutom kan du också tolka eller generera filer med ett visst format. Du kan till exempel utföra följande:

* Kopiera data från en lokal SQL Server-databas och skriv till Azure Data Lake Storage Gen2 i parkettformat.
* Kopiera filer i CSV-format (Text) från ett lokalt filsystem och skriv till Azure Blob-lagring i Avro-format.
* Kopiera zippade filer från ett lokalt filsystem, expandera dem i farten och skriv extraherade filer till Azure Data Lake Storage Gen2.
* Kopiera data i Gzip-formatet med komprimerad text (CSV) från Azure Blob-lagring och skriv det till Azure SQL Database.
* Många fler aktiviteter som kräver serialisering/deserialisering eller komprimering/dekompression.

## <a name="next-steps"></a>Nästa steg

Se de andra artiklarna för kopieringsaktivitet:

- [Kopiera aktivitetsöversikt](copy-activity-overview.md)
- [Kopiera aktivitetsprestanda](copy-activity-performance.md)
