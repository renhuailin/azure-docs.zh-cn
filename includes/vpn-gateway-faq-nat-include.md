---
title: include 文件
description: include 文件
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 06/22/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: a237122aaeba57d8c3aa99274a0cc41232729eca
ms.sourcegitcommit: 16580bb4fbd8f68d14db0387a3eee1de85144367
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/24/2021
ms.locfileid: "112676922"
---
### <a name="is-nat-supported-on-all-azure-vpn-gateway-skus"></a>NAT 是否在所有 Azure VPN 网关 SKU 上都受支持？

NAT 在 VpnGw2-5 以及 VpnGw2AZ-5AZ 上受支持。

### <a name="can-i-use-nat-on-vnet-to-vnet-or-p2s-connections"></a>是否可以在 VNet 到 VNet 连接或 P2S 连接上使用 NAT？

不可以，仅支持在 IPsec 跨界连接上使用 NAT。

### <a name="how-many-nat-rules-can-i-use-on-a-vpn-gateway"></a>在一个 VPN 网关上可以使用多少个 NAT 规则？

在一个 VPN 网关上最多可以创建 100 个 NAT 规则（包括传入和传出规则）。

### <a name="is-nat-applied-to-all-connections-on-a-vpn-gateway"></a>NAT 是否应用于 VPN 网关上的所有连接？

NAT 应用于带有 NAT 规则的连接。 如果某个连接没有 NAT 规则，则 NAT 不会对该连接生效。 在同一个 VPN 网关上，可以将一些带有 NAT 的连接与其他一些不带有 NAT 的连接一起使用。

### <a name="what-types-of-nat-is-supported-on-azure-vpn-gateways"></a>Azure VPN 网关支持哪些类型的 NAT？

仅支持静态 1:1 NAT。 不支持动态 NAT 或 NAT64。

### <a name="does-nat-work-on-active-active-vpn-gateways"></a>NAT 是否可在主动-主动 VPN 网关上正常工作？

是的。 NAT 可以在主动-主动和主动-备用 VPN 网关上正常工作。

### <a name="does-nat-work-with-bgp-connections"></a>NAT 是否适用于 BGP 连接？

是，可将 BGP 与 NAT 配合使用。 下面是一些重要注意事项：

* 在“NAT 规则”配置页上选择“启用 BGP 路由转换”，以确保基于与连接关联的 NAT 规则，将获知的路由和播发的路由转换为执行 NAT 后的地址前缀（外部映射）。 需确保本地 BGP 路由器播发 IngressSNAT 规则中定义的确切前缀。

* 如果本地 VPN 路由器使用 APIPA (169.254.x.x) 作为 BGP 发言方/对等方 IP，请直接在本地网络网关的“BGP 对等方 IP 地址”字段中使用 APIPA 地址。 如果本地 VPN 路由器使用普通的非 APIPA 地址，但该地址与 VNet 地址空间或其他本地网络空间有冲突，请确保 IngressSNAT 规则会将 BGP 对等方 IP 转换为唯一的不重叠地址，并将执行 NAT 后的地址输入到本地网络网关的“BGP 对等方 IP 地址”字段中。

### <a name="do-i-need-to-create-the-matching-dnat-rules-for-the-snat-rule"></a>是否需要为 SNAT 规则创建匹配的 DNAT 规则？

不是。 单个 SNAT 规则将定义特定网络的双向转换：

* IngressSNAT 规则定义从本地网络进入 Azure VPN 网关的源 IP 地址的转换。 它还会处理离开 VNet 并进入同一本地网络的目标 IP 地址的转换。

* EgressSNAT 规则定义离开 Azure VPN 网关并进入本地网络的 VNet 源 IP 地址的转换。 它还会处理通过那些带有 EgressSNAT 规则的连接进入 VNet 的数据包的目标 IP 地址转换。

* 在任一情况下，都不需要 DNAT 规则。

### <a name="what-do-i-do-if-my-vnet-or-local-network-gateway-address-space-has-two-or-more-prefixes-can-i-apply-nat-to-all-of-them-or-just-a-subset"></a>如果我的 VNet 或本地网络网关地址空间具有两个或更多个前缀，该怎么办？ 是否可以将 NAT 应用于所有这些前缀？ 还是说，只能应用于其中的一部分前缀？

对于每个需要执行 NAT 的前缀，都要创建一个 NAT 规则，因为每个 NAT 规则只能包含该 NAT 的一个地址前缀。 例如，如果本地网络网关地址空间包括 10.0.1.0/24 和 10.0.2.0/25，则你可以创建两个规则，如下所示：

* IngressSNAT 规则 1：将 10.0.1.0/24 映射到 100.0.1.0/24
* IngressSNAT 规则 2：将 10.0.2.0/25 映射到 100.0.2.0/25

这两个规则必须与相应地址前缀的前缀长度相匹配。 这同样适用于 VNet 地址空间的 EgressSNAT 规则。

> [!IMPORTANT]
> 如果只将一个规则链接到上述连接，则不会转换其他地址空间。

### <a name="can-i-use-different-egresssnat-rules-to-translate-my-vnet-address-space-to-different-prefixes-to-different-on-premises-networks"></a>是否可以使用不同的 EgressSNAT 规则将 VNet 地址空间转换为不同本地网络的不同前缀？

是，可为同一个 VNet 地址空间创建多个 EgressSNAT 规则，并将 EgressSNAT 规则应用于不同的连接。 对于不带有 EgressSNAT 规则的连接，

### <a name="can-i-use-the-same-ingresssnat-rule-on-different-connections"></a>是否可以在不同的连接上使用同一个 IngressSNAT 规则？

是，如果在同一个本地网络中使用这些连接来提供冗余，则通常会采取这种做法。 如果这些连接用于不同的本地网络，则不能使用同一个传入规则。

### <a name="do-i-need-both-ingress-and-egress-rules-on-a-nat-connection"></a>一个 NAT 连接上是否既需要使用传入规则，又需要使用传出规则？

如果本地网络地址空间与 VNet 地址空间重叠，则在同一连接上既需要使用传入规则，又需要使用传出规则。 如果 VNet 地址空间在所有连接的网络中是唯一的，则不需要在这些连接上使用 EgressSNAT 规则。 可以使用传入规则来避免本地网络之间出现地址重叠的情况。