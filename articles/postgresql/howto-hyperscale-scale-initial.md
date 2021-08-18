---
title: 初始服务器组大小 - 超大规模 (Citus) - Azure Database for PostgreSQL
description: 为用例选取正确的初始大小
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 08/03/2021
ms.openlocfilehash: 22a0fb4d06194138d5169df5005d5a23b3580569
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121726141"
---
# <a name="pick-initial-size-for-hyperscale-citus-server-group"></a>选择超大规模 (Citus) 服务器组的初始大小

服务器组的大小（节点数和硬件容量）很[容易更改](howto-hyperscale-scale-grow.md)。 但仍需为新服务器组选择初始大小。 下面是适用于合理选择的一些提示。

## <a name="use-cases"></a>用例

超大规模 (Citus) 经常按以下方式使用。

### <a name="multi-tenant-saas"></a>多租户 SaaS

当从现有单节点 PostgreSQL 数据库实例迁移到超大规模 (Citus) 时，请选择一个辅助角色 VCore 和 RAM 总数等于原始实例的群集。 在这种情况下，我们实现了2~3倍的性能改进，因为分片可提高资源利用率，从而允许较小的索引等。

VCore 计数实际上是唯一的决策。 如[超大规模 (Citus) 配置选项](concepts-hyperscale-configuration-options.md)页中所述，RAM 分配当前可根据 vCore 计数确定。
协调器节点需要的 RAM 不像辅助角色那样多，但无法单独选择 RAM 和 Vcore。

### <a name="real-time-analytics"></a>实时分析

vCore 总数：当工作数据可容纳在 RAM 中时，可以期望超大规模 (Citus) 上出现与辅助角色核心数成正比的线性性能提高。 若要根据需要确定正确的 vCore 数，请考虑单节点数据库中查询的当前延迟以及超大规模 (Citus) 中的必需延迟。 将当前延迟除以必需延迟，对结果进行舍入。

辅助角色 RAM：最佳情况是提供足够的内存，使大部分工作集可容纳在内存中。 应用程序使用的查询类型会影响内存要求。 可以对查询运行“EXPLAIN ANALYZE”以确定它需要的内存量。 请记住，Vcore 和 RAM 按“[超大规模 (Citus) 配置选项](concepts-hyperscale-configuration-options.md)”一文中所述进行缩放。

## <a name="choosing-a-hyperscale-citus-tier"></a>选择超大规模 (Citus) 层

上述部分说明了每个用例所需的 vCore 数和 RAM 量。 可通过在两个超大规模 (Citus) 层之间进行选择来满足这些需求：基本层和标准层。

基本层使用单一数据库节点执行处理，而标准层允许使用更多节点。 这两个层在其他方面是相同的，提供相同的功能。 在某些情况下，单一节点的 vCore 和磁盘空间可扩展到足够的规模，而在其他情况下，这需要多个节点的协作。

有关层的比较，请参阅[基本层](concepts-hyperscale-tiers.md)概念页。

## <a name="next-steps"></a>后续步骤

- [缩放服务器组](howto-hyperscale-scale-grow.md)
- 了解有关服务器组[性能选项](concepts-hyperscale-configuration-options.md)的详细信息。
