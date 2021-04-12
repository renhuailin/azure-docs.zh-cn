---
title: 防止 Azure Cosmos DB API for MongoDB 操作的速率限制错误。
description: 了解如何使用 SSR（服务器端重试）功能防止 Azure Cosmos DB API for MongoDB 操作遇到速率限制错误。
author: gahl-levy
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 01/13/2021
ms.author: gahllevy
ms.openlocfilehash: 1e9062b111c30efa90b98c4ebcee710b1d975a1d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "99507924"
---
# <a name="prevent-rate-limiting-errors-for-azure-cosmos-db-api-for-mongodb-operations"></a>防止 Azure Cosmos DB API for MongoDB 操作的速率限制错误
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

如果 Azure Cosmos DB API for MongoDB 操作超出集合的吞吐量限制 (RU)，则可能失败，出现速率限制 (16500/429) 错误。 

可以启用服务器端重试 (SSR) 功能，并让服务器自动重试这些操作。 短暂延迟后，你的帐户中的所有集合的请求会重试。 此功能是处理客户端应用程序中的速率限制错误的便捷替代方法。

## <a name="use-the-azure-portal"></a>使用 Azure 门户

1. 登录到 [Azure 门户](https://portal.azure.com/)。

1. 导航到你的 Azure Cosmos DB API for MongoDB 帐户。

1. 转到“设置”部分下面的“功能”窗格 。

1. 选择“服务器端重试”。

1. 单击“启用”为帐户中的所有集合启用此功能。

:::image type="content" source="./media/prevent-rate-limiting-errors/portal-features-server-side-retry.png" alt-text="适用于 Azure Cosmos DB API for MongoDB 的服务器端重试功能的屏幕截图":::

## <a name="use-the-azure-cli"></a>使用 Azure CLI

1. 检查是否已为帐户启用 SSR：
```bash
az cosmosdb show --name accountname --resource-group resourcegroupname
```
2. 为数据库帐户中的所有集合启用 SSR。 为使此更改生效，可能需要 15 分钟。
```bash
az cosmosdb update --name accountname --resource-group resourcegroupname --capabilities EnableMongo DisableRateLimitingResponses
```
以下命令将通过从功能列表中删除“DisableRateLimitingResponses”，为数据库帐户中的所有集合禁用 SSR。 为使此更改生效，可能需要 15 分钟。
```bash
az cosmosdb update --name accountname --resource-group resourcegroupname --capabilities EnableMongo
```

## <a name="frequently-asked-questions"></a>常见问题
* 如何重试请求？
    * 可以不断（反复）重试请求，直到达到 60 秒的超时时间。 如果达到超时值，客户端将收到 [ExceededTimeLimit 异常 (50)](mongodb-troubleshoot.md)。
*  如何监视 SSR 的影响？
    *  可以在“Cosmos DB 指标”窗格中查看服务器端重试的速率限制错误（429 秒）。 请记住，当启用 SSR 时，这些错误不会出现在客户端上，因为它们会在服务器端处理和重试。 
    *  可以在 [Cosmos DB 资源日志](cosmosdb-monitor-resource-logs.md)中搜索包含“estimatedDelayFromRateLimitingInMilliseconds”的日志条目。
*  SSR 会影响我的一致性级别吗？
    *  SSR 不会影响请求的一致性。 如果请求的速率受限（显示 429 错误），则会在服务器端重试请求。 
*  SSR 是否会影响客户端可能收到的任何类型的错误？
    *  否，SSR 仅通过在服务器端重试来影响速率限制错误（429 秒）。 此功能会阻止用户在客户端应用程序中处理速率限制错误。 所有[其他错误](mongodb-troubleshoot.md)都将发送到客户端。 

## <a name="next-steps"></a>后续步骤

若要了解有关排除常见错误的更多信息，请参阅此文：

* [解决 Azure Cosmos DB 的 API for MongoDB 中的常见问题](mongodb-troubleshoot.md)
