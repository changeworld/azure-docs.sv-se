---
title: Problem med JDBC/ODBC & Apache Thrift-ramverket – Azure HDInsight
description: Det går inte att hämta stora datauppsättningar med JDBC/ODBC och Apache Thrift-programramverk i Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: 23693dcae2f361b88440ec88ca39fd8ed229d85a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894264"
---
# <a name="unable-to-download-large-data-sets-using-jdbcodbc-and-apache-thrift-software-framework-in-hdinsight"></a>Det går inte att hämta stora datauppsättningar med JDBC/ODBC och Apache Thrift software framework i HDInsight

I den här artikeln beskrivs felsökningssteg och möjliga lösningar för problem när du använder Apache Spark-komponenter i Azure HDInsight-kluster.

## <a name="issue"></a>Problem

När du försöker hämta stora datauppsättningar med JDBC/ODBC och Apache Thrift-programvaruramverket i Azure HDInsight visas ett felmeddelande som liknar följande:

```
org.apache.spark.SparkException: Kryo serialization failed:
Buffer overflow. Available: 0, required: 36518. To avoid this, increase spark.kryoserializer.buffer.max value.
```

## <a name="cause"></a>Orsak

Det här undantaget orsakas av serialiseringsprocessen som försöker använda mer buffertutrymme än vad som tillåts. I Spark 2.0.0 `org.apache.spark.serializer.KryoSerializer` används klassen för serialisering av objekt när data nås via Apache Thrift-programvaruramverket. En annan klass används för data som ska skickas över nätverket eller cachelagras i serialiserad form.

## <a name="resolution"></a>Lösning

Öka `Kryoserializer` buffertvärdet. Lägg till `spark.kryoserializer.buffer.max` en nyckel `2048` som heter och `Custom spark2-thrift-sparkconf`ställ in den i spark2 config under .

## <a name="next-steps"></a>Nästa steg

Om du inte såg problemet eller inte kan lösa problemet besöker du någon av följande kanaler för mer support:

* Få svar från Azure-experter via [Azure Community Support](https://azure.microsoft.com/support/community/).

* Anslut [@AzureSupport](https://twitter.com/azuresupport) med – det officiella Microsoft Azure-kontot för att förbättra kundupplevelsen genom att ansluta Azure-communityn till rätt resurser: svar, support och experter.

* Om du behöver mer hjälp kan du skicka en supportbegäran från [Azure-portalen](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj **Stöd** i menyraden eller öppna **supporthubben Hjälp +.** Mer detaljerad information finns i Så här skapar du [en Azure-supportbegäran](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Åtkomst till prenumerationshantering och faktureringssupport ingår i din Microsoft Azure-prenumeration och teknisk support tillhandahålls via en av [Azure-supportplanerna](https://azure.microsoft.com/support/plans/).
