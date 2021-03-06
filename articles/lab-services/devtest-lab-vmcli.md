---
title: Skapa och hantera virtuella datorer i DevTest Labs med Azure CLI
description: Lär dig hur du använder Azure DevTest Labs för att skapa och hantera virtuella datorer med Azure CLI
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: d3cd104e36cb407e9b1b833335869cac2c69d0ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76167058"
---
# <a name="create-and-manage-virtual-machines-with-devtest-labs-using-the-azure-cli"></a>Skapa och hantera virtuella datorer med DevTest Labs med Hjälp av Azure CLI
Den här snabbstarten hjälper dig att skapa, starta, ansluta, uppdatera och rensa en utvecklingsmaskin i ditt labb. 

Innan du börjar:

* Om ett labb inte har skapats finns instruktioner [här](devtest-lab-create-lab.md).

* [Installera Azure CLI](/cli/azure/install-azure-cli). Om du vill starta kör du az-inloggning för att skapa en anslutning med Azure. 

## <a name="create-and-verify-the-virtual-machine"></a>Skapa och verifiera den virtuella datorn 
Innan du kör DevTest Labs-relaterade kommandon anger du `az account set` lämplig Azure-kontext med kommandot:

```azurecli
az account set --subscription 11111111-1111-1111-1111-111111111111
```

Kommandot för att skapa en `az lab vm create`virtuell dator är: . Resursgruppen för labbet, labbnamnet och namnet på den virtuella datorn krävs alla. Resten av argumenten ändras beroende på vilken typ av virtuell dator.

Följande kommando skapar en Windows-baserad avbildning från Azure Market Place. Namnet på avbildningen är detsamma som du skulle se när du skapar en virtuell dator med Azure-portalen. 

```azurecli
az lab vm create --resource-group DtlResourceGroup --lab-name MyLab --name 'MyTestVm' --image "Visual Studio Community 2017 on Windows Server 2016 (x64)" --image-type gallery --size 'Standard_D2s_v3' --admin-username 'AdminUser' --admin-password 'Password1!'
```

Följande kommando skapar en virtuell dator baserat på en anpassad avbildning som är tillgänglig i labbet:

```azurecli
az lab vm create --resource-group DtlResourceGroup --lab-name MyLab --name 'MyTestVm' --image "My Custom Image" --image-type custom --size 'Standard_D2s_v3' --admin-username 'AdminUser' --admin-password 'Password1!'
```

Argumentet **bildtyp** har ändrats från **galleri** till **anpassat**. Namnet på avbildningen matchar vad du ser om du skulle skapa den virtuella datorn i Azure-portalen.

Följande kommando skapar en virtuell dator från en marketplace-avbildning med ssh-autentisering:

```azurecli
az lab vm create --lab-name sampleLabName --resource-group sampleLabResourceGroup --name sampleVMName --image "Ubuntu Server 16.04 LTS" --image-type gallery --size Standard_DS1_v2 --authentication-type  ssh --generate-ssh-keys --ip-configuration public 
```

Du kan också skapa virtuella datorer baserat på formler genom att ställa in parametern **bildtyp** till **formel**. Om du behöver välja ett specifikt virtuellt nätverk för den virtuella datorn använder du parametrarna **för vnet-namn** och **undernät.** Mer information finns i [az lab vm create](/cli/azure/lab/vm#az-lab-vm-create).

## <a name="verify-that-the-vm-is-available"></a>Kontrollera att den virtuella datorn är tillgänglig.
Använd `az lab vm show` kommandot för att kontrollera att den virtuella datorn är tillgänglig innan du startar och ansluter till den. 

```azurecli
az lab vm show --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup --expand 'properties($expand=ComputeVm,NetworkInterface)' --query '{status: computeVm.statuses[0].displayStatus, fqdn: fqdn, ipAddress: networkInterface.publicIpAddress}'
```
```json
{
  "fqdn": "lisalabvm.southcentralus.cloudapp.azure.com",
  "ipAddress": "13.85.228.112",
  "status": "Provisioning succeeded"
}
```

## <a name="start-and-connect-to-the-virtual-machine"></a>Starta och ansluta till den virtuella datorn
Följande exempelkommando startar en virtuell dator:

```azurecli
az lab vm start --lab-name sampleLabName --name sampleVMName --resource-group sampleLabResourceGroup
```

Anslut till en virtuell dator: [SSH](../virtual-machines/linux/mac-create-ssh-keys.md) eller [Fjärrskrivbord](../virtual-machines/windows/connect-logon.md).
```bash
ssh userName@ipAddressOrfqdn 
```

## <a name="update-the-virtual-machine"></a>Uppdatera den virtuella datorn
Följande exempelkommando tillämpar artefakter på en virtuell dator:

```azurecli
az lab vm apply-artifacts --lab-name  sampleLabName --name sampleVMName  --resource-group sampleResourceGroup  --artifacts @/artifacts.json
```

```json
[
  {
    "artifactId": "/artifactSources/public repo/artifacts/linux-java",
    "parameters": []
  },
  {
    "artifactId": "/artifactSources/public repo/artifacts/linux-install-nodejs",
    "parameters": []
  },
  {
    "artifactId": "/artifactSources/public repo/artifacts/linux-apt-package",
    "parameters": [
      {
        "name": "packages",
        "value": "abcd"
      },
      {
        "name": "update",
        "value": "true"
      },
      {
        "name": "options",
        "value": ""
      }
    ]
  } 
]
```

### <a name="list-artifacts-available-in-the-lab"></a>Lista artefakter som är tillgängliga i labbet

Om du vill visa en lista över artefakter som är tillgängliga i en virtuell dator i ett labb kör du följande kommandon.

**Cloud Shell - PowerShell**: meddelande om\`användningen av backtick ( ) före $ i $expand (dvs. "$expand):

```azurecli-interactive
az lab vm show --resource-group <resourcegroupname> --lab-name <labname> --name <vmname> --expand "properties(`$expand=artifacts)" --query "artifacts[].{artifactId: artifactId, status: status}"
```

**Cloud Shell - Bash:** lägg märke\\till användningen av snedstrecket ( ) tecknet framför $ i kommandot. 

```azurecli-interactive
az lab vm show --resource-group <resourcegroupname> --lab-name <labname> --name <vmname> --expand "properties(\$expand=artifacts)" --query "artifacts[].{artifactId: artifactId, status: status}"
```

Exempel på utdata: 

```json
[
  {
    "artifactId": "/subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.DevTestLab/labs/<lab name>/artifactSources/public repo/artifacts/windows-7zip",
    "status": "Succeeded"
  }
]
```

## <a name="stop-and-delete-the-virtual-machine"></a>Stoppa och ta bort den virtuella datorn    
Följande exempelkommando stoppar en virtuell dator.

```azurecli
az lab vm stop --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup
```

Ta bort en virtuell dator.
```azurecli
az lab vm delete --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup
```

## <a name="next-steps"></a>Nästa steg
Se följande innehåll: [Azure CLI-dokumentation för Azure DevTest Labs](/cli/azure/lab?view=azure-cli-latest). 
