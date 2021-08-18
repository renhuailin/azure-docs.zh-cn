---
title: 防止 Azure Cosmos DB API for MongoDB 操作的速率限制错误。
description: 了解如何使用 SSR（服务器端重试）功能防止 Azure Cosmos DB API for MongoDB 操作遇到速率限制错误。
author: gahl-levy
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 01/13/2021
ms.author: gahllevy
ms.openlocfilehash: 5aa1fd009896a6483794e663a7b6251bd10cef41
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121777877"
---
# <a name="prevent-rate-limiting-errors-for-azure-cosmos-db-api-for-mongodb-operations"></a>防止 Azure Cosmos DB API for MongoDB 操作的速率限制错误
[!INCLUDE[appliesto-mongodb-api](../includes/appliesto-mongodb-api.md)]

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

1. 检查是否已为你的帐户启用了 SSR：

   ```azurecli-interactive
   az cosmosdb show --name accountname --resource-group resourcegroupname
   ```

1. 为数据库帐户中的所有集合启用 SSR。 此更改生效可能需要长达 15 分钟的时间。

   ```azurecli-interactive
   az cosmosdb update --name accountname --resource-group resourcegroupname --capabilities EnableMongo DisableRateLimitingResponses
   ```

1. 以下命令会通过从功能列表中删除 `DisableRateLimitingResponses`，对数据库帐户中的所有集合“禁用”服务器端重试。 此更改生效可能需要长达 15 分钟的时间。

   ```azurecli-interactive
   az cosmosdb update --name accountname --resource-group resourcegroupname --capabilities EnableMongo
   ```

## <a name="frequently-asked-questions"></a>常见问题

### <a name="how-are-requests-retried"></a>如何重试请求？

连续（一次又一次）重试请求，直到出现 60 秒超时为止。 如果超时，则客户端会收到 [ExceededTimeLimit 异常 (50)](error-codes-solutions.md)。

### <a name="how-can-i-monitor-the-effects-of-a-server-side-retry"></a>如何监视服务器端重试的效果？

可以在“Cosmos DB 指标”窗格中查看服务器端重试的速率限制错误 (429)。 请记住，启用 SSR 后，这些错误不会传递给客户端，因为它们是在服务器端处理和重试的。

可以在 [Cosmos DB 资源日志](../cosmosdb-monitor-resource-logs.md) 中搜索包含“estimatedDelayFromRateLimitingInMilliseconds”的日志条目。

### <a name="will-server-side-retry-affect-my-consistency-level"></a>服务器端重试是否会影响我的一致性级别？

服务器端重试不会影响请求的一致性。 如果请求受到速率限制（出现 429 错误），则会在服务器端重试。

### <a name="does-server-side-retry-affect-any-type-of-error-that-my-client-might-receive"></a>服务器端重试是否影响客户端可能收到的任何类型的错误？

否。由于是在服务器端重试，服务器端重试只会影响速率限制错误 (429)。 有了此功能，就不必在客户端应用程序中处理速率限制错误。 所有[其他错误](error-codes-solutions.md)都会传递到客户端。

## <a name="next-steps"></a>后续步骤

若要了解有关排除常见错误的更多信息，请参阅此文：

* [解决 Azure Cosmos DB 的 API for MongoDB 中的常见问题](error-codes-solutions.md)
