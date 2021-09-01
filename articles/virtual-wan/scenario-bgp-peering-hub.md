---
title: 与虚拟中心建立 BGP 对等互连的相关内容
titleSuffix: Azure Virtual WAN
description: 了解与 Azure 虚拟 WAN 虚拟中心建立 BGP 对等互连的相关内容。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 08/06/2021
ms.author: cherylmc
ms.openlocfilehash: 1f57d48d2fecded727e4087a138fa67c514bea0e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121779715"
---
# <a name="scenario-bgp-peering-with-a-virtual-hub-preview"></a>方案：与虚拟中心建立 BGP 对等互连（预览版）

Azure 虚拟 WAN 中心路由器（也称为虚拟中心路由器）可用作路由管理器，并简化虚拟中心内部及跨虚拟中心的路由操作。 换而言之，虚拟中心路由器可以执行以下操作：

* 作为核心路由引擎与 VPN、ExpressRoute、P2S 和网络虚拟设备 (NVA) 等网关执行通信，从而简化路由管理。 
* 启用自定义路由表的高级路由方案、关联和路由传播。
* 充当已连接到虚拟中心的虚拟网络之间的流量传输路由器。

虚拟中心路由器现在已公开与之对等互连的功能，从而实现直接通过边界网关协议 (BGP) 路由协议交换路由信息。 如果在已连接到虚拟中心的虚拟网络中已预配 NVA 或 BGP 终结点，则这些终结点可直接与虚拟中心路由器对等互连，前提是虚拟中心路由器支持 BGP 路由协议，并确保 NVA 上的 ASN 拥有不同于虚拟中心 ASN 的设置情况。

## <a name="benefits-and-considerations"></a>优点和注意事项

**主要优点**

* 每当虚拟网络地址更新时，你不再需要手动更新 NVA 上的路由表。
* 每当 NVA 公布新路由或撤消旧路由时，你不再需要手动更新用户定义的路由。
* 虚拟网络中已连接到虚拟中心的 NVA 可以发现虚拟中心网关（VPN、ExpressRoute 或托管 NVA）路由。
* 可以将 NVA 的多个实例与虚拟中心路由器对等互连。 可以在 NVA 中配置 BGP 属性，并根据设计（例如主动-主动或主动-被动），使虚拟中心路由器获悉 NVA 实例的主动或被动情况。

**注意事项**

* 请勿将虚拟中心路由器与已在虚拟网络中配置的 Azure 路由服务器对等互联。
* 虚拟中心路由器仅支持 16 位（2 字节）ASN。
* 具有 NVA BGP 连接终结点的虚拟网络连接必须始终关联并传播到 defaultRouteTable。 目前不支持自定义路由表。
* 虚拟中心路由器支持已连接到虚拟中心的虚拟网络之间的传输连接。 鉴于虚拟 WAN 已支持传输连接，则上述虚拟中心路由器支持的功能与 BGP 对等互连功能并不相关。 示例：
  * VNET1：已连接到虚拟中心 1 的 NVA1 ->（传输连接）-> VNET2：已连接到虚拟中心 1 的 NVA2。
  * VNET1：已连接到虚拟中心 1 的 NVA1 ->（传输连接）-> VNET2：已连接到虚拟中心 2 的 NVA2。
* 可以在网络虚拟设备中使用自己的公共 ASN 或专用 ASN。 不能使用 Azure 或 IANA 保留的范围。 Azure 或 IANA 保留的 ASN 如下所示：
   * 由 Azure 保留的 ASN：
     * 公用 ASN：8074、8075、12076
     * 专用 ASN：65515、65517、65518、65519、65520
   * 由 IANA 保留的 ASN：23456、64496-64511、65535-65551
* 尽管虚拟中心路由器可以与 NVA 交换 BGP 路由，从而将 BGP 路由传播到虚拟网络，但虚拟中心路由器仍会通过虚拟中心托管网关（VPN 网关/ExpressRoute 网关/托管 NVA 网关），直接在本地促进路由的传播。 

   虚拟中心路由器受以下内容限制：

   | 资源 | 限制 |
   |---|---|
   |  每个 BGP 对等节点可以播发到虚拟中心的路由数。| 虚拟中心最多只能接受来自其已连接资源的 10,000 个路由（总数）。 例如，如果与虚拟网络、分支、虚拟中心等连接的虚拟中心共有 6000 个路由，则在使用 NVA 配置新的 BGP 对等互连时，NVA 最多只能播发到 4000 个路由。 |

## <a name="bgp-peering-scenarios"></a>BGP 对等互连方案

本部分介绍了使用 BGP 对等互连功能配置路由的方案。

## <a name="transit-vnet-connectivity"></a><a name="vnet-vnet"></a>VNet 之间的传输连接

:::image type="content" source="./media/scenario-bgp-peering-hub/vnet-vnet.png" alt-text="图形：VNet 到 VNet 路由。":::

在此方案中，名为“虚拟中心 1”的虚拟中心已连接到多个虚拟网络。 目标是在虚拟网络 VNET1 和 VNET5 之间建立路由。

### <a name="configuration-steps-without-bgp-peering"></a>不使用 BGP 对等互连的配置步骤

当未在虚拟中心使用 BGP 对等互连时，必须执行以下步骤：

虚拟中心配置

* 在虚拟中心 1 的 defaultRouteTable 中，为指向 VNET2 连接的 VNET5（子网：10.2.1.0/24）配置静态路由。
* 在虚拟中心 1 的 VNET2 虚拟网络连接中，为指向 VNET2 NVA IP（子网：10.2.0.5）的 VNET5 配置静态路由。
* 在虚拟中心 1 中，将来自 VNET1 和 VNET2 连接的路由传播到 defaultRouteTable，并将这些路由关联到 defaultRouteTable。

虚拟网络配置

* 在 VNET5 上，设置用户定义的路由 (UDR)，并将其指向 VNET2 NVA IP。

### <a name="configuration-steps-with-bgp-peering"></a>使用 BGP 对等互连的配置步骤

在先前的配置中，如果 VNET5 配置频繁更改，则对静态路由和 UDR 的维护可能会变得比较复杂。 若要解决此问题，可以使用与虚拟中心建立 BGP 对等互连功能，并且必须通过以下步骤更改路由配置：

虚拟中心配置

* 在虚拟中心 1 中，将 VNET2 NVA 配置为 BGP 对等节点。 同时，配置 VNET2 NVA，使其与虚拟中心 1 建立 BGP 对等互连。
* 在虚拟中心 1 中，将来自 VNET1 和 VNET2 连接的路由传播到 defaultRouteTable，并将这些路由关联到 defaultRouteTable。

虚拟网络配置

* 在 VNET5 上，设置用户定义的路由 (UDR)，并将其指向 VNET2 NVA IP。

#### <a name="effective-routes"></a>有效路由

下表显示的是虚拟中心 1 的 defaultRouteTable 中几个有效路由项。 请注意 VNET5 的路由（子网：10.2.1.0/24），可由此确认 VNET1 和 VNET5 能够实现相互通信。

| 目标前缀 |  下一跃点| 源 | ASN 路径|
| --- | --- | --- | ---|
| 10.2.0.0/24 |eastusconn  | VNet 连接 ID | - |
| 10.2.1.0/24 |适用于 NVA 的 BGP 对等节点 ID  | 适用于 NVA 的 BGP 对等节点 ID |  65510|
|  10.4.1.0/24 | 虚拟中心 2 | 虚拟中心 2 | - |

使用上述方式配置路由时，无需在虚拟中心使用静态路由项。 因此，此配置方法更为简单。并且当已连接的虚拟网络（如 VNET5）中的配置发生更改时，系统会动态更新路由表。

## <a name="branch-vnet-connectivity"></a><a name="branch-vnet">分支到 VNet 的连接</a>

:::image type="content" source="./media/scenario-bgp-peering-hub/branch-vnet.png" alt-text="图形：分支到 VNet 路由。":::

在此方案中，名为“NVA 分支 1”的本地站点，其 VPN 配置为终止于 VNET2 NVA。 目标是在配置 NVA 分支 1 和虚拟网络 VNET1 之间配置路由。

### <a name="configuration-steps-without-bgp-peering"></a>不使用 BGP 对等互连的配置步骤

当未在虚拟中心使用 BGP 对等互连时，必须执行以下步骤：

虚拟中心配置

* 在虚拟中心 1 的 defaultRouteTable 中，为指向 VNET2 连接的 NVA 分支 1 配置静态路由。
* 在虚拟中心 1 的 VNET2 虚拟网络连接中，为指向 VNET2 NVA IP（子网：10.2.0.5）的 NVA 分支 1 配置静态路由。
* 在虚拟中心 1 中，将来自 VNET1 和 VNET2 连接的路由传播到 defaultRouteTable，并将这些路由关联到 defaultRouteTable。

虚拟网络配置

* 在 VNET2 NVA 和 NVA 分支 1 之间建立 BGP 对等互连，并在 VNET2 NVA 和 NVA 分支 1 之间播发 VNET1 路由。

### <a name="configuration-steps-with-bgp-peering"></a>使用 BGP 对等互连的配置步骤

随着时间的推移，NVA 分支 1 的目标前缀可能会更改，或者可能有许多站点（如 NVA 分支 1）需要连接到 VNET1。 这会造成需要更新虚拟中心 1 和 VNET2 连接上的静态路由，同时该操作也很繁琐。 这种情况下，我们可以使用与虚拟中心建立 BGP 对等互连功能，而路由连接的配置步骤则如下所示。

虚拟中心配置

* 在虚拟中心 1 中，将 VNET2 NVA 配置为 BGP 对等节点。 同时，配置 VNET2 NVA，使其与虚拟中心 1 建立 BGP 对等互连。
* 在虚拟中心 1 中，将来自 VNET1 和 VNET2 连接的路由传播到 defaultRouteTable，并将这些路由关联到 defaultRouteTable。

虚拟网络配置

* 在 VNET2 NVA 和 NVA 分支 1 之间建立 BGP 对等互连，并在 VNET2 NVA 和 NVA 分支 1 之间播发 VNET1 路由。

#### <a name="effective-routes"></a>有效路由

下表显示的是虚拟中心 1 的 defaultRouteTable 中几个有效路由项。 请注意，通过与 NVA 建立 BGP 对等互连可发现 NVA 分支 1 路由（子网：192.168.1.0/24）。

 | 目标前缀 |  下一个跃点| 源 | ASN 路径|
| --- | --- | --- | ---|
| 10.2.0.0/24 | eastusconn  | VNet 连接 ID | - |
| 192.168.1.0/24 | 适用于 NVA 的 BGP 对等节点 ID  | 适用于 NVA 的 BGP 对等节点 ID |  65510|

由于虚拟中心 1 和 NVA 之间的 BGP 对等互连将动态更新路由表，因此若要管理 NVA 分支 1 中的网络更改情况或在新站点（如 NVA 分支 1）之间建立连接，无需更改虚拟中心 1 中的配置情况。 配置和维护因此大大简化。

## <a name="next-steps"></a>后续步骤

* [与虚拟中心建立 BGP 对等互连](create-bgp-peering-hub-portal.md)。
