---
title: 选择分布列 – 超大规模 (Citus) - Azure Database for PostgreSQL
description: 了解如何在 Azure Database for PostgreSQL - 超大规模 (Citus) 的常见方案中选择分布列。
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 129eff8c954c0c5469d3607e6ae16ce3202630ed
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "91929330"
---
# <a name="choose-distribution-columns-in-azure-database-for-postgresql--hyperscale-citus"></a>在 Azure Database for PostgreSQL - 超大规模 (Citus) 中选择分布列

选择每个表的分发列是你将做出的最重要的建模决策之一。 Azure Database for PostgreSQL – 超大规模 (Citus) 根据行的分布列的值将行存储在分片中。

正确的选择会将相关数据一起分组到相同物理节点上，从而可使查询快速运行，并添加对所有 SQL 功能的支持。 不正确的选择会导致系统缓慢运行，并且不能在各个节点中支持所有的 SQL 功能。

本文提供了两个最常见的超大规模 (Citus) 方案的分布列提示。

### <a name="multi-tenant-apps"></a>多租户应用

多租户体系结构使用一种分层数据库建模形式，以跨服务器组中的节点分布查询。 数据层次结构的顶部称为“租户 ID”，需要存储在每个表的列中。

超大规模 (Citus) 检查查询项目，以查看其涉及的租户 ID，并找到匹配表分片。 它将查询路由到包含分片的单个工作器节点。 在同一节点上运行包含所有相关数据的查询称为归置。

下图说明了多租户数据模型中的归置。 它包含两个表：帐户和 Campaigns，每个表由 `account_id` 分布。 阴影框代表分片。 绿色分片存储在一个工作器节点上，而蓝色分片存储在另一个工作器节点上。 请注意，当两个表限制为同一帐户\_ id 时，帐户和 Campaigns 之间的联接查询如何将所有必要的数据包含在同一个节点上。

![多租户归置](media/concepts-hyperscale-choosing-distribution-column/multi-tenant-colocation.png)

若要在自己的架构中应用此设计，请确定在应用程序中构成租户的内容。 常见的实例包括公司、帐户、组织或客户。 列名称将类似于 company_id 或 customer_id。 检查每个查询并问自己，如果有其他 WHERE 子句来将涉及的所有表限制为具有相同租户 ID 的行，它是否起作用？
多租户模型中的查询的作用域为租户。 例如，针对销售或库存的查询作用域在特定商店内。

#### <a name="best-practices"></a>最佳做法

-   **按常见租户\_ id 列分区分布式表。** 例如，在租户为公司的 SaaS 应用程序中，租户\_ id 很可能是公司\_ id。
-   **将小型跨租户表转换为引用表。** 当多个租户共享一个较小的信息表时，将它作为引用表进行分布。
-   **按租户\_ id 限制筛选所有应用程序查询。** 每次查询只能请求一个租户的信息。

有关如何生成此类应用程序的示例，请参阅[多租户教程](./tutorial-design-database-hyperscale-multi-tenant.md)。

### <a name="real-time-apps"></a>实时应用

多租户体系结构引入了一个层次结构，并使用数据归置来路由每个租户的查询。 与此相反，实时体系结构依赖于其数据的特定分布属性来实现高度并行的处理。

我们在实时模型中使用“实体 ID”作为分布列的术语。 典型实体是用户、主机或设备。

实时查询通常要求按日期或类别分组的数值聚合。 超大规模 (Citus) 将这些查询发送到每个分片以获得部分结果，并将最终答案汇编到协调器节点。 当尽可能多的节点参与，并且没有单个节点必须执行不成比例的工作量时，查询运行速度最快。

#### <a name="best-practices"></a>最佳做法

-   **选择一个具有高基数的列作为分布列。** 如果要进行比较，如果订单表具有“新”、“已支付”和“已发货”值，则“状态”字段不是适当的分布列选择。 该字段仅假定这几个值，它们限制了可容纳数据的分片的数目，以及可处理数据的节点数。 在具有高基数的列中，选择那些经常用于“分组依据”子句或用作联接键的列也是不错的选择。
-   **选择包含均匀分布的列。** 如果根据偏向特定公用值的列进行表分布，则表中的数据往往会累积到特定分片。 最终保存这些分片的节点所做的工作多于其他节点。
-   **将事实数据表和维度表分布在其通用列上。**
    事实数据表只能有一个分布键。 联接其他键的表将不会与事实数据表相连接。 根据联接的频率和联接行的大小，选择要归置的一个维度。
-   **将一些维度表更改为引用表。** 如果维度表不能与事实数据表一起使用，则可以通过将维度表的副本以引用表的形式分布到所有节点，从而提高查询性能。

有关如何生成此类应用程序的示例，请阅读[实时仪表板教程](./tutorial-design-database-hyperscale-realtime.md)。

### <a name="time-series-data"></a>时序数据

在时序工作负载中，应用程序会在存档旧信息时查询最新信息。

在超大规模 (Citus) 中对时序信息进行建模时，最常见的错误是使用时间戳本身作为分布列。 基于时间的哈希分布时间似乎随机将时间分布于不同的分片，而不是在分片中同时保留时间范围。 涉及时间的查询通常引用时间范围，例如，最新的数据。 这种类型的哈希分布会导致网络开销。

#### <a name="best-practices"></a>最佳做法

-   **不要选择时间戳作为分布列。** 选择不同的分布列。 在多租户应用中，使用租户 ID，或在实时应用中使用实体 ID。
-   **改为使用时间分区的 PostgreSQL 表。** 使用表分区可将大量有时间顺序的数据分解成多个继承表，其中每个表都包含不同的时间范围。 将 Postgres 分区表分布于超大规模 (Citus) 可为继承的表创建分片。

## <a name="next-steps"></a>后续步骤
- 了解分布式数据之间的[归置](concepts-hyperscale-colocation.md)如何帮助查询快速运行。
