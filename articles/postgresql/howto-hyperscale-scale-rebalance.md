---
title: 重新平衡分片-超大规模 (Citus) -Azure Database for PostgreSQL
description: 在服务器之间平均分配分片以获得更好的性能
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 11/17/2020
ms.openlocfilehash: b69c4fc3ff16cf30181a3529f61af7126b92950b
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/21/2020
ms.locfileid: "95026380"
---
# <a name="rebalance-shards-in-hyperscale-citus-server-group"></a>重新平衡超大规模 (Citus) 服务器组中的分片

若要利用新添加的节点，必须重新平衡分布式表 [分片](concepts-hyperscale-distributed-data.md#shards)，这意味着将某些分片从现有节点移到新节点。 首先，验证新辅助角色是否已成功完成设置。 然后，通过使用 psql 连接到群集协调器节点并运行来启动分片 rebalancer：

```sql
SELECT rebalance_table_shards('distributed_table_name');
```

[Rebalance_table_shards](reference-hyperscale-functions.md#rebalance_table_shards)函数间重新平衡其参数中命名的表的[归置](concepts-hyperscale-colocation.md)组中的所有表。 因此，您不必为每个分布式表调用函数，只需在每个归置组的代表性表中调用它。

后续步骤


- 了解有关服务器组 [性能选项](concepts-hyperscale-configuration-options.md)的详细信息。
- 向上或向外[缩放服务器组](howto-hyperscale-scale-grow.md)
- 请参阅 [rebalance_table_shards](reference-hyperscale-functions.md#rebalance_table_shards) 参考资料
