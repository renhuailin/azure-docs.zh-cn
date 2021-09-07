---
title: 适用于 Azure Cosmos DB API for MongoDB 的 Azure CLI 示例
description: 适用于 Azure Cosmos DB API for MongoDB 的 Azure CLI 示例
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: sample
ms.date: 08/26/2021
ms.author: mjbrown
ms.custom: devx-track-azurecli
ms.openlocfilehash: 04ac3178b439e755bbcbd33855ff0bab9a566784
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123029193"
---
# <a name="azure-cli-samples-for-azure-cosmos-db-api-for-mongodb"></a>适用于 Azure Cosmos DB API for MongoDB 的 Azure CLI 示例
[!INCLUDE[appliesto-mongodb-api](../includes/appliesto-mongodb-api.md)]

下表包括用于 Azure Cosmos DB 的示例 Azure CLI 脚本的链接。 使用右侧的链接可导航到 API 特定示例。 常见示例在所有 API 间是相同的。 [Azure CLI 参考](/cli/azure/cosmosdb)中收录了所有 Azure Cosmos DB CLI 命令的参考页。 还可以在 [Azure Cosmos DB CLI GitHub 存储库](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb)中找到 Azure Cosmos DB CLI 脚本示例。

这些示例要求使用 Azure CLI 2.12.1 版或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)

## <a name="common-samples"></a>常见示例

这些示例适用于所有 Azure Cosmos DB API

|任务 | 说明 |
|---|---|
| [添加或故障转移区域](../scripts/cli/common/regions.md?toc=%2fcli%2fazure%2ftoc.json) | 添加区域、更改故障转移优先级、触发手动故障转移。|
| [帐户密钥和连接字符串](../scripts/cli/common/keys.md?toc=%2fcli%2fazure%2ftoc.json) | 列出帐户密钥、只读密钥，重新生成密钥并列出连接字符串。|
| [使用 IP 防火墙保护](../scripts/cli/common/ipfirewall.md?toc=%2fcli%2fazure%2ftoc.json)| 创建配置了 IP 防火墙的 Cosmos 帐户。|
| [使用服务终结点保护新帐户](../scripts/cli/common/service-endpoints.md?toc=%2fcli%2fazure%2ftoc.json)| 使用服务终结点创建 Cosmos 帐户并确保其安全。|
| [使用服务终结点保护现有帐户](../scripts/cli/common/service-endpoints-ignore-missing-vnet.md?toc=%2fcli%2fazure%2ftoc.json)| 最终配置子网后，更新 Cosmos 帐户以使用服务终结点进行保护。|
|||

## <a name="mongodb-api-samples"></a>MongoDB API 示例

|任务 | 说明 |
|---|---|
| [创建 Azure Cosmos 帐户、数据库和集合](../scripts/cli/mongodb/create.md?toc=%2fcli%2fazure%2ftoc.json)| 为 MongoDB API 创建 Azure Cosmos DB 帐户、数据库和集合。 |
| [创建 Azure Cosmos 帐户、数据库（具有自动缩放功能）以及两个集合（具有共享吞吐量）](../scripts/cli/mongodb/autoscale.md?toc=%2fcli%2fazure%2ftoc.json)| 为 MongoDB API 创建 Azure Cosmos DB 帐户、数据库（具有自动缩放功能）以及两个集合（具有共享吞吐量）。 |
| [吞吐量操作](../scripts/cli/mongodb/throughput.md?toc=%2fcli%2fazure%2ftoc.json) | 在数据库和集合上的自动缩放和标准吞吐量之间读取、更新和迁移。|
| [锁定资源以防止将其删除](../scripts/cli/mongodb/lock.md?toc=%2fcli%2fazure%2ftoc.json)| 使用资源锁防止删除资源。|
|||

## <a name="next-steps"></a>后续步骤

尝试为迁移到 Azure Cosmos DB 进行容量规划？ 可以使用有关现有数据库群集的信息进行容量规划。
* 若只知道现有数据库群集中的 vcore 和服务器数量，请阅读[使用 vCore 或 vCPU 估算请求单位](../convert-vcore-to-request-unit.md) 
* 若知道当前数据库工作负载的典型请求速率，请阅读[使用 Azure Cosmos DB 容量计划工具估算请求单位](estimate-ru-capacity-planner.md)
