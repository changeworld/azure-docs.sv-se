---
title: Datakryptering - Azure CLI - Azure Database för MySQL
description: Lär dig hur du konfigurerar och hanterar datakryptering för din Azure-databas för MySQL med hjälp av Azure CLI.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 03/30/2020
ms.openlocfilehash: fca9b9eea3697a10650e5dbf0522f795fe0a8e0b
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80522217"
---
# <a name="data-encryption-for-azure-database-for-mysql-by-using-the-azure-cli"></a>Datakryptering för Azure Database för MySQL med hjälp av Azure CLI

Lär dig hur du använder Azure CLI för att konfigurera och hantera datakryptering för din Azure-databas för MySQL.

## <a name="prerequisites-for-azure-cli"></a>Förutsättningar för Azure CLI

* Du måste ha en Azure-prenumeration och vara administratör för den prenumerationen.
* Skapa ett nyckelvalv och en nyckel som ska användas för en kundhanterad nyckel. Aktivera även rensningsskydd och mjuk borttagning på nyckelvalvet.

    ```azurecli-interactive
    az keyvault create -g <resource_group> -n <vault_name> --enable-soft-delete true --enable-purge-protection true
    ```

* Skapa nyckeln som ska användas för datakryptering av Azure-databasen för MySQL i det skapade Azure Key Vault.

    ```azurecli-interactive
    az keyvault key create --name <key_name> -p software --vault-name <vault_name>
    ```

* För att kunna använda ett befintligt nyckelvalv måste det ha följande egenskaper som ska användas som kundhanterad nyckel:
  * [Mjuk borttagning](../key-vault/key-vault-ovw-soft-delete.md)

    ```azurecli-interactive
    az resource update --id $(az keyvault show --name \ <key_vault_name> -o tsv | awk '{print $1}') --set \ properties.enableSoftDelete=true
    ```

  * [Rensa skyddad](../key-vault/key-vault-ovw-soft-delete.md#purge-protection)

    ```azurecli-interactive
    az keyvault update --name <key_vault_name> --resource-group <resource_group_name>  --enable-purge-protection true
    ```

* Nyckeln måste ha följande attribut att använda som kundhanterad nyckel:
  * Inget utgångsdatum
  * Inte inaktiverad
  * Utföra **get,** **wrap,** **packa upp** åtgärder

## <a name="set-the-right-permissions-for-key-operations"></a>Ange rätt behörigheter för nyckelåtgärder

1. Det finns två sätt att hämta den hanterade identiteten för din Azure-databas för MySQL.

    ### <a name="create-an-new-azure-database-for-mysql-server-with-a-managed-identity"></a>Skapa en ny Azure-databas för MySQL-server med en hanterad identitet.

    ```azurecli-interactive
    az mysql server create --name -g <resource_group> --location <locations> --storage-size <size>  -u <user>-p <pwd> --backup-retention <7> --sku-name <sku name> --geo-redundant-backup <Enabled/Disabled>  --assign-identity
    ```

    ### <a name="update-an-existing-the-azure-database-for-mysql-server-to-get-a-managed-identity"></a>Uppdatera en befintlig Azure Database for MySQL-server för att få en hanterad identitet.

    ```azurecli-interactive
    az mysql server update --name  <server name>  -g <resource_group> --assign-identity
    ```

2. Ange **nyckelbehörigheter** (**Hämta**, **Radbryt**, **Packa upp**) för **huvudmannen**, som är namnet på MySQL-servern.

    ```azurecli-interactive
    az keyvault set-policy --name -g <resource_group> --key-permissions get unwrapKey wrapKey --object-id <principal id of the server>
    ```

## <a name="set-data-encryption-for-azure-database-for-mysql"></a>Ange datakryptering för Azure Database för MySQL

1. Aktivera datakryptering för Azure Database for MySQL med hjälp av nyckeln som skapats i Azure Key Vault.

    ```azurecli-interactive
    az mysql server key create –name  <server name>  -g <resource_group> --kid <key url>
    ```

    Nyckel url:https://YourVaultName.vault.azure.net/keys/YourKeyName/01234567890123456789012345678901>

## <a name="using-data-encryption-for-restore-or-replica-servers"></a>Använda datakryptering för återställnings- eller replikservrar

När Azure Database for MySQL har krypterats med en kunds hanterade nyckel lagrad i Key Vault krypteras även alla nyskapade kopior av servern. Du kan göra den nya kopian antingen via en lokal eller geoåterställningsåtgärd eller genom en replik (lokal/regionöverskridande åtgärd). Så för en krypterad MySQL-server kan du använda följande steg för att skapa en krypterad återställd server.

### <a name="creating-a-restoredreplica-server"></a>Skapa en återställd/replikserver

  *  [Skapa en återställningsserver](howto-restore-server-cli.md) 
  *  [Skapa en läsreplikserver](howto-read-replicas-cli.md) 

### <a name="once-the-server-is-restored-revalidate-data-encryption-the-restored-server"></a>När servern har återställts, förnya datakryptering den återställda servern

    ```azurecli-interactive
    az mysql server key create –name  <server name> -g <resource_group> --kid <key url>
    ```

## <a name="additional-capability-for-the-key-being-used-for-the-azure-database-for-mysql"></a>Ytterligare funktioner för nyckeln som används för Azure Database for MySQL

### <a name="get-the-key-used"></a>Få nyckeln använd

    ```azurecli-interactive
    az mysql server key show --name  <server name>  -g <resource_group> --kid <key url>
    ```

    Key url:  https://YourVaultName.vault.azure.net/keys/YourKeyName/01234567890123456789012345678901>

### <a name="list-the-key-used"></a>Lista den nyckel som används

    ```azurecli-interactive
    az mysql server key list --name  <server name>  -g <resource_group>
    ```

### <a name="drop-the-key-being-used"></a>Släpp nyckeln som används

    ```azurecli-interactive
    az mysql server key delete -g <resource_group> --kid <key url> 
    ```

## <a name="using-an-azure-resource-manager-template-to-enable-data-encryption"></a>Använda en Azure Resource Manager-mall för att aktivera datakryptering

Förutom Azure-portalen kan du också aktivera datakryptering på din Azure Database for MySQL-server med Azure Resource Manager-mallar för nya och befintliga servrar.

### <a name="for-a-new-server"></a>För en ny server

Använd en av de förskapade Azure Resource Manager-mallarna för att etablera servern med datakryptering aktiverad: [Exempel med datakryptering](https://github.com/Azure/azure-mysql/tree/master/arm-templates/ExampleWithDataEncryption)

Den här Azure Resource Manager-mallen skapar en Azure Database för MySQL-server och använder **KeyVault** och **Key** som skickas som parametrar för att aktivera datakryptering på servern.

### <a name="for-an-existing-server"></a>För en befintlig server
Dessutom kan du använda Azure Resource Manager-mallar för att aktivera datakryptering på dina befintliga Azure-databas för MySQL-servrar.

* Skicka resurs-ID för Azure Key Vault-nyckeln som `Uri` du kopierade tidigare under egenskapen i egenskapsobjektet.

* Använd *2020-01-01-preview* som API-version.

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string"
    },
    "serverName": {
      "type": "string"
    },
    "keyVaultName": {
      "type": "string",
      "metadata": {
        "description": "Key vault name where the key to use is stored"
      }
    },
    "keyVaultResourceGroupName": {
      "type": "string",
      "metadata": {
        "description": "Key vault resource group name where it is stored"
      }
    },
    "keyName": {
      "type": "string",
      "metadata": {
        "description": "Key name in the key vault to use as encryption protector"
      }
    },
    "keyVersion": {
      "type": "string",
      "metadata": {
        "description": "Version of the key in the key vault to use as encryption protector"
      }
    }
  },
  "variables": {
    "serverKeyName": "[concat(parameters('keyVaultName'), '_', parameters('keyName'), '_', parameters('keyVersion'))]"
  },
  "resources": [
    {
      "type": "Microsoft.DBforMySQL/servers",
      "apiVersion": "2017-12-01",
      "kind": "",
      "location": "[parameters('location')]",
      "identity": {
        "type": "SystemAssigned"
      },
      "name": "[parameters('serverName')]",
      "properties": {
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2019-05-01",
      "name": "addAccessPolicy",
      "resourceGroup": "[parameters('keyVaultResourceGroupName')]",
      "dependsOn": [
        "[resourceId('Microsoft.DBforMySQL/servers', parameters('serverName'))]"
      ],
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "resources": [
            {
              "type": "Microsoft.KeyVault/vaults/accessPolicies",
              "name": "[concat(parameters('keyVaultName'), '/add')]",
              "apiVersion": "2018-02-14-preview",
              "properties": {
                "accessPolicies": [
                  {
                    "tenantId": "[subscription().tenantId]",
                    "objectId": "[reference(resourceId('Microsoft.DBforMySQL/servers/', parameters('serverName')), '2017-12-01', 'Full').identity.principalId]",
                    "permissions": {
                      "keys": [
                        "get",
                        "wrapKey",
                        "unwrapKey"
                      ]
                    }
                  }
                ]
              }
            }
          ]
        }
      }
    },
    {
      "name": "[concat(parameters('serverName'), '/', variables('serverKeyName'))]",
      "type": "Microsoft.DBforMySQL/servers/keys",
      "apiVersion": "2020-01-01-preview",
      "dependsOn": [
        "addAccessPolicy",
        "[resourceId('Microsoft.DBforMySQL/servers', parameters('serverName'))]"
      ],
      "properties": {
        "serverKeyType": "AzureKeyVault",
        "uri": "[concat(reference(resourceId(parameters('keyVaultResourceGroupName'), 'Microsoft.KeyVault/vaults/', parameters('keyVaultName')), '2018-02-14-preview', 'Full').properties.vaultUri, 'keys/', parameters('keyName'), '/', parameters('keyVersion'))]"
      }
    }
  ]
}

```

## <a name="next-steps"></a>Nästa steg

 Mer information om datakryptering finns i [Azure Database for MySQL-datakryptering med kundhanterad nyckel](concepts-data-encryption-mysql.md).
