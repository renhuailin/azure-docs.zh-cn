---
title: 用于 Azure Cosmos DB Core (SQL) API 资源的吞吐量 (RU/s) 操作的 Azure CLI 脚本
description: 用于 Azure Cosmos DB Core (SQL) API 资源的吞吐量 (RU/s) 操作的 Azure CLI 脚本
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 10/07/2020
ms.openlocfilehash: 2df31e5903785b6e25ea79a107a53084849c66fe
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/20/2021
ms.locfileid: "107789080"
---
# <a name="throughput-rus-operations-with-azure-cli-for-a-database-or-container-for-azure-cosmos-db-core-sql-api"></a>针对 Azure Cosmos DB Core (SQL) API 的数据库或容器的，使用 Azure CLI 的吞吐量 (RU/s) 操作
[!INCLUDE[appliesto-sql-api](../../../includes/appliesto-sql-api.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../../includes/azure-cli-prepare-your-environment.md)]

- 本文需要 Azure CLI 版本 2.12.1 或更高版本。 如果使用 Azure Cloud Shell，则最新版本已安装。

## <a name="sample-script"></a>示例脚本

此脚本创建一个具有共享吞吐量的 Core (SQL) API 数据库和一个具有专用吞吐量的 Core (SQL) API 容器，然后更新该数据库和该容器的吞吐量。 脚本随后从标准吞吐量迁移到自动缩放吞吐量，然后在迁移后读取自动缩放吞吐量的值。

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/sql/throughput.sh "Throughput operations for a SQL database and container.")]

## <a name="clean-up-deployment"></a>清理部署

运行脚本示例后，可以使用以下命令删除资源组以及与其关联的所有资源。

```azurecli-interactive
az group delete --name $resourceGroupName
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | 创建用于存储所有资源的资源组。 |
| [az cosmosdb create](/cli/azure/cosmosdb#az_cosmosdb_create) | 创建 Azure Cosmos DB 帐户。 |
| [az cosmosdb sql database create](/cli/azure/cosmosdb/sql/database#az_cosmosdb_sql_database_create) | 创建 Azure Cosmos Core (SQL) 数据库。 |
| [az cosmosdb sql container create](/cli/azure/cosmosdb/sql/container#az_cosmosdb_sql_container_create) | 创建 Azure Cosmos Core (SQL) 容器。 |
| [az cosmosdb sql database throughput update](/cli/azure/cosmosdb/sql/database/throughput#az_cosmosdb_sql_database_throughput_update) | 更新 Azure Cosmos Core (SQL) 数据库的吞吐量。 |
| [az cosmosdb sql container throughput update](/cli/azure/cosmosdb/sql/container/throughput#az_cosmosdb_sql_container_throughput_update) | 更新 Azure Cosmos Core (SQL) 容器的吞吐量。 |
| [az cosmosdb sql 数据库吞吐量迁移](/cli/azure/cosmosdb/sql/database/throughput#az_cosmosdb_sql_database_throughput_migrate) | 迁移 Azure Cosmos Core (SQL) 数据库的吞吐量。 |
| [az cosmosdb sql 容器吞吐量迁移](/cli/azure/cosmosdb/sql/container/throughput#az_cosmosdb_sql_container_throughput_migrate) | 迁移 Azure Cosmos Core (SQL) 容器的吞吐量。 |
| [az group delete](/cli/azure/resource#az_resource_delete) | 删除资源组，包括所有嵌套的资源。 |

## <a name="next-steps"></a>后续步骤

有关 Azure Cosmos DB CLI 的详细信息，请参阅 [Azure Cosmos DB CLI 文档](/cli/azure/cosmosdb)。

可以在 [Azure Cosmos DB CLI GitHub 存储库](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb)中找到所有 Azure Cosmos DB CLI 脚本示例。
