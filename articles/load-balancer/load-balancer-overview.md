---
title: 什么是 Azure 负载均衡器？
titleSuffix: Azure Load Balancer
description: Azure 负载均衡器功能、体系结构和实现概述。 了解负载均衡器的工作原理，以及如何在云中使用它。
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: overview
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 1/25/2021
ms.author: allensu
ms.openlocfilehash: 3d3c1d9937382080ea5a735e3f67e9767919366f
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124740769"
---
# <a name="what-is-azure-load-balancer"></a>什么是 Azure 负载均衡器？

“负载均衡”是指在一组后端资源或服务器之间均衡地分配负载（传入网络流量）。 

Azure 负载均衡器在开放式系统互连 (OSI) 模型的第 4 层上运行。 它是客户端的单一联系点。 负载均衡器将抵达负载均衡器前端的入站流分配到后端池实例。 这些流取决于配置的负载均衡规则和运行状况探测。 后端池实例可以是 Azure 虚拟机，或虚拟机规模集中的实例。

**[公共负载均衡器](./components.md#frontend-ip-configurations)** 可以为虚拟网络中的虚拟机 (VM) 提供出站连接。 可以通过将专用 IP 地址转换为公共 IP 地址来实现这些连接。 公共负载均衡器用于对传入 VM 的 Internet 流量进行负载均衡。

**[内部（或专用）负载平衡器](./components.md#frontend-ip-configurations)** 用于仅在前端需要专用 IP 的情况。 内部负载均衡器用于对虚拟网络内部的流量进行负载均衡。 负载均衡器前端可以在混合方案中从本地网络进行访问。

<p align="center">
  <img src="./media/load-balancer-overview/load-balancer.svg" alt="Figure depicts both public and internal load balancers directing traffic to port 80 on multiple servers on a Web tier and port 443 on multiple servers on a business tier." width="512" title="Azure 负载均衡器">
</p>

图：*使用公共和内部负载均衡器对多层应用程序进行均衡*

有关各个负载均衡器组件的详细信息，请参阅 [Azure 负载均衡器组件](./components.md)。

>[!NOTE]
> Azure 为方案提供了一套完全托管的负载均衡解决方案。 
> * 若要进行基于 DNS 的全局路由，并且在传输层安全性 (TLS) 协议终止（“SSL 卸载”）、每 HTTP/HTTPS 请求或应用程序层处理方面没有要求，请查看[流量管理器](../traffic-manager/traffic-manager-overview.md)。 
> * 若要在应用程序层某个区域中的服务器之间实现负载均衡，请查看[应用程序网关](../application-gateway/overview.md)。
> * 如果需要优化 Web 流量的全局路由，并且需要通过快速全局故障转移来优化顶层最终用户的性能和可靠性，请参阅 [Front Door](../frontdoor/front-door-overview.md)。
> 
> 端到端场景可从结合所需的解决方案中受益。
> 有关 Azure 负载平衡选项的比较，请参阅 [Overview of load-balancing options in Azure](/azure/architecture/guide/technology-choices/load-balancing-overview)（Azure 中的负载平衡选项概述）。


## <a name="why-use-azure-load-balancer"></a>为什么使用 Azure 负载均衡器？
使用 Azure 负载均衡器可以缩放应用程序，并创建高度可用的服务。 负载均衡器支持入站和出站方案。 负载均衡器提供低延迟和高吞吐量，以及为所有 TCP 和 UDP 应用程序纵向扩展到数以百万计的流。

可以通过使用 Azure 标准负载均衡器来完成的关键方案包括：

- 对发往 Azure 虚拟机的 **[内部](./quickstart-load-balancer-standard-internal-portal.md)** 和 **[外部](./quickstart-load-balancer-standard-public-portal.md)** 流量进行负载均衡。

- 通过在区域 **[内部](./tutorial-load-balancer-standard-public-zonal-portal.md)** 和区域 **[之间](./quickstart-load-balancer-standard-public-portal.md)** 分配资源，提高可用性。

- 配置 Azure 虚拟机的 **[出站连接](./load-balancer-outbound-connections.md)** 。

- 使用 **[运行状况探测](./load-balancer-custom-probe-overview.md)** 来监视已实现负载均衡的资源。

- 使用 **[端口转发](./tutorial-load-balancer-port-forwarding-portal.md)** 通过公共 IP 地址和端口访问虚拟网络中的虚拟机。

- 启用对 **[IPv6](../virtual-network/ipv6-overview.md)** **[负载均衡](../virtual-network/virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md)** 的支持。

- 标准负载均衡器通过 [Azure Monitor](../azure-monitor/overview.md) 提供多维度指标。  可以就给定维度对这些指标进行筛选、分组和细分。  可便于深入了解服务的当前及历史性能和运行状况。 [Azure 负载均衡器见解](./load-balancer-insights.md)提供了一个预配置的仪表板，其中有用于呈现这些指标的实用可视化效果。  还支持资源运行状况。 有关更多详细信息，请查看[标准负载均衡器诊断](load-balancer-standard-diagnostics.md)。

- 对 **[多个端口和/或多个 IP 地址](./load-balancer-multivip-overview.md)** 上的服务进行负载均衡。

- 跨 Azure 区域移动 **[内部](./move-across-regions-internal-load-balancer-portal.md)** 和 **[外部](./move-across-regions-external-load-balancer-portal.md)** 负载均衡器资源。

- 使用 **[HA 端口](./load-balancer-ha-ports-overview.md)** ，同时对所有端口上的 TCP 和 UDP 流进行负载均衡。

### <a name="secure-by-default"></a><a name="securebydefault"></a>默认保护

* 标准负载均衡器是基于零信任网络安全模型构建的。

* 标准负载均衡器默认情况下为安全状态，并且是虚拟网络的一部分。 虚拟网络是一个专用的隔离网络。  

* 标准负载均衡器和标准公共 IP 地址对入站连接关闭，除非由网络安全组打开。 NSG 用于显式允许允许的流量。  如果虚拟机资源的子网或 NIC 上没有 NSG，则不允许流量访问此资源。 若要了解 NSG 以及如何将它们应用于你的方案，请参阅[网络安全组](../virtual-network/network-security-groups-overview.md)。

* 默认情况下，基本负载均衡器对 Internet 开放。 

* 负载均衡器不会存储客户数据。

## <a name="pricing-and-sla"></a>定价和 SLA

有关标准负载均衡器定价的信息，请参阅[负载均衡器定价](https://azure.microsoft.com/pricing/details/load-balancer/)。
基本负载均衡器是免费提供的。
请参阅[负载均衡器的 SLA](https://aka.ms/lbsla)。 基本负载均衡器没有 SLA。

## <a name="whats-new"></a>新增功能

订阅 RSS 源，并在 [Azure 更新](https://azure.microsoft.com/updates/?category=networking&query=load%20balancer)页上查看最新的 Azure 负载均衡器功能更新。

## <a name="next-steps"></a>后续步骤

请参阅[创建公共标准负载均衡器](quickstart-load-balancer-standard-public-portal.md)以开始使用负载均衡器。

若要详细了解 Azure 负载均衡器限制和组件，请参阅 [Azure 负载均衡器组件](./components.md)和 [Azure 负载均衡器概念](./concepts.md)