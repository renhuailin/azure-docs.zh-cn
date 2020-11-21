---
title: 规模服务器组-超大规模 (Citus) -Azure Database for PostgreSQL
description: 调整服务器组内存、磁盘和 CPU 资源来处理增加的负载
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 11/17/2020
ms.openlocfilehash: 59e6e73c99569b0a35c56d65c1a7ccdfcb394c0f
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/21/2020
ms.locfileid: "95026414"
---
# <a name="scale-a-hyperscale-citus-server-group"></a>缩放超大规模 (Citus) 服务器组

Azure Database for PostgreSQL-超大规模 (Citus) 提供自助扩展，以处理增加的负载。 Azure 门户可以轻松地添加新的辅助角色节点，以及增加现有节点的 Vcore。 添加节点会导致不停机，甚至会将分片移动到新节点 (称为 [分片重新平衡](howto-hyperscale-scale-rebalance.md)) 发生，而不会中断查询。

## <a name="add-worker-nodes"></a>添加辅助角色节点

若要添加节点，请在超大规模 (Citus) 服务器组中转到 " **计算 + 存储** " 选项卡。  拖动 **辅助节点计数** 的滑块将更改该值。

:::image type="content" source="./media/howto-hyperscale-scaling/01-sliders-workers.png" alt-text="资源滑块":::

单击 " **保存** " 按钮以使更改的值生效。

> [!NOTE]
> 增加并保存后，使用滑块无法减少工作节点数。

> [!NOTE]
> 若要利用新添加的节点，必须重新 [平衡分布式表分片](howto-hyperscale-scale-rebalance.md)，这意味着将某些 [分片](concepts-hyperscale-distributed-data.md#shards) 从现有节点移到新节点。

## <a name="increase-or-decrease-vcores-on-nodes"></a>增加或减少节点上的 Vcore

除了添加新节点以外，还可以增加现有节点的功能。 向上和向下调整计算容量可用于性能试验，以及对流量需求的短期或长期更改。

若要更改所有辅助节点的 Vcore，请在 "**配置 (每个辅助角色节点)** 下调整 **vcore** 滑块。 协调器节点的 Vcore 可以独立进行调整。 调整 "**配置 (协调器" 节点** 下的 " **vcore** " 滑块) 。

## <a name="next-steps"></a>后续步骤

- 了解有关服务器组 [性能选项](concepts-hyperscale-configuration-options.md)的详细信息。
- 重新[平衡分布式表分片](howto-hyperscale-scale-rebalance.md)，以便所有工作节点都可以参与并行查询
