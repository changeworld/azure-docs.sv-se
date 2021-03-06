---
title: Skapa en Azure-datafabrik med azure data factory-användargränssnittet
description: Skapa en datafabrik med en pipeline som en kopierar data från en plats till en annan i Azure Blob Storage.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: quickstart
ms.date: 02/25/2020
ms.author: jingwang
ms.openlocfilehash: 0afb0ddb65a4f27463e2bb5c1b9441d248c34415
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "79240760"
---
# <a name="quickstart-create-a-data-factory-by-using-the-azure-data-factory-ui"></a>Snabbstart: Skapa en datafabrik med hjälp av Azure Data Factory UI

> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Aktuell version](quickstart-create-data-factory-portal.md)

Den här snabbstarten beskriver hur du använder Azure Data Factory-användargränssnittet till att skapa och övervaka en datafabrik. Den pipeline du skapar i den här datafabriken *kopierar* data från en mapp till en annan mapp i Azure Blob Storage. Om du vill se en självstudie som visar hur du *omvandlar* data med Azure Data Factory går du till [Tutorial: Transform data using Spark](tutorial-transform-data-spark-portal.md) (Självstudie: Omvandla data med Spark).

> [!NOTE]
> Om du inte har använt Azure Data Factory tidigare kan du läsa [Introduktion till Azure Data Factory](data-factory-introduction.md). 

[!INCLUDE [data-factory-quickstart-prerequisites](../../includes/data-factory-quickstart-prerequisites.md)] 

### <a name="video"></a>Video 
Om du tittar på den här videon får du hjälp med att förstå Data Factory-användargränssnittet: 
>[!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Visually-build-pipelines-for-Azure-Data-Factory-v2/Player]

## <a name="create-a-data-factory"></a>Skapa en datafabrik

1. Starta webbläsaren **Microsoft Edge** eller **Google Chrome**. Användargränssnittet för Data Factory stöds för närvarande bara i webbläsarna Microsoft Edge och Google Chrome.
1. Gå till [Azure-portalen](https://portal.azure.com). 
1. På Portal-menyn i Azure väljer du **Skapa en resurs**.
   
   ![Välj Skapa en resurs på Azure Portal-menyn](./media/doc-common-process/create-a-resource.png)
1. Välj **Analys**och välj sedan **Data Factory**. 
   
   ![Valet Data Factory i fönstret Nytt](./media/doc-common-process/new-azure-data-factory-menu.png)
1. På sidan **Ny datafabrik** anger du **ADFTutorialDataFactory** som **Namn**. 
 
   Namnet på Azure-datafabriken måste vara *globalt unikt*. Om följande fel visas ändrar du namnet på datafabriken (till exempel ** &lt;ditt&gt;namn ADFTutorialDataFactory)** och försöker skapa igen. Namngivningsregler för Data Factory-artefakter finns i artikeln [Data Factory – namnregler](naming-rules.md).
  
   ![Fel när ett namn inte är tillgängligt](./media/doc-common-process/name-not-available-error.png)
1. Välj den Azure-prenumeration där du vill skapa den nya datafabriken för **Prenumeration**. 
1. För **Resursgrupp** utför du något av följande steg:
     
   - Välj **Använd befintlig** och välj en befintlig resursgrupp från listan. 
   - Välj **Skapa ny**och ange namnet på en resursgrupp.   
         
   Mer information om resursgrupper finns i [Använda resursgrupper till att hantera Azure-resurser](../azure-resource-manager/management/overview.md).  
1. För **Version** väljer du **V2**.
1. För **Plats** väljer du en plats för datafabriken.

   I listan visas endast platser som Data Factory har stöd för och var dina Azure Data Factory-metadata kommer att lagras. Associerade datalager (som Azure Storage och Azure SQL Database) och beräkningar (som Azure HDInsight) som Data Factory använder kan köras i andra regioner.

1. Välj **Skapa**. När skapandet är klart väljer du **Gå till resurs** för att navigera till sidan Data **Factory.** 

1. Välj panelen **Författare och övervakare** för att starta användargränssnittet för Azure Data Factory på en separat flik.
   
   ![Startsidan för datafabriken med panelen Författare och övervakare](./media/doc-common-process/data-factory-home-page.png)
   
   > [!NOTE]
   > Om du ser att webbläsaren har fastnat på "Auktorisera", avmarkera **kryssrutan Blockera cookies från tredje part och webbplatsdata.** Eller se till att den är markerad, skapa ett undantag för **login.microsoftonline.com**och försök sedan öppna appen igen.
   
1. På sidan **Nu sätter vi igång** växlar du till fliken **Författare** i den vänstra panelen. 

    ![Sidan ”Nu sätter vi igång”](./media/doc-common-process/get-started-page-author-button.png)

## <a name="create-a-linked-service"></a>Skapa en länkad tjänst
I den här proceduren skapar du en länkad tjänst för att länka ditt Azure Storage-konto till datafabriken. Den länkade tjänsten har anslutningsinformationen som Data Factory-tjänsten använder vid körning för att ansluta till den.

1. Välj **Anslutningar**och välj sedan knappen **Nytt** i**verktygsfältet** ( Knappen Anslutningar finns längst ned i den vänstra kolumnen under **Fabriksresurser**). 

1. På sidan **New Linked Service** (Ny länkad tjänst) väljer du **Azure Blob Storage** och klickar på **Fortsätt**. 

1. Gör så här på sidan Ny länkad tjänst (Azure Blob Storage): 

   a. Som **Namn** anger du **AzureStorageLinkedService**.

   b. För **lagringskontonamn**väljer du namnet på ditt Azure Storage-konto.

   c. Välj **Testanslutning** och bekräfta att Data Factory-tjänsten kan ansluta till lagringskontot. 

   d. Välj **Skapa** om du vill spara den länkade tjänsten. 

      ![Ny länkad tjänst](./media/quickstart-create-data-factory-portal/linked-service.png)


## <a name="create-datasets"></a>Skapa datauppsättningar
I den här proceduren skapar du två datauppsättningar: **InputDataset** och **OutputDataset**. Dessa datauppsättningar är av typen **AzureBlob**. De refererar till den länkade Azure Storage-tjänst du skapade i föregående avsnitt. 

Datauppsättningen för indata representerar källdata i indatamappen. I definitionen av datauppsättningen för indata anger du blobcontainern (**adftutorial**), mappen (**input**) och filen (**emp.txt**) som innehåller källdata. 

Datauppsättningen för utdata representerar de data som kopieras till målet. I definitionen av datauppsättningen för utdata anger du blobcontainern (**adftutorial**), mappen (**output**) och filen som data ska kopieras till. Varje pipelinekörning har ett unikt ID tilldelat. Du kan komma åt detta ID via systemvariabeln **RunId**. Namnet på utdatafilen utvärderas dynamiskt baserat på pipelinens körnings-ID.   

I de länkade tjänstinställningarna angav du Azure Storage-kontot som innehåller källdata. I inställningarna för källdatauppsättningen anger du exakt var källdata finns (blobcontainer, mapp och fil). I inställningarna för mottagaruppsättningen anger du var du vill kopiera data (blobcontainer, mapp och fil). 
 
1. Välj **+** knappen (plus) och välj sedan **Datauppsättning**.

   ![Meny för att skapa en datauppsättning](./media/quickstart-create-data-factory-portal/new-dataset-menu.png)
1. På sidan **Ny datauppsättning** väljer du **Azure Blob Storage**och väljer sedan **Fortsätt**. 

1. På sidan **Välj format** väljer du formattyp för dina data och väljer sedan **Fortsätt**. I det här fallet väljer du **Binär** när du kopierar filer som det är utan att tolka innehållet.

   ![Välj format](./media/quickstart-create-data-factory-portal/select-format.png)
   
1. Gör följande på sidan **Ange egenskaper:**

    a. Under **Namn**anger du **InputDataset**. 

    b. För **Länkad tjänst** väljer du **AzureStorageLinkedService**.

    c. För **Filsökväg** väljer du knappen **Bläddra**.

    d. I fönstret **Välj en fil eller mapp** bläddrar du till **indatamappen** i **adftutorial-behållaren,** markerar filen **emp.txt** och väljer sedan **OK**.
    
    e. Välj **OK**.   

    ![Ange egenskaper för InputDataset](./media/quickstart-create-data-factory-portal/set-properties-for-inputdataset.png)
1. Upprepa stegen för att skapa datauppsättningen för utdata:  

    a. Välj **+** knappen (plus) och välj sedan **Datauppsättning**.

    b. På sidan **Ny datauppsättning** väljer du **Azure Blob Storage**och väljer sedan **Fortsätt**.

    c. På sidan **Välj format** väljer du formattyp för dina data och väljer sedan **Fortsätt**.

    d. På sidan **Ange egenskaper** anger du **OutputDataset** för namnet. Välj **AzureStorageLinkedService** som länkad tjänst.

    e. Ange **adftutorial/output**under **Sökväg**. Om **utdatamappen** inte finns skapar kopieringsaktiviteten den vid körning.

    f. Välj **OK**.   

    ![Ange egenskaper för OutputDataset](./media/quickstart-create-data-factory-portal/set-properties-for-outputdataset.png)
## <a name="create-a-pipeline"></a>Skapa en pipeline 
I den här proceduren skapar och verifierar du en pipeline med en kopieringsaktivitet som använder uppsättningar för indata och utdata. Kopieringsaktiviteten kopierar data från filen som anges i inställningarna för datauppsättningen för indata till filen som anges i inställningarna för datauppsättningen för utdata. Om datauppsättningen för indata endast anger en mapp (inte filnamnet) kopierar kopieringsaktiviteten alla filer i källmappen till målet. 

1. Välj **+** knappen (plus) och välj sedan **Pipeline**. 

1. In fliken **Allmänt** anger du **CopyPipeline** som **Name**. 

1. Gå till verktygsfältet **Aktiviteter** och expandera **Flytta och transformera**. Dra aktiviteten **Kopiera data** från **verktygslådan Aktiviteter** till pipelinedesignerytan. Du kan också söka efter aktiviteter i verktygslådan **Aktiviteter**. Ange **CopyFromBlobToBlob** som **Namn**.
   ![Skapa en kopieringsdataaktivitet](./media/quickstart-create-data-factory-portal/copy-activity.png)

1. Växla till fliken **Källa** i inställningarna för kopieringsaktiviteten och välj **InputDataset** som **Källdatauppsättning**.

1. Växla till fliken **Mottagare** i inställningarna för kopieringsaktiviteten och välj **OutputDataset** som **Datauppsättning för mottagare**.

1. Verifiera pipelineinställningarna genom att klicka på **Verifiera** i verktygsfältet för pipelinen. Bekräfta att pipelinen har verifierats. Om du vill stänga **>>** valideringsutdata väljer du knappen (högerpil). 
   ![Validera en pipeline](./media/quickstart-create-data-factory-portal/pipeline-validate.png)

## <a name="debug-the-pipeline"></a>Felsöka pipeline
I det här steget felsöker du pipelinen innan du distribuerar den till Data Factory. 

1. Klicka på **Felsök** i Pipeline-verktygsfältet över arbetsytan för att starta en testkörning. 
    
1. Bekräfta att du ser status för pipelinekörningen på fliken **Utdata** i pipelineinställningarna längst ner. 
 
    ![Utdata för pipelinekörning](./media/quickstart-create-data-factory-portal/pipeline-output.png)

1. Bekräfta att du ser en utdatafil i **outputfolder** för containern **adftutorial**. Om utdatamappen inte finns skapar tjänsten Data Factory den automatiskt. 

## <a name="trigger-the-pipeline-manually"></a>Utlös pipelinen manuellt
I den här proceduren distribuerar du entiteter (länkade tjänster, datauppsättningar, pipeliner) till Azure Data Factory. Sedan utlöser du en pipelinekörning manuellt. 

1. Innan du utlöser en pipeline måste du publicera entiteter i Data Factory. Om du vill publicera väljer du **Publicera alla** högst upp. 
    ![Publicera alla](./media/quickstart-create-data-factory-portal/publish-all.png)

1. Om du vill utlösa pipelinen manuellt väljer du **Lägg till utlösare** i verktygsfältet för pipeline och väljer sedan **Utlösare nu**. Välj **Slutför**på sidan **Pipeline-körning** .

## <a name="monitor-the-pipeline"></a>Övervaka pipeline

1. Växla till fliken **Övervaka** till vänster. Du kan uppdatera listan med knappen **Uppdatera**.

   ![Flik för övervakning av pipelinekörningar](./media/quickstart-create-data-factory-portal/monitor-trigger-now-pipeline.png)
1. Välj länken **CopyPipeline** visas status för kopieringsaktiviteten som körs på den här sidan. 

1. Om du vill visa information om kopieringen väljer du länken **Detaljer** (glasögonbild). Mer information om egenskaperna finns i [Copy Activity overview](copy-activity-overview.md) (Översikt över kopieringsaktivitet). 

   ![Detaljerad information om kopieringsåtgärden](./media/quickstart-create-data-factory-portal/copy-operation-details.png)
1. Bekräfta att du ser en ny fil i **utdatamappen**. 
1. Du kan växla tillbaka till vyn **Pipeline-körningar** från vyn **Aktivitetskörning** genom att välja länken **Alla pipelinekörningar.** 

## <a name="trigger-the-pipeline-on-a-schedule"></a>Utlös pipelinen enligt ett schema
Den här proceduren är valfri i den här självstudien. Du kan skapa en *schemautlösare* för att schemalägga pipelinen så att den körs regelbundet (varje timme, varje dag och så vidare). I den här proceduren skapar du en utlösare som ska köras varje minut tills det slutdatum och den sluttid du anger. 

1. Växla till fliken **Författare**. 

1. Gå till pipelinen, välj **Lägg till utlösare** i verktygsfältet för pipeline och välj sedan **Ny/Redigera**. 

1. På sidan **Add Triggers** (Lägg till utlösare) väljer du **Choose trigger** (Välj utlösare) och sedan **Ny**. 

1. På sidan **Ny utlösare** under **Avsluta**väljer du **På datum**, anger en sluttid några minuter efter den aktuella tiden och väljer sedan **OK**. 

   Den tillkommer en kostnad för varje pipelinekörning, så ange sluttiden bara några minuter efter starttiden. Kontrollera att det är samma dag. Se dock till att det finns tillräckligt med tid för pipelinen att köras mellan publiceringstiden och sluttiden. Utlösaren träder endast i kraft när du har publicerat lösningen till Data Factory, och inte när du sparar utlösaren i användargränssnittet. 

1. Markera kryssrutan **Aktiverad** på sidan **Ny utlösare** och välj sedan **OK**. 

   ![Ny utlösarinställning](./media/quickstart-create-data-factory-portal/trigger-settings-next.png)
1. Granska varningsmeddelandet och välj **OK**.

1. Välj **Publicera alla** om du vill publicera ändringar i Data Factory. 

1. Växla till fliken **Övervaka** till vänster. Om du vill uppdatera listan väljer du **Refresh** (Uppdatera). Du ser att pipelinen körs varje minut från publiceringstiden till sluttiden. 

   Lägg märke till värdena i kolumnen **UTLÖST AV.** Den manuella körningen av utlösaren var från steget (**Trigger Now**) (Utlös nu) du gjorde tidigare. 

1. Växla till **vyn Utlösare.** 

1. Bekräfta att en utdatafil har skapats för varje pipelinekörning fram till det angivet slutdatum och angiven sluttid i **utdatamappen**. 

## <a name="next-steps"></a>Nästa steg
Pipelinen i det här exemplet kopierar data från en plats till en annan i Azure Blob Storage. Gå igenom [självstudiekurserna](tutorial-copy-data-portal.md) om du vill lära dig hur du använder Data Factory i fler scenarier. 
