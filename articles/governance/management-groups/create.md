---
title: Skapa hanteringsgrupper för att ordna resurser - Azure-styrning
description: Lär dig hur du skapar Azure-hanteringsgrupper för att hantera flera resurser med hjälp av portalen, Azure PowerShell och Azure CLI.
ms.date: 04/15/2020
ms.topic: conceptual
ms.openlocfilehash: 34815089367512c4aa54f148c118a669625d0ea3
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81381593"
---
# <a name="create-management-groups-for-resource-organization-and-management"></a>Skapa hanteringsgrupper för organisering och hantering av resurser

Hanteringsgrupper är behållare som hjälper dig att hantera åtkomst, princip och efterlevnad mellan flera prenumerationer. Skapa dessa behållare för att skapa en effektiv och ändamålsenlig hierarki som kan användas med [Azure-princip](../policy/overview.md) och [Azure-rollbaserade åtkomstkontroller](../../role-based-access-control/overview.md). Mer information om hanteringsgrupper finns i [Ordna dina resurser med Azure-hanteringsgrupper](overview.md).

Den första hanteringsgruppen som skapas i katalogen kan ta upp till 15 minuter att slutföra. Det finns processer som körs första gången för att konfigurera hanteringsgrupper tjänsten i Azure för din katalog. Du får ett meddelande när processen är klar. Mer information finns i [inledande inställningar för hanteringsgrupper](./overview.md#initial-setup-of-management-groups).

## <a name="create-a-management-group"></a>Skapa en hanteringsgrupp

Alla Azure AD-användare i klienten kan skapa en hanteringsgrupp utan att den skrivbehörighet för hanteringsgruppen som tilldelats den användaren. Den här nya hanteringsgruppen kommer att vara underordnad rothanteringsgruppen och skaparen får en rolltilldelning för ägare. Hanteringsgruppstjänsten tillåter den här möjligheten så att rolltilldelningar inte behövs på rotnivå. Inga användare har åtkomst till rothanteringsgruppen när den skapas. För att undvika att hindren för att hitta Azure AD Global Admins att börja använda hanteringsgrupper, tillåter vi skapandet av de första hanteringsgrupperna vid roten  
Nivå.

Du kan skapa hanteringsgruppen med hjälp av portalen, en [Resource Manager-mall](../../azure-resource-manager/templates/deploy-to-tenant.md#create-management-group), PowerShell eller Azure CLI.

### <a name="create-in-portal"></a>Skapa i portalen

1. Logga in på [Azure Portal](https://portal.azure.com).

1. Välj **Alla tjänster** > **Management + styrning**.

1. Välj **Kostnadshantering + Fakturering**

1. På sidan Kostnadshantering + Fakturering - Hanteringsgrupper väljer du **Hanteringsgrupper**

1. Välj **+ Lägg till hanteringsgrupp**.

   :::image type="content" source="./media/main.png" alt-text="Sida för att arbeta med hanteringsgrupper" border="false":::

1. Fyll i fältet hanteringsgrupp-ID.

   - **Hanteringsgrupp-ID** är den katalogunika identifierare som används för att skicka kommandon i den här hanteringsgruppen. Den här identifieraren kan inte redigeras när den har skapats eftersom den används i hela Azure-systemet för att identifiera den här gruppen. [Rothanteringsgruppen](overview.md#root-management-group-for-each-directory) skapas automatiskt med ett ID som är Azure Active Directory ID. Tilldela ett unikt ID för alla andra hanteringsgrupper.
   - Fältet visningsnamn är det namn som visas i Azure-portalen. Ett separat visningsnamn är ett valfritt fält när hanteringsgruppen skapas och kan ändras när som helst  
     Tid.

   :::image type="content" source="./media/create_context_menu.png" alt-text="Alternativfönstret för att skapa en ny hanteringsgrupp" border="false":::

1. Välj **Spara**.

### <a name="create-in-powershell"></a>Skapa i PowerShell

För PowerShell använder du cmdlet [new-AzManagementGroup](/powershell/module/az.resources/new-azmanagementgroup) för att skapa en ny hanteringsgrupp.

```azurepowershell-interactive
New-AzManagementGroup -GroupName 'Contoso'
```

**GroupName** är en unik identifierare som skapas. Det här ID:t används av andra kommandon för att referera till den här gruppen och det kan inte ändras senare.

Om du vill att hanteringsgruppen ska visa ett annat namn i Azure-portalen lägger du till parametern **DisplayName.** Om du till exempel vill skapa en hanteringsgrupp med GroupName of Contoso och visningsnamnet "Contoso Group" använder du följande cmdlet:

```azurepowershell-interactive
New-AzManagementGroup -GroupName 'Contoso' -DisplayName 'Contoso Group'
```

I föregående exempel skapas den nya hanteringsgruppen under rothanteringsgruppen. Om du vill ange en annan hanteringsgrupp som överordnad använder du parametern **ParentId.**

```azurepowershell-interactive
$parentGroup = Get-AzManagementGroup -GroupName Contoso
New-AzManagementGroup -GroupName 'ContosoSubGroup' -ParentId $parentGroup.id
```

### <a name="create-in-azure-cli"></a>Skapa i Azure CLI

För Azure CLI använder du kommandot [skapa az-kontohanteringsgrupp](/cli/azure/account/management-group?view=azure-cli-latest#az-account-management-group-create) för att skapa en ny hanteringsgrupp.

```azurecli-interactive
az account management-group create --name Contoso
```

**Namnet** är en unik identifierare som skapas. Det här ID:t används av andra kommandon för att referera till den här gruppen och det kan inte ändras senare.

Om du vill att hanteringsgruppen ska visa ett annat namn i Azure-portalen lägger du till parametern **visningsnamn.** Om du till exempel vill skapa en hanteringsgrupp med GroupName of Contoso och visningsnamnet "Contoso Group" använder du följande kommando:

```azurecli-interactive
az account management-group create --name Contoso --display-name 'Contoso Group'
```

I föregående exempel skapas den nya hanteringsgruppen under rothanteringsgruppen. Om du vill ange en annan hanteringsgrupp som överordnad använder du den **överordnade** parametern och anger namnet på den överordnade gruppen.

```azurecli-interactive
az account management-group create --name ContosoSubGroup --parent Contoso
```

## <a name="next-steps"></a>Nästa steg

Läs mer om hanteringslösningar här:

- [Skapa hanteringsgrupper för att organisera Azure-resurser](./create.md)
- [Så här ändrar, raderar och hanterar du dina hanteringsgrupper](./manage.md)
- [Granska hanteringsgrupper i Azure PowerShell-resursmodulen](/powershell/module/az.resources#resources)
- [Granska hanteringsgrupper i REST API](/rest/api/resources/managementgroups)
- [Granska hanteringsgrupper i Azure CLI](/cli/azure/account/management-group)
