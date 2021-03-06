---
title: 'Självstudiekurs: Kopiera data till Azure Data Box Heavy via datakopieringstjänst'
description: I den här självstudien får du lära dig hur du kopierar data till din Azure Data Box Heavy-enhet via datakopieringstjänsten
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 07/03/2019
ms.author: alkohli
ms.openlocfilehash: 67547db53d2b9ce05838335ffcb5d789b77ecbbe
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "77560228"
---
# <a name="tutorial-use-the-data-copy-service-to-copy-data-into-azure-data-box-heavy-preview"></a>Självstudiekurs: Använd datakopieringstjänsten för att kopiera data till Azure Data Box Heavy (förhandsgranskning)

I den här självstudien beskrivs hur du matar in data med hjälp av datakopieringstjänsten utan en mellanliggande värd. Datakopieringstjänsten körs lokalt på Azure Data Box Heavy, ansluter till din nätverksanslutna lagringsenhet (NAS) via SMB och kopierar data till Data Box Heavy.

Använd datakopieringstjänsten:

- I NAS miljöer där mellanliggande värdar kanske inte är tillgängliga.
- Med små filer som kräver veckor för inmatning och uppladdning av data. Datakopieringstjänsten förbättrar avsevärt tiden för inmatning och uppladdning för små filer.

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Kopiera data till Data Box Heavy

## <a name="prerequisites"></a>Krav

Innan du börjar bör du kontrollera att:

1. Du har slutfört den här självstudien: [Konfigurera Azure Data Box Heavy](data-box-heavy-deploy-set-up.md).
2. Du har fått din Data Box Heavy-enhet och orderstatusen på portalen är **Levererad**.
3. Du har autentiseringsuppgifterna för den NAS-källenhet som du kommer att ansluta till för datakopiering.
4. Du har anslutning till ett höghastighetsnätverk. För snabbast kopieringshastighet kan två 40-GbE-anslutningar (en per nod) användas parallellt. Om du inte har någon tillgänglig 40-GbE-anslutning rekommenderar vi att du har minst två 10 GbE-anslutningar (en per nod). 

## <a name="copy-data-to-data-box-heavy"></a>Kopiera data till Data Box Heavy

När du har anslutit till NAS är nästa steg att kopiera data. Granska följande innan du kopierar data:

- När du kopierar data, se till att datastorleken överensstämmer med de storleksbegränsningar som beskrivs i artikeln [Azure storage and Data Box Heavy limits](data-box-heavy-limits.md).
- Om data som överförs av Data Box Heavy samtidigt överförs av andra program utanför Data Box Heavy kan uppladdningsjobbfel och dataskador uppstå.
- Om data ändras när datakopieringstjänsten läser dem kan det uppstå fel eller skadade data.

För att kopiera data med hjälp av datakopieringstjänsten behöver du skapa ett jobb:

1. I det lokala webbgränssnittet för din Data Box Heavy-enhet går du till **Hantera** > **kopieringsdata**.
2. På sidan **Kopiera data** väljer du **Skapa**.

    ![Välj Skapa på sidan ”Kopiera data”](media/data-box-deploy-copy-data-via-copy-service/click-create.png)

3. I dialogrutan **Konfigurera och starta** fyller du i följande fält:
    
    |Field                          |Värde    |
    |-------------------------------|---------|
    |**Jobbnamn**                       |Ett unikt namn med färre än 230 tecken för jobbet. Följande tecken tillåts inte i jobbnamnet: \<, \>, \|, \?, \*, \\, \:, \/ och \\\.         |
    |**Källplats**                |Ange SMB-sökvägen till datakällan i formatet: `\\<ServerIPAddress>\<ShareName>` eller `\\<ServerName>\<ShareName>`.        |
    |**Användarnamn**                       |Användarnamn i `\\<DomainName><UserName>`-format för åtkomst till datakällan. Om en lokal administratör ansluter behöver de uttryckliga säkerhetsbehörigheter. Högerklicka på mappen, välj **Egenskaper** och välj sedan **Säkerhet**. Detta bör lägga till den lokala administratören på fliken **Säkerhet.**       |
    |**Lösenord**                       |Lösenord för åtkomst till datakällan.           |
    |**Mållagringskonto**    |Välj mållagringskonto för uppladdning av data från listan.         |
    |**Måltyp**       |Välj mållagringstypen i listan: **Blockera Blob,** **Sidblob**eller **Azure-filer**.        |
    |**Målcontainer/-resurs**    |Ange namnet på den container eller resurs som du vill ladda upp data till i ditt mållagringskonto. Namnet kan vara ett resursnamn eller ett containernamn. Använd till exempel `myshare` eller `mycontainer`. Du kan även ange namnet i formatet `sharename\directory_name` eller `containername\virtual_directory_name`.        |
    |**Matchningsmönster för filkopiering**    | Du kan ange matchningsmönstret för filnamn på följande två sätt:<ul><li>**Använda jokerteckenuttryck:** Endast `*` `?` och stöds i jokerteckenuttryck. Till exempel matchar uttrycket `*.vhd` alla filer som har filnamnstillägget `.vhd`. På ett liknande sätt matchar `*.dl?` alla filer med antingen filnamnstillägget `.dl` eller som börjar med `.dl`, till exempel `.dll`. Och `*foo` matchar alla filer vars namn slutar med `foo`.<br>Du kan ange uttrycket med jokertecken direkt i fältet. Som standard behandlas det värde som du anger i fältet som ett uttryck med jokertecken.</li><li>**Använd reguljära uttryck:** POSIX-baserade reguljära uttryck stöds. Till exempel matchar det reguljära uttrycket `.*\.vhd` alla filer som har filnamnstillägget `.vhd`. För reguljära uttryck anger du `<pattern>` direkt som `regex(<pattern>)`. Mer information om reguljära uttryck finns i [snabbreferensen för reguljära uttryck](https://docs.microsoft.com/dotnet/standard/base-types/regular-expression-language-quick-reference).</li><ul>|
    |**Filoptimering**              |När den här funktionen är aktiverad packas filer som är mindre än 1 MB under inmatning. Paketeringen gör datakopieringen snabbare för små filer. Den sparar även betydande tid när antalet filer långt överstiger antalet kataloger.        |
 
4. Välj **Starta**. Indata valideras, och om validering lyckas startas jobbet. Det kan ta några minuter för jobbet att startas.

    ![Starta ett jobb från dialogrutan ”Konfigurera jobb och starta”](media/data-box-deploy-copy-data-via-copy-service/configure-and-start.png)

5. Ett jobb med de angivna inställningarna skapas. Du kan pausa, återuppta, avbryta eller starta om ett jobb. Markera kryssrutan intill jobbnamnet och välj sedan lämplig knapp.

    ![Hantera ett jobb på sidan ”Kopiera data”](media/data-box-deploy-copy-data-via-copy-service/select-job.png)
    
    - Du kan pausa ett jobb om det påverkar NAS-enhetens resurser under tider med hög belastning:

        ![Pausa ett jobb på sidan ”Kopiera data”](media/data-box-deploy-copy-data-via-copy-service/pause-job.png)

        Du kan återuppta jobbet senare under tider med låg belastning:

        ![Återuppta ett jobb på sidan ”Kopiera data”](media/data-box-deploy-copy-data-via-copy-service/resume-job.png)

    - Du kan avbryta ett jobb när som helst:

        ![Avbryta ett jobb på sidan ”Kopiera data”](media/data-box-deploy-copy-data-via-copy-service/cancel-job.png)
        
        När du avbryter ett jobb krävs en bekräftelse:

        ![Bekräfta jobbavbrott](media/data-box-deploy-copy-data-via-copy-service/confirm-cancel-job.png)

        Om du avbryter ett jobb tas data som redan har kopierats inte bort. Om du vill ta bort data som du har kopierat till din Data Box-enhet återställer du enheten.

        ![Återställa en enhet](media/data-box-deploy-copy-data-via-copy-service/reset-device.png)

        >[!NOTE]
        > Om du avbryter eller pausar ett jobb kan det hända att stora filer kopieras endast delvis. De här delvis kopierade filerna laddas upp i samma tillstånd till Azure. När du avbryter eller pausar ett jobb bör du kontrollera att filerna har kopierats korrekt. För att validera filerna tittar du på SMB-resurser eller laddar ned BOM-filen.

    - Du kan starta om ett jobb om det har misslyckats på grund av ett tillfälligt fel, till exempel ett glapp i nätverket. Du kan dock inte starta om ett jobb om det har nått ett avslutat tillstånd, till exempel **Succeeded** (Lyckades) eller **Completed with errors** (Slutfört med fel). Jobbfel kan orsakas av problem med filnamngivning eller filstorlek. De här felen loggas, men jobbet kan inte startas om när det väl har slutförts.

        ![Starta om ett jobb som misslyckades](media/data-box-deploy-copy-data-via-copy-service/restart-failed-job.png)

        Om det uppstår ett fel och du inte kan starta om jobbet laddar du ned felloggarna och letar upp felet i loggfilerna. När du har åtgärdat problemet skapar du ett nytt jobb för att kopiera filerna. Du kan även [kopiera filerna över SMB](data-box-deploy-copy-data.md).
    
    - I den här versionen kan du inte ta bort ett jobb.
    
    - Du kan skapa obegränsade jobb men kan köra högst 10 jobb parallellt åt gången.
    - Om **Filoptimering** är aktiverat packas små filer under inmatning för att förbättra kopieringsprestanda. I dessa fall visas en packad fil (den har ett GUID som filnamn). Ta inte bort den här filen. Det kommer att packas upp under uppladdningen.

6. När jobbet pågår på sidan **Kopiera data**:

    - I kolumnen **Status** kan du visa statusen för kopieringsjobbet. Statusen kan vara:
        - **Körs**
        - **Misslyckades**
        - **Lyckades**
        - **Pausar**
        - **Pausat**
        - **Avbryts**
        - **Avbrutet**
        - **Slutfört med fel**
    - I kolumnen **Filer** ser du antalet och den totala storleken på de filer som kopieras.
    - I kolumnen **Bearbetade** ser du antalet och den totala storleken på de filer som bearbetas.
    - I kolumnen **Jobbinformation** väljer du **Visa** om du vill se jobbinformation.
    - Om det inträffar några fel under kopieringsprocessen enligt vad som visas i kolumnen **Antal fel** går du till kolumnen **Fellogg** och laddar ned felloggarna för felsökning.

Vänta tills kopieringsjobbet är klart. Eftersom vissa fel endast loggas på sidan **Anslut och kopiera** kontrollerar du att kopieringsjobben har slutförts utan fel innan du går vidare till nästa steg.

![Inga fel på sidan ”Anslut och kopiera”](media/data-box-deploy-copy-data-via-copy-service/verify-no-errors-on-connect-and-copy.png)

För att säkerställa dataintegriteten beräknas en kontrollsumma infogat när data kopieras. När kopieringen är klar väljer du **Visa instrumentpanel** och kontrollerar det använda utrymmet och det lediga utrymmet på enheten.
    
![Kontrollera ledigt och använt utrymme på instrumentpanelen](media/data-box-deploy-copy-data-via-copy-service/verify-used-space-dashboard.png)

> [!IMPORTANT]
> Upprepa samma instruktioner för att kopiera data till den andra noden på Data Box Heavy.

När kopieringsjobbet är klart kan du välja **Förbered för att skicka**.

>[!NOTE]
> **Förbereda leveransen** kan inte köras medan kopieringsjobb pågår.

## <a name="next-steps"></a>Nästa steg

Gå vidare till nästa självstudiekurs om du vill lära dig hur du skickar tillbaka din Data Box Heavy-enhet till Microsoft.

> [!div class="nextstepaction"]
> [Skicka din Azure Data Box Heavy-enhet till Microsoft](./data-box-heavy-deploy-picked-up.md)

