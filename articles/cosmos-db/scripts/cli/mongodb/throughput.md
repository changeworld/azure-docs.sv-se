---
title: Uppdatera RU/s för en databas och samling för MongoDB API för Azure Cosmos DB
description: Uppdatera RU/s för en databas och samling för MongoDB API för Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: sample
ms.date: 9/25/2019
ms.openlocfilehash: 20516a66fe664e415b97e40beacd77c34c7ccaea
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "71275509"
---
# <a name="update-rus-for-a-database-and-collection-for-mongodb-api-for-azure-cosmos-db-using-azure-cli"></a>Uppdatera RU/s för en databas och samling för MongoDB API för Azure Cosmos DB med Azure CLI

[!INCLUDE [cloud-shell-try-it.md](../../../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt kräver det här avsnittet att du kör Azure CLI version 2.0.73 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Exempelskript

Det här skriptet skapar en databas med delat dataflöde och en samling med dedikerat dataflöde för Azure Cosmos DB för MongoDB API och uppdaterar sedan dataflödet för både databasen och samlingen.

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/mongodb/throughput.sh "Update RU/s for an Azure Cosmos DB MongoDB API database and collection.")]

## <a name="clean-up-deployment"></a>Rensa distribution

När skriptexemplet har körts kan följande kommando användas för att ta bort resursgruppen och alla resurser som är kopplade till den.

```azurecli-interactive
az group delete --name $resourceGroupName
```

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Skapar en resursgrupp där alla resurser lagras. |
| [az cosmosdb create](/cli/azure/cosmosdb#az-cosmosdb-create) | Skapar ett Azure Cosmos DB-konto. |
| [az cosmosdb mongodb databas skapa](/cli/azure/cosmosdb/mongodb/database#az-cosmosdb-mongodb-database-create) | Skapar en Azure Cosmos MongoDB API-databas. |
| [az cosmosdb mongodb samling skapa](/cli/azure/cosmosdb/mongodb/collection#az-cosmosdb-mongodb-collection-create) | Skapar en Azure Cosmos MongoDB API-samling. |
| [az cosmosdb mongodb databas dataflöde uppdatering](/cli/azure/cosmosdb/mongodb/database/throughput#az-cosmosdb-mongodb-database-throughput-update) | Uppdatera ru:er för en Azure Cosmos MongoDB API-databas. |
| [az cosmosdb mongodb samling genomströmning uppdatering](/cli/azure/cosmosdb/mongodb/collection/throughput#az-cosmosdb-mongodb-collection-throughput-update) | Uppdatera ru:er för en Azure Cosmos MongoDB API-samling. |
| [az group delete](/cli/azure/resource#az-resource-delete) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure Cosmos DB CLI finns i [Azure Cosmos DB CLI-dokumentation](/cli/azure/cosmosdb).

Alla Azure Cosmos DB CLI-skriptexempel finns i [Azure Cosmos DB CLI GitHub Repository](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).
