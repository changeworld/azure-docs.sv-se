---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 592131bf6cca4c4c3c827de23742e8d52bcb4d1c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67187398"
---
#### <a name="to-mount-initialize-and-format-a-volume"></a>Montera, initiera och formatera en volym
1. Starta Microsoft iSCSI-initieraren.
2. I fönstret **Egenskaper för iSCSI-initieraren**, på fliken **Identifiering**, klickar du på **Identifiera portal**.
3. I dialogrutan **Identifiera målportal**, anger du IP-adressen för ditt iSCSI-aktiverade nätverksgränssnitt och klickar på **OK**. 
4. I fönstret **Egenskaper för iSCSI-initieraren**, på fliken **Mål**, letar du upp **Upptäckta mål**. Enhetens status borde visas som **Inaktiv**.
5. Välj målenheten och klicka sedan på **Anslut**. När enheten är ansluten, bör statusen ändras till **Ansluten**. (Mer information om hur du använder Microsofts iSCSI-initierare finns i [Installera och konfigurera Microsoft iSCSI-initieraren][1]).
6. På din Windows-värd trycker du på Windows-tangenten + X och klickar sedan på **Kör**. 
7. I dialogrutan **Kör**, skriver du in **diskmgmt.msc**. Klicka på **OK** och dialogrutan **Diskhantering** visas. Den högra panelen visar volymerna som finns på din värd.
8. I fönstret **Diskhantering** visas de monterade volymerna enligt följande bild. Högerklicka på den identifierade volymen (klicka på diskens namn) och klicka sedan på **Online**.
   
     ![Initiera formatera volym](./media/storsimple-mount-initialize-format-volume/HCS_InitializeFormatVolume-include.png) 
9. Högerklicka på volymen (klicka på disknamnet) och klicka sedan på **Initiera**.
10. Genomför följande steg för att formatera en enkel volym:
    
    1. Välj volymen genom att högerklicka på den (klicka på rätt område) och klicka på **Ny enkel volym**.
    2. I guiden Ny enkel volym anger du volymstorlek och enhetsbeteckning och konfigurerar volymen som ett NTFS-filsystem.
    3. Ange en storlek på allokeringsenheterna på 64 KB. Den här storleken för allokeringsenheter fungerar väl med de dedupliceringsalgoritmer som används av StorSimple-lösningen.
    4. Utför en snabbformatering.

![Video](./media/storsimple-mount-initialize-format-volume/Video_icon.png) tillgänglig **Video tillgänglig**

Om du vill se en video som visar hur du monterar, initierar och formaterar en StorSimple-volym, klickar du [här](https://azure.microsoft.com/documentation/videos/mount-initialize-and-format-a-storsimple-volume/).

<!--Link references-->
[1]: https://technet.microsoft.com/library/ee338480(WS.10).aspx
