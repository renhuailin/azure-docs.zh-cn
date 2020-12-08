---
title: 排查客户端响应超时和 API 管理错误
description: 排查 API 管理中的间歇性连接错误和相关延迟问题
author: vladvino
ms.topic: troubleshooting
ms.date: 12/04/2020
ms.author: apimpm
ms.service: api-management
ms.openlocfilehash: 770a8191b1b07a7ebc779b84f443ae96d66d1c97
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/07/2020
ms.locfileid: "96841379"
---
# <a name="troubleshooting-client-response-timeouts-and-errors-with-api-management"></a>排查客户端响应超时和 API 管理错误

本文将帮助你排查 [AZURE API 管理](./api-management-key-concepts.md)中的间歇性连接错误和相关延迟问题。 具体而言，本文将提供有关源地址网络转换 (SNAT) 端口的耗尽的信息和故障排除。 如果需要更多帮助，请联系 azure [社区支持](https://azure.microsoft.com/support/community/) 部门的 azure 专家，或向 [azure 支持](https://azure.microsoft.com/support/options/)人员提供支持请求。

## <a name="symptoms"></a>症状

通过 API 管理 (APIM) 服务调用 Api 的客户端应用程序可能会出现以下一种或多种症状：

* 间歇性 HTTP 500 错误
* 超时错误消息

这些症状清单作为 `BackendConnectionFailure` [Azure Monitor 资源日志](../azure-monitor/platform/resource-logs.md)中的实例。

## <a name="cause"></a>原因

这种症状模式通常是由于网络地址转换 (SNAT) 端口限制与你的 APIM 服务有关。

每当客户端调用一个 APIM Api 时，Azure API 管理服务就会打开一个 SNAT 端口来访问后端 API。 如 Azure 中的 [出站连接](../load-balancer/load-balancer-outbound-connections.md)中所述，azure 使用源网络地址转换 (SNAT) 和负载均衡器 (不向客户公开，) 在公共 IP 地址空间中与 azure 外部的终结点进行通信，以及不使用 [虚拟网络服务终结点](../virtual-network/virtual-network-service-endpoints-overview.md)的 azure 内部的终结点。 这种情况仅适用于公开在公共 Ip 上的后端 Api。

最初为 API 管理服务的每个实例提供预分配的 SNAT 端口数。 该限制会影响与同一个主机/端口组合打开的连接数。 当你对相同地址和端口组合进行重复调用时，将使用 SNAT 端口。 在 SNAT 端口释放后，可以根据需要使用该端口。 仅在等待四分钟后，Azure 网络负载均衡器才会从关闭的连接回收 SNAT 端口。

如果这些端口未关闭并迅速回收，则快速向 Api 发出的客户端请求可能会耗尽 SNAT 端口的预分配配额，这会阻止 APIM 服务及时处理客户端请求。

## <a name="mitigations-and-solutions"></a>缓解和解决方案

解决 SNAT 端口耗尽问题首先需要诊断和优化后端服务的性能。

排查来自 *Azure 负载均衡器* 的 [出站连接故障](../load-balancer/troubleshoot-outbound-connection.md)文档中讨论了用于缓解 SNAT 端口耗尽的常规策略。 在这些策略中，以下各项适用于 API 管理。

### <a name="scale-your-apim-instance"></a>缩放你的 APIM 实例

每个 API 管理实例都是根据 APIM 单位分配的多个 SNAT 端口。 可以通过使用其他单位来扩展 API 管理实例来分配其他 SNAT 端口。 有关详细信息，请参阅 [缩放 API 管理服务](upgrade-and-scale.md#scale-your-api-management-service)

> [!NOTE]
> SNAT 端口使用情况当前不可用于自动缩放 API 管理单元的指标。

### <a name="use-multiple-ips-for-your-backend-urls"></a>将多个 Ip 用于后端 Url

APIM 实例与后端服务的相同目标 IP 和目标端口的每个连接都将使用 SNAT 端口，以便维护不同的流量流。 如果不使用不同的 SNAT 端口来从后台服务返回流量，APIM 无法将一个响应与另一个响应分开。

由于在目标 IP 或目标端口不同的情况下可以重复使用 SNAT 端口，因此，要避免 SNAT 端口耗尽，另一种方法是将多个 Ip 用于后端服务 Url。

有关详细信息，请参阅 [出站代理 Azure 负载均衡器](../load-balancer/load-balancer-outbound-connections.md)。

### <a name="place-your-apim-and-backend-service-in-the-same-vnet"></a>将 APIM 和后端服务放在同一 VNet 中

如果后端 API 托管在支持 *服务终结点* （如应用服务）的 Azure 服务上，则可以通过将 APIM 实例和后端服务放在同一虚拟网络中，并通过 [服务终结点](../virtual-network/virtual-network-service-endpoints-overview.md) 或 [专用终结点](../private-link/private-endpoint-overview.md)来公开它，来避免 SNAT 端口耗尽问题。 使用公用 VNet 并将服务终结点置于集成子网中时，从 APIM 实例到这些服务的出站流量将绕过 internet，从而避免 SNAT 端口限制。 同样，如果你使用 VNet 和专用终结点，则不会向该目标发送任何出站 SNAT 端口问题。

有关详细信息，请参阅[如何在虚拟网络中使用 AZURE API 管理和将](api-management-using-with-vnet.md)[应用服务与 Azure 虚拟网络集成](../app-service/web-sites-integrate-with-vnet.md)。

### <a name="place-your-apim-in-a-virtual-network-and-route-outbound-calls-to-azure-firewall"></a>将 APIM 放在虚拟网络中，并将出站调用路由到 Azure 防火墙

类似于将 APIM 和后端服务放在虚拟网络中，可以使用 APIM 服务在 VNet 中使用 Azure 防火墙，然后将出站 APIM 调用路由到 Azure 防火墙。 在 APIM 与 Azure 防火墙之间 () 同一 VNet 中，不需要 SNAT 端口。 对于与后端服务的 SNAT 连接，Azure 防火墙具有64000个可用端口，比分配给 APIM 实例的容量高得多。

有关详细信息，请参阅 [Azure 防火墙](../firewall/overview.md) 文档。

### <a name="consider-response-caching-and-other-backend-performance-tuning"></a>考虑响应缓存和其他后端性能优化

另一种可能的缓解措施是改进后端 Api 的处理时间。 实现此目的的一种方法是通过将某些 Api 配置为使用响应缓存来减少调用 API 的客户端应用程序和 APIM 后端负载之间的延迟。

有关详细信息，请参阅 [添加缓存以提高 AZURE API 管理中的性能](api-management-howto-cache.md)。

### <a name="consider-implementing-access-restriction-policies"></a>考虑实施访问限制策略

如果适合你的业务方案，则可以为 API 管理产品实现访问限制策略。 例如， `rate-limit-by-key` 可以通过限制每个指定时间段的调用速率，使用策略来防止每个密钥出现 API 使用高峰。

有关详细信息，请参阅 [API 管理访问限制策略](api-management-access-restriction-policies.md) 。

## <a name="see-also"></a>请参阅

* [Azure 负载均衡器：对出站连接故障进行故障排除](../load-balancer/troubleshoot-outbound-connection.md)
* [Azure App Service：排查间歇出站连接错误](../app-service/troubleshoot-intermittent-outbound-connection-errors.md)
