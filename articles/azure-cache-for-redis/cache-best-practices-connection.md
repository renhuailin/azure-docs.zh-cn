---
title: 有关连接复原的最佳做法
titleSuffix: Azure Cache for Redis
description: 了解如何使 Azure Cache for Redis 连接能够复原。
author: shpathak-msft
ms.service: cache
ms.topic: conceptual
ms.date: 08/25/2021
ms.author: shpathak
ms.openlocfilehash: a0dd6e3e8f4c2a7645da1ceccf77f7607d2b84b3
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128656942"
---
# <a name="connection-resilience"></a>连接复原能力

## <a name="retry-commands"></a>重试命令

将客户端连接配置为使用指数退避重试命令。 有关详细信息，请参阅[重试指导原则](/azure/architecture/best-practices/retry-service-specific#azure-cache-for-redis)。

## <a name="test-resiliency"></a>测试复原

使用[重启](cache-administration.md#reboot)来模拟某个补丁，测试系统对连接中断的复原能力。 有关如何测试性能的详细信息，请参阅[性能测试](cache-best-practices-performance.md)。

## <a name="configure-appropriate-timeouts"></a>配置适当的超时

在连接复原能力方面必须考虑两个超时值：[连接超时](#connect-timeout)和[命令超时](#command-timeout)。

### <a name="connect-timeout"></a>连接超时

`connect timeout` 是指客户端等待与 Redis 服务器建立连接的时间。 将客户端库配置为使用 5 秒的 `connect timeout`，以便即使在 CPU 负载较高的情况下，系统也有足够的时间建立连接。

如果 `connection timeout` 值很小，则无法保证在该期限内建立连接。 如果出现问题（客户端 CPU 负载过高、服务器 CPU 负载过高等），则使用很短的 `connection timeout` 值会导致连接尝试失败。 此行为通常会使问题变得更糟。 使用较短的超时不仅无助于解决问题，而且会加剧问题，这会强制系统重启尝试重新连接的进程，从而可能导致出现“连接 -> 失败 -> 重试”循环。

### <a name="command-timeout"></a>命令超时

大多数客户端库为 `command timeouts`（客户端等待 Redis 服务器做出响应的时间）使用另一种超时配置。 尽管我们建议在不到 5 秒的时间内进行初始设置，但请考虑根据你的方案和存储在缓存中的值的大小来设置 `command timeout` 较高或较低的值。

如果 `command timeout` 太小，则连接可能不稳定。 但是，如果 `command timeout` 太大，则应用程序可能需要等待很长时间才能确定命令是否将要超时。

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
