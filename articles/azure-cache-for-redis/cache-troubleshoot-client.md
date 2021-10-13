---
title: 排查 Azure Cache for Redis 客户端问题
description: 了解如何解决 Azure Cache for Redis 的常见客户端问题，例如 Redis 客户端内存压力、流量突发、高 CPU、带宽受限、大型的请求或大型的响应大小。
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: troubleshooting
ms.date: 10/18/2019
ms.openlocfilehash: bd0ca3b20cc37ecf2107e03eea5d6e4a62633f16
ms.sourcegitcommit: 54e7b2e036f4732276adcace73e6261b02f96343
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/12/2021
ms.locfileid: "129807186"
---
# <a name="troubleshoot-azure-cache-for-redis-client-side-issues"></a>排查 Azure Cache for Redis 客户端问题

此部分讨论由于应用程序使用的 Redis 客户端上出现状况而导致的故障排除问题。

- [Redis 客户端上的内存压力](#memory-pressure-on-redis-client)
- [流量突增](#traffic-burst)
- [客户端 CPU 使用率过高](#high-client-cpu-usage)
- [客户端带宽限制](#client-side-bandwidth-limitation)
<!-- [Large request or response size](#large-request-or-response-size) -->

## <a name="memory-pressure-on-redis-client"></a>Redis 客户端上的内存压力

客户端计算机上出现的内存压力会导致各种性能问题，这些问题可能会延迟对缓存所发出的响应的处理。 出现内存压力时，系统可能会将数据分页到磁盘。 此分页错误  导致系统性能显著下降。

检测客户端上的内存压力：

- 监视计算机上的内存用量，确保所用内存未超过可用内存。
- 监视客户端的 `Page Faults/Sec` 性能计数器。 在正常运行期间，大多数系统会出现某些页面错误。 如果页面错误中存在与请求超时相关的峰值，可能表示出现了内存压力。

可通过多种方式缓解客户端上较高的内存压力：

- 深入分析内存使用模式，并减少客户端上的内存消耗量。
- 将客户端 VM 升级到可提供更多内存的更大大小。

## <a name="traffic-burst"></a>流量突增

流量激增时，如果 `ThreadPool` 设置不佳，则可能导致对 Redis 服务器已发送但尚未在客户端上使用的数据的处理出现延迟。

使用[示例 `ThreadPoolLogger`](https://github.com/JonCole/SampleCode/blob/master/ThreadPoolMonitor/ThreadPoolLogger.cs) 监视 `ThreadPool` 统计信息在不同时间的变化。 可以使用 StackExchange.Redis 发出的 `TimeoutException` 消息（如下所示）做进一步的调查：

```output
    System.TimeoutException: Timeout performing EVAL, inst: 8, mgr: Inactive, queue: 0, qu: 0, qs: 0, qc: 0, wr: 0, wq: 0, in: 64221, ar: 0,
    IOCP: (Busy=6,Free=999,Min=2,Max=1000), WORKER: (Busy=7,Free=8184,Min=2,Max=8191)
```

在上面的异常中，有几个需要注意的问题：

- 请注意，在 `IOCP` 部分和 `WORKER` 部分，`Busy` 值大于 `Min` 值。 这种差异意味着 `ThreadPool` 设置需要调整。
- 也可参看 `in: 64221`。 此值表示客户端的内核套接字层收到了 64,211 字节，但应用程序尚未读取这些字节。 这种差异通常意味着，应用程序（例如 StackExchange.Redis）从网络读取数据的速度没有服务器向你发送数据的速度快。

可以[配置 `ThreadPool` 设置](cache-management-faq.yml#important-details-about-threadpool-growth)，确保线程池在流量激增的情况下快速扩展。

## <a name="high-client-cpu-usage"></a>客户端 CPU 使用率过高

客户端 CPU 使用率偏高表示系统跟不上所要求执行的工作的进度。 即使缓存发送响应的速度很快，客户端也可能无法及时处理该响应。

使用 Azure 门户中提供的指标或者通过计算机上的性能计数器监视客户端的系统范围的 CPU 使用率。 请注意不要监视进程 CPU，因为即使单个进程的 CPU 使用率较低，但系统范围的 CPU 使用率也可能很高。 注意与超时相对应的 CPU 使用率峰值。 CPU 使用率较高可能还会导致 `TimeoutException` 错误消息中出现较大的 `in: XXX` 值，如[流量突增](#traffic-burst)部分所述。

> [!NOTE]
> StackExchange.Redis 1.1.603 及更高版本在 `TimeoutException` 错误消息中包括了 `local-cpu` 指标。 确保使用最新版本的 [StackExchange.Redis NuGet 包](https://www.nuget.org/packages/StackExchange.Redis/)。 我们会不断对代码中的 Bug 进行修正，以便更好地应对超时情况。因此，请务必使用最新的版本。
>

缓解客户端 CPU 使用率较高的问题：

- 调查出现 CPU 峰值的原因。
- 将客户端升级到可提供更多 CPU 容量的更大 VM 大小。

## <a name="client-side-bandwidth-limitation"></a>客户端带宽限制

不同的客户端计算机体系结构对于可提供的网络带宽存在不同的限制。 如果客户端超出可用带宽，则客户端的数据处理速度将赶不上服务器的数据发送速度。 这种情况下会导致超时。

使用 [示例 `BandwidthLogger`](https://github.com/JonCole/SampleCode/blob/master/BandWidthMonitor/BandwidthLogger.cs)监视带宽使用率在不同时间的变化。 在对权限有限制的某些环境（例如 Azure 网站）中，此代码可能无法成功运行。

若要缓解此问题，请减少网络带宽消耗，或者将客户端 VM 大小提高到可以提供更大网络容量的大小。

<!-- 
## Large request or response Size

A large request/response can cause timeouts. As an example, suppose your timeout value configured on your client is 1 second. Your application requests two keys (for example, 'A' and 'B') at the same time (using the same physical network connection). Most clients support request "pipelining", where both requests 'A' and 'B' are sent one after the other without waiting for their responses. The server sends the responses back in the same order. If response 'A' is large, it can eat up most of the timeout for later requests.

In the following example, request 'A' and 'B' are sent quickly to the server. The server starts sending responses 'A' and 'B' quickly. Because of data transfer times, response 'B' must wait behind response 'A' times out even though the server responded quickly.

```console
|-------- 1 Second Timeout (A)----------|
|-Request A-|
     |-------- 1 Second Timeout (B) ----------|
     |-Request B-|
            |- Read Response A --------|
                                       |- Read Response B-| (**TIMEOUT**)
```

This request/response is a difficult one to measure. You could instrument your client code to track large requests and responses.

Resolutions for large response sizes are varied but include:

1. Optimize your application for a large number of small values, rather than a few large values.
    - The preferred solution is to break up your data into related smaller values.
    - See the post [What is the ideal value size range for redis? Is 100 KB too large?](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ) for details on why smaller values are recommended.
1. Increase the size of your VM to get higher bandwidth capabilities
    - More bandwidth on your client or server VM may reduce data transfer times for larger responses.
    - Compare your current network usage on both machines to the limits of your current VM size. More bandwidth on only the server or only on the client may not be enough.
1. Increase the number of connection objects your application uses.
    - Use a round-robin approach to make requests over different connection objects.

 -->
## <a name="additional-information"></a>其他信息

- [排查 Azure Cache for Redis 服务器端问题](cache-troubleshoot-server.md)
- [如何制定基准和测试缓存的性能？](cache-management-faq.yml#how-can-i-benchmark-and-test-the-performance-of-my-cache-)
