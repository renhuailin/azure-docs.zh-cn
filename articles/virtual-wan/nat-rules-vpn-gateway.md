---
title: 为网关配置 VPN NAT 规则
titleSuffix: Azure Virtual WAN
description: 了解如何为 VWAN VPN 网关配置 NAT 规则。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 03/05/2021
ms.author: cherylmc
ms.openlocfilehash: 6613952b5d7811c187123963a6e611602aabb5dc
ms.sourcegitcommit: 695a33a2123429289ac316028265711a79542b1c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/01/2021
ms.locfileid: "113127683"
---
# <a name="configure-nat-rules-for-your-virtual-wan-vpn-gateway"></a>为虚拟 WAN VPN 网关配置 NAT 规则

可以为虚拟 WAN VPN 网关配置静态一对一 NAT 规则。 NAT 规则提供了一种机制，用于设置 IP 地址的一对一转换。 NAT 可用于将 IP 地址不兼容或 IP 地址重叠的两个 IP 网络互连。 一个典型的应用场景是包含重叠 IP 且需要访问 Azure VNet 资源的分支。

此配置使用流表将流量从外部（主机）IP 地址路由到与虚拟网络中的终结点（虚拟机、计算机、容器等）关联的内部 IP 地址。

   :::image type="content" source="./media/nat-rules-vpn-gateway/diagram.png" alt-text="显示体系结构的示意图。":::
   
为使用 NAT，VPN 设备需要使用任意到任意（通配符）流量选择器， 不支持将基于策略（窄）的流量选择器与 NAT 配置结合使用。

## <a name="configure-nat-rules"></a><a name="rules"></a>配置 NAT 规则

你可以随时配置和查看 VPN 网关设置中的 NAT 规则。

> [!NOTE]
> 使用基于策略的流量选择器的站点到站点 VPN 连接不支持站点到站点 NAT。

   :::image type="content" source="./media/nat-rules-vpn-gateway/edit-rules.png" alt-text="显示如何编辑规则的屏幕截图。"lightbox="./media/nat-rules-vpn-gateway/edit-rules.png":::
1. 导航到虚拟中心。
1. 选择“VPN (站点到站点)”。
1. 选择“NAT 规则(编辑)”。
1. 在“编辑 NAT 规则”页上，可以添加/编辑/删除使用以下值的 NAT 规则： 

   * **名称：** NAT 规则的唯一名称。
   * **类型：** 静态。 静态一对一 NAT 可在内部地址和外部地址之间建立一对一关系。
   * **模式：** IngressSnat 或 EgressSnat。  
      * IngressSnat 模式（也称为“入口源 NAT”）适用于进入 Azure 中心的站点到站点 VPN 网关的流量。 
      * EgressSnat 模式（也称为“出口源 NAT”）适用于离开 Azure 中心的站点到站点 VPN 网关的流量。 
   * **InternalMapping：** 内部网络上要映射到一组外部 IP 的源 IP 的地址前缀范围。 换句话说，就是你的 NAT 前地址前缀范围。
   * **ExternalMapping：** 源 IP 要映射到的外部网络上的目标 IP 的地址前缀范围。 换句话说，就是你的 NAT 后地址前缀范围。
   * 链接连接：连接资源，用于以虚拟方式将 VPN 站点连接到 Azure 虚拟 WAN 中心的站点到站点 VPN 网关。
 
> [!NOTE]
> 如果希望站点到站点 VPN 网关通过 BGP 播发转换后的 (ExternalMapping) 地址前缀，请单击“启用 BGP 转换”按钮，这是由于本地会自动了解出口规则的 post-NAT 范围，且 Azure（虚拟 WAN 中心、已连接的虚拟网络、VPN 和 ExpressRoute 分支）会自动了解入口规则的 post-NAT 范围。 请注意，“启用 BGP 转换”设置适用于虚拟 WAN 中心站点到站点 VPN 网关上的所有 NAT 规则。 

## <a name="example-configurations"></a><a name="examples"></a>示例配置

### <a name="ingress-snat-bgp-enabled-vpn-site"></a>入口 SNAT（启用 BGP 的 VPN 站点）

入口 SNAT 规则适用于通过虚拟 WAN 站点到站点 VPN 网关进入 Azure 的数据包。 在此应用场景中，需要将两个站点到站点 VPN 分支连接到 Azure。 VPN 站点 1 通过链接 A 进行连接，VPN 站点 2 通过链接 B 进行连接。每个站点都具有相同的地址空间 10.30.0.0/24。

在本示例中，我们将 NAT 站点 1 连接到 127.30.0.0.0/24。 虚拟 WAN 辐射式虚拟网络和其他分支会自动了解此 post-NAT 地址空间。 

下图显示了预计的最终结果：

:::image type="content" source="./media/nat-rules-vpn-gateway/diagram-bgp.png" alt-text="显示站点（启用 BGP）入口模式 NAT 的关系图。":::

1. 指定 NAT 规则。

   指定 NAT 规则，以确保站点到站点 VPN 网关能够将两个具有重叠地址空间（例如 10.30.0.0/24）的分支区别开来。 在本示例中，我们将重点放在 VPN 站点 1 的链接 A 上。

   可以设置以下 NAT 规则并将其关联到链接 A。由于这是静态 NAT 规则，因此 InternalMapping 和 ExternalMapping 的地址空间包含相同数量的 IP 地址。

   * 名称：ingressRule01
   * **类型：** 静态
   * **模式：** IngressSnat
   * InternalMapping：10.30.0.0/24
   * ExternalMapping：172.30.0.0/24
   * 链接连接：链接 A

 2.  将“BGP 路由转换”切换为“启用”。

       :::image type="content" source="./media/nat-rules-vpn-gateway/enable-bgp.png" alt-text="显示如何启用 BGP 转换的屏幕截图。":::


3. 确保站点到站点 VPN 网关能够与本地 BGP 对等体对等。

      在本示例中，入口 NAT 规则需要将 10.30.0.132 转换为 127.30.0.132。 为此，请单击“编辑 VPN 站点”配置 VPN 站点链接 A BGP 地址，以反映此转换后的 BGP 对等体地址 (127.30.0.132)。 

   :::image type="content" source="./media/nat-rules-vpn-gateway/edit-site-bgp.png" alt-text="显示如何更改 BGP 对等互连 IP 的屏幕截图。"lightbox="./media/nat-rules-vpn-gateway/edit-site-bgp.png":::

 

### <a name="considerations-if-the-vpn-site-connects-via-bgp"></a><a name="considerations"></a>VPN 站点通过 BGP 进行连接时的注意事项
* 对于静态一对一 NAT，内部和外部映射的子网大小必须相同。
* 如果启用 BGP 转换，则站点到站点 VPN 网关会自动将出口 NAT 规则的外部映射播发到本地，并将入口 NAT 规则的外部映射播发到 Azure（虚拟 WAN 中心、已连接的辐射式虚拟网络、已连接的 VPN/ExpresRoute）。 如果禁用 BGP 转换，则转换后的路由不会自动播发到本地。 因此，必须将本地 BGP 扬声器配置为播发与该 VPN 站点链接连接相关联的入口 NAT 规则的 post-NAT (ExternalMapping) 范围。 同样，必须在本地设备上应用适用于出口 NAT 规则 post-NAT (ExternalMapping) 范围的路由。
* 如果本地 BGP 对等体 IP 地址包含在入口 NAT 规则的内部映射内，则站点到站点 VPN 网关会自动转换本地 BGP 对等体 IP 地址。 因此，VPN 站点的链接连接 BGP 地址必须反映 NAT 转换的地址（外部映射的一部分）。 

    例如，如果本地 BGP IP 地址是 10.30.0.133，且存在将 10.30.0.0/24 转换为 127.30.0.0/24 的入口 NAT 规则，则必须将 VPN 站点的链接连接 BGP 地址配置为转换后的地址 (127.30.0.133)。

 
### <a name="ingress-snat-vpn-site-with-statically-configured-routes"></a>入口 SNAT（具有静态配置路由的 VPN 站点）

入口 SNAT 规则适用于通过虚拟 WAN 站点到站点 VPN 网关进入 Azure 的数据包。 在此应用场景中，需要将两个站点到站点 VPN 分支连接到 Azure。 VPN 站点 1 通过链接 A 进行连接，VPN 站点 2 通过链接 B 进行连接。每个站点都具有相同的地址空间 10.30.0.0/24。

在本示例中，我们将 NAT VPN 站点 1 连接到 127.30.0.0.0/24。 但是，由于 VPN 站点未通过 BGP 连接至站点到站点 VPN 网关，因此配置步骤与启用 BGP 的示例略有不同。 

   :::image type="content" source="./media/nat-rules-vpn-gateway/diagram-static.png" alt-text="显示 VPN 站点（使用静态路由）关系图配置的屏幕截图。":::


1. 指定 NAT 规则。

    指定 NAT 规则，以确保站点到站点 VPN 网关能够将两个具有相同地址空间 10.30.0.0/24 的分支区别开来。 在本示例中，我们将重点放在 VPN 站点 1 的链接 A 上。

    可以设置以下 NAT 规则并将其关联到其中一个 VPN 站点 1 的链接 A。 由于这是静态 NAT 规则，因此 InternalMapping 和 ExternalMapping 的地址空间包含相同数量的 IP 地址。

    * 名称：IngressRule01
    * 类型：静态
    * 模式：IngressSnat
    * InternalMapping：10.30.0.0/24
    * ExternalMapping：172.30.0.0/24
    * 链接连接：链接 A

2. 编辑 VPN 站点 1 的“专用地址空间”字段，以确保站点到站点 VPN 网关了解 post-NAT 范围 (172.30.0.0/24)

   * 导航到包含站点到站点 VPN 网关的虚拟中心资源。 在虚拟中心页的“连接性”下，选择“VPN (站点到站点)”。 

   * 选择通过链接 A 连接至虚拟 WAN 中心的 VPN 站点。选择“编辑站点”，然后输入 172.30.0.0/24 作为 VPN 站点的专用地址空间。 

   :::image type="content" source="./media/nat-rules-vpn-gateway/vpn-site-static.png" alt-text="显示如何编辑 VPN 站点专用地址空间的屏幕截图" lightbox="./media/nat-rules-vpn-gateway/vpn-site-static.png":::

### <a name="considerations-if-vpn-sites-are-statically-configured-not-connected-via-bgp"></a><a name="considerationsnobgp"></a>静态配置 VPN 站点（未通过 BGP 连接）时的注意事项
* 对于静态一对一 NAT，内部和外部映射的子网大小必须相同。
* 在 Azure 门户中编辑 VPN 站点，以在“专用地址空间”字段入口 NAT 规则的 ExternalMapping 中添加前缀。 
* 对于涉及出口 NAT 规则的配置，需要在本地设备上应用路由策略或带有出口 NAT 规则 ExternalMapping 的静态路由。

### <a name="packet-flow"></a>数据包流

在上述示例中，本地设备希望访问辐射式虚拟网络中的资源。 数据包流如下所示，NAT 转换以粗体显示。

1. 来自本地的流量已启动。
   * 源 IP 地址：10.30.0.4
   * 目标 IP 地址：10.200.0.4
1. 流量进入站点到站点网关，并使用 NAT 规则进行转换，然后发送到分支。
   * 源 IP 地址：172.30.0.4
   * 目标 IP 地址：10.200.0.4
1. 来自分支的回复已启动。
   * 源 IP 地址：10.200.0.4
   * 目标 IP 地址：172.30.0.4
1. 流量进入站点到站点 VPN 网关，进行反向转换，然后发送到本地。
   * 源 IP 地址：10.200.0.4
   * 目标 IP 地址：10.30.0.4

### <a name="verification-checks"></a>验证清单

此部分介绍用于验证是否正确设置了配置的检查。 

#### <a name="validate-defaultroutetable-rules-and-routes"></a>验证 DefaultRouteTable、规则和路由

虚拟 WAN 中的分支关联到 **DefaultRouteTable**，这意味着所有分支连接都了解在 DefaultRouteTable 中填充的路由。 在 DefaultRouteTable 的有效路由中，你会看到带有转换前缀的 NAT 规则。

在上述示例中：

* 前缀：172.30.0.0/24  
* **下一个跃点类型：** VPN_S2S_Gateway
* **下一个跃点：** VPN_S2S_Gateway 资源

#### <a name="validate-address-prefixes"></a>验证地址前缀

此示例适用于与 DefaultRouteTable 关联的虚拟网络中的资源。

位于辐射式虚拟网络（已连接至虚拟 WAN 中心）中的任何虚拟机的网络接口卡 (NIC) 上的“有效路由”还应包含入口 NAT 规则中指定的 ExternalMapping 地址前缀。 

本地设备还应包含前缀（包含在出口 NAT 规则的外部映射中）路由。 

####  <a name="common-configuration-patterns"></a>常见配置模式 

> [!NOTE]
> 使用基于策略的流量选择器的站点到站点 VPN 连接不支持站点到站点 NAT。

下表显示在站点到站点 VPN 网关上配置不同类型的 NAT 规则时出现的常见配置模式。  

| VPN 站点类型 | 入口 NAT 规则 | 出口 NAT 规则
|--- |--- | ---|
|具有静态配置路由的 VPN 站点 |编辑 VPN 站点中的“专用地址空间”以包含 NAT 规则的 ExternalMapping| 在本地设备上为 NAT 规则的 ExternalMapping 应用路由。|
|VPN 站点（已启用 BGP 转换）|将 BGP 对等体的 ExternalMapping 地址置于 VPN 站点链接连接的 BGP 地址中。  | 无特殊注意事项。 |
| VPN 站点（已禁用 BGP 转换） | 确保本地 BGP 扬声器播发 NAT 规则 ExternalMapping 中的前缀。 同时将 BGP 对等体的 ExternalMapping 地址置于 VPN 站点链接连接的 BGP 地址中。| 在本地设备上为 NAT 规则的 ExternalMapping 应用路由。|

## <a name="next-steps"></a>后续步骤

有关站点到站点配置的详细信息，请参阅[配置虚拟 WAN 站点到站点连接](virtual-wan-site-to-site-portal.md)。
