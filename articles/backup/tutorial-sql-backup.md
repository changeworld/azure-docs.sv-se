---
title: Självstudiekurs - Säkerhetskopiera SQL Server-databaser till Azure
description: I den här självstudien kan du läsa om hur du säkerhetskopierar en SQL Server-databas som körs på en Azure VM-dator till ett Azure Backup Recovery Services-valv.
ms.topic: tutorial
ms.date: 06/18/2019
ms.openlocfilehash: 3216404b04647e2fb5f15beb09b9a4ccd6648bba
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "74171891"
---
# <a name="back-up-a-sql-server-database-in-an-azure-vm"></a>Säkerhetskopiera en SQL Server-databas i en virtuell Azure-dator

Den här självstudien visar hur du säkerhetskopierar en SQL Server-databas som körs på en Virtuell Azure-dator till ett Azure Backup Recovery Services-valv. I den här artikeln kan du se hur du:

> [!div class="checklist"]
>
> * Skapa och konfigurera ett valv.
> * Identifiera databaser och konfigurera säkerhetskopieringar.
> * Konfigurera automatiskt skydd för databaser.
> * Kör en säkerhetskopiering på begäran.

## <a name="prerequisites"></a>Krav

Kontrollera följande villkor innan du säkerhetskopierar SQL Server-databasen:

1. Identifiera eller [skapa](backup-sql-server-database-azure-vms.md#create-a-recovery-services-vault) ett Recovery Services-valv i samma region eller språk som den virtuella datorn som är värd för SQL Server-instansen.
2. [Kontrollera de behörigheter för virtuella datorer](backup-azure-sql-database.md#set-vm-permissions) som behövs för att säkerhetskopiera SQL-databaserna.
3. Kontrollera att den virtuella datorn har [nätverksanslutning](backup-sql-server-database-azure-vms.md#establish-network-connectivity).
4. Kontrollera att SQL Server-databaserna namnges enligt [riktlinjerna för namngivning](#verify-database-naming-guidelines-for-azure-backup) för Azure Backup.
5. Kontrollera att du inte har några andra lösningar för säkerhetskopiering aktiverade för databasen. Inaktivera alla andra SQL Server-säkerhetskopieringar innan du installerar det här scenariot. Du kan aktivera Azure Backup för en virtuell Azure-dator tillsammans med Azure Backup för en SQL Server-databas som körs på den virtuella datorn utan konflikter.

### <a name="establish-network-connectivity"></a>Etablera nätverksanslutning

Den virtuella SQL Server-datorn behöver ha anslutning till offentliga Azure-IP-adresser för alla åtgärder. VM-åtgärder (databasidentifiering, konfigurera säkerhetskopior, schemalägga säkerhetskopior, återställa återställningspunkter och så vidare) misslyckas utan anslutning till de offentliga IP-adresserna. Upprätta en anslutning med något av följande alternativ:

* **Tillåt AZURE datacenter IP-intervall:** Tillåt [IP-intervall](https://www.microsoft.com/download/details.aspx?id=41653) i hämtningen. Använd cmdleten **Set-AzureNetworkSecurityRule** för att komma åt nätverkssäkerhetsgruppen (NSG).
* **Distribuera en HTTP-proxyserver för att dirigera trafik:** När du säkerhetskopierar en SQL Server-databas på en Virtuell Azure använder tillägget för säkerhetskopiering på den virtuella datorn HTTPS API:erna för att skicka hanteringskommandon till Azure Backup och data till Azure Storage. Säkerhetskopieringstillägget använder också Azure Active Directory (Azure AD) för autentisering. Dirigera trafiken för säkerhetskopieringstillägget för dessa tre tjänster via HTTP-proxyn. Tilläggen är den enda komponent som är konfigurerad för åtkomst till det offentliga internet.

Varje alternativ har fördelar och nackdelar

**Alternativet** | **Fördelar** | **Nackdelar**
--- | --- | ---
Tillåta IP-intervall | Inga ytterligare kostnader. | Komplicerat att hantera eftersom IP-adressintervallen ändras över tid. <br/><br/> Ger åtkomst till hela Azure, inte bara Azure Storage.
Använda en HTTP-proxy   | Detaljerad kontroll i proxyn över lagrings-URL:er tillåts. <br/><br/> Enskild punkt för Internetåtkomst till virtuella datorer. <br/><br/> Inte föremål för Azure-IP-adressändringar. | Ytterligare kostnader för att köra en virtuell dator med proxyprogramvaran.

### <a name="set-vm-permissions"></a>Ange VM-behörigheter

Azure Backup gör ett antal saker när du konfigurerar säkerhetskopiering för en SQL Server-databas:

* Lägger till tillägget **AzureBackupWindowsWorkload**.
* För att identifiera databaser på den virtuella datorn skapar Azure Backup kontot **NT SERVICE\AzureWLBackupPluginSvc**. Det här kontot används för säkerhetskopiering och återställning och kräver SQL-sysadmin-behörighet.
* Azure Backup utnyttjar kontot **NT AUTHORITY\SYSTEM** för databasidentifiering/-förfrågan. Därför behöver det här kontot vara en offentlig inloggning på SQL.

Om du inte skapade SQL Server-datorn från Azure Marketplace kan det hända att du får felet **UserErrorSQLNoSysadminMembership**. Om det inträffar [följer du de här instruktionerna](backup-azure-sql-database.md#set-vm-permissions).

### <a name="verify-database-naming-guidelines-for-azure-backup"></a>Kontrollera riktlinjerna för namngivning av databaser för Azure Backup

Undvik följande för databasnamn:

* Avslutande/inledande blanksteg
* Avslutande ”!”
* Avslutande hakparentes ”]”
* Databasnamn som börjar med 'F:\'

Vi har alias för tecken som inte stöds i Azure-tabellen, men vi rekommenderar att du undviker dem. [Läs mer](https://docs.microsoft.com/rest/api/storageservices/Understanding-the-Table-Service-Data-Model?redirectedfrom=MSDN).

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-sql-server-databases"></a>Identifiera SQL Server-databaser

Identifiera databaser som körs på den virtuella datorn.

1. I [Azure-portalen](https://portal.azure.com) öppnar du det Recovery Services-valv som du använder för att säkerhetskopiera databasen.

2. På instrumentpanelen för **Recovery Services-valvet** väljer du **Säkerhetskopiering**.

   ![Välj Säkerhetskopiering för att öppna menyn Säkerhetskopieringsmål](./media/backup-azure-sql-database/open-backup-menu.png)

3. I **Säkerhetskopieringsmål** anger du **Var körs din arbetsbelastning** till **Azure** (standardinställningen).

4. I **Vad vill du säkerhetskopiera** väljer du **SQL Server på Azure VM**.

    ![Välj SQL Server på Azure VM för säkerhetskopieringen](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

5. I **Säkerhetskopieringsmål** > **Upptäck DBs i virtuella datorer**väljer du Starta **identifiering** för att söka efter oskyddade virtuella datorer i prenumerationen. Det kan ta ett tag beroende på antalet oskyddade virtuella datorer i prenumerationen.

   * Oskyddade virtuella datorer bör visas i listan efter identifiering, sorterade efter namn och resursgrupp.
   * Om en virtuell dator inte visas som förväntat kontrollerar du om den redan har säkerhetskopierats i ett valv.
   * Flera virtuella datorer kan ha samma namn, men de tillhör olika resursgrupper.

     ![Säkerhetskopieringen väntar under sökningen efter databaser på virtuella datorer](./media/backup-azure-sql-database/discovering-sql-databases.png)

6. I listan över virtuella datorer väljer du den virtuella dator som kör SQL Server-databasen > **Identifiera databaser**.

7. Spåra databasidentifiering i området **Meddelanden**. Det kan ta en stund innan jobbet är klart, beroende på hur många databaser som finns på den virtuella datorn. När de valda databaserna identifieras visas ett meddelande.

    ![Meddelande som anger att distributionen lyckades](./media/backup-azure-sql-database/notifications-db-discovered.png)

8. Azure Backup identifierar alla SQL Server-databaser på den virtuella datorn. Under identifieringen sker följande i bakgrunden:

    * Azure Backup registrerar den virtuella datorn med valvet för säkerhetskopiering av arbetsbelastning. Alla databaser på den registrerade virtuella datorn kan endast säkerhetskopieras till det här valvet.
    * Azure Backup installerar tillägget **AzureBackupWindowsWorkload** på den virtuella datorn. Det installeras inte någon agent på SQL-databasen.
    * Azure Backup skapar tjänstkontot **NT Service\AzureWLBackupPluginSvc** på den virtuella datorn.
      * Alla åtgärder för säkerhetskopiering och återställning använder tjänstkontot.
      * **NT Service\AzureWLBackupPluginSvc** behöver SQL-sysadmin-behörigheter. Alla virtuella SQL Server-datorer som skapas i Azure Marketplace har **SqlIaaSExtension** installerat. Tillägget **AzureBackupWindowsWorkload** använder **SQLIaaSExtension** för att automatiskt hämta de behörigheter som krävs.
    * Om du inte skapade den virtuella datorn från Marketplace har den inte **SqlIaaSExtension** installerat, och identifieringsåtgärden misslyckas med felmeddelandet **UserErrorSQLNoSysAdminMembership**. Följ [instruktionerna](backup-azure-sql-database.md#set-vm-permissions) för att åtgärda problemet.

        ![Välj den virtuella datorn och databasen](./media/backup-azure-sql-database/registration-errors.png)

## <a name="configure-backup"></a>Konfigurera säkerhetskopiering  

Konfigurera säkerhetskopiering på följande sätt:

1. I **Säkerhetskopieringsmål**väljer du **Konfigurera säkerhetskopiering**.

   ![Välj Konfigurera säkerhetskopiering](./media/backup-azure-sql-database/backup-goal-configure-backup.png)

2. Klicka på **Konfigurera säkerhetskopiering**visas **bladet Välj objekt till säkerhetskopiering.** Detta visar alla registrerade tillgänglighetsgrupper och fristående SQL-servrar. Expandera sparren till vänster om raden om du vill visa alla oskyddade databaser i den instansen eller Alltid på AG.  

    ![Visa alla SQL Server-instanser med fristående databaser](./media/backup-azure-sql-database/list-of-sql-databases.png)

3. Välj alla de databaser som du vill skydda > **OK**.

   ![Skydda databasen](./media/backup-azure-sql-database/select-database-to-protect.png)

   För att optimera säkerhetskopieringsbelastningar anger Azure Backup ett maximalt antal databaser i ett säkerhetskopieringsjobb till 50.

     * Alternativt kan du aktivera automatiskt skydd på hela instansen eller AlwaysOn-tillgänglighetsgruppen genom att välja alternativet **PÅ** i motsvarande listruta i kolumnen **AUTOPROTECT** (Automatiskt skydd). Funktionen automatiskt skydd aktiverar inte bara skydd på alla befintliga databaser i ett svep, utan skyddar även automatiskt alla nya nya databaser som läggs till i den instansen eller tillgänglighetsgruppen i framtiden.  

4. Öppna **principbladet Säkerhetskopiering** genom att klicka på **OK.**

    ![Aktivera automatiskt skydd i AlwaysOn-tillgänglighetsgruppen](./media/backup-azure-sql-database/enable-auto-protection.png)

5. I **Välj säkerhetskopieringspolicy** väljer du en policy och klickar på **OK**.

   * Välj standardprincip: HourlyLogBackup.
   * Välj en befintlig säkerhetskopieringspolicy som har skapats för SQL.
   * Definiera en ny policy baserat på ditt RPO och kvarhållningsintervall.

     ![Välja säkerhetskopieringspolicy](./media/backup-azure-sql-database/select-backup-policy.png)

6. På menyn **Säkerhetskopiering** väljer du **Aktivera säkerhetskopiering**.

    ![Aktivera den valda säkerhetskopieringspolicyn](./media/backup-azure-sql-database/enable-backup-button.png)

7. Spåra konfigurationsförloppet i **meddelandefältet** på portalen.

    ![Meddelandefältet](./media/backup-azure-sql-database/notifications-area.png)

### <a name="create-a-backup-policy"></a>Skapa en säkerhetskopieringspolicy

En säkerhetskopieringspolicy definierar när säkerhetskopior skapas och hur länge de behålls.

* En policy skapas på valvnivå.
* Flera valv kan använda samma säkerhetskopieringspolicy, men du måste tillämpa säkerhetskopieringspolicyn på varje valv.
* När du skapar en säkerhetskopieringspolicy används en daglig fullständig säkerhetskopia som standard.
* Du kan lägga till en differentiell säkerhetskopia, men endast om du konfigurerar så att fullständiga säkerhetskopior utförs varje vecka.
* [Lär dig mer om](backup-architecture.md#sql-server-backup-types) olika typer av säkerhetskopieringspolicyer.

Så här skapar du en säkerhetskopieringspolicy:

1. Klicka på **Säkerhetskopiera principer** > **Lägg till**i valvet.
2. På **Lägg till-menyn** klickar du på **SQL Server i Azure VM** för att definiera principtypen.

   ![Välj en policytyp för den nya säkerhetskopieringspolicyn](./media/backup-azure-sql-database/policy-type-details.png)

3. I **Policynamn** anger du ett namn för den nya policyn.
4. I **Policy för fullständig säkerhetskopia** väljer du en **Säkerhetskopieringsfrekvens** och väljer **Dagligen** eller **Varje vecka**.

   * För **Dagligen** väljer du den timme och den tidszon då säkerhetskopieringsjobbet börjar.
   * Du måste köra en fullständig säkerhetskopia eftersom du inte kan stänga av alternativet **Fullständig säkerhetskopiering.**
   * Klicka på **Fullständig säkerhetskopia** för att visa policyn.
   * Du kan inte skapa differentiella säkerhetskopior för dagliga fullständiga säkerhetskopior.
   * För **Varje vecka** väljer du den veckodag, timme och tidszon då säkerhetskopieringsjobbet börjar.

     ![Nya fält för säkerhetskopieringspolicy](./media/backup-azure-sql-database/full-backup-policy.png)  

5. För **Kvarhållningsintervall** är alla alternativ markerade som standard. Avmarkera eventuella gränser för kvarhållningsintervall som du inte vill använda, och ange intervall som ska användas.

    * Minsta kvarhållningsperiod för alla typer av säkerhetskopiering (full/differential/log) är sju dagar.
    * Återställningspunkter taggas för kvarhållning baserat på deras kvarhållningsintervall. Om du till exempel väljer en daglig fullständig säkerhetskopia utlöses endast en fullständig säkerhetskopia varje dag.
    * Säkerhetskopieringen för en viss dag taggas och behålls baserat på det veckovisa kvarhållningsintervallet och inställningen för veckovis kvarhållning.
    * De månatliga och årliga kvarhållningsintervallen fungerar på liknande sätt.

   ![Inställningar för kvarhållningsintervall](./media/backup-azure-sql-database/retention-range-interval.png)

6. På menyn **Policy för fullständig säkerhetskopia** väljer du **OK** för att acceptera inställningarna.
7. Om du vill lägga till en policy för differentiell säkerhetskopia väljer du **Differentiell säkerhetskopia**.

   ![Inställningar för kvarhållningsintervall](./media/backup-azure-sql-database/retention-range-interval.png)
   ![Öppna menyn för policy för differentiell säkerhetskopia](./media/backup-azure-sql-database/backup-policy-menu-choices.png)

8. I **Policy för differentiell säkerhetskopia** väljer du **Aktivera** för att öppna kontrollerna för frekvens och kvarhållning.

    * Du kan endast utlösa en differentiell säkerhetskopia per dag.
    * Differentiella säkerhetskopior kan behållas i upp till 180 dagar. Om du behöver längre kvarhållning måste du använda fullständiga säkerhetskopior.

9. Välj **OK** för att spara policyn och återgå till huvudmenyn **Säkerhetskopieringspolicy**.

10. Om du vill lägga till en policy för loggsäkerhetskopia väljer du **Loggsäkerhetskopia**.
11. I **Loggsäkerhetskopia** väljer du **Aktivera** och anger kontrollerna för frekvens och kvarhållning. Loggsäkerhetskopior kan skapas så ofta som var 15:e minut och kan behållas i upp till 35 dagar.
12. Välj **OK** för att spara policyn och återgå till huvudmenyn **Säkerhetskopieringspolicy**.

    ![Redigera policyn för loggsäkerhetskopia](./media/backup-azure-sql-database/log-backup-policy-editor.png)

13. På menyn **Säkerhetskopieringspolicy** väljer du om du vill aktivera **komprimering av SQL-säkerhetskopiering**.
    * Komprimering är inaktiverat som standard.
    * På serverdelen använder Azure Backup SQL-specifik säkerhetskopieringskomprimering.

14. När du har slutfört redigeringarna i säkerhetskopieringspolicyn väljer du **OK**.

## <a name="run-an-on-demand-backup"></a>Kör en säkerhetskopiering på begäran

1. Välj Säkerhetskopieringsobjekt i valvet för Återställningstjänster.
2. Klicka på "SQL i Azure VM".
3. Högerklicka på en databas och välj "Backup now".
4. Välj säkerhetskopieringstyp (endast fullständig/differentiell/log/kopiera endast fullständig) och komprimering (Aktivera/inaktivera)
5. Välj OK för att starta säkerhetskopian.
6. Övervaka säkerhetskopieringsjobbet genom att gå till ditt Recovery Services-valv och välja "Säkerhetskopieringsjobb".

## <a name="next-steps"></a>Nästa steg

I den här självstudien använde du Azure Portal för att:

> [!div class="checklist"]
>
> * Skapa och konfigurera ett valv.
> * Identifiera databaser och konfigurera säkerhetskopieringar.
> * Konfigurera automatiskt skydd för databaser.
> * Kör en säkerhetskopiering på begäran.

Fortsätta till nästa kurs för att återställa en virtuell Azure-dator från disken.

> [!div class="nextstepaction"]
> [Återställa SQL Server-databaser på virtuella Azure-datorer](./restore-sql-database-azure-vm.md)
