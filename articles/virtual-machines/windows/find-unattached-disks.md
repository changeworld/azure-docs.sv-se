---
title: Hitta och ta bort obundna Azure-hanterade och ohanterade diskar
description: Så här hittar och tar du bort obundna Azure-hanterade och ohanterade (VHDs/sidblobar) diskar med hjälp av Azure PowerShell.
author: roygara
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.date: 02/22/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 2973d2589be05426a13d16870d0b0fe1a5be9213
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74023057"
---
# <a name="find-and-delete-unattached-azure-managed-and-unmanaged-disks"></a>Hitta och ta bort obundna Azure-hanterade och ohanterade diskar

När du tar bort en virtuell dator (VM) i Azure tas som standard inte alla diskar som är kopplade till den virtuella datorn bort. Den här funktionen hjälper till att förhindra dataförlust på grund av oavsiktlig borttagning av virtuella datorer. När en virtuell dator har tagits bort fortsätter du att betala för obundna diskar. Den här artikeln visar hur du hittar och tar bort obundna diskar och minskar onödiga kostnader.

## <a name="managed-disks-find-and-delete-unattached-disks"></a>Hanterade diskar: Hitta och ta bort obundna diskar

Följande skript söker efter obundna [hanterade diskar](managed-disks-overview.md) genom att undersöka värdet för egenskapen **ManagedBy.** När en hanterad disk är kopplad till en virtuell dator innehåller egenskapen **ManagedBy** resurs-ID för den virtuella datorn. När en hanterad disk är obunden är egenskapen **ManagedBy** null. Skriptet undersöker alla hanterade diskar i en Azure-prenumeration. När skriptet hittar en hanterad disk med egenskapen **ManagedBy** inställd på null avgör skriptet att disken är okopplad.

>[!IMPORTANT]
>Kör först skriptet genom att ange variabeln **deleteUnattachedDisks** till 0. Med den här åtgärden kan du hitta och visa alla obundna hanterade diskar.
>
>När du har granskat alla obundna diskar kör du skriptet igen och ställer in variabeln **deleteUnattachedDisks** till 1. Med den här åtgärden kan du ta bort alla obundna hanterade diskar.

```azurepowershell-interactive
# Set deleteUnattachedDisks=1 if you want to delete unattached Managed Disks
# Set deleteUnattachedDisks=0 if you want to see the Id of the unattached Managed Disks
$deleteUnattachedDisks=0
$managedDisks = Get-AzDisk
foreach ($md in $managedDisks) {
    # ManagedBy property stores the Id of the VM to which Managed Disk is attached to
    # If ManagedBy property is $null then it means that the Managed Disk is not attached to a VM
    if($md.ManagedBy -eq $null){
        if($deleteUnattachedDisks -eq 1){
            Write-Host "Deleting unattached Managed Disk with Id: $($md.Id)"
            $md | Remove-AzDisk -Force
            Write-Host "Deleted unattached Managed Disk with Id: $($md.Id) "
        }else{
            $md.Id
        }
    }
 }
```

## <a name="unmanaged-disks-find-and-delete-unattached-disks"></a>Ohanterade diskar: Hitta och ta bort obundna diskar

Ohanterat diskar är VHD-filer som lagras som [sidblobar](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-page-blobs) i [Azure-lagringskonton](../../storage/common/storage-create-storage-account.md). Följande skript söker efter oanslutna ohanterade ohanterade diskar (sidblobar) genom att undersöka värdet för egenskapen **LeaseStatus.** När en ohanterad disk är kopplad till en virtuell dator anges egenskapen **LeaseStatus** till **Låst**. När en ohanterad disk är okopplad är egenskapen **LeaseStatus** inställd **på Olåst**. Skriptet undersöker alla ohanterade diskar i alla Azure-lagringskonton i en Azure-prenumeration. När skriptet hittar en ohanterad disk med egenskapen **LeaseStatus** inställd **på Olåst**avgör skriptet att disken är okopplad.

>[!IMPORTANT]
>Kör först skriptet genom att ange variabeln **deleteUnattachedVHDs** till 0. Med den här åtgärden kan du hitta och visa alla obundna ohanterade virtuella hårddiskar.
>
>När du har granskat alla obundna diskar kör du skriptet igen och ställer in variabeln **deleteUnattachedVHDs** till 1. Med den här åtgärden kan du ta bort alla obundna ohanterade virtuella hårddiskar.

```azurepowershell-interactive
# Set deleteUnattachedVHDs=1 if you want to delete unattached VHDs
# Set deleteUnattachedVHDs=0 if you want to see the Uri of the unattached VHDs
$deleteUnattachedVHDs=0
$storageAccounts = Get-AzStorageAccount
foreach($storageAccount in $storageAccounts){
    $storageKey = (Get-AzStorageAccountKey -ResourceGroupName $storageAccount.ResourceGroupName -Name $storageAccount.StorageAccountName)[0].Value
    $context = New-AzStorageContext -StorageAccountName $storageAccount.StorageAccountName -StorageAccountKey $storageKey
    $containers = Get-AzStorageContainer -Context $context
    foreach($container in $containers){
        $blobs = Get-AzStorageBlob -Container $container.Name -Context $context
        #Fetch all the Page blobs with extension .vhd as only Page blobs can be attached as disk to Azure VMs
        $blobs | Where-Object {$_.BlobType -eq 'PageBlob' -and $_.Name.EndsWith('.vhd')} | ForEach-Object { 
            #If a Page blob is not attached as disk then LeaseStatus will be unlocked
            if($_.ICloudBlob.Properties.LeaseStatus -eq 'Unlocked'){
                    if($deleteUnattachedVHDs -eq 1){
                        Write-Host "Deleting unattached VHD with Uri: $($_.ICloudBlob.Uri.AbsoluteUri)"
                        $_ | Remove-AzStorageBlob -Force
                        Write-Host "Deleted unattached VHD with Uri: $($_.ICloudBlob.Uri.AbsoluteUri)"
                    }
                    else{
                        $_.ICloudBlob.Uri.AbsoluteUri
                    }
            }
        }
    }
}
```

## <a name="next-steps"></a>Nästa steg

Mer information finns i [Ta bort lagringskonto](../../storage/common/storage-create-storage-account.md) och [Identifiera överblivna diskar med PowerShell](https://blogs.technet.microsoft.com/ukplatforms/2018/02/21/azure-cost-optimisation-series-identify-orphaned-disks-using-powershell/)
