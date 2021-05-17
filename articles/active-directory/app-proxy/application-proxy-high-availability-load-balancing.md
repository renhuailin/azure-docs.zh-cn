---
title: 高可用性和负载均衡 - Azure Active Directory 应用程序代理
description: 流量分布如何与应用程序代理部署搭配使用。 包括有关如何优化连接器性能和对后端服务器使用负载均衡的提示。
services: active-directory
author: kenwith
manager: mtillman
ms.service: active-directory
ms.subservice: app-proxy
ms.workload: identity
ms.topic: conceptual
ms.date: 04/29/2021
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: 8dbff6b53133bace6643b21c8faa706ef817f1de
ms.sourcegitcommit: fc9fd6e72297de6e87c9cf0d58edd632a8fb2552
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2021
ms.locfileid: "108286269"
---
# <a name="high-availability-and-load-balancing-of-your-application-proxy-connectors-and-applications"></a>应用程序代理连接器和应用程序的高可用性与负载均衡

本文介绍流量分布如何与应用程序代理部署搭配工作。 我们将讨论：

- 如何在用户和连接器之间分布流量，并提供用来优化连接器性能的提示

- 流量在连接器与后端应用服务器之间是如何流动的，并提供在多个后端服务器之间实现负载均衡的建议

## <a name="traffic-distribution-across-connectors"></a>跨连接器的流量分布

连接器基于高可用性原则建立连接。 不保证流量始终在连接器之间均匀分布，而且没有会话亲和性。 不过，使用情况不同，请求也随机发送到应用程序代理服务实例。 因此，流量通常在连接器之间几乎均匀分布。 以下关系图和步骤演示了如何在用户与连接器之间建立连接。

![显示用户与连接器之间的连接的关系图](media/application-proxy-high-availability-load-balancing/application-proxy-connections.png)

1. 客户端设备上的用户尝试访问通过应用程序代理发布的本地应用程序。
2. 该请求经由 Azure 负载均衡器来确定哪个应用程序代理服务实例应接受请求。 每个区域中有数十个实例可接受请求。 此方法有助于在服务实例之间均匀分布流量。
3. 请求被发送到[服务总线](../../service-bus-messaging/index.yml)。
4. 服务总线信号发送到可用连接器。 然后，连接器从服务总线提取请求。
   - 在第 2 步中，请求转到不同的应用程序代理服务实例，因此更有可能与不同的连接器建立连接。 因此，连接器在组内的使用几乎是均匀的。
5. 连接器将请求传递给应用程序的后端服务器。 然后，应用程序将响应发送回连接器。
6. 连接器通过打开与发送请求的服务实例的出站连接来完成响应。 随后，立即关闭此连接。 默认情况下，每个连接器的并发出站连接数限制为 200 个。
7. 然后，响应从服务实例传递回客户端。
8. 来自相同连接的后续请求重复上述步骤。

一个应用程序通常有多个资源，并在加载时打开多个连接。 每个连接都通过上述步骤分配给服务实例；如果连接之前尚未与连接器配对，则选择一个新的可用连接器。


## <a name="best-practices-for-high-availability-of-connectors"></a>可实现连接器高可用性的最佳做法

- 鉴于在连接器之间分配流量来实现高可用性的方式，一个连接器组中必须始终至少要有两个连接器。 最好使用 3 个连接器，以在连接器之间提供额外的缓冲区。 若要确定所需连接器的正确数量，请遵循容量计划文档。

- 为避免单一故障点，请将连接器放在不同的出站连接上。 如果连接器使用同一个出站连接，则连接的网络问题可能会影响使用该连接的所有连接器。

- 不要在连接到生产应用程序时强制连接器重启。 这样做可能会对跨连接器分布流量产生负面影响。 重启连接器会导致更多连接器不可用，并强制连接到剩余的可用连接器。 这会导致一开始对连接器的使用就不均衡。

- 不要对连接器与 Azure 之间的出站 TLS 通信进行任何形式的内联检查。 这种内联检查会导致通信流降级。

- 确保对连接器运行自动更新。 如果应用程序代理连接器更新服务在运行，那么连接器会自动更新并接收最新升级。 如果在服务器上未看到连接器更新程序服务，需要重新安装连接器才能获得所有更新。

## <a name="traffic-flow-between-connectors-and-back-end-application-servers"></a>连接器与后端应用程序服务器之间的流量流

高可用性的另一个关键方面是连接器与后端服务器之间的连接。 通过 Azure AD 应用程序代理发布应用程序时，从用户发往应用程序的流量将流经 3 个跃点：

1. 用户连接到 Azure 上的 Azure AD 应用程序代理服务公共终结点。 该连接是在客户端的起始客户端 IP 地址（公共）和应用程序代理终结点的 IP 地址之间建立的。
2. 应用程序代理连接器从应用程序代理服务中提取客户端的 HTTP 请求。
3. 应用程序代理连接器连接到目标应用程序。 连接器使用其自己的 IP 地址来建立连接。

![显示用户通过应用程序代理连接到应用程序的关系图](media/application-proxy-high-availability-load-balancing/application-proxy-three-hops.png)

### <a name="x-forwarded-for-header-field-considerations"></a>X-Forwarded-For 标头字段注意事项
在某些情况下（例如审核、负载均衡等），需要与本地环境共享外部客户端的起始 IP 地址。 为了满足此要求，Azure AD 应用程序代理连接器将具有起始客户端 IP 地址（公共）的 X-Forwarded-For 标头字段添加到 HTTP 请求。 适当的网络设备（负载均衡器、防火墙）或者 Web 服务器/后端应用程序可读取和使用该信息。

## <a name="best-practices-for-load-balancing-among-multiple-app-servers"></a>可在多个应用服务器之间实现负载均衡的最佳做法
如果分配给应用程序代理应用程序的连接器组包含两个或更多连接器，并且你在多个服务器（服务器场）上运行后端 Web 应用程序，那么你需要良好的负载均衡策略。 一个好的策略可确保服务器平均获取客户端请求，并防止服务器场中服务器的过度使用或使用不足。
### <a name="scenario-1-back-end-application-does-not-require-session-persistence"></a>方案 1：后端应用程序不需要会话持续性
最简单的方案是后端 Web 应用程序不需要会话粘性（会话持续性）。 来自用户的任何请求都可由服务器场中的任何后端应用程序实例处理。 你可使用 4 层负载均衡器，并将其配置为无相关性。 适合的一些选项包括 Microsoft 网络负载均衡和 Azure 负载均衡器，或者来自其他供应商的负载均衡器。 或者，可配置轮询 DNS。
### <a name="scenario-2-back-end-application-requires-session-persistence"></a>方案 2：后端应用程序要求具备会话持续性
在此方案中，后端 Web 应用程序要求在经过身份验证的会话期间具有会话粘性（会话持续性）。 来自用户的所有请求必须由在服务器场中的同一服务器上运行的后端应用程序实例处理。
此方案可能更复杂，因为客户端通常与应用程序代理服务建立多个连接。 不同连接的请求可能到达服务器场中不同的连接器和服务器。 由于每个连接器使用其自己的 IP 地址进行此通信，因此负载均衡器无法基于连接器的 IP 地址来确保会话粘性。 源 IP 相关性也不能使用。
下面是适合方案 2 的一些选项：

- 选项 1：基于负载均衡器设置的会话 Cookie 实现会话持续性。 建议使用此选项，因为这样就可在后端服务器之间更均匀地分布负载。 它需要具有此功能并可处理 HTTP 流量和终止 TLS 连接的 7 层负载均衡器。 可使用 Azure 应用程序网关（会话亲和性）或者来自其他供应商的负载均衡器。

- 选项 2：基于 X-Forwarded-For 标头字段实现会话持续性。 此选项需要具有此功能并可处理 HTTP 流量和终止 TLS 连接的 7 层负载均衡器。  

- 选项 3：将后端应用程序配置为不需要会话持续性。

请查看软件供应商的相应文档，了解后端应用程序的负载均衡要求。

## <a name="next-steps"></a>后续步骤

- [启用应用程序代理](application-proxy-add-on-premises-application.md)
- [启用单一登录](application-proxy-configure-single-sign-on-with-kcd.md)
- [启用条件访问](../manage-apps/application-proxy-integrate-with-sharepoint-server.md)
- [解决使用应用程序代理时遇到的问题](application-proxy-troubleshoot.md)
- [了解 Azure AD 体系结构如何支持高可用性](../fundamentals/active-directory-architecture.md)