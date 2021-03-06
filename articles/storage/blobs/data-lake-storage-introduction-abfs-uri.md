---
title: Använda Azure Data Lake Storage Gen2 URI
description: Använda Azure Data Lake Storage Gen2 URI
author: normesta
ms.topic: conceptual
ms.author: normesta
ms.date: 12/06/2018
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.reviewer: jamesbak
ms.openlocfilehash: fa0f67e0d72ee5710a42b6de744ddae98e20220a
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437131"
---
# <a name="use-the-azure-data-lake-storage-gen2-uri"></a>Använda Azure Data Lake Storage Gen2 URI

[Hadoop-filsystemdrivrutinen](https://www.aosabook.org/en/hdfs.html) som är kompatibel med Azure Data Lake Storage `abfs` Gen2 är känd av dess schemaidentifierare (Azure Blob File System). I enlighet med andra Hadoop Filesystem drivrutiner använder ABFS-drivrutinen ett URI-format för att adressera filer och kataloger inom ett Data Lake Storage Gen2-kompatibelt konto.

## <a name="uri-syntax"></a>URI-syntax

URI-syntaxen för DataSjölagring Gen2 är beroende av om ditt lagringskonto har konfigurerats för att ha Data Lake Storage Gen2 som standardfilsystem.

Om det datasjölagringsgenm2-konto som du vill ta itu med **inte anges** som standardfilsystem när kontot skapas, är korttids-URI-syntaxen:

<pre>abfs[s]<sup>1</sup>://&lt;file_system&gt;<sup>2</sup>@&lt;account_name&gt;<sup>3</sup>.dfs.core.windows.net/&lt;path&gt;<sup>4</sup>/&lt;file_name&gt;<sup>5</sup></pre>

1. **Schemaidentifierare** `abfs` : Protokollet används som schemaidentifierare. Du har möjlighet att ansluta till eller utan en Anslutningspunkt (Transport Layer Security), som tidigare kallades SSL (Secure Sockets Layer), som tidigare kallades SSL (Secure Sockets Layer). Används `abfss` för att ansluta till en TLS-anslutning.

2. **Filsystem**: Den överordnade platsen som innehåller filerna och mapparna. Detta är samma som behållare i Azure Storage Blobs-tjänsten.

3. **Kontonamn**: Namnet på ditt lagringskonto när det skapas.

4. **Sökvägar**: En framåtsnedstreck`/`avgränsad ( ) representation av katalogstrukturen.

5. **Filnamn**: Namnet på den enskilda filen. Den här parametern är valfri om du adresserar en katalog.

Men om kontot du vill adressera anges som standardfilsystem när kontot skapas, är korttids-URI-syntaxen:

<pre>/&lt;path&gt;<sup>1</sup>/&lt;file_name&gt;<sup>2</sup></pre>

1. **Sökväg**: En framåtsnedstreck avgränsad (`/`) representation av katalogstrukturen.

2. **Filnamn**: Namnet på den enskilda filen.


## <a name="next-steps"></a>Nästa steg

- [Använda Azure Data Lake Storage Gen2 med Azure HDInsight-kluster](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
