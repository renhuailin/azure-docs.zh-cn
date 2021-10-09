---
title: 有关 Azure 路由服务器的常见问题解答
description: 查看有关 Azure 路由服务器的常见问题解答。
services: route-server
author: duongau
ms.service: route-server
ms.topic: article
ms.date: 09/23/2021
ms.author: duau
ms.openlocfilehash: fa5ea8f191c0b2ea9c7db483eb4d7b9c5a679be0
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/28/2021
ms.locfileid: "129094360"
---
# <a name="azure-route-server-faq"></a>Azure 路由服务器常见问题解答

## <a name="what-is-azure-route-server"></a>什么是 Azure 路由服务器？

Azure 路由服务器是一项完全托管的服务，可让你轻松管理网络虚拟设备 (NVA) 和虚拟网络之间的路由。

### <a name="is-azure-route-server-just-a-vm"></a>Azure 路由服务器是否只是一个 VM？

不是。 Azure 路由服务器是一项旨在提供高可用性的服务。 如果将它部署在支持[可用性区域](../availability-zones/az-overview.md)的 Azure 区域中，它将具有区域级别冗余。

### <a name="how-many-route-servers-can-i-create-in-a-virtual-network"></a>可以在虚拟网络中创建多少个路由服务器？

只能在虚拟网络中创建一个路由服务器。 必须将其部署在名为 RouteServerSubnet 的专用子网中。

### <a name="does-azure-route-server-support-virtual-network-peering"></a>Azure 路由服务器是否支持虚拟网络对等互连？

支持，如果将托管 Azure 路由服务器的虚拟网络对等互连到另一个虚拟网络，并在第二个虚拟网络上启用“使用远程网关”，则 Azure 路由服务器将获知该虚拟网络的地址空间，并将其发送到所有已对等互连的 NVA。 它还会将来自 NVA 的路由编入已对等互连的虚拟网络中 VM 的路由表。 


### <a name="what-routing-protocols-does-azure-route-server-support"></a><a name = "protocol"></a>Azure 路由服务器支持哪些路由协议？

Azure 路由服务器仅支持边界网关协议 (BGP)。 NVA 需要支持多跃点外部 BGP，因为你将需要在虚拟网络的专用子网中部署 Azure 路由服务器。 在 NVA 上配置 BGP 时，所选的 [ASN](https://en.wikipedia.org/wiki/Autonomous_system_(Internet)) 必须与 Azure 路由服务器使用的不同。

### <a name="does-azure-route-server-route-data-traffic-between-my-nva-and-my-vms"></a>Azure 路由服务器是否会在我的 NVA 和 VM 之间路由数据流量？

不是。 Azure 路由服务器仅与 NVA 交换 BGP 路由。 数据流量直接从 NVA 流入目标 VM，并直接从 VM 流入 NVA。

### <a name="does-azure-route-server-store-customer-data"></a>Azure 路由服务器是否存储客户数据？
不是。 Azure 路由服务器仅与 NVA 交换 BGP 路由，然后将其传播到虚拟网络。

### <a name="why-does-azure-route-server-require-a-public-ip-address"></a>为什么 Azure 路由服务器需要公共 IP 地址？

Azure 路由器服务器需要确保与管理路由服务器配置的后端服务的连接，因此需要公共 IP 地址。 

### <a name="does-azure-route-server-support-ipv6"></a>Azure 路由服务器是否支持 IPv6？

不是。 我们未来会增加 IPv6 支持。 

### <a name="if-azure-route-server-receives-the-same-route-from-more-than-one-nva-how-does-it-handle-them"></a>如果 Azure 路由服务器接收来自多个 NVA 的相同路由，它会如何处理这些路由？

如果路由的 AS 路径长度相同，Azure 路由服务器会将路由的多个副本（每个副本有不同的下一个跃点）编入虚拟网络中的 VM。 当 VM 将流量发送到此路由的目标位置时，VM 主机将执行等价多路径 (ECMP) 路由。 但是，如果一个 NVA 发送的路由的 AS 路径长度比其他 NVA 的短，则 Azure 路由服务器只会将下一个跃点设置为此 NVA 的路由编入虚拟网络中的 VM。

### <a name="does-azure-route-server-preserve-the-bgp-communities-of-the-route-it-receives"></a>Azure 路由服务器是否保留其接收的路由的 BGP 社区？

是的，Azure 路由服务器按原样传播路由及 BGP 社区。

### <a name="what-is-the-bgp-timer-setting-of-azure-route-server"></a>Azure 路由服务器的 BGP 计时器设置是什么？
“保持活动状态”计时器设置为 60 秒，“保持关闭”计时器设置为 180 秒。

### <a name="what-autonomous-system-numbers-asns-can-i-use"></a>我可以使用哪些自治系统编号 (ASN)？

可以在网络虚拟设备中使用自己的公共 ASN 或专用 ASN。 不能使用 Azure 或 IANA 保留的范围。
Azure 或 IANA 保留的 ASN 如下所示：

* 由 Azure 保留的 ASN：
    * 公用 ASN：8074、8075、12076
    * 专用 ASN：65515、65517、65518、65519、65520
* [由 IANA 保留](http://www.iana.org/assignments/iana-as-numbers-special-registry/iana-as-numbers-special-registry.xhtml)的 ASN：
    * 23456、64496-64511、65535-65551

### <a name="can-i-use-32-bit-4-byte-asns"></a>能否使用 32 位（4 字节）ASN？

不能，Azure 路由服务器仅支持 16 位（2 字节）ASN。

### <a name="can-i-associate-a-user-defined-route-udr-to-the-routeserversubnet"></a>是否可以将用户定义的路由 (UDR) 关联到 RouteServerSubnet？

不能，Azure 路由服务器不支持在 RouteServerSubnet 中配置 UDR。

### <a name="can-i-associate-a-network-security-group-nsg-to-the-routeserversubnet"></a>是否可以将网络安全组 (NSG) 关联到 RouteServerSubnet？

不能，Azure 路由服务器不支持将 NSG 关联到 RouteServerSubnet。

### <a name="can-i-peer-two-route-servers-in-two-peered-virtual-networks-and-enable-the-nvas-connected-to-the-route-servers-to-talk-to-each-other"></a>能否在两个对等互连的虚拟网络中将两个路由服务器对等互连，使连接到路由服务器的 NVA 能够相互通信？ 

***拓扑：NVA1 -> RouteServer1 ->（通过 VNet 对等互连）-> RouteServer2 -> NVA2***

不能，Azure 路由服务器不会转发数据流量。 若要通过 NVA 启用传输连接，请在 NVA 之间设置直接连接（例如，IPsec 隧道），并使用路由服务器进行动态路由传播。 

## <a name="route-server-limits"></a><a name = "limitations"></a>路由服务器限制

Azure 路由服务器具有以下限制（对于每个部署）。

| 资源 | 限制 |
|----------|-------|
| 支持的 BGP 对等节点数量 | 8 |
| 每个 BGP 对等节点可以播发到 Azure 路由服务器的路由数 | 1000 |
| Azure 路由服务器可以播发到 ExpressRoute 或 VPN 网关的路由数 | 200 |
| Azure 路由服务器可支持的虚拟网络（包括对等互连虚拟网络）中的 VM 数量 | 2000 |

Azure 路由服务器支持的 VM 数量并非硬性限制。 此数量取决于在 Azure 区域中部署路由服务器基础结构的方式。

如果 NVA 播发的路由数超出了限制，则会删除 BGP 会话。 如果网关与 Azure 路由服务器之间删除了 BGP 会话，则会失去从本地网络到 Azure 的连接。 有关详细信息，请参阅[诊断 Azure 虚拟机路由问题](../virtual-network/diagnose-network-routing-problem.md)。


## <a name="next-steps"></a>后续步骤

了解如何[配置 Azure 路由服务器](quickstart-configure-route-server-powershell.md)。
