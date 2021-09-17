---
ms.author: cherylmc
author: cherylmc
ms.date: 08/17/2021
ms.service: virtual-wan
ms.topic: include
ms.openlocfilehash: 5f52297778ec6dc30e96aeac00cd8751b6b39582
ms.sourcegitcommit: d43193fce3838215b19a54e06a4c0db3eda65d45
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2021
ms.locfileid: "122638204"
---
1. 导航到“虚拟 WAN”->“VPN 站点”以打开“VPN 站点”页面 。
1. 在“VPN 站点”页上，单击“+创建站点”。
1. 在“创建 VPN 站点”页的“基本信息”选项卡上，填写以下字段 ：

   :::image type="content" source="./media/virtual-wan-tutorial-site-include/site-basics.png" alt-text="屏幕截图显示“创建 VPN 站点”页，其中“基本信息”选项卡已打开。" lightbox="./media/virtual-wan-tutorial-site-include/site-basics.png":::

    * **区域**：以前称为位置。 这是要在其中创建此站点资源的位置。
    * **名称**：要用于指代本地站点的名称。
    * **设备供应商**：VPN 设备供应商的名称（例如：Citrix、Cisco、Barracuda）。 添加设备供应商有助于 Azure 团队更好地了解你的环境，以便将来添加更多的可用优化选项，或帮助你进行故障排除。
    * **专用地址空间**：位于本地站点的 IP 地址空间。 发往此地址空间的流量将路由到本地站点。 如果没有为站点启用 BGP，则必需填写此字段。
    
      >[!NOTE]
      >如果在创建站点后编辑地址空间（例如，添加额外的地址空间），则在重新创建组件时，可能需要 8-10 分钟来更新有效路由。
      >
1. 选择“链路”以在分支添加物理链路的信息。 如果有虚拟 WAN 合作伙伴 CPE 设备，请检查该设备，查看是否将此信息作为从其系统设置的分支信息上传的一部分与 Azure 进行交换。

   :::image type="content" source="./media/virtual-wan-tutorial-site-include/links.png" alt-text="屏幕截图显示“创建 VPN 站点”页，其中“链接”选项卡已打开。" lightbox="./media/virtual-wan-tutorial-site-include/links.png":::

   * **链路名称**：要在 VPN 站点为物理链路提供的名称。 例如：mylink1。
   * **速度**：这是 VPN 设备在分支位置的速度。 示例：50 表示 VPN 设备在分支站点的速度为 50 Mbps。
   * **链路提供程序名称**：物理链路在 VPN 站点的名称。 示例：ATT、Verizon。
   * **链路 IP 地址/FQDN**：使用此链路的本地设备的公共 IP 地址。 可以根据需要提供 ExpressRoute 后的本地 VPN 设备的专用 IP 地址。 你还可包括完全限定的域名。 例如 something.contoso.com。 应可从 VPN 网关解析 FQDN。 如果可通过 Internet 访问托管此 FQDN 的 DNS 服务器，此操作则是可行的。 如果同时指定了 IP 地址和 FQDN，将优先采用 IP 地址。

     >[!NOTE]
     >
     >* 每个 FQDN 支持一个 IPv4 地址。 如果 FQDN 要被解析为多个 IP 地址，VPN 网关将从列表中选取第一个 IP4 地址。 目前不支持 IPv6 地址。
     >
     >* VPN 网关维护一个 DNS 缓存，该缓存每 5 分钟刷新一次。 此网关仅尝试解析已断开连接的隧道的 FQDN。 重置网关或配置更改也可触发 FQDN 解析。
     >
   * **链路边界网关协议**：在虚拟 WAN 链路上配置 BGP 等同于在 Azure 虚拟网络网关 VPN 上配置 BGP。 本地 BGP 对等节点地址不能与 VPN 到设备的公共 IP 地址或 VPN 站点的 VNet 地址空间相同。 在 VPN 设备上对 BGP 对等节点 IP 使用不同的 IP 地址。 它可以是分配给该设备上环回接口的地址。 在表示该位置的相应 VPN 站点中指定此地址。  有关 BGP 先决条件，请参阅[关于 Azure VPN 网关的 BGP](../articles/vpn-gateway/vpn-gateway-bgp-overview.md)。 可随时编辑 VPN 链路连接来更新其 BGP 参数（链路上的对等互连 IP 和 AS 编号）。
1. 可添加或删除更多链路。 支持每个 VPN 站点四条链路。 例如，如果在分支位置有四个 ISP（Internet 服务提供商），则可以创建四个链接。每个 ISP 一个链接，并为每个链接提供信息。
1. 填写完这些字段后，选择“查看 + 创建”进行验证。 单击“创建”以创建站点。
1. 在“VPN 站点”页上，单击“中心关联: 连接到此中心”以清除筛选器 。

   :::image type="content" source="./media/virtual-wan-tutorial-site-include/connect.png" alt-text="屏幕截图显示“连接到此中心”。" lightbox="./media/virtual-wan-tutorial-site-include/connect.png":::
1. 清除筛选器后，即可查看站点。

   :::image type="content" source="./media/virtual-wan-tutorial-site-include/sites.png" alt-text="显示站点的屏幕截图。":::
