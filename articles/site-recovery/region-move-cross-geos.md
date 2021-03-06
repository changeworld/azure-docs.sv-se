---
title: Flytta virtuella Azure-datorer mellan myndigheter och offentliga regioner med Azure Site Recovery
description: Använd Azure Site Recovery för att flytta virtuella Azure-datorer mellan Azure-myndigheter och offentliga regioner.
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: acaf16e7469b3ea4e5e391db91e37dc76be3b261
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "78298538"
---
# <a name="move-azure-vms-between-azure-government-and-public-regions"></a>Flytta virtuella Azure-datorer mellan Azure Government-regioner och offentliga regioner 

Du kanske vill flytta dina virtuella IaaS-datorer mellan Azure Government och Public Regions för att öka tillgängligheten för dina befintliga virtuella datorer, förbättra hanterbarheten eller av styrningsskäl, som beskrivs [här](azure-to-azure-move-overview.md).

Utöver att använda tjänsten [Azure Site Recovery](site-recovery-overview.md) för att hantera och samordna haveriberedskap på lokala datorer och virtuella Azure-datorer i syftet affärskontinuitet och haveriberedskap (BCDR) kan du även använda Site Recovery för att hantera flytt av virtuella Azure-datorer till en sekundär region.       

Den här självstudien visar hur du flyttar virtuella Azure-datorer mellan Azure Government och offentliga regioner med Hjälp av Azure Site Recovery. Detsamma kan utökas för att flytta virtuella datorer mellan regionpar som inte finns inom samma geografiska kluster. I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Kontrollera förutsättningar
> * Förbereda källregionens virtuella datorer
> * Förbereda målregionen
> * Kopiera data till målregionen
> * Testa konfigurationen
> * Utföra flytten
> * Ta bort resurserna från källregionen

> [!IMPORTANT]
> Den här självstudien visar hur du flyttar virtuella Azure-datorer mellan Azure Government och Public-regioner eller mellan regioner par som inte stöds av den vanliga katastrofåterställningslösningen för virtuella Azure-datorer. Om käll- och målområdena [stöds](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support)kan du läsa det här [dokumentet](azure-to-azure-tutorial-migrate.md) för flytten. Om ditt krav är att förbättra tillgängligheten genom att flytta virtuella datorer i en tillgänglighetsuppsättning till zonsatta virtuella datorer i en annan region läser du självstudien [här](move-azure-VMs-AVset-Azone.md).

> [!IMPORTANT]
> Det är inte tillrådligt att använda den här metoden för att konfigurera DR mellan regionpar som inte stöds när paren definieras med datafördröjning i åtanke, vilket är avgörande för ett DR-scenario.

## <a name="verify-prerequisites"></a>Kontrollera förutsättningar

> [!NOTE]
> Se till att du förstår [arkitekturen och komponenterna](physical-azure-architecture.md) för det här scenariot. Den här arkitekturen används för att flytta virtuella Azure-datorer **genom att behandla de virtuella datorerna som fysiska servrar**.

- Granska [kraven för stöd](vmware-physical-secondary-support-matrix.md) för alla komponenter.
- Kontrollera att de servrar som du vill replikera uppfyller [Kraven på Azure VM](vmware-physical-secondary-support-matrix.md#replicated-vm-support).
- Förbered ett konto för automatisk installation av mobilitetstjänsten på varje server som du vill replikera.

- Observera att när du har växlat över till målregionen i Azure kan du inte direkt utföra en återställning till källregionen. Du måste ställa in replikering igen tillbaka till målet.

### <a name="verify-azure-account-permissions"></a>Verifiera Azure-kontobehörigheter

Kontrollera att ditt Azure-konto har behörigheter för replikering av virtuella datorer till Azure.

- Granska de [behörigheter](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) du behöver för att replikera datorer till Azure.
- Verifiera och ändra [rollbaserade](../role-based-access-control/role-assignments-portal.md) åtkomstbehörigheter. 

### <a name="set-up-an-azure-network"></a>Skapa ett Azure-nätverk

Konfigurera ett [Azure-nätverk](../virtual-network/quick-create-portal.md)för målet .

- Virtuella Azure-datorer placeras i det här nätverket när de skapas efter redundans.
- Nätverket ska finnas i samma region som Recovery Services-valvet


### <a name="set-up-an-azure-storage-account"></a>Skapa ett Azure-lagringskonto

Konfigurera ett [Azure-lagringskonto](../storage/common/storage-account-create.md).

- Site Recovery replikerar lokala datorer till Azure-lagring. Virtuella Azure-datorer skapas från lagringen när redundans inträffar.
- Lagringskontot måste finnas i samma region som Recovery Services-valvet.


## <a name="prepare-the-source-vms"></a>Förbereda källregionens virtuella datorer

### <a name="prepare-an-account-for-mobility-service-installation"></a>Förbereda ett konto för installation av mobilitetstjänsten

Mobilitetstjänsten måste installeras på varje server som du vill replikera. Site Recovery installerar den här tjänsten automatiskt när du aktiverar replikering för servern. Om du vill installera automatiskt måste du förbereda ett konto som Site Recovery använder för att komma åt servern.

- Du kan använda en domän eller ett lokalt konto
- Om du inte använder ett domänkonto för Windows-datorer inaktiverar du kontrollen Fjärråtkomst på den lokala datorn. Det gör du i registret under **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System**, lägger du till DWORD-posten **LocalAccountTokenFilterPolicy**med värdet 1.
- Om du vill lägga till registerposten för att inaktivera inställningen från en CLI skriver du:``REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1.``
- För Linux bör kontot vara rot på källan Linux-server.


## <a name="prepare-the-target-region"></a>Förbereda målregionen

1. Verifiera att Azure-prenumerationen låter dig skapa virtuella datorer i målregionen för haveriberedskap. Kontakta supporten och aktivera den kvot som krävs.

2. Se till att din prenumeration har tillräckligt med resurser för att hantera virtuella datorer med de storlekar som de virtuella källdatorerna har. Om du använder Site Recovery till att kopiera data till målet väljer det samma storlek eller närmast möjliga storlek för den virtuella måldatorn.

3. Se till att du skapar en målresurs för varje komponent som identifieras i källans nätverkslayout. Detta är viktigt för att säkerställa att dina virtuella datorer har alla funktioner och funktioner som du hade i källan efter att ha klippt över till målregionen.

    > [!NOTE]
    > Azure Site Recovery identifierar och skapar automatiskt ett virtuellt nätverk när du aktiverar replikering för den virtuella källdatorn. Du kan också skapa ett nätverk i förväg och tilldela det till den virtuella datorn i arbetsflödet där du aktiverar replikering. Du behöver dock skapa andra resurser manuellt i målregionen, enligt beskrivningen nedan.

     I följande dokument visas hur du skapar de vanligaste nätverksresurserna, baserat på den virtuella källdatorkonfigurationen.

    - [Säkerhetsgrupper för nätverk](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)
    - [Lastbalanserare](https://docs.microsoft.com/azure/load-balancer)
    - [Offentlig IP](../virtual-network/virtual-network-public-ip-address.md)
    
    För andra nätverkskomponenter finns i [nätverksdokumentationen](https://docs.microsoft.com/azure/?pivot=products&panel=network).

4. Du kan [skapa ett icke-produktionsnätverk](https://docs.microsoft.com/azure/virtual-network/quick-create-portal) manuellt i målregionen om du vill testa konfigurationen innan du slutgiltigt växlar över till målregionen. Detta ger minimala störningar i produktionen och rekommenderas därför.

## <a name="copy-data-to-the-target-region"></a>Kopiera data till målregionen
I stegen nedan får du hjälp att kopiera data till målregionen med hjälp av Azure Site Recovery.

### <a name="create-the-vault-in-any-region-except-the-source-region"></a>Skapa valvet i valfri region, utom i källregionen.

1. Logga in på [Azure Portal](https://portal.azure.com) > **Recovery Services**.
2. Klicka på **Skapa en** > **säkerhetskopiering****av** > resurser och återställning av webbplatser .
3. I **Namn** anger du det egna namnet **ContosoVMVault**. Om du har fler än en prenumeration väljer du den rätta.
4. Skapa en resursgrupp med namnet **ContosoRG**.
5. Ange en Azure-region. Information om vilka regioner som stöds finns under Geografisk tillgänglighet i avsnittet med [Azure Site Recovery-prisinformation](https://azure.microsoft.com/pricing/details/site-recovery/).
6. I Recovery Services-valv klickar du på **Översikt** > **ConsotoVMVault** > **+Replikera**
7. Välj **Till Azure** > **Inte virtualiserad/annan**.

### <a name="set-up-the-configuration-server-to-discover-vms"></a>Konfigurera konfigurationsservern för att identifiera virtuella datorer.


Konfigurera konfigurationsservern, registrera den i valvet och upptäck virtuella datorer.

1. Klicka på > **Infrastrukturkälla för**förbereda infrastruktur för > **platsåterställning**. **Site Recovery**
2. Om du inte har någon konfigurationsserver klickar du på **+Konfigurationsserver**.
3. Kontrollera att Configuration **Server** visas i **servertyp**i **Lägg till**server .
4. Hämta installationsfilen för installationsprogrammet för enhetlig installation för webbplatsåterställning.
5. Ladda ned valvregistreringsnyckeln. Du behöver detta när du kör Unified Setup. Nyckeln är giltig i fem dagar efter att du har genererat den.

   ![Konfigurera källan](./media/physical-azure-disaster-recovery/source-environment.png)


### <a name="register-the-configuration-server-in-the-vault"></a>Registrera konfigurationsservern i valvet

Gör följande innan du börjar: 

#### <a name="verify-time-accuracy"></a>Verifiera tidsnoggrannhet
Kontrollera att systemklockan är synkroniserad med en [tidsserver](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/get-started/windows-time-service/windows-time-service)på konfigurationsserverdatorn . Det borde stämma. Om det är 15 minuter framför eller bakom kan installationen misslyckas.

#### <a name="verify-connectivity"></a>Verifiera anslutning
Kontrollera att datorn kan komma åt dessa webbadresser baserat på din miljö: 

[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]  

IP-adressbaserade brandväggsregler bör tillåta kommunikation till alla Azure-url:er som visas ovan via HTTPS-port (443). För att förenkla och begränsa IP-intervallen rekommenderar vi att URL-filtrering görs.

- **Kommersiella IP-adresser** – Tillåt [Azure Datacenter IP-intervall](https://www.microsoft.com/download/confirmation.aspx?id=41653)och HTTPS-porten (443). Tillåt IP-adressintervall för Azure-regionen för din prenumeration för att stödja webbadresserna AAD, Backup, Replication och Lagring.  
- **Myndighets-IP** - Tillåt [azure government datacenter IP-intervall](https://www.microsoft.com/en-us/download/details.aspx?id=57063)och HTTPS-porten (443) för alla USGov-regioner (Virginia, Texas, Arizona och Iowa) för att stödja AAD-, säkerhetskopierings-, replikerings- och lagringsadresser.  

#### <a name="run-setup"></a>Kör installationsprogrammet
Installera konfigurationsservern genom att köra enhetliga installationsprogrammet som lokal administratör. Processservern och huvudmålservern installeras också som standard på konfigurationsservern.

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

När registreringen är klar visas konfigurationsservern på sidan **Inställningars** > **servrar** i valvet.

### <a name="configure-target-settings-for-replication"></a>Konfigurera målinställningar för replikering

Välj och kontrollera målresurserna.

1. Klicka på **Förbered infrastrukturmål** > **Target**och välj den Azure-prenumeration som du vill använda.
2. Ange måldistributionsmodellen.
3. Site Recovery kontrollerar att du har ett eller flera kompatibla Azure-lagringskonton och Azure-nätverk.

   ![Mål](./media/physical-azure-disaster-recovery/network-storage.png)


### <a name="create-a-replication-policy"></a>Skapa replikeringsprincip

1. Om du vill skapa en ny replikeringsprincip klickar du på > **Replikeringsprinciper för** > replikering av **platsåterställning****+Replikeringsprincip**.
2. I **Skapa replikeringsprincip** anger du ett principnamn.
3. I **Tröskelvärde för replikeringspunktmål** anger du gränsen för replikeringspunktmålet (RPO). Det här värdet anger hur ofta dataåterställningspunkter skapas. En avisering genereras när den kontinuerliga replikeringen överskrider den här gränsen.
4. I **Återställningspunkt för kvarhållning** anger du kvarhållningsperioden (i antal timmar) för varje återställningspunkt. Replikerade virtuella datorer kan återställas till valfri punkt i ett fönster. Upp till 24 timmars kvarhållning stöds för datorer replikerade till premiumlagring och 72 timmar för standardlagring.
5. I **Appkonsekvent ögonblicksbildfrekvens**anger du hur ofta (i minuter) återställningspunkter som innehåller programkonsekventa ögonblicksbilder ska skapas. Klicka på **OK** för att skapa principen.

    ![Replikeringsprincip](./media/physical-azure-disaster-recovery/replication-policy.png)


Principen associeras automatiskt med konfigurationsservern. Som standard skapas automatiskt en matchande princip för återställning efter fel. Om replikeringsprincipen till exempel är **rep-princip** skapas en återställningsprincip för återställning av **principåterfrisör.** Den här principen används inte förrän du initierar en återställning efter fel från Azure.

### <a name="enable-replication"></a>Aktivera replikering

- Site Recovery installerar mobilitetstjänsten när replikering är aktiverad.
- När du aktiverar replikering för en server kan det ta 15 minuter eller längre innan ändringarna börjar gälla och visas i portalen.

1. Klicka på **Replikera programkälla** > **Source**.
2. I **Källa** väljer du konfigurationsservern.
3. I **Maskintyp**väljer du **Fysiska datorer**.
4. Välj processserver (konfigurationsservern). Klicka sedan på **OK**.
5. I **Mål**väljer du den prenumeration och resursgrupp där du vill skapa virtuella Azure-datorer efter redundans. Välj den distributionsmodell som du vill använda i Azure (klassisk hantering eller resurshantering).
6. Välj Azure-lagringskonto som du vill använda när du replikerar data. 
7. Välj det Azure-nätverk och undernät som virtuella Azure-datorer ska ansluta till efter en redundansväxling.
8. Välj **Konfigurera nu för valda datorer**om du vill använda nätverksinställningen på alla datorer som du väljer för skydd. Välj **Konfigurera senare** om du vill välja Azure-nätverket för varje dator. 
9. I **Fysiska datorer**och klicka på **+Fysisk dator**. Ange namn och IP-adress. Välj operativsystemet för den dator som du vill replikera. Det tar några minuter för servrarna att upptäckas och listas. 

   > [!WARNING]
   > Du måste ange IP-adressen för den Azure VM du tänker flytta

10. I **Egenskaper** > **Konfigurera egenskaper**väljer du det konto som ska användas av processservern för att automatiskt installera mobilitetstjänsten på datorn.
11. Kontrollera att rätt replikeringsprincip är markerad i **Replikeringsinställningar** > Konfigurera**replikeringsinställningar.** 
12. Klicka på **Aktivera replikering**. Du kan spåra förloppet för jobbet **Aktivera skydd** i**Jobs** > **Jobbplatsåterställningsjobb**för **inställningar.** >  När jobbet **Slutför skydd** har körts är datorn redo för redundans.


Om du vill övervaka servrar som du lägger till kan du kontrollera den senast identifierade tiden för dem i **konfigurationsservrar** > **senaste kontakt vid**. Om du vill lägga till datorer utan att vänta på en schemalagd identifieringstid markerar du konfigurationsservern (klicka inte på den) och klickar på **Uppdatera**.

## <a name="test-the-configuration"></a>Testa konfigurationen


1. Navigera till valvet, i **Inställningar** > **replikerade objekt**, klicka på den virtuella datorn du tänker flytta till målområdet, klicka på **+Testa redundansikonen.**
2. I **Testa redundans** väljer du en återställningspunkt som ska användas för redundansen:

   - **Senaste bearbetade**: Redundansväxlar den virtuella datorn till den senaste återställningspunkten som bearbetades av Site Recovery-tjänsten. Tidsstämpeln visas. Med det här alternativet läggs ingen tid på bearbetning av data, så den ger ett lågt mål för återställningstid (RTO)
   - **Senaste app-konsekventa**: Det här alternativet redundansväxlar alla virtuella datorer till den senaste app-konsekventa återställningspunkten. Tidsstämpeln visas.
   - **Anpassad**: Välj annan återställningspunkt.

3. Välj målets virtuella Azure-nätverk, dit du vill flytta de virtuella Azure-datorerna för att testa konfigurationen. 

   > [!IMPORTANT]
   > Vi rekommenderar att du använder ett separat Azure VM-nätverk för test redundans och inte det produktionsnätverk som du vill flytta dina virtuella datorer till så småningom som skapades när du aktiverade replikering.

4. När du vill börja testa flytten klickar du på **OK**. Om du vill spåra förloppet klickar du på den virtuella datorn för att öppna dess egenskaper. Du kan också klicka på jobbet **Testa redundans** i valvnamnet > **Inställningar** > **Jobb** > **Site Recovery-jobb**.
5. När redundansväxlingen är klar visas repliken av den virtuella Azure-datorn i Azure-portalen > **Virtual Machines**. Kontrollera att den virtuella datorn körs med rätt storlek och ansluten till lämpligt nätverk.
6. Om du vill ta bort den virtuella dator som skapades i samband med flyttestet klickar du på **Rensa redundanstestning** i det replikerade objektet. I **Kommentarer** skriver du ned och sparar eventuella observationer från testet.

## <a name="perform-the-move-to-the-target-region-and-confirm"></a>Utför flytten till målregionen och bekräfta.

1. Navigera till valvet, i **Inställningar** > **replikerade objekt**, klicka på den virtuella datorn och klicka sedan på **Redundans**.
2. I **Redundans** väljer du **Senaste**. 
3. Välj **Stäng datorn innan du påbörjar redundans**. Site Recovery försöker stänga av den virtuella källdatorn innan redundansen utlöses. Redundansen fortsätter även om avstängningen misslyckas. Du kan följa förloppet för redundans på sidan **Jobb**. 
4. När jobbet har slutförts kan du kontrollera att den virtuella datorn visas i Azure-målregionen som förväntat.
5. I **Replikerade objekt** högerklickar du på den virtuella datorn > **Genomför**. Detta avslutar flytten till målregionen. Vänta tills incheckningsjobbet har slutförts.

## <a name="discard-the-resource-in-the-source-region"></a>Ta bort resursen från källregionen 

- Gå till den virtuella datorn.  Klicka på **Inaktivera replikering**.  Detta stoppar processen med att kopiera data för den virtuella datorn.  

   > [!IMPORTANT]
   > Det är viktigt att du utför det här steget så att du inte debiteras för ASR-replikering.

Fortsätt med nästa uppsättning steg om du inte har för avsikt att återanvända några av källresurserna.

1. Ta bort alla relevanta nätverksresurser från den källregion som ingick i steg 4 i [Förbereda de virtuella källdatorerna](#prepare-the-source-vms) 
2. Ta bort motsvarande lagringskonto från källregionen.



## <a name="next-steps"></a>Nästa steg

I den här självstudien flyttade du en virtuell Azure-dator till en annan Azure-region. Nu kan du konfigurera haveriberedskap för de flyttade virtuella datorerna.

> [!div class="nextstepaction"]
> [Konfigurera haveriberedskap efter migrering](azure-to-azure-quickstart.md)
