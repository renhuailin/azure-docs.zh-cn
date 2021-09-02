---
title: 利用生存时间使 Azure Cosmos DB 中的数据过期
description: 通过 TTL 功能，Microsoft Azure Cosmos DB 能够在一段时间后将文档自动从系统清除。
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 11/04/2020
ms.reviewer: sngun
ms.openlocfilehash: e859e244e41b6bbd065244a285653b2d9d3988e9
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123113323"
---
# <a name="time-to-live-ttl-in-azure-cosmos-db"></a>Azure Cosmos DB 中的生存时间 (TTL)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

利用 **生存时间**（简称 TTL），Azure Cosmos DB 能够在特定一段时间后自动将项从容器中删除。 默认情况下，可以在容器级别设置生存时间，并基于每个项替代该值。 在容器或项级别设置 TTL 后，Azure Cosmos DB 会在一段时间（自上次修改项的时间开始算起）后自动删除这些项。 配置的生存时间值以秒为单位。 配置 TTL 后，系统会基于 TTL 值自动删除已过期的项，不需要客户端应用程序显式发出的 delete 操作。 TTL 的最大值是 2147483647。

删除过期项是一个后台任务，它使用剩余的[请求单元](../request-units.md)，即用户请求没有使用的请求单元。 即使在 TTL 过期后，如果容器出现请求过载的情况，并且没有足够的 RU 使用，也会延迟数据删除操作。 如果提供了足够的 RU 来执行删除操作，则会删除数据。 虽然数据删除延迟，但任何查询都不会（通过任何 API）在 TTL 已过期后返回数据。

> 此内容与 Azure Cosmos DB 事务存储 TTL 相关。 如果正在查找通过 [Azure Synapse Link](../synapse-link.md) 启用 NoETL HTAP 方案的分析存储 TTL，请单击[此处](../analytical-store-introduction.md#analytical-ttl)。

## <a name="time-to-live-for-containers-and-items"></a>容器和项的生存时间

生存时间值是以秒为单位设置的，解释为自上次修改项的时间的增量。 可以针对容器或容器中的项设置生存时间：

1. **容器的生存时间**（使用 `DefaultTimeToLive` 设置）：

   - 如果缺失（或设置为 null），则项不会自动过期。

   - 如果存在且值设置为“-1”（无限期），则默认情况下，项不会过期。

   - 如果存在且值设置为某个非零数字（“n”） ，则项将在上次修改“n”秒后过期。

2. **项的生存时间**（使用 `ttl` 设置）：

   - 仅当父容器的 `DefaultTimeToLive` 存在且不是设置为 null 时，此属性才适用。

   - 如果存在，它将替代父容器的 `DefaultTimeToLive` 值。

## <a name="time-to-live-configurations"></a>生存时间配置

- 如果将某个容器的 TTL 设置为“n”，该容器中的项将在 n 秒后过期。    如果同一容器中的项有自身的生存时间且 TTL 设置为 -1（表示不会过期），或者某些项使用不同的数字替代了生存时间设置，则这些项会根据其自己的已配置 TTL 值过期。

- 如果未针对某个容器设置 TTL，则此容器中的项的生存时间不起作用。

- 如果某个容器的 TTL 设置为 -1，则此容器中生存时间设置为 n 的项将在 n 秒后过期，剩余的项不会过期。

## <a name="examples"></a>示例

本部分显示分配给容器和项不同生存时间值的一些示例：

### <a name="example-1"></a>示例 1

容器的 TTL 设置为 null (DefaultTimeToLive = null)

|项的 TTL| 结果|
|---|---|
|ttl = null|TTL 已禁用。 该项将永不过期（默认值）。|
|ttl = -1|TTL 已禁用。 该项将永不过期。|
|ttl = 2000|TTL 已禁用。 该项将永不过期。|

### <a name="example-2"></a>示例 2

容器的 TTL 设置为 -1 (DefaultTimeToLive = -1)

|项的 TTL| 结果|
|---|---|
|ttl = null|TTL 已启用。 该项将永不过期（默认值）。|
|ttl = -1|TTL 已启用。 该项将永不过期。|
|ttl = 2000|TTL 已启用。 该项将在 2000 秒后过期。|

### <a name="example-3"></a>示例 3

容器的 TTL 设置为 1000 (DefaultTimeToLive = 1000)

|项的 TTL| 结果|
|---|---|
|ttl = null|TTL 已启用。 该项将在 1000 秒（默认值）后过期。|
|ttl = -1|TTL 已启用。 该项将永不过期。|
|ttl = 2000|TTL 已启用。 该项将在 2000 秒后过期。|

## <a name="next-steps"></a>后续步骤

通过以下文章了解如何配置生存时间：

- [如何配置生存时间](how-to-time-to-live.md)