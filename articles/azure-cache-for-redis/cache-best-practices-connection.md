---
title: 有关连接复原的最佳做法
titleSuffix: Azure Cache for Redis
description: 了解如何使 Azure Cache for Redis 连接能够复原。
author: shpathak-msft
ms.service: cache
ms.topic: conceptual
ms.date: 10/11/2021
ms.author: shpathak
ms.openlocfilehash: 02b5c4bd42abc9c36ef971b053979d590d1e602d
ms.sourcegitcommit: 54e7b2e036f4732276adcace73e6261b02f96343
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/12/2021
ms.locfileid: "129808836"
---
# <a name="connection-resilience"></a>连接复原能力

## <a name="retry-commands"></a>重试命令

将客户端连接配置为使用指数退避重试命令。 有关详细信息，请参阅[重试指导原则](/azure/architecture/best-practices/retry-service-specific#azure-cache-for-redis)。

## <a name="test-resiliency"></a>测试复原

使用[重启](cache-administration.md#reboot)来模拟某个补丁，测试系统对连接中断的复原能力。 有关如何测试性能的详细信息，请参阅[性能测试](cache-best-practices-performance.md)。

## <a name="tcp-settings-for-linux-hosted-client-applications"></a>Linux 托管客户端应用程序的 TCP 设置

有些 Linux 版本默认使用乐观 TCP 设置。 TCP 设置可能会创建一种情况：Redis 服务器在正常关闭连接之前停止响应时，无法长时间重新建立与缓存的客户端连接。 如果 Azure Redis 缓存的主节点变得不可用（例如，用于计划外维护），则可能会发生重新建立连接失败的情况。

建议使用以下 TCP 设置：

|设置  |值 |
|---------|---------|
| net.ipv4.tcp_retries2   | 5 |
| TCP_KEEPIDLE   | 15 |
| TCP_KEEPINTVL  | 5 |
| TCP_KEEPCNT | 3 |

请考虑使用 ForceReconnect 模式。 有关模式的实现，请参阅[使用 Lazy\<T\> 模式重新连接](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-lazyreconnect-cs)中的代码。

有关此方案的详细信息，请参阅[在 Linux 上运行时，无法重新建立长达 15 分钟的连接](https://github.com/StackExchange/StackExchange.Redis/issues/1848#issuecomment-913064646)。 虽然此讨论与 StackExchange.Redis 库有关，但 Linux 上运行的其他客户端库也会受到影响。 该说明仍然有用，可以通用化到其他库。

## <a name="configure-appropriate-timeouts"></a>配置适当的超时

在连接复原能力方面必须考虑两个超时值：[connect timeout](#connect-timeout) 和 [command timeout](#command-timeout)。

### <a name="connect-timeout"></a>连接超时

`connect timeout` 是指客户端等待与 Redis 服务器建立连接的时间。 将客户端库配置为使用 5 秒的 `connect timeout`，这样，即使在 CPU 负载较高的情况下，系统也有足够的时间建立连接。

如果 `connection timeout` 值很小，则无法保证在该期限内建立连接。 如果出现问题（客户端 CPU 负载过高、服务器 CPU 负载过高，等等），则使用很短的 `connection timeout` 值会导致连接尝试失败。 此行为通常会使问题变得更糟。 使用较短的超时不仅无助于解决问题，而且会加剧问题，这会强制系统重启尝试重新连接的进程，从而可能导致出现“连接 -> 失败 -> 重试”循环。

### <a name="command-timeout"></a>命令超时

大多数客户端库为 `command timeouts`（客户端等待 Redis 服务器做出响应的时间）使用另一种超时配置。 尽管我们建议使用不到 5 秒的初始设置，但请根据你的方案和存储在缓存中的值的大小来调高或调低 `command timeout`。

如果 `command timeout` 太小，则连接可能不稳定。 但是，如果 `command timeout` 太大，则应用程序可能需要等待很长时间才能确定命令是否会超时。

## <a name="avoid-client-connection-spikes"></a>避免出现客户端连接高峰

在连接断开后重新连接时，避免同时创建多个连接。 与使用[很短的连接超时](#configure-appropriate-timeouts)可能导致长时间的服务中断一样，同时发起多个重新连接尝试也会增大服务器负载，并延长所有客户端成功重新连接所需的时间。

如果要重新连接许多客户端实例，请考虑错开新的连接，以避免连接的客户端数出现陡峰。

> [!NOTE]
> 使用 `StackExchange.Redis` 客户端库时，请在连接字符串中将 `abortConnect` 设置为 `false`。  建议让 `ConnectionMultiplexer` 处理重新连接。 有关详细信息，请参阅 [StackExchange.Redis 最佳做法](/azure/azure-cache-for-redis/cache-management-faq#stackexchangeredis-best-practices)。

## <a name="avoid-leftover-connections"></a>避免残余连接

缓存对每个缓存层的客户端连接数施加了限制。 确保客户端应用程序在重新创建连接时关闭并删除旧连接。

## <a name="advance-maintenance-notification"></a>提前发出维护通知

使用通知来通告即将进行维护。 有关详细信息，请参阅[我是否可以提前收到计划内维护的通知](cache-failover.md#can-i-be-notified-in-advance-of-planned-maintenance)。

## <a name="schedule-maintenance-window"></a>计划维护时段

调整缓存设置以顺应维护工作。 有关创建维护时段以降低对缓存造成的任何负面影响的信息，请参阅[计划更新](cache-administration.md#schedule-updates)。

## <a name="more-design-patterns-for-resilience"></a>可实现复原的其他设计模式

应用设计模式来实现复原。 有关详细信息，请参阅[如何使我的应用程序能够复原](cache-failover.md#how-do-i-make-my-application-resilient)。

## <a name="idle-timeout"></a>空闲超时

Azure Cache for Redis 目前的连接空闲超时为 10 分钟，因此客户端应用程序中的空闲超时设置应小于 10 分钟。 大多数常用客户端库都有一个配置设置，客户端库可通过此设置自动定期将 Redis `PING` 命令发送到 Redis 服务器。 但是，如果使用的客户端库没有这种类型的设置，则客户应用程序本身将负责保持连接的活动状态。

## <a name="next-steps"></a>后续步骤

- [开发的最佳做法](cache-best-practices-development.md)
- [Azure Cache for Redis 开发常见问题解答](cache-development-faq.yml)
- [故障转移和修补](cache-failover.md)
