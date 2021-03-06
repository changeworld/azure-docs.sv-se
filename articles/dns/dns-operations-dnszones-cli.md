---
title: Hantera DNS-zoner i Azure DNS - Azure CLI | Microsoft-dokument
description: Du kan hantera DNS-zoner med Azure CLI. Den här artikeln visar hur du uppdaterar, tar bort och skapar DNS-zoner på Azure DNS.
services: dns
documentationcenter: na
author: rohinkoul
manager: timlt
ms.assetid: 8ab63bc4-5135-4ed8-8c0b-5f0712b9afed
ms.service: dns
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/27/2017
ms.author: rohink
ms.openlocfilehash: 413c2ab3ee04249c2bb52bf42ca6a31a58fb9082
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76936929"
---
# <a name="how-to-manage-dns-zones-in-azure-dns-using-the-azure-cli"></a>Hantera DNS-zoner i Azure DNS med Hjälp av Azure CLI

> [!div class="op_single_selector"]
> * [Portal](dns-operations-dnszones-portal.md)
> * [Powershell](dns-operations-dnszones.md)
> * [Azure CLI](dns-operations-dnszones-cli.md)


Den här guiden visar hur du hanterar dina DNS-zoner med hjälp av azure CLI, som är tillgängligt för Windows, Mac och Linux. Du kan också hantera dina DNS-zoner med [Azure PowerShell](dns-operations-dnszones.md) eller Azure-portalen.

Den här guiden behandlar specifikt offentliga DNS-zoner. Information om hur du använder Azure CLI för att hantera privata zoner i Azure DNS finns i [Komma igång med Azure DNS Private Zones med Azure CLI](private-dns-getstarted-cli.md).

## <a name="introduction"></a>Introduktion

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

## <a name="set-up-azure-cli-for-azure-dns"></a>Konfigurera Azure CLI för Azure DNS

### <a name="before-you-begin"></a>Innan du börjar

Kontrollera att du har följande innan du påbörjar konfigurationen.

* En Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du aktivera dina [MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) eller registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/).

* Installera den senaste versionen av Azure CLI. Den finns tillgänglig för Windows, Linux och MAC. Mer information finns på [Installera Azure CLI](https://docs.microsoft.com/cli/azure/install-az-cli2).

### <a name="sign-in-to-your-azure-account"></a>Logga in på ditt Azure-konto

Öppna ett konsolfönster och autentisera med dina autentiseringsuppgifter. Mer information finns [i Logga in på Azure från Azure CLI](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest)

```
az login
```

### <a name="select-the-subscription"></a>Välja prenumerationen

Kontrollera prenumerationerna för kontot.

```
az account list
```

Välj vilka av dina Azure-prenumerationer som du vill använda.

```azurecli
az account set --subscription "subscription name"
```

### <a name="optional-to-installuse-azure-dns-private-zones-feature"></a>Valfritt: Så här installerar/använder du azure DNS-funktionen privata zoner
Azure DNS Private Zone-funktionen är tillgänglig via ett tillägg till Azure CLI. Installera dns-tillägget för Azure CLI 
```
az extension add --name dns
``` 

### <a name="create-a-resource-group"></a>Skapa en resursgrupp

Azure Resource Manager kräver att alla resursgrupper anger en plats. Den här platsen används som standardplats för resurser i resursgruppen. Men eftersom alla DNS-resurser är globala, inte regionala, så påverkar inte valet av resursgruppens plats Azure DNS.

Du kan hoppa över det här steget om du använder en befintlig resursgrupp.

```azurecli
az group create --name myresourcegroup --location "West US"
```

## <a name="getting-help"></a>Få hjälp

Alla Azure CLI-kommandon som rör `az network dns`Azure DNS börjar med . Hjälp är tillgänglig för `--help` varje kommando `-h`med hjälp av alternativet (kort form ).  Ett exempel:

```azurecli
az network dns --help
az network dns zone --help
az network dns zone create --help
```

## <a name="create-a-dns-zone"></a>Skapa en DNS-zon

En DNS-zon skapas med hjälp av kommandot `az network dns zone create`. Om du vill ha hjälp, så gå till `az network dns zone create -h`.

I följande exempel skapas en DNS-zon som kallas *contoso.com* i resursgruppen *MyResourceGroup:*

```azurecli
az network dns zone create --resource-group MyResourceGroup --name contoso.com
```

### <a name="to-create-a-dns-zone-with-tags"></a>Så här skapar du en DNS-zon med taggar

I följande exempel visas hur du skapar en DNS-zon med två [Azure Resource Manager-taggar](dns-zones-records.md#tags), projekt = *demo* och *env = test*, med hjälp av `--tags` parametern (kort formulär): `-t`

```azurecli
az network dns zone create --resource-group MyResourceGroup --name contoso.com --tags "project=demo" "env=test"
```

## <a name="get-a-dns-zone"></a>Skaffa en DNS-zon

Om du vill hämta `az network dns zone show`en DNS-zon använder du . Om du vill ha hjälp, så gå till `az network dns zone show --help`.

I följande exempel returneras DNS-zonen *contoso.com* och tillhörande data från resursgruppen *MyResourceGroup*. 

```azurecli
az network dns zone show --resource-group myresourcegroup --name contoso.com
```

Följande exempel är svaret.

```json
{
  "etag": "00000002-0000-0000-3d4d-64aa3689d201",
  "id": "/subscriptions/147a22e9-2356-4e56-b3de-1f5842ae4a3b/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com",
  "location": "global",
  "maxNumberOfRecordSets": 5000,
  "name": "contoso.com",
  "nameServers": [
    "ns1-04.azure-dns.com.",
    "ns2-04.azure-dns.net.",
    "ns3-04.azure-dns.org.",
    "ns4-04.azure-dns.info."
  ],
  "numberOfRecordSets": 4,
  "resourceGroup": "myresourcegroup",
  "tags": {},
  "type": "Microsoft.Network/dnszones"
}
```

Observera att DNS-poster inte returneras av `az network dns zone show`. Använd `az network dns record-set list` om du vill lista DNS-poster.


## <a name="list-dns-zones"></a>Lista DNS-zoner

Använd `az network dns zone list` om du vill räkna upp DNS-zoner. Om du vill ha hjälp, så gå till `az network dns zone list --help`.

När du anger resursgrupp så listas bara zonerna i resursgruppen:

```azurecli
az network dns zone list --resource-group MyResourceGroup
```

Om du utesluter resursgruppen listas alla zoner i prenumerationen:

```azurecli
az network dns zone list 
```

## <a name="update-a-dns-zone"></a>Uppdatera en DNS-zon

Du kan göra ändringar i en DNS-zonresurs med `az network dns zone update`. Om du vill ha hjälp, så gå till `az network dns zone update --help`.

Det här kommandot uppdaterar inte någon av DNS-postuppsättningarna i zonen (mer information finns i [Hantera DNS-poster](dns-operations-recordsets-cli.md)). Det används endast för att uppdatera zonresursens egenskaper. Dessa egenskaper är för närvarande begränsade till [Azure Resource Manager 'tags'](dns-zones-records.md#tags) för zonresursen.

I följande exempel visas hur du uppdaterar taggarna i en DNS-zon. De befintliga taggarna ersätts med det angivna värdet.

```azurecli
az network dns zone update --resource-group myresourcegroup --name contoso.com --set tags.team=support
```

## <a name="delete-a-dns-zone"></a>Ta bort en DNS-zon

Du kan ta bort DNS-zoner med `az network dns zone delete`. Om du vill ha hjälp, så gå till `az network dns zone delete --help`.

> [!NOTE]
> Om du tar bort en DNS-zon så tar du även bort DNS-posterna i zonen. Du kan inte ångra den här åtgärden. Om DNS-zonen används, så misslyckas de tjänster som använder zonen när zonen tas bort.
>
>Hur du gör för att förebygga oavsiktlig zonborttagning beskrivs i [Skydda DNS-zoner och poster](dns-protect-zones-recordsets.md).

Det här kommandot uppmanar dig att bekräfta. Det valfria `--yes`-växeln utelämnar det här meddelandet.

I följande exempel visas hur du tar bort zonen *contoso.com* från resursgruppen *MyResourceGroup*.

```azurecli
az network dns zone delete --resource-group myresourcegroup --name contoso.com
```

## <a name="next-steps"></a>Nästa steg

Läs om hur du [hanterar postuppsättningar och poster](dns-getstarted-create-recordset-cli.md) i DNS-zonen.

Lär dig hur du [delegerar domänen till Azure DNS](dns-domain-delegation.md).

