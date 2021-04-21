---
title: 创建 Azure Cosmos DB 的 Gremlin 数据库和图
description: 创建 Azure Cosmos DB 的 Gremlin 数据库和图
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: sample
ms.date: 07/29/2020
ms.openlocfilehash: 8b551517dfd0066091086944d02a464fd484b8af
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/20/2021
ms.locfileid: "107770674"
---
# <a name="create-an-azure-cosmos-gremlin-api-account-database-and-graph-using-azure-cli"></a>使用 Azure CLI 创建 Azure Cosmos Gremlin API 帐户、数据库和图
[!INCLUDE[appliesto-gremlin-api](../../../includes/appliesto-gremlin-api.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../../includes/azure-cli-prepare-your-environment.md)]

- 本文需要 Azure CLI 版本 2.9.1 或更高版本。 如果使用 Azure Cloud Shell，则最新版本已安装。

## <a name="sample-script"></a>示例脚本

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/gremlin/create.sh "Create an Azure Cosmos DB Gremlin API account, database, and graph.")]

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
| [az cosmosdb gremlin database create](/cli/azure/cosmosdb/gremlin/database#az_cosmosdb_gremlin_database_create) | 创建 Azure Cosmos Gremlin 数据库。 |
| [az cosmosdb gremlin graph create](/cli/azure/cosmosdb/gremlin/graph#az_cosmosdb_gremlin_graph_create) | 创建 Azure Cosmos Gremlin 图。 |
| [az group delete](/cli/azure/resource#az_resource_delete) | 删除资源组，包括所有嵌套的资源。 |

## <a name="next-steps"></a>后续步骤

有关 Azure Cosmos DB CLI 的详细信息，请参阅 [Azure Cosmos DB CLI 文档](/cli/azure/cosmosdb)。

可以在 [Azure Cosmos DB CLI GitHub 存储库](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb)中找到所有 Azure Cosmos DB CLI 脚本示例。
