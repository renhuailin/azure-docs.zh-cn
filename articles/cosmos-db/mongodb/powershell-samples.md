---
title: 用于 MongoDB 的 Azure Cosmos DB API 的 Azure PowerShell 示例
description: 获取 Azure PowerShell 示例以在用于 MongoDB 的 Azure Cosmos DB API 中执行常见任务
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: sample
ms.date: 01/20/2021
ms.author: mjbrown
ms.openlocfilehash: 5810247e93c21a9b992c00ecc2ce8ffd04623281
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121783134"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db-api-for-mongodb"></a>用于 MongoDB 的 Azure Cosmos DB API 的 Azure PowerShell 示例
[!INCLUDE[appliesto-mongodb-api](../includes/appliesto-mongodb-api.md)]

下表包含常用于 Azure Cosmos DB 的 Azure PowerShell 脚本的链接。 使用右侧的链接可导航到 API 特定示例。 常见示例在所有 API 间是相同的。 [Azure PowerShell 参考](/powershell/module/az.cosmosdb)中收录了所有 Azure Cosmos DB PowerShell cmdlet 的参考页。 `Az.CosmosDB` 模块现在是 `Az` 模块的一部分。 [下载并安装](/powershell/azure/install-az-ps) Az 模块的最新版本，获取 Azure Cosmos DB cmdlet。 还可从 [PowerShell 库](https://www.powershellgallery.com/packages/Az/5.4.0)中获取最新版本。 还可以从我们的 GitHub 存储库 [GitHub 上的 Cosmos DB PowerShell 示例](https://github.com/Azure/azure-docs-powershell-samples/tree/master/cosmosdb)创建这些适用于 Cosmos DB 的 PowerShell 示例的分支。

## <a name="common-samples"></a>常见示例

|任务 | 说明 |
|---|---|
|[更新帐户](../scripts/powershell/common/account-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 更新 Cosmos DB 帐户的默认一致性级别。 |
|[更新帐户的区域](../scripts/powershell/common/update-region.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 更新 Cosmos DB 帐户的区域。 |
|[更改故障转移优先级或触发故障转移](../scripts/powershell/common/failover-priority-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 更改 Azure Cosmos 帐户的区域故障转移优先级或触发手动故障转移。 |
|[帐户密钥或连接字符串](../scripts/powershell/common/keys-connection-strings.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 获取 Azure Cosmos DB 帐户的主密钥和辅助密钥、连接字符串或重新生成其帐户密钥。 |
|[创建启用 IP 防火墙的 Cosmos 帐户](../scripts/powershell/common/firewall-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 创建启用 IP 防火墙的 Azure Cosmos DB 帐户。 |
|||

## <a name="mongo-db-api-samples"></a>Mongo DB API 示例

|任务 | 说明 |
|---|---|
|[创建帐户、数据库和集合](../scripts/powershell/mongodb/create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 创建 Azure Cosmos 帐户、数据库和集合。 |
|[创建帐户、数据库和集合（具有自动缩放功能）](../scripts/powershell/mongodb/autoscale.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 创建 Azure Cosmos 帐户、数据库和集合（具有自动缩放功能）。 |
|[列出或获取数据库或集合](../scripts/powershell/mongodb/list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 列出或获取数据库或集合。 |
|[吞吐量操作](../scripts/powershell/mongodb/throughput.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 数据库或集合的吞吐量操作，包括获取、更新自动缩放和标准吞吐量和在其之间进行迁移。 |
|[锁定资源以防止将其删除](../scripts/powershell/mongodb/lock.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 通过资源锁防止资源遭到删除。 |
|||
