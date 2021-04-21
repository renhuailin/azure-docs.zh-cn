---
title: 为 MongoDB API for Azure Cosmos DB 的数据库和集合创建资源锁
description: 为 MongoDB API for Azure Cosmos DB 的数据库和集合创建资源锁
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: sample
ms.date: 07/29/2020
ms.openlocfilehash: ee298b33736ae25b365cc54ee1bb5ec9f38bfb62
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/20/2021
ms.locfileid: "107763590"
---
# <a name="create-a-resource-lock-for-azure-cosmos-dbs-api-for-mongodb-using-azure-cli"></a>使用 Azure CLI 为 Azure Cosmos DB 的 MongoDB API 创建资源锁
[!INCLUDE[appliesto-mongodb-api](../../../includes/appliesto-mongodb-api.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../../includes/azure-cli-prepare-your-environment.md)]

- 本文需要 Azure CLI 版本 2.9.1 或更高版本。 如果使用 Azure Cloud Shell，则最新版本已安装。

> [!IMPORTANT]
> 除非在启用了 `disableKeyBasedMetadataWriteAccess` 属性的情况下先锁定 Cosmos DB 帐户，否则资源锁对使用任何 MongoDB SDK、Mongoshell、任何工具或 Azure 门户进行连接的用户所做的更改均无效。 要详细了解如何启用此属性，请参阅[防止 SDK 更改](../../../role-based-access-control.md#prevent-sdk-changes)。

## <a name="sample-script"></a>示例脚本

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/mongodb/lock.sh "Create a resource lock for an Azure Cosmos DB MongoDB API database and collection.")]

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [az lock create](/cli/azure/lock#az_lock_create) | 创建锁。 |
| [az lock list](/cli/azure/lock#az_lock_list) | 列出锁信息。 |
| [az lock show](/cli/azure/lock#az_lock_show) | 显示锁的属性。 |
| [az lock delete](/cli/azure/lock#az_lock_delete) | 删除锁。 |

## <a name="next-steps"></a>后续步骤

- [锁定资源以防止意外更改](../../../../azure-resource-manager/management/lock-resources.md)

- [Azure Cosmos DB CLI 文档](/cli/azure/cosmosdb)。

- [Azure Cosmos DB CLI GitHub 存储库](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb)。
