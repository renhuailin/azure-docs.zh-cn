---
title: include 文件 description: include 文件 services: event-hubs author: spelluru ms.service: event-hubs ms.topic: include ms.date: 05/25/2021 ms.author: spelluru ms.custom: "include file","fasttrack-edit","iot","event-hubs"

---

下表显示的限制对于基本层、标准层和专用层可能会有所不同。 在表中，CU 是[容量单位](../event-hubs-dedicated-overview.md)，PU 是[处理单位](../event-hubs-scalability.md#processing-units)，TU 是[吞吐量单位](../event-hubs-scalability.md#throughput-units)。 

| 限制 | 基本 | Standard | 高级 |  专用 |
| ----- | ----- | -------- | -------- | --------- | 
| 事件中心发布的最大大小 | 256 KB | 1 MB | 1 MB |  1 MB |
| 每个事件中心的使用者组数 | 1 | 20 | 100 | 1000<br/>单个 CU 没有限制  |
| 每个命名空间的中转连接数 | 100 | 5,000 | 每个处理单位（每个 PU）10000 | 每个 CU 100,000 |
| 事件数据的最长保留期限 | 1 天 | 7 天 | 90 天<br/>每个 PU 1 TB | 90 天<br/>每个 CU 10 TB |
| 最大 TU、PU 或 CU 数 |20 TU | 40 个 TU | 16 PU | 20 CU |
| 每个事件中心的分区数 | 32 | 32 | 100（固定） | 每个事件中心 1024 个<br/> 每个 CU 2000 个 |
| 每个订阅的命名空间数 | 1000 | 1000 | 1000 | 1000（每个 CU 50） |
| 每个命名空间的事件中心数 | 10 | 10 | 每个 PU 100 | 1000 |
| 捕获 | 不适用 | 按每小时支付 | 已含 | 已含 |
| 架构注册表（命名空间）的大小（以兆字节为单位） | 不适用 | 25 | 100 | 1024 |
| 架构注册表或命名空间中的架构组数 | 不适用 | 1 - 排除默认组 | 100 <br/>每个架构 1 MB | 1000<br/>每个架构 1 MB |
| 所有架构组的架构版本数 | 不适用 | 25 | 1000 | 10000 |
| 每个单位的吞吐量 | 流入量 - 每秒 1 MB 或每秒 1000 个事件<br/>流出量 - 每秒 2 MB 或每秒 4096 个事件 | 流入量 - 每秒 1 MB 或每秒 1000 个事件<br/>流出量 - 每秒 2 MB 或每秒 4096 个事件 | 单个 PU 无限制 * | 单个 CU 无限制 * |

\* 取决于多种因素，例如，资源分配、分区数、存储，等等。 
 

> [!NOTE]
> 可以逐个或者成批发送事件。 无论是单个事件还是一批事件，发布限制（根据 SKU）均适用。 发布大于最大阈值的事件将被拒绝。

