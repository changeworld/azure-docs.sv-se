---
title: Hämta värden för appautentisering
description: Skapa ett tjänsthuvudnamn för åtkomst till SQL Database från kod.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/12/2019
ms.openlocfilehash: 5b2c64660f37745f5b13d53559037e84ca20c47b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79476974"
---
# <a name="get-the-required-values-for-authenticating-an-application-to-access-sql-database-from-code"></a>Hämta de värden som krävs för att autentisera ett program för att komma åt SQL Database från kod

Om du vill skapa och hantera SQL Database från kod måste du registrera din app i Azure Active Directory (AAD) domänen i prenumerationen där dina Azure-resurser har skapats.

## <a name="create-a-service-principal-to-access-resources-from-an-application"></a>Skapa ett tjänsthuvudnamn för att komma åt resurser från ett program

Följande exempel skapar Active Directory (AD) programmet och tjänstens huvudnamn som vi behöver för att autentisera vår C# app. Skriptet matar ut värden som vi behöver för det föregående C#-exemplet. Detaljerad information finns i [Skapa ett tjänstobjekt med Azure PowerShell för att komma åt resurser](../active-directory/develop/howto-authenticate-service-principal-powershell.md).

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

> [!IMPORTANT]
> PowerShell Azure Resource Manager (RM) stöds fortfarande av Azure SQL Database, men all framtida utveckling är för Az.Sql-modulen. AzureRM-modulen fortsätter att ta emot buggfixar fram till åtminstone december 2020.  Argumenten för kommandona i Az-modulen och i AzureRm-modulerna är i stort sett identiska. Mer information om deras kompatibilitet finns i [Introduktion till den nya Azure PowerShell Az-modulen](/powershell/azure/new-azureps-module-az).

```powershell
# sign in to Azure
Connect-AzAccount

# for multiple subscriptions, uncomment and set to the subscription you want to work with
#$subscriptionId = "{xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}"
#Set-AzContext -SubscriptionId $subscriptionId

$appName = "{app-name}" # display name for your app, must be unique in your directory
$uri = "http://{app-name}" # does not need to be a real uri
$secret = "{app-password}"

# create an AAD app
$azureAdApplication = New-AzADApplication -DisplayName $appName -HomePage $Uri -IdentifierUris $Uri -Password $secret

# create a Service Principal for the app
$svcprincipal = New-AzADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

Start-Sleep -s 15 # to avoid a PrincipalNotFound error, pause here for 15 seconds

# if you still get a PrincipalNotFound error, then rerun the following until successful.
$roleassignment = New-AzRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $azureAdApplication.ApplicationId.Guid

# output the values we need for our C# application to successfully authenticate
Write-Output "Copy these values into the C# sample app"

Write-Output "_subscriptionId:" (Get-AzContext).Subscription.SubscriptionId
Write-Output "_tenantId:" (Get-AzContext).Tenant.TenantId
Write-Output "_applicationId:" $azureAdApplication.ApplicationId.Guid
Write-Output "_applicationSecret:" $secret
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
# sign in to Azure
az login

# for multiple subscriptions, uncomment and set to the subscription you want to work with
#$subscriptionId = "{xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}"
#az account set --subscription $subscriptionId

$appName = "{app-name}" # display name for your app, must be unique in your directory
$uri = "http://{app-name}" # does not need to be a real uri
$secret = "{app-password}"

# create an AAD app
$azureAdApplication = az ad app create --display-name $appName --homepage $Uri --identifier-uris $Uri --password $secret

# create a Service Principal for the app
$svcprincipal = az ad sp create --id $azureAdApplication.ApplicationId

Start-Sleep -s 15 # to avoid a PrincipalNotFound error, pause for 15 seconds

# if you still get a PrincipalNotFound error, then rerun the following until successful.
$roleassignment = az role assignment create --role "Contributor" --assignee $azureAdApplication.ApplicationId.Guid

# output the values we need for our C# application to successfully authenticate
Write-Output "Copy these values into the C# sample app"

Write-Output "_subscriptionId:" (az account show --query "id")
Write-Output "_tenantId:" (az account show --query "tenantId")
Write-Output "_applicationId:" $azureAdApplication.ApplicationId.Guid
Write-Output "_applicationSecret:" $secret
```

* * *

## <a name="see-also"></a>Se även

[Skapa en SQL-databas med C #](sql-database-get-started-csharp.md)  
[Ansluta till SQL Database med hjälp av Azure Active Directory-autentisering](sql-database-aad-authentication.md)
