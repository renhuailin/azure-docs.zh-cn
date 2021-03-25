---
title: 为网关配置 VPN NAT 规则
titleSuffix: Azure Virtual WAN
description: 了解如何为 VWAN VPN 网关配置 NAT 规则
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 03/05/2021
ms.author: cherylmc
ms.openlocfilehash: 6fbee31f015953bd7e65648ea273e3ca84686115
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2021
ms.locfileid: "102431176"
---
# <a name="configure-nat-rules-for-your-virtual-wan-vpn-gateway---preview"></a>为虚拟 WAN VPN 网关配置 NAT 规则 - 预览版

> [!IMPORTANT]
> NAT 规则目前以公共预览版提供。
> 此预览版在提供时没有附带服务级别协议，建议不要将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

可以为虚拟 WAN VPN 网关配置静态一对一 NAT 规则。 NAT 规则提供了一种机制，用于设置 IP 地址的一对一转换。 NAT 可用于将 IP 地址不兼容或 IP 地址重叠的两个 IP 网络互连。 一个典型的应用场景是包含重叠 IP 且需要访问 Azure VNet 资源的分支。

此配置使用流表将流量从外部（主机）IP 地址路由到与虚拟网络中的终结点（虚拟机、计算机、容器等）关联的内部 IP 地址。

   :::image type="content" source="./media/nat-rules-vpn-gateway/diagram.png" alt-text="显示体系结构的示意图。":::

## <a name="configure-nat-rules"></a><a name="rules"></a>配置 NAT 规则

你可以随时配置和查看 VPN 网关设置中的 NAT 规则。

   :::image type="content" source="./media/nat-rules-vpn-gateway/edit-rules.png" alt-text="显示编辑规则的屏幕截图。":::

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
   * **链接连接：** 连接资源，用于以虚拟方式将 VPN 站点连接到 Azure 中心的站点到站点 VPN 网关。

### <a name="configuration-considerations"></a><a name="considerations"></a>配置注意事项

* 对于静态一对一 NAT，内部和外部映射的子网大小必须相同。
* 请确保在 Azure 门户中编辑 VPN 站点，以在“专用地址空间”字段中添加 **ExternalMapping** 前缀。 目前，启用了 BGP 的站点需要确保本地 BGP 公告方（设备 BGP 设置）包含外部映射前缀的条目。

## <a name="examples-and-verification"></a><a name="examples"></a>示例和验证

### <a name="ingress-mode-nat"></a>入口模式 NAT

入口模式 NAT 规则适用于通过虚拟 WAN 站点到站点 VPN 网关进入 Azure 的数据包。 在此应用场景中，需要将两个站点到站点 VPN 分支连接到 Azure。 VPN 站点 1 通过链接 1 进行连接，VPN 站点 2 通过链接 2 进行连接。 每个站点都有地址空间 192.169.1.0/24。

下图显示了预计的最终结果：

:::image type="content" source="./media/nat-rules-vpn-gateway/ingress.png" alt-text="显示入口模式 NAT 的关系图。":::

1. 指定 NAT 规则。

   指定 NAT 规则，以确保站点到站点 VPN 网关能够将两个具有重叠地址空间（例如 192.168.1.0/24）的分支区别开来。 在此示例中，我们将重点放在 VPN 站点 1 的链接 1 上。

   可以设置以下 NAT 规则并将其关联到其中一个分支的链接 1。 由于这是静态 NAT 规则，因此 InternalMapping 和 ExternalMapping 的地址空间包含相同数量的 IP 地址。

   * **名称：** IngressRule01
   * **类型：** 静态
   * **模式：** IngressSnat
   * **InternalMapping：** 192.168.1.0/24
   * **ExternalMapping：** 10.1.1.0/24
   * **链接连接：** 链接 1

1. 播发正确的 ExternalMapping。

   在此步骤中，请确保站点到站点 VPN 网关将正确的 ExternalMapping 地址空间播发到其余的 Azure 资源。 下面提供的说明因是否启用了 BGP 而有所不同。

   **示例 1：已启用 BGP**

   * 确保位于 VPN 站点 1 的本地 BGP 发言方已配置为播发 10.1.1.0/24 地址空间。
   * 在此预览版中，启用了 BGP 的站点需要确保本地 BGP 公告方（设备 BGP 设置）包含外部映射前缀的条目。

   **示例 2：未启用 BGP**

   * 导航到包含站点到站点 VPN 网关的虚拟中心资源。 在虚拟中心页的“连接性”下，选择“VPN (站点到站点)”。 
   * 选择通过链接 1 连接到虚拟 WAN 中心的 VPN 站点。 选择“编辑站点”并输入 10.1.1.0/24 作为 VPN 站点的专用地址空间。

     :::image type="content" source="./media/nat-rules-vpn-gateway/edit-site.png" alt-text="显示“编辑 VPN 站点”页的屏幕截图。":::

### <a name="packet-flow"></a>数据包流

在此示例中，本地设备需要访问分支虚拟网络。 数据包流如下所示，NAT 转换以粗体显示。

1. 来自本地的流量已启动。
   * 源 IP 地址：**192.168.1.1**
   * 目标 IP 地址：30.0.0.1
1. 流量进入站点到站点网关，并使用 NAT 规则进行转换，然后发送到分支。
   * 源 IP 地址：**10.1.1.1**
   * 目标 IP 地址：30.0.0.1
1. 来自分支的回复已启动。
   * 源 IP 地址：30.0.0.1
   * 目标 IP 地址：**10.1.1.1**
1. 流量进入站点到站点 VPN 网关，进行反向转换，然后发送到本地。
   * 源 IP 地址：30.0.0.1
   * 目标 IP 地址：**192.168.1.1**

### <a name="verification-checks"></a>验证清单

此部分介绍用于验证是否正确设置了配置的检查。

#### <a name="validate-defaultroutetable-rules-and-routes"></a>验证 DefaultRouteTable、规则和路由

虚拟 WAN 中的分支关联到 **DefaultRouteTable**，这意味着所有分支连接都了解在 DefaultRouteTable 中填充的路由。 在 DefaultRouteTable 的有效路由中，你会看到 NAT 规则，其中包含外部映射前缀。

示例：

* **前缀：** 10.1.1.0/24  
* **下一个跃点类型：** VPN_S2S_Gateway
* **下一个跃点：** VPN_S2S_Gateway 资源

#### <a name="validate-address-prefixes"></a>验证地址前缀

此示例适用于与 DefaultRouteTable 关联的虚拟网络中的资源。

位于分支虚拟网络（已连接到虚拟 WAN 中心）中的任何虚拟机的网络接口卡 (NIC) 上的“有效路由”还应包含 NAT 规则的 **ExternalMapping** 地址前缀。

#### <a name="validate-bgp-advertisements"></a>验证 BGP 播发

如果在 VPN 站点连接上配置了 BGP，请检查本地 BGP 发言方，确保它在为外部映射前缀播发条目。

## <a name="next-steps"></a>后续步骤

有关站点到站点配置的详细信息，请参阅[配置虚拟 WAN 站点到站点连接](virtual-wan-site-to-site-portal.md)。
