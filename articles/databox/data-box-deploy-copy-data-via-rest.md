---
title: 'Självstudiekurs: Använd REST-API:er för att kopiera till Blob-lagring'
titleSuffix: Azure Data Box
description: Lär dig hur du kopierar data till en Azure Data Box Blob-lagring via REST-API:er
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 05/09/2019
ms.author: alkohli
ms.openlocfilehash: 7642c009a5bcd1d00efb432975fff5a65c7ba340
ms.sourcegitcommit: fe6c9a35e75da8a0ec8cea979f9dec81ce308c0e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "80297201"
---
# <a name="tutorial-copy-data-to-azure-data-box-blob-storage-via-rest-apis"></a>Självstudiekurs: Kopiera data till Azure Data Box Blob-lagring via REST-API:er  

Den här självstudien beskriver procedurer för att ansluta till Azure Data Box Blob-lagring via REST-API:er genom *http* eller *https*. När anslutningen har upprättats beskrivs de steg som krävs för att kopiera data till Data Box Blob-lagring och förbereda Data Box för transport.

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
>
> * Krav
> * Ansluta till Data Box Blob-lagring via *http* eller *https*
> * Kopiera data till Data Box

## <a name="prerequisites"></a>Krav

Innan du börjar bör du kontrollera att:

1. Du har slutfört [självstudien: Konfigurera Azure Data Box](data-box-deploy-set-up.md).
2. Du har fått din Data Box och att orderstatusen i portalen är **Levererad**.
3. Du har granskat [systemkraven för Data Box Blob-lagring](data-box-system-requirements-rest.md) och känner till versioner av API:er, SDK:er och verktyg som stöds.
4. Du har åtkomst till en värddator som har de data du vill kopiera över till Data Box. Värddatorn måste
    - Kör ett [operativsystem som stöds](data-box-system-requirements.md).
    - Vara ansluten till en höghastighetsnätverk. Vi rekommenderar starkt att du har en anslutning på minst 10 GbE. Om en anslutning på 10 GbE inte är tillgänglig kan en datalänk på 1 GbE användas, men i så fall påverkas kopieringshastigheten.
5. [Ladda ned AzCopy 7.1.0](https://aka.ms/azcopyforazurestack20170417) på värddatorn. Du använder AzCopy för att kopiera data till Azure Data Box Blob-lagring från värddatorn.


## <a name="connect-via-http-or-https"></a>Anslut via http eller https

Du kan ansluta till Data Box Blob-lagring genom *http* eller *https*.

- *Https* är det säkra och rekommenderade sättet att ansluta till Data Box Blob-lagring.
- *http* används när du ansluter via betrodda nätverk.

Stegen för att ansluta är olika när du ansluter till Data Box Blob storage via *http* eller *https*.

## <a name="connect-via-http"></a>Ansluta via http

För anslutning till REST-API:er för Data Box Blob-lagring genom *http* krävs följande steg:

- Lägga till enhets-IP-adressen och blob-tjänstslutpunkten till fjärrvärden
- Konfigurera programvara från tredje part och verifiera anslutningen

Vart och ett av dessa steg beskrivs i följande avsnitt.

### <a name="add-device-ip-address-and-blob-service-endpoint"></a>Lägga till enhetens IP-adress och blob-tjänstslutpunkt

[!INCLUDE [data-box-add-device-ip](../../includes/data-box-add-device-ip.md)]

### <a name="configure-partner-software-and-verify-connection"></a>Konfigurera partnerprogramvara och verifiera anslutningen

[!INCLUDE [data-box-configure-partner-software](../../includes/data-box-configure-partner-software.md)]

[!INCLUDE [data-box-verify-connection](../../includes/data-box-verify-connection.md)]

## <a name="connect-via-https"></a>Ansluta via https

För anslutning till REST-API:er för Azure Blob-lagring genom https krävs följande steg:

- Ladda ned certifikatet från Azure-portalen
- Importera certifikatet på klienten eller fjärrvärden
- Lägga till enhetens IP- och blob-tjänstslutpunkt till klienten eller fjärrvärden
- Konfigurera programvara från tredje part och verifiera anslutningen

Vart och ett av dessa steg beskrivs i följande avsnitt.

### <a name="download-certificate"></a>Ladda ned certifikat

Använda Azure-portalen för att ladda ned certifikatet.

1. Logga in i Azure-portalen.
2. Gå till din Data Box-order och navigera till **Allmänt > Enhetsinformation**.
3. Under **Autentiseringsuppgifter för enheten** går du till **API-åtkomst** till enheten. Klicka på **Ladda ner**. Den här åtgärden hämtar ett ** \<ditt beställningsnamn>.cer-certifikatfilen.** **Spara** den här filen. Du installerar det här certifikatet på den klient- eller värddatorn som du kommer att använda för att ansluta till enheten.

    ![Ladda ned certifikat i Azure-portalen](media/data-box-deploy-copy-data-via-rest/download-cert-1.png)
 
### <a name="import-certificate"></a>Importera certifikatet 

Åtkomst till databoxblolagring via HTTPS kräver ett TLS/SSL-certifikat för enheten. Det sätt på vilket certifikatet görs tillgängligt för klientprogrammet varierar från program till program och mellan operativsystem och distributioner. Vissa program kan komma åt certifikatet när det har importerats till systemets certifikatarkiv, medan andra program inte använder den mekanismen.

Specifik information för vissa program nämns i det här avsnittet. Mer information om andra program finns i dokumentationen för programmet och det operativsystem som används.

Följ dessa steg `.cer` för att importera filen till rotarkivet för en Windows- eller Linux-klient. På ett Windows-system kan du använda Windows PowerShell eller Windows Server-användargränssnittet för att importera och installera certifikatet i systemet.

#### <a name="use-windows-powershell"></a>Använda Windows PowerShell

1. Starta en Windows PowerShell-session som administratör.
2. Skriv följande i kommandotolken:

    ```
    Import-Certificate -FilePath C:\temp\localuihttps.cer -CertStoreLocation Cert:\LocalMachine\Root
    ```

#### <a name="use-windows-server-ui"></a>Använda Windows Server-användargränssnittet

1.   Högerklicka på `.cer` filen och välj **Installera certifikat**. Den här åtgärden startar guiden Importera certifikat.
2.   För **Store location** (Lagringsplats) väljer du **Lokal dator** och klickar sedan på **Nästa**.

    ![Importera certifikat med hjälp av PowerShell](media/data-box-deploy-copy-data-via-rest/import-cert-ws-1.png)

3.   Välj **Place all certificates in the following store** (Placera alla certifikat i följande lagringsplats) och klicka sedan på **Bläddra**. Gå till rotcertifikatarkivet på fjärrvärden och klicka på **Nästa**.

    ![Importera certifikat med hjälp av PowerShell](media/data-box-deploy-copy-data-via-rest/import-cert-ws-2.png)

4.   Klicka på **Slutför**. Ett meddelande visas där det står att importen lyckades.

    ![Importera certifikat med hjälp av PowerShell](media/data-box-deploy-copy-data-via-rest/import-cert-ws-3.png)

#### <a name="use-a-linux-system"></a>Använda ett Linux-system

Metoden för att importera ett certifikat varierar beroende på distribution.

Flera, som Ubuntu och Debian, använder `update-ca-certificates` kommandot.  

- Byt namn på den Base64-kodade `.crt` certifikatfilen för `/usr/local/share/ca-certificates directory`att ha ett tillägg och kopiera den till .
- Kör kommandot `update-ca-certificates`.

De senaste versionerna `update-ca-trust` av RHEL, Fedora och CentOS använder kommandot.

- Kopiera certifikatfilen till `/etc/pki/ca-trust/source/anchors` katalogen.
- Kör `update-ca-trust`.

Mer information finns i dokumentationen som är specifik för distributionen.

### <a name="add-device-ip-address-and-blob-service-endpoint"></a>Lägga till enhetens IP-adress och blob-tjänstslutpunkt 

Följ samma steg för att lägga till [enhetens IP-adress och blob-tjänstslutpunkt när du ansluter via *http*](#add-device-ip-address-and-blob-service-endpoint).

### <a name="configure-partner-software-and-verify-connection"></a>Konfigurera partnerprogramvara och verifiera anslutningen

Följ stegen för att [konfigurera partnerprogramvara som du använde när du anslöt via *http*](#configure-partner-software-and-verify-connection). Den enda skillnaden är att du låter alternativet *Använd http* vara avmarkerat.

## <a name="copy-data-to-data-box"></a>Kopiera data till Data Box

När du är ansluten till Data Box-lagringen är nästa steg att kopiera data. Granska följande innan du kopierar data:

* När du kopierar data ser du till att datastorleken överensstämmer med storleksbegränsningarna som beskrivs i avsnittet om [Azure Storage- och Data Box-gränser](data-box-limits.md).
* Om data som laddas upp av Data Box samtidigt laddas upp av andra program utanför Data Box kan detta resultera i att uppladdningsjobbet misslyckas samt att data skadas.
* Se till att du behåller en kopia av källdata tills du kan bekräfta att datarutan har överfört dina data till Azure Storage.

I den här självstudien används AzCopy för att kopiera data till Data Box Blob-lagring. Du kan även använda Azure Storage Explorer (om du föredrar ett GUI-baserat verktyg) eller partnerprogramvara för att kopiera data.

Kopieringsproceduren omfattar följande steg:

- Skapa en container
- Ladda upp innehåll i en mapp till Data Box Blob-lagring
- Ladda upp ändrade filer till Data Box Blob-lagring

Vart och ett av dessa steg beskrivs ingående i följande avsnitt.

### <a name="create-a-container"></a>Skapa en container

Det första steget är att skapa en container, eftersom blobar alltid laddas upp till en container. Containrar organiserar grupper av blobar på samma sätt som du ordnar filer i mappar på datorn. Skapa en blob-container genom att följa de här stegen.

1. Öppna Storage Explorer.
2. I den vänstra fönsterrutan expanderar du det lagringskonto där du vill skapa blob-containern.
3. Högerklicka på **Blob-containrar** och välj **Skapa blob-container** på snabbmenyn.

   ![Snabbmeny för att skapa blob-containrar](media/data-box-deploy-copy-data-via-rest/create-blob-container-1.png)

4. En textruta visas nedanför mappen **Blob Containers** (Blob-containrar). Ange namnet på blob-containern. Information om regler och begränsningar vid namngivning av blob-containrar finns i [Skapa containern och ange behörigheter](../storage/blobs/storage-quickstart-blobs-dotnet.md).
5. Tryck på **Retur** när du är klar så att blob-containern skapas eller på **Esc** om du vill avbryta. När blob-containern har skapats visas den i mappen **Blob Containers** (Blob-containrar) för det valda lagringskontot.

   ![Blob-containern har skapats](media/data-box-deploy-copy-data-via-rest/create-blob-container-2.png)

### <a name="upload-contents-of-a-folder-to-data-box-blob-storage"></a>Ladda upp innehåll i en mapp till Data Box Blob-lagring

Använd AzCopy för att ladda upp alla filer i en mapp till Blob-lagring i Windows eller Linux. Överför alla blobar i en mapp genom att ange följande AzCopy-kommando:

#### <a name="linux"></a>Linux

    azcopy \
        --source /mnt/myfolder \
        --destination https://data-box-storage-account-name.blob.device-serial-no.microsoftdatabox.com/container-name/files/ \
        --dest-key <key> \
        --recursive

#### <a name="windows"></a>Windows

    AzCopy /Source:C:\myfolder /Dest:https://data-box-storage-account-name.blob.device-serial-no.microsoftdatabox.com/container-name/files/ /DestKey:<key> /S


Ersätt `<key>` med din kontonyckel. Du hämtar kontonyckeln genom att gå till Azure-portalen och sedan till ditt lagringskonto. Gå till **Inställningar > Åtkomstnycklar**, välj en nyckel och klistra in den i AzCopy-kommandot.

Om den angivna målcontainern inte finns, så skapar AzCopy den och överför filen till den. Uppdatera källsökvägen till datakatalogen och ersätt `data-box-storage-account-name` i mål-URL:en med namnet på det lagringskontot som är associerat med din Data Box-enhet.

Om du vill överföra den angivna katalogens innehåll till Blob Storage rekursivt, så ange alternativet `--recursive` (Linux) eller `/S` (Windows). När du kör AzCopy med något av följande alternativ, så överförs även alla undermappar och filer.

### <a name="upload-modified-files-to-data-box-blob-storage"></a>Ladda upp ändrade filer till Data Box Blob-lagring

Använd AzCopy för att ladda upp filer baserat på den tid då de senaste ändrades. Om du vill testa detta, så ändra eller skapa nya filer i källkatalogen i testsyfte. Om du bara vill överföra uppdaterade eller nya filer, så lägg till parametern `--exclude-older` (Linux) eller `/XO` (Windows) i AzCopy-kommandot.

Om du bara vill kopiera källresurser som inte finns i målet, så ange båda parametrarna `--exclude-older` och `--exclude-newer` (Linux) eller `/XO` och `/XN` (Windows) i AzCopy-kommandot. AzCopy överför bara uppdaterade data utifrån deras tidsstämplar.

#### <a name="linux"></a>Linux
    azcopy \
    --source /mnt/myfolder \
    --destination https://data-box-storage-account-name.blob.device-serial-no.microsoftdatabox.com/container-name/files/ \
    --dest-key <key> \
    --recursive \
    --exclude-older

#### <a name="windows"></a>Windows

    AzCopy /Source:C:\myfolder /Dest:https://data-box-storage-account-name.blob.device-serial-no.microsoftdatabox.com/container-name/files/ /DestKey:<key> /S /XO

Om det finns några fel under anslutnings- eller kopieringsåtgärden läser [du Felsöka problem med datarumpblolagring](data-box-troubleshoot-rest.md).

Nästa steg är att förbereda enheten för leverans.

## <a name="next-steps"></a>Nästa steg

I den här kursen har du lärt dig om Azure Data Box-ämnen som att:

> [!div class="checklist"]
> * Krav
> * Ansluta till Data Box Blob-lagring via *http* eller *https*
> * Kopiera data till Data Box


Gå vidare till nästa självstudie och lär dig hur du skickar tillbaka din Data Box-enhet till Microsoft.

> [!div class="nextstepaction"]
> [Skicka din Azure Data Box till Microsoft](./data-box-deploy-picked-up.md)
