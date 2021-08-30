---
title: 有关使用已启用 Arc 的超大规模 PostgreSQL 服务器组分发数据和横向扩展的概念
titleSuffix: Azure Arc-enabled data services
description: 有关使用已启用 Arc 的超大规模 PostgreSQL 服务器组分发数据的概念
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 06/02/2021
ms.topic: how-to
ms.openlocfilehash: 69405ac6386587e6621c67532a8bd7f41450370e
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2021
ms.locfileid: "113729931"
---
# <a name="concepts-for-distributing-data-with-arc-enabled-postgresql-hyperscale-server-group"></a>有关使用已启用 Arc 的超大规模 PostgreSQL 服务器组分发数据的概念

本文解释了要最大程度地受益于已启用 Azure Arc 的超大规模 PostgreSQL 而要理解的重要概念。
下面链接的文章指向 Azure Database for PostgreSQL 超大规模 (Citus) 的概念解释。 Azure Database for PostgreSQL 超大规模 (Citus) 与已启用 Azure Arc 的超大规模 PostgreSQL 属于同一种技术，因此本文所述的概念和观点对它同样适用。

**它们之间的区别是什么？**
- _Azure Database for PostgreSQL 超大规模 (Citus)_

这是在 Azure 中以数据库即服务 (PaaS) 形式提供的超大规模外形规格的 Postgres 数据库引擎。 它由实现超大规模体验的 Citus 扩展提供支持。 在此外形规格中，服务在 Microsoft 数据中心运行，由 Microsoft 操作。

- 已启用 Azure Arc 的超大规模 PostgreSQL

这是通过已启用 Azure Arc 的数据服务提供的超大规模外形规格的 Postgres 数据库引擎。 在此外形规格中，我们的客户将提供用于托管系统和运行系统的基础结构。

下面汇总了有关已启用 Azure Arc 的超大规模 PostgreSQL 的重要概念：

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="nodes-and-tables"></a>节点和表
了解以下概念对于最大程度地受益于已启用 Azure Arc 的超大规模 PostgreSQL 非常重要：
- 已启用 Azure Arc 的超大规模 PostgreSQL 中的专用 Postgres 节点：协调器和工作器
- 表类型：分布式表、引用表和本地表
- Shards

参阅 [Azure Database for PostgreSQL - 超大规模 (Citus) 中的节点和表](../../postgresql/concepts-hyperscale-nodes.md)中的详细信息。 

## <a name="determine-the-application-type"></a>确定应用程序类型
明确你要生成的应用程序类型非常重要。 为什么？ 因为对已启用 Azure Arc 的超大规模 PostgreSQL 服务器组运行高效查询需要在服务器之间正确分布表。 建议的分布方式因应用程序的类型及其查询模式而异。 概括而言，有两种类型的应用程序可在已启用 Azure Arc 的超大规模 Postgres 上正常运行：
- 多租户应用程序
- 实时应用程序

数据建模中的第一步是确定其中哪些应用程序与你的应用程序更类似。

参阅[确定应用程序类型](../../postgresql/concepts-hyperscale-app-type.md)中的详细信息。


## <a name="choose-a-distribution-column"></a>选择分布列
为何选择分布式列？

这是你要做出的最重要建模决策之一。 已启用 Azure Arc 的超大规模 PostgreSQL 根据行的分布列值在分片中存储行。 正确的选择会将相关数据一起分组到相同物理节点上，从而可使查询快速运行，并添加对所有 SQL 功能的支持。 不正确的选择会导致系统缓慢运行，并且不能在各个节点中支持所有的 SQL 功能。 本文提供了适用于两种最常见超大规模方案的分布列提示。

参阅[选择分布列](../../postgresql/concepts-hyperscale-choose-distribution-column.md)中的详细信息。


## <a name="table-colocation"></a>表共置

共置涉及到将相关信息一起存储在相同节点上。 当提供了所有必要数据时，查询可以快速运行，而且不会产生任何网络流量。 将相关数据共置在不同节点上可使查询在每个节点上高效地并行运行。

参阅[表共置](../../postgresql/concepts-hyperscale-colocation.md)中的详细信息。


## <a name="next-steps"></a>后续步骤
- [了解如何创建已启用 Azure Arc 的超大规模 PostgreSQL](create-postgresql-hyperscale-server-group.md)
- [了解如何横向扩展在 Arc 数据控制器中创建的已启用 Azure Arc 的超大规模 PostgreSQL 服务器组](scale-out-in-postgresql-hyperscale-server-group.md)
- [了解已启用 Azure Arc 的数据服务](https://azure.microsoft.com/services/azure-arc/hybrid-data-services)
- [了解 Azure Arc](https://aka.ms/azurearc)

