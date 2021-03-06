---
title: Uppdatera RU/s för en SQL-API-databas och -behållare (Core) för Azure Cosmos DB
description: Uppdatera RU/s för en SQL-API-databas och -behållare (Core) för Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 9/25/2019
ms.openlocfilehash: cde0615a5312372992d2604f32809a983c248a2b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "71275397"
---
# <a name="update-rus-for-a-sql-core-api-database-and-container-for-azure-cosmos-db-using-azure-cli"></a>Uppdatera RU/s för en SQL-API-databas och -behållare (Core) för Azure Cosmos DB med Azure CLI

[!INCLUDE [cloud-shell-try-it.md](../../../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt kräver det här avsnittet att du kör Azure CLI version 2.0.73 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Exempelskript

Det här skriptet skapar en SQL-API-databas (Core) med delat dataflöde och en SQL-API-behållare (Core) med dedikerat dataflöde och uppdaterar sedan dataflödet för både databasen och behållaren.

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/sql/throughput.sh "Update RU/s for a SQL database and container.")]

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
| [az cosmosdb sql databas skapa](/cli/azure/cosmosdb/sql/database#az-cosmosdb-sql-database-create) | Skapar en Azure Cosmos SQL-databas (Core). |
| [az cosmosdb sql container skapa](/cli/azure/cosmosdb/sql/container#az-cosmosdb-sql-container-create) | Skapar en Azure Cosmos SQL-behållare (Core). |
| [az cosmosdb sql databas dataflöde uppdatering](/cli/azure/cosmosdb/sql/database/throughput#az-cosmosdb-sql-database-throughput-update) | Uppdatera RU/s för en Azure Cosmos SQL-databas (Core). |
| [az cosmosdb sql container dataflöde uppdatering](/cli/azure/cosmosdb/sql/container/throughput#az-cosmosdb-sql-container-throughput-update) | Uppdatera RU/s för en Azure Cosmos SQL-behållare (Core). |
| [az group delete](/cli/azure/resource#az-resource-delete) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure Cosmos DB CLI finns i [Azure Cosmos DB CLI-dokumentation](/cli/azure/cosmosdb).

Alla Azure Cosmos DB CLI-skriptexempel finns i [Azure Cosmos DB CLI GitHub Repository](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).
