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
ms.openlocfilehash: e570e5f06af814a6d0cbb581275d1c70ebf0df8a
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124780787"
---
# <a name="scenario-bgp-peering-with-a-virtual-hub-preview"></a>方案：与虚拟中心建立 BGP 对等互连（预览版）

> [!IMPORTANT]
> 与虚拟 WAN 中心的 BGP 对等互连功能目前以封闭公开预览版形式提供。 如果有兴趣试用此功能，请发送电子邮件至 previewbgpwithvhub@microsoft.com 并附上虚拟 WAN 资源的资源 ID。 收到功能启用的确认后，请在以下[文档页](create-bgp-peering-hub-portal.md)中查看主要注意事项和详细配置指南。 
>
> 要查找资源 ID，请打开 Azure 门户，导航到虚拟 WAN 资源，并单击“设置”>“属性”>“资源 ID”。<br> 示例： `/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Network/virtualWans/<virtualWANname>`
>


Azure 虚拟 WAN 中心路由器（也称为虚拟中心路由器）可用作路由管理器，并简化虚拟中心内部及跨虚拟中心的路由操作。 换而言之，虚拟中心路由器可以执行以下操作：

* 通过作为与 VPN、ExpressRoute、P2S 和网络虚拟设备 (NVA) 等网关通信的中心路由引擎来简化路由管理。 
* 支持自定义路由表的高级路由方案、路由关联和路由传播。
* 充当在已连接到虚拟中心的虚拟网络之间传输/传输到虚拟网络的流量的路由器。

现在，虚拟中心路由器还公开了与之进行对等互连的功能，从而实现直接通过边界网关协议 (BGP) 路由协议来交换路由信息。 已连接到虚拟中心的虚拟网络中预配的 NVA 或 BGP 终结点可直接与虚拟中心路由器对等互连，前提是虚拟中心路由器支持 BGP 路由协议，并确保 NVA 上的 ASN 拥有不同于虚拟中心 ASN 的设置。

## <a name="benefits-and-considerations"></a>优点和注意事项

**主要优点**

* 每当虚拟网络地址更新时，你不再需要手动更新 NVA 上的路由表。
* 每当 NVA 公布新路由或撤消旧路由时，你不再需要手动更新用户定义的路由。
* 连接到虚拟中心的虚拟网络中的 NVA 可以发现虚拟中心网关（VPN、ExpressRoute 或托管 NVA）路由。
* 可以将 NVA 的多个实例与虚拟中心路由器对等互连。 可以在 NVA 中配置 BGP 属性，并根据设计（主动-主动或主动-被动），让虚拟中心路由器知道哪个 NVA 实例是主动的，哪个是被动的。

**注意事项**

* 不能将虚拟中心路由器与虚拟网络中预配的 Azure 路由服务器对等互连。
* 虚拟中心路由器仅支持 16 位（2 字节）ASN。
* 具有 NVA BGP 连接终结点的虚拟网络连接必须始终关联并传播到 defaultRouteTable。 目前不支自定义路由表。
* 虚拟中心路由器支持连接到虚拟中心的虚拟网络之间的传输连接。 此功能与 BGP 对等互连功能并不相关，因为虚拟 WAN 已支持传输连接。 示例：
  * VNET1：连接到虚拟中心 1 的 NVA1 ->（传输连接）-> VNET2：连接到虚拟中心 1 的 NVA2。
  * VNET1：连接到虚拟中心 1 的 NVA1 ->（传输连接）-> VNET2：连接到虚拟中心 2 的 NVA2。
* 可以在网络虚拟设备中使用自己的公共 ASN 或专用 ASN。 不能使用 Azure 或 IANA 保留的范围。 Azure 或 IANA 保留的 ASN 如下所示：
   * 由 Azure 保留的 ASN：
     * 公用 ASN：8074、8075、12076
     * 专用 ASN：65515、65517、65518、65519、65520
   * IANA 保留的 ASN：23456、64496-64511、65535-65551
* 虽然虚拟中心路由器可以与 NVA 交换 BGP 路由，并传播到虚拟网络，但它通过虚拟中心托管网关（VPN 网关/ExpressRoute 网关/托管 NVA 网关）直接促进从本地传播路由。 

   虚拟中心路由器具有以下限制：

   | 资源 | 限制 |
   |---|---|
   |  每个 BGP 对等节点可以播发到虚拟中心的路由数。| 中心最多只能接受来自其连接资源的 10,000 个路由（总计）。 例如，如果虚拟中心具有来自连接的虚拟网络、分支和虚拟中心等共计 6000 个路由，当使用 NVA 配置新的 BGP 对等互连时，NVA 最多只能播发 4000 个路由。 |

## <a name="bgp-peering-scenarios"></a>BGP 对等互连方案

本部分介绍了使用 BGP 对等互连功能配置路由的方案。

## <a name="transit-vnet-connectivity"></a><a name="vnet-vnet"></a>VNet 之间的传输连接

:::image type="content" source="./media/scenario-bgp-peering-hub/vnet-vnet.png" alt-text="图形：VNet 到 VNet 路由。":::

在此方案中，名为“中心 1”的虚拟中心连接到多个虚拟网络。 目标是在虚拟网络 VNET1 和 VNET5 之间建立路由。

### <a name="configuration-steps-without-bgp-peering"></a>不使用 BGP 对等互连的配置步骤

当未对虚拟中心使用 BGP 对等互连时，需要执行以下步骤：

虚拟中心配置

* 在中心 1 的 defaultRouteTable 上，为指向 VNET2 连接的 VNET5（子网 10.2.1.0/24）配置静态路由。
* 在中心 1 的 VNET2 虚拟网络连接上，为指向 VNET2 NVA IP（子网 10.2.0.5）的 VNET5 配置静态路由。
* 在中心 1 上，将来自 VNET1 和 VNET2 连接的路由传播到 defaultRouteTable，并将这些路由关联到 defaultRouteTable。

虚拟网络配置

* 在 VNET5 上，设置用户定义的路由 (UDR) 以指向 VNET2 NVA IP。

### <a name="configuration-steps-with-bgp-peering"></a>使用 BGP 对等互连的配置步骤

在以前的配置中，如果 VNET5 配置频繁更改，维护静态路由和 UDR 可能会变得复杂。 若要解决这个难题，可以使用与虚拟中心建立 BGP 对等互连功能，并必须将路由配置更改为以下步骤：

虚拟中心配置

* 在中心 1 上，将 VNET2 NVA 配置为 BGP 对等节点。 此外，配置 VNET2 NVA 以与中心 1 建立 BGP 对等互连。
* 在中心 1 上，将来自 VNET1 和 VNET2 连接的路由传播到 defaultRouteTable，并将这些路由关联到 defaultRouteTable。

虚拟网络配置

* 在 VNET5 上，设置用户定义的路由 (UDR) 以指向 VNET2 NVA IP。

#### <a name="effective-routes"></a>有效路由

下表显示的是中心 1 的 defaultRouteTable 中几个有效路由项。 请注意 VNET5（子网 10.2.1.0/24）的路由，这确认了 VNET1 和 VNET5 能够相互通信。

| 目标前缀 |  下一跃点| 源 | ASN 路径|
| --- | --- | --- | ---|
| 10.2.0.0/24 |eastusconn  | VNet 连接 ID | - |
| 10.2.1.0/24 |NVA 的 BGP 对等节点连接 ID  | NVA 的 BGP 对等节点连接 ID |  65510|
|  10.4.1.0/24 | 中心 2 | 中心 2 | - |

通过使用上述方式配置路由即无需在虚拟中心上使用静态路由项。 因此，此配置方法更为简单。并且当已连接的虚拟网络（如 VNET5）中的配置发生更改时，系统会动态更新路由表。

## <a name="branch-vnet-connectivity"></a><a name="branch-vnet">分支到 VNet 的连接</a>

:::image type="content" source="./media/scenario-bgp-peering-hub/branch-vnet.png" alt-text="图形：分支到 VNet 路由。":::

在此方案中，名为“NVA 分支 1”的本地站点有一个 VPN，配置为终止于 VNET2 NVA。 目标是在 NVA 分支 1 和虚拟网络 VNET1 之间配置路由。

### <a name="configuration-steps-without-bgp-peering"></a>不使用 BGP 对等互连的配置步骤

当未对虚拟中心使用 BGP 对等互连时，需要执行以下步骤：

虚拟中心配置

* 在中心 1 的 defaultRouteTable 上，为指向 VNET2 连接的 NVA 分支 1 配置静态路由。
* 在中心 1 的 VNET2 虚拟网络连接上，为指向 VNET2 NVA IP (10.2.0.5) 的 NVA 分支 1 配置静态路由。
* 在中心 1 上，将来自 VNET1 和 VNET2 连接的路由传播到 defaultRouteTable，并将这些路由关联到 defaultRouteTable。

虚拟网络配置

* 在 VNET2 NVA 和 NVA 分支 1 之间建立 BGP 对等互连，将 VNET1 的播发从 VNET2 NVA 路由到 NVA 分支 1。

### <a name="configuration-steps-with-bgp-peering"></a>使用 BGP 对等互连的配置步骤

随着时间的推移，NVA 分支 1 中的目标前缀可能会更改，或者可能有许多站点（例如 NVA 分支 1）需要连接到 VNET1。 这会造成需要更新中心 1 和 VNET2 连接上的静态路由，而这样可能会很麻烦。 在这种情况下，我们可以使用与虚拟中心建立 BGP 对等互连功能，而路由连接的配置步骤则如下所示。

虚拟中心配置

* 在中心 1 上，将 VNET2 NVA 配置为 BGP 对等节点。 此外，配置 VNET2 NVA 以与中心 1 建立 BGP 对等互连。
* 在中心 1 上，将来自 VNET1 和 VNET2 连接的路由传播到 defaultRouteTable，并将这些路由关联到 defaultRouteTable。

虚拟网络配置

* 在 VNET2 NVA 和 NVA 分支 1 之间建立 BGP 对等互连，将 VNET1 的播发从 VNET2 NVA 路由到 NVA 分支 1。

#### <a name="effective-routes"></a>有效路由

下表显示的是中心 1 的 defaultRouteTable 中几个有效路由项。 请注意，NVA 分支 1（子网 192.168.1.0/24）的路由是通过与 NVA 建立 BGP 对等互连来发现的。

 | 目标前缀 |  下一跃点| 源 | ASN 路径|
| --- | --- | --- | ---|
| 10.2.0.0/24 | eastusconn  | VNet 连接 ID | - |
| 192.168.1.0/24 | NVA 的 BGP 对等节点连接 ID  | NVA 的 BGP 对等节点连接 ID |  65510|

若要管理 NVA 分支 1 中的网络更改，或在新站点（例如 NVA 分支 1）之间建立连接，无需在中心 1 上进行额外的配置，因为中心 1 和 NVA 之间的 BGP 对等互连将动态更新路由表。 配置和维护因此大大简化。

## <a name="next-steps"></a>后续步骤

* [与虚拟中心建立 BGP 对等互连](create-bgp-peering-hub-portal.md)。
