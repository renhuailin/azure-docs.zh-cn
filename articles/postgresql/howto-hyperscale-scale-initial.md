---
title: 初始服务器组大小 - 超大规模 (Citus) - Azure Database for PostgreSQL
description: 为用例选取正确的初始大小
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 11/17/2020
ms.openlocfilehash: 7e68e9f8caad7d7e4bc44bc4e1e55150a78b4a98
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "95026397"
---
# <a name="pick-initial-size-for-hyperscale-citus-server-group"></a>选择超大规模 (Citus) 服务器组的初始大小

服务器组的大小（节点数和硬件容量）很[容易更改](howto-hyperscale-scale-grow.md)。 但仍需为新服务器组选择初始大小。 下面是适用于合理选择的一些提示。

## <a name="multi-tenant-saas-use-case"></a>多租户 SaaS 用例

当从现有单节点 PostgreSQL 数据库实例迁移到超大规模 (Citus) 时，请选择一个辅助角色 VCore 和 RAM 总数等于原始实例的群集。 在这种情况下，我们实现了2~3倍的性能改进，因为分片可提高资源利用率，从而允许较小的索引等。

VCore 计数实际上是唯一的决策。 如[超大规模 (Citus) 配置选项](concepts-hyperscale-configuration-options.md)页中所述，RAM 分配当前可根据 vCore 计数确定。
协调器节点需要的 RAM 不像辅助角色那样多，但无法单独选择 RAM 和 Vcore。

## <a name="real-time-analytics-use-case"></a>实时分析用例

vCore 总数：当工作数据可容纳在 RAM 中时，可以期望超大规模 (Citus) 上出现与辅助角色核心数成正比的线性性能提高。 若要根据需要确定正确的 vCore 数，请考虑单节点数据库中查询的当前延迟以及超大规模 (Citus) 中的必需延迟。 将当前延迟除以必需延迟，对结果进行舍入。

辅助角色 RAM：最佳情况是提供足够的内存，使大部分工作集可容纳在内存中。 应用程序使用的查询类型会影响内存要求。 可以对查询运行“EXPLAIN ANALYZE”以确定它需要的内存量。 请记住，Vcore 和 RAM 按“[超大规模 (Citus) 配置选项](concepts-hyperscale-configuration-options.md)”一文中所述进行缩放。

## <a name="next-steps"></a>后续步骤

- [缩放服务器组](howto-hyperscale-scale-grow.md)
- 了解有关服务器组[性能选项](concepts-hyperscale-configuration-options.md)的详细信息。
