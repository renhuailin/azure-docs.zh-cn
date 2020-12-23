---
title: 用于 Azure Cosmos DB SQL API 数据库或容器的吞吐量 (RU/s) 操作的 PowerShell 脚本
description: 用于 Azure Cosmos DB SQL API 数据库或容器的吞吐量 (RU/s) 操作的 PowerShell 脚本
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 10/07/2020
ms.author: mjbrown
ms.openlocfilehash: 49e969474ab58f48ca4df99a08d9db19bd99059d
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93100348"
---
# <a name="throughput-rus-operations-with-powershell-for-a-database-or-container-for-azure-cosmos-db-core-sql-api"></a>用于 Azure Cosmos DB Core (SQL) API 的数据库或容器的使用 PowerShell 的吞吐量 (RU/s) 操作
[!INCLUDE[appliesto-sql-api](../../../includes/appliesto-sql-api.md)]

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="get-throughput"></a>获取吞吐量

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/sql/ps-sql-ru-get.ps1 "Get throughput (RU/s) for Azure Cosmos DB Core (SQL) API database or container")]

## <a name="update-throughput"></a>更新吞吐量

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/sql/ps-sql-ru-update.ps1 "Update throughput (RU/s) for an Azure Cosmos DB Core (SQL) API database or container")]

## <a name="migrate-throughput"></a>迁移吞吐量

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/sql/ps-sql-ru-migrate.ps1 "Migrate between standard and autoscale throughput on an Azure Cosmos DB Core (SQL) API database or container")]

## <a name="clean-up-deployment"></a>清理部署

运行脚本示例后，可以使用以下命令删除资源组以及与其关联的所有资源。

```powershell
Remove-AzResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
|**Azure Cosmos DB**| |
| [Get-AzCosmosDBSqlDatabaseThroughput](/powershell/module/az.cosmosdb/get-azcosmosdbsqldatabasethroughput) | 获取在 Azure Cosmos DB Core (SQL) API 数据库上预配的吞吐量。 |
| [Get-AzCosmosDBSqlContainerThroughput](/powershell/module/az.cosmosdb/get-azcosmosdbsqlcontainerthroughput) | 获取在 Azure Cosmos DB Core (SQL) API 容器上预配的吞吐量。 |
| [Update-AzCosmosDBSqlDatabaseThroughput](/powershell/module/az.cosmosdb/get-azcosmosdbsqldatabase) | 更新 Azure Cosmos DB Core (SQL) API 数据库的吞吐量值。 |
| [Update-AzCosmosDBSqlContainerThroughput](/powershell/module/az.cosmosdb/get-azcosmosdbsqlcontainer) | 更新 Azure Cosmos DB Core (SQL) API 容器的吞吐量值。 |
| [Invoke-AzCosmosDBSqlDatabaseThroughputMigration](/powershell/module/az.cosmosdb/invoke-azcosmosdbsqldatabasethroughputmigration) | 迁移 Azure Cosmos DB Core (SQL) API 数据库的吞吐量。 |
| [Invoke-AzCosmosDBSqlContainerThroughputMigration](/powershell/module/az.cosmosdb/invoke-azcosmosdbsqlcontainerthroughputmigration) | 迁移 Azure Cosmos DB Core (SQL) API 容器的吞吐量。 |
|**Azure 资源组**| |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | 删除资源组，包括所有嵌套的资源。 |
|||

## <a name="next-steps"></a>后续步骤

有关 Azure PowerShell 的详细信息，请参阅 [Azure PowerShell 文档](/powershell/)。