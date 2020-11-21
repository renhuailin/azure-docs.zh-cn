---
title: 初始服务器组大小-超大规模 (Citus) -Azure Database for PostgreSQL
description: 为用例选取正确的初始大小
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 11/17/2020
ms.openlocfilehash: 7e68e9f8caad7d7e4bc44bc4e1e55150a78b4a98
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/21/2020
ms.locfileid: "95026397"
---
# <a name="pick-initial-size-for-hyperscale-citus-server-group"></a>选择超大规模 (Citus) 服务器组的初始大小

服务器组的大小（节点数和硬件容量）很 [容易更改](howto-hyperscale-scale-grow.md)) 。 但仍需为新服务器组选择初始大小。 下面是适用于合理选择的一些提示。

## <a name="multi-tenant-saas-use-case"></a>多租户 SaaS 使用情况

从现有的单节点 PostgreSQL 数据库实例迁移到超大规模 (Citus) 时，请选择一个群集，其中，的工作线程数和 RAM 总数等于原始实例的数目。 在这种情况下，我们已了解到2倍的性能改进，因为分片可提高资源利用率，并允许较小的索引等等。

VCore 计数实际上是唯一的决策。 RAM 分配当前根据 vCore 计数确定，如 [超大规模 (Citus) 配置选项](concepts-hyperscale-configuration-options.md) "页中所述。
协调器节点不需要像辅助角色一样多的 RAM，但无法单独选择 RAM 和 Vcore。

## <a name="real-time-analytics-use-case"></a>实时分析用例

Total Vcore：当工作数据适合 RAM 时，超大规模 (Citus 的线性性能提高) 与辅助角色内核数成正比。 若要根据需要确定正确的 Vcore 数，请考虑单节点数据库中查询的当前延迟，以及超大规模 (Citus) 中所需的延迟时间。 将当前延迟除以必需延迟，对结果进行舍入。

辅助角色 RAM：最佳情况是提供足够的内存，使大部分工作集可容纳在内存中。 应用程序使用的查询类型会影响内存要求。 您可以运行对查询进行分析，以确定其所需的内存量。 请记住，Vcore 和 RAM 按 [超大规模 (Citus) 配置选项](concepts-hyperscale-configuration-options.md) "一文中所述进行扩展。

## <a name="next-steps"></a>后续步骤

- [缩放服务器组](howto-hyperscale-scale-grow.md)
- 了解有关服务器组 [性能选项](concepts-hyperscale-configuration-options.md)的详细信息。
