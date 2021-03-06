---
title: 'Självstudiekurs: Bevilja användaråtkomst till Azure-resurser med MALLEN RBAC och Resource Manager'
description: Lär dig hur du ger en användare åtkomst till Azure-resurser med hjälp av rollbaserad åtkomstkontroll (RBAC) med hjälp av Azure Resource Manager-mallen i den här självstudien.
services: role-based-access-control,azure-resource-manager
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: tutorial
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 05/15/2019
ms.author: rolyon
ms.openlocfilehash: 96ca4f65d2def5f5004388c533410f09cc2a71fa
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "77138206"
---
# <a name="tutorial-grant-a-user-access-to-azure-resources-using-rbac-and-resource-manager-template"></a>Självstudiekurs: Bevilja en användare åtkomst till Azure-resurser med hjälp av RBAC- och Resource Manager-mall

[Rollbaserad åtkomstkontroll (RBAC)](overview.md) är det du använder för att hantera åtkomst till Azure-resurser. I den här självstudien skapar du en resursgrupp och ger en användare åtkomst till att skapa och hantera virtuella datorer i resursgruppen. Den här självstudien fokuserar på hur du distribuerar en Resource Manager-mall för att bevilja åtkomsten. Mer information om hur du utvecklar Resource Manager-mallar finns i [Dokumentationen](/azure/azure-resource-manager/) till Resource Manager och [mallreferensen](/azure/templates/microsoft.authorization/allversions
).

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Bevilja åtkomst för en användare i ett resursgruppomfång
> * Verifiera distributionen
> * Rensa

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

## <a name="prerequisites"></a>Krav

Om du vill lägga till och ta bort rolltilldelningar måste du ha:

* `Microsoft.Authorization/roleAssignments/write`och `Microsoft.Authorization/roleAssignments/delete` behörigheter, till exempel [administratör för användaråtkomst](built-in-roles.md#user-access-administrator) eller [ägare](built-in-roles.md#owner)

## <a name="grant-access"></a>Bevilja åtkomst

Mallen som används i den här snabbstarten är från [Azure-snabbstartsmallar](https://azure.microsoft.com/resources/templates/101-rbac-builtinrole-resourcegroup/). Fler Azure-auktoriseringsrelaterade mallar finns [här](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Authorization).

Om du vill distribuera mallen väljer du **Prova det** för att öppna Azure Cloud-skalet och klistra sedan in följande PowerShell-skript i skalfönstret. Om du vill klistra in koden högerklickar du på skalfönstret och väljer sedan **Klistra in**.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used to generate Azure resource names"
$emailAddress = Read-Host -Prompt "Enter your email address that is associated with your Azure subscription (used to find the principal ID)"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

$resourceGroupName = "${projectName}rg"
$roleAssignmentName = New-Guid
$principalId = (Get-AzAdUser -Mail $emailAddress).id
$roleDefinitionId = (Get-AzRoleDefinition -name "Virtual Machine Contributor").id
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-rbac-builtinrole-resourcegroup/azuredeploy.json"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -roleAssignmentName $roleAssignmentName -roleDefinitionID $roleDefinitionId -principalId $principalId
```

## <a name="validate-the-deployment"></a>Verifiera distributionen

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Öppna resursgruppen som skapades i den sista proceduren. Standardnamnet är projektnamnet med **rg** bifogat.
1. Välj **Åtkomstkontroll (IAM)** från den vänstra menyn.
1. Välj **Rolltilldelningar**. 
1. I **Namn**anger du den e-postadress som du skrev i den senaste proceduren. Du ska se att användaren med e-postadressen har rollen **Deltagare i den virtuella datorn.**

## <a name="clean-up"></a>Rensa

Om du vill ta bort resursgruppen som skapades i den senaste proceduren väljer du **Prova** det för att öppna Azure Cloud-skalet och klistrar sedan in följande PowerShell-skript i skalfönstret.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a same project name you used in the last procedure"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -Name $resourceGroupName
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Självstudiekurs: Bevilja en användare åtkomst till Azure-resurser med RBAC och Azure PowerShell](tutorial-role-assignments-user-powershell.md)