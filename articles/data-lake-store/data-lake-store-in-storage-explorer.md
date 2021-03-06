---
title: Hantera datalagringsgend1-resurser – Azure Storage Explorer
description: Lär dig hur du kommer åt och hanterar dina Azure Data Lake Storage Gen1-data och resurser i Azure Storage Explorer
author: jejiang
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 02/05/2018
ms.author: jejiang
ms.openlocfilehash: a5117f72f933e1e48dc471a75624a8d3921f55af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73832263"
---
# <a name="manage-azure-data-lake-storage-gen1-resources-by-using-storage-explorer"></a>Hantera Azure Data Lake Storage Gen1-resurser med hjälp av Storage Explorer

[Azure Data Lake Storage Gen1](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview) är en tjänst för lagring av stora mängder ostrukturerade data, till exempel text eller binära data. Du kan komma åt data var du än är via HTTP eller HTTPS. Med DataSjölagringsgend1 i Azure Storage Explorer kan du komma åt och hantera datasjölagring gen1 data och resurser, tillsammans med andra Azure-entiteter som blobbar och köer. Nu kan du använda samma verktyg för att hantera olika Azure-entiteter på ett och samma ställe.

En annan fördel är att du inte behöver ha prenumerationsbehörighet för att hantera Data Lake Storage Gen1-data. I Storage Explorer kan du ansluta sökvägen DataSjölagring gen1 till den **lokala och bifogade** noden så länge någon ger behörighet.

## <a name="prerequisites"></a>Krav

Du behöver följande för att slutföra stegen i den här artikeln:

* En Azure-prenumeration. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial).
* Ett Data Lake Storage Gen1-konto. Instruktioner om hur du skapar en finns i [Komma igång med Azure Data Lake Storage Gen1](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal).

## <a name="install-storage-explorer"></a>Installera Storage Explorer

Installera den senaste versionen av Azure Storage Explorer på [webbsidan för produkten](https://azure.microsoft.com/features/storage-explorer/). Installationen stöder Windows-, Linux- och Mac-versioner.

## <a name="connect-to-an-azure-subscription"></a>Ansluta till en Azure-prenumeration

1. Välj ikonen för plugin-programmet till vänster i Storage Explorer.

   ![Ikon för plugin-program](./media/data-lake-store-in-storage-explorer/plug-in-icon.png)

1. Välj **Lägg till ett Azure-konto** och välj sedan **Logga in**.

   ![Dialogrutan Anslut till Azure Storage](./media/data-lake-store-in-storage-explorer/connect-to-azure-subscription.png)

1. I dialogrutan **Logga in på ditt konto** anger du dina autentiseringsuppgifter för Azure.

    ![Dialogrutan för inloggning i Azure](./media/data-lake-store-in-storage-explorer/sign-in.png)

1. Välj din prenumeration i listan och välj sedan **Använd**.

    ![Prenumerationsinformation och knappen Använd](./media/data-lake-store-in-storage-explorer/apply-subscription.png)

    **EXPLORER**-fönstret uppdateras och visar konton i den valda prenumerationen.

    ![Kontolista](./media/data-lake-store-in-storage-explorer/account-list.png)

Du har anslutit Data Lake Storage Gen1 till din Azure-prenumeration.

## <a name="connect-to-data-lake-storage-gen1"></a>Ansluta till DataSjölagring Gen1

Du kan komma åt resurser som inte finns i din prenumeration om någon ger dig URI:n till dessa resurser. Du kan sedan ansluta till Data Lake Storage Gen1 med hjälp av URI när du har loggat in.

1. Öppna Storage Explorer.
2. I det vänstra fönstret expanderar du **Lokala och ansluten**.
3. Högerklicka på **Data Lake Store** och välj **Anslut till Data Lake Store**.

      ![Anslut till Data Lake Store på snabbmenyn](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-uri-attach.png)

4. Ange URI:n. Verktyget går till platsen för den URL som du angav.

      ![Dialogrutan Anslut till Data Lake Store med textrutan för att ange URI:n](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-uri-attach-dialog.png)

      ![Resultat av anslutning till Data Lake Storage Gen1](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-attach-finish.png)

## <a name="view-a-data-lake-storage-gen1-accounts-contents"></a>Visa innehållet i ett datasjölagringsgenm1-konto

Resurserna för ett DataSjölagringsgen1-konto innehåller mappar och filer.

Följande steg illustrerar hur du visar innehållet i ett DataSjölagringsgend1-konto i Storage Explorer:

1. Öppna Storage Explorer.
2. Expandera prenumerationen som innehåller det DataSjölagringsgenm1-konto som du vill visa i den vänstra rutan.
3. Expandera **Data Lake Store**.
4. Högerklicka på den datasjölagringsgend1-kontonod som du vill visa och välj sedan **Öppna**. Du kan också dubbelklicka på kontot Datasjölagring gen1 för att öppna det.

   Huvudfönstret visar innehållet i datasjölagringsgenm1.The main pane displays the Data Lake Storage Gen1 account's contents.

   ![Huvudfönstret med en lista över mappar](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-toolbar-mainpane.png)

## <a name="manage-resources-in-data-lake-storage-gen1"></a>Hantera resurser i Gen1 för lagring av datasjö

Du kan hantera Data Lake Storage Gen1-resurser genom att utföra följande åtgärder:

* Bläddra igenom Data Lake Storage Gen1-resurser över flera Data Lake Storage Gen1-konton.  
* Använd en anslutningssträng för att ansluta till och hantera Data Lake Storage Gen1 direkt.
* Visa datasjölagringsgenm1-resurser som delas av andra via en åtkomstkontrollista under **Lokal och Bifogad**.
* Utför CRUD-åtgärder på filer eller mappar: stöd för rekursiva mappar och val av flera filer.
* Dra, släpp och lägg till en mapp så att du snabbt kommer åt nyligen använda platser. Den här åtgärden speglar Utforskaren i skrivbordsmiljö.
* Kopiera och öppna en hyperlänk för datasjölagringgenny1 i Storage Explorer med ett klick.
* Visa aktivitetslogg i det undre högra fönstret för att hålla reda på aktivitetsstatusen.
* Visa mappstatistik och filegenskaper.

## <a name="manage-resources-in-azure-storage-explorer"></a>Hantera resurser i Azure Storage Explorer

När du har skapat ett Data Lake Storage Gen1-konto kan du:

* ladda upp mappar och filer, ladda ned mappar och filer och öppna resurser på din lokala dator
* fästa på **Snabbåtkomst**, skapa en ny mapp, kopiera en URL och markera alla
* kopiera och klistra in, byta namn, ta bort, få mappstatistik och uppdatera.

Följande objekt illustrerar hur du hanterar resurser i ett Data Lake Storage Gen1-konto. Följ stegen för den uppgift som du vill utföra.

### <a name="upload-files"></a>Överföra filer

1. Gå till verktygsfältet i huvudfönstret, välj **Ladda upp** och välj sedan **Ladda upp filer** i den nedrullningsbara menyn.

   ![Menyalternativet Ladda upp filer](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-upload-files-menu.png)

2. I dialogrutan **Välj filer att ladda upp** väljer du de filer som du vill ladda upp.

   ![Dialogrutan för att överföra filer](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-upload-files-dialog.png)

3. Välj **Öppna** för att påbörja uppladdningen.

### <a name="upload-a-folder"></a>Ladda upp en mapp

1. Gå till verktygsfältet i huvudfönstret, välj **Ladda upp** och välj sedan **Ladda upp mapp** i den nedrullningsbara menyn.

   ![Menyalternativet Ladda upp mapp](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-upload-folder-menu.png)

2. I dialogrutan **Välj mapp att ladda upp** väljer du en mapp som du vill ladda upp. Klicka på **Välj mapp**.

   ![Dialogrutan för att ladda upp mappar](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-upload-folder-dialog.png)

   Uppladdningen påbörjas.

   ![Dialogrutan med pågående uppladdning](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-upload-folder-drag.png)

> [!NOTE]
> Du kan starta uppladdningen genom att dra filer och mappar direkt på en lokal dator.

### <a name="download-folders-or-files-to-your-local-computer"></a>Ladda ned mappar eller filer till en lokal dator

1. Markera de mappar eller filer som du vill ladda ned.
2. Gå till verktygsfältet i huvudfönstret och klicka på **Hämta**.
3. Ange en plats och ett namn i dialogrutan **Välj mapp där nedladdade filer ska sparas**.
4. Välj **Spara**.

### <a name="open-a-folder-or-file-from-your-local-computer"></a>Öppna en mapp eller en fil från den lokala datorn

1. Markera den mapp eller fil som du vill öppna.
2. Gå till verktygsfältet i huvudfönstret och välj **Öppna**. Högerklicka på den markerade mappen eller filen och välj **Öppna** på snabbmenyn.

Filen laddas ned och öppnas med det program som är associerat med filens underliggande filtyp. Eller också öppnas mappen i huvudfönstret.

![Öppnad fil](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-open.png)

### <a name="copy-folders-or-files-to-the-clipboard"></a>Kopiera mappar och filer till Urklipp

1. Markera de mappar eller filer som du vill kopiera.
2. Gå till verktygsfältet i huvudfönstret och klicka på **Kopiera**. Högerklicka på de markerade mapparna eller filerna och välj **Kopiera** på snabbmenyn.
3. Bläddra till ett annat Data Lake Storage Gen1-konto i den vänstra rutan och dubbelklicka på det för att visa det i huvudfönstret.
4. Gå till verktygsfältet i huvudfönstret och klicka på **Klistra in** för att skapa en kopia. Eller välj **Klistra in** på snabbmenyn för målet.

![Val för att kopiera en mapp](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-copy-paste.png)

> [!NOTE]
> Kopiera och klistra in mellan lagringstyper stöds inte. Du kan kopiera mappar eller filer för datasjölagringsgenm1 och klistra in dem i ett annat Data Lake Storage Gen1-konto. Men du *kan inte* kopiera mappar eller filer för datasjölagringsgenm1 och klistra in dem i Azure Blob-lagring eller tvärtom.
>
> Kopieringen och inklistringen fungerar genom att mapparna eller filerna laddas ned lokalt och sedan laddas upp till destinationen. Verktyget *utför inte* åtgärden på serversidan. Det är långsamt att kopiera och klistra in stora filer. Optimering av högpresterande kopiering och flytt av filer pågår.

### <a name="delete-folders-or-files"></a>Ta bort mappar eller filer

1. Markera de mappar eller filer som du vill ta bort.
2. Gå till verktygsfältet i huvudfönstret och klicka på **Ta bort**. Högerklicka på de markerade mapparna eller filerna och välj **Ta bort** på snabbmenyn.
3. Välj **Ja** i bekräftelsedialogrutan.

![Val för att ta bort en mapp](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-delete.png)

### <a name="pin-to-quick-access"></a>Fäst i Snabbåtkomst

1. Markera den mapp som du vill fästa.
2. Gå till verktygsfältet i huvudfönstret och välj **Fäst i Snabbåtkomst**.

   Den valda mappen läggs till i noden **Snabbåtkomst** i det vänstra fönstret.

   ![Val för att fästa en mapp i Snabbåtkomst](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-quick-access.png)

När du har fäst en mapp i noden **Snabbåtkomst** kan du enkelt komma åt resurserna.

### <a name="use-deep-links"></a>Använda djuplänkar

Om du har en URL kan du ange den i adressfältet i Utforskaren eller i en webbläsare. Sedan körs Storage Explorer.exe automatiskt för att gå till platsen för URL:en.

![Djuplänk i Utforskaren](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-deep-link.png)

## <a name="next-steps"></a>Nästa steg

* Visa [viktig information och videor för den senaste Storage Explorer-versionen](https://www.storageexplorer.com).
* Lär dig hur du [hanterar Azure Cosmos DB i Azure Storage Explorer](https://docs.microsoft.com/azure/cosmos-db/storage-explorer).
* [Komma igång med Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer).
* [Kom igång med Azure Data Lake Storage Gen1](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview).
* Titta på en [YouTube-video om hur du använder Azure Cosmos DB i Azure Storage Explorer](https://www.youtube.com/watch?v=iNIbg1DLgWo&feature=youtu.be).
