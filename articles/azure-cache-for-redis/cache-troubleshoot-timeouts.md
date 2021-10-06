---
title: 排查 Azure Cache for Redis 超时问题
description: 了解如何解决 Azure Cache for Redis 的常见超时问题，例如 Redis 服务器修补和 StackExchange.Redis 超时异常。
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 10/18/2019
ms.openlocfilehash: fab4587cc6320cc020a1d92eb1c6fc2f8fa3e3af
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2021
ms.locfileid: "129537378"
---
# <a name="troubleshoot-azure-cache-for-redis-timeouts"></a>排查 Azure Cache for Redis 超时问题

本部分讨论如何排查连接到 Azure Cache for Redis 时出现的超时问题。

- [Redis 服务器修补](#redis-server-patching)
- [StackExchange.Redis 超时异常](#stackexchangeredis-timeout-exceptions)

> [!NOTE]
> 本指南中的多个故障排除步骤包括了运行 Redis 命令和监视各种性能指标的说明。 如需更多信息和说明，请参阅 [其他信息](#additional-information) 部分的文章。
>

## <a name="redis-server-patching"></a>Redis 服务器修补

Azure Cache for Redis 定期更新其服务器软件，作为它提供的托管服务功能的一部分。 此[修补](cache-failover.md)活动主要在幕后进行。 在故障转移期间，当修补 Redis 服务器节点时，连接到这些节点的 Redis 客户端在这些节点之间切换连接时可能会遇到临时超时。 有关修补可能对应用程序产生的副作用以及如何改进其修补事件处理的详细信息，请参阅[故障转移如何影响我的客户端应用程序](cache-failover.md#how-does-a-failover-affect-my-client-application)。

## <a name="stackexchangeredis-timeout-exceptions"></a>StackExchange.Redis 超时异常

StackExchange.Redis 使用名为 `synctimeout` 的配置设置进行同步操作，该设置的默认值为 5000 毫秒。 如果同步调用未在此时间内完成，StackExchange.Redis 客户端会引发类似于以下示例的超时错误：

```output
    System.TimeoutException: Timeout performing MGET 2728cc84-58ae-406b-8ec8-3f962419f641, inst: 1,mgr: Inactive, queue: 73, qu=6, qs=67, qc=0, wr=1/1, in=0/0 IOCP: (Busy=6, Free=999, Min=2,Max=1000), WORKER (Busy=7,Free=8184,Min=2,Max=8191)
```

此错误消息中包含的指标可以指出问题的原因和可能的解决方法。 下表包含有关错误消息指标的详细信息。

| 错误消息指标 | 详细信息 |
| --- | --- |
| inst |在最后一个时间切片中：发出了 0 条命令 |
| mgr |套接字管理器正在执行 `socket.select`，即，它正在请求 OS 指示一个需要执行某种操作的套接字。 读取器并未主动从网络读取数据，因为它认为不需执行任何操作 |
| queue |总共有 73 个正在进行的操作 |
| qu |正在进行的操作中，有 6 个操作位于未发送队列中，而尚未写入到出站网络 |
| qs |正在进行的操作中，有 67 个操作已发送给服务器，但尚未得到响应。 响应可能为 `Not yet sent by the server` 或 `sent by the server but not yet processed by the client.` |
| qc |正在进行的操作中，有 0 个操作已看到回复，但尚未标记为完成，因为它们正在完成循环中等待 |
| wr |存在活动的写入器（这意味着系统不会忽略这 6 个尚未发送的请求）字节/活动写入器 |
| in |没有活动的读取器，NIC 字节/活动读取器上没有可供读取的字节 |

在前一异常示例中，`IOCP` 和 `WORKER` 部分都包含一个大于 `Min` 值的 `Busy` 值。 存在不同意味着应调整 `ThreadPool` 设置。 可以[配置 ThreadPool 设置](cache-management-faq.yml#important-details-about-threadpool-growth)，以确保线程池在突发情况下快速地纵向扩展。

可以使用以下步骤调查可能的根本原因。

1. 作为最佳做法，在使用 StackExchange.Redis 客户端时，请确保遵循以下模式进行连接。

    ```csharp
    private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
    {
        return ConnectionMultiplexer.Connect("cachename.redis.cache.windows.net,abortConnect=false,ssl=true,password=...");

    });

    public static ConnectionMultiplexer Connection
    {
        get
        {
            return lazyConnection.Value;
        }
    }
    ```

    有关详细信息，请参阅[使用 StackExchange.Redis 连接到缓存](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache)。

1. 确保服务器和客户端应用程序位于 Azure 中的同一区域。 例如，如果缓存位于美国东部但客户端位于美国西部，而且请求没有在 `synctimeout` 时间间隔内完成，则可能出现超时；或者，如果从本地开发计算机进行调试，也可能出现超时。 

    强烈建议将缓存和客户端置于同一 Azure 区域。 如果方案中包括跨区域调用，则应将 `synctimeout` 时间间隔设置为比默认的 5000 毫秒时间间隔更高的值，方法是在连接字符串中包括 `synctimeout` 属性。 以下示例显示了 Azure Cache for Redis 提供的 StackExchange.Redis 的连接字符串的代码片段（其中 `synctimeout` 为 8000 毫秒）。

    ```output
    synctimeout=8000,cachename.redis.cache.windows.net,abortConnect=false,ssl=true,password=...
    ```

1. 确保使用最新版本的 [StackExchange.Redis NuGet 包](https://www.nuget.org/packages/StackExchange.Redis/)。 我们会不断对代码中的 Bug 进行修正，以便更好地应对超时情况。因此，请务必使用最新的版本。
1. 如果请求受服务器或客户端上的带宽限制的约束，则需要更长的时间才能完成，因此可能会导致超时。 若要了解超时是否是客户端网络带宽造成的，请参阅[服务器端带宽限制](cache-troubleshoot-server.md#server-side-bandwidth-limitation)。 若要了解超时是否是客户端网络带宽造成的，请参阅[客户端带宽限制](cache-troubleshoot-client.md#client-side-bandwidth-limitation)。
1. 操作是否占用了服务器或客户端上的大量 CPU？

   - 检查是否受到了客户端上的 CPU 消耗量的约束。 较高的 CPU 可能会导致请求无法在 `synctimeout` 时间间隔内得到处理，从而导致请求超时。改用更大型客户端或者将负载分散也许有助于控制这种情况。
   - 监视 CPU [缓存性能指标](cache-how-to-monitor.md#available-metrics-and-reporting-intervals)，检查是否受到了服务器上的 CPU 消耗量的约束。 如果请求传入时 Redis 受到 CPU 消耗量的约束，则可能会导致这些请求超时。为了解决此问题，可以将负载分散到高级缓存的多个分片中，也可以升级缓存大小或定价层。 有关详细信息，请参阅[服务器端带宽限制](cache-troubleshoot-server.md#server-side-bandwidth-limitation)。
1. 是否存在需要在服务器上进行长时间处理的命令？ 在 Redis 服务器上花费很长时间处理请求的命令可能会导致超时。 有关长时间运行的命令的详细信息，请参阅[长时间运行的命令](cache-troubleshoot-server.md#long-running-commands)。 可以使用 redis-cli 客户端或 [Redis 控制台](cache-configure.md#redis-console)连接到 Azure Redis 缓存实例。 然后，运行 [SLOWLOG](https://redis.io/commands/slowlog) 命令查看是否存在比预期速度更慢的请求。 Redis 服务器和 StackExchange.Redis 适合处理多个小型请求，而不适合处理寥寥数个大型请求。 将数据拆分成更小的块可能会解决问题。

    有关使用 redis-cli 和 stunnel 连接到缓存 TLS/SSL 终结点的信息，请参阅博客文章：[Announcing ASP.NET Session State Provider for Redis Preview Release](https://devblogs.microsoft.com/aspnet/announcing-asp-net-session-state-provider-for-redis-preview-release/)（宣布推出适用于 Redis 的 ASP.NET 会话状态提供程序预览版）。
1. Redis 服务器负载过高可能会导致超时。 可以通过监视 `Redis Server Load` [缓存性能指标](cache-how-to-monitor.md#available-metrics-and-reporting-intervals)来监视服务器负载。 服务器负载值为 100（最大值）表示 Redis 服务器正忙于处理请求，没有空闲时间。 若要查看某些请求是否占用了服务器的全部处理能力，请按上一段中的说明运行 SlowLog 命令。 有关详细信息，请参阅“CPU 使用率/服务器负载过高”。
1. 客户端上是否存在其他可能导致网络故障的事件？ 常见事件包括：增加或减少客户端实例的数目、部署新的客户端版本，或启用自动缩放。 我们在测试中发现，自动缩放或扩展/缩减可能会导致出站网络连接断开几秒。 StackExchange.Redis 代码可以灵活应对此类事件，并且会重新连接。 重新连接时，队列中的所有请求可能超时。
1. 在向缓存发出多个小型请求之前，是否存在导致超时的大型请求？ 错误消息中的参数 `qs` 会告知，有多少个请求已从客户端发送到服务器，但尚未处理响应。 此值可能会持续增加，因为 StackExchange.Redis 使用单个 TCP 连接，一次只能读取一个响应。 即使第一个操作超时，也不会阻止与服务器之间来回发送数据。 在完成大型请求之前，系统会阻止其他请求，从而可能导致超时。 降低超时概率的一种解决方案是确保缓存对于工作负荷来说足够大，并将大的值拆分成较小的块。 另一种可能的解决方案是使用客户端中的 `ConnectionMultiplexer` 对象池，在发送新请求时选择负载最小的 `ConnectionMultiplexer`。 通过多个连接对象进行加载应该可以防止单次超时导致其他请求也发生超时。
1. 如果使用 `RedisSessionStateProvider`，请确保正确设置重试超时。 `retryTimeoutInMilliseconds` 应高于 `operationTimeoutInMilliseconds`，否则无法执行重试。 在下面的示例中， `retryTimeoutInMilliseconds` 设置为 3000。 有关详细信息，请参阅 [Azure Redis 缓存的 ASP.NET 会话状态提供程序](cache-aspnet-session-state-provider.md)和 [How to use the configuration parameters of Session State Provider and Output Cache Provider](https://github.com/Azure/aspnet-redis-providers/wiki/Configuration)（如何使用会话状态提供程序和输出缓存提供程序的配置参数）。

    ```xml
    <add
      name="AFRedisCacheSessionStateProvider"
      type="Microsoft.Web.Redis.RedisSessionStateProvider"
      host="enbwcache.redis.cache.windows.net"
      port="6380"
      accessKey="…"
      ssl="true"
      databaseId="0"
      applicationName="AFRedisCacheSessionState"
      connectionTimeoutInMilliseconds = "5000"
      operationTimeoutInMilliseconds = "1000"
      retryTimeoutInMilliseconds="3000" />
    ```

1. 通过[监视](cache-how-to-monitor.md#available-metrics-and-reporting-intervals) `Used Memory RSS` 和 `Used Memory`，检查 Azure Cache for Redis 服务器上的内存使用情况。 如果实施了逐出策略，则当 `Used_Memory` 达到缓存大小时，Redis 就会开始逐出密钥。 理想情况下，`Used Memory RSS` 应只稍高于 `Used memory`。 差异过大意味着出现内存碎片（内部或外部）。 如果 `Used Memory RSS` 小于 `Used Memory`，则意味着部分缓存内存已由操作系统更换。 如果发生这种情况，则会出现明显的延迟。 由于 Redis 无法控制如何将其分配映射到内存页，`Used Memory RSS` 偏高通常是内存用量出现高峰而造成的。 当 Redis 服务器释放内存时，分配器会回收该内存，但不一定会将内存重新分配到系统。 `Used Memory` 值与操作系统所报告的内存消耗量可能存在差异。 内存可能已由 Redis 使用并释放，但尚未重新分配到系统。 为了帮助缓解内存问题，可执行以下步骤：

   - 将缓存升级到更大的大小，以免系统中的内存限制导致运行受阻。
   - 对密钥设置过期时间，以便主动逐出过旧的值。
   - 监视 `used_memory_rss` 缓存指标。 当此值接近缓存大小时，可能会开始出现性能问题。 将数据分配到多个分片（如果使用高级缓存），或者升级到更大的缓存大小。

   有关详细信息，请参阅 [Redis 服务器上的内存压力](cache-troubleshoot-server.md#memory-pressure-on-redis-server)。

## <a name="additional-information"></a>其他信息

- [排查 Azure Cache for Redis 客户端问题](cache-troubleshoot-client.md)
- [排查 Azure Cache for Redis 服务器端问题](cache-troubleshoot-server.md)
- [如何制定基准和测试缓存的性能？](cache-management-faq.yml#how-can-i-benchmark-and-test-the-performance-of-my-cache-)
- [如何监视 Azure Redis 缓存](cache-how-to-monitor.md)
