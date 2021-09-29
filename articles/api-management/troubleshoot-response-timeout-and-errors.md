---
title: 排查 API 管理的客户端响应超时和错误问题
description: 排查 API 管理中的间歇性连接错误和相关延迟问题
author: dlepow
ms.topic: troubleshooting
ms.date: 12/04/2020
ms.author: danlep
ms.service: api-management
ms.openlocfilehash: d7b6f8ed20f1af7b4a6f512b9aa4611936f784fd
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128623905"
---
# <a name="troubleshooting-client-response-timeouts-and-errors-with-api-management"></a>排查 API 管理的客户端响应超时和错误问题

本文帮助你排查 [Azure API 管理](./api-management-key-concepts.md)中的间歇性连接错误和相关延迟问题。 具体而言，本文将提供有关源地址网络转换 (SNAT) 端口耗尽的信息及其故障排除方法。 如果需要更多帮助，请联系 [Azure 社区支持](https://azure.microsoft.com/support/community/) 的 Azure 专家，或向 [Azure 支持](https://azure.microsoft.com/support/options/)提交支持请求。

## <a name="symptoms"></a>症状

通过 API 管理 (APIM) 服务调用 API 的客户端应用程序可能会出现以下一种或多种症状：

* 间歇性 HTTP 500 错误
* 超时错误消息

这些症状在 [Azure Monitor 资源日志](../azure-monitor/essentials/resource-logs.md)中作为 `BackendConnectionFailure` 的实例列出。

## <a name="cause"></a>原因

这种模式的症状通常是由 APIM 服务的网络地址转换 (SNAT) 端口限制导致的。

每当客户端调用 APIM API 之一时，Azure API 管理服务就会打开一个用于访问后端 API 的 SNAT 端口。 如 [Azure 中的出站连接](../load-balancer/load-balancer-outbound-connections.md)所述，Azure 使用源网络地址转换 (SNAT) 和负载均衡器（不向客户公开）与公共 IP 地址空间中 Azure 外部的终结点以及未使用[虚拟网络服务终结点](../virtual-network/virtual-network-service-endpoints-overview.md)的 Azure 内部终结点进行通信。 这种情况仅适用于在公共 IP 上公开的后端 API。

最初会为每个 API 管理服务实例提供预分配数量的 SNAT 端口。 该限制会影响与同一个主机/端口组合打开的连接数。 重复调用同一个包含地址和端口的组合时，会用尽 SNAT 端口。 释放某个 SNAT 端口以后，即可根据需要重复使用该端口。 只有在等待 4 分钟后，Azure 网络负载均衡器才会从关闭的连接回收 SNAT 端口。

如果这些端口的关闭和回收速度不够快，则快速连续地向 API 发出客户端请求可能会耗尽预先分配的 SNAT 端口配额，从而妨碍 APIM 服务及时处理客户端请求。

## <a name="mitigations-and-solutions"></a>缓解和解决方案

解决 SNAT 端口耗尽问题首先需要诊断和优化后端服务的性能。

Azure 负载均衡器文档中的[排查出站连接故障](../load-balancer/troubleshoot-outbound-connection.md)介绍了缓解 SNAT 端口耗尽问题的一般策略。 在这些策略中，以下各项适用于 API 管理。

### <a name="scale-your-apim-instance"></a>缩放你的 APIM 实例

系统会根据 APIM 单元数为每个 API 管理实例分配一定数量的 SNAT 端口。 你可以分配额外的 SNAT 端口，方法是使用额外的单元来扩展 API 管理实例。 有关详细信息，请参阅[缩放 API 管理服务](upgrade-and-scale.md#scale-your-api-management-service)

> [!NOTE]
> SNAT 端口使用情况目前不能作为自动缩放 API 管理单元的指标。

### <a name="use-multiple-ips-for-your-backend-urls"></a>为后端 URL 使用多个 IP

从 APIM 实例到后端服务的同一目标 IP 和目标端口的每个连接都会使用一个 SNAT 端口，以维持不同的通信流。 如果不对来自后台服务的返回流量使用不同的 SNAT 端口，APIM 会无法将一个响应与另一个响应分开。

由于在目标 IP 或目标端口不同的情况下可以重复使用 SNAT 端口，因此，要避免 SNAT 端口耗尽，另一种方法是为后端服务 URL 使用多个 IP。

有关详细信息，请参阅[出站代理 Azure 负载均衡器](../load-balancer/load-balancer-outbound-connections.md)。

### <a name="place-your-apim-and-backend-service-in-the-same-vnet"></a>将 APIM 和后端服务置于同一 VNet 中

如果后端 API 托管在支持服务终结点的 Azure 服务（如应用服务）上，则可以将 APIM 实例和后端服务置于同一虚拟网络中，并通过[服务终结点](../virtual-network/virtual-network-service-endpoints-overview.md)或[专用终结点](../private-link/private-endpoint-overview.md)公开，从而避免 SNAT 端口耗尽问题。 使用公用 VNet 并将服务终结点置于集成子网中时，从 APIM 实例到这些服务的出站流量会绕过 Internet，从而避免 SNAT 端口限制。 同样，如果你使用 VNet 和专用终结点，则向该目标发送流量的出站 SNAT 端口将不会有任何问题。

有关详细信息，请参阅[如何将 Azure API 管理与虚拟网络配合使用](api-management-using-with-vnet.md)和[将应用服务与 Azure 虚拟网络集成](../app-service/web-sites-integrate-with-vnet.md)。

### <a name="place-your-apim-in-a-virtual-network-and-route-outbound-calls-to-azure-firewall"></a>将 APIM 置于虚拟网络中，并将出站调用路由到 Azure 防火墙

与将 APIM 和后端服务置于虚拟网络中类似，你可以将 VNet 中的 Azure 防火墙与 APIM 服务配合使用，然后将出站 APIM 调用路由到 Azure 防火墙。 同一 VNet 中的 APIM 与 Azure 防火墙之间不需要 SNAT 端口。 对于到后端服务的 SNAT 连接，Azure 防火墙具有 64,000 个可用端口，比分配给 APIM 实例的数量高得多。

有关详细信息，请参阅 [Azure 防火墙](../firewall/overview.md)文档。

### <a name="consider-response-caching-and-other-backend-performance-tuning"></a>考虑响应缓存和其他后端性能优化

另一种可能的缓解措施是改进后端 API 的处理时间。 实现此目的的一种方法是为某些 API 配置响应缓存，以降低调用 API 的客户端应用程序与 APIM 后端负载之间的延迟。

有关详细信息，请参阅[添加缓存以提高 Azure API 管理中的性能](api-management-howto-cache.md)。

### <a name="consider-implementing-access-restriction-policies"></a>考虑实施访问限制策略

如果适合你的业务方案，你可以为 API 管理产品实施访问限制策略。 例如，可以使用 `rate-limit-by-key` 策略对每个指定时段内的调用速率进行限制，以避免单个密钥的 API 使用量暴增。

有关详细信息，请参阅 [API 管理访问限制策略](api-management-access-restriction-policies.md)。

## <a name="see-also"></a>请参阅

* [Azure 负载均衡器：排查出站连接故障](../load-balancer/troubleshoot-outbound-connection.md)
* [Azure 应用服务：排查间歇性出站连接错误](../app-service/troubleshoot-intermittent-outbound-connection-errors.md)
