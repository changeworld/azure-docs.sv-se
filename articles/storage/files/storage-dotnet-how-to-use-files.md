---
title: Utveckla för Azure Files med .NET | Microsoft Docs
description: Lär dig hur du utvecklar .NET-program och tjänster som använder Azure Files för att lagra fildata.
author: roygara
ms.service: storage
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 10/7/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 4d8be13a75e276d5be6ec71141a13f95601869f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78301445"
---
# <a name="develop-for-azure-files-with-net"></a>Utveckla för Azure Files med .NET

[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

Den här kursen visar grunderna i hur du använder .NET för att utveckla program eller tjänster som lagrar fildata med hjälp av [Azure Files](storage-files-introduction.md). Den här självstudien skapar ett enkelt konsolprogram för att utföra grundläggande åtgärder med .NET- och Azure-filer:

* Hämta innehållet i en fil.
* Ange den maximala storleken eller *kvoten* för filresursen.
* Skapa en SAS-signatur (SHARED Access-signatur) för en fil som använder en lagrad åtkomstprincip som definierats på resursen.
* Kopiera en fil till en annan fil i samma lagringskonto.
* Kopiera en fil till en blobb i samma lagringskonto.
* Använd Azure Storage Metrics för felsökning.

Mer information om Azure-filer finns i [Vad är Azure-filer?](storage-files-introduction.md)

[!INCLUDE [storage-check-out-samples-dotnet](../../../includes/storage-check-out-samples-dotnet.md)]

## <a name="understanding-the-net-apis"></a>Förstå .NET-API: er

Azure Files tillhandahåller två breda metoder för klientprogram: Server Message Block (SMB) och REST. Inom .NET `System.IO` abstrakta metoder och `WindowsAzure.Storage` API:er.

API | När du ska använda detta | Anteckningar
----|-------------|------
[System.IO](https://docs.microsoft.com/dotnet/api/system.io) | Ditt program: <ul><li>Måste läsa/skriva filer med hjälp av SMB</li><li>Körs på en enhet som har åtkomst via port 445 till ditt Azure Files-konto</li><li>Behöver inte hantera några av de administrativa inställningarna för filresursen</li></ul> | Fil-I/O implementeras med Azure-filer via SMB är i allmänhet samma som I/O med alla nätverksfilsresurser eller lokala lagringsenheter. En introduktion till ett antal funktioner i .NET, inklusive fil-I/O, finns i självstudien [för konsolprogram.](https://docs.microsoft.com/dotnet/csharp/tutorials/console-teleprompter)
[Microsoft.Azure.Storage.File](/dotnet/api/overview/azure/storage?view=azure-dotnet#version-11x) | Ditt program: <ul><li>Det går inte att komma åt Azure-filer med SMB i port 445 på grund av brandväggs- eller Isp-begränsningar</li><li>Kräver administrativa funktioner, som möjligheten att ställa in en filresurs kvot eller skapa en signatur för delad åtkomst</li></ul> | Den här artikeln visar `Microsoft.Azure.Storage.File` användningen av för fil-I/O med REST istället för SMB och hantering av filresursen.

## <a name="create-the-console-application-and-obtain-the-assembly"></a>Skapa konsolprogrammet och hämta monteringen

Skapa ett nytt Windows-konsolprogram i Visual Studio. Följande steg visar hur du skapar ett konsolprogram i Visual Studio 2019. Stegen är ungefär som i andra versioner av Visual Studio.

1. Starta Visual Studio och välj **Skapa ett nytt projekt**.
1. I **Skapa ett nytt projekt**väljer du Console App **(.NET Framework)** för C#och väljer sedan **Nästa**.
1. Ange ett namn på appen i **Konfigurera det nya projektet**och välj **Skapa**.

Du kan lägga till alla kodexempel i den här självstudien till `Main()` metoden för konsolprogrammets `Program.cs` fil.

Du kan använda Azure Storage-klientbiblioteket i alla typer av .NET-program. Dessa typer inkluderar en Azure molntjänst eller webbapp och stationära och mobila program. I den här guiden använder vi oss av en konsolapp för enkelhetens skull.

## <a name="use-nuget-to-install-the-required-packages"></a>Använd NuGet för att installera de paket som behövs

Se dessa paket i projektet för att slutföra den här självstudien:

* [Gemensamt bibliotek för Microsoft Azure Storage för .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/)
  
  Det här paketet ger programmatisk åtkomst till gemensamma resurser i ditt lagringskonto.
* [Microsoft Azure Storage Blob-bibliotek för .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.Blob/)

  Det här paketet ger programmatisk åtkomst till blob-resurser i ditt lagringskonto.
* [Microsoft Azure Storage File-bibliotek för .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.File/)

  Det här paketet ger programmatisk åtkomst till filresurser i ditt lagringskonto.
* [Microsoft Azure Configuration Manager-bibliotek för .NET](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/)

  Det här paketet innehåller en klass för att tolka en anslutningssträng i en konfigurationsfil, oavsett var ditt program körs.

Du kan använda NuGet för att hämta båda paketen. Följ de här stegen:

1. Högerklicka på projektet i **Solution Explorer**och välj **Hantera NuGet-paket**.
1. I **NuGet Package Manager**väljer du **Bläddra**. Sök sedan efter och välj **Microsoft.Azure.Storage.Blob**och välj sedan **Installera**.

   Det här steget installerar paketet och dess beroenden.
1. Sök efter och installera dessa paket:

   * **Microsoft.Azure.Storage.Common**
   * **Microsoft.Azure.Storage.File**
   * **Microsoft.Azure.ConfigurationHanager**

## <a name="save-your-storage-account-credentials-to-the-appconfig-file"></a>Spara autentiseringsuppgifterna för lagringskontot i filen App.config

Spara sedan dina autentiseringsuppgifter `App.config` i projektets fil. Dubbelklicka `App.config` och redigera filen i **Solution Explorer**så att den liknar följande exempel. Ersätt `myaccount` med ditt lagringskontonamn och `mykey` med din lagringskontonyckel.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
    <startup>
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
    </startup>
    <appSettings>
        <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=StorageAccountKeyEndingIn==" />
    </appSettings>
</configuration>
```

> [!NOTE]
> Den senaste versionen av Azure Storage-emulatorn har inte stöd för Azure Files. Anslutningssträngen måste peka på ett Azure Storage-konto i molnet för att fungera med Azure Files.

## <a name="add-using-directives"></a>Lägga till med hjälp av direktiv

Öppna **Solution Explorer**filen i `Program.cs` Solution Explorer och lägg till följande med hjälp av direktiv överst i filen.

```csharp
using Microsoft.Azure; // Namespace for Azure Configuration Manager
using Microsoft.Azure.Storage; // Namespace for Storage Client Library
using Microsoft.Azure.Storage.Blob; // Namespace for Azure Blobs
using Microsoft.Azure.Storage.File; // Namespace for Azure Files
```

[!INCLUDE [storage-cloud-configuration-manager-include](../../../includes/storage-cloud-configuration-manager-include.md)]

## <a name="access-the-file-share-programmatically"></a>Ansluta till filresursen via programmering

Lägg sedan till följande `Main()` innehåll i metoden, efter koden som visas ovan, för att hämta anslutningssträngen. Den här koden får en referens till filen som vi skapade tidigare och matar ut dess innehåll.

```csharp
// Create a CloudFileClient object for credentialed access to Azure Files.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Get a reference to the file share we created previously.
CloudFileShare share = fileClient.GetShareReference("logs");

// Ensure that the share exists.
if (share.Exists())
{
    // Get a reference to the root directory for the share.
    CloudFileDirectory rootDir = share.GetRootDirectoryReference();

    // Get a reference to the directory we created previously.
    CloudFileDirectory sampleDir = rootDir.GetDirectoryReference("CustomLogs");

    // Ensure that the directory exists.
    if (sampleDir.Exists())
    {
        // Get a reference to the file we created previously.
        CloudFile file = sampleDir.GetFileReference("Log1.txt");

        // Ensure that the file exists.
        if (file.Exists())
        {
            // Write the contents of the file to the console window.
            Console.WriteLine(file.DownloadTextAsync().Result);
        }
    }
}
```

Visa resultatet genom att köra konsolprogrammet.

## <a name="set-the-maximum-size-for-a-file-share"></a>Ange den största storleken för en filresurs

Från och med version 5.x av Azure Storage Client Library kan du ange kvoten (maximal storlek) för en filresurs. Du kan också kontrollera hur mycket data som lagras på resursen för närvarande.

Om du anger kvoten för en resurs begränsas den totala storleken på de filer som lagras på resursen. Om den totala storleken på filerna på resursen överskrider kvotuppsättningen för resursen kan klienterna inte öka storleken på befintliga filer. Klienter kan inte skapa nya filer, såvida inte filerna är tomma.

Exemplet nedan visar hur du kontrollerar användningen av en resurs och hur du ställer in kvoten för resursen.

```csharp
// Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create a CloudFileClient object for credentialed access to Azure Files.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Get a reference to the file share we created previously.
CloudFileShare share = fileClient.GetShareReference("logs");

// Ensure that the share exists.
if (share.Exists())
{
    // Check current usage stats for the share.
    // Note that the ShareStats object is part of the protocol layer for the File service.
    Microsoft.Azure.Storage.File.Protocol.ShareStats stats = share.GetStats();
    Console.WriteLine("Current share usage: {0} GB", stats.Usage.ToString());

    // Specify the maximum size of the share, in GB.
    // This line sets the quota to be 10 GB greater than the current usage of the share.
    share.Properties.Quota = 10 + stats.Usage;
    share.SetProperties();

    // Now check the quota for the share. Call FetchAttributes() to populate the share's properties.
    share.FetchAttributes();
    Console.WriteLine("Current share quota: {0} GB", share.Properties.Quota);
}
```

### <a name="generate-a-shared-access-signature-for-a-file-or-file-share"></a>Generera en signatur för delad åtkomst för en fil eller filresurs

Från och med version 5.x av klientbiblioteket för Azure Storage kan du generera en signatur för delad åtkomst (SAS) för en filresurs eller för en enskild fil. Du kan också skapa en lagrad åtkomstprincip på en filresurs för att hantera signaturer för delad åtkomst. Vi rekommenderar att du skapar en lagrad åtkomstprincip eftersom du kan återkalla SAS om den äventyras.

I följande exempel skapas en lagrad åtkomstprincip på en resurs. I exemplet används den principen för att ange begränsningar för en SAS-fil i en fil i resursen.

```csharp
// Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create a CloudFileClient object for credentialed access to Azure Files.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Get a reference to the file share we created previously.
CloudFileShare share = fileClient.GetShareReference("logs");

// Ensure that the share exists.
if (share.Exists())
{
    string policyName = "sampleSharePolicy" + DateTime.UtcNow.Ticks;

    // Create a new stored access policy and define its constraints.
    SharedAccessFilePolicy sharedPolicy = new SharedAccessFilePolicy()
        {
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Permissions = SharedAccessFilePermissions.Read | SharedAccessFilePermissions.Write
        };

    // Get existing permissions for the share.
    FileSharePermissions permissions = share.GetPermissions();

    // Add the stored access policy to the share's policies. Note that each policy must have a unique name.
    permissions.SharedAccessPolicies.Add(policyName, sharedPolicy);
    share.SetPermissions(permissions);

    // Generate a SAS for a file in the share and associate this access policy with it.
    CloudFileDirectory rootDir = share.GetRootDirectoryReference();
    CloudFileDirectory sampleDir = rootDir.GetDirectoryReference("CustomLogs");
    CloudFile file = sampleDir.GetFileReference("Log1.txt");
    string sasToken = file.GetSharedAccessSignature(null, policyName);
    Uri fileSasUri = new Uri(file.StorageUri.PrimaryUri.ToString() + sasToken);

    // Create a new CloudFile object from the SAS, and write some text to the file.
    CloudFile fileSas = new CloudFile(fileSasUri);
    fileSas.UploadText("This write operation is authorized via SAS.");
    Console.WriteLine(fileSas.DownloadText());
}
```

Mer information om hur du skapar och använder signaturer för delad åtkomst finns i [Så här fungerar en signatur för delad åtkomst](../common/storage-sas-overview.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#how-a-shared-access-signature-works).

## <a name="copy-files"></a>Kopiera filer

Från och med version 5.x av klientbiblioteket för Azure Storage kan du kopiera en fil till en annan fil, en fil till en blobb eller en blobb till en fil. I nästa avsnitt visar vi hur du gör dessa kopieringsåtgärder programmässigt.

Du kan också använda AzCopy för att kopiera en fil till en annan eller för att kopiera en blob till en fil eller tvärtom. Se [Kom igång med AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

> [!NOTE]
> Om du kopierar en blobb till en fil eller en fil till en blobb måste du använda en signatur för delad åtkomst (SAS) för att auktorisera åtkomst till källobjektet, även om du kopierar inom samma lagringskonto.
>

### <a name="copy-a-file-to-another-file"></a>Kopiera en fil till en annan fil

I följande exempel kopierar vi en fil till en annan fil i samma resurs. Eftersom den här kopieringen kopierar kopior mellan filer i samma lagringskonto kan du använda autentisering med delad nyckel för att göra kopian.

```csharp
// Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create a CloudFileClient object for credentialed access to Azure Files.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Get a reference to the file share we created previously.
CloudFileShare share = fileClient.GetShareReference("logs");

// Ensure that the share exists.
if (share.Exists())
{
    // Get a reference to the root directory for the share.
    CloudFileDirectory rootDir = share.GetRootDirectoryReference();

    // Get a reference to the directory we created previously.
    CloudFileDirectory sampleDir = rootDir.GetDirectoryReference("CustomLogs");

    // Ensure that the directory exists.
    if (sampleDir.Exists())
    {
        // Get a reference to the file we created previously.
        CloudFile sourceFile = sampleDir.GetFileReference("Log1.txt");

        // Ensure that the source file exists.
        if (sourceFile.Exists())
        {
            // Get a reference to the destination file.
            CloudFile destFile = sampleDir.GetFileReference("Log1Copy.txt");

            // Start the copy operation.
            destFile.StartCopy(sourceFile);

            // Write the contents of the destination file to the console window.
            Console.WriteLine(destFile.DownloadText());
        }
    }
}
```

### <a name="copy-a-file-to-a-blob"></a>Kopiera en fil till en blobb

I följande exempel skapar vi en fil och kopierar den till en blobb inom samma lagringskonto. I exemplet skapas en SAS för källfilen, som tjänsten använder för att auktorisera åtkomsten till källfilen under kopieringen.

```csharp
// Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create a CloudFileClient object for credentialed access to Azure Files.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Create a new file share, if it does not already exist.
CloudFileShare share = fileClient.GetShareReference("sample-share");
share.CreateIfNotExists();

// Create a new file in the root directory.
CloudFile sourceFile = share.GetRootDirectoryReference().GetFileReference("sample-file.txt");
sourceFile.UploadText("A sample file in the root directory.");

// Get a reference to the blob to which the file will be copied.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
CloudBlobContainer container = blobClient.GetContainerReference("sample-container");
container.CreateIfNotExists();
CloudBlockBlob destBlob = container.GetBlockBlobReference("sample-blob.txt");

// Create a SAS for the file that's valid for 24 hours.
// Note that when you are copying a file to a blob, or a blob to a file, you must use a SAS
// to authorize access to the source object, even if you are copying within the same
// storage account.
string fileSas = sourceFile.GetSharedAccessSignature(new SharedAccessFilePolicy()
{
    // Only read permissions are required for the source file.
    Permissions = SharedAccessFilePermissions.Read,
    SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24)
});

// Construct the URI to the source file, including the SAS token.
Uri fileSasUri = new Uri(sourceFile.StorageUri.PrimaryUri.ToString() + fileSas);

// Copy the file to the blob.
destBlob.StartCopy(fileSasUri);

// Write the contents of the file to the console window.
Console.WriteLine("Source file contents: {0}", sourceFile.DownloadText());
Console.WriteLine("Destination blob contents: {0}", destBlob.DownloadText());
```

Du kan kopiera en blobb till en fil på samma sätt. Om källobjektet är en blobb skapar du en SAS för att auktorisera åtkomsten till blobben under kopieringen.

## <a name="share-snapshots"></a>Resursögonblicksbilder

Från och med version 8.5 av klientbiblioteket för Azure Storage kan du skapa en resursögonblicksbild. Du kan också visa eller bläddra bland resursögonblicksbilder och ta bort resursögonblicksbilder. Resursögonblicksbilder är skrivskyddade så att inga skrivåtgärder tillåts på resursögonblicksbilder.

### <a name="create-share-snapshots"></a>Skapa resursögonblicksbilder

I följande exempel skapas en ögonblicksbild av en filresurs.

```csharp
storageAccount = CloudStorageAccount.Parse(ConnectionString); 
fClient = storageAccount.CreateCloudFileClient(); 
string baseShareName = "myazurefileshare"; 
CloudFileShare myShare = fClient.GetShareReference(baseShareName); 
var snapshotShare = myShare.Snapshot();

```

### <a name="list-share-snapshots"></a>Lista resursögonblicksbilder

Följande exempel visar en lista över ögonblicksbilder på en resurs.

```csharp
var shares = fClient.ListShares(baseShareName, ShareListingDetails.All);
```

### <a name="browse-files-and-directories-within-share-snapshots"></a>Bläddra bland filer och kataloger i resursögonblicksbilder

Följande exempel bläddrar bland filer och kataloger i resursögonblicksbilder.

```csharp
CloudFileShare mySnapshot = fClient.GetShareReference(baseShareName, snapshotTime); 
var rootDirectory = mySnapshot.GetRootDirectoryReference(); 
var items = rootDirectory.ListFilesAndDirectories();
```

### <a name="list-shares-and-share-snapshots-and-restore-file-shares-or-files-from-share-snapshots"></a>Visa en lista över resurser och resursögonblicksbilder och återställ filresurser eller filer från resursögonblicksbilder

Genom att göra en ögonblicksbild av en filresurs kan du återställa enskilda filer eller hela filresursen i framtiden.

Du kan återställa en fil från en ögonblicksbild av en filresurs genom att skicka en fråga till ögonblicksbilderna av en filresurs. Du kan sedan hämta en fil som tillhör en viss ögonblicksbild av resursen. Använd den versionen för att antingen läsa direkt och jämföra eller för att återställa.

```csharp
CloudFileShare liveShare = fClient.GetShareReference(baseShareName);
var rootDirOfliveShare = liveShare.GetRootDirectoryReference();
var dirInliveShare = rootDirOfliveShare.GetDirectoryReference(dirName);
var fileInliveShare = dirInliveShare.GetFileReference(fileName);

CloudFileShare snapshot = fClient.GetShareReference(baseShareName, snapshotTime);
var rootDirOfSnapshot = snapshot.GetRootDirectoryReference();
var dirInSnapshot = rootDirOfSnapshot.GetDirectoryReference(dirName);
var fileInSnapshot = dir1InSnapshot.GetFileReference(fileName);

string sasContainerToken = string.Empty;
SharedAccessFilePolicy sasConstraints = new SharedAccessFilePolicy();
sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
sasConstraints.Permissions = SharedAccessFilePermissions.Read;

//Generate the shared access signature on the container, setting the constraints directly on the signature.
sasContainerToken = fileInSnapshot.GetSharedAccessSignature(sasConstraints);

string sourceUri = (fileInSnapshot.Uri.ToString() + sasContainerToken + "&" + fileInSnapshot.SnapshotTime.ToString()); ;
fileInliveShare.StartCopyAsync(new Uri(sourceUri));
```

### <a name="delete-share-snapshots"></a>Ta bort resursögonblicksbilder

Följande exempel tar bort en ögonblicksbild av en filresurs.

```csharp
CloudFileShare mySnapshot = fClient.GetShareReference(baseShareName, snapshotTime); mySnapshot.Delete(null, null, null);
```

## <a name="troubleshoot-azure-files-by-using-metrics"></a>Felsöka Azure-filer med hjälp av mått<a name="troubleshooting-azure-files-using-metrics"></a>

Nu stöder Azure Storage Analytics mätvärden för Azure Files. Med hjälp av mätvärdesdata kan du spåra begäranden och diagnostisera problem.

Du kan aktivera mått för Azure-filer från [Azure-portalen](https://portal.azure.com). Du kan också aktivera mått programmässigt genom att anropa åtgärden Ange filtjänstegenskaper med REST API eller en av dess analoger i lagringsklientbiblioteket.

Följande kodexempel visar hur du använder Storage-klientbiblioteket för .NET för att aktivera mätvärden för Azure Files.

Lägg först till `using` följande `Program.cs` direktiv i filen tillsammans med de som du har lagt till ovan:

```csharp
using Microsoft.Azure.Storage.File.Protocol;
using Microsoft.Azure.Storage.Shared.Protocol;
```

Även om Azure Blobbar, Azure-tabeller och `ServiceProperties` Azure-köer använder den delade typen i `Microsoft.Azure.Storage.Shared.Protocol` namnområdet, använder Azure Files sin egen typ, `FileServiceProperties` typen i `Microsoft.Azure.Storage.File.Protocol` namnområdet. Du måste dock referera till båda namnområdena från koden för att följande kod ska kunna kompileras.

```csharp
// Parse your storage connection string from your application's configuration file.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));
// Create the File service client.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Set metrics properties for File service.
// Note that the File service currently uses its own service properties type,
// available in the Microsoft.Azure.Storage.File.Protocol namespace.
fileClient.SetServiceProperties(new FileServiceProperties()
{
    // Set hour metrics
    HourMetrics = new MetricsProperties()
    {
        MetricsLevel = MetricsLevel.ServiceAndApi,
        RetentionDays = 14,
        Version = "1.0"
    },
    // Set minute metrics
    MinuteMetrics = new MetricsProperties()
    {
        MetricsLevel = MetricsLevel.ServiceAndApi,
        RetentionDays = 7,
        Version = "1.0"
    }
});

// Read the metrics properties we just set.
FileServiceProperties serviceProperties = fileClient.GetServiceProperties();
Console.WriteLine("Hour metrics:");
Console.WriteLine(serviceProperties.HourMetrics.MetricsLevel);
Console.WriteLine(serviceProperties.HourMetrics.RetentionDays);
Console.WriteLine(serviceProperties.HourMetrics.Version);
Console.WriteLine();
Console.WriteLine("Minute metrics:");
Console.WriteLine(serviceProperties.MinuteMetrics.MetricsLevel);
Console.WriteLine(serviceProperties.MinuteMetrics.RetentionDays);
Console.WriteLine(serviceProperties.MinuteMetrics.Version);
```

Om du stöter på problem kan du läsa [felsöka Problem med Azure Files i Windows](storage-troubleshoot-windows-file-connection-problems.md).

## <a name="next-steps"></a>Nästa steg

Mer information om Azure-filer finns i följande resurser:

### <a name="conceptual-articles-and-videos"></a>Begreppsrelaterade artiklar och videoklipp

* [Azure Files: ett smidigt SMB-filsystem i molnet för Windows och Linux](https://azure.microsoft.com/documentation/videos/azurecon-2015-azure-files-storage-a-frictionless-cloud-smb-file-system-for-windows-and-linux/)
* [Använda Azure Files med Linux](storage-how-to-use-files-linux.md)

### <a name="tooling-support-for-file-storage"></a>Verktygsstöd för File Storage

* [Kom igång med AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)
* [Felsöka Azure Files-problem i Windows](https://docs.microsoft.com/azure/storage/storage-troubleshoot-file-connection-problems)

### <a name="reference"></a>Referens

* [API:er för .NET i Azure Storage](/dotnet/api/overview/azure/storage)
* [Fil-tjänstens REST-API](/rest/api/storageservices/File-Service-REST-API)

### <a name="blog-posts"></a>Blogginlägg

* [Azure File Storage, nu allmänt tillgänglig](https://azure.microsoft.com/blog/azure-file-storage-now-generally-available/)
* [Inuti Azure File Storage](https://azure.microsoft.com/blog/inside-azure-file-storage/)
* [Introduktion till Microsoft Azure Files Service](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx)
* [Spara anslutningar till Microsoft Azure-filer](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx)
