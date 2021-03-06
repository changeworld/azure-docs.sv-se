---
title: Skydda privata DNS-zoner och -poster – Azure DNS
description: I den här utbildningssökvägen kommer du igång med att skydda privata DNS-zoner och postuppsättningar i Microsoft Azure DNS.
services: dns
author: asudbring
ms.service: dns
ms.topic: article
ms.date: 02/18/2020
ms.author: allensu
ms.openlocfilehash: da94c9aa97483ab5792e917d6a8f60f846b0722e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77473069"
---
# <a name="how-to-protect-private-dns-zones-and-records"></a>Så här skyddar du privata DNS-zoner och -poster

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Privata DNS-zoner och poster är kritiska resurser. Om du tar bort en DNS-zon eller en enda DNS-post kan det leda till ett avbrott i tjänsten. Det är viktigt att DNS-zoner och poster skyddas mot obehöriga eller oavsiktliga ändringar.

I den här artikeln beskrivs hur Azure DNS gör att du kan skydda dina privata DNS-zoner och poster mot sådana ändringar.  Vi tillämpar två kraftfulla värdepappersfunktioner som tillhandahålls av Azure Resource Manager: [rollbaserad åtkomstkontroll](../role-based-access-control/overview.md) och [resurslås](../azure-resource-manager/management/lock-resources.md).

## <a name="role-based-access-control"></a>Rollbaserad åtkomstkontroll

Azure Role-Based Access Control (RBAC) möjliggör finkornig åtkomsthantering för Azure-användare, grupper och resurser. Med RBAC kan du bevilja den åtkomstnivå som användarna behöver. Mer information om hur RBAC hjälper dig att hantera åtkomst finns i [Vad är rollbaserad åtkomstkontroll](../role-based-access-control/overview.md).

### <a name="the-private-dns-zone-contributor-role"></a>Rollen Privat DNS-zondeltagare

Rollen Privat DNS Zone Contributor är en inbyggd roll för hantering av privata DNS-resurser. Den här rollen som tillämpas på en användare eller grupp gör det möjligt för dem att hantera privata DNS-resurser.

Resursgruppen *myPrivateDNS* innehåller fem zoner för Contoso Corporation. Genom att ge DNS-administratören behörigheter för private DNS Zone Contributor till den resursgruppen kan fullständig kontroll över dessa DNS-zoner. Det undviker att bevilja onödiga behörigheter. DNS-administratören kan inte skapa eller stoppa virtuella datorer.

Det enklaste sättet att tilldela RBAC-behörigheter är [via Azure-portalen](../role-based-access-control/role-assignments-portal.md).  

Öppna **åtkomstkontroll (IAM)** för resursgruppen, välj **Lägg till**och välj sedan rollen Privat **DNS-zondeltagare.** Välj de användare eller grupper som krävs för att bevilja behörigheter.

![RBAC på resursgruppsnivå via Azure-portalen](./media/dns-protect-private-zones-recordsets/rbac1.png)

Behörigheter kan också [beviljas med Azure PowerShell:](../role-based-access-control/role-assignments-powershell.md)

```azurepowershell-interactive
# Grant 'Private DNS Zone Contributor' permissions to all zones in a resource group

$rsg = "<resource group name>"
$usr = "<user email address>"
$rol = "Private DNS Zone Contributor"

New-AzRoleAssignment -SignInName $usr -RoleDefinitionName $rol -ResourceGroupName $rsg
```

Motsvarande kommando är också [tillgängligt via Azure CLI:](../role-based-access-control/role-assignments-cli.md)

```azurecli-interactive
# Grant 'Private DNS Zone Contributor' permissions to all zones in a resource group

az role assignment create \
--assignee "<user email address>" \
--role "Private DNS Zone Contributor" \
--resource-group "<resource group name>"
```

### <a name="private-zone-level-rbac"></a>RBAC-nivå på privat zon

Azure RBAC-regler kan tillämpas på en prenumeration, en resursgrupp eller på en enskild resurs. Den resursen kan vara en enskild DNS-zon eller en enskild postuppsättning.

Resursgruppen *myPrivateDNS* innehåller till exempel zonen *private.contoso.com* och en underzon *customers.private.contoso.com*. CNAME-poster skapas för varje kundkonto. Administratörskontot som används för att hantera CNAME-poster tilldelas behörigheter för att skapa poster i *zonen customers.private.contoso.com.* Kontot kan bara hantera *customers.private.contoso.com.*

RBAC-behörigheter på zonnivå kan beviljas via Azure-portalen.  Öppna **åtkomstkontroll (IAM)** för zonen, välj **Lägg till**och välj sedan rollen Privat **DNS-zondeltagare.** Välj de användare eller grupper som krävs för att bevilja behörigheter.

![RBAC-nivå på DNS-zonnivå via Azure-portalen](./media/dns-protect-private-zones-recordsets/rbac2.png)

Behörigheter kan också [beviljas med Azure PowerShell:](../role-based-access-control/role-assignments-powershell.md)

```azurepowershell-interactive
# Grant 'Private DNS Zone Contributor' permissions to a specific zone

$rsg = "<resource group name>"
$usr = "<user email address>"
$zon = "<zone name>"
$rol = "Private DNS Zone Contributor"
$rsc = "Microsoft.Network/privateDnsZones"

New-AzRoleAssignment -SignInName $usr -RoleDefinitionName $rol -ResourceGroupName $rsg -ResourceName $zon -ResourceType $rsc
```

Motsvarande kommando är också [tillgängligt via Azure CLI:](../role-based-access-control/role-assignments-cli.md)

```azurecli-interactive
# Grant 'Private DNS Zone Contributor' permissions to a specific zone

az role assignment create \
--assignee <user email address> \
--role "Private DNS Zone Contributor" \
--scope "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/privateDnsZones/<zone name>/"
```

### <a name="record-set-level-rbac"></a>RBAC-nivå för postuppsättning

Behörigheter tillämpas på postuppsättningsnivå.  Användaren beviljas kontroll till poster som de behöver och kan inte göra några andra ändringar.

RBAC-behörigheter på postuppsättningsnivå kan konfigureras via Azure-portalen med hjälp av **IAM-knappen (Access Control)** på postuppsättningssidan:

![RBAC på postuppsättningsnivå via Azure-portalen](./media/dns-protect-private-zones-recordsets/rbac3.png)

![RBAC på postuppsättningsnivå via Azure-portalen](./media/dns-protect-private-zones-recordsets/rbac4.png)

RBAC-behörigheter på postuppsättningsnivå kan också [beviljas med Azure PowerShell:](../role-based-access-control/role-assignments-powershell.md)

```azurepowershell-interactive
# Grant permissions to a specific record set

$usr = "<user email address>"
$rol = "Private DNS Zone Contributor"
$sco = 
"/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/privateDnsZones/<zone name>/<record type>/<record name>"

New-AzRoleAssignment -SignInName $usr -RoleDefinitionName $rol -Scope $sco
```

Motsvarande kommando är också [tillgängligt via Azure CLI:](../role-based-access-control/role-assignments-cli.md)

```azurecli-interactive
# Grant permissions to a specific record set

az role assignment create \
--assignee "<user email address>" \
--role "Private DNS Zone Contributor" \
--scope "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/privateDnsZones/<zone name>/<record type>/<record name>"
```

### <a name="custom-roles"></a>Anpassade roller

Den inbyggda rollen Privat DNS Zone Contributor ger fullständig kontroll över en DNS-resurs. Det är möjligt att skapa dina egna anpassade Azure-roller för att ge finare kornig kontroll.

Kontot som används för att hantera CNAMEs beviljas endast behörighet att hantera CNAME-poster. Kontot kan inte ändra poster av andra typer. Kontot kan inte utföra zonnivååtgärder som att ta bort zonen.

I följande exempel visas en anpassad rolldefinition endast för hantering av CNAME-poster:

```json
{
    "Name": "Private DNS CNAME Contributor",
    "Id": "",
    "IsCustom": true,
    "Description": "Can manage DNS CNAME records only.",
    "Actions": [
        "Microsoft.Network/privateDnsZones/CNAME/*",
        "Microsoft.Network/privateDNSZones/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
    ],
    "NotActions": [
    ],
    "AssignableScopes": [
        "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ]
}
```

Egenskapen Åtgärder definierar följande DNS-specifika behörigheter:

* `Microsoft.Network/privateDnsZones/CNAME/*`ger fullständig kontroll över CNAME-poster
* `Microsoft.Network/privateDNSZones/read`ger behörighet att läsa PRIVATA DNS-zoner, men inte ändra dem, så att du kan se zonen där CNAME skapas.

> [!NOTE]
> Att använda en anpassad RBAC-roll för att förhindra att postuppsättningar tas bort samtidigt som de kan uppdateras är inte en effektiv kontroll. Det förhindrar att postuppsättningar tas bort, men de hindras inte från att ändras.  Tillåtna ändringar inkluderar att lägga till och ta bort poster från postuppsättningen, inklusive att ta bort alla poster för att lämna en tom postuppsättning. Detta har samma effekt som att ta bort posten från en DNS-lösning synvinkel.

Anpassade rolldefinitioner kan för närvarande inte definieras via Azure-portalen. En anpassad roll baserad på den här rolldefinitionen kan skapas med Azure PowerShell:

```azurepowershell-interactive
# Create new role definition based on input file

New-AzRoleDefinition -InputFile <file path>
```

Det kan också skapas via Azure CLI:

```azurecli-interactive
# Create new role definition based on input file

az role create -inputfile <file path>
```

Rollen kan sedan tilldelas på samma sätt som inbyggda roller, som beskrivs tidigare i den här artikeln.

Mer information om hur du skapar, hanterar och tilldelar anpassade roller finns [i Anpassade roller i Azure RBAC](../role-based-access-control/custom-roles.md).

## <a name="resource-locks"></a>Resurslås

Azure Resource Manager stöder en annan typ av säkerhetskontroll, möjligheten att låsa resurser. Resurslås tillämpas på resursen och är effektiva för alla användare och roller. Mer information finns i [Låsa resurser med Azure Resource Manager](../azure-resource-manager/management/lock-resources.md).

Det finns två typer av resurslås: **CanNotDelete** och **ReadOnly**. Dessa låstyper kan användas antingen på en privat DNS-zon eller på en enskild postuppsättning.  I följande avsnitt beskrivs flera vanliga scenarier och hur du stöder dem med hjälp av resurslås.

### <a name="protecting-against-all-changes"></a>Skydda mot alla förändringar

Om du vill förhindra att ändringar görs använder du ett ReadOnly-lås på zonen. Det här låset förhindrar att nya postuppsättningar skapas och befintliga postuppsättningar ändras eller tas bort.

Resurslås på zonnivå kan skapas via Azure-portalen.  På sidan DNS-zon väljer du **Lås**och väljer sedan **+Lägg till:**

![Resurslås på zonnivå via Azure-portalen](./media/dns-protect-private-zones-recordsets/locks1.png)

Resurslås på zonnivå kan också skapas via [Azure PowerShell:](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcelock?view=latest)

```azurepowershell-interactive
# Lock a DNS zone

$lvl = "<lock level>"
$lnm = "<lock name>"
$rsc = "<zone name>"
$rty = "Microsoft.Network/privateDnsZones"
$rsg = "<resource group name>"

New-AzResourceLock -LockLevel $lvl -LockName $lnm -ResourceName $rsc -ResourceType $rty -ResourceGroupName $rsg
```

Motsvarande kommando är också [tillgängligt via Azure CLI:](https://docs.microsoft.com/cli/azure/lock?view=azure-cli-latest#az-lock-create)

```azurecli-interactive
# Lock a DNS zone

az lock create \
--lock-type "<lock level>" \
--name "<lock name>" \
--resource-name "<zone name>" \
--namespace "Microsoft.Network" \
--resource-type "privateDnsZones" \
--resource-group "<resource group name>"
```
### <a name="protecting-individual-records"></a>Skydda enskilda poster

Om du vill förhindra att en befintlig DNS-post ställs in mot ändringar använder du ett ReadOnly-lås på postuppsättningen.

> [!NOTE]
> Att använda ett CanNotDelete-lås på en postuppsättning är inte en effektiv kontroll. Det förhindrar att postuppsättningen tas bort, men den förhindrar inte att den ändras.  Tillåtna ändringar inkluderar att lägga till och ta bort poster från postuppsättningen, inklusive att ta bort alla poster för att lämna en tom postuppsättning. Detta har samma effekt som att ta bort posten från en DNS-lösning synvinkel.

Resurslås på postuppsättningsnivå kan för närvarande endast konfigureras med Azure PowerShell.  De stöds inte i Azure-portalen eller Azure CLI.

Azure PowerShell

```azurepowershell-interactive
# Lock a DNS record set

$lvl = "<lock level>"
$lnm = "<lock name>"
$rnm = "<zone name>/<record set name>"
$rty = "Microsoft.Network/privateDnsZones"
$rsg = "<resource group name>"

New-AzResourceLock -LockLevel $lvl -LockName $lnm -ResourceName $rnm -ResourceType $rty -ResourceGroupName $rsg
```
### <a name="protecting-against-zone-deletion"></a>Skydda mot borttagning av zon

När en zon tas bort i Azure DNS tas alla postuppsättningar i zonen bort.  Den här åtgärden kan inte ångras. Om du tar bort en kritisk zon av misstag kan det finnas en betydande inverkan på verksamheten.  Det är viktigt att skydda mot oavsiktlig zonradering.

Om du använder ett CanNotDelete-lås på en zon tas zonen bort. Lås ärvs av underordnade resurser. Ett lås förhindrar att postuppsättningar i zonen tas bort. Som beskrivs i anteckningen ovan är det ineffektivt eftersom poster fortfarande kan tas bort från de befintliga postuppsättningarna.

Alternativt kan du använda ett CanNotDelete-lås på en postuppsättning i zonen, till exempel SOA-postuppsättningen. Zonen tas inte bort utan att även ta bort postuppsättningarna. Det här låset skyddar mot borttagning av zoner, samtidigt som postuppsättningar inom zonen tillåts fritt. Om ett försök görs att ta bort zonen identifierar Azure Resource Manager den här borttagningen. Borttagningen skulle också ta bort SOA-postuppsättningen, Azure Resource Manager blockerar anropet eftersom SOA är låst.  Inga postuppsättningar tas bort.

Följande PowerShell-kommando skapar ett CanNotDelete-lås mot SOA-posten i den angivna zonen:

```azurepowershell-interactive
# Protect against zone delete with CanNotDelete lock on the record set

$lvl = "CanNotDelete"
$lnm = "<lock name>"
$rnm = "<zone name>/@"
$rty = "Microsoft.Network/privateDnsZones/SOA"
$rsg = "<resource group name>"

New-AzResourceLock -LockLevel $lvl -LockName $lnm -ResourceName $rnm -ResourceType $rty -ResourceGroupName $rsg
```
Ett annat alternativ för att förhindra borttagning av en zon i en annan är att använda en anpassad roll. Den här rollen säkerställer att de konton som används för att hantera dina zoner inte har behörigheter för zonborttagning. 

När du behöver ta bort en zon kan du framtvinga en borttagning i två steg:

 - Först bevilja zonborttagningsbehörigheter
 - För det andra bevilja behörigheter för att ta bort zonen.

Den anpassade rollen fungerar för alla zoner som används av dessa konton. Konton med zonborttagningsbehörigheter, till exempel prenumerationsägaren, kan fortfarande av misstag ta bort en zon.

Det är möjligt att använda båda metoderna - resurslås och anpassade roller - samtidigt som en försvars-djupgående metod för DNS-zonskydd.

## <a name="next-steps"></a>Nästa steg

* Mer information om hur du arbetar med RBAC finns [i Komma igång med åtkomsthantering i Azure-portalen](../role-based-access-control/overview.md).
* Mer information om hur du arbetar med resurslås finns i [Låsa resurser med Azure Resource Manager](../azure-resource-manager/management/lock-resources.md).
