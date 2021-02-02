---
title: 对于 MongoDB 操作，防止 Azure Cosmos DB API 的速率限制。
description: 了解如何通过 SSR (服务器端重试) 功能防止 MongoDB 操作的 Azure Cosmos DB API 处理命中速率限制错误。
author: gahl-levy
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 01/13/2021
ms.author: gahllevy
ms.openlocfilehash: e1ccf55d38a9a3a5a1d0a3622c90dd7b51e5e477
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/02/2021
ms.locfileid: "99258484"
---
# <a name="prevent-rate-limiting-errors-for-azure-cosmos-db-api-for-mongodb-operations"></a>阻止用于 MongoDB 操作的 Azure Cosmos DB API 的速率限制错误
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

如果 Azure Cosmos DB API for MongoDB 操作可能会失败，并出现速率限制 (16500/429) 错误，前提是它们超过了集合的吞吐量限制)  (： 

你可以启用服务器端重试 (SSR) 功能，并让服务器自动重试这些操作。 在帐户中的所有集合的短暂延迟后，会重试请求。 此功能是在客户端应用程序中处理速率限制错误的一种简便方法。

## <a name="use-the-azure-portal"></a>使用 Azure 门户

1. 登录到 [Azure 门户](https://portal.azure.com/)。

1. 导航到 MongoDB 帐户的 Azure Cosmos DB API。

1. 中转到 "**设置**" 部分下的 "**功能**" 窗格。

1. 选择 " **服务器重试**"。

1. 单击 " **启用** " 可为帐户中的所有集合启用此功能。

:::image type="content" source="./media/prevent-rate-limiting-errors/portal-features-server-side-retry.png" alt-text="适用于 MongoDB Azure Cosmos DB API 的服务器端重试功能的屏幕截图":::

## <a name="use-the-azure-cli"></a>使用 Azure CLI

1. 检查是否已为你的帐户启用了 SSR：
```bash
az cosmosdb show --name accountname --resource-group resourcegroupname
```
2. **启用** SSR 用于数据库帐户中的所有集合。 要使此更改生效，可能需要15分钟。
```bash
az cosmosdb update --name accountname --resource-group resourcegroupname --capabilities EnableMongo DisableRateLimitingResponses
```
以下命令将对数据库帐户中的所有集合 **禁用** SSR。 要使此更改生效，可能需要15分钟。
```bash
az cosmosdb update --name accountname --resource-group resourcegroupname --capabilities EnableMongo DisableRateLimitingResponses
```

## <a name="frequently-asked-questions"></a>常见问题
* 如何重试请求？
    * 请求会连续重试 () ，直到达到60秒的超时。 如果达到超时值，则客户端将收到 [ExceededTimeLimit 异常 (50) ](mongodb-troubleshoot.md)。
*  如何监视 SSR 的影响？
    *  可以在 "Cosmos DB 指标" 窗格中查看重试服务器端 (429s) 的速率限制错误。 请记住，当启用 SSR 时，这些错误不会发送到客户端，因为它们经过处理并重试服务器端。 
    *  可以在 [Cosmos DB 资源日志](cosmosdb-monitor-resource-logs.md)中搜索包含 "estimatedDelayFromRateLimitingInMilliseconds" 的日志条目。
*  SSR 会影响我的一致性级别吗？
    *  SSR 不会影响请求的一致性。 如果请求的速率受限 (为 429) ，则会在服务器端重试请求。 
*  SSR 是否会影响客户端可能收到的任何类型的错误？
    *  不，SSR 只会通过重试服务器端来影响 (429s) 的速率限制。 此功能可防止用户在客户端应用程序中处理速率限制错误。 所有 [其他错误](mongodb-troubleshoot.md) 都将发送到客户端。 

## <a name="next-steps"></a>后续步骤

若要详细了解常见错误的疑难解答，请参阅以下文章：

* [解决 Azure Cosmos DB 的 API for MongoDB 中的常见问题](mongodb-troubleshoot.md)
