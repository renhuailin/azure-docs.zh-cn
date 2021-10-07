---
title: “用于 Redis 的 Azure 缓存”示例
description: 了解如何通过以下代码示例使用 Azure Cache for Redis：连接到缓存、在缓存中读取和写入数据、ASP.NET Azure Cache for Redis 提供程序。
author: curib
ms.author: cauribeg
ms.service: cache
ms.custom: devx-track-dotnet
ms.topic: sample
ms.date: 01/23/2017
ms.openlocfilehash: 899bf97fc638fa2a763013319e517b76cd37d361
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2021
ms.locfileid: "129537629"
---
# <a name="azure-cache-for-redis-samples"></a>“用于 Redis 的 Azure 缓存”示例
本文提供了 Azure Cache for Redis 示例列表。 这些示例涵盖了如下所述的方案： 

* 连接到缓存
* 向/从缓存写入/读取数据
* 使用 ASP.NET Azure Cache for Redis 提供程序。 

某些示例是可下载的项目。 其他示例提供包含代码片段的分步指导，但未链接到可下载的项目。

## <a name="hello-world-samples"></a>Hello world 示例
本部分中的示例演示有关连接到 Azure Cache for Redis 实例的基本知识。 示例还将演示如何使用不同的语言和 Redis 客户端在缓存中读取和写入数据。

[Hello world](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) 示例演示如何使用 [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) .NET 客户端执行各种缓存操作。

此示例演示如何：

* 使用不同的连接选项
* 使用同步和异步操作与缓存相互读取和写入对象
* 使用 Redis MGET/MSET 命令返回指定键的值
* 执行 Redis 事务操作
* 处理 Redis 列表和排序集
* 使用 JsonConvert 序列化程序存储.NET 对象
* 使用 Redis 集实现标记
* 使用 Redis 群集

有关详细信息，请参阅 GitHub 上的 [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) 文档。 有关更多使用方案，请参阅 [StackExchange.Redis.Tests](https://github.com/StackExchange/StackExchange.Redis/tree/master/tests) 单元测试。

[如何将 Azure Redis 缓存与 Python 配合使用](cache-python-get-started.md)展示了如何使用 Python 和 [redis-py](https://github.com/andymccurdy/redis-py) 客户端开始使用 Azure Redis 缓存。

[在缓存中处理 .NET 对象](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache)演示如何对 .NET 对象进行序列化，以便可以将这些对象写入到 Azure Cache for Redis 实例以及从中读取它们。 

## <a name="use-azure-cache-for-redis-as-a-scale-out-backplane-for-aspnet-signalr"></a>将 Azure Redis 缓存用作 ASP.NET SignalR 的横向扩展基架
[将 Azure Cache for Redis 用作 ASP.NET SignalR 的横向扩展基架](https://github.com/rustd/RedisSamples/tree/master/RedisAsSignalRBackplane)示例演示如何将 Azure Cache for Redis 用作 SignalR 基架。 有关基架的更多信息，请参阅[采用 Redis 的 SignalR 扩展](https://www.asp.net/signalr/overview/performance/scaleout-with-redis)。

## <a name="azure-cache-for-redis-customer-query-sample"></a>Azure Redis 缓存客户查询示例
此示例对从缓存访问数据与从持久存储访问数据时的性能进行了比较。 此示例有两个项目。

* [展示 Azure Redis 缓存如何通过对数据进行缓存提高性能](https://github.com/rustd/RedisSamples/tree/master/RedisCacheCustomerQuerySample)
* [为进行展示创立数据库和缓存](https://github.com/rustd/RedisSamples/tree/master/SeedCacheForCustomerQuerySample)

## <a name="aspnet-session-state-and-output-caching"></a>ASP.NET 会话状态和输出缓存
[使用 Azure Cache for Redis 来存储 ASP.NET SessionState 和 OutputCache](https://github.com/rustd/RedisSamples/tree/master/SessionState_OutputCaching) 示例演示：  

* 如何使用 Azure Cache for Redis 来存储 ASP.NET 会话和输出缓存
* 为 Redis 使用 SessionState 和 OutputCache 提供程序。

## <a name="manage-azure-cache-for-redis-with-maml"></a>使用 MAML 管理 Azure Redis 缓存
[使用 Azure 管理库来管理 Azure Cache for Redis](https://github.com/rustd/RedisSamples/tree/master/ManageCacheUsingMAML) 示例演示如何使用 Azure 管理库来管理（创建/更新/删除）缓存。 

## <a name="custom-monitoring-sample"></a>自定义监视示例
[访问 Azure Cache for Redis 监视数据](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring)示例演示如何在 Azure 门户外部访问 Azure Cache for Redis 的监视数据。

## <a name="a-twitter-style-clone-written-using-php-and-redis"></a>使用 PHP 和 Redis 编写的 Twitter 式克隆
[Retwis](https://github.com/SyntaxC4-MSFT/retwis) 示例是 Redis Hello World。 它是极简的 Twitter 式社交网络克隆，是通过 [Predis](https://github.com/nrk/predis) 客户端使用 Redis 和 PHP 编写的。 源代码非常简单，同时还可以显示不同的 Redis 数据结构。

## <a name="bandwidth-monitor"></a>带宽监视器
借助[带宽监视器](https://github.com/JonCole/SampleCode/tree/master/BandWidthMonitor)示例，可监视客户端上使用的带宽。 要测量带宽、请在缓存客户端计算机上运行该示例，对缓存执行调用，并观察带宽监视器示例报告的带宽。
