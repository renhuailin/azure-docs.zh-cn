---
title: 通过 Azure Cosmos DB 图形访问系统文档属性
description: 了解如何通过 Gremlin API 读取和写入 Cosmos DB 系统文档属性
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: how-to
ms.date: 09/16/2021
author: manishmsfte
ms.author: mansha
ms.openlocfilehash: 8a0574d5622ae0ceceb52be72ccd8c5d99a18529
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128614800"
---
# <a name="system-document-properties"></a>系统文档属性
[!INCLUDE[appliesto-gremlin-api](../includes/appliesto-gremlin-api.md)]

Azure Cosmos DB 具有与每个文档有关的[系统属性](/rest/api/cosmos-db/databases)，例如 ```_ts```、```_self```、```_attachments```、```_rid``` 和 ```_etag```。 此外，Gremlin 引擎会添加与边缘有关的 ```inVPartition``` 和 ```outVPartition``` 属性。 默认情况下，这些属性可供遍历。 但是，可将特定属性或所有这些属性都包含在 Gremlin 遍历中。

```console
g.withStrategies(ProjectionStrategy.build().IncludeSystemProperties('_ts').create())
```

## <a name="e-tag"></a>E-Tag

此属性用于执行乐观并发控制。 如果应用程序需要将操作分为几个不同的遍历，则可以使用 eTag 属性避免并发写入时的数据丢失。

```console
g.withStrategies(ProjectionStrategy.build().IncludeSystemProperties('_etag').create()).V('1').has('_etag', '"00000100-0000-0800-0000-5d03edac0000"').property('test', '1')
```

## <a name="time-to-live-ttl"></a>生存时间 (TTL)

如果集合已启用文档过期，并且文档上设置了 `ttl` 属性，则此属性将在 Gremlin 遍历中可用作常规的顶点或边缘属性。 启用生存时间属性曝光不需要 `ProjectionStrategy`。

* 使用以下命令设置新顶点的生存时间：

  ```console
  g.addV(<ID>).property('ttl', <expirationTime>)
  ```

  例如，在 123 秒后会自动删除使用以下遍历创建的顶点：

  ```console
  g.addV('vertex-one').property('ttl', 123)
  ```

* 使用以下命令设置现有顶点的生存时间：

  ```console
  g.V().hasId(<ID>).has('pk', <pk>).property('ttl', <expirationTime>)
  ```

* 对顶点应用生存时间属性不会自动将该属性应用到边缘。 因为边缘在数据库存储中是独立记录。 使用以下命令设置顶点的生存时间以及顶点的所有传入和传出边缘：

  ```console
  g.V().hasId(<ID>).has('pk', <pk>).as('v').bothE().hasNot('ttl').property('ttl', <expirationTime>)
  ```

可以将容器的 TTL 设置为 -1，或者从 Azure 门户中将其设置为“打开(无默认值)”，然后，该 TTL 对于任何项均为无限（除非该项显式设置了 TTL 值）。

## <a name="next-steps"></a>后续步骤
* [Cosmos DB 乐观并发](../faq.yml#how-does-the-sql-api-provide-concurrency-)
* Azure Cosmos DB 中的[生存时间 (TTL)](../time-to-live.md)
