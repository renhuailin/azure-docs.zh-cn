---
title: 防止 Azure Cosmos DB API for Cassandra 发生速率限制错误。
description: 使用 SSR（服务器端重试）功能防止 Azure Cosmos DB API for Cassandra 操作发生速率限制错误
author: dileepraotv-github
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 10/11/2021
ms.author: turao
ms.openlocfilehash: 469dc3f87bb7c783f2c3138e2bcf592c85312006
ms.sourcegitcommit: af303268d0396c0887a21ec34c9f49106bb0c9c2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2021
ms.locfileid: "129754731"
---
# <a name="prevent-rate-limiting-errors-for-azure-cosmos-db-api-for-cassandra-operations"></a>防止 Azure Cosmos DB API for Cassandra 操作发生速率限制错误
[!INCLUDE[appliesto-cassandra-api](../includes/appliesto-cassandra-api.md)]

所有数据库操作的成本将由 Azure Cosmos DB 规范化，并以“请求单位”(RU) 表示。 可将请求单位视为性能货币，它抽象化了执行 Azure Cosmos DB 支持的数据库操作所需的系统资源，例如 CPU、IOPS 和内存。

如果 Azure Cosmos DB Cassandra API 操作超过表的吞吐量限制 (RU)，则这些操作可能会失败并发生速率限制 (OverloadedException/429) 错误。 可以按照 [Cassandra API 建议文档](https://devblogs.microsoft.com/cosmosdb/cassandra-api-java/#retry-policy)的“重试策略”中所述，在客户端处理这种情况。 如果无法实现客户端重试策略来处理因速率限制错误而导致的失败，则我们可以利用服务器端重试 (SSR) 功能，在这种情况下，超过表吞吐量限制的操作将在短暂的延迟后自动重试。 这是一个帐户级设置，将应用于帐户中的所有密钥空间和表。

## <a name="use-the-azure-portal"></a>使用 Azure 门户

1. 登录到 [Azure 门户](https://portal.azure.com/)。

2. 导航到你的 Azure Cosmos DB API for Cassandra 帐户。

3. 转到“设置”部分下面的“功能”窗格 。

4. 选择“服务器端重试”。

5. 单击“启用”为帐户中的所有集合启用此功能。

:::image type="content" source="./media/prevent-rate-limiting-errors/prevent-rate-limiting-errors.png" alt-text="适用于 Azure Cosmos DB API for Cassandra 的服务器端重试功能的屏幕截图":::

## <a name="use-the-azure-cli"></a>使用 Azure CLI

1. 检查是否已为你的帐户启用了 SSR：

   ```azurecli-interactive
   az cosmosdb show --name accountname --resource-group resourcegroupname
   ```

2. 为数据库帐户中的所有表启用 SSR。 此更改生效可能需要长达 15 分钟的时间。

   ```azurecli-interactive
   az cosmosdb update --name accountname --resource-group resourcegroupname --capabilities EnableCassandra DisableRateLimitingResponses
   ```

3. 以下命令通过从功能列表中删除 `DisableRateLimitingResponses`，对数据库帐户中的所有表“禁用”服务器端重试。 此更改生效可能需要长达 15 分钟的时间。

   ```azurecli-interactive
   az cosmosdb update --name accountname --resource-group resourcegroupname --capabilities EnableCassandra
   ```

## <a name="frequently-asked-questions"></a>常见问题

### <a name="how-are-requests-retried"></a>如何重试请求？

连续（一次又一次）重试请求，直到出现 60 秒超时为止。 如果达到了超时，客户端会相应地收到读取或写入超时错误

### <a name="when-is-ssr-most-beneficial"></a>SSR 在哪种情况下最有用？

出现持续时间不超过 1 分钟的突发性高峰时，服务器端重试 (SSR) 最有用，它可以避免发生限制错误。 如果工作负载增大并稳定地保持在指定的 RU 之上，则 SSR 没有太大的作用。 建议相应地提高 RU。

### <a name="suggested-client-side-settings"></a>建议的客户端设置是什么？

启用 SSR 后，客户端应用应该增大读取超时，使其超过服务器的 60 秒重试设置。 为安全起见，建议设置 90 秒超时。

SocketOptions setReadTimeoutMillis DefaultDriverOption.REQUEST_TIMEOUT


### <a name="how-can-i-monitor-the-effects-of-a-server-side-retry"></a>如何监视服务器端重试的效果？

可以在“Cosmos DB 指标”窗格中查看服务器端重试的速率限制错误 (429)。 启用 SSR 后，这些错误不会传递给客户端，因为它们是在服务器端处理和重试的。

可以在 [Cosmos DB 资源日志](../cosmosdb-monitor-resource-logs.md) 中搜索包含“estimatedDelayFromRateLimitingInMilliseconds”的日志条目。

### <a name="will-server-side-retry-affect-my-consistency-level"></a>服务器端重试是否会影响我的一致性级别？

服务器端重试不会影响一致性级别。 如果请求受到速率限制（错误 429），则会在服务器端重试。

### <a name="does-server-side-retry-affect-any-type-of-error-that-my-client-might-receive"></a>服务器端重试是否影响客户端可能收到的任何类型的错误？

否。由于是在服务器端重试，服务器端重试只会影响速率限制错误 (429)。 有了此功能，就不必在客户端应用程序中处理速率限制错误。 所有[其他错误](troubleshoot-common-issues.md)都会传递到客户端。

## <a name="next-steps"></a>后续步骤

若要了解有关排除常见错误的更多信息，请参阅此文：

* [排查 Azure Cosmos DB's API for Cassandra 中的常见问题](troubleshoot-common-issues.md)


请参阅以下文章，了解如何在 Azure Cosmos DB 中预配吞吐量：

* [Azure Cosmos DB 中的请求单位和吞吐量](../request-units.md)
* [在容器和数据库上预配吞吐量](../how-to-provision-throughput-cassandra.md) 
* [分区键最佳做法](../cassandra-partitioning.md)

