---
title: 排查 Azure Cosmos DB 的 API for Mongo DB 中的常见错误
description: 本文档讨论解决 Azure Cosmos DB 的 API for MongoDB 中遇到的常见问题的方法。
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: troubleshooting
ms.date: 07/15/2020
author: gahl-levy
ms.author: gahllevy
ms.openlocfilehash: 759b08267515a796a60f6752a6fa43b732011b10
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121777885"
---
# <a name="troubleshoot-common-issues-in-azure-cosmos-dbs-api-for-mongodb"></a>解决 Azure Cosmos DB 的 API for MongoDB 中的常见问题
[!INCLUDE[appliesto-mongodb-api](../includes/appliesto-mongodb-api.md)]

下面的文章介绍了使用 Azure Cosmos DB API for MongoDB 的部署的常见错误和解决方案。

>[!Note]
> Azure Cosmos DB 不托管 MongoDB 引擎。 它提供了 MongoDB [线路协议版本 4.0](feature-support-40.md)、[3.6](feature-support-36.md) 的实现，以及对[线路协议版本 3.2](feature-support-32.md) 的旧版支持。 因此，其中的一些错误仅出现在 Azure Cosmos DB API for MongoDB 中。

## <a name="common-errors-and-solutions"></a>常见错误和解决方法

| 代码       | 错误                | 说明  | 解决方案  |
|------------|----------------------|--------------|-----------|
| 2 | BadValue | 一个常见原因是，已排除与指定订单项对应的索引路径，或者 order by 查询没有可从中提供的相应的组合索引。 查询请求对未建立索引的字段进行排序。 | 为所尝试的排序查询创建匹配索引（或复合索引）。 |
| 2 | 事务不处于活动状态 | 多文档事务超过了 5 秒的固定时间限制。 | 重试多文档事务，或限制其中操作的范围，使其在 5 秒的时间限制内完成。 |
| 13 | 未授权 | 请求缺少权限，无法完成。 | 请确保使用正确的密钥。  |
| 26 | NamespaceNotFound | 找不到查询中所引用的数据库或集合。 | 请确保你的数据库/集合名称与查询中的名称完全匹配。|
| 50 | ExceededTimeLimit | 请求已超过 60 秒执行时间的超时。 |  此问题的原因可能有很多。 其中一个原因是当前分配的请求单位容量不足，无法完成请求。 可以通过增加该集合或数据库的请求单位来解决此问题。 其他情况下，可以通过将大型请求拆分为较小请求来规避此问题。 重试已收到此错误的写入操作可能会导致重复写入。 <br><br>如果尝试删除大量数据而不影响 RU： <br>- 请考虑使用 TTL（基于时间戳）：[使用 Azure Cosmos DB 的用于 MongoDB 的 API 过期数据](mongodb-time-to-live.md) <br>- 使用游标/批大小执行删除操作。 可以一次提取一个文档，然后通过循环将其删除。 此操作将有助于进行缓慢删除数据，而不影响生产应用程序。|
| 61 | ShardKeyNotFound | 请求中的文档未包含集合的分片键（Azure Cosmos DB 分区键）。 | 请确保在请求中使用集合的分片键。|
| 66 | ImmutableField | 请求试图更改不可变字段 | “id”字段是不可变的。 确保请求不会尝试更新该字段或分片键字段。 |
| 67 | CannotCreateIndex | 无法完成创建索引的请求。 | 在每个容器中最多可以创建 500 个单字段索引。 复合索引中最多可以包含八个字段（版本 3.6+ 支持复合索引）。 |
| 112 | WriteConflict | 多文档事务由于一个冲突的多文档事务失败 | 重试该多文档事务，直到它成功。 |
| 115 | CommandNotSupported | 不支持所尝试的请求。 | 错误中应当提供了更多详细信息。 如果此功能对你的部署很重要，请在 [Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)中创建支持票证，Azure Cosmos DB 团队将回复你。 |
| 11000 | DuplicateKey | 集合中已存在要插入的文档的分片键（Azure Cosmos DB 分区键），或者违反了唯一索引字段约束。 | 请使用 update() 函数更新现有文档。 如果违反了唯一索引字段约束，则对文档执行插入或更新操作时请使用分片/分区中尚不存在的字段值。 另一种选项是使用包含 id 和分片键字段的组合的字段。 |
| 16500 | TooManyRequests  | 使用的请求单位总数超过了集合的预配请求单位率，已达到限制。 | 请考虑从 Azure 门户对分配给一个容器或一组容器的吞吐量进行缩放，也可以重试该操作。 如果启用 [SSR（服务器端重试）](prevent-rate-limiting-errors.md)，则 Azure Cosmos DB 会自动重试由于此错误而失败的请求。 |
| 16501 | ExceededMemoryLimit | 作为一种多租户服务，操作已超出客户端的内存配额。 这仅适用于 MongoDB 版本 3.2 的 Azure Cosmos DB API。 | 通过限制性更强的查询条件缩小操作的作用域，或者通过 [Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)联系技术支持。 示例： `db.getCollection('users').aggregate([{$match: {name: "Andy"}}, {$sort: {age: -1}}]))` |
| 40324 | 无法识别的管道阶段名称。 | 聚合管道请求中的阶段名称无法识别。 | 请确保你的请求中的所有聚合管道名称都有效。 |
| - | MongoDB 线路版本问题 | 旧版本的 MongoDB 驱动程序无法在连接字符串中检测 Azure Cosmos 帐户的名称。 | 在连接字符串的末尾追加 `appName=@accountName@`，其中 `accountName` 是 Azure Cosmos DB 帐户名称。 |
| - | MongoDB 客户端网络问题（例如套接字或 endOfStream 异常）| 该网络请求已失败。 这通常是由 MongoDB 客户端尝试使用的非活动 TCP 连接导致的。 MongoDB 驱动程序通常会利用连接池，这会导致从用于某个请求的池中选择随机连接。 非活动连接通常会在四分钟后在 Azure Cosmos DB 端超时。 | 你可以在应用程序代码中重试这些失败的请求，更改 MongoDB 客户端（驱动程序）设置以在四分钟超时时段之前拆解非活动 TCP 连接，也可以配置 OS `keepalive` 设置以保持 TCP 连接处于活动状态。<br><br>为了避免出现连接消息，建议更改连接字符串，将 `maxConnectionIdleTime` 设置为 1-2 分钟。<br>- Mongo 驱动程序：配置 `maxIdleTimeMS=120000` <br>- Node.JS：配置 `socketTimeoutMS=120000`、`autoReconnect` = true、`keepAlive` = true、`keepAliveInitialDelay` = 3 分钟
| - | Mongo Shell 在 Azure 门户中无法正常工作 | 当用户尝试打开 Mongo shell 时，没有发生任何操作，选项卡始终为空白。  | 检查防火墙。 在 Azure 门户中，Mongo shell 不支持防火墙。 <br>- 在防火墙规则内的本地计算机上安装 Mongo shell <br>- 使用旧版 Mongo shell
| - | 无法与连接字符串连接  | 从 3.2 -> 3.6 升级时，连接字符串已更改 | 请注意，使用 Azure Cosmos DB's API for MongoDB 帐户时，3.6 版的帐户的终结点格式为 `*.mongo.cosmos.azure.com`，而 3.2 版的帐户的终结点格式为 `*.documents.azure.com`。  

## <a name="next-steps"></a>后续步骤

- 了解如何将 [Studio 3T](connect-using-mongochef.md) 与 Azure Cosmos DB 的用于 MongoDB 的 API 配合使用。
- 了解如何将 [Robo 3T](connect-using-robomongo.md) 与 Azure Cosmos DB 的用于 MongoDB 的 API 配合使用。
- 通过 Azure Cosmos DB 的用于 MongoDB 的 API 来浏览 MongoDB [示例](nodejs-console-app.md)。
