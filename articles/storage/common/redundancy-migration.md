---
title: Ändra hur ett lagringskonto replikeras
titleSuffix: Azure Storage
description: Lär dig hur du ändrar hur data i ett befintligt lagringskonto replikeras.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/10/2020
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: 5c37dbdc34138faab8adae6ad18252c18a75cad4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80337079"
---
# <a name="change-how-a-storage-account-is-replicated"></a>Ändra hur ett lagringskonto replikeras

Azure Storage lagrar alltid flera kopior av dina data så att de skyddas från planerade och oplanerade händelser, inklusive tillfälliga maskinvarufel, nätverks- eller strömavbrott och omfattande naturkatastrofer. Redundans säkerställer att ditt lagringskonto uppfyller [servicenivåavtalet (SLA) för Azure Storage](https://azure.microsoft.com/support/legal/sla/storage/) även med tanke på fel.

Azure Storage erbjuder följande typer av replikering:

- Lokalt redundant lagring (LRS)
- Zonredundant lagring (ZRS)
- Geo-redundant lagring (GRS) eller geo redundant lagring med läsåtkomst (RA-GRS)
- Geo-zon-redundant lagring (GZRS) eller läsåtkomst geo-zon-redundant lagring (RA-GZRS) (förhandsvisning)

En översikt över vart och ett av dessa alternativ finns i [Azure Storage redundans](storage-redundancy.md).

## <a name="switch-between-types-of-replication"></a>Växla mellan typer av replikering

Du kan växla ett lagringskonto från en typ av replikering till någon annan typ, men vissa scenarier är enklare än andra. Om du vill lägga till eller ta bort geo-replikering eller läsåtkomst till den sekundära regionen kan du använda Azure-portalen, PowerShell eller Azure CLI för att uppdatera replikeringsinställningen. Men om du vill ändra hur data replikeras i den primära regionen, genom att flytta från LRS till ZRS eller vice versa, måste du utföra en manuell migrering.

I följande tabell finns en översikt över hur du växlar från varje typ av replikering till en annan:

| Växling | ... till LRS | ... till GRS/RA-GRS | ... till ZRS | ... till GZRS/RA-GZRS |
|--------------------|----------------------------------------------------|---------------------------------------------------------------------|----------------------------------------------------|---------------------------------------------------------------------|
| <b>... från LRS</b> | Ej tillämpligt | Använda Azure portal, PowerShell eller CLI för att ändra replikeringsinställningen<sup>1</sup> | Utföra en manuell migrering <br /><br />Begär en direktmigrering | Utföra en manuell migrering <br /><br /> ELLER <br /><br /> Byt till GRS/RA-GRS först och begär sedan en direktmigrering<sup>1</sup> |
| <b>... från GRS/RA-GRS</b> | Använda Azure portal, PowerShell eller CLI för att ändra replikeringsinställningen | Ej tillämpligt | Utföra en manuell migrering <br /><br /> ELLER <br /><br /> Växla till LRS först och begär sedan en direktmigrering | Utföra en manuell migrering <br /><br /> Begär en direktmigrering |
| <b>... från ZRS</b> | Utföra en manuell migrering | Utföra en manuell migrering | Ej tillämpligt | Använda Azure portal, PowerShell eller CLI för att ändra replikeringsinställningen<sup>1</sup> |
| <b>... från GZRS/RA-GZRS</b> | Utföra en manuell migrering | Utföra en manuell migrering | Använda Azure portal, PowerShell eller CLI för att ändra replikeringsinställningen | Ej tillämpligt |

<sup>1</sup> Medför en engångsavgift för utgående utgång.

> [!CAUTION]
> Om du har utfört en [kontoundanssväxling](https://docs.microsoft.com/azure/storage/common/storage-disaster-recovery-guidance) för ditt (RA-)GRS- eller (RA-)GZRS-konto är det konfigurerat för att vara lokalt redundant i den nya primära regionen. Direktmigrering till ZRS eller GZRS för sådana LRS-konton stöds inte. Du måste utföra [manuell migrering](https://docs.microsoft.com/azure/storage/common/redundancy-migration#perform-a-manual-migration-to-zrs).

## <a name="change-the-replication-setting"></a>Ändra replikeringsinställningen

Du kan använda Azure-portalen, PowerShell eller Azure CLI för att ändra replikeringsinställningen för ett lagringskonto, så länge du inte ändrar hur data replikeras i den primära regionen. Om du migrerar från LRS i den primära regionen till ZRS i den primära regionen eller vice versa, måste du utföra antingen en [manuell migrering](#perform-a-manual-migration-to-zrs) eller en [livemigrering](#request-a-live-migration-to-zrs).

Om du ändrar hur ditt lagringskonto replikeras kan du inte ändra tid för dina program.

# <a name="portal"></a>[Portal](#tab/portal)

Så här ändrar du redundansalternativet för ditt lagringskonto i Azure-portalen:

1. Navigera till ditt lagringskonto i Azure-portalen.
1. Välj **konfigurationsinställningen.**
1. Uppdatera **replikeringsinställningen.**

![Skärmbild som visar hur du ändrar replikeringsalternativ i portalen](media/redundancy-migration/change-replication-option.png)

# <a name="powershell"></a>[Powershell](#tab/powershell)

Om du vill ändra redundansalternativet för ditt lagringskonto med PowerShell `-SkuName` anropar du kommandot [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) och anger parametern:

```powershell
Set-AzStorageAccount -ResourceGroupName <resource_group> `
    -AccountName <storage_account> `
    -SkuName <sku>
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill ändra redundansalternativet för ditt lagringskonto med `--sku` Azure CLI anropar du kommandot az storage account [update](/cli/azure/storage/account#az-storage-account-update) och anger parametern:

```azurecli-interactive
az storage account update \
    --name <storage-account>
    --resource-group <resource_group> \
    --sku <sku>
```

---

## <a name="perform-a-manual-migration-to-zrs"></a>Utföra en manuell migrering till ZRS

Om du vill ändra hur data i ditt lagringskonto replikeras i den primära regionen, genom att flytta från LRS till ZRS eller vice versa, kan du välja att utföra en manuell migrering. Manuell migrering ger större flexibilitet än direktmigrering. Du styr tidpunkten för en manuell migrering, så använd det här alternativet om du behöver migreringen för att slutföras vid ett visst datum.

När du utför en manuell migrering från LRS till ZRS i den primära regionen eller vice versa kan mållagringskontot vara geouppsagt och kan också konfigureras för läsåtkomst till den sekundära regionen. Du kan till exempel migrera ett LRS-konto till ett GZRS- eller RA-GZRS-konto i ett steg.

En manuell migrering kan resultera i stilleståndstider för program. Om ditt program kräver hög tillgänglighet tillhandahåller Microsoft även ett alternativ för direktmigrering. Direktmigrering är migrering på plats utan driftavbrott.

Med en manuell migrering kopierar du data från ditt befintliga lagringskonto till ett nytt lagringskonto som använder ZRS i den primära regionen. Om du vill utföra en manuell migrering kan du använda något av följande alternativ:

- Kopiera data med hjälp av ett befintligt verktyg som AzCopy, ett av Azure Storage-klientbiblioteken eller ett tillförlitligt verktyg från tredje part.
- Om du är bekant med Hadoop eller HDInsight kan du koppla både källlagringskontot och mållagringskontot till klustret. Sedan parallellisera datakopieringsprocessen med ett verktyg som DistCp.

## <a name="request-a-live-migration-to-zrs"></a>Begär en direktmigrering till ZRS

Om du behöver migrera ditt lagringskonto från LRS eller GRS till ZRS i den primära regionen utan programstopp kan du begära en direktmigrering från Microsoft. Under en livemigrering kan du komma åt data i ditt lagringskonto och utan förlust av hållbarhet eller tillgänglighet. Azure Storage SLA underhålls under migreringsprocessen. Det finns ingen dataförlust som är associerad med en livemigrering. Tjänstslutpunkter, åtkomstnycklar, signaturer för delad åtkomst och andra kontoalternativ förblir oförändrade efter migreringen.

ZRS stöder endast v2-konton för allmänt ändamål, så se till att uppgradera ditt lagringskonto innan du skickar en begäran om en livemigrering till ZRS. Mer information finns i [Uppgradera till ett allmänt v2-lagringskonto](storage-account-upgrade.md). Ett lagringskonto måste innehålla data som ska migreras via livemigrering.

Livemigrering stöds endast för lagringskonton som använder LRS- eller GRS-replikering. Om ditt konto använder RA-GRS måste du först ändra kontots replikeringstyp till antingen LRS eller GRS innan du fortsätter. Det här mellanliggande steget tar bort den sekundära skrivskyddade slutpunkten som tillhandahålls av RA-GRS före migreringen.

Microsoft hanterar din begäran om direktmigrering utan dröjsmål, men det finns inte någon garanti om när den slutförs. Om dina data måste migreras till ZRS före ett visst datum rekommenderar Microsoft att du utför en manuell migrering i stället. Ju mer data du har på ditt konto, desto längre tid tar det vanligtvis att migrera dina data.

Du måste utföra en manuell migrering om:

- Du vill migrera dina data till ett ZRS-lagringskonto som finns i en annan region än källkontot.
- Ditt lagringskonto är ett premium-sidblob eller blockblolob-konto.
- Du vill migrera data från ZRS till LRS, GRS eller RA-GRS.
- Ditt lagringskonto innehåller data på arkivnivån.

Du kan begära direktmigrering via [Azure Support-portalen](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview). Välj det lagringskonto som du vill konvertera till ZRS på portalen.

1. Välj **ny supportbegäran**
2. Fyll i **grunderna** baserat på din kontoinformation. I avsnittet **Tjänst** väljer du **Hantering av lagringskonto** och den resurs som du vill konvertera till ZRS.
3. Välj **Nästa**.
4. Ange följande värden i avsnittet **Problem:**
    - **Allvarlighetsgrad**: Lämna standardvärdet som det är.
    - **Problemtyp:** Välj **datamigrering**.
    - **Kategori**: Välj **Migrera till ZRS**.
    - **Titel**: Skriv en beskrivande rubrik, till exempel **ZRS-kontomigrering**.
    - **Detaljer:** Skriv ytterligare information i rutan **Detaljer,** till exempel skulle jag vilja migrera till ZRS \_ \_ från [LRS, GRS] i regionen.
5. Välj **Nästa**.
6. Kontrollera att kontaktinformationen är korrekt på **bladet för kontaktinformation.**
7. Välj **Skapa**.

En support person kommer att kontakta dig och ge all hjälp du behöver.

> [!NOTE]
> Livemigrering stöds för närvarande inte för premiumfilresurser. Endast manuellt kopiera eller flytta data stöds för närvarande.
>
> GZRS-lagringskonton stöder för närvarande inte arkivnivån. Mer information finns i [Azure Blob-lagring: åtkomstnivåer för frekvent, lågfrekvent och arkiv.](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers)
>
> Hanterade diskar är endast tillgängliga för LRS och kan inte migreras till ZRS. Du kan lagra ögonblicksbilder och avbildningar för vanliga SSD-hanterade diskar på standard hdd-lagring och [välja mellan LRS- och ZRS-alternativ](https://azure.microsoft.com/pricing/details/managed-disks/). Information om integrering med tillgänglighetsuppsättningar finns i [Introduktion till Azure-hanterade diskar](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview#integration-with-availability-sets).

## <a name="switch-from-zrs-classic"></a>Växla från ZRS Classic

> [!IMPORTANT]
> Microsoft kommer att förringa och migrera ZRS Classic-konton den 31 mars 2021. Mer information kommer att ges till ZRS Classic kunder före utfasning.
>
> När ZRS blir allmänt tillgängligt i en viss region kan kunderna inte längre skapa ZRS Classic-konton från Azure-portalen i den regionen. Att använda Microsoft PowerShell och Azure CLI för att skapa ZRS Classic-konton är ett alternativ tills ZRS Classic är inaktuell. Information om var ZRS är tillgängligt finns i [Azure Storage redundans](storage-redundancy.md).

ZRS Classic replikerar asynkront data över datacenter inom en till två regioner. Replikerade data kanske inte är tillgängliga om inte Microsoft initierar redundans till den sekundära. Ett ZRS Classic-konto kan inte konverteras till eller från LRS, GRS eller RA-GRS. ZRS Classic-konton stöder inte heller mått eller loggning.

ZRS Classic är endast tillgängligt för **blockblobar** i virtuella V1-lagringskonton (General Purpose. Mer information om lagringskonton finns i [Översikt över Azure storage-konto](storage-account-overview.md).

Om du vill migrera ZRS-kontodata manuellt till eller från ett LRS-, GRS-, RA-GRS- eller ZRS Classic-konto använder du något av följande verktyg: AzCopy, Azure Storage Explorer, PowerShell eller Azure CLI. Du kan också skapa en egen migreringslösning med ett av Azure Storage-klientbiblioteken.

Du kan också uppgradera ditt ZRS Classic-lagringskonto till ZRS med hjälp av Azure-portalen, PowerShell eller Azure CLI i regioner där ZRS är tillgängligt.

# <a name="portal"></a>[Portal](#tab/portal)

Om du vill uppgradera till ZRS i Azure-portalen navigerar du till **konfigurationsinställningarna** för kontot och väljer **Uppgradera:**

![Uppgradera ZRS Classic till ZRS i portalen](media/redundancy-migration/portal-zrs-classic-upgrade.png)

# <a name="powershell"></a>[Powershell](#tab/powershell)

Om du vill uppgradera till ZRS med PowerShell anropar du följande kommando:

```powershell
Set-AzStorageAccount -ResourceGroupName <resource_group> -AccountName <storage_account> -UpgradeToStorageV2
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill uppgradera till ZRS med Azure CLI anropar du följande kommando:

```cli
az storage account update -g <resource_group> -n <storage_account> --set kind=StorageV2
```

---

## <a name="costs-associated-with-changing-how-data-is-replicated"></a>Kostnader i samband med att ändra hur data replikeras

Kostnaderna för att ändra hur data replikeras beror på konverteringssökvägen. Beställa från minst till de dyraste Azure Storage redundans erbjudanden inkluderar LRS, ZRS, GRS, RA-GRS, GZRS och RA-GZRS.

Om du till exempel går *från* LRS till någon annan typ av replikering medför ytterligare avgifter eftersom du flyttar till en mer sofistikerad redundansnivå. Migrera *till* GRS eller RA-GRS medför en utgående bandbreddsavgift eftersom dina data (i din primära region) replikeras till din sekundära fjärrregion. Den här avgiften är en engångskostnad vid den första installationen. När data har kopierats tillkommer inga ytterligare migreringsavgifter. Mer information om bandbreddsavgifter finns på [sidan Azure Storage Pricing](https://azure.microsoft.com/pricing/details/storage/blobs/).

Om du migrerar ditt lagringskonto från GRS till LRS finns det ingen extra kostnad, men dina replikerade data tas bort från den sekundära platsen.

> [!IMPORTANT]
> Om du migrerar ditt lagringskonto från RA-GRS till GRS eller LRS faktureras det kontot som RA-GRS i ytterligare 30 dagar efter det datum då det konverterades.

## <a name="see-also"></a>Se även

- [Redundans för Azure Storage](storage-redundancy.md)
- [Kontrollera egenskapen Senaste synkroniseringstid för ett lagringskonto](last-sync-time-get.md)
- [Designa program med hög tillgänglighet med hjälp av geo redundant lagring med läsåtkomst](storage-designing-ha-apps-with-ragrs.md)
