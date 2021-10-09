---
title: 内存管理的最佳做法
titleSuffix: Azure Cache for Redis
description: 了解如何有效管理 Azure Cache for Redis 内存。
author: shpathak-msft
ms.service: cache
ms.topic: conceptual
ms.date: 08/25/2021
ms.author: shpathak
ms.openlocfilehash: 36fe87e03a78a4dee34c2016b8f4723cb8aa95be
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128598720"
---
# <a name="memory-management"></a>内存管理

## <a name="eviction-policy"></a>逐出策略

选择适合应用程序的[逐出策略](https://redis.io/topics/lru-cache)。 Azure Cache for Redis 的默认策略为 `volatile-lru`，这意味着只有设置了 TTL 值的键才有资格被逐出。  如果没有任何键具有 TTL 值，则系统不会逐出任何键。  如果希望系统允许在内存不足时逐出任何键，那么可以考虑 `allkeys-lru` 策略。

## <a name="keys-expiration"></a>键过期

为键设置过期值。 过期时会主动删除键，而不会等到出现内存压力的时候。  由于内存压力而开始逐出时，可能会导致服务器负载增多。 有关详细信息，请参阅 [EXPIRE](https://redis.io/commands/expire) 和 [EXPIREAT](https://redis.io/commands/expireat) 命令的文档。

## <a name="minimize-memory-fragmentation"></a>最小化内存碎片

较大的值可能会在逐出时造成内存碎片化，并可能导致较高的内存使用率和服务器负载。

## <a name="monitor-memory-usage"></a>监视内存使用量

对内存使用量添加监视可确保内存不会用完，并且可以在遇到问题之前缩放缓存。

## <a name="configure-your-maxmemory-reserved-setting"></a>配置 maxmemory-reserved 设置

配置 [maxmemory-reserved 设置](cache-configure.md#maxmemory-policy-and-maxmemory-reserved)可提高系统响应能力：

* 对于写入密集型工作负载，或者，如果你要在缓存中存储 100 KB 或更大的值，则设置充足的预留尤为重要。 如果使用的是写入密集型工作负载，则以缓存大小的 10% 为基础并增大此百分比。

*  `maxmemory-reserved` 设置用于配置群集中预留给非缓存操作（例如故障转移期间的复制）的每个实例的内存量（以 MB 为单位）。 设置此值能够在负载变化时具有更一致的 Redis 服务器体验。 对于写入大量数据的工作负载，应将此值设置得较大。 为此类操作保留内存后，无法存储缓存数据。

*  `maxfragmentationmemory-reserved` 用于配置群集中预留以容纳内存碎片的每个实例的内存量（以 MB 为单位）。 设置此值后，当缓存已满或接近满的状态并且碎片比率很高时，Redis 服务器体验更加稳定。 为此类操作保留内存后，无法存储缓存数据。

* 选择新的内存预留值（`maxmemory-reserved` 或 `maxfragmentationmemory-reserved`）时，请注意此更改对已在运行的包含大量数据的缓存有何影响。 例如，如果你的 53 GB 缓存中已有 49 GB 数据，然后将预留值更改为 8 GB，那么系统的最大可用内存将降至 45 GB。 如果你的当前 `used_memory` 或  `used_memory_rss` 值高于 45 GB 的新限制，则系统需要逐出数据，直到  `used_memory` 和  `used_memory_rss` 均低于 45 GB。 逐出可能会增加服务器负载和内存碎片。 有关 `used_memory` 和 `used_memory_rss`等缓存指标的详细信息，请参阅 [可用指标和报告时间间隔](cache-how-to-monitor.md#available-metrics-and-reporting-intervals)。

## <a name="next-steps"></a>后续步骤

* [开发的最佳做法](cache-best-practices-development.md)
* [Azure Cache for Redis 开发的常见问题解答](cache-development-faq.yml)
* [maxmemory-reserved 设置](cache-configure.md#maxmemory-policy-and-maxmemory-reserved)
* [缩放最佳做法](cache-best-practices-scale.md)
