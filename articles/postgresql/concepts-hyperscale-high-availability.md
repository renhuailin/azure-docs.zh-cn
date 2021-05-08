---
title: 高可用性 - 超大规模 (Citus) - Azure Database for PostgreSQL
description: 高可用性和灾难恢复概念
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 406b4f2ada665d65ee0452579e24744d1f7cfc63
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "91314847"
---
# <a name="high-availability-in-azure-database-for-postgresql--hyperscale-citus"></a>Azure Database for PostgreSQL - 超大规模 (Citus) 中的高可用性

高可用性 (HA) 可维护服务器组中每个节点的备用副本，从而避免数据库停机。 如果某个节点关闭，超大规模 (Citus) 会将传入连接从失败的节点切换到其备用节点。 故障转移几分钟即可完成，提升的节点通过 PostgreSQL 同步流式复制，始终可以获取全新数据。

若要在协调器节点上利用 HA，数据库应用程序需要检测和重试已删除的连接和失败的事务。 可以通过相同的连接字符串来访问新提升的协调器。

恢复可以分为三个阶段：检测、故障转移和完全恢复。  超大规模 (Citus) 会在每个节点上运行定期运行状况检查，检查失败四次后，其将确定节点是否关闭。 然后，超大规模 (Citus) 会将一个备用提升至主节点状态（故障转移），并预配新的备用。
流式复制开始，从而使新节点保持最新状态。  所有数据均复制后，节点已实现完全恢复。

### <a name="next-steps"></a>后续步骤

- 了解如何在超大规模 (Citus) 服务器组中[启用“高可用性”](howto-hyperscale-high-availability.md)。
