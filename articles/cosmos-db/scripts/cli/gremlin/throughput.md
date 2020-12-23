---
title: 用于 Azure Cosmos DB Gremlin API 资源的吞吐量 (RU/s) 操作的 Azure CLI 脚本
description: 用于 Azure Cosmos DB Gremlin API 资源的吞吐量 (RU/s) 操作的 Azure CLI 脚本
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: sample
ms.date: 10/07/2020
ms.openlocfilehash: 0aa05d165f83eec4bacb588ce974a18034918028
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/12/2020
ms.locfileid: "94565562"
---
# <a name="throughput-rus-operations-with-azure-cli-for-a-database-or-graph-for-azure-cosmos-db---gremlin-api"></a>针对用于 Azure Cosmos DB Gremlin API 的数据库或图形，使用 Azure CLI 实现的吞吐量 (RU/s) 操作
[!INCLUDE[appliesto-gremlin-api](../../../includes/appliesto-gremlin-api.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../../includes/azure-cli-prepare-your-environment.md)]

- 本文需要 Azure CLI 版本 2.12.1 或更高版本。 如果使用 Azure Cloud Shell，则最新版本已安装。

## <a name="sample-script"></a>示例脚本

此脚本创建一个具有共享吞吐量的 Gremlin 数据库和一个具有专用吞吐量的 Gremlin 图，然后更新该数据库和该图的吞吐量。 脚本随后从标准吞吐量迁移到自动缩放吞吐量，然后在迁移后读取自动缩放吞吐量的值。

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/gremlin/throughput.sh "Throughput operations for a Gremlin database and graph.")]

## <a name="clean-up-deployment"></a>清理部署

运行脚本示例后，可以使用以下命令删除资源组以及与其关联的所有资源。

```azurecli-interactive
az group delete --name $resourceGroupName
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | 创建用于存储所有资源的资源组。 |
| [az cosmosdb create](/cli/azure/cosmosdb#az-cosmosdb-create) | 创建 Azure Cosmos DB 帐户。 |
| [az cosmosdb gremlin database create](/cli/azure/cosmosdb/gremlin/database#az-cosmosdb-gremlin-database-create) | 创建 Azure Cosmos Gremlin 数据库。 |
| [az cosmosdb gremlin graph create](/cli/azure/cosmosdb/gremlin/graph#az-cosmosdb-gremlin-graph-create) | 创建 Azure Cosmos Gremlin 图。 |
| [az cosmosdb gremlin database throughput update](/cli/azure/cosmosdb/gremlin/database/throughput#az-cosmosdb-gremlin-database-throughput-update) | 更新 Azure Cosmos Gremlin 数据库的 RU/秒。 |
| [az cosmosdb gremlin graph throughput update](/cli/azure/cosmosdb/gremlin/graph/throughput#az-cosmosdb-gremlin-graph-throughput-update) | 更新 Azure Cosmos Gremlin 图的 RU/秒。 |
| [az cosmosdb gremlin database throughput migrate](/cli/azure/cosmosdb/gremlin/database/throughput#az_cosmosdb_gremlin_database_throughput_migrate) | 迁移 Azure Cosmos Gremlin 数据库的吞吐量。 |
| [az cosmosdb gremlin graph throughput migrate](/cli/azure/cosmosdb/gremlin/graph/throughput#az_cosmosdb_gremlin_graph_throughput_migrate) | 迁移 Azure Cosmos Gremlin 图形的吞吐量。 |
| [az group delete](/cli/azure/resource#az-resource-delete) | 删除资源组，包括所有嵌套的资源。 |

## <a name="next-steps"></a>后续步骤

有关 Azure Cosmos DB CLI 的详细信息，请参阅 [Azure Cosmos DB CLI 文档](/cli/azure/cosmosdb)。

可以在 [Azure Cosmos DB CLI GitHub 存储库](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb)中找到所有 Azure Cosmos DB CLI 脚本示例。
