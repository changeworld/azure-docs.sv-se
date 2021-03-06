---
title: Återställa Virtuella Azure-datorer med REST API
description: I den här artikeln kan du läsa om hur du hanterar återställningsåtgärder för Azure Virtual Machine Backup med REST API.
ms.topic: conceptual
ms.date: 09/12/2018
ms.assetid: b8487516-7ac5-4435-9680-674d9ecf5642
ms.openlocfilehash: 4990d815721ddbdde8e6eb6ebf8d6d3b49adc700
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74173372"
---
# <a name="restore-azure-virtual-machines-using-rest-api"></a>Återställa Virtuella Azure-datorer med REST API

När säkerhetskopieringen av en virtuell Azure-dator med Azure Backup har slutförts kan man återställa hela Virtuella Azure-datorer eller diskar eller filer från samma säkerhetskopia. I den här artikeln beskrivs hur du återställer en virtuell Azure-dator eller diskar med REST API.

För alla återställningsåtgärder måste man först identifiera den relevanta återställningspunkten.

## <a name="select-recovery-point"></a>Välj återställningspunkt

De tillgängliga återställningspunkterna för ett säkerhetskopieringsobjekt kan visas med hjälp av [rest-API:et för listans återställningspunkt](https://docs.microsoft.com/rest/api/backup/recoverypoints/list). Det är en enkel *GET-operation* med alla relevanta värden.

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/recoveryPoints?api-version=2019-05-13
```

Den `{containerName}` `{protectedItemName}` och är lika konstruerade [här](backup-azure-arm-userestapi-backupazurevms.md#example-responses-1). `{fabricName}`är "Azure".

*GET* URI har alla nödvändiga parametrar. Det finns inget behov av ytterligare en begäran organ

### <a name="responses"></a>Svar

|Namn  |Typ  |Beskrivning  |
|---------|---------|---------|
|200 OK     |   [RecoveryPointResourceList](https://docs.microsoft.com/rest/api/backup/recoverypoints/list#recoverypointresourcelist)      |       OK  |

#### <a name="example-response"></a>Exempelsvar

När *GET* URI har skickats returneras ett 200 (OK) svar.

```http
HTTP/1.1 200 OK
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id: 03453538-2f8d-46de-8374-143ccdf60f40
x-ms-client-request-id: c48f4436-ce3f-42da-b537-12710d4d1a24; c48f4436-ce3f-42da-b537-12710d4d1a24
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 14998
x-ms-correlation-request-id: 03453538-2f8d-46de-8374-143ccdf60f40
x-ms-routing-request-id: SOUTHINDIA:20180604T071851Z:03453538-2f8d-46de-8374-143ccdf60f40
Cache-Control: no-cache
Date: Mon, 04 Jun 2018 07:18:51 GMT
Server: Microsoft-IIS/8.0
X-Powered-By: ASP.NET

{
  "value": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/VM;testRG;testVM/recoveryPoints/20982486783671",
      "name": "20982486783671",
      "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints",
      "properties": {
        "objectType": "IaasVMRecoveryPoint",
        "recoveryPointType": "AppConsistent",
        "recoveryPointTime": "2018-06-04T06:06:00.5121087Z",
        "recoveryPointAdditionalInfo": "",
        "sourceVMStorageType": "NormalStorage",
        "isSourceVMEncrypted": false,
        "isInstantIlrSessionActive": false,
        "recoveryPointTierDetails": [
          {
            "type": 1,
            "status": 1
          },
          {
            "type": 2,
            "status": 1
          }
        ],
        "isManagedVirtualMachine": true,
        "virtualMachineSize": "Standard_A1_v2",
        "originalStorageAccountOption": false
      }
    },
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/VM;testRG;testVM/recoveryPoints/23358112038108",
      "name": "23358112038108",
      "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints",
      "properties": {
        "objectType": "IaasVMRecoveryPoint",
        "recoveryPointType": "CrashConsistent",
        "recoveryPointTime": "2018-06-03T06:20:56.3043878Z",
        "recoveryPointAdditionalInfo": "",
        "sourceVMStorageType": "NormalStorage",
        "isSourceVMEncrypted": false,
        "isInstantIlrSessionActive": false,
        "recoveryPointTierDetails": [
          {
            "type": 1,
            "status": 1
          },
          {
            "type": 2,
            "status": 1
          }
        ],
        "isManagedVirtualMachine": true,
        "virtualMachineSize": "Standard_A1_v2",
        "originalStorageAccountOption": false
      }
    },
......
```

Återställningspunkten identifieras med `{name}` fältet i ovanstående svar.

## <a name="restore-disks"></a>Återställa diskar

Om det finns ett behov av att anpassa skapandet av en virtuell dator från säkerhetskopieringsdata, kan man bara återställa diskar till ett valt lagringskonto och skapa en virtuell dator från dessa diskar enligt deras krav. Lagringskontot ska finnas i samma region som återställningstjänstvalvet och bör inte vara zonundret. Diskarna samt konfigurationen av den säkerhetskopierade virtuella datorn ("vmconfig.json") lagras i det angivna lagringskontot.

Utlösa återställningsdiskar är en *POST-begäran.* Mer information om åtgärden Återställ diskar finns i [REST API:et för "trigger restore"](https://docs.microsoft.com/rest/api/backup/restores/trigger).

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/recoveryPoints/{recoveryPointId}/restore?api-version=2019-05-13
```

Den `{containerName}` `{protectedItemName}` och är lika konstruerade [här](backup-azure-arm-userestapi-backupazurevms.md#example-responses-1). `{fabricName}`är "Azure" `{recoveryPointId}` och `{name}` är fältet för återställningspunkten som nämns [ovan](#example-response).

### <a name="create-request-body"></a>Skapa förfråningstext

För att utlösa en diskåterställning från en Azure VM-säkerhetskopia följer komponenterna i begärandetexten.

|Namn  |Typ  |Beskrivning  |
|---------|---------|---------|
|properties     | [IaaSVMRestoreRequest](https://docs.microsoft.com/rest/api/backup/restores/trigger#iaasvmrestorerequest)        |    RestoreRequestResourceProperties     |

Den fullständiga listan över definitioner av begärandetexten och annan information finns i [utlösaren Rest API-dokument](https://docs.microsoft.com/rest/api/backup/restores/trigger#request-body).

#### <a name="example-request"></a>Exempelbegäran

Följande begärandetext definierar egenskaper som krävs för att utlösa en diskåterställning.

```json
{
  "properties": {
    "objectType": "IaasVMRestoreRequest",
    "recoveryPointId": "20982486783671",
    "recoveryType": "RestoreDisks",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "storageAccountId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Storage/storageAccounts/testAccount",
    "region": "westus",
    "createNewCloudService": false,
    "originalStorageAccountOption": false,
    "encryptionDetails": {
      "encryptionEnabled": false
    }
  }
}
```

### <a name="response"></a>Svar

Utlösning av en återställningsdisk är en [asynkron åtgärd](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations). Det innebär att den här åtgärden skapar en annan åtgärd som måste spåras separat.

Två svar returneras: 202 (Godkänd) när en annan åtgärd skapas och sedan 200 (OK) när åtgärden är klar.

|Namn  |Typ  |Beskrivning  |
|---------|---------|---------|
|202 Accepterad     |         |     Accepterad    |

#### <a name="example-responses"></a>Exempel på svar

När du har skickat *POST* URI för att utlösa återställningsdiskar är det första svaret 202 (Accepterad) med ett platshuvud eller Azure-async-header.

```http
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 60
Azure-AsyncOperation: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationsStatus/781a0f18-e250-4d73-b059-5e9ffed4069e?api-version=2019-05-13
X-Content-Type-Options: nosniff
x-ms-request-id: 893fe372-8d6c-4c56-b589-45a95eeef95f
x-ms-client-request-id: a15ce064-25bd-4ac6-87e5-e3bc6ec65c0b; a15ce064-25bd-4ac6-87e5-e3bc6ec65c0b
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1198
x-ms-correlation-request-id: 893fe372-8d6c-4c56-b589-45a95eeef95f
x-ms-routing-request-id: SOUTHINDIA:20180604T130003Z:893fe372-8d6c-4c56-b589-45a95eeef95f
Cache-Control: no-cache
Date: Mon, 04 Jun 2018 13:00:03 GMT
Location: https://management.azure.com/subscriptions//subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationResults/781a0f18-e250-4d73-b059-5e9ffed4069e?api-version=2019-05-13
X-Powered-By: ASP.NET
```

Spåra sedan den resulterande åtgärden med hjälp av platshuvudet eller Azure-AsyncOperation-huvudet med ett enkelt *GET-kommando.*

```http
GET https://management.azure.com/subscriptions//subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationResults/781a0f18-e250-4d73-b059-5e9ffed4069e?api-version=2019-05-13
```

När åtgärden är klar returneras 200 (OK) med ID:t för det resulterande återställningsjobbet i svarstexten.

```http
HTTP/1.1 200 OK
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id: ea2a8011-eb83-4a4b-9ed2-e694070a966a
x-ms-client-request-id: a7f3a144-ed80-41ee-bffe-ae6a90c35a2f; a7f3a144-ed80-41ee-bffe-ae6a90c35a2f
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 14973
x-ms-correlation-request-id: ea2a8011-eb83-4a4b-9ed2-e694070a966a
x-ms-routing-request-id: SOUTHINDIA:20180604T130917Z:ea2a8011-eb83-4a4b-9ed2-e694070a966a
Cache-Control: no-cache
Date: Mon, 04 Jun 2018 13:09:17 GMT
Server: Microsoft-IIS/8.0
X-Powered-By: ASP.NET

{
  "id": "781a0f18-e250-4d73-b059-5e9ffed4069e",
  "name": "781a0f18-e250-4d73-b059-5e9ffed4069e",
  "status": "Succeeded",
  "startTime": "2018-06-04T13:00:03.8068176Z",
  "endTime": "2018-06-04T13:00:03.8068176Z",
  "properties": {
    "objectType": "OperationStatusJobExtendedInfo",
    "jobId": "3021262a-fb3a-4538-9b37-e3e97a386093"
  }
}
```

Eftersom säkerhetskopieringsjobbet är en tidskrävande åtgärd bör det spåras enligt beskrivningen i [övervakarjobben med REST API-dokument](backup-azure-arm-userestapi-managejobs.md#tracking-the-job).

När det tidskrävande jobbet är klart kommer diskarna och konfigurationen av den säkerhetskopierade virtuella datorn ("VMConfig.json") att finnas i det angivna lagringskontot.

## <a name="restore-as-another-virtual-machine"></a>Återställa som en annan virtuell dator

[Välj återställningspunkten](#select-recovery-point) och skapa begärandetexten enligt nedan för att skapa en annan Virtuell Azure-dator med data från återställningspunkten.

Följande begärandetext definierar egenskaper som krävs för att utlösa en återställning av en virtuell dator.

```json
{
  "parameters": {
        "subscriptionId": "00000000-0000-0000-0000-000000000000",
        "resourceGroupName": "testVaultRG",
        "vaultName": "testVault",
        "fabricName": "Azure",
        "containerName": "IaasVMContainer;iaasvmcontainerv2;testRG;testVM",
        "protectedItemName": "VM;iaasvmcontainerv2;testRG;testVM",
        "recoveryPointId": "348916168024334",
        "api-version": "2019-05-13",
      "parameters": {
        "properties": {
          "objectType":  "IaasVMRestoreRequest",
          "recoveryPointId": "348916168024334",
          "recoveryType": "AlternateLocation",
          "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
          "targetVirtualMachineId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/targetRG/providers/Microsoft.Compute/virtualmachines/targetVM",
          "targetResourceGroupId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/targetRG",
          "storageAccountId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Storage/storageAccounts/testingAccount",
          "virtualNetworkId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/targetRG/providers/Microsoft.Network/virtualNetworks/testNet",
          "subnetId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/targetRG/providers/Microsoft.Network/virtualNetworks/testNet/subnets/default",
          "region": "westus",
          "createNewCloudService": false,
          "originalStorageAccountOption": false,
          "encryptionDetails": {
            "encryptionEnabled": false
          }
        }
      }
    }
}
```

Svaret bör hanteras på samma sätt som [förklaras ovan för att återställa diskar](#response).

## <a name="next-steps"></a>Nästa steg

Mer information om AZURE Backup REST API:er finns i följande dokument:

- [REST-API för Azure Recovery Services-provider](/rest/api/recoveryservices/)
- [Komma igång med Azure REST API](/rest/api/azure/)
