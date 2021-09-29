---
title: 性能测试的最佳做法
titleSuffix: Azure Cache for Redis
description: 了解如何测试 Azure Cache for Redis 的性能。
author: shpathak-msft
ms.service: cache
ms.topic: conceptual
ms.date: 08/25/2021
ms.author: shpathak
ms.openlocfilehash: 748e7e09332e9feded9af0bc6cfa9a38d27086f5
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128615199"
---
# <a name="performance-testing"></a>性能测试

1. 在编写自己的性能测试之前，请先使用 `redis-benchmark.exe` 检查缓存的一般吞吐量和延迟特征。 有关详细信息，请参阅 [Redis 基准](#redis-benchmark-utility)。

1. 用于测试的客户端 VM 应与 Redis 缓存实例位于 *同一区域*。

1. 确保所用客户端 VM 至少与正在测试的缓存拥有相同的计算和带宽容量。

1. 请勿仅在稳定状态条件下对缓存进行性能测试。 还需要按照故障转移条件进行测试，并在测试期间测量缓存中的 CPU/服务器负载。 可以通过[重新启动主节点](cache-administration.md#reboot)来启动故障转移。 通过在故障转移条件下进行测试，可了解应用程序在故障转移条件下的吞吐量和延迟情况。 故障转移可能在更新期间或计划外事件期间发生。 理想情况下，即使是在故障转移期间，CPU/服务器负载峰值也应该不会很高（例如超过 80%），因为这可能会影响性能。

1. 请考虑使用高级层 Azure Cache for Redis 实例。 这些缓存大小具有更好的网络延迟和吞吐量，因为它们是在 CPU 和网络两方面都更好的硬件上运行的。

   > [!NOTE]
   > [此处发布](./cache-planning-faq.yml#azure-cache-for-redis-performance)了我们观测到的性能结果供你参考。 另请注意，SSL/TLS 会增大一些开销，因此，如果你使用传输加密，延迟和/或吞吐量可能会有变化。

## <a name="redis-benchmark-utility"></a>Redis 基准实用工具

你可[在此处](https://redis.io/topics/benchmarks)找到 Redis 基准文档。

`redis-benchmark.exe` 不支持 TLS。 运行测试前，必须[通过门户启用非 TLS 端口](cache-configure.md#access-ports)。 可在[此处](https://github.com/MSOpenTech/redis/releases)找到 Windows 兼容版本的 redis-benchmark.exe。

## <a name="redis-benchmark-examples"></a>Redis 基准示例

**测试前的设置**：准备好缓存实例以及测试延迟和吞吐量所需的数据：

```dos
redis-benchmark -h yourcache.redis.cache.windows.net -a yourAccesskey -t SET -n 10 -d 1024
```

**测试延迟**：使用 1k 有效负载测试 GET 请求：

```dos
redis-benchmark -h yourcache.redis.cache.windows.net -a yourAccesskey -t GET -d 1024 -P 50 -c 4
```

**测试吞吐量**：使用 1k 有效负载测试管道 GET 请求：

```dos
redis-benchmark -h yourcache.redis.cache.windows.net -a yourAccesskey -t  GET -n 1000000 -d 1024 -P 50  -c 50
```

## <a name="next-steps"></a>后续步骤

- [开发](cache-best-practices-development.md)
- [Azure Cache for Redis 开发的常见问题解答](cache-development-faq.yml)
- [Azure Cache for Redis 的故障转移和修补](cache-failover.md)