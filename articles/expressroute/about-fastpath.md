---
title: 关于 ExpressRoute FastPath
description: 了解如何通过 Azure ExpressRoute FastPath 绕过网关来发送网络流量
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 08/10/2021
ms.author: duau
ms.custom: references_regions
ms.openlocfilehash: b6483ef0e034f695da1e03475ca2a4b716773684
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121749639"
---
# <a name="about-expressroute-fastpath"></a>关于 ExpressRoute FastPath

ExpressRoute 虚拟网络网关旨在交换网络路由和路由网络流量。 FastPath 旨在改善本地网络和虚拟网络之间的数据路径性能。 启用后，FastPath 会绕过网关直接将网络流量发送到虚拟网络中的虚拟机。

## <a name="requirements"></a>要求

### <a name="circuits"></a>线路

FastPath 在所有 ExpressRoute 线路上都可用。

### <a name="gateways"></a>网关

FastPath 仍要求创建一个虚拟网络网关，以在虚拟网络和本地网络之间交换路由。 有关虚拟网络网关和 ExpressRoute 的更多信息（包括性能信息和网关 SKU），请参阅 [ExpressRoute 虚拟网络网关](expressroute-about-virtual-network-gateways.md)。

要配置 FastPath，虚拟网络网关必须满足以下条件之一：

* 超高性能
* ErGw3AZ

> [!IMPORTANT]
> 如果计划将 FastPath 与基于 IPv6 的专用对等互联（通过 ExpressRoute）配合使用，请确保为 SKU 选择 ErGw3AZ。 请注意，这仅适用于使用 ExpressRoute 直通的线路。
> 
>

## <a name="limitations"></a>限制

尽管 FastPath 支持大多数配置，但不支持以下功能：

* 网关子网中的 UDR：此 UDR 不会影响 FastPath 从本地网络直接发送到 Azure 虚拟网络中的虚拟机的网络流量。 

* 基本负载均衡器：如果在虚拟网络中部署基本的内部负载均衡器，或者在虚拟网络中部署的 Azure PaaS 服务使用基本的内部负载均衡器，则从本地网络到托管在基本负载均衡器上的虚拟 IP 的网络流量将发送到虚拟网络网关。 解决方案是将基本负载均衡器升级到[标准负载均衡器](../load-balancer/load-balancer-overview.md)。

* 专用链接：如果是从本地网络连接到虚拟网络中的[专用终结点](../private-link/private-link-overview.md)，则连接将通过虚拟网络网关。

## <a name="public-preview"></a>公共预览版

以下 FastPath 功能以公共预览版形式提供：

**VNet 对等互连** - FastPath 会绕过 ExpressRoute 虚拟网络网关，将流量直接发送到与连接到 ExpressRoute 的虚拟网络对等互连的虚拟网络中部署的任何 VM。

请参阅[如何注册 ExpressRoute FastPath 功能](expressroute-howto-linkvnet-arm.md#enroll-in-expressroute-fastpath-features-preview)。

在所有区域中可用。
 
## <a name="next-steps"></a>后续步骤

要启用 FastPath，请参阅将[虚拟网络链接到 ExpressRoute](expressroute-howto-linkvnet-arm.md#configure-expressroute-fastpath)。
