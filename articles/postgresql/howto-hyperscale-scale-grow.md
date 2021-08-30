---
title: 缩放服务器组 - 超大规模 (Citus) - Azure Database for PostgreSQL
description: 调整服务器组内存、磁盘和 CPU 资源以处理增加的负载
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 08/03/2021
ms.openlocfilehash: 12c4cd7848b0d58fd6b91e27e254ccc613ff5676
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121751909"
---
# <a name="scale-a-hyperscale-citus-server-group"></a>缩放超大规模 (Citus) 服务器组

Azure Database for PostgreSQL - 超大规模 (Citus) 提供了自助服务扩展以处理增加的负载。 通过 Azure 门户，可以轻松添加新工作器节点，并增加现有节点的 vCore。 添加节点不会导致停机，即使将分片移到新节点（称为[分片重新均衡](howto-hyperscale-scale-rebalance.md)）也不会中断查询。

## <a name="add-worker-nodes"></a>添加工作器节点

若要添加节点，请转到超大规模 (Citus) 服务器组中的“计算 + 存储”选项卡。  拖动“工作器节点计数”的滑块可更改该值。

> [!NOTE]
>
> 使用[基本层](concepts-hyperscale-tiers.md)创建的超大规模 (Citus) 服务器组没有辅助角色。 增加工作器计数会自动将服务器组升级到标准层。  在将服务器组升级到标准层之后，不能将其降级回基本层。

:::image type="content" source="./media/howto-hyperscale-scaling/01-sliders-workers.png" alt-text="资源滑块":::

单击“保存”按钮以使更改的值生效。

> [!NOTE]
> 增加并保存后，无法使用滑块减少工作器节点数。

> [!NOTE]
> 若要利用新添加的节点，必须[重新均衡分布式表分片](howto-hyperscale-scale-rebalance.md)，这意味着将一些[分片](concepts-hyperscale-distributed-data.md#shards)从现有节点移到新节点。 重新均衡可以在后台运行，无需停机。

## <a name="increase-or-decrease-vcores-on-nodes"></a>增加或减少节点上的 vCore

除了添加新节点之外，还可以增加现有节点的功能。 调高和调低计算容量对于性能试验以及流量需求的短期或长期更改都很有用。

若要更改所有工作器节点的 vCore 数，请调整“配置(每工作器节点)”下的“vCore”滑块。 协调器节点的 vCore 数可以单独调整。 调整“配置(协调器节点)”下的“vCore”滑块。

## <a name="increase-storage-on-nodes"></a>增加节点上的存储

除了添加新节点之外，还可以增加现有节点的磁盘空间。 增加磁盘空间可让你在需要添加更多工作器节点之前，对现有工作器节点执行更多操作。

若要更改所有工作器节点的存储，请调整“配置(每工作器节点)”下的“存储”滑块。 协调器节点的存储可以单独调整。 调整“配置(协调器节点)”下的“存储”滑块。

> [!NOTE]
> 每个节点的存储在增加并保存后无法使用滑块来减少。

## <a name="next-steps"></a>后续步骤

- 详细了解服务器组[性能选项](concepts-hyperscale-configuration-options.md)。
- [重新均衡分布式表分片](howto-hyperscale-scale-rebalance.md)，使所有工作器节点都可以参与并行查询
