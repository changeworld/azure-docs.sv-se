---
title: Snabbstart för hantering av Azure-filresurser med Azure PowerShell
description: Använd den här snabbstarten för att lära dig hur du hanterar Azure-filresurser med Azure PowerShell.
author: roygara
ms.service: storage
ms.topic: quickstart
ms.date: 10/26/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: c419c2127b1c5fe3aaa60c6e828ff0c5a6676c07
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "77598555"
---
# <a name="quickstart-create-and-manage-an-azure-file-share-with-azure-powershell"></a>Snabbstart: Skapa och hantera en Azure-filresurs med Azure PowerShell 
Den här guiden går igenom grunderna med att arbeta med [Azure-filresurser](storage-files-introduction.md) med PowerShell. Azure-filresurser är precis som andra filresurser men lagras i molnet och täcks av Azure-plattformen. Azure-filresurser stöder SMB-protokollet som är branschstandard och möjliggör fildelning på olika datorer, program och instanser. 

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du vill installera och använda PowerShell lokalt krävs Azure PowerShell-modul Az version 0.7 eller senare i den här självstudien. Om du vill ta reda på vilken version av Azure PowerShell-modulen som du kör, kör du `Get-Module -ListAvailable Az`. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-Az-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du även köra `Login-AzAccount` för att logga in på ditt Azure account.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp
En resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. Om du inte redan har en Azure-resursgrupp, kan du skapa en ny med cmdleten [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). 

I följande exempel skapas en resursgrupp med namnet *myResourceGroup* i regionen Västra USA 2:

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$region = "westus2"

New-AzResourceGroup `
    -Name $resourceGroupName `
    -Location $region | Out-Null
```

## <a name="create-a-storage-account"></a>Skapa ett lagringskonto
Ett lagringskonto är en delad lagringspool som du kan använda för att distribuera Azure-filresurser. Ett lagringskonto kan innehålla ett obegränsat antal resurser och en resurs kan lagra ett obegränsat antal filer, upp till lagringskontots kapacitetsgräns. I det här exemplet skapas ett allmänt versions 2-lagringskonto (GPv2-lagringskonto), som kan lagra standardresurser för Azure-filer eller andra lagringsresurser, till exempel blobbar eller köer, på hårddiskens rotationsmedia (HDD). Azure Files stöder också SSD-enheter (Premium Solid State-diskar). premium Azure-filresurser kan skapas i FileStorage-lagringskonton.

I det här exemplet skapas ett lagringskonto med cmdleten [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount). Lagringskontot heter *random\<number>* och en referens till det lagringskontot lagras i variabeln **$storageAcct**. Lagringskontonamn måste vara unika så använd `Get-Random` för att lägga till ett tal till namnet som gör det unikt. 

```azurepowershell-interactive 
$storageAccountName = "mystorageacct$(Get-Random)"

$storageAcct = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -Location $region `
    -Kind StorageV2 `
    -SkuName Standard_ZRS `
    -EnableLargeFileShare
```

> [!Note]  
> Aktier som är större än 5 TiB (upp till maximalt 100 TiB per aktie) är endast tillgängliga i lokalt redundanta (LRS) och zon redundanta (ZRS) lagringskonton. Om du vill skapa ett geo redundant (GRS) eller ett gzrs-lagringskonto (geo-zone-redundant) tar du bort parametern. `-EnableLargeFileShare`

## <a name="create-an-azure-file-share"></a>Skapa en Azure-filresurs
Nu kan du skapa din första Azure-filresurs. Du kan skapa en filresurs med cmdleten [New-AzStorageShare.](/powershell/module/az.storage/New-AzRmStorageShare) I det här exemplet skapar vi en resurs som heter `myshare`.

```azurepowershell-interactive
$shareName = "myshare"

New-AzRmStorageShare `
    -StorageAccount $storageAcct `
    -Name $shareName `
    -QuotaGiB 1024 | Out-Null
```

Resursnamn får bara innehålla gemener, siffror och enskilda bindestreck, men får inte inledas med bindestreck. Mer information om hur du namnger filresurser och filer finns i [Namnge och referera till resurser, kataloger, filer och Metadata](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata).

## <a name="use-your-azure-file-share"></a>Använda Azure-filresursen
Azure Files har två metoder för att arbeta med filer och mappar i din Azure-filresurs: branschstandardprotokollen [SMB (Server Message Block)](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx) och [fil-REST](https://docs.microsoft.com/rest/api/storageservices/file-service-rest-api). 

Om du vill montera en filresurs med SMB läser du följande dokument baserat på ditt operativsystem:
- [Windows](storage-how-to-use-files-windows.md)
- [Linux](storage-how-to-use-files-linux.md)
- [Macos](storage-how-to-use-files-mac.md)

### <a name="using-an-azure-file-share-with-the-file-rest-protocol"></a>Använda en Azure-filresurs med fil-REST-protokollet 
Det är möjligt arbete med File REST-protokollet direkt (dvs. handcrafting REST HTTP-anrop själv), men det vanligaste sättet att använda File REST-protokollet är att använda Azure PowerShell-modulen, [Azure CLI](storage-how-to-use-files-cli.md)eller en Azure Storage SDK, som alla ger ett trevligt omslag runt file REST-protokollet i skript/programmeringsspråk som du väljer.  

I de flesta fall använder du Azure-filresursen via SMB-protokollet, eftersom du då kan använda de befintliga program och verktyg som du förväntar dig kunna använda, men det finns flera anledningar till varför det är fördelaktigt att använda fil-REST-API:et istället för SMB, till exempel:

- Du bläddrar efter filresursen från PowerShell Cloud Shell (som inte kan montera filresurser via SMB).
- Du utnyttjar serverlösa resurser såsom [Azure Functions](../../azure-functions/functions-overview.md). 
- Du skapar en value-add-tjänst som interagerar med många Azure-filresurser, till exempel att utföra säkerhetskopiering eller antivirussökningar.

I följande exempel visas hur du använder Azure PowerShell-modulen till att ändra din Azure-filresurs med fil-REST-protokollet. Parametern `-Context` används för att hämta lagringskontonyckeln för att utföra de angivna åtgärderna mot filresursen. Om du vill hämta lagringskontonyckeln måste `Owner` du ha RBAC-rollen för på lagringskontot.

#### <a name="create-directory"></a>Skapa katalog
Om du vill skapa en ny katalog med namnet *myDirectory* i roten av din Azure-filresurs, använder du cmdleten [New-AzStorageDirectory](/powershell/module/az.storage/New-AzStorageDirectory).

```azurepowershell-interactive
New-AzStorageDirectory `
   -Context $storageAcct.Context `
   -ShareName $shareName `
   -Path "myDirectory"
```

#### <a name="upload-a-file"></a>Överför en fil
För att visa dig hur du överför en fil med hjälp av cmdleten [Set-AzStorageFileContent](/powershell/module/az.storage/Set-AzStorageFileContent), måste vi först skapa en fil på din tillfälliga PowerShell Cloud Shell-enhet att ladda upp. 

Det här exemplet placerar aktuellt datum och tid i en ny fil på din tillfälliga enhet och överför sedan filen till filresursen.

```azurepowershell-interactive
# this expression will put the current date and time into a new file on your scratch drive
cd "~/CloudDrive/"
Get-Date | Out-File -FilePath "SampleUpload.txt" -Force

# this expression will upload that newly created file to your Azure file share
Set-AzStorageFileContent `
   -Context $storageAcct.Context `
   -ShareName $shareName `
   -Source "SampleUpload.txt" `
   -Path "myDirectory\SampleUpload.txt"
```   

Om du kör PowerShell lokalt, kan du ersätta `~/CloudDrive/` med en sökväg som finns på din dator.

När du har överfört filen kan du använda cmdleten [Get-AzStorageFile](/powershell/module/Az.Storage/Get-AzStorageFile) för att kontrollera att filen överfördes till din Azure-filresurs. 

```azurepowershell-interactive
Get-AzStorageFile `
    -Context $storageAcct.Context `
    -ShareName $shareName `
    -Path "myDirectory\" 
```

#### <a name="download-a-file"></a>Hämta en fil
Du kan hämta en kopia av filen du laddade upp till den tillfälliga Cloud Shell-enheten genom att använda cmdleten [Get-AzStorageFileContent](/powershell/module/az.storage/Get-AzStorageFilecontent).

```azurepowershell-interactive
# Delete an existing file by the same name as SampleDownload.txt, if it exists because you've run this example before.
Remove-Item `
    -Path "SampleDownload.txt" `
    -Force `
    -ErrorAction SilentlyContinue

Get-AzStorageFileContent `
    -Context $storageAcct.Context `
    -ShareName $shareName `
    -Path "myDirectory\SampleUpload.txt" `
    -Destination "SampleDownload.txt"
```

När du hämtat filen, kan du använda `Get-ChildItem` för att se att filen har hämtats till din tillfälliga PowerShell Cloud Shell-enhet.

```azurepowershell-interactive
Get-ChildItem | Where-Object { $_.Name -eq "SampleDownload.txt" }
``` 

#### <a name="copy-files"></a>Kopiera filer
En vanlig uppgift är att kopiera filer från en filresurs till en annan filresurs. Om du vill demonstrera den här funktionen kan du skapa en ny resurs och kopiera filen du överförde till denna nya resurs med cmdleten [Start-AzStorageFileCopy](/powershell/module/az.storage/Start-AzStorageFileCopy). 

```azurepowershell-interactive
$otherShareName = "myshare2"

New-AzRmStorageShare `
    -StorageAccount $storageAcct `
    -Name $otherShareName `
    -QuotaGiB 1024 | Out-Null
  
New-AzStorageDirectory `
   -Context $storageAcct.Context `
   -ShareName $otherShareName `
   -Path "myDirectory2"

Start-AzStorageFileCopy `
    -Context $storageAcct.Context `
    -SrcShareName $shareName `
    -SrcFilePath "myDirectory\SampleUpload.txt" `
    -DestShareName $otherShareName `
    -DestFilePath "myDirectory2\SampleCopy.txt" `
    -DestContext $storageAcct.Context
```

Du bör nu se den kopierade filen om du visa filerna i den nya resursen.

```azurepowershell-interactive
Get-AzStorageFile `
    -Context $storageAcct.Context `
    -ShareName $otherShareName `
    -Path "myDirectory2" 
```

Cmdlet är `Start-AzStorageFileCopy` praktiskt för ad hoc-filflyttningar mellan Azure-filresurser, för `robocopy` migreringar och större datarörelser, rekommenderar vi på Windows och `rsync` på macOS och Linux. `robocopy`och `rsync` använd SMB för att utföra datarörelserna i stället för FileREST API.

## <a name="create-and-manage-share-snapshots"></a>Skapa och hantera ögonblicksbilder
Ytterligare en användbar uppgift som du kan göra med en Azure-filresurs är att skapa resursögonblicksbilder. En ögonblicksbild bevarar en tidpunkt för en Azure-filresurs. Ögonblicksbilder av resurser liknar de operativsystemtekniker som du kanske redan är bekant med såsom:

- [Vss (Volume Shadow Copy Service)](https://docs.microsoft.com/windows/desktop/VSS/volume-shadow-copy-service-portal) för Windows-filsystem som NTFS och ReFS.
- [Lvm-ögonblicksbilder (Logical Volume Manager)](https://en.wikipedia.org/wiki/Logical_Volume_Manager_(Linux)#Basic_functionality) för Linux-system.
- [Apple File System (APFS)](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/APFS_Guide/Features/Features.html) ögonblicksbilder för macOS. 

Du kan skapa en ögonblicksbild av en resurs genom att använda metoden `Snapshot` på PowerShell-objektet för en filresurs, vilken hämtas med cmdleten [Get-AzStorageShare](/powershell/module/az.storage/Get-AzStorageShare). 

```azurepowershell-interactive
$share = Get-AzStorageShare -Context $storageAcct.Context -Name $shareName
$snapshot = $share.Snapshot()
```

### <a name="browse-share-snapshots"></a>Bläddra resursögonblicksbilder
Du kan söka i innehållet på resursögonblicksbilden genom att skicka ögonblicksbildreferensen (`$snapshot`) till parametern `-Share` för cmdleten `Get-AzStorageFile`.

```azurepowershell-interactive
Get-AzStorageFile -Share $snapshot
```

### <a name="list-share-snapshots"></a>Lista resursögonblicksbilder
Du kan se listan över ögonblicksbilder som du har tagit för din resurs med följande kommando.

```azurepowershell-interactive
Get-AzStorageShare `
        -Context $storageAcct.Context | `
    Where-Object { $_.Name -eq $shareName -and $_.IsSnapshot -eq $true }
```

### <a name="restore-from-a-share-snapshot"></a>Återställ från en resursögonblicksbild
Du kan återställa en fil med hjälp av kommandot `Start-AzStorageFileCopy` som vi använde tidigare. För den här snabbstarten, tar vi först bort vår `SampleUpload.txt`-fil som överförde tidigare så att vi kan återställa den från ögonblicksbilden.

```azurepowershell-interactive
# Delete SampleUpload.txt
Remove-AzStorageFile `
    -Context $storageAcct.Context `
    -ShareName $shareName `
    -Path "myDirectory\SampleUpload.txt"

# Restore SampleUpload.txt from the share snapshot
Start-AzStorageFileCopy `
    -SrcShare $snapshot `
    -SrcFilePath "myDirectory\SampleUpload.txt" `
    -DestContext $storageAcct.Context `
    -DestShareName $shareName `
    -DestFilePath "myDirectory\SampleUpload.txt"
```

### <a name="delete-a-share-snapshot"></a>Ta bort en resursögonblicksbild
Du kan ta bort en resursögonblicksbild med cmdleten [Remove-AzStorageShare](/powershell/module/az.storage/Remove-AzStorageShare), med variabeln som innehåller `$snapshot`-referensen till `-Share`-parametern.

```azurepowershell-interactive
Remove-AzStorageShare `
    -Share $snapshot `
    -Confirm:$false `
    -Force
```

## <a name="clean-up-resources"></a>Rensa resurser
När resursgruppen inte längre behövs kan du använda kommandot [Remove-AzRmResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) för att ta bort resursgruppen och alla relaterade resurser. 

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

Du kan också ta bort resurserna en i taget:

- Ta bort de Azure-filresurser som vi skapade för den här snabbstarten.

    ```azurepowershell-interactive
    Get-AzRmStorageShare -StorageAccount $storageAcct | Remove-AzRmStorageShare -Force
    ```

    > [!Note]  
    > Du måste ta bort alla ögonblicksbilder av resursen för Azure-filresurserna som du skapade innan du tar bort Azure-filresursen.

- Ta bort själva lagringskontot (vilket medför att de Azure-filresurser som vi skapat liksom andra lagringsresurser som du kan ha skapat, t.ex. en Azure Blob Storage-container, också tas bort).

    ```azurepowershell-interactive
    Remove-AzStorageAccount `
        -ResourceGroupName $storageAcct.ResourceGroupName `
        -Name $storageAcct.StorageAccountName
    ```

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Vad är Azure Files?](storage-files-introduction.md)