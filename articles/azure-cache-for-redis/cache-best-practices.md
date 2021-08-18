---
title: Azure Redis 缓存的最佳做法
description: 遵循这些最佳做法了解如何有效使用 Azure Redis 缓存。
author: carldc
reviewer: shpathak
ms.service: cache
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: cadaco
ms.openlocfilehash: 98382cef7e9cc1de5c7c66f0465dc621fde6e841
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2021
ms.locfileid: "114291609"
---
# <a name="best-practices-for-azure-cache-for-redis"></a>Azure Redis 缓存的最佳做法

遵循这些最佳做法可帮助最大化性能并在 Azure 中经济、高效地利用 Azure Redis 缓存实例。

## <a name="configuration-and-concepts"></a>配置和概念

* **对生产系统使用标准层或高级层。**  基本层是没有数据复制和 SLA 的单节点系统。 此外，使用至少一个 C1 缓存。  C0 缓存适用于简单的开发/测试方案，因为它们只配备了共享的 CPU 核心和少量的内存，并且容易出现“干扰性邻居”问题。

* **请记住，Redis 是一个内存中数据存储。**  [此文](cache-troubleshoot-data-loss.md)概述了可能发生数据丢失的一些情况。

* 在开发系统时让它可以处理 [由于修补和故障转移](cache-failover.md)出现的 **连接故障**。

* **配置 [maxmemory-reserved 设置](cache-configure.md#maxmemory-policy-and-maxmemory-reserved)，以提高系统在遇到内存压力时的响应能力**。  对于写入密集型工作负载，或者，如果你要在 Redis 中存储 100 KB 或更大的值，则充足的预留设置尤为重要。 如果使用的是写入密集型工作负载，则以缓存大小的 10% 为基础并增大此百分比。

* **具有较小值的 Redis 工作性能最佳**，因此请考虑将较大数据分成多个键。  [此 Redis 介绍文章](https://stackoverflow.com/questions/55517224/what-is-the-ideal-value-size-range-for-redis-is-100kb-too-large/)中列出了一些应该仔细考虑的因素。  阅读[本文](cache-troubleshoot-client.md#large-request-or-response-size)了解较大值可能引起的问题示例。

* **将缓存实例和应用程序定位在同一区域中。**  连接到不同区域中的缓存可能会明显增大延迟并降低可靠性。  尽管可以从 Azure 外部进行连接，但不建议这样做，尤其是使用 Redis 作为缓存时。  如果只是使用 Redis 作为键/值存储，则延迟可能不是主要考虑因素。

* **重复使用连接。**  创建新连接是高开销的操作，会增大延迟，因此请尽量重复使用连接。 如果你选择创建新连接，请确保在释放旧连接之前先将其关闭（即使是在 .NET 或 Java 等托管内存语言中）。

* **使用管道。**  尝试选择支持 [Redis 管道](https://redis.io/topics/pipelining)的 Redis 客户端。 管道有助于高效利用网络并尽可能获取最佳吞吐量。

* **将客户端库配置为使用至少 15 秒的连接超时**，以便即使是在 CPU 负载较高的情况下，系统也有时间建立连接。  使用较小的连接超时值无法保证在该时间范围内能够建立连接。  如果出现问题（客户端 CPU 负载偏高、服务器 CPU 负载偏高等），则使用较短的连接超时值会导致连接尝试失败。 此行为通常会使问题变得更糟。  使用较短的超时不仅无助于解决问题，而且会加剧问题，这会强制系统重启尝试重新连接的进程，从而可能导致出现“连接 -> 失败 -> 重试”循环。 我们通常建议将连接超时保留为 15 秒或更长。 让连接尝试在 15 或 20 秒后成功，比失败后立即重试更有利。 与最初让系统花费更长时间尝试连接相比，这种重试循环可能会导致服务中断的持续时间变长。  
   > [!NOTE]
   > 本指南特定于连接尝试，而与你愿意等待 GET 或 SET 等操作完成的时间无关。 

* **避免高开销操作** - 某些 Redis 操作（例如 [KEYS](https://redis.io/commands/keys) 命令）的开销很大，应该避免。  有关详细信息，请参阅有关[长时间运行的命令](cache-troubleshoot-server.md#long-running-commands)的一些注意事项

* **使用 TLS 加密** - 默认情况下，Azure Cache for Redis 需要 TLS 加密通信。  目前支持 TLS 版本 1.0、1.1 和 1.2。  但是，TLS 1.0 和 TLS 1.1 即将在全行业范围内弃用，因此，请尽可能使用 TLS 1.2。  如果客户端库或工具不支持 TLS，则可以[通过 Azure 门户](cache-configure.md#access-ports)或[管理 API](/rest/api/redis/redis/update) 来启用未加密的连接。  在无法进行加密连接的情况下，建议将缓存和客户端应用程序放入虚拟网络中。  有关虚拟网络缓存方案中使用的端口的详细信息，请参阅此[表](cache-how-to-premium-vnet.md#outbound-port-requirements)。

* 空闲超时 - Azure Cache for Redis 的连接空闲超时目前为 10 分钟，因此你的设置应小于 10 分钟。 大多数常用客户端库都有一个配置设置，客户端库可通过此设置自动定期将 Redis `PING` 命令发送到 Redis 服务器。 但是，如果使用的客户端库没有这种类型的设置，则客户应用程序本身将负责保持连接的活动状态。

<!-- Most common client libraries have keep-alive configuration that pings Azure Redis automatically. However, in clients that don't have a keep-alive setting, customer applications are responsible for keeping the connection alive.
 -->
## <a name="memory-management"></a>内存管理

可能需要考虑到与 Redis 服务器实例中内存用量相关的一些问题。  下面是一些建议：

* **选择适合应用程序的 [逐出策略](https://redis.io/topics/lru-cache)。**  Azure Redis 的默认策略是 *volatile-lru*，表示只有设置了 TTL 值的键才符合逐出条件。  如果没有任何键具有 TTL 值，则系统不会逐出任何键。  如果你希望系统在遇到内存压力的情况下允许逐出任何键，可能需要考虑使用 *allkeys-lru* 策略。

* **为键设置过期值。**  过期时会主动删除键，而不会等到出现内存压力的时候。  由于内存压力开始逐出时，可能会导致服务器负载增多。  有关详细信息，请参阅 [EXPIRE](https://redis.io/commands/expire) 和 [EXPIREAT](https://redis.io/commands/expireat) 命令的文档。

## <a name="client-library-specific-guidance"></a>特定于客户端库的指南

* [StackExchange.Redis (.NET)](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-stackexchange-redis-md)
* [Java - 应使用哪种客户端？](https://gist.github.com/warrenzhu25/1beb02a09b6afd41dff2c27c53918ce7#file-azure-redis-java-best-practices-md)
* [Lettuce (Java)](https://github.com/Azure/AzureCacheForRedis/blob/main/Lettuce%20Best%20Practices.md)
* [Jedis (Java)](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-java-jedis-md)
* [Node.js](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-node-js-md)
* [PHP](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-php-md)
* [HiRedisCluster](https://github.com/Azure/AzureCacheForRedis/blob/main/HiRedisCluster%20Best%20Practices.md)
* [ASP.NET 会话状态提供程序](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-session-state-provider-md)

## <a name="when-is-it-safe-to-retry"></a>何时可以安全重试？

遗憾的是，没有一个肯定的答案。  每个应用程序需要确定哪些操作可重试，哪些操作不可重试。  每个操作具有不同的要求以及键间的依赖关系。  下面是可能需要考虑的一些因素：

* 即使 Redis 已成功根据要求运行了命令，你也仍可能会收到客户端错误。  例如：
  * 超时是与客户端相关的概念。  如果操作已抵达服务器，服务器将运行命令，即使客户端放弃等待。  
  * 当套接字连接发生错误时，无法知道操作是否确实在服务器上运行。  例如，在服务器处理请求之后（在客户端接收响应之前），会发生连接错误。
* 如果我意外运行同一操作两次，应用程序如何做出反应？  例如，如果我递增某个整数两次而不是一次，会发生什么情况？  我的应用程序是否会从多个位置写入同一个键？  如果重试逻辑覆盖了应用的另一部分设置的值，会发生什么情况？

若要测试代码在出错的情况下的运行情况，请考虑使用[重启功能](cache-administration.md#reboot)。 重启即可了解连接故障对应用程序的影响。

## <a name="performance-testing"></a>性能测试

* **首先使用 `redis-benchmark.exe`** 以在编写自己的性能测试之前感受可能的吞吐量/延迟。  [可在此处找到](https://redis.io/topics/benchmarks) Redis 基准文档。 `redis-benchmark.exe` 不支持 TLS。 运行测试前，必须[通过门户启用非 TLS 端口](cache-configure.md#access-ports)。  可在[此处](https://github.com/MSOpenTech/redis/releases)找到 Windows 兼容版本的 redis-benchmark.exe。
* 用于测试的客户端 VM 应与 Redis 缓存实例位于 **同一区域**。
* **建议为客户端使用 Dv2 VM 系列**，因为它们具有更好的硬件，会提供最佳的结果。
* 确保所用客户端 VM 的计算和带宽资源 *至少与要测试的缓存相同。
* 在缓存中 **按照故障转移条件进行测试**。 必须确保不只是在稳定状态条件下对缓存进行性能测试。 还需要按照故障转移条件进行测试，并在测试期间测量缓存中的 CPU/服务器负载。 可以通过[重新启动主节点](cache-administration.md#reboot)来启动故障转移。 在故障转移条件下进行测试可了解应用程序在故障转移条件下的吞吐量和延迟性能。 故障转移可能在更新期间以及计划外事件期间发生。 理想情况下，即使是在故障转移期间，CPU/服务器负载峰值也应该不会很高（例如超过 80%），因为这可能会影响性能。
* 某些大小的缓存托管在具有 4 个或更多核心的 VM 上。 将 TLS 加密/解密以及 TLS 连接/断开连接工作负载分散到多个核心，使缓存 VM 上的总体 CPU 使用率降低。  [参阅此文了解有关 VM 大小和核心的详细信息](./cache-planning-faq.yml#azure-cache-for-redis-performance)
* 如果是在 Windows 设备上操作，请在客户端计算机上 **启用 VRSS**。  [请参阅此处了解详细信息](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn383582(v=ws.11))。  PowerShell 脚本示例：
   >PowerShell -ExecutionPolicy Unrestricted Enable-NetAdapterRSS -Name (  Get-NetAdapter).Name

* **考虑使用高级层 Redis 实例**。  这些缓存大小具有更好的网络延迟和吞吐量，因为它们是在 CPU 和网络两方面都更好的硬件上运行的。

   > [!NOTE]
   > [此处发布](./cache-planning-faq.yml#azure-cache-for-redis-performance)了我们观测到的性能结果供你参考。   另请注意，SSL/TLS 会增大一些开销，因此，如果你使用传输加密，延迟和/或吞吐量可能会有变化。

### <a name="redis-benchmark-examples"></a>Redis 基准示例

**测试前的设置**：使用下列延迟和吞吐量测试命令所需的数据准备缓存实例。
> redis-benchmark -h yourcache.redis.cache.windows.net -a yourAccesskey -t SET -n 10 -d 1024

**测试延迟**：使用 1k 有效负载测试 GET 请求。
> redis-benchmark -h yourcache.redis.cache.windows.net -a yourAccesskey -t GET -d 1024 -P 50 -c 4

**测试吞吐量：** 管道化的 GET 请求，其有效负载为 1k。
> redis-benchmark -h yourcache.redis.cache.windows.net -a yourAccesskey -t  GET -n 1000000 -d 1024 -P 50  -c 50