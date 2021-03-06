---
title: Skapa Apache Hadoop-kluster med Azure REST API - Azure
description: Lär dig hur du skapar HDInsight-kluster genom att skicka Azure Resource Manager-mallar till Azure REST API.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/10/2019
ms.openlocfilehash: 2680304bd73bdbae35b29b89f38ae2665615f5e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80239922"
---
# <a name="create-apache-hadoop-clusters-using-the-azure-rest-api"></a>Skapa Apache Hadoop-kluster med Azure REST API

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Lär dig hur du skapar ett HDInsight-kluster med hjälp av en Azure Resource Manager-mall och Azure REST API.

Azure REST API kan du utföra hanteringsåtgärder på tjänster som finns i Azure-plattformen, inklusive skapandet av nya resurser som HDInsight-kluster.

> [!NOTE]  
> Stegen i det här dokumentet använder [curl (verktygethttps://curl.haxx.se/) ](https://curl.haxx.se/) för att kommunicera med Azure REST API.

## <a name="create-a-template"></a>Skapa en mall

Azure Resource Manager-mallar är JSON-dokument som beskriver en **resursgrupp** och alla resurser i den (till exempel HDInsight.) Med den här mallbaserade metoden kan du definiera de resurser som du behöver för HDInsight i en mall.

Följande JSON-dokument är en sammanslagning av [https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-ssh-password](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-ssh-password)mall- och parameterfilerna från , som skapar ett Linux-baserat kluster med ett lösenord för att skydda SSH-användarkontot.

   ```json
   {
       "properties": {
           "template": {
               "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
               "contentVersion": "1.0.0.0",
               "parameters": {
                   "clusterType": {
                       "type": "string",
                       "allowedValues": ["hadoop",
                       "hbase",
                       "storm",
                       "spark"],
                       "metadata": {
                           "description": "The type of the HDInsight cluster to create."
                       }
                   },
                   "clusterName": {
                       "type": "string",
                       "metadata": {
                           "description": "The name of the HDInsight cluster to create."
                       }
                   },
                   "clusterLoginUserName": {
                       "type": "string",
                       "metadata": {
                           "description": "These credentials can be used to submit jobs to the cluster and to log into cluster dashboards."
                       }
                   },
                   "clusterLoginPassword": {
                       "type": "securestring",
                       "metadata": {
                           "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
                       }
                   },
                   "sshUserName": {
                       "type": "string",
                       "metadata": {
                           "description": "These credentials can be used to remotely access the cluster."
                       }
                   },
                   "sshPassword": {
                       "type": "securestring",
                       "metadata": {
                           "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
                       }
                   },
                   "clusterStorageAccountName": {
                       "type": "string",
                       "metadata": {
                           "description": "The name of the storage account to be created and be used as the cluster's storage."
                       }
                   },
                   "clusterWorkerNodeCount": {
                       "type": "int",
                       "defaultValue": 4,
                       "metadata": {
                           "description": "The number of nodes in the HDInsight cluster."
                       }
                   }
               },
               "variables": {
                   "defaultApiVersion": "2015-05-01-preview",
                   "clusterApiVersion": "2015-03-01-preview"
               },
               "resources": [{
                   "name": "[parameters('clusterStorageAccountName')]",
                   "type": "Microsoft.Storage/storageAccounts",
                   "location": "[resourceGroup().location]",
                   "apiVersion": "[variables('defaultApiVersion')]",
                   "dependsOn": [],
                   "tags": {

                   },
                   "properties": {
                       "accountType": "Standard_LRS"
                   }
               },
               {
                   "name": "[parameters('clusterName')]",
                   "type": "Microsoft.HDInsight/clusters",
                   "location": "[resourceGroup().location]",
                   "apiVersion": "[variables('clusterApiVersion')]",
                   "dependsOn": ["[concat('Microsoft.Storage/storageAccounts/',parameters('clusterStorageAccountName'))]"],
                   "tags": {

                   },
                   "properties": {
                       "clusterVersion": "3.6",
                       "osType": "Linux",
                       "clusterDefinition": {
                           "kind": "[parameters('clusterType')]",
                           "configurations": {
                               "gateway": {
                                   "restAuthCredential.isEnabled": true,
                                   "restAuthCredential.username": "[parameters('clusterLoginUserName')]",
                                   "restAuthCredential.password": "[parameters('clusterLoginPassword')]"
                               }
                           }
                       },
                       "storageProfile": {
                           "storageaccounts": [{
                               "name": "[concat(parameters('clusterStorageAccountName'),'.blob.core.windows.net')]",
                               "isDefault": true,
                               "container": "[parameters('clusterName')]",
                               "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('clusterStorageAccountName')), variables('defaultApiVersion')).key1]"
                           }]
                       },
                       "computeProfile": {
                           "roles": [{
                               "name": "headnode",
                               "targetInstanceCount": "2",
                               "hardwareProfile": {
                                   "vmSize": "{}" 
                               },
                               "osProfile": {
                                   "linuxOperatingSystemProfile": {
                                       "username": "[parameters('sshUserName')]",
                                       "password": "[parameters('sshPassword')]"
                                   }
                               }
                           },
                           {
                               "name": "workernode",
                               "targetInstanceCount": "[parameters('clusterWorkerNodeCount')]",
                               "hardwareProfile": {
                                   "vmSize": "{}"
                               },
                               "osProfile": {
                                   "linuxOperatingSystemProfile": {
                                       "username": "[parameters('sshUserName')]",
                                       "password": "[parameters('sshPassword')]"
                                   }
                               }
                           }]
                       }
                   }
               }],
               "outputs": {
                   "cluster": {
                       "type": "object",
                       "value": "[reference(resourceId('Microsoft.HDInsight/clusters',parameters('clusterName')))]"
                   }
               }
           },
           "mode": "incremental",
           "Parameters": {
               "clusterName": {
                   "value": "newclustername"
               },
               "clusterType": {
                   "value": "hadoop"
               },
               "clusterStorageAccountName": {
                   "value": "newstoragename"
               },
               "clusterLoginUserName": {
                   "value": "admin"
               },
               "clusterLoginPassword": {
                   "value": "changeme"
               },
               "sshUserName": {
                   "value": "sshuser"
               },
               "sshPassword": {
                   "value": "changeme"
               }
           }
       }
   }
   ```

Det här exemplet används i stegen i det här dokumentet. Ersätt *exempelvärdena* i avsnittet **Parametrar** med värdena för klustret.

> [!IMPORTANT]  
> Mallen använder standardantalet arbetsnoder (4) för ett HDInsight-kluster. Om du planerar mer än 32 arbetsnoder måste du välja en huvudnodstorlek med minst 8 kärnor och 14 GB RAM.If you plan on more than 32 worker nodes, then you must select a head node size with least 8 cores and 14-GB RAM.
>
> Mer information om nodstorlekar och relaterade kostnader finns i [HDInsight-prissättning](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="sign-in-to-your-azure-subscription"></a>Logga in på din Azure-prenumeration

Följ stegen som dokumenteras i Kom igång med Azure `az login` [CLI](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2) och anslut till din prenumeration med kommandot.

## <a name="create-a-service-principal"></a>Skapa ett huvudnamn för tjänsten

> [!NOTE]  
> De här stegen är en förkortad version av *huvudnumret för Skapa tjänsten med lösenord* i avsnittet Använd Azure CLI för att skapa ett [tjänsthuvudnamn för att komma åt](../azure-resource-manager/resource-group-authenticate-service-principal-cli.md) resursdokumentet. De här stegen skapar ett tjänsthuvudnamn som används för att autentisera till Azure REST API.

1. Från en kommandorad använder du följande kommando för att lista dina Azure-prenumerationer.

   ```azurecli
   az account list --query '[].{Subscription_ID:id,Tenant_ID:tenantId,Name:name}'  --output table
   ```

    I listan väljer du den prenumeration som du vill använda och noterar **Subscription_ID** och __Tenant_ID__ kolumnerna. Spara dessa värden.

2. Använd följande kommando för att skapa ett program i Azure Active Directory.

   ```azurecli
   az ad app create --display-name "exampleapp" --homepage "https://www.contoso.org" --identifier-uris "https://www.contoso.org/example" --password <Your password> --query 'appId'
   ```

    Ersätt värdena `--display-name`för `--homepage`, `--identifier-uris` och med dina egna värden. Ange ett lösenord för den nya Active Directory-posten.

   > [!NOTE]  
   > `--home-page` Värdena `--identifier-uris` och behöver inte referera till en verklig webbsida som finns på internet. De måste vara unika URI:er.

   Värdet som returneras från det här kommandot är __app-ID__ för det nya programmet. Spara det här värdet.

3. Använd följande kommando för att skapa ett tjänsthuvudnamn med hjälp av **app-ID**.

   ```azurecli
   az ad sp create --id <App ID> --query 'objectId'
   ```

     Värdet som returneras från det här kommandot är __objekt-ID__. Spara det här värdet.

4. Tilldela **ägarrollen** till tjänstens huvudnamn med värdet **Objekt-ID.** Använd **prenumerations-ID:t** som du fick tidigare.

   ```azurecli
   az role assignment create --assignee <Object ID> --role Owner --scope /subscriptions/<Subscription ID>/
   ```

## <a name="get-an-authentication-token"></a>Hämta en autentiseringstoken

Använd följande kommando för att hämta en autentiseringstoken:

```bash
curl -X "POST" "https://login.microsoftonline.com/$TENANTID/oauth2/token" \
-H "Cookie: flight-uxoptin=true; stsservicecookie=ests; x-ms-gateway-slice=productionb; stsservicecookie=ests" \
-H "Content-Type: application/x-www-form-urlencoded" \
--data-urlencode "client_id=$APPID" \
--data-urlencode "grant_type=client_credentials" \
--data-urlencode "client_secret=$PASSWORD" \
--data-urlencode "resource=https://management.azure.com/"
```

Ange `$TENANTID` `$APPID`, `$PASSWORD` och till de värden som erhållits eller använts tidigare.

Om den här begäran lyckas får du ett svar i 200 serier och svarstexten innehåller ett JSON-dokument.

JSON-dokumentet som returneras av den här begäran innehåller ett element med namnet **access_token**. Värdet **för access_token** används för autentiseringsbegäranden till REST API.The value of access_token is used to authentication requests to the REST API.

```json
{
    "token_type":"Bearer",
    "expires_in":"3599",
    "expires_on":"1463409994",
    "not_before":"1463406094",
    "resource":"https://management.azure.com/","access_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWoNBVGZNNXBPWWlKSE1iYTlnb0VLWSIsImtpZCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSJ9.eyJhdWQiOiJodHRwczovL21hbmFnZW1lbnQuYXp1cmUuY29tLyIsImlzcyI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI2Ny8iLCJpYXQiOjE0NjM0MDYwOTQsIm5iZiI6MTQ2MzQwNjA5NCwiZXhwIjoxNDYzNDA5OTk5LCJhcHBpZCI6IjBlYzcyMzM0LTZkMDMtNDhmYi04OWU1LTU2NTJiODBiZDliYiIsImFwcGlkYWNyIjoiMSIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0Ny8iLCJvaWQiOiJlNjgxZTZiMi1mZThkLTRkZGUtYjZiMS0xNjAyZDQyNWQzOWYiLCJzdWIiOiJlNjgxZTZiMi1mZThkLTRkZGUtYjZiMS0xNjAyZDQyNWQzOWYiLCJ0aWQiOiI3MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDciLCJ2ZXIiOiIxLjAifQ.nJVERbeDHLGHn7ZsbVGBJyHOu2PYhG5dji6F63gu8XN2Cvol3J1HO1uB4H3nCSt9DTu_jMHqAur_NNyobgNM21GojbEZAvd0I9NY0UDumBEvDZfMKneqp7a_cgAU7IYRcTPneSxbD6wo-8gIgfN9KDql98b0uEzixIVIWra2Q1bUUYETYqyaJNdS4RUmlJKNNpENllAyHQLv7hXnap1IuzP-f5CNIbbj9UgXxLiOtW5JhUAwWLZ3-WMhNRpUO2SIB7W7tQ0AbjXw3aUYr7el066J51z5tC1AK9UC-mD_fO_HUP6ZmPzu5gLA6DxkIIYP3grPnRVoUDltHQvwgONDOw"
}
```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Använd följande för att skapa en resursgrupp.

* Ange `$SUBSCRIPTIONID` till det mottagna prenumerations-ID:t när tjänstens huvudnamn skapas.
* Ange `$ACCESSTOKEN` till åtkomsttoken som togs emot i föregående steg.
* Ersätt `DATACENTERLOCATION` med det datacenter som du vill skapa resursgruppen och resurser i. Till exempel "Södra centrala USA".
* Ange `$RESOURCEGROUPNAME` det namn som du vill använda för den här gruppen:

```bash
curl -X "PUT" "https://management.azure.com/subscriptions/$SUBSCRIPTIONID/resourcegroups/$RESOURCEGROUPNAME?api-version=2015-01-01" \
    -H "Authorization: Bearer $ACCESSTOKEN" \
    -H "Content-Type: application/json" \
    -d $'{
"location": "DATACENTERLOCATION"
}'
```

Om den här begäran lyckas får du ett svar i 200 serier och svarstexten innehåller ett JSON-dokument som innehåller information om gruppen. Elementet `"provisioningState"` innehåller värdet `"Succeeded"`.

## <a name="create-a-deployment"></a>Skapa en distribution

Använd följande kommando för att distribuera mallen till resursgruppen.

* Ange `$DEPLOYMENTNAME` det namn som du vill använda för den här distributionen.

```bash
curl -X "PUT" "https://management.azure.com/subscriptions/$SUBSCRIPTIONID/resourcegroups/$RESOURCEGROUPNAME/providers/microsoft.resources/deployments/$DEPLOYMENTNAME?api-version=2015-01-01" \
-H "Authorization: Bearer $ACCESSTOKEN" \
-H "Content-Type: application/json" \
-d "{set your body string to the template and parameters}"
```

> [!NOTE]  
> Om du sparade mallen i en fil kan `-d "{ template and parameters}"`du använda följande kommando i stället för:
>
> `--data-binary "@/path/to/file.json"`

Om den här begäran lyckas får du ett svar i 200-serien och svarstexten innehåller ett JSON-dokument som innehåller information om distributionsåtgärden.

> [!IMPORTANT]  
> Distributionen har skickats, men har inte slutförts. Det kan ta flera minuter, vanligtvis runt 15, för distributionen att slutföras.

## <a name="check-the-status-of-a-deployment"></a>Kontrollera status för en distribution

Om du vill kontrollera distributionens status använder du följande kommando:

```bash
curl -X "GET" "https://management.azure.com/subscriptions/$SUBSCRIPTIONID/resourcegroups/$RESOURCEGROUPNAME/providers/microsoft.resources/deployments/$DEPLOYMENTNAME?api-version=2015-01-01" \
-H "Authorization: Bearer $ACCESSTOKEN" \
-H "Content-Type: application/json"
```

Det här kommandot returnerar ett JSON-dokument som innehåller information om distributionsåtgärden. Elementet `"provisioningState"` innehåller status för distributionen. Om det här elementet `"Succeeded"`innehåller ett värde på har distributionen slutförts.

## <a name="troubleshoot"></a>Felsöka

Om du får problem med att skapa HDInsight-kluster läser du [åtkomstkontrollkrav](./hdinsight-hadoop-customize-cluster-linux.md#access-control).

## <a name="next-steps"></a>Nästa steg

Nu när du har skapat ett HDInsight-kluster kan du använda följande för att lära dig hur du arbetar med klustret.

### <a name="apache-hadoop-clusters"></a>Apache Hadoop kluster

* [Använda Apache Hive med HDInsight](hadoop/hdinsight-use-hive.md)
* [Använd MapReduce med HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Apache HBase-kluster

* [Komma igång med Apache HBase på HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Utveckla Java-program för Apache HBase på HDInsight](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="apache-storm-clusters"></a>Apache Storm kluster

* [Utveckla Java-topologier för Apache Storm på HDInsight](storm/apache-storm-develop-java-topology.md)
* [Använda Python-komponenter i Apache Storm på HDInsight](storm/apache-storm-develop-python-topology.md)
* [Distribuera och övervaka topologier med Apache Storm på HDInsight](storm/apache-storm-deploy-monitor-topology-linux.md)
