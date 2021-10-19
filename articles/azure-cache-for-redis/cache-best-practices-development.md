---
title: 开发的最佳做法
titleSuffix: Azure Cache for Redis
description: 了解如何开发 Azure Cache for Redis 的代码。
author: shpathak-msft
ms.service: cache
ms.topic: conceptual
ms.date: 10/11/2021
ms.author: shpathak
ms.openlocfilehash: 4d10af1d6f7b56c578d201c51b4c706eae0e8bf6
ms.sourcegitcommit: 54e7b2e036f4732276adcace73e6261b02f96343
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/12/2021
ms.locfileid: "129808748"
---
# <a name="development"></a>开发

## <a name="connection-resilience-and-server-load"></a>连接复原能力和服务器负载

开发客户端应用程序时，请务必考虑与[连接复原能力](cache-best-practices-connection.md)和[管理服务器负载](cache-best-practices-server-load.md)相关的最佳做法。

## <a name="consider-more-keys-and-smaller-values"></a>考虑更多键和较小的值

Azure Cache for Redis 的值越小，效果最佳。 请考虑将较大的数据块划分为较小的区块，以将数据分布到多个键。 有关理想值大小的详细信息，请参阅[这篇文章](https://stackoverflow.com/questions/55517224/what-is-the-ideal-value-size-range-for-redis-is-100kb-too-large/)。

## <a name="large-request-or-response-size"></a>请求或响应大小过大

请求/响应过大可能导致超时。 例如，假设你在客户端上配置的超时值为 1 秒。 你的应用程序（使用相同的物理网络连接）的同时请求两个键 （例如，A 和 B）。 大多数客户端支持对请求进行“管道操作”，使得请求“A”和“B”可以逐个发送，而无需等待响应。 服务器会按相同顺序将响应发送回来。 如果响应“A”较大，可能会消耗掉后续请求的大部分超时时间。

在以下示例中，请求“A”和“B”快速发送到服务器。 服务器开始快速发送响应“A”和“B”。 由于数据传输需要时间，即使服务器的响应速度很快，响应“B”也必须等到响应“A”超时。

```console
|-------- 1 Second Timeout (A)----------|
|-Request A-|
     |-------- 1 Second Timeout (B) ----------|
     |-Request B-|
            |- Read Response A --------|
                                       |- Read Response B-| (**TIMEOUT**)
```

此请求/响应很难度量值。 可对客户端代码进行检测，以跟踪大型请求和响应。

针对大型响应的解决方法各不相同，但是包括：

- 优化应用程序以处理大量的小值，而不是处理少量的大值。
    - 首选解决方案是将数据分解成较小的相关值。
    - 请参阅帖子 [Redis 的理想值大小范围是多大？100 KB 是否太大？](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ)以了解有关为何推荐更小值的详细信息。
- 增大 VM 的大小以获得更高的带宽能力
    - 提高客户端或服务器 VM 上的带宽可以缩短较大响应的数据传输时间。
    - 将两台计算机上的网络用量与当前 VM 大小的限制进行比较。 只提高服务器上的带宽，或者只提高客户端上的带宽，都不足以解决问题。
- 增加应用程序使用的连接对象数。
    - 使用轮询方法通过不同的连接对象发出请求。

## <a name="key-distribution"></a>键分布

如果计划使用 Redis 聚类分析，请先阅读[针对键的 Redis 聚类分析最佳做法](https://redislabs.com/blog/redis-clustering-best-practices-with-keys/)。

## <a name="use-pipelining"></a>使用管道

尝试选择支持 [Redis 管道](https://redis.io/topics/pipelining)的 Redis 客户端。 管道有助于高效利用网络并尽可能获取最佳吞吐量。

## <a name="avoid-expensive-operations"></a>避免高开销的操作

某些 Redis 操作（如 [KEYS](https://redis.io/commands/keys) 命令）开销很高，应避免使用。 有关长时间运行命令的一些注意事项，请参阅[长时间运行的命令](cache-troubleshoot-server.md#long-running-commands)

## <a name="choose-an-appropriate-tier"></a>选择适当的层级

对生产系统使用标准层或高级层。  请勿在生产环境中使用基本层。 基本层是没有数据复制和 SLA 的单节点系统。 此外，使用至少一个 C1 缓存。 C0 缓存仅适用于简单的开发/测试方案，原因如下：

- 它们共享一个 CPU 核心
- 使用少量内存
- 容易出现“近邻干扰”问题

建议进行性能测试以选择适当的层级，并验证连接设置。 有关更多信息，请参阅[性能测试](cache-best-practices-performance.md)。

## <a name="client-in-same-region-as-cache"></a>客户端与缓存位于同一区域

将缓存实例和应用程序定位在同一区域中。 连接到不同区域中的缓存可能会明显提高延迟并降低可靠性。  

虽然可以从 Azure 外部进行连接，但不建议这样做，尤其是使用 Redis 作为缓存时。  如果仅将 Redis 服务器用作键/值存储，那么延迟可能不是主要问题。

## <a name="use-tls-encryption"></a>使用 TLS 加密

默认情况下，Azure Cache for Redis 要求使用 TLS 加密通信。 目前支持 TLS 版本 1.0、1.1 和 1.2。 但是，TLS 1.0 和 TLS 1.1 即将在全行业范围内弃用，因此，请尽可能使用 TLS 1.2。

如果客户端库或工具不支持 TLS，则可通过 [Azure 门户](cache-configure.md#access-ports)或[管理 API](/rest/api/redis/redis/update) 来启用未加密的连接。 在无法加密连接的情况下，建议将缓存和客户端应用程序放入虚拟网络中。 有关虚拟网络缓存方案中使用的端口的详细信息，请参阅此[表](cache-how-to-premium-vnet.md#outbound-port-requirements)。

## <a name="client-library-specific-guidance"></a>特定于客户端库的指南

- [StackExchange.Redis (.NET)](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-stackexchange-redis-md)
- [Java - 应使用哪种客户端？](https://gist.github.com/warrenzhu25/1beb02a09b6afd41dff2c27c53918ce7#file-azure-redis-java-best-practices-md)
- [Lettuce (Java)](https://github.com/Azure/AzureCacheForRedis/blob/main/Lettuce%20Best%20Practices.md)
- [Jedis (Java)](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-java-jedis-md)
- [Node.js](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-node-js-md)
- [PHP](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-php-md)
- [HiRedisCluster](https://github.com/Azure/AzureCacheForRedis/blob/main/HiRedisCluster%20Best%20Practices.md)
- [ASP.NET 会话状态提供程序](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-session-state-provider-md)

## <a name="next-steps"></a>后续步骤

- [Azure Cache for Redis 开发的常见问题解答](cache-development-faq.yml)
- [性能测试](cache-best-practices-performance.md)
- [Azure Cache for Redis 的故障转移和修补](cache-failover.md)
