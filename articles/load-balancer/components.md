---
title: Azure 负载均衡器组件
description: Azure 负载均衡器组件概述
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/04/2020
ms.author: allensu
ms.openlocfilehash: a75ea7e9f3659b45e79748fdead7be9aa8ca4f94
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128649466"
---
# <a name="azure-load-balancer-components"></a>Azure 负载均衡器组件

Azure 负载均衡器包含几个关键组件。 可以通过以下方式在订阅中配置这些组件：

* Azure 门户
* Azure CLI
* Azure PowerShell
* 资源管理器模板

## <a name="frontend-ip-configuration"></a>前端 IP 配置 <a name = "frontend-ip-configurations"></a>

Azure 负载均衡器的 IP 地址。 这是客户端的联系点。 这些 IP 地址可以是：

- **公共 IP 地址**
- **专用 IP 地址**

IP 地址的性质决定了所创建的负载均衡器的类型。 选择“专用 IP 地址”将创建内部负载均衡器。 选择“公共 IP 地址”将创建公共负载均衡器。

|  | 公共负载均衡器  | 内部负载均衡器 |
| ---------- | ---------- | ---------- |
| 前端 IP 配置| 公共 IP 地址 | 专用 IP 地址|
| **说明** | 公共负载均衡器将传入流量的公共 IP 和端口映射到 VM 的专用 IP 和端口。 负载均衡器将来自 VM 的响应流量映射到另一个方向。 你可以通过应用负载均衡规则，在多个 VM 或服务之间分配特定类型的流量。 例如，可将 Web 请求流量负载分配到多个 Web 服务器。| 内部负载均衡器将流量分配给虚拟网络内的各个资源。 Azure 会限制对虚拟网络的负载均衡前端 IP 地址的访问。 前端 IP 地址和虚拟网络不会直接在 Internet 终结点上公开。 内部业务线应用程序可在 Azure 中运行，并可从 Azure 内或从本地资源访问这些应用程序。 |
| 支持的 SKU | 基本、标准 | 基本、标准 |

![分层的负载均衡器示例](./media/load-balancer-overview/load-balancer.png)

负载均衡器可有多个前端 IP。 详细了解[多个前端](load-balancer-multivip-overview.md)。

## <a name="backend-pool"></a>后端池

虚拟机规模集中用于处理传入请求的虚拟机组或实例组。 为了经济高效地扩展以满足大量传入流量，计算准则通常建议向后端池添加更多实例。

纵向扩展或缩减实例时，负载均衡器可即时通过自动重新配置来重新配置自身。 在后端池中添加或删除 VM 会重新配置负载均衡器，无需执行其他操作。 后端池的范围包括单个虚拟网络中的任何虚拟机。

考虑如何设计后端池时，请在设计时尽量减少后端池单个资源的数目，从而缩短管理操作的时长。 数据平面的性能或规模并无差异。

## <a name="health-probes"></a>运行状况探测

运行状况探测用于确定后端池中实例的运行状况。 在创建负载均衡器期间，请配置运行状况探测以供负载均衡器使用。  此运行状况探测将确定实例是否正常并可以接收流量。

可以定义运行状况探测的不正常阈值。 当探测无法响应时，负载均衡器会停止向状况不良的实例发送新连接。 探测失败不会影响现有连接。 连接将继续，直到应用程序：

- 结束流
- 出现空闲超时
- VM 关闭

负载均衡器为终结点提供了不同的运行状况探测类型：TCP、HTTP 和 HTTPS。 [详细了解负载均衡器运行状况探测](load-balancer-custom-probe-overview.md)。

基本负载均衡器不支持 HTTPS 探测。 基本负载均衡器会关闭所有 TCP 连接（包括已建立的连接）。

## <a name="load-balancing-rules"></a>负载均衡算法

负载均衡器规则用于定义将传入的流量分配至后端池中所有实例的方式。 负载均衡规则将给定的前端 IP 配置和端口映射到多个后端 IP 地址和端口。

例如，使用端口 80 的负载均衡规则将流量从前端 IP 路由到后端实例的端口 80。

:::image type="content" source="./media/load-balancer-components/lbrules.png" alt-text="负载均衡器规则参考关系图" border="false":::

图：负载均衡规则

## <a name="high-availability-ports"></a>高可用性端口

使用“协议 - 全部”和“端口 - 0”配置的负载均衡器规则。 

通过此规则，可以使用单条规则对到达内部标准负载均衡器的所有端口的所有 TCP 和 UDP 流进行负载均衡。 

按流进行负载均衡决策。 此操作基于以下五个元组连接： 

1. 源 IP 地址
2. 源端口
3. 目标 IP 地址
4. 目标端口
5. 协议

HA 端口负载均衡规则可帮助实现关键方案，如虚拟网络内部网络虚拟设备 (NVA) 的高可用性和缩放。 当大量端口必须进行负载均衡时，此功能可以帮助完成。

<p align="center">
  <img src="./media/load-balancer-components/harules.svg" alt="Figure depicts how Azure Load Balancer directs all frontend ports to three instances of all backend ports" width="512" title="HA 端口规则">
</p>

图：HA 端口规则

详细了解 [HA 端口](load-balancer-ha-ports-overview.md)。

## <a name="inbound-nat-rules"></a>入站 NAT 规则

入站 NAT 规则将转发发送到前端 IP 地址和端口组合的传入流量。 该流量将被转发到后端池中的特定虚拟机或实例。 可以通过与负载均衡相同的基于哈希的分配来实现此端口转发。

:::image type="content" source="./media/load-balancer-components/inboundnatrules.png" alt-text="入站 NAT 规则参考关系图" border="false":::

图：入站 NAT 规则

虚拟机规模集的上下文中的入站 NAT 规则是入站 NAT 池。 详细了解[负载均衡器组件和虚拟机规模集](../virtual-machine-scale-sets/virtual-machine-scale-sets-networking.md#azure-virtual-machine-scale-sets-with-azure-load-balancer)。

## <a name="outbound-rules"></a>出站规则

出站规则为后端池所标识的所有虚拟机或实例配置出站网络地址转换 (NAT)。 此规则使后端中的实例能够与 Internet 或其他终结点进行通信（出站）。

详细了解[出站连接和规则](load-balancer-outbound-connections.md)。

基本负载均衡器不支持出站规则。

:::image type="content" source="./media/load-balancer-components/outbound-rules.png" alt-text="出站规则参考关系图" border="false":::

图：出站规则

## <a name="limitations"></a>限制

- 了解负载均衡器[限制](../azure-resource-manager/management/azure-subscription-service-limits.md) 
- 负载均衡器针对特定的 TCP 或 UDP 协议提供了负载均衡和端口转发。 负载均衡规则和入站 NAT 规则支持 TCP 和 UDP，但不支持其他 IP 协议（包括 ICMP）。
- 从后端 VM 到内部负载均衡器前端的出站流将会失败。
- 负载均衡器规则不能跨越两个虚拟网络。 所有负载均衡器前端及其后端实例都必须位于单个虚拟网络中。  
- 负载均衡规则不支持转发 IP 片段。 负载均衡规则不支持 UDP 和 TCP 数据包的 IP 片段。 HA 端口负载均衡规则可用于转发现有 IP 片段。 有关详细信息，请参阅[高可用性端口概述](load-balancer-ha-ports-overview.md)。
- 每个可用性集只能有 1 个公共负载均衡器和 1 个内部负载均衡器

## <a name="next-steps"></a>后续步骤

- 请参阅[创建公共标准负载均衡器](quickstart-load-balancer-standard-public-portal.md)，开始使用负载均衡器。
- 详细了解 [Azure 负载均衡器](load-balancer-overview.md)。
- 了解[公共 IP 地址](../virtual-network/virtual-network-public-ip-address.md)
- 了解[专用 IP 地址](../virtual-network/private-ip-addresses.md)
- 了解如何使用[标准负载均衡器和可用性区域](load-balancer-standard-availability-zones.md)。
- 了解[标准负载均衡器诊断](load-balancer-standard-diagnostics.md)。
- 了解如何[在空闲时重置 TCP](load-balancer-tcp-reset.md)。
- 了解[具有 HA 端口负载均衡规则的标准负载均衡器](load-balancer-ha-ports-overview.md)。
- 详细了解[网络安全组](../virtual-network/network-security-groups-overview.md)。
- 详细了解[负载均衡器限制](../azure-resource-manager/management/azure-subscription-service-limits.md#load-balancer)。
- 了解如何使用[端口转发](./tutorial-load-balancer-port-forwarding-portal.md)。
