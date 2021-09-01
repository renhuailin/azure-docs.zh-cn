---
title: 适用于 Azure Cosmos DB Core (SQL) API 的 Azure PowerShell 示例
description: 获取用于在 Azure Cosmos DB Core (SQL) API 中执行常见任务的 Azure PowerShell 示例
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 01/20/2021
ms.author: mjbrown
ms.openlocfilehash: f3e4f38af571f0356cfa92776801784a97225cde
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123117362"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db-core-sql-api"></a>适用于 Azure Cosmos DB Core (SQL) API 的 Azure PowerShell 示例
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

下表包含常用于 Azure Cosmos DB 的 Azure PowerShell 脚本的链接。 使用右侧的链接可导航到 API 特定示例。 常见示例在所有 API 间是相同的。 [Azure PowerShell 参考](/powershell/module/az.cosmosdb)中收录了所有 Azure Cosmos DB PowerShell cmdlet 的参考页。 `Az.CosmosDB` 模块现在是 `Az` 模块的一部分。 [下载并安装](/powershell/azure/install-az-ps) Az 模块的最新版本，获取 Azure Cosmos DB cmdlet。 还可从 [PowerShell 库](https://www.powershellgallery.com/packages/Az/5.4.0)中获取最新版本。 还可以从我们的 GitHub 存储库 [GitHub 上的 Cosmos DB PowerShell 示例](https://github.com/Azure/azure-docs-powershell-samples/tree/master/cosmosdb)创建这些适用于 Cosmos DB 的 PowerShell 示例的分支。

对于其他 API 的 PowerShell cmdlet，请参阅[适用于 Cassandra 的 PowerShell 示例](../cassandra/powershell-samples.md)、[适用于 MongoDB API 的 PowerShell 示例](../mongodb/powershell-samples.md)、[适用于 Gremlin 的 PowerShell 示例](../graph/powershell-samples.md)、[适用于 Table 的 PowerShell 示例](../table/powershell-samples.md)

## <a name="common-samples"></a>常见示例

|任务 | 说明 |
|---|---|
|[更新帐户](../scripts/powershell/common/account-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 更新 Cosmos DB 帐户的默认一致性级别。 |
|[更新帐户的区域](../scripts/powershell/common/update-region.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 更新 Cosmos DB 帐户的区域。 |
|[更改故障转移优先级或触发故障转移](../scripts/powershell/common/failover-priority-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 更改 Azure Cosmos 帐户的区域故障转移优先级或触发手动故障转移。 |
|[帐户密钥或连接字符串](../scripts/powershell/common/keys-connection-strings.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 获取 Azure Cosmos DB 帐户的主密钥和辅助密钥、连接字符串或重新生成其帐户密钥。 |
|[创建启用 IP 防火墙的 Cosmos 帐户](../scripts/powershell/common/firewall-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 创建启用 IP 防火墙的 Azure Cosmos DB 帐户。 |
|||

## <a name="core-sql-api-samples"></a>Core (SQL) API 示例

|任务 | 说明 |
|---|---|
|[创建帐户、数据库和容器](../scripts/powershell/sql/create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 创建 Azure Cosmos DB 帐户、数据库和容器。 |
|[创建帐户、数据库和容器（具有自动缩放功能）](../scripts/powershell/sql/autoscale.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 创建 Azure Cosmos DB 帐户、数据库和容器（具有自动缩放功能）。 |
|[创建具有大分区键的容器](../scripts/powershell/sql/create-large-partition-key.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 创建具有大分区键的容器。 |
|[使用无索引策略创建容器](../scripts/powershell/sql/create-index-none.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 在索引策略关闭的情况下创建 Azure Cosmos 容器。|
|[列出或获取数据库或容器](../scripts/powershell/sql/list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 列出或获取数据库或容器。 |
|[吞吐量操作](../scripts/powershell/sql/throughput.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 数据库或容器的吞吐量操作，包括获取、更新自动缩放和标准吞吐量和在其之间进行迁移。 |
|[锁定资源以防止将其删除](../scripts/powershell/sql/lock.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 通过资源锁防止资源遭到删除。 |
|||
