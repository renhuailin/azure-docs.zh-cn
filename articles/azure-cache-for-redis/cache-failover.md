---
title: 故障转移和修补 - Azure Cache for Redis
description: 了解 Azure Cache for Redis 的故障转移、修补和更新过程。
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 10/18/2019
ms.openlocfilehash: 69ddda7bd88218a3667b16bfdc9fa33aa5349ff6
ms.sourcegitcommit: ca38027e8298c824e624e710e82f7b16f5885951
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/24/2021
ms.locfileid: "112573933"
---
# <a name="failover-and-patching-for-azure-cache-for-redis"></a>Azure Cache for Redis 的故障转移和修补

要构建可复原且成功的客户端应用程序，了解 Azure Cache for Redis 服务中的故障转移至关重要。 故障转移可能是计划的管理操作中的一部分，也可能由意外硬件或网络故障引发。 当管理服务修补 Azure Cache for Redis 二进制文件时，往往会使用缓存故障转移。

在本文中，你将找到以下信息：  

- 什么是故障转移？
- 在修补期间如何进行故障转移。
- 如何构建可复原的客户端应用程序。

## <a name="what-is-a-failover"></a>什么是故障转移？

让我们从 Azure Cache for Redis 故障转移的概述开始。

### <a name="a-quick-summary-of-cache-architecture"></a>缓存体系结构的快速摘要

缓存是由多个具有不同专用 IP 地址的虚拟机构造而成的。 每个虚拟机（也称为节点）通过单个虚拟 IP 地址连接到一个共享的负载均衡器。 每个节点运行 Redis 服务器进程，可通过主机名和 Redis 端口进行访问。 每个节点被视为主节点或副本节点。 当客户端应用程序连接到缓存时，其流量将流经此负载均衡器，并自动路由到主节点。

在基本缓存中，单一节点始终为主节点。 标准或高级缓存中有两个节点，其中一个为主节点，另一个为副本节点。 由于标准缓存和高级缓存有多个节点，其中一个节点可处于不可用状态，而其他节点可继续处理请求。 群集缓存由许多分片组成，每个分片具有不同的主节点和副本节点。 一个分片可处于关闭状态，其他节点仍保持可用。

> [!NOTE]
> 基本缓存没有多个节点，并且不提供可用性方面的服务级别协议 (SLA)。 基本缓存仅建议用于开发和测试目的。 对多节点部署使用标准或高级缓存可提高可用性。

### <a name="explanation-of-a-failover"></a>故障转移的说明

当某个副本节点将其自身提升为主节点，且旧主节点关闭现有连接时，将发生故障转移。 主节点重新启动后，它会注意到角色的变化，从而将自身降级为副本节点。 然后，它将连接到新的主节点并同步数据。 故障转移可以是计划性的，也可以是非计划的。

*计划的故障转移* 发生在两个不同的时间：

- 系统更新，例如 Redis 修补或 OS 升级。  
- 管理操作，例如缩放和重新启动。

由于节点会提前收到更新通知，因此它们可以协作交换角色，并在更改后快速更新负载均衡器。 计划性故障转移通常可在 1 秒内完成。

发生非计划性故障转移的可能原因是硬件故障、网络故障或主节点的其他意外中断。 副本节点可将自身提升为主节点，但该过程需要更长时间。 副本节点必须先检测到其主节点不可用，然后才能启动故障转移过程。 副本节点还必须验证此非计划性故障不是暂时性的或局部性的，以避免不必要的故障转移。 检测时出现的这种延迟意味着非计划性故障转移通常要在 10 到 15 秒内完成。

## <a name="how-does-patching-occur"></a>修补是如何进行的？

Azure Cache for Redis 服务定期使用最新的平台功能和修补程序更新缓存。 该服务遵循以下步骤来修补缓存：

1. 管理服务选择一个要修补的节点。
1. 如果所选的节点是主节点，则相应的副本节点将以协作方式提升自身。 这种升级被视为计划性故障转移。
1. 所选节点将重新启动以获取新的更改，然后以副本节点的角色重新启动。
1. 副本节点连接到主节点并同步数据。
1. 数据同步完成后，将对剩余的节点重复修补过程。

因为修补属于计划性故障转移，所以副本节点会快速将自身提升为主节点。 然后，节点开始为请求和新连接提供服务。 基本缓存没有副本节点，在更新完成之前不可用。 群集缓存的每个分片单独进行修补，不会关闭与另一个分片的连接。

> [!IMPORTANT]
> 每次修补一个节点以防数据丢失。 基本缓存会发生数据丢失。 每次修补缓存群集的一个分片。

同一资源组和区域中的多个缓存也是每次修补一个。  不同资源组或区域中的缓存可以同时修补。

由于完全数据同步在该过程重复之前发生，因此，在使用标准或高级缓存时不太可能发生数据丢失。 可以使用[导出](cache-how-to-import-export-data.md#export)数据功能并启用[持久性](cache-how-to-premium-persistence.md)来进一步防止数据丢失。

## <a name="additional-cache-load"></a>额外的缓存负载

每当发生故障转移时，标准和高级缓存需要将数据从一个节点复制到另一个节点。 这种复制会导致负载消耗的服务器内存和 CPU 增大。 如果缓存实例的负载已很繁重，客户端应用程序遇到的延迟可能会增大。 在极端情况下，客户端应用程序可能会收到超时异常。 为了帮助减轻负载增加的影响，请[配置](cache-configure.md#memory-policies)缓存的 `maxmemory-reserved` 设置。

## <a name="how-does-a-failover-affect-my-client-application"></a>故障转移如何影响我的客户端应用程序？

客户端应用程序遇到的错误数目取决于故障转移时该连接上挂起的操作数目。 通过关闭连接的节点路由的任何连接将遇到错误。 在连接中断时，许多客户端库可能会引发不同类型的错误，包括超时异常、连接异常或套接字异常。 异常的数目和类型取决于当缓存关闭其连接时，请求在代码路径中所处的位置。 例如，在发生故障转移时发送了请求但未收到响应的操作可能会收到超时异常。 对关闭的连接对象发出的新请求将收到连接异常，直到重新连接成功为止。

大多数客户端库会尝试重新连接到缓存（如果采用此配置）。 但是，不可预测的 bug 偶尔会将库对象置于不可恢复状态。 如果出错的持续时间超过了预先配置的时间，则应重新创建连接对象。 在 Microsoft.NET 和其他面向对象的语言中，可以使用 [Lazy\<T\> 模式](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#reconnecting-with-lazyt-pattern)来重新创建连接，而无需重启应用程序。

### <a name="can-i-be-notified-in-advance-of-a-planned-maintenance"></a>能否提前得到计划内维护的通知？

Azure Cache for Redis 现在会在计划内更新前约 30 秒在名为 [AzureRedisEvents](https://github.com/Azure/AzureCacheForRedis/blob/main/AzureRedisEvents.md) 的发布/订阅通道上发布通知。 通知是运行时通知。 这些通知专门为可使用断路器绕过缓存或缓冲区命令的应用程序而生成，例如，在计划内更新期间。 该机制不能提前几天或几小时通知你。

### <a name="client-network-configuration-changes"></a>客户端网络配置更改

某些客户端网络配置更改可能会触发“无可用连接”错误。 此类更改可能包括：

- 在过渡槽与生产槽之间交换客户端应用程序的虚拟 IP 地址。
- 缩放应用程序实例的大小或数量。

此类更改可能会导致持续一分钟以下的连接问题。 客户端应用程序可能不仅会断开与其他外部网络资源的连接，而且还会断开与 Azure Cache for Redis 服务的连接。

## <a name="build-in-resiliency"></a>内置的复原能力

无法完全避免故障转移。 应该合理编写客户端应用程序，使之能够弹性应对连接中断和请求失败的问题。 大多数客户端库可以自动重新连接到缓存终结点，但有少量的客户端库会重试失败的请求。 根据具体的应用方案，使用支持退让的重试逻辑可能有作用。

### <a name="how-do-i-make-my-application-resilient"></a>如何使应用程序能够复原？

请参考这些设计模式来构建可复原的客户端，特别是断路器和重试模式：

- [可靠性模式 - 云设计模式](/azure/architecture/framework/resiliency/reliability-patterns#resiliency)
- [Azure 服务的重试指南 - 云应用程序最佳做法](/azure/architecture/best-practices/retry-service-specific)
- [实现使用指数退避算法的重试](/dotnet/architecture/microservices/implement-resilient-applications/implement-retries-exponential-backoff)

若要测试客户端应用程序的复原能力，请使用[重新启动](cache-administration.md#reboot)作为连接中断时的手动触发器。

此外，我们建议对缓存[计划更新](cache-administration.md#schedule-updates)，以便在每周的特定时段应用 Redis 运行时补丁。 通常，这些时段是客户端应用程序流量较低的时段，目的是避免潜在的事件。

## <a name="next-steps"></a>后续步骤

- 为缓存[计划更新](cache-administration.md#schedule-updates)。
- 使用[重新启动](cache-administration.md#reboot)测试应用程序复原能力。
- [配置](cache-configure.md#memory-policies)内存预留和策略。
