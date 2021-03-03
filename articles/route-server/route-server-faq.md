---
title: 有关 Azure 路由服务器的常见问题
description: 查找有关 Azure 路由服务器的常见问题的解答。
services: route-server
author: duongau
ms.service: route-server
ms.topic: article
ms.date: 03/02/2021
ms.author: duau
ms.openlocfilehash: 994fdf3f929f1f64ab4a064d7f2bd24c19322629
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101679143"
---
# <a name="azure-route-server-preview-faq"></a>Azure 路由服务器 (预览版) 常见问题

> [!IMPORTANT]
> Azure 路由服务器 (预览版) 目前为公共预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="what-is-azure-route-server"></a>什么是 Azure 路由服务器？

Azure 路由服务器是一种完全托管的服务，可让你轻松管理网络虚拟设备 (NVA) 和虚拟网络之间的路由。

### <a name="is-azure-route-server-just-a-vm"></a>Azure 路由服务器是否只是 VM？

否。 Azure 路由服务器是一种旨在实现高可用性的服务。 如果它部署在支持 [可用性区域](../availability-zones/az-overview.md)的 Azure 区域中，它将具有区域级别冗余。

### <a name="what-routing-protocols-does-azure-route-server-support"></a><a name = "protocol"></a>什么路由协议支持 Azure 路由服务器？

Azure 路由服务器仅支持 (BGP) 边界网关协议。 你的 NVA 需要支持多跃点外部 BGP，因为你将需要在虚拟网络中的专用子网中部署 Azure 路由服务器。 你选择的 [ASN](https://en.wikipedia.org/wiki/Autonomous_system_(Internet)) 必须与在 NVA 上配置 BGP 时使用的 Azure 路由服务器相同。

### <a name="does-azure-route-server-route-data-traffic-between-my-nva-and-my-vms"></a>Azure 路由服务器是否在我的 NVA 和我的 Vm 之间路由数据流量？

否。 Azure 路由服务器仅与 NVA 交换 BGP 路由。 数据流量直接从 NVA 转到所选 VM，并直接从 VM 发送到 NVA。

### <a name="if-azure-route-server-receives-the-same-route-from-more-than-one-nva-will-it-program-all-copies-of-the-route-but-each-with-a-different-next-hop-to-the-vms-in-the-virtual-network"></a>如果 Azure 路由服务器接收到来自多个 NVA 的相同路由，则它将对该 (路由的所有副本进行编程，而每个副本都有不同的下一跃点) 到虚拟网络中的 Vm？

是，仅当路由与路径长度相同时才存在。 当 Vm 向此路由的目标发送流量时，VM 主机将 Equal-Cost 多路径 (ECMP) 路由。 但是，如果一个 NVA 发送的路由的与其他 Nva 的路径长度短。 Azure 路由服务器将仅对将下一个跃点设置为此 NVA 的路由设置为虚拟网络中的 Vm。

### <a name="does-azure-route-server-support-vnet-peering"></a>Azure 路由服务器是否支持 VNet 对等互连？

是的。 如果将 Azure 路由服务器托管的 VNet 与另一个 VNet 一起使用，则在该 VNet 上启用 "使用远程网关"。 Azure 路由服务器将了解该 VNet 的地址空间，并将其发送到所有对等互连 Nva。

### <a name="what-autonomous-system-numbers-asns-can-i-use"></a>我可以使用哪些自治系统编号 (Asn) ？

可以在网络虚拟设备中使用自己的公共 Asn 或专用 Asn。 不能使用 Azure 或 IANA 保留的范围。
Azure 或 IANA 保留的 ASN 如下所示：

* 由 Azure 保留的 ASN：
    * 公用 ASN：8074、8075、12076
    * 专用 ASN：65515、65517、65518、65519、65520
* [由 IANA 保留](http://www.iana.org/assignments/iana-as-numbers-special-registry/iana-as-numbers-special-registry.xhtml)的 ASN：
    * 23456、64496-64511、65535-65551

### <a name="can-i-use-32-bit-4-byte-asns"></a>是否可以使用32位 (4 byte) Asn？

不，Azure 路由服务器仅支持16位 (2 个字节) Asn。

## <a name="route-server-limits"></a><a name = "limitations"></a>路由服务器限制

对于每个部署) ，Azure 路由服务器具有以下限制 (。

| 资源 | 限制 |
|----------|-------|
| 支持的 BGP 对等方数量 | 8 |
| 每个 BGP 对等节点可以播发到 Azure 路由服务器的路由数 | 200 |
| Azure 路由服务器可以播发到 ExpressRoute 或 VPN 网关的路由数 | 200 |

有关详细信息，请参阅 [诊断 Azure 虚拟机路由问题](../virtual-network/diagnose-network-routing-problem.md)。

## <a name="next-steps"></a>后续步骤

了解如何 [配置 Azure 路由服务器](quickstart-configure-route-server-powershell.md)。
