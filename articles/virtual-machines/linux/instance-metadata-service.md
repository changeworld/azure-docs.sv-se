---
title: Azure-instansmetadatatjänst
description: RESTful gränssnitt för att få information om Linux virtuella datorer beräkning, nätverk och kommande underhåll händelser.
services: virtual-machines-linux
author: KumariSupriya
manager: paulmey
ms.service: virtual-machines-linux
ms.subservice: monitoring
ms.topic: article
ms.workload: infrastructure-services
ms.date: 03/30/2020
ms.author: sukumari
ms.reviewer: azmetadata
ms.openlocfilehash: 0971b542065972a8f150083245e4ed31e42e2c67
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521632"
---
# <a name="azure-instance-metadata-service"></a>Azure-instansmetadatatjänst

Azure Instance Metadata Service (IMDS) innehåller information om instanser som körs för virtuella datorer och kan användas för att hantera och konfigurera dina virtuella datorer.
Informationen omfattar SKU, nätverkskonfiguration och kommande underhållshändelser. En fullständig lista över tillgängliga data finns i [metadata-API:er](#metadata-apis).

Azures instansmetadatatjänst är en REST-slutpunkt som är tillgänglig för alla virtuella IaaS-datorer som skapats via [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/).
Slutpunkten är tillgänglig på en välkänd icke-dirigerbar IP-adress (`169.254.169.254`) som endast kan nås från den virtuella datorn.

> [!IMPORTANT]
> Den här tjänsten är **allmänt tillgänglig** i alla Azure-regioner.  Den får regelbundet uppdateringar för att exponera ny information om instanser av virtuella datorer. Den här sidan visar de uppdaterade [metadata-API:er som](#metadata-apis) är tillgängliga.

## <a name="service-availability"></a>Tjänstens tillgänglighet

Tjänsten är tillgänglig i allmänt tillgängliga Azure-regioner. Alla API-versioner kanske inte är tillgängliga i alla Azure-regioner.

Regioner                                        | Tillgänglighet?                                 | Versioner som stöds
-----------------------------------------------|-----------------------------------------------|-----------------
[Alla allmänt tillgängliga globala Azure-regioner](https://azure.microsoft.com/regions/)     | Allmänt tillgängliga | 2017-04-02, 2017-08-01, 2017-12-01, 2018-02-01, 2018-04-02, 2018-10-01, 2019-02-01, 2019-03-11, 2019-04-30, 2019-06-01, 2019-06-04, 2019-08-01, 2019-08-15
[Azure Government](https://azure.microsoft.com/overview/clouds/government/)              | Allmänt tillgängliga | 2017-04-02, 2017-08-01, 2017-12-01, 2018-02-01, 2018-04-02, 2018-10-01, 2019-02-01, 2019-03-11, 2019-04-30, 2019-06-01, 2019-06-04, 2019-08-01, 2019-08-15
[Azure Kina 21Vianet](https://www.azure.cn/)                                            | Allmänt tillgängliga | 2017-04-02, 2017-08-01, 2017-12-01, 2018-02-01, 2018-04-02, 2018-10-01, 2019-02-01, 2019-03-11, 2019-04-30, 2019-06-01, 2019-06-04, 2019-08-01, 2019-08-15
[Azure Tyskland](https://azure.microsoft.com/overview/clouds/germany/)                    | Allmänt tillgängliga | 2017-04-02, 2017-08-01, 2017-12-01, 2018-02-01, 2018-04-02, 2018-10-01, 2019-02-01, 2019-03-11, 2019-04-30, 2019-06-01, 2019-06-04, 2019-08-01, 2019-08-15

Versionen 2019-11-01 distribueras för närvarande och kanske inte är tillgänglig i alla regioner.

Den här tabellen uppdateras när det finns tjänstuppdateringar och/eller nya versioner som stöds tillgängliga.

Om du vill prova instansmetadatatjänsten skapar du en virtuell dator från [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/) eller [Azure-portalen](https://portal.azure.com) i ovanstående regioner och följer exemplen nedan.
Ytterligare exempel på hur du frågar IMDS finns på [Azure Instance Metadata Samples](https://github.com/microsoft/azureimds)

## <a name="usage"></a>Användning

### <a name="versioning"></a>Versionshantering

Instansmetadatatjänsten är version av versionen och det är obligatoriskt att ange API-versionen i HTTP-begäran.

Du kan se de senaste versionerna i den här [tillgänglighetstabellen](#service-availability).

När nyare versioner läggs till kan äldre versioner fortfarande nås för kompatibilitet om skripten har beroenden av specifika dataformat.

När ingen version har angetts returneras ett fel med en lista över de senaste versionerna som stöds.

> [!NOTE]
> Svaret är en JSON-sträng. Följande exempelsvar är ganska utskrivet för läsbarhet.

**Förfrågan**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance"
```

**Svar**

```json
{
    "error": "Bad request. api-version was not specified in the request. For more information refer to aka.ms/azureimds",
    "newest-versions": [
        "2018-10-01",
        "2018-04-02",
        "2018-02-01"
    ]
}
```

### <a name="using-headers"></a>Använda rubriker

När du frågar instansmetadatatjänsten `Metadata: true` måste du ange huvudet för att säkerställa att begäran inte omdirigerades oavsiktligt.

### <a name="retrieving-metadata"></a>Hämta metadata

Instansmetadata är tillgängliga för att köra virtuella datorer som skapats/hanterats med [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/). Få tillgång till alla datakategorier för en instans för virtuella datorer med hjälp av följande begäran:

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-08-01"
```

> [!NOTE]
> Alla instansmetadatafrågor är skiftlägeskänsliga.

### <a name="data-output"></a>Datautdata

Som standard returnerar instansmetadata tjänsten`Content-Type: application/json`data i JSON-format ( ). Olika API:er returnerar dock data i olika format om så önskas.
Följande tabell är en referens till andra api:er för dataformat som kan stödja.

API | Standarddataformat | Andra format
--------|---------------------|--------------
/instans | json | text
/scheduledevents | json | ingen
/intygas | json | ingen

Om du vill komma åt ett svarsformat som inte är standard anger du det begärda formatet som en frågesträngparameter i begäran. Ett exempel:

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-08-01&format=text"
```

> [!NOTE]
> För bladnoder `format=json` fungerar inte lövnoderna. För dessa `format=text` frågor måste uttryckligen anges om standardformatet är json.

### <a name="security"></a>Säkerhet

Slutpunkten för instansmetadatatjänsten är endast tillgänglig från den virtuella datorinstansen som körs på en IP-adress som inte är dirigerbar. Dessutom avvisas alla `X-Forwarded-For` begäranden med ett huvud av tjänsten.
Begäranden måste `Metadata: true` också innehålla ett huvud för att säkerställa att den faktiska begäran var direkt avsedd och inte en del av oavsiktlig omdirigering.

### <a name="error"></a>Fel

Om det inte finns något dataelement eller en felaktig begäran returnerar instansmetadatatjänsten standard-HTTP-fel. Ett exempel:

HTTP-statuskod | Orsak
----------------|-------
200 OK |
400 dålig begäran | Namn `Metadata: true` som saknas eller formatet saknas när en lövnod efterfrågas
404 – Hittades inte | Det begärda elementet finns inte
405 Metod är inte tillåten | Endast `GET` begäranden stöds
410 Borta | Försök igen efter en tid i högst 70 sekunder
429 För många förfrågningar | API:et stöder för närvarande högst 5 frågor per sekund
500 servicefel     | Försök igen efter en tid

### <a name="examples"></a>Exempel

> [!NOTE]
> Alla API-svar är JSON-strängar. Alla följande exempelsvar är ganska utskrivna för läsbarhet.

#### <a name="retrieving-network-information"></a>Hämta nätverksinformation

**Förfrågan**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/network?api-version=2017-08-01"
```

**Svar**

> [!NOTE]
> Svaret är en JSON-sträng. Följande exempelsvar är ganska utskrivet för läsbarhet.

```json
{
  "interface": [
    {
      "ipv4": {
        "ipAddress": [
          {
            "privateIpAddress": "10.1.0.4",
            "publicIpAddress": "X.X.X.X"
          }
        ],
        "subnet": [
          {
            "address": "10.1.0.0",
            "prefix": "24"
          }
        ]
      },
      "ipv6": {
        "ipAddress": []
      },
      "macAddress": "000D3AF806EC"
    }
  ]
}

```

#### <a name="retrieving-public-ip-address"></a>Hämtar offentlig IP-adress

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/network/interface/0/ipv4/ipAddress/0/publicIpAddress?api-version=2017-08-01&format=text"
```

#### <a name="retrieving-all-metadata-for-an-instance"></a>Hämta alla metadata för en instans

**Förfrågan**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2019-06-01"
```

**Svar**

> [!NOTE]
> Svaret är en JSON-sträng. Följande exempelsvar är ganska utskrivet för läsbarhet.

```json
{
  "compute": {
    "azEnvironment": "AzurePublicCloud",
    "customData": "",
    "location": "centralus",
    "name": "negasonic",
    "offer": "lampstack",
    "osType": "Linux",
    "placementGroupId": "",
    "plan": {
        "name": "5-6",
        "product": "lampstack",
        "publisher": "bitnami"
    },
    "platformFaultDomain": "0",
    "platformUpdateDomain": "0",
    "provider": "Microsoft.Compute",
    "publicKeys": [],
    "publisher": "bitnami",
    "resourceGroupName": "myrg",
    "resourceId": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/myrg/providers/Microsoft.Compute/virtualMachines/negasonic",
    "sku": "5-6",
    "storageProfile": {
        "dataDisks": [
          {
            "caching": "None",
            "createOption": "Empty",
            "diskSizeGB": "1024",
            "image": {
              "uri": ""
            },
            "lun": "0",
            "managedDisk": {
              "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampledatadiskname",
              "storageAccountType": "Standard_LRS"
            },
            "name": "exampledatadiskname",
            "vhd": {
              "uri": ""
            },
            "writeAcceleratorEnabled": "false"
          }
        ],
        "imageReference": {
          "id": "",
          "offer": "UbuntuServer",
          "publisher": "Canonical",
          "sku": "16.04.0-LTS",
          "version": "latest"
        },
        "osDisk": {
          "caching": "ReadWrite",
          "createOption": "FromImage",
          "diskSizeGB": "30",
          "diffDiskSettings": {
            "option": "Local"
          },
          "encryptionSettings": {
            "enabled": "false"
          },
          "image": {
            "uri": ""
          },
          "managedDisk": {
            "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampleosdiskname",
            "storageAccountType": "Standard_LRS"
          },
          "name": "exampleosdiskname",
          "osType": "Linux",
          "vhd": {
            "uri": ""
          },
          "writeAcceleratorEnabled": "false"
        }
    },
    "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "tags": "Department:IT;Environment:Prod;Role:WorkerRole",
    "version": "7.1.1902271506",
    "vmId": "13f56399-bd52-4150-9748-7190aae1ff21",
    "vmScaleSetName": "",
    "vmSize": "Standard_A1_v2",
    "zone": "1"
  },
  "network": {
    "interface": [
      {
        "ipv4": {
          "ipAddress": [
            {
              "privateIpAddress": "10.1.2.5",
              "publicIpAddress": "X.X.X.X"
            }
          ],
          "subnet": [
            {
              "address": "10.1.2.0",
              "prefix": "24"
            }
          ]
        },
        "ipv6": {
          "ipAddress": []
        },
        "macAddress": "000D3A36DDED"
      }
    ]
  }
}
```

## <a name="metadata-apis"></a>Api:er för metadata

Följande API:er är tillgängliga via slutpunkten för metadata:

Data | Beskrivning | Version införd
-----|-------------|-----------------------
Belagt | Se [bestyrkade data](#attested-data) | 2018-10-01
identity | Hanterade identiteter för Azure-resurser. Se [hämta en åtkomsttoken](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md) | 2018-02-01
Instans | Se [instans-API](#instance-api) | 2017-04-02
schemalagda händelser | Se [schemalagda händelser](scheduled-events.md) | 2017-08-01

### <a name="instance-api"></a>Instans-API

Följande beräkningskategorier är tillgängliga via instans-API:et:

> [!NOTE]
> Via metadataslutpunkten används följande kategorier via instans/beräkning

Data | Beskrivning | Version införd
-----|-------------|-----------------------
azMiljö | Azure-miljö där den virtuella datorn körs i | 2018-10-01
customData (anpassadedata) | Den här funktionen är för närvarande inaktiverad och vi uppdaterar den här dokumentationen när den blir tillgänglig | 2019-02-01
location | Azure Region den virtuella datorn körs i | 2017-04-02
namn | Den virtuella datorns namn | 2017-04-02
offer | Erbjudandeinformation för vm-avbildningen och finns endast för avbildningar som distribueras från Azure-avbildningsgalleriet | 2017-04-02
osType (olika) | Linux eller Windows | 2017-04-02
placeringGroupId | [Placeringsgrupp](../../virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md) för skalningsuppsättningen för virtuella datorer | 2017-08-01
planera | [Planera](https://docs.microsoft.com/rest/api/compute/virtualmachines/createorupdate#plan) att innehålla namn, produkt och utgivare för en virtuell dator om det är en Azure Marketplace-avbildning | 2018-04-02
plattformUppdateDomain |  [Uppdatera domän](manage-availability.md) som den virtuella datorn körs i | 2017-04-02
plattformFaultDomain | [Feldomän](manage-availability.md) som den virtuella datorn körs i | 2017-04-02
Leverantör | Leverantör av den virtuella datorn | 2018-10-01
publicKeys (offentliga Tangenter) | [Insamling av offentliga nycklar](https://docs.microsoft.com/rest/api/compute/virtualmachines/createorupdate#sshpublickey) som tilldelats den virtuella datorn och sökvägar | 2018-04-02
utgivare | Utgivare av vm-avbildningen | 2017-04-02
resourceGroupName | [Resursgrupp](../../azure-resource-manager/management/overview.md) för din virtuella dator | 2017-08-01
resourceId | [Resursens fullständigt kvalificerade](https://docs.microsoft.com/rest/api/resources/resources/getbyid) ID | 2019-03-11
sku | Specifik SKU för vm-avbildningen | 2017-04-02
lagringProfile | Se [lagringsprofil](#storage-profile) | 2019-06-01
subscriptionId | Azure-prenumeration för den virtuella datorn | 2017-08-01
tags | [Taggar](../../azure-resource-manager/management/tag-resources.md) för din virtuella dator  | 2017-08-01
taggarLista | Taggar formaterade som en JSON-matris för enklare programmatisk tolkning  | 2019-06-04
version | Version av vm-avbildningen | 2017-04-02
vmId (vmId) | [Unik identifierare](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) för den virtuella datorn | 2017-04-02
vmScaleSetName | [Skaluppsättning för virtuell dator Namn](../../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) på skaluppsättningen för den virtuella datorn | 2017-12-01
vmSize | [Storlek på virtuell dator](sizes.md) | 2017-04-02
zon | [Tillgänglighetszon](../../availability-zones/az-overview.md) för din virtuella dator | 2017-12-01

Följande nätverkskategorier är tillgängliga via instans-API:et:

> [!NOTE]
> Via metadataslutpunkten används följande kategorier via instans/nätverk/gränssnitt

Data | Beskrivning | Version införd
-----|-------------|-----------------------
ipv4/privateIpAddress | Lokal IPv4-adress för den virtuella datorn | 2017-04-02
ipv4/publicIpAddress | Offentlig IPv4-adress för den virtuella datorn | 2017-04-02
undernät/adress | Undernätsadress för den virtuella datorn | 2017-04-02
undernät/prefix | Prefix för undernät, exempel 24 | 2017-04-02
ipv6/ipAddress | Lokal IPv6-adress för den virtuella datorn | 2017-04-02
macAddress | VM-mac-adress | 2017-04-02

## <a name="attested-data"></a>Intygade data

En del av scenariot som betjänas av instansmetadatatjänsten är att ge garantier för att data som tillhandahålls kommer från Azure. Vi signerar en del av den här informationen så att marketplace-avbildningar kan vara säkra på att det är deras avbildning som körs på Azure.

### <a name="example-attested-data"></a>Exempel bedömade data

> [!NOTE]
> Alla API-svar är JSON-strängar. Följande exempelsvar är ganska utskrivna för läsbarhet.

 **Förfrågan**

 ```bash
curl -H Metadata:true "http://169.254.169.254/metadata/attested/document?api-version=2018-10-01&nonce=1234567890"

```

Api-version är ett obligatoriskt fält. Se [avsnittet tjänsttillgänglighet](#service-availability) för API-versioner som stöds.
Nonce är en valfri 10-siffrig sträng. Om det inte anges returnerar IMDS den aktuella UTC-tidsstämpeln i stället. På grund av IMDS cachelagringsmekanism kan ett tidigare cachelagrat nonce-värde returneras.

 **Svar**

> [!NOTE]
> Svaret är en JSON-sträng. Följande exempelsvar är ganska utskrivet för läsbarhet.

 ```json
{
 "encoding":"pkcs7","signature":"MIIEEgYJKoZIhvcNAQcCoIIEAzCCA/8CAQExDzANBgkqhkiG9w0BAQsFADCBugYJKoZIhvcNAQcBoIGsBIGpeyJub25jZSI6IjEyMzQ1NjY3NjYiLCJwbGFuIjp7Im5hbWUiOiIiLCJwcm9kdWN0IjoiIiwicHVibGlzaGVyIjoiIn0sInRpbWVTdGFtcCI6eyJjcmVhdGVkT24iOiIxMS8yMC8xOCAyMjowNzozOSAtMDAwMCIsImV4cGlyZXNPbiI6IjExLzIwLzE4IDIyOjA4OjI0IC0wMDAwIn0sInZtSWQiOiIifaCCAj8wggI7MIIBpKADAgECAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBBAUAMCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tMB4XDTE4MTEyMDIxNTc1N1oXDTE4MTIyMDIxNTc1NlowKzEpMCcGA1UEAxMgdGVzdHN1YmRvbWFpbi5tZXRhZGF0YS5henVyZS5jb20wgZ8wDQYJKoZIhvcNAQEBBQADgY0AMIGJAoGBAML/tBo86ENWPzmXZ0kPkX5dY5QZ150mA8lommszE71x2sCLonzv4/UWk4H+jMMWRRwIea2CuQ5RhdWAHvKq6if4okKNt66fxm+YTVz9z0CTfCLmLT+nsdfOAsG1xZppEapC0Cd9vD6NCKyE8aYI1pliaeOnFjG0WvMY04uWz2MdAgMBAAGjYDBeMFwGA1UdAQRVMFOAENnYkHLa04Ut4Mpt7TkJFfyhLTArMSkwJwYDVQQDEyB0ZXN0c3ViZG9tYWluLm1ldGFkYXRhLmF6dXJlLmNvbYIQZ8VuSofHbJRAQNBNpiASdDANBgkqhkiG9w0BAQQFAAOBgQCLSM6aX5Bs1KHCJp4VQtxZPzXF71rVKCocHy3N9PTJQ9Fpnd+bYw2vSpQHg/AiG82WuDFpPReJvr7Pa938mZqW9HUOGjQKK2FYDTg6fXD8pkPdyghlX5boGWAMMrf7bFkup+lsT+n2tRw2wbNknO1tQ0wICtqy2VqzWwLi45RBwTGB6DCB5QIBATA/MCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBCwUAMA0GCSqGSIb3DQEBAQUABIGAld1BM/yYIqqv8SDE4kjQo3Ul/IKAVR8ETKcve5BAdGSNkTUooUGVniTXeuvDj5NkmazOaKZp9fEtByqqPOyw/nlXaZgOO44HDGiPUJ90xVYmfeK6p9RpJBu6kiKhnnYTelUk5u75phe5ZbMZfBhuPhXmYAdjc7Nmw97nx8NnprQ="
}
```

Signaturbloben är en [pkcs7-signerad](https://aka.ms/pkcs7) version av dokumentet. Den innehåller certifikatet som används för signering tillsammans med vm-information som vmId, sku, nonce, subscriptionId, timeStamp för att skapa och förfalla dokumentet och planen information om avbildningen. Planen information är endast ifylld för Azure Market plats avbildningar. Certifikatet kan extraheras från svaret och användas för att verifiera att svaret är giltigt och kommer från Azure.

## <a name="example-scenarios-for-usage"></a>Exempelscenarier för användning  

### <a name="tracking-vm-running-on-azure"></a>Spåra virtuell dator som körs i Azure

Som tjänsteleverantör kan du behöva spåra antalet virtuella datorer som kör programvaran eller har agenter som behöver spåra den virtuella datorns unika egenskaper. Om du vill kunna få ett unikt ID för en virtuell dator använder du fältet `vmId` från instansmetadatatjänsten.

**Förfrågan**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/vmId?api-version=2017-08-01&format=text"
```

**Svar**

```text
5c08b38e-4d57-4c23-ac45-aca61037f084
```

### <a name="placement-of-containers-data-partitions-based-faultupdate-domain"></a>Placering av containrar, datapartitionsbaserad feldomän/uppdateringsdomän

För vissa scenarier är placering av olika datarepliker av största vikt. Till exempel kan [HDFS replikplacering](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html#Replica_Placement:_The_First_Baby_Steps) eller behållarplacering via `platformFaultDomain` `platformUpdateDomain` en [orchestrator](https://kubernetes.io/docs/user-guide/node-selection/) du behöva känna till och den virtuella datorn körs på.
Du kan också använda [tillgänglighetszoner](../../availability-zones/az-overview.md) för instanser för att fatta dessa beslut.
Du kan fråga dessa data direkt via instansmetadatatjänsten.

**Förfrågan**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-08-01&format=text"
```

**Svar**

```text
0
```

### <a name="getting-more-information-about-the-vm-during-support-case"></a>Hämta mer information om den virtuella datorn under supportbegäran

Som tjänsteleverantör kan du få ett supportsamtal där du vill veta mer om den virtuella datorn. Be kunden att dela beräkningsmetadata kan ge grundläggande information för supporttekniker att veta om vilken typ av virtuell dator på Azure.

**Förfrågan**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute?api-version=2019-06-01"
```

**Svar**

> [!NOTE]
> Svaret är en JSON-sträng. Följande exempelsvar är ganska utskrivet för läsbarhet.

```json
{
    "azEnvironment": "AzurePublicCloud",
    "customData": "",
    "location": "centralus",
    "name": "negasonic",
    "offer": "lampstack",
    "osType": "Linux",
    "placementGroupId": "",
    "plan": {
        "name": "5-6",
        "product": "lampstack",
        "publisher": "bitnami"
    },
    "platformFaultDomain": "0",
    "platformUpdateDomain": "0",
    "provider": "Microsoft.Compute",
    "publicKeys": [],
    "publisher": "bitnami",
    "resourceGroupName": "myrg",
    "resourceId": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/myrg/providers/Microsoft.Compute/virtualMachines/negasonic",
    "sku": "5-6",
    "storageProfile": {
        "dataDisks": [
          {
            "caching": "None",
            "createOption": "Empty",
            "diskSizeGB": "1024",
            "image": {
              "uri": ""
            },
            "lun": "0",
            "managedDisk": {
              "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampledatadiskname",
              "storageAccountType": "Standard_LRS"
            },
            "name": "exampledatadiskname",
            "vhd": {
              "uri": ""
            },
            "writeAcceleratorEnabled": "false"
          }
        ],
        "imageReference": {
          "id": "",
          "offer": "UbuntuServer",
          "publisher": "Canonical",
          "sku": "16.04.0-LTS",
          "version": "latest"
        },
        "osDisk": {
          "caching": "ReadWrite",
          "createOption": "FromImage",
          "diskSizeGB": "30",
          "diffDiskSettings": {
            "option": "Local"
          },
          "encryptionSettings": {
            "enabled": "false"
          },
          "image": {
            "uri": ""
          },
          "managedDisk": {
            "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampleosdiskname",
            "storageAccountType": "Standard_LRS"
          },
          "name": "exampleosdiskname",
          "osType": "Linux",
          "vhd": {
            "uri": ""
          },
          "writeAcceleratorEnabled": "false"
        }
    },
    "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "tags": "Department:IT;Environment:Test;Role:WebRole",
    "version": "7.1.1902271506",
    "vmId": "13f56399-bd52-4150-9748-7190aae1ff21",
    "vmScaleSetName": "",
    "vmSize": "Standard_A1_v2",
    "zone": "1"
}
```

### <a name="getting-azure-environment-where-the-vm-is-running"></a>Hämta Azure-miljön där den virtuella datorn körs

Azure har olika suveräna moln som [Azure Government](https://azure.microsoft.com/overview/clouds/government/). Ibland behöver du Azure-miljön för att kunna fatta några körningsbeslut. Följande exempel visar hur du kan uppnå detta beteende.

**Förfrågan**
```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/azEnvironment?api-version=2018-10-01&format=text"
```

**Svar**

```bash
AzurePublicCloud
```

Molnet och värdena i Azure-miljön visas nedan.

 Molnet   | Azure-miljö
---------|-----------------
[Alla allmänt tillgängliga globala Azure-regioner](https://azure.microsoft.com/regions/)     | AzurePublicCloud (På andra)
[Azure Government](https://azure.microsoft.com/overview/clouds/government/)              | AzureUSGovernmentCloud
[Azure Kina 21Vianet](https://azure.microsoft.com/global-infrastructure/china/)         | AzureChinaCloud
[Azure Tyskland](https://azure.microsoft.com/overview/clouds/germany/)                    | AzureGermanCloud

### <a name="getting-the-tags-for-the-vm"></a>Hämta taggarna för den virtuella datorn

Taggar kan ha tillämpats på din virtuella Azure-dator för att logiskt ordna dem till en taxonomi. Taggarna som tilldelats en virtuell dator kan hämtas med hjälp av begäran nedan.

**Förfrågan**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/tags?api-version=2018-10-01&format=text"
```

**Svar**

```text
Department:IT;Environment:Test;Role:WebRole
```

Fältet `tags` är en sträng med taggarna avgränsade av semikolon. Detta kan vara ett problem om semikolon används i taggarna själva. Om en tolk skrivs för att programmässigt extrahera taggarna, bör du lita på `tagsList` fältet som är en JSON-matris utan avgränsare, och därmed lättare att tolka.

**Förfrågan**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/tagsList?api-version=2019-06-04&format=json"
```

**Svar**

```json
[
  {
    "name": "Department",
    "value": "IT"
  },
  {
    "name": "Environment",
    "value": "Test"
  },
  {
    "name": "Role",
    "value": "WebRole"
  }
]
```

### <a name="validating-that-the-vm-is-running-in-azure"></a>Verifiera att den virtuella datorn körs i Azure

Marketplace-leverantörer vill se till att deras programvara är licensierad för att köras endast i Azure. Om någon kopierar den virtuella hårddisken ut till lokala, då de bör ha möjlighet att upptäcka det. Genom att anropa till Instansmetadatatjänsten kan Marketplace-leverantörer få signerade data som endast garanterar svar från Azure.

> [!NOTE]
> Kräver jq installeras.

**Förfrågan**

 ```bash
  # Get the signature
   curl  --silent -H Metadata:True http://169.254.169.254/metadata/attested/document?api-version=2019-04-30 | jq -r '.["signature"]' > signature
  # Decode the signature
  base64 -d signature > decodedsignature
  #Get PKCS7 format
  openssl pkcs7 -in decodedsignature -inform DER -out sign.pk7
  # Get Public key out of pkc7
  openssl pkcs7 -in decodedsignature -inform DER  -print_certs -out signer.pem
  #Get the intermediate certificate
  wget -q -O intermediate.cer "$(openssl x509 -in signer.pem -text -noout | grep " CA Issuers -" | awk -FURI: '{print $2}')"
  openssl x509 -inform der -in intermediate.cer -out intermediate.pem
  #Verify the contents
  openssl smime -verify -in sign.pk7 -inform pem -noverify
 ```

 **Svar**

```json
Verification successful
{"nonce":"20181128-001617",
  "plan":
    {
     "name":"",
     "product":"",
     "publisher":""
    },
"timeStamp":
  {
    "createdOn":"11/28/18 00:16:17 -0000",
    "expiresOn":"11/28/18 06:16:17 -0000"
  },
"vmId":"d3e0e374-fda6-4649-bbc9-7f20dc379f34",
"subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
"sku": "RS3-Pro"
}
```

Data | Beskrivning
-----|------------
Nonce | Användaren som tillhandahålls valfri sträng med begäran. Om ingen nonce angavs i begäran returneras den aktuella UTC-tidsstämpeln
planera | [Planera](https://docs.microsoft.com/rest/api/compute/virtualmachines/createorupdate#plan) för en virtuell dator i den är en Azure Marketplace-avbildning, innehåller namn, produkt och utgivare
tidsstämpel/createdOn | DEN UTC-tidsstämpel vid vilken det första signerade dokumentet skapades
tidsstämpel/upphör Att gällaPå | DEN UTC-tidsstämpel där det signerade dokumentet upphör att gälla
vmId (vmId) |  [Unik identifierare](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) för den virtuella datorn
subscriptionId | Azure-prenumeration för den virtuella datorn, som introducerades i`2019-04-30`
sku | Specifik SKU för vm-avbildningen, som introducerades i`2019-11-01`

#### <a name="verifying-the-signature"></a>Verifiera signaturen

När du har fått signaturen ovan kan du kontrollera att signaturen kommer från Microsoft. Du kan också verifiera det mellanliggande certifikatet och certifikatkedjan. Slutligen kan du kontrollera att prenumerations-ID:t är korrekt.

> [!NOTE]
> Certifikatet för offentligt moln och suveränt moln kommer att vara annorlunda.

 Molnet | Certifikat
---------|-----------------
[Alla allmänt tillgängliga globala Azure-regioner](https://azure.microsoft.com/regions/)     | *.metadata.azure.com
[Azure Government](https://azure.microsoft.com/overview/clouds/government/)              | *.metadata.azure.us
[Azure Kina 21Vianet](https://azure.microsoft.com/global-infrastructure/china/)         | *.metadata.azure.cn
[Azure Tyskland](https://azure.microsoft.com/overview/clouds/germany/)                    | *.metadata.microsoftazure.de

Det finns ett känt problem kring certifikatet som används för signering. Certifikaten kanske inte har en `metadata.azure.com` exakt matchning för offentliga moln. Därför bör certifieringsvalideringen `.metadata.azure.com` tillåta ett vanligt namn från alla underdomäner.

```bash

# Verify the subject name for the main certificate
openssl x509 -noout -subject -in signer.pem
# Verify the issuer for the main certificate
openssl x509 -noout -issuer -in signer.pem
#Validate the subject name for intermediate certificate
openssl x509 -noout -subject -in intermediate.pem
# Verify the issuer for the intermediate certificate
openssl x509 -noout -issuer -in intermediate.pem
# Verify the certificate chain, for Azure China 21Vianet the intermediate certificate will be from DigiCert Global Root CA
openssl verify -verbose -CAfile /etc/ssl/certs/Baltimore_CyberTrust_Root.pem -untrusted intermediate.pem signer.pem
```

I de fall där det mellanliggande certifikatet inte kan hämtas på grund av nätverksbegränsningar under valideringen kan det mellanliggande certifikatet fästas. Azure kommer dock att rulla över certifikaten enligt standard PKI-praxis. De fästa certifikaten måste uppdateras när överrullning sker. När en ändring för att uppdatera det mellanliggande certifikatet planeras uppdateras Azure-bloggen och Azure-kunder meddelas. Mellanliggande certifikat hittar du [här](https://www.microsoft.com/pki/mscorp/cps/default.htm). Mellanliggande certifikat för var och en av regionerna kan vara olika.

> [!NOTE]
>Det mellanliggande certifikatet för Azure China 21Vianet kommer att vara från DigiCert Global Root CA i stället för Baltimore.
Även om du hade fäst mellanliggande certifikat för Azure Kina som en del av root chain authority change, måste mellanliggande certifikat uppdateras.

### <a name="storage-profile"></a>Lagringsprofil

Instansmetadatatjänsten kan ge information om lagringsdiskar som är associerade med den virtuella datorn. Dessa data finns på instansen/beräkning/storageProfile-slutpunkten.

Lagringsprofilen för en virtuell dator är indelad i tre kategorier - avbildningsreferens, OS-diskar och datadiskar.

Bildreferensobjektet innehåller följande information om OS-avbildningen:

Data    | Beskrivning
--------|-----------------
id      | Resurs-ID
offer   | Erbjudande av plattformen eller marknadsplats bild
utgivare | Bildutgivare
sku     | Bild sku
version | Version av plattformen eller marketplace-avbildningen

OS-diskobjektet innehåller följande information om OS-disken som används av den virtuella datorn:

Data    | Beskrivning
--------|-----------------
Cachelagring | Krav på cachelagring
createOption | Information om hur den virtuella datorn skapades
diffDiskSettings | Inställningar för efemära diskar
diskSizeGB | Diskens storlek i GB
image   | Virtuell hårddisk för källanvändarbild
Lun     | Diskens logiska enhetsnummer
managedDisk | Parametrar för hanterade diskar
namn    | Disknamn
Vhd     | Virtuell hårddisk
writeAcceleratorEnabled | Om writeAccelerator är aktiverat på disken eller inte

Matrisen datadiskar innehåller en lista över datadiskar som är kopplade till den virtuella datorn. Varje datadiskobjekt innehåller följande information:

Data    | Beskrivning
--------|-----------------
Cachelagring | Krav på cachelagring
createOption | Information om hur den virtuella datorn skapades
diffDiskSettings | Inställningar för efemära diskar
diskSizeGB | Diskens storlek i GB
krypteringInställningar | Krypteringsinställningar för disken
image   | Virtuell hårddisk för källanvändarbild
managedDisk | Parametrar för hanterade diskar
namn    | Disknamn
osType (olika)  | Typ av operativsystem som ingår i disken
Vhd     | Virtuell hårddisk
writeAcceleratorEnabled | Om writeAccelerator är aktiverat på disken eller inte

Följande är ett exempel på hur du frågar den virtuella datorns lagringsinformation.

**Förfrågan**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/storageProfile?api-version=2019-06-01"
```

**Svar**

> [!NOTE]
> Svaret är en JSON-sträng. Följande exempelsvar är ganska utskrivet för läsbarhet.

```json
{
    "dataDisks": [
      {
        "caching": "None",
        "createOption": "Empty",
        "diskSizeGB": "1024",
        "image": {
          "uri": ""
        },
        "lun": "0",
        "managedDisk": {
          "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampledatadiskname",
          "storageAccountType": "Standard_LRS"
        },
        "name": "exampledatadiskname",
        "vhd": {
          "uri": ""
        },
        "writeAcceleratorEnabled": "false"
      }
    ],
    "imageReference": {
      "id": "",
      "offer": "UbuntuServer",
      "publisher": "Canonical",
      "sku": "16.04.0-LTS",
      "version": "latest"
    },
    "osDisk": {
      "caching": "ReadWrite",
      "createOption": "FromImage",
      "diskSizeGB": "30",
      "diffDiskSettings": {
        "option": "Local"
      },
      "encryptionSettings": {
        "enabled": "false"
      },
      "image": {
        "uri": ""
      },
      "managedDisk": {
        "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampleosdiskname",
        "storageAccountType": "Standard_LRS"
      },
      "name": "exampleosdiskname",
      "osType": "Linux",
      "vhd": {
        "uri": ""
      },
      "writeAcceleratorEnabled": "false"
    }
}
```

### <a name="examples-of-calling-metadata-service-using-different-languages-inside-the-vm"></a>Exempel på att anropa metadatatjänst med olika språk i den virtuella datorn

Språk | Exempel
---------|----------------
Ruby     | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.rb
Go  | https://github.com/Microsoft/azureimds/blob/master/imdssample.go
Python   | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.py
C++      | https://github.com/Microsoft/azureimds/blob/master/IMDSSample-windows.cpp
C#       | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.cs
JavaScript | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.js
PowerShell | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.ps1
Bash       | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.sh
Perl       | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.pl
Java       | https://github.com/Microsoft/azureimds/blob/master/imdssample.java
Visual Basic | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.vb
Puppet | https://github.com/keirans/azuremetadata

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

1. Jag får felet `400 Bad Request, Required metadata header not specified`. Vad betyder detta?
   * Instansmetadatatjänsten kräver `Metadata: true` att huvudet skickas i begäran. Om du skickar det här huvudet i REST-anropet får du åtkomst till instansmetadatatjänsten.
2. Varför får jag inte beräkningsinformation för min virtuella dator?
   * För närvarande stöder instansmetadatatjänsten endast instanser som skapats med Azure Resource Manager. I framtiden kan stöd för virtuella datorer för molntjänster läggas till.
3. Jag skapade min virtuella dator via Azure Resource Manager för ett tag sedan. Varför visas inte information om beräkningsmetadata?
   * För alla virtuella datorer som skapats efter sep 2016 lägger du till en [tagg för](../../azure-resource-manager/management/tag-resources.md) att börja se beräkningsmetadata. För äldre virtuella datorer (som skapats före september 2016) lägger du till/tar bort tillägg eller datadiskar till den virtuella datorn för att uppdatera metadata.
4. Jag ser inte alla data som fylls i för ny version
   * För alla virtuella datorer som skapats efter sep 2016 lägger du till en [tagg för](../../azure-resource-manager/management/tag-resources.md) att börja se beräkningsmetadata. För äldre virtuella datorer (som skapats före september 2016) lägger du till/tar bort tillägg eller datadiskar till den virtuella datorn för att uppdatera metadata.
5. Varför får jag `500 Internal Server Error`felet?
   * Försök igen din begäran baserat på exponentiell back off-system. Om problemet kvarstår kontakta Azure-supporten.
6. Var delar jag ytterligare frågor/kommentarer?
   * Skicka dina https://feedback.azure.comkommentarer på .
7. Skulle detta fungera för Virtual Machine Scale Set Instans?
   * Ja Metadata-tjänsten är tillgänglig för skalningsuppsättningsinstanser.
8. Hur får jag support för tjänsten?
   * Skapa ett supportproblem i Azure-portalen för den virtuella datorn där du inte kan få metadatasvar efter långa återförsök för att få support för tjänsten.
9. Jag får tidsfördy hur länge jag vill ha tid för mitt samtal till tjänsten?
   * Metadataanrop måste göras från den primära IP-adressen som tilldelats den virtuella datorns primära nätverkskort, om du dessutom har ändrat dina vägar måste det finnas en väg för 169.254.0.0/16-adress från nätverkskortet.
10. Jag har uppdaterat mina taggar i skalningsuppsättningen för virtuella datorer, men de visas inte i instanserna till skillnad från virtuella datorer?
    * För närvarande för ScaleSets-taggar visas bara för den virtuella datorn på en omstart/omimage/eller en diskändring till instansen.

    ![Stöd för instansmetadata](./media/instance-metadata-service/InstanceMetadata-support.png)

## <a name="next-steps"></a>Nästa steg

- Läs mer om [schemalagda händelser](scheduled-events.md)
