---
title: 再平衡分片 - 超大规模 (Citus) - Azure Database for PostgreSQL
description: 在服务器之间均匀分布分片以获得更好的性能
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 11/17/2020
ms.openlocfilehash: b69c4fc3ff16cf30181a3529f61af7126b92950b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "95026380"
---
# <a name="rebalance-shards-in-hyperscale-citus-server-group"></a>在超大规模 (Citus) 服务器组中再平衡分片

若要利用新添加的节点，必须再平衡分布式表[分片](concepts-hyperscale-distributed-data.md#shards)，这意味着将一些分片从现有节点移到新节点。 首先验证新工作器节点的预配是否已成功完成。 然后使用 psql 连接到群集协调器节点并运行以下内容，以启动分片再平衡器：

```sql
SELECT rebalance_table_shards('distributed_table_name');
```

[rebalance_table_shards](reference-hyperscale-functions.md#rebalance_table_shards) 函数会重新平衡以参数命名的表的[并置](concepts-hyperscale-colocation.md)组中的所有表。 因此，不必为每个分布式表调用该函数，只需在每个并置组的代表表上调用该函数即可。

后续步骤


- 详细了解服务器组[性能选项](concepts-hyperscale-configuration-options.md)。
- 纵向或横向[缩放服务器组](howto-hyperscale-scale-grow.md)
- 请参阅 [rebalance_table_shards](reference-hyperscale-functions.md#rebalance_table_shards) 参考资料
