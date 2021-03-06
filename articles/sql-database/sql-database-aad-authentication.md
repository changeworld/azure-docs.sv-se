---
title: Azure Active Directory
description: Lär dig mer om hur du använder Azure Active Directory för autentisering med SQL Database, hanterad instans och Azure Synapse Analytics
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: azure-synapse
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto, carlrab
ms.date: 03/27/2020
ms.openlocfilehash: 58f40bc7406048df2b052bea799bcbf6466fbbae
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2020
ms.locfileid: "80421113"
---
# <a name="use-azure-active-directory-authentication-for-authentication-with-sql"></a>Använda Azure Active Directory-autentisering för autentisering med SQL

Azure Active Directory-autentisering är en mekanism för anslutning till Azure [SQL Database](sql-database-technical-overview.md), [hanterad instans](sql-database-managed-instance.md)och Azure [Synapse Analytics (tidigare Azure SQL Data Warehouse)](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) med hjälp av identiteter i Azure Active Directory (Azure AD). 

> [!NOTE]
> Den här artikeln gäller Azure SQL-server och både SQL Database och Azure Synapse. För enkelhetens skull används SQL Database när du refererar till både SQL Database och Azure Synapse.

Med Azure AD-autentisering kan du hantera identiteter för databasanvändare och andra Microsoft-tjänster på en enda central plats. Central ID-hantering ger en enda plats för hantering av databasanvändare och förenklar behörighetshanteringen. Här är några av fördelarna:

- Det är ett alternativ till SQL Server-autentisering
- Hjälper till att stoppa spridningen av användaridentiteter över databasservrar.
- Lösenord kan roteras på ett och samma ställe
- Kunderna kan hantera sin databasåtkomst via externa grupper (Azure AD)
- Du slipper lagra lösenord om du aktiverar integrerad Windows-autentisering eller andra former av autentisering som stöds av Azure Active Directory
- Med Azure AD-autentisering används oberoende databasanvändare till att autentisera identiteter på databasnivå
- Azure AD har stöd för tokenbaserad autentisering för appar som ansluter till SQL Database
- Azure AD-autentisering stöder:
  - Azure AD-molnidentiteter
  - Azure AD-hybrididentiteter som stöder:
    - Molnautentisering med två alternativ i kombination med sömlös enkel inloggning (SSO): **Direktautentisering** och **lösenordshönadautentisering**
    - Federerad autentisering
  - Mer information om Azure AD-autentiseringsmetoder och vilken du vill välja finns i följande artikel:
    - [Välj rätt autentiseringsmetod för azure Active Directory-hybrididentitetslösningen](../active-directory/hybrid/choose-ad-authn.md)
- Azure AD har stöd för anslutning från SQL Server Management Studio som använder Active Directory Universal Authentication, inklusive Multi-Factor Authentication (MFA).  I MFA används stark autentisering via en rad enkla verifieringsalternativ – telefonsamtal, SMS, smarta kort med PIN-kod eller avisering i mobilappen. Mer information finns i [SSMS-stöd för Azure AD MFA med SQL Database och Azure Synapse](sql-database-ssms-mfa-authentication.md)
- Azure AD har stöd för liknande anslutningar från SSDT (SQL Server Data Tools) som använder Active Directory Interactive Authentication. Mer information finns [i Azure Active Directory-stöd i SQL Server Data Tools (SSDT)](/sql/ssdt/azure-active-directory)

> [!NOTE]  
> Det går inte att ansluta till SQL Server som körs på en Virtuell Azure-dator med ett Azure Active Directory-konto. Använd ett Active Directory-konto i domänen i stället.  

Konfigurationsstegen innehåller följande procedurer för att konfigurera och använda Azure Active Directory-autentisering.

1. Skapa och fyll i Azure AD.
2. Valfritt: Associera eller ändra den active directory som för närvarande är associerad med din Azure-prenumeration.
3. Skapa en Azure Active Directory-administratör för Azure SQL Database-servern, den hanterade instansen eller [Azure Synapse](https://azure.microsoft.com/services/sql-data-warehouse/).
4. Konfigurera klientdatorerna.
5. Skapa inneslutna databasanvändare i databasen mappade till Azure AD-identiteter.
6. Anslut till databasen med hjälp av Azure AD-identiteter.

> [!NOTE]
> Mer information om hur du skapar och fyller i Azure AD och sedan konfigurerar Azure AD med Azure SQL Database, hanterad instans och Azure Synapse finns i [Konfigurera Azure AD med Azure SQL Database](sql-database-aad-authentication-configure.md).

## <a name="trust-architecture"></a>Förtroendearkitektur

- För att stödja Azure AD-inbyggt användarlösenord beaktas endast Cloud-delen och Azure AD/Azure SQL Database.
- Om du vill stödja Windows-autentiseringsuppgifter för enkel inloggning (eller användar-/lösenord för Windows-autentiseringsuppgifter) använder du Azure Active Directory-autentiseringsuppgifter från en federerad eller hanterad domän som är konfigurerad för sömlös enkel inloggning för direkt- och lösenordshh-autentisering. Mer information finns i [Azure Active Directory Seamless Single Sign-On](../active-directory/hybrid/how-to-connect-sso.md).
- För att stödja Federerad autentisering (eller användar/lösenord för Windows-autentiseringsuppgifter) krävs kommunikation med ADFS-block.

Mer information om Azure AD-hybrididentiteter, inställningar och synkronisering finns i följande artiklar:

- Autentisering av lösenord hash - [Implementera synkronisering av lösenordsh hash med Azure AD Connect-synkronisering](../active-directory/hybrid/how-to-connect-password-hash-synchronization.md)
- Direktautentisering - [Azure Active Directory Direktautentisering](../active-directory/hybrid/how-to-connect-pta-quick-start.md)
- Federerad autentisering – [distribuera Active Directory Federation Services i Azure](/windows-server/identity/ad-fs/deployment/how-to-connect-fed-azure-adfs) och Azure AD Connect och [federation](../active-directory/hybrid/how-to-connect-fed-whatis.md)

Ett exempel på federerad autentisering med ADFS-infrastruktur (eller användar-/lösenord för Windows-autentiseringsuppgifter) finns i diagrammet nedan. Pilarna visar kommunikationsvägar.

![aad auth diagram][1]

Följande diagram visar federations-, förtroende- och värdrelationer som gör att en klient kan ansluta till en databas genom att skicka en token. Token autentiseras av en Azure AD och är betrodd av databasen. Kund 1 kan representera en Azure Active Directory med inbyggda användare eller en Azure AD med federerade användare. Kund 2 representerar en möjlig lösning inklusive importerade användare. i det här exemplet kommer från en federerad Azure Active Directory med ADFS synkroniseras med Azure Active Directory. Det är viktigt att förstå att åtkomst till en databas med Azure AD-autentisering kräver att värdprenumerationen är associerad med Azure AD. Samma prenumeration måste användas för att skapa SQL Server som är värd för Azure SQL Database eller Azure Synapse.

![prenumerationsrelation][2]

## <a name="administrator-structure"></a>Administratörsstruktur

När du använder Azure AD-autentisering finns det två administratörskonton för SQL Database-servern och hanterad instans. den ursprungliga SQL Server-administratören och Azure AD-administratören. Samma begrepp gäller för Azure Synapse. Endast administratören baserat på ett Azure AD-konto kan skapa den första Azure AD-innehållna databasanvändaren i en användardatabas. Azure AD-administratörsinloggningen kan vara en Azure AD-användare eller en Azure AD-grupp. När administratören är ett gruppkonto kan det användas av valfri gruppmedlem, vilket aktiverar flera Azure AD-administratörer för SQL Server-instansen. Genom att använda gruppkontot som administratör kan du öka hanterbarheten genom att du kan lägga till och ta bort gruppmedlemmar centralt i Azure AD utan att ändra användare eller behörigheter i SQL Database. Endast en Azure AD-administratör (en användare eller grupp) kan konfigureras när som helst.

![administratörsstruktur][3]

## <a name="permissions"></a>Behörigheter

Om du vill skapa nya `ALTER ANY USER` användare måste du ha behörighet i databasen. Behörigheten `ALTER ANY USER` kan beviljas alla databasanvändare. `ALTER ANY USER` Behörigheten lagras också av serveradministratörskontona och `CONTROL ON DATABASE` `ALTER ON DATABASE` databasanvändare med databasens eller `db_owner` behörighet och av medlemmar i databasrollen.

Om du vill skapa en innehållen databasanvändare i Azure SQL Database, hanterad instans eller Azure Synapse måste du ansluta till databasen eller instansen med hjälp av en Azure AD-identitet. Om du vill skapa den första databasanvändaren måste du ansluta till databasen med hjälp av en Azure AD-administratör (som är ägare till databasen). Detta visas i [Konfigurera och hantera Azure Active Directory-autentisering med SQL Database eller Azure Synapse](sql-database-aad-authentication-configure.md). All Azure AD-autentisering är endast möjlig om Azure AD-administratören har skapats för Azure SQL Database eller Azure Synapse. Om Azure Active Directory-administratören har tagits bort från servern kan befintliga Azure Active Directory-användare som skapats tidigare i SQL Server inte längre ansluta till databasen med sina Azure Active Directory-autentiseringsuppgifter.

## <a name="azure-ad-features-and-limitations"></a>Azure AD-funktioner och begränsningar

- Följande medlemmar i Azure AD kan etableras i Azure SQL server eller Azure Synapse:

  - Inbyggda medlemmar: En medlem som skapats i Azure AD i den hanterade domänen eller i en kunddomän. Mer information finns i [Lägga till ditt eget domännamn i Azure AD](../active-directory/active-directory-domains-add-azure-portal.md).
  - Medlemmar i en Active Directory-domän som federerats med Azure Active Directory på en hanterad domän som konfigurerats för sömlös enkel inloggning med pass-through- eller lösenordshh-autentisering. Mer information finns i [Microsoft Azure stöder nu federation med Windows Server Active Directory](https://azure.microsoft.com/blog/windows-azure-now-supports-federation-with-windows-server-active-directory//) och Azure Active Directory Seamless Single [Sign-On](../active-directory/hybrid/how-to-connect-sso.md).
  - Importerade medlemmar från andra Azure AD:er som är inbyggda eller federerade domänmedlemmar.
  - Active Directory-grupper som skapats som säkerhetsgrupper.

- Azure AD-användare som ingår i `db_owner` en grupp som har serverroll kan inte använda syntaxen **[skapa databasscopen för autentiseringsuppgifter](/sql/t-sql/statements/create-database-scoped-credential-transact-sql)** mot Azure SQL Database och Azure Synapse. Följande fel visas:

    `SQL Error [2760] [S0001]: The specified schema name 'user@mydomain.com' either does not exist or you do not have permission to use it.`

    Bevilja `db_owner` rollen direkt till den enskilda Azure AD-användaren för att minska problemet **skapa databasscoped.**

- Dessa systemfunktioner returnerar NULL-värden när de körs under Azure AD-huvudnamn:

  - `SUSER_ID()`
  - `SUSER_NAME(<admin ID>)`
  - `SUSER_SNAME(<admin SID>)`
  - `SUSER_ID(<admin name>)`
  - `SUSER_SID(<admin name>)`

### <a name="managed-instances"></a>Hanterade instanser

- Azure AD-serverhuvudnamn (inloggningar) och användare stöds som en förhandsgranskningsfunktion för [hanterade instanser](sql-database-managed-instance.md).
- Det går inte att ange Azure AD-serverobjekt (inloggningar) som mappas till en Azure AD-grupp som databasägare i [hanterade instanser](sql-database-managed-instance.md).
    - Ett tillägg av detta är att när en `dbcreator` grupp läggs till som en del av serverrollen kan användare från den här gruppen ansluta till den hanterade instansen och skapa nya databaser, men kommer inte att kunna komma åt databasen. Detta beror på att den nya databasägaren är SA och inte Azure AD-användaren. Det här problemet visas inte om den `dbcreator` enskilda användaren läggs till i serverrollen.
- SQL Agent-hantering och jobbkörning stöds för Azure AD-serverhuvudnamn (inloggningar).
- Åtgärder för databassäkerhetskopiering och -återställning kan köras av Azure AD-serverhuvudkonton (inloggningar).
- Granskning av alla program som är relaterade till Azure AD-serverhuvudnamn (inloggningar) och autentiseringshändelser stöds.
- Dedikerad administratörsanslutning för Azure AD-serverhuvudnamn (inloggningar) som är medlemmar i sysadmin-serverrollen stöds.
    - Stöds via SQLCMD Utility och SQL Server Management Studio.
- Inloggningsutlösare stöds för inloggningshändelser som kommer från Azure AD-serverhuvudkonton (inloggningar).
- Service Broker och DB-e-post kan konfigureras med hjälp av ett huvudnamn för Azure AD-servern (inloggning).


## <a name="connecting-using-azure-ad-identities"></a>Ansluta med Azure AD-identiteter

Azure Active Directory-autentisering stöder följande metoder för att ansluta till en databas med Hjälp av Azure AD-identiteter:

- Azure Active Directory-lösenord
- Integrerad i Azure Active Directory
- Azure Active Directory Universal med MFA
- Använda autentisering av programtoken

Följande autentiseringsmetoder stöds för Azure AD-serverhuvudnamn (inloggningar):

- Azure Active Directory-lösenord
- Integrerad i Azure Active Directory
- Azure Active Directory Universal med MFA


### <a name="additional-considerations"></a>Annat som är bra att tänka på

- För att förbättra hanterbarheten rekommenderar vi att du etablerar en dedikerad Azure AD-grupp som administratör.   
- Endast en Azure AD-administratör (en användare eller grupp) kan konfigureras för en Azure SQL Database-server eller Azure Synapse när som helst.
  - Tillägget av Azure AD-serverhuvudnamn (inloggningar) för hanterade instanser gör det möjligt att skapa flera Azure `sysadmin` AD-serverobjekt (inloggningar) som kan läggas till i rollen.
- Endast en Azure AD-administratör för SQL Server kan inledningsvis ansluta till Azure SQL Database-servern, hanterad instans eller Azure Synapse med ett Azure Active Directory-konto. Active Directory-administratören kan konfigurera efterföljande Azure AD-databasanvändare.   
- Vi rekommenderar att du ställer in tidsgränsen för anslutningen till 30 sekunder.   
- SQL Server 2016 Management Studio och SQL Server Data Tools för Visual Studio 2015 (version 14.0.60311.1April 2016 eller senare) stöder Azure Active Directory-autentisering. (Azure AD-autentisering stöds av **.NET Framework Data Provider för SqlServer**; åtminstone version .NET Framework 4.6). Därför de senaste versionerna av dessa verktyg och data-tier applikationer (DAC och . BACPAC) kan använda Azure AD-autentisering.   
- Från och med version 15.0.1 stöder [sqlcmd-verktyget](/sql/tools/sqlcmd-utility) och [bcp-verktyget](/sql/tools/bcp-utility) Active Directory Interactive-autentisering med MFA.
- SQL Server Data Tools för Visual Studio 2015 kräver minst april 2016-versionen av dataverktygen (version 14.0.60311.1). Azure AD-användare visas inte i SSDT Object Explorer. Som en lösning visar du användarna i [sys.database_principals](https://msdn.microsoft.com/library/ms187328.aspx).   
- [Microsoft JDBC Driver 6.0 för SQL Server](https://www.microsoft.com/download/details.aspx?id=11774) stöder Azure AD-autentisering. Se också [ange anslutningsegenskaper](https://msdn.microsoft.com/library/ms378988.aspx).   
- PolyBase kan inte autentisera med hjälp av Azure AD-autentisering.   
- Azure AD-autentisering stöds för SQL Database av Azure Portal **Import Database** och **Export Database** blades. Import och export med Azure AD-autentisering stöds också från PowerShell-kommandot.   
- Azure AD authentication is supported for SQL Database, managed instance, and Azure Synapse with using CLI. Mer information finns i [Konfigurera och hantera Azure Active Directory-autentisering med SQL Database eller Azure Synapse](sql-database-aad-authentication-configure.md) och SQL Server - az sql [server](https://docs.microsoft.com/cli/azure/sql/server).

## <a name="next-steps"></a>Nästa steg

- Mer information om hur du skapar och fyller i Azure AD och sedan konfigurerar Azure AD med Azure SQL Database eller Azure Synapse finns i [Konfigurera och hantera Azure Active Directory-autentisering med SQL Database, hanterad instans eller Azure Synapse](sql-database-aad-authentication-configure.md).
- En självstudiekurs för att använda Azure AD-serverhuvudnamn (inloggningar) med hanterade instanser finns i [Azure AD-serverhuvudnamn (inloggningar) med hanterade instanser](sql-database-managed-instance-aad-security-tutorial.md)
- En översikt över inloggningar, användare, databasroller och behörigheter i SQL Database finns i [Inloggningar, användare, databasroller och behörigheter](sql-database-manage-logins.md).
- Mer information om huvudkonton finns i [Huvudkonton](https://msdn.microsoft.com/library/ms181127.aspx).
- Mer information om databasroller finns [Databasroller](https://msdn.microsoft.com/library/ms189121.aspx).
- Syntax för hur du skapar Azure AD-serverhuvudnamn (inloggningar) för hanterade instanser finns i [SKAPA INLOGGNING](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current).
- Mer information om brandväggsregler i SQL Database finns [SQL Database-brandväggsregler](sql-database-firewall-configure.md).

<!--Image references-->

[1]: ./media/sql-database-aad-authentication/1aad-auth-diagram.png
[2]: ./media/sql-database-aad-authentication/2subscription-relationship.png
[3]: ./media/sql-database-aad-authentication/3admin-structure.png
[4]: ./media/sql-database-aad-authentication/4select-subscription.png
[5]: ./media/sql-database-aad-authentication/5ad-settings-portal.png
[6]: ./media/sql-database-aad-authentication/6edit-directory-select.png
[7]: ./media/sql-database-aad-authentication/7edit-directory-confirm.png
[8]: ./media/sql-database-aad-authentication/8choose-ad.png
[9]: ./media/sql-database-aad-authentication/9ad-settings.png
[10]: ./media/sql-database-aad-authentication/10choose-admin.png
[11]: ./media/sql-database-aad-authentication/11connect-using-int-auth.png
[12]: ./media/sql-database-aad-authentication/12connect-using-pw-auth.png
[13]: ./media/sql-database-aad-authentication/13connect-to-db.png
