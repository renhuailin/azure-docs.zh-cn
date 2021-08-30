---
title: 再平衡分片 - 超大规模 (Citus) - Azure Database for PostgreSQL
description: 在服务器之间均匀分布分片以获得更好的性能
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 07/20/2021
ms.openlocfilehash: 19b44306185dcfcdd3675898b9066019f686dc75
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114463550"
---
# <a name="rebalance-shards-in-hyperscale-citus-server-group"></a>在超大规模 (Citus) 服务器组中再平衡分片

若要利用新添加的节点，必须再平衡分布式表[分片](concepts-hyperscale-distributed-data.md#shards)，这意味着将一些分片从现有节点移到新节点。 超大规模 (Citus) 提供零停机时间重新平衡，意味着查询可以在分片重新平衡期间运行，而不会中断。

## <a name="determine-if-the-server-group-needs-a-rebalance"></a>确定服务器组是否需要再平衡

Azure 门户可以显示服务器组中工作器节点之间的数据分布是否均匀。 若要查看此信息，请在“服务器组管理”菜单中转到“分片再平衡器”页 。 如果工作器之间的数据分布不均，则你会看到“建议再平衡”消息，以及每个节点的大小列表。

如果数据已平衡，则你会看到“此时不建议再平衡”消息。

## <a name="run-the-shard-rebalancer"></a>运行分片再平衡器

若要启动分片再平衡器，需要连接到服务器组的协调器节点，然后对分布式表运行 [rebalance_table_shards](reference-hyperscale-functions.md#rebalance_table_shards) SQL 函数。 该函数会再平衡以参数命名的表的[并置](concepts-hyperscale-colocation.md)组中的所有表。 因此，不必为每个分布式表调用该函数，只需在每个并置组的代表表上调用该函数即可。

```sql
SELECT rebalance_table_shards('distributed_table_name');
```

## <a name="monitor-rebalance-progress"></a>监视再平衡进度

若要在启动再平衡器后对其进行监视，请返回 Azure 门户。 在“服务器组管理”中打开“分片再平衡器”页 。 其中会显示“正在再平衡”消息以及两个表。

第一个表显示移入或移出节点的分片数，例如“已移入 6 个，共 24 个”。 第二个表显示每个数据库表的进度：名称、受影响的分片计数、受影响的数据大小，以及再平衡状态。

选择“刷新”按钮可更新页面。 再平衡完成后，页面上会再次显示“此时不建议再平衡”。

## <a name="next-steps"></a>后续步骤

- 详细了解服务器组[性能选项](concepts-hyperscale-configuration-options.md)。
- 纵向或横向[缩放服务器组](howto-hyperscale-scale-grow.md)
- 请参阅 [rebalance_table_shards](reference-hyperscale-functions.md#rebalance_table_shards) 参考资料
