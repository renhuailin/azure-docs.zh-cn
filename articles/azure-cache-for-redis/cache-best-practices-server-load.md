---
title: 有关使用和监视 Azure Cache for Redis 服务器负载的最佳做法
titleSuffix: Azure Cache for Redis
description: 了解如何使用和监视 Azure Cache for Redis 的服务器负载。
author: shpathak-msft
ms.service: cache
ms.topic: conceptual
ms.date: 08/25/2021
ms.author: shpathak
ms.openlocfilehash: ceeff68f65ace76f4fe9060edeb775085508c969
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123113173"
---
# <a name="manage-server-load-for-azure-cache-for-redis"></a>管理 Azure Cache for Redis 的服务器负载

## <a name="value-sizes"></a>值大小

客户端应用程序的设计决定了是要存储大量的较小值，还是存储少量的较大值。 从 Redis 服务器的角度来看，值越小，性能就越好。 建议将值大小保留在 100 kB 以下。

如果你的设计要求在 Azure Cache for Redis 中存储更大的值，则服务器负载会变得更高。 在这种情况下，可能需要使用更高的缓存层来确保 CPU 使用率不会限制吞吐量。

即使缓存具有足够的 CPU 容量，较大的值也会增大延迟，因此请遵循[配置适当的超时](cache-best-practices-connection.md#configure-appropriate-timeouts)中的指导。

较大值还会增大出现内存碎片的可能性，因此请务必遵循[配置maxmemory-reserved 设置](cache-best-practices-memory-management.md#configure-your-maxmemory-reserved-setting)中的指导。

## <a name="avoid-client-connection-spikes"></a>避免出现客户端连接高峰

创建和关闭连接对于 Redis 服务器而言是一项高开销的操作。 如果客户端应用程序在短时间内创建或关闭过多的连接，可能会给 Redis 服务器造成负担。

如果你要实例化许多客户端实例以便立即连接到 Redis，请考虑错开创建新连接的操作，以避免连接的客户端数出现徒峰。

## <a name="memory-pressure"></a>内存压力

如果服务器上的内存使用率过高，则系统更可能需要将数据分页到磁盘，从而导致页面错误，使系统运行速度明显变慢。

## <a name="avoid-long-running-commands"></a>避免长时间运行的命令

Redis 服务器是单线程系统。 长时间运行的命令可能导致客户端出现延迟或超时，因为服务器在忙于处理长时间运行的命令时无法响应任何其他请求。 有关详细信息，请参阅[排查 Azure Cache for Redis 服务器端问题](cache-troubleshoot-server.md)。  

## <a name="monitor-server-load"></a>监视服务器负载

添加对服务器负载的监视，确保在服务器负载过高时收到通知。 监视可帮助你了解应用程序约束。 然后，可以主动采取措施来缓解问题。 建议尝试使服务器负载保持在 80% 以下，以避免对性能造成负面影响。

## <a name="plan-for-server-maintenance"></a>规划服务器维护

确保在缓存服务器接受维护时，你有足够的服务器容量用于处理高峰负载。 在承受高峰负载的情况下，通过重新启动节点来测试系统。 有关如何模拟补丁部署的详细信息，请参阅[重新启动](cache-administration.md#reboot)。

## <a name="next-steps"></a>后续步骤

- [排查 Azure Cache for Redis 服务器端问题](cache-troubleshoot-server.md)
- [连接复原能力](cache-best-practices-connection.md)
  - [配置适当的超时](cache-best-practices-connection.md#configure-appropriate-timeouts)。
- [内存管理](cache-best-practices-memory-management.md)
  - [配置 maxmemory-reserved 设置](cache-best-practices-memory-management.md#configure-your-maxmemory-reserved-setting)
