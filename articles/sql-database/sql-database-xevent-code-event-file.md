---
title: XEvent-händelsefilkod
description: Tillhandahåller PowerShell och Transact-SQL för ett kodexempel i två faser som demonstrerar händelsefilsmålet i en utökad händelse i Azure SQL Database. Azure Storage är en obligatorisk del av det här scenariot.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: PowerShell
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: jrasnik
ms.date: 03/12/2019
ms.openlocfilehash: a9bf28fb1b3c5278d25b417fc646d2ad3d6f1abc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79213982"
---
# <a name="event-file-target-code-for-extended-events-in-sql-database"></a>Målkod för händelsefil för utökade händelser i SQL Database

[!INCLUDE [sql-database-xevents-selectors-1-include](../../includes/sql-database-xevents-selectors-1-include.md)]

Du vill ha ett fullständigt kodexempel för ett robust sätt att samla in och rapportera information för en utökad händelse.

I Microsoft SQL Server används [eventfilsmålet](https://msdn.microsoft.com/library/ff878115.aspx) för att lagra händelseutdata i en lokal hårddiskfil. Men sådana filer är inte tillgängliga för Azure SQL Database. I stället använder vi Azure Storage-tjänsten för att stödja eventfilmålet.

Det här avsnittet innehåller ett kodexempel i två faser:

- PowerShell, för att skapa en Azure Storage-behållare i molnet.
- Transact-SQL:
  
  - Så här tilldelar du Azure Storage-behållaren till ett eventfilsmål.
  - Så här skapar och startar du händelsesessionen och så vidare.

## <a name="prerequisites"></a>Krav

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

> [!IMPORTANT]
> PowerShell Azure Resource Manager-modulen stöds fortfarande av Azure SQL Database, men all framtida utveckling är för Az.Sql-modulen. För dessa cmdlets finns i [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenten för kommandona i Az-modulen och i AzureRm-modulerna är i stort sett identiska.

- Ett Azure-konto och prenumeration. Registrera dig för en [kostnadsfri utvärderingsversion](https://azure.microsoft.com/pricing/free-trial/).
- Alla databaser som du kan skapa en tabell i.
  
  - Du kan också [skapa en **AdventureWorksLT-demonstrationsdatabas** ](sql-database-get-started.md) på några minuter.

- SQL Server Management Studio (ssms.exe), helst sin senaste månatliga uppdateringsversion.
  Du kan hämta den senaste ssms.exe från:
  
  - Ämne med titeln [Hämta SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
  - [En direkt länk till nedladdningen.](https://go.microsoft.com/fwlink/?linkid=616025)

- Du måste ha [Azure PowerShell-modulerna](https://go.microsoft.com/?linkid=9811175) installerade.

  - Modulerna innehåller kommandon som - **New-AzStorageAccount**.

## <a name="phase-1-powershell-code-for-azure-storage-container"></a>Fas 1: PowerShell-kod för Azure Storage-behållare

Detta PowerShell är fas 1 i kodexemplet i två faser.

Skriptet börjar med kommandon för att rensa efter en eventuell tidigare körning och kan köras om.

1. Klistra in PowerShell-skriptet i en enkel textredigerare som Notepad.exe och spara skriptet som en fil med tillägget **.ps1**.
2. Starta PowerShell ISE som administratör.
3. Vid prompten skriver du<br/>`Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser`<br/>och tryck sedan på Retur.
4. Öppna **PS1-filen** i PowerShell ISE. Kör skriptet.
5. Skriptet startar först ett nytt fönster där du loggar in på Azure.

   - Om du kör skriptet igen utan att störa sessionen har du det praktiska alternativet att kommentera kommandot **Add-AzureAccount.**

![PowerShell ISE, med Azure-modul installerad, redo att köra skript.][30_powershell_ise]

### <a name="powershell-code"></a>PowerShell-kod

Detta PowerShell-skript förutsätter att du redan har installerat Az-modulen. Mer information finns [i Installera Azure PowerShell-modulen](/powershell/azure/install-Az-ps).

```powershell
## TODO: Before running, find all 'TODO' and make each edit!!

cls;

#--------------- 1 -----------------------

'Script assumes you have already logged your PowerShell session into Azure.
But if not, run  Connect-AzAccount (or  Connect-AzAccount), just one time.';
#Connect-AzAccount;   # Same as  Connect-AzAccount.

#-------------- 2 ------------------------

'
TODO: Edit the values assigned to these variables, especially the first few!
';

# Ensure the current date is between
# the Expiry and Start time values that you edit here.

$subscriptionName    = 'YOUR_SUBSCRIPTION_NAME';
$resourceGroupName   = 'YOUR_RESOURCE-GROUP-NAME';

$policySasExpiryTime = '2018-08-28T23:44:56Z';
$policySasStartTime  = '2017-10-01';

$storageAccountLocation = 'YOUR_STORAGE_ACCOUNT_LOCATION';
$storageAccountName     = 'YOUR_STORAGE_ACCOUNT_NAME';
$contextName            = 'YOUR_CONTEXT_NAME';
$containerName          = 'YOUR_CONTAINER_NAME';
$policySasToken         = ' ? ';

$policySasPermission = 'rwl';  # Leave this value alone, as 'rwl'.

#--------------- 3 -----------------------

# The ending display lists your Azure subscriptions.
# One should match the $subscriptionName value you assigned
#   earlier in this PowerShell script. 

'Choose an existing subscription for the current PowerShell environment.';

Select-AzSubscription -Subscription $subscriptionName;

#-------------- 4 ------------------------

'
Clean up the old Azure Storage Account after any previous run, 
before continuing this new run.';

if ($storageAccountName) {
    Remove-AzStorageAccount `
        -Name              $storageAccountName `
        -ResourceGroupName $resourceGroupName;
}

#--------------- 5 -----------------------

[System.DateTime]::Now.ToString();

'
Create a storage account. 
This might take several minutes, will beep when ready.
  ...PLEASE WAIT...';

New-AzStorageAccount `
    -Name              $storageAccountName `
    -Location          $storageAccountLocation `
    -ResourceGroupName $resourceGroupName `
    -SkuName           'Standard_LRS';

[System.DateTime]::Now.ToString();
[System.Media.SystemSounds]::Beep.Play();

'
Get the access key for your storage account.
';

$accessKey_ForStorageAccount = `
    (Get-AzStorageAccountKey `
        -Name              $storageAccountName `
        -ResourceGroupName $resourceGroupName
        ).Value[0];

"`$accessKey_ForStorageAccount = $accessKey_ForStorageAccount";

'Azure Storage Account cmdlet completed.
Remainder of PowerShell .ps1 script continues.
';

#--------------- 6 -----------------------

# The context will be needed to create a container within the storage account.

'Create a context object from the storage account and its primary access key.
';

$context = New-AzStorageContext `
    -StorageAccountName $storageAccountName `
    -StorageAccountKey  $accessKey_ForStorageAccount;

'Create a container within the storage account.
';

$containerObjectInStorageAccount = New-AzStorageContainer `
    -Name    $containerName `
    -Context $context;

'Create a security policy to be applied to the SAS token.
';

New-AzStorageContainerStoredAccessPolicy `
    -Container  $containerName `
    -Context    $context `
    -Policy     $policySasToken `
    -Permission $policySasPermission `
    -ExpiryTime $policySasExpiryTime `
    -StartTime  $policySasStartTime;

'
Generate a SAS token for the container.
';
try {
    $sasTokenWithPolicy = New-AzStorageContainerSASToken `
        -Name    $containerName `
        -Context $context `
        -Policy  $policySasToken;
}
catch {
    $Error[0].Exception.ToString();
}

#-------------- 7 ------------------------

'Display the values that YOU must edit into the Transact-SQL script next!:
';

"storageAccountName: $storageAccountName";
"containerName:      $containerName";
"sasTokenWithPolicy: $sasTokenWithPolicy";

'
REMINDER: sasTokenWithPolicy here might start with "?" character, which you must exclude from Transact-SQL.
';

'
(Later, return here to delete your Azure Storage account. See the preceding  Remove-AzStorageAccount -Name $storageAccountName)';

'
Now shift to the Transact-SQL portion of the two-part code sample!';

# EOFile
```

Notera de få namngivna värden som PowerShell-skriptet skriver ut när det slutar. Du måste redigera dessa värden i Transact-SQL-skriptet som följer som fas 2.

## <a name="phase-2-transact-sql-code-that-uses-azure-storage-container"></a>Fas 2: Transact-SQL-kod som använder Azure Storage-behållare

- I fas 1 i det här kodexemplet körde du ett PowerShell-skript för att skapa en Azure Storage-behållare.
- Nästa i fas 2 måste följande Transact-SQL-skript använda behållaren.

Skriptet börjar med kommandon för att rensa efter en eventuell tidigare körning och kan köras om.

PowerShell-skriptet skrev ut några namngivna värden när det avslutades. Du måste redigera skriptet Transact-SQL för att kunna använda dessa värden. Leta reda på **TODO** i skriptet Transact-SQL för att hitta redigeringspunkterna.

1. Öppna SQL Server Management Studio (ssms.exe).
2. Anslut till din Azure SQL Database-databas.
3. Öppna ett nytt frågefönster genom att klicka här.
4. Klistra in följande Transact-SQL-skript i frågefönstret.
5. Hitta alla **TODO** i skriptet och gör lämpliga ändringar.
6. Spara och kör sedan skriptet.

> [!WARNING]
> Sas-nyckelvärdet som genereras av det föregående PowerShell-skriptet kan börja med ett '?' (frågetecken). När du använder SAS-tangenten i följande T-SQL-skript måste du *ta bort den inledande '?'*. Annars kan dina ansträngningar blockeras av säkerhet.

### <a name="transact-sql-code"></a>Transact-SQL-kod

```sql
---- TODO: First, run the earlier PowerShell portion of this two-part code sample.
---- TODO: Second, find every 'TODO' in this Transact-SQL file, and edit each.

---- Transact-SQL code for Event File target on Azure SQL Database.

SET NOCOUNT ON;
GO

----  Step 1.  Establish one little table, and  ---------
----  insert one row of data.

IF EXISTS
    (SELECT * FROM sys.objects
        WHERE type = 'U' and name = 'gmTabEmployee')
BEGIN
    DROP TABLE gmTabEmployee;
END
GO

CREATE TABLE gmTabEmployee
(
    EmployeeGuid         uniqueIdentifier   not null  default newid()  primary key,
    EmployeeId           int                not null  identity(1,1),
    EmployeeKudosCount   int                not null  default 0,
    EmployeeDescr        nvarchar(256)          null
);
GO

INSERT INTO gmTabEmployee ( EmployeeDescr )
    VALUES ( 'Jane Doe' );
GO

------  Step 2.  Create key, and  ------------
------  Create credential (your Azure Storage container must already exist).

IF NOT EXISTS
    (SELECT * FROM sys.symmetric_keys
        WHERE symmetric_key_id = 101)
BEGIN
    CREATE MASTER KEY ENCRYPTION BY PASSWORD = '0C34C960-6621-4682-A123-C7EA08E3FC46' -- Or any newid().
END
GO

IF EXISTS
    (SELECT * FROM sys.database_scoped_credentials
        -- TODO: Assign AzureStorageAccount name, and the associated Container name.
        WHERE name = 'https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent')
BEGIN
    DROP DATABASE SCOPED CREDENTIAL
        -- TODO: Assign AzureStorageAccount name, and the associated Container name.
        [https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent] ;
END
GO

CREATE
    DATABASE SCOPED
    CREDENTIAL
        -- use '.blob.',   and not '.queue.' or '.table.' etc.
        -- TODO: Assign AzureStorageAccount name, and the associated Container name.
        [https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent]
    WITH
        IDENTITY = 'SHARED ACCESS SIGNATURE',  -- "SAS" token.
        -- TODO: Paste in the long SasToken string here for Secret, but exclude any leading '?'.
        SECRET = 'sv=2014-02-14&sr=c&si=gmpolicysastoken&sig=EjAqjo6Nu5xMLEZEkMkLbeF7TD9v1J8DNB2t8gOKTts%3D'
    ;
GO

------  Step 3.  Create (define) an event session.  --------
------  The event session has an event with an action,
------  and a has a target.

IF EXISTS
    (SELECT * from sys.database_event_sessions
        WHERE name = 'gmeventsessionname240b')
BEGIN
    DROP
        EVENT SESSION
            gmeventsessionname240b
        ON DATABASE;
END
GO

CREATE
    EVENT SESSION
        gmeventsessionname240b
    ON DATABASE

    ADD EVENT
        sqlserver.sql_statement_starting
            (
            ACTION (sqlserver.sql_text)
            WHERE statement LIKE 'UPDATE gmTabEmployee%'
            )
    ADD TARGET
        package0.event_file
            (
            -- TODO: Assign AzureStorageAccount name, and the associated Container name.
            -- Also, tweak the .xel file name at end, if you like.
            SET filename =
                'https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent/anyfilenamexel242b.xel'
            )
    WITH
        (MAX_MEMORY = 10 MB,
        MAX_DISPATCH_LATENCY = 3 SECONDS)
    ;
GO

------  Step 4.  Start the event session.  ----------------
------  Issue the SQL Update statements that will be traced.
------  Then stop the session.

------  Note: If the target fails to attach,
------  the session must be stopped and restarted.

ALTER
    EVENT SESSION
        gmeventsessionname240b
    ON DATABASE
    STATE = START;
GO

SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM gmTabEmployee;

UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 2
    WHERE EmployeeDescr = 'Jane Doe';

UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 13
    WHERE EmployeeDescr = 'Jane Doe';

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM gmTabEmployee;
GO

ALTER
    EVENT SESSION
        gmeventsessionname240b
    ON DATABASE
    STATE = STOP;
GO

-------------- Step 5.  Select the results. ----------

SELECT
        *, 'CLICK_NEXT_CELL_TO_BROWSE_ITS_RESULTS!' as [CLICK_NEXT_CELL_TO_BROWSE_ITS_RESULTS],
        CAST(event_data AS XML) AS [event_data_XML]  -- TODO: In ssms.exe results grid, double-click this cell!
    FROM
        sys.fn_xe_file_target_read_file
            (
                -- TODO: Fill in Storage Account name, and the associated Container name.
                -- TODO: The name of the .xel file needs to be an exact match to the files in the storage account Container (You can use Storage Account explorer from the portal to find out the exact file names or you can retrieve the name using the following DMV-query: select target_data from sys.dm_xe_database_session_targets. The 3rd xml-node, "File name", contains the name of the file currently written to.)
                'https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent/anyfilenamexel242b',
                null, null, null
            );
GO

-------------- Step 6.  Clean up. ----------

DROP
    EVENT SESSION
        gmeventsessionname240b
    ON DATABASE;
GO

DROP DATABASE SCOPED CREDENTIAL
    -- TODO: Assign AzureStorageAccount name, and the associated Container name.
    [https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent]
    ;
GO

DROP TABLE gmTabEmployee;
GO

PRINT 'Use PowerShell Remove-AzStorageAccount to delete your Azure Storage account!';
GO
```

Om målet inte kan kopplas när du kör måste du stoppa och starta om händelsesessionen:

```sql
ALTER EVENT SESSION ... STATE = STOP;
GO
ALTER EVENT SESSION ... STATE = START;
GO
```

## <a name="output"></a>Resultat

När transact-SQL-skriptet är klart klickar du på en cell under **kolumnrubriken event_data_XML.** En ** \<händelse>** element visas som visar en UPDATE-sats.

Här är ** \<** en händelse>element som genererades under testning:

```xml
<event name="sql_statement_starting" package="sqlserver" timestamp="2015-09-22T19:18:45.420Z">
  <data name="state">
    <value>0</value>
    <text>Normal</text>
  </data>
  <data name="line_number">
    <value>5</value>
  </data>
  <data name="offset">
    <value>148</value>
  </data>
  <data name="offset_end">
    <value>368</value>
  </data>
  <data name="statement">
    <value>UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 2
    WHERE EmployeeDescr = 'Jane Doe'</value>
  </data>
  <action name="sql_text" package="sqlserver">
    <value>

SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM gmTabEmployee;

UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 2
    WHERE EmployeeDescr = 'Jane Doe';

UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 13
    WHERE EmployeeDescr = 'Jane Doe';

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM gmTabEmployee;
</value>
  </action>
</event>
```

Det föregående Transact-SQL-skriptet använde följande systemfunktion för att läsa event_file:

- [sys.fn_xe_file_target_read_file (Transact-SQL)](https://msdn.microsoft.com/library/cc280743.aspx)

En förklaring av avancerade alternativ för visning av data från utökade händelser finns på:

- [Avancerad visning av måldata från utökade händelser](https://msdn.microsoft.com/library/mt752502.aspx)

## <a name="converting-the-code-sample-to-run-on-sql-server"></a>Konvertera kodexemplet så att det körs på SQL Server

Anta att du vill köra föregående Transact-SQL-exempel på Microsoft SQL Server.

- För enkelhetens skull vill du helt ersätta användningen av Azure Storage-behållaren med en enkel fil som *C:\myeventdata.xel*. Filen skulle skrivas till den lokala hårddisken på datorn som är värd för SQL Server.
- Du behöver ingen form av Transact-SQL-satser för **SKAPA HUVUDNYCKEL** OCH **SKAPA AUTENTISERINGSUPPGIFTER**.
- I uttrycket SKAPA HÄNDELSESESSION ersätter du **http-värdet** som tilldelats **filnamnet i** uttrycket **SKAPA HÄNDELSESESSION** med en sträng med en fullständig sökväg som *C:\myfile.xel*.
  
  - Inget Azure Storage-konto behöver involveras.

## <a name="more-information"></a>Mer information

Mer information om konton och behållare i Azure Storage-tjänsten finns i:

- [Så här använder du Blob-lagring från .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md)
- [Namnge och referera behållare, blobbar och metadata](https://msdn.microsoft.com/library/azure/dd135715.aspx)
- [Arbeta med rotbehållaren](https://msdn.microsoft.com/library/azure/ee395424.aspx)
- [Lektion 1: Skapa en lagrad åtkomstprincip och en signatur för delad åtkomst på en Azure-behållare](https://msdn.microsoft.com/library/dn466430.aspx)
  - [Lektion 2: Skapa en SQL Server-autentiseringsuppgifter med hjälp av en signatur för delad åtkomst](https://msdn.microsoft.com/library/dn466435.aspx)
- [Utökade händelser för Microsoft SQL Server](https://docs.microsoft.com/sql/relational-databases/extended-events/extended-events)

<!-- Image references. -->
[30_powershell_ise]: ./media/sql-database-xevent-code-event-file/event-file-powershell-ise-b30.png
