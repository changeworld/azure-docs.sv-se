---
title: 'Självstudiekurs: Kopiera från virtuella hårddiskar till hanterade diskar'
titleSuffix: Azure Data Box Heavy
description: Lär dig hur du kopierar data från virtuella hårddiskar från lokala VM-arbetsbelastningar till din Azure Data Box Heavy
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 07/03/2019
ms.author: alkohli
ms.openlocfilehash: 01031159d1894c7cb5f36b48f268186dff21fd22
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "77471337"
---
# <a name="tutorial-use-data-box-heavy-to-import-data-as-managed-disks-in-azure"></a>Självstudiekurs: Använd dataruta tung för att importera data som hanterade diskar i Azure

Den här självstudien beskriver hur du använder Azure Data Box Heavy för att migrera dig lokala virtuella hårddiskar till hanterade diskar i Azure. De virtuella hårddiskarna från lokala virtuella datorer kopieras till Data Box Heavy som sidblobar och överförs till Azure som hanterade diskar. Dessa hanterade diskar kan sedan kopplas till virtuella Azure-datorer.

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Gå igenom förhandskraven
> * Ansluta till Data Box Heavy
> * Kopiera data till Data Box Heavy


## <a name="prerequisites"></a>Krav

Innan du börjar bör du kontrollera att:

1. Du har slutfört [självstudien: Konfigurera Azure Data Box Heavy](data-box-heavy-deploy-set-up.md).
2. Du har fått din Data Box Heavy-enhet och orderstatusen på portalen är **Levererad**.
3. Du har anslutning till ett höghastighetsnätverk. För snabbast kopieringshastighet kan två 40-GbE-anslutningar (en per nod) användas parallellt. Om du inte har någon tillgänglig 40-GbE-anslutning rekommenderar vi att du har minst två 10 GbE-anslutningar (en per nod). 
4. Du har recenserat:

    - Hanterade [diskstorlekar som stöds i Azure-objektstorleksbegränsningar](data-box-heavy-limits.md#azure-object-size-limits).
    - [Introduktion till Azure-hanterade diskar](/azure/virtual-machines/windows/managed-disks-overview). 

## <a name="connect-to-data-box-heavy"></a>Ansluta till Data Box Heavy

Baserat på de angivna resursgrupperna skapar Data Box Heavy en resurs för varje associerad resursgrupp per nod. Om `mydbmdrg1` och `mydbmdrg2` skapades när beställningen gjordes skapas följande resurser:

- `mydbmdrg1_MDisk`
- `mydbmdrg2_MDisk`

Inom varje resurs skapas följande tre mappar som motsvarar behållare i ditt lagringskonto.

- Premium SSD
- Standard HDD
- Standard SSD

I följande tabell visas UNC-sökvägarna till resurserna i datarutan Tung.
 
|        Anslutningsprotokoll           |             UNC-sökväg till resursen                                               |
|-------------------|--------------------------------------------------------------------------------|
| SMB |`\\<DeviceIPAddress>\<ResourceGroupName_MDisk>\<Premium SSD>\file1.vhd`<br> `\\<DeviceIPAddress>\<ResourceGroupName_MDisk>\<Standard HDD>\file2.vhd`<br> `\\<DeviceIPAddress>\<ResourceGroupName_MDisk>\<Standard SSD>\file3.vhd` |  
| NFS |`//<DeviceIPAddress>/<ResourceGroup1_MDisk>/<Premium SSD>/file1.vhd`<br> `//<DeviceIPAddress>/<ResourceGroupName_MDisk>/<Standard HDD>/file2.vhd`<br> `//<DeviceIPAddress>/<ResourceGroupName_MDisk>/<Standard SSD>/file3.vhd` |

Beroende på om du använder SMB eller NFS för att ansluta till Data Box Tunga resurser är stegen för att ansluta olika.

> [!NOTE]
> - Anslutning via REST stöds inte för den här funktionen.
> - Upprepa anslutningsanvisningarna för att ansluta till den andra noden i Data Box Heavy.

### <a name="connect-to-data-box-heavy-via-smb"></a>Anslut till Data Box Heavy via SMB

Om du använder en Windows Server-värddator följer du stegen nedan för att ansluta till Data Box Heavy.

1. Det första steget är att autentisera och starta en session. Gå till **Anslut och kopiera**. Klicka på **Hämta autentiseringsuppgifter** om du vill hämta åtkomstautentiseringsuppgifterna för de resurser som är associerade med resursgruppen. Du kan också få åtkomstautentiseringsuppgifterna från **enhetsinformationen** i Azure-portalen.

    > [!NOTE]
    > Autentiseringsuppgifterna för alla resurser för hanterade diskar är identiska.

    ![Hämta resursautentiseringsuppgifter 1](media/data-box-deploy-copy-data-from-vhds/get-share-credentials1.png)

2. Kopiera **användarnamnet** och lösenordet för resursen från dialogrutan Dela och kopiera **data.** Klicka på **OK**.
    
    ![Hämta resursautentiseringsuppgifter 1](media/data-box-deploy-copy-data-from-vhds/get-share-credentials2.png)

3. Öppna ett kommandofönster om du vill komma åt de resurser som är associerade med din resurs *(mydbmdrg1* i följande exempel) från värddatorn. Skriv följande i kommandotolken:

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    Dina UNC-delningsvägar i det här exemplet är följande:

    - `\\169.254.250.200\mydbmdrg1_MDisk`
    - `\\169.254.250.200\mydbmdrg2_MDisk`
    
4. Ange lösenordet för resursen när du tillfrågas. Följande exempel visar hur du ansluter till en resurs via kommandot ovan.

    ```
    C:\>net use \\169.254.250.200\mydbmdrgl_MDisk /u:mdisk
    Enter the password for ‘mdisk’ to connect to '169.254.250.200':
    The command completed successfully.
    C: \>
    ```

4. Tryck på Windows + R. I fönstret **Kör** anger du `\\<device IP address>\<ShareName>`. Öppna Utforskaren genom att klicka på **OK**.
    
    ![Ansluta till resursen via Utforskaren 2](media/data-box-deploy-copy-data-from-vhds/connect-shares-file-explorer1.png)

    Du bör nu se följande förskapade mappar i varje resurs.
    
    ![Ansluta till resursen via Utforskaren 2](media/data-box-deploy-copy-data-from-vhds/connect-shares-file-explorer2.png)


### <a name="connect-to-data-box-heavy-via-nfs"></a>Anslut till Data Box Heavy via NFS

Om du använder en Linux-värddator utför du följande steg för att konfigurera enheten så att den ger åtkomst till NFS-klienter.

1. Ange IP-adresserna för de tillåtna klienterna som har åtkomst till resursen. I det lokala webbgränssnittet går du till sidan **Anslut och kopiera**. Under **NFS-inställningar** klickar du på **NFS-klientåtkomst**.

    ![Konfigurera NFS-klientåtkomst 1](media/data-box-deploy-copy-data-from-vhds/nfs-client-access1.png)

2. Ange NFS-klientens IP-adress och klicka på **Add**. Du kan konfigurera åtkomst för flera NFS genom att upprepa det här steget. Klicka på **OK**.

    ![Konfigurera NFS-klientåtkomst 2](media/data-box-deploy-copy-data-from-vhds/nfs-client-access2.png)

2. Kontrollera att Linux-värddatorn har en NFS-klient av en [version som stöds](data-box-system-requirements.md) installerad. Använd den specifika versionen för din Linux-distribution.

3. När NFS-klienten har installerats använder du följande kommando för att montera NFS-resursen på enheten:

    `sudo mount <Data Box or Data Box Heavy IP>:/<NFS share on Data Box or Data Box Heavy device> <Path to the folder on local Linux computer>`

    Följande exempel visar hur du ansluter via NFS till en databox eller databox tung resurs. Data Box eller Data Box `169.254.250.200`Heavy enhet `mydbmdrg1_MDisk` IP är , aktien är monterad `/home/databoxubuntuhost/databox`på ubuntuVM, monteringspunkten är .

    `sudo mount -t nfs 169.254.250.200:/mydbmdrg1_MDisk /home/databoxubuntuhost/databox`


## <a name="copy-data-to-data-box-heavy"></a>Kopiera data till Data Box Heavy

När du är ansluten till dataservern är nästa steg att kopiera data. VHD-filen kopieras till mellanlagringslagringskontot som sidblobb. Sidbloben konverteras sedan till en hanterad disk och flyttas till en resursgrupp.

Läs följande innan du börjar kopiera data:

- Kopiera alltid de virtuella hårddiskarna till en av de förskapade mapparna. Om du kopierar virtuella hårddiskar utanför dessa mappar eller i en mapp som du skapade, överförs de virtuella hårddiskarna till Azure Storage-kontot som sidblobar och inte hanterade diskar.
- Endast fasta virtuella hårddiskar kan laddas upp för att skapa hanterade diskar. VHDX-filer eller dynamiska och differentierande virtuella hårddiskar stöds inte.
- Du kan bara ha en hanterad disk med ett visst namn i en resursgrupp i alla förskapade mappar. Det betyder att de virtuella hårddiskarna som laddats upp till de förskapade mapparna måste ha unika namn. Kontrollera att namnet inte matchar en redan befintlig hanterad disk i en resurs grupp.
- Granska hanterade diskgränser i [Azure-objektstorleksbegränsningar](data-box-heavy-limits.md#azure-object-size-limits).

Beroende på om du ansluter via SMB eller NFS kan du använda:

- [Kopiera data via SMB](data-box-heavy-deploy-copy-data.md#copy-data-to-data-box-heavy)
- [Kopiera data via NFS](data-box-heavy-deploy-copy-data-via-nfs.md#copy-data-to-data-box-heavy)

Vänta tills kopieringsjobben är klara. Kontrollera att kopieringsjobben har slutförts utan fel innan du går till nästa steg.

![Inga fel på sidan **Anslut och kopiera**](media/data-box-deploy-copy-data-from-vhds/verify-no-errors-connect-and-copy.png)

Om det finns fel under kopieringsprocessen hämtar du loggarna från sidan **Anslut och kopiera.**

- Om du kopierade en fil som inte är 512-byte justerad överförs filen inte som sidblob till ditt mellanlagringslagringskonto. Du kommer att se ett fel i loggarna. Ta bort filen och kopiera en fil som är 512 byte justerad.

- Om du kopierade en VHDX (dessa filer stöds inte) med ett långt namn visas ett fel i loggarna.

    ![Fel i loggarna från **Anslut och kopiera** sida](media/data-box-deploy-copy-data-from-vhds/errors-connect-and-copy.png)

    Lös felen innan du går vidare till nästa steg.

För att säkerställa dataintegriteten beräknas kontrollsumman infogat när data kopieras. När kopieringen är klar kontrollerar du det använda utrymmet och det lediga utrymmet på enheten.
    
![Kontrollera ledigt och använt utrymme på instrumentpanelen](media/data-box-deploy-copy-data-from-vhds/verify-used-space-dashboard.png)

När kopieringsjobbet är klart kan du gå till **Förbered för att skicka**.


## <a name="next-steps"></a>Nästa steg

I den här självstudien om Azure Data Box Heavy har du bland annat lärt dig att:

> [!div class="checklist"]
> * Gå igenom förhandskraven
> * Ansluta till Data Box Heavy
> * Kopiera data till Data Box Heavy


Gå vidare till nästa självstudie och lär dig hur du skickar tillbaka din Data Box Heavy till Microsoft.

> [!div class="nextstepaction"]
> [Skicka Azure Data Box Heavy till Microsoft](./data-box-heavy-deploy-picked-up.md)

