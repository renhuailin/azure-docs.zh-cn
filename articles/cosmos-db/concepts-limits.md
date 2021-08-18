---
title: Azure Cosmos DB 服务配额
description: Azure Cosmos DB 服务配额和不同资源类型的默认限制。
author: abhijitpai
ms.author: abpai
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: a33b7fb26b06b9a89b7e4f16699441fd9a5bbd0b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121738373"
---
# <a name="azure-cosmos-db-service-quotas"></a>Azure Cosmos DB 服务配额

[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

本文概述了 Azure Cosmos DB 中提供给不同资源的默认配额。

## <a name="storage-and-database-operations"></a>存储和数据库操作

在订阅下创建 Azure Cosmos 帐户后，可以通过[创建数据库、容器和项](account-databases-containers-items.md)来管理帐户中的数据。

### <a name="provisioned-throughput"></a>预配的吞吐量

可以在容器级别或数据库级别按[请求单位 (RU)](request-units.md) 预配吞吐量。 下表列出了每个容器/数据库的存储和吞吐量限制。

| 资源 | 默认限制 |
| --- | --- |
| 每个容器的最大 RU 数（[专用吞吐量预配模式](account-databases-containers-items.md#azure-cosmos-containers)） | 默认为 1,000,000。 可以通过[开具 Azure 支持票证](create-support-request-quota-increase.md)来提高此限制 |
| 每个数据库的最大 RU 数（[共享吞吐量预配模式](account-databases-containers-items.md#azure-cosmos-containers)） | 默认为 1,000,000。 可以通过[开具 Azure 支持票证](create-support-request-quota-increase.md)来提高此限制 |
| 每个分区（逻辑和物理分区）的最大 RU 数 | 10,000 |
| 每个（逻辑）分区的所有项的最大存储 | 20 GB |
| 不同（逻辑）分区键的最大数目 | 无限制 |
| 每个容器的最大存储 | 无限制 |
| 每个数据库的最大存储 | 无限制 |
| 每个帐户的最大附件大小（附件功能即将弃用） | 2 GB |
| 每 1 GB 需要的最小 RU 数 | 10 RU/秒<br>注意：如果你的帐户符合我们的[“高存储/低吞吐量”计划](set-throughput.md#high-storage-low-throughput-program)的条件，这个最小值可以降低 |

> [!NOTE]
> 若要了解有关管理其分区键需要更高存储或吞吐量限制的工作负荷的最佳做法，请参阅[创建合成分区键](synthetic-partition-keys.md)。

### <a name="minimum-throughput-limits"></a>最小吞吐量限制

Cosmos 容器（或共享吞吐量数据库）的最小吞吐量必须为 400 RU/秒。 随着容器的增长，Cosmos DB 需要一个最小吞吐量，以确保数据库或容器有足够的资源用于其运行。

可以从 Azure 门户或 SDK 检索容器或数据库的当前和最小吞吐量。 有关详细信息，请参阅[对容器和数据库预配吞吐量](set-throughput.md)。 

实际的最小 RU/s 可能因帐户配置而异。 可以使用 [Azure Monitor 指标](monitor-cosmos-db.md#view-operation-level-metrics-for-azure-cosmos-db)来查看资源上预配吞吐量 (RU/s) 和存储的历史记录。 

#### <a name="minimum-throughput-on-container"></a>容器最小吞吐量 

若要估算具有手动吞吐量的容器所需的最小吞吐量，请找到以下项的最大值：

* 400 RU/s 
* 当前存储空间 (GB) * 10 RU/s
* 容器中预配的最高 RU/s / 100

示例：假设你有一个预配了 400 RU/s 和 0 GB 存储的容器。 你将吞吐量提高到 50,000 RU/s，并导入 20 GB 数据。 现在，最小 RU/s 为 `MAX(400, 20 * 10 RU/s per GB, 50,000 RU/s / 100)` = 500 RU/s。 随着时间的推移，存储会增长到 200 GB。 现在，最小 RU/s 为 `MAX(400, 200 * 10 RU/s per GB, 50,000 / 100)` = 2000 RU/s。 

注意：如果你的帐户符合我们的[“高存储/低吞吐量”计划](set-throughput.md#high-storage-low-throughput-program)的条件，这个每 GB 存储每秒 10 RU 的最小吞吐量可以降低。

#### <a name="minimum-throughput-on-shared-throughput-database"></a>共享吞吐量数据库的最小吞吐量 
若要估算具有手动吞吐量的共享吞吐量数据库所需的最小吞吐量，请找到以下项的最大值：

* 400 RU/s 
* 当前存储空间 (GB) * 10 RU/s
* 数据库上预配的最高 RU/s / 100
* 400 + MAX(容器计数 - 25, 0) * 100 RU/s

示例：假设你有一个预配了 400 RU/s、15 GB 存储和 10 个容器的数据库。 最小 RU/s 为 `MAX(400, 15 * 10 RU/s per GB, 400 / 100, 400 + 0 )` = 400 RU/s。 如果数据库中有 30 个容器，则最小 RU/s 将为 `400 + MAX(30 - 25, 0) * 100 RU/s` = 900 RU/s。 

注意：如果你的帐户符合我们的[“高存储/低吞吐量”计划](set-throughput.md#high-storage-low-throughput-program)的条件，这个每 GB 存储每秒 10 RU 的最小吞吐量可以降低。

总之，最小预配 RU 限制如下所示。 

| 资源 | 默认限制 |
| --- | --- |
| 每个容器的最小 RU 数（[专用吞吐量预配模式](./account-databases-containers-items.md#azure-cosmos-containers)） | 400 |
| 每个数据库的最小 RU 数（[共享吞吐量预配模式](./account-databases-containers-items.md#azure-cosmos-containers)） | 前 25 个容器为 400 RU/s。 此后的每个容器增加 100 RU/s。 |

Cosmos DB 支持通过 SDK 或门户以编程方式缩放每个容器或数据库的吞吐量 (RU/s)。    

根据当前的已预配 RU/s 和资源设置，每个资源可以立即以同步方式在最小 RU/s 到最小 RU/s 的 100 倍之间进行缩放。 如果请求的吞吐量值超出范围，将以异步方式执行缩放。 完成异步缩放所需的时间为数分钟到数小时不等，具体取决于请求的吞吐量和容器中的数据存储大小。  

### <a name="serverless"></a>无服务器

[无服务器](serverless.md)可让你以基于消耗的方式使用 Azure Cosmos DB 资源。 下表列出了每个容器/数据库的存储和吞吐量可突发性限制。

| 资源 | 限制 |
| --- | --- |
| 每个容器的最大 RU/秒 | 5,000 |
| 每个（逻辑）分区的所有项的最大存储 | 20 GB |
| 不同（逻辑）分区键的最大数目 | 无限制 |
| 每个容器的最大存储 | 50 GB |

## <a name="control-plane-operations"></a>控制平面操作

可以使用 Azure 门户、Azure PowerShell、Azure CLI 和 Azure 资源管理器模板[预配和管理 Azure Cosmos 帐户](how-to-manage-database-account.md)。 下表列出了每个订阅、帐户和操作数目的限制。

| 资源 | 默认限制 |
| --- | --- |
| 每个订阅的最大数据库帐户数 | 默认值为 50。 可以通过[开具 Azure 支持票证](create-support-request-quota-increase.md)来提高此限制|
| 最大区域故障转移数 | 默认值为 1/小时。 可以通过[开具 Azure 支持票证](create-support-request-quota-increase.md)来提高此限制|

> [!NOTE]
> 区域故障转移仅适用于单个区域写入帐户。 多区域写入帐户不需要，也不会对更改写入区域施加任何限制。

Cosmos DB 会定期自动备份数据。 有关备份保留时间间隔和窗口的详细信息，请参阅 [Azure Cosmos DB 中的联机备份和按需数据还原](online-backup-and-restore.md)。

## <a name="per-account-limits"></a>每个帐户的限制

### <a name="provisioned-throughput"></a>预配的吞吐量

| 资源 | 默认限制 |
| --- | --- |
| 数据库的最小数目 | 无限制 |
| 具有共享吞吐量的每个数据库的最大容器数 |25 |
| 具有专用吞吐量的每个数据库或帐户的最大容器数  |不受限制 |
| 最大区域数 | 无限制（所有 Azure 区域） |

### <a name="serverless"></a>无服务器

| 资源 | 限制 |
| --- | --- |
| 数据库的最小数目 | 无限制 |
| 每个帐户的最大容器数  | 100 |
| 最大区域数 | 1（任何 Azure 区域） |

## <a name="per-container-limits"></a>每个容器的限制

根据所用的 API，Azure Cosmos 容器可以代表集合、表或图形。 容器支持[唯一键约束](unique-keys.md)、[存储过程、触发器和 UDF](stored-procedures-triggers-udfs.md) 和[索引策略](how-to-manage-indexing-policy.md)的配置。 下表列出了特定于容器中的配置的限制。 

| 资源 | 默认限制 |
| --- | --- |
| 数据库或容器名称的最大长度 | 255 |
| 每个容器的最大存储过程数 | 100 <sup>*</sup>|
| 每个容器的最大 UDF 数 | 50 <sup>*</sup>|
| 索引策略中的最大路径数| 100 <sup>*</sup>|
| 每个容器的最大唯一键数|10 <sup>*</sup>|
| 每个唯一键约束的最大路径数|16 <sup>*</sup>|
| 最大 TTL 值 |2147483647|

<sup>*</sup> 可以通过创建 [Azure 支持请求](create-support-request-quota-increase.md)来增加上述每个容器的限制。

## <a name="per-item-limits"></a>每项的限制

根据所用的 API，Azure Cosmos 项可以代表集合中的文档、表中的行，或者图形中的节点或边缘。 下表显示了 Cosmos DB 中每个项的限制。 

| 资源 | 默认限制 |
| --- | --- |
| 项的最大大小 | 2 MB（JSON 表示形式的 UTF-8 长度） |
| 分区键值的最大长度 | 2048 个字节 |
| ID 值的最大长度 | 1023 个字节 |
| 每项的最大属性数 | 无实际限制 |
| 属性名称的最大长度 | 无实际限制 |
| 属性值的最大长度 | 无实际限制 |
| 字符串属性值的最大长度 | 无实际限制 |
| 数字属性值的最大长度 | IEEE754 双精度 64 位 |
| 嵌入对象/数组的最大嵌套级别 | 128 |
| 最大 TTL 值 |2147483647|

除了对分区键和 ID 值的长度限制以及 2 MB 的总大小限制外，对项负载没有任何限制，如属性数量和嵌套深度。 可能需要为具有大型或复杂项结构的容器配置索引策略，以减少 RU 消耗。 [为 Cosmos DB 中的项建模](how-to-model-partition-example.md)中提供了一个真实示例，以及用于管理大项的模式。

## <a name="per-request-limits"></a>每个请求限制

Azure Cosmos DB 支持对容器、项和数据库等资源执行 [CRUD 和查询操作](/rest/api/cosmos-db/)。 它还支持针对容器中具有相同分区键的多个项的[事务性批处理请求](/dotnet/api/microsoft.azure.cosmos.transactionalbatch)。

| 资源 | 默认限制 |
| --- | --- |
| 单个操作（例如存储过程执行或单个查询页检索）的最长执行时间| 5 秒 |
| 最大请求大小（例如，存储过程、CRUD）| 2 MB |
| 最大响应大小（例如分页查询） | 4 MB |
| 事务性批处理中的最大操作数 | 100 |

一旦查询之类的操作达到执行超时或响应大小限制，它会向客户端返回结果页和继续标记，以继续执行操作。 单个查询可以针对不同的页面/继续执行活动运行的持续时间没有实际的限制。

Cosmos DB 使用 HMAC 进行授权。 可以使用主密钥或[资源令牌](secure-access-to-data.md)对容器、分区键或项等资源进行精细的访问控制。 下表列出了 Cosmos DB 中授权令牌的限制。

| 资源 | 默认限制 |
| --- | --- |
| 主令牌最长过期时间 | 15 分钟  |
| 资源令牌最短过期时间 | 10 分钟  |
| 资源令牌最长过期时间 | 默认为 24 小时。 可以通过[开具 Azure 支持票证](create-support-request-quota-increase.md)来提高此限制|
| 令牌授权的最大时钟偏差| 15 分钟 |

Cosmos DB 支持在写入期间执行触发器。 对于每个写入操作，该服务最多支持一个预先触发器和一个后期触发器。

## <a name="metadata-request-limits"></a>元数据请求限制

Azure Cosmos DB 为每个帐户维护系统元数据。 此元数据可用于免费枚举集合、数据库、其他 Azure Cosmos DB 资源及其配置。

| 资源 | 默认限制 |
| --- | --- |
|每分钟最大集合创建速率|    100|
|每分钟最大数据库创建速率|    100|
|每分钟最大预配吞吐量更新速率|    5|

## <a name="limits-for-autoscale-provisioned-throughput"></a>自动缩放预配吞吐量限制

有关自动缩放吞吐量和存储限制的更多详细说明，请参阅[自动缩放](provision-throughput-autoscale.md#autoscale-limits)文章和[常见问题解答](autoscale-faq.yml#lowering-the-max-ru-s)。

| 资源 | 默认限制 |
| --- | --- |
| 系统可以缩放到的最大 RU/秒 |  `Tmax`，用户设置的自动缩放最大 RU/秒|
| 系统可以缩放到的最小 RU/秒 | `0.1 * Tmax`|
| 系统缩放到的当前 RU/秒  |  `0.1*Tmax <= T <= Tmax`根据使用情况|
| 每小时最小可计费 RU/秒| `0.1 * Tmax` <br></br>计费以小时为单位，在这种情况下，将按系统在一小时内缩放到的最高 RU/秒或 `0.1*Tmax` 计费，以较大者为准。 |
| 容器的最小自动缩放最大 RU/秒  |  `MAX(4000, highest max RU/s ever provisioned / 10, current storage in GB * 100)`，舍入为最接近的 1000 RU/秒 |
| 数据库的最小自动缩放最大 RU/秒  |  `MAX(4000, highest max RU/s ever provisioned / 10, current storage in GB * 100,  4000 + (MAX(Container count - 25, 0) * 1000))`，舍入为最接近的 1000 RU/秒。 <br></br>请注意，如果数据库有 25 个以上的容器，每增加一个容器，系统会将最小自动缩放最大 RU/秒增加 1000 RU/秒。 例如，如果有 30 个容器，则可以设置的最低自动缩放最大 RU/秒为 9000 RU/秒（缩放范围是 900 - 9000 RU/秒）。

## <a name="sql-query-limits"></a>SQL 查询限制

Cosmos DB 支持使用 [SQL](./sql-query-getting-started.md) 查询项。 下表描述了查询语句的限制，例如子句数目或查询长度方面的限制。

| 资源 | 默认限制 |
| --- | --- |
| 最大 SQL 队列长度| 256 KB |
| 每个查询的最大 JOIN 数| 5 <sup>*</sup>|
| 每个查询的最大 UDF 数| 10 <sup>*</sup>|
| 每个多边形的最大点数| 4096 |
| 每个容器的最大包含路径数| 500 |
| 每个容器的最大排除路径数| 500 |
| 组合索引中的最大属性数| 8 |

<sup>*</sup> 可以通过创建 [Azure 支持请求](create-support-request-quota-increase.md)来增加上述任一 SQL 查询限制。

## <a name="mongodb-api-specific-limits"></a>MongoDB API 特定限制

Cosmos DB 支持针为 MongoDB 编写的应用程序使用 MongoDB 线路协议。 可以在[支持的 MongoDB 功能和语法](mongodb/feature-support-32.md)中找到支持的命令和协议版本。

下表列出了特定于 MongoDB 功能支持的限制。 针对 SQL（核心）API 所述的其他服务限制同样适用于 MongoDB API。

| 资源 | 默认限制 |
| --- | --- |
| 最大 MongoDB 查询内存大小（此限制仅适用于 3.2 服务器版本） | 40 MB |
| MongoDB 操作的最长执行时间（适用于 3.2 服务器版本）| 15 秒|
| MongoDB 操作的最长执行时间（适用于 3.6 和 4.0 服务器版本）| 60 秒|
| 索引定义上嵌入对象/数组的最大嵌套级别 | 6 |
| 导致服务器端连接关闭的空闲连接超时值* | 30 分钟 |

\* 我们建议客户端应用程序将驱动程序设置中的空闲连接超时值设为 2-3 分钟，因为 [Azure LoadBalancer 的默认超时值为 4 分钟](../load-balancer/load-balancer-tcp-idle-timeout.md)。  此超时将确保客户端计算机与 Azure Cosmos DB 之间的中间负载均衡器不会关闭空闲连接。

## <a name="try-cosmos-db-free-limits"></a>免费试用 Cosmos DB 限制

下表列出了[免费试用 Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) 的限制。

| 资源 | 默认限制 |
| --- | --- |
| 试用持续时间 | 30 天（过期后可以请求重新试用） <br> 过期后，存储的信息将被删除。 |
| 每个订阅的最大容器数（SQL、Gremlin、表 API） | 1 |
| 每个订阅的最大容器数 (MongoDB API) | 3 |
| 每个容器的最大吞吐量 | 5000 |
| 每个共享吞吐量数据库的最大吞吐量 | 20000 |
| 每个帐户的最大总存储量 | 10 GB |

试用 Cosmos DB 仅支持美国中部、北欧和东南亚地区的全局分发。 无法为 Azure Cosmos DB 试用帐户创建 Azure 支持票证。 但是，为参与现有支持计划的订阅者提供了支持。

## <a name="azure-cosmos-db-free-tier-account-limits"></a>Azure Cosmos DB 免费层帐户的限制

下表列出了 [Azure Cosmos DB 免费层帐户](optimize-dev-test.md#azure-cosmos-db-free-tier)的限制。

| 资源 | 默认限制 |
| --- | --- |
| 每个 Azure 订阅的免费层帐户数 | 1 |
| 免费层折扣持续时间 | 帐户的生存期。 必须在创建帐户期间选择加入。 |
| 免费层的最大 RU/s | 1000 RU/秒 |
| 免费层的最大存储量 | 25 GB |
| 最大共享吞吐量数据库数 | 5 |
| 共享吞吐量数据库中的最大容器数 | 25 <br>在免费层帐户中，最多包含 25 个容器的共享吞吐量数据库的最小 RU/s 为 400 RU/s。 |

除上述限制外，[每个帐户的限制](#per-account-limits)也适用于免费层帐户。 有关详细信息，请参阅如何[创建免费层帐户](free-tier.md)一文。

## <a name="next-steps"></a>后续步骤

详细了解 Cosmos DB 的核心概念：[全局分布](distribute-data-globally.md)[分区](partitioning-overview.md)以及[预配吞吐量](request-units.md)。

请通过阅读以下快速入门文章之一，来开始使用 Azure Cosmos DB：

* [Azure Cosmos DB SQL API 入门](create-sql-api-dotnet.md)
* [Azure Cosmos DB 的用于 MongoDB 的 API 入门](mongodb/create-mongodb-nodejs.md)
* [Azure Cosmos DB Cassandra API 入门](cassandra/manage-data-dotnet.md)
* [Azure Cosmos DB Gremlin API 入门](create-graph-dotnet.md)
* [Azure Cosmos DB 表 API 入门](table/create-table-dotnet.md)

> [!div class="nextstepaction"]
> [免费试用 Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/)
