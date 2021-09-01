---
title: 在 vWAN 中为 Azure VMware 解决方案配置站点到站点 VPN
description: 了解如何在 Azure VMware 解决方案中建立 VPN（IPsec IKEv1 和 IKEv2）站点到站点隧道。
ms.topic: how-to
ms.custom: contperf-fy22q1
ms.date: 06/30/2021
ms.openlocfilehash: f3ed10e2a67e40fe8f1124a88fd8e11b24b71986
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122324998"
---
# <a name="configure-a-site-to-site-vpn-in-vwan-for-azure-vmware-solution"></a>在 vWAN 中为 Azure VMware 解决方案配置站点到站点 VPN

在本文中，你会建立一个在 Microsoft Azure 虚拟 WAN 中心终止的 VPN（IPsec IKEv1 和 IKEv2）站点到站点隧道。 中心包含 Azure VMware 解决方案 ExpressRoute 网关和站点到站点 VPN 网关。 该中心将本地 VPN 设备与 Azure VMware 解决方案终结点相连接。

:::image type="content" source="media/create-ipsec-tunnel/vpn-s2s-tunnel-architecture.png" alt-text=" 为 VPN 站点到站点隧道体系结构示意图" border="false":::

## <a name="prerequisites"></a>先决条件
必须在本地 VPN 设备上终止面向公众的 IP 地址。

## <a name="create-an-azure-virtual-wan"></a>创建 Azure 虚拟 WAN

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-create-vwan-include.md)]

## <a name="create-a-virtual-hub"></a>创建虚拟中心

虚拟中心是虚拟 WAN 创建和使用的虚拟网络。 这是区域中虚拟 WAN 网络的核心。  它可以包含站点到站点和 ExpressRoute 的网关。 

>[!TIP]
>还可以[在现有中心创建网关](../virtual-wan/virtual-wan-expressroute-portal.md#existinghub)。


[!INCLUDE [Create a hub](../../includes/virtual-wan-tutorial-s2s-hub-include.md)]

## <a name="create-a-vpn-gateway"></a>创建 VPN 网关 

[!INCLUDE [Create a gateway](../../includes/virtual-wan-tutorial-s2s-gateway-include.md)]


## <a name="create-a-site-to-site-vpn"></a>创建站点到站点 VPN

1. 在 Azure 门户选择先前创建的虚拟 WAN。

2. 在虚拟中心的“概述”选择“连接性” > “VPN（站点对站点)” > “创建新 VPN 站点”。

   :::image type="content" source="media/create-ipsec-tunnel/create-vpn-site-basics.png" alt-text="虚拟中心“概述”页的屏幕截图，其中选择了“VPN (站点到站点)”和“创建新的 VPN 站点”。":::  
 
3. 在“基本信息”选项卡输入必填字段。 

   :::image type="content" source="../../includes/media/virtual-wan-tutorial-site-include/site-basics.png" alt-text="显示“创建 VPN 站点”页的屏幕截图，其中“基本信息”选项卡已打开。" lightbox="../../includes/media/virtual-wan-tutorial-site-include/site-basics.png":::

   * **区域** - 之前称为位置。 这是要在其中创建此站点资源的位置。
   
   * **名称** - 本地站点的名称。
   
   * 设备供应商 - VPN 设备供应商的名称（例如 Citrix、Cisco 或 Barracuda）. 这有助于 Azure 团队更好地了解你的环境，以便将来添加更多的可用优化选项，或帮助你进行故障排除。

   * 专用地址空间 - 位于本地站点的 CIDR IP 地址空间。 发往此地址空间的流量将路由到本地站点。 仅当没有为站点启用 [BGP](../vpn-gateway/bgp-howto.md) 时，才需要 CIDR 块。
    
   >[!NOTE]
   >如果在创建站点后编辑地址空间（例如，添加额外的地址空间），则在重新创建组件时，可能需要 8-10 分钟来更新有效路由。


1. 选择“链路”以在分支添加物理链路的信息。 如果有虚拟 WAN 合作伙伴 CPE 设备，请检查该设备，查看是否将此信息作为从其系统设置的分支信息上传的一部分与 Azure 进行交换。

   指定链接和提供程序名称可以区分最终可作为中心一部分创建的任意数量的网关。  [BGP](../vpn-gateway/vpn-gateway-bgp-overview.md) 和自治系统编号 (ASN) 在组织中必须唯一。 BGP 可确保 Azure VMware 解决方案和本地服务器在隧道中播发路由。 如果禁用，则必须手动维护需要播发的子网。 如果缺少子网，HCX 将无法形成服务网格。 
 
   >[!IMPORTANT]
   >默认情况下，Azure 会将 GatewaySubnet 前缀范围内的一个专用 IP 地址自动分配为 Azure VPN 网关上的 Azure BGP IP 地址。 如果本地 VPN 设备使用 APIPA 地址（169.254.0.1 到169.254.255.254）作为 BGP IP，则需要一个自定义 Azure APIPA BGP 地址。 Azure VPN 网关会选择自定义 APIPA 地址的前提是，相应的本地网络网关资源（本地网络）将 APIPA 地址作为 BGP 对等节点 IP。 如果本地网络网关使用常规 IP 地址（而不是 APIPA），则 Azure VPN 网关会恢复使用 GatewaySubnet 范围内的专用 IP 地址。

   :::image type="content" source="../../includes/media/virtual-wan-tutorial-site-include/site-links.png" alt-text="显示“创建 VPN 站点”页的屏幕截图，其中“链接”选项卡已打开。" lightbox="../../includes/media/virtual-wan-tutorial-site-include/site-links.png":::

1. 选择“查看 + 创建”。 

1. 导航到所需的虚拟中心，然后取消选择“中心关联”，将 VPN 站点连接到中心。
 
   :::image type="content" source="../../includes/media/virtual-wan-tutorial-site-include/connect.png" alt-text="屏幕截图显示“连接到此中心”。" lightbox="../../includes/media/virtual-wan-tutorial-site-include/connect.png":::   

## <a name="optional-create-policy-based-vpn-site-to-site-tunnels"></a>（可选）创建基于策略的 VPN 站点到站点隧道

>[!IMPORTANT]
>该步骤为可选项，仅适用于基于策略的 VPN。 

[基于策略的 VPN 设置](../virtual-wan/virtual-wan-custom-ipsec-portal.md)需要指定本地和 Azure VMware 解决方案网络，包括中心范围。  这些范围指定基于策略的 VPN 隧道本地终结点的加密域。  Azure VMware 解决方案端只要求启用基于策略的流量选择器指示器。 

1. 在 Azure 门户中转到你的虚拟 WAN 中心站点，然后在“连接”下，选择“VPN (站点到站点)” 。

2. 选择要为其设置自定义 IPsec 策略的 VPN 站点。

   :::image type="content" source="../virtual-wan/media/virtual-wan-custom-ipsec-portal/locate.png" alt-text="显示现有 VPN 站点以设置客户 IPsec 策略的屏幕截图。" lightbox="../virtual-wan/media/virtual-wan-custom-ipsec-portal/locate.png":::

3. 选择你的 VPN 站点名称，选择最右侧的“更多”(...)，然后选择“编辑 VPN 连接” 。

   :::image type="content" source="../virtual-wan/media/virtual-wan-custom-ipsec-portal/contextmenu.png" alt-text="显示现有 VPN 站点的上下文菜单的屏幕截图。" lightbox="../virtual-wan/media/virtual-wan-custom-ipsec-portal/contextmenu.png":::

   - 对于“Internet 协议安全性”(IPSec)，请选择“自定义”。

   - 对于“使用基于策略的流量选择器”，请选择“启用”。

   - 指定“IKE 阶段 1”和“IKE 阶段 2 (ipsec)”的详细信息。  

4. 将 IPsec 设置从默认设置更改为自定义设置，然后自定义 IPsec 策略。 再选择“保存”。

   :::image type="content" source="../virtual-wan/media/virtual-wan-custom-ipsec-portal/edit.png" alt-text="显示现有 VPN 站点的屏幕截图。" lightbox="../virtual-wan/media/virtual-wan-custom-ipsec-portal/edit.png":::

   流量选择器或属于基于策略的加密域的子网应是：
    
   - 虚拟 WAN 中心`/24`

   - Azure VMware 解决方案私有云`/22`

   - 已连接的 Azure 虚拟网络（如果存在）

## <a name="connect-your-vpn-site-to-the-hub"></a>将 VPN 站点连接到中心

1. 选择你的 VPN 站点名称，然后选择“连接 VPN 站点”。 

1. 在“预共享密钥”字段中，输入先前为本地终结点定义的密钥。 

   >[!TIP]
   >如果先前未定义密钥，可将此字段留空。 系统将自动为你生成密钥。 

   :::image type="content" source="../../includes/media/virtual-wan-tutorial-connect-vpn-site-include/connect.png" alt-text="屏幕截图为已准备好预共享密钥和相关设置的“虚拟中心”“连接站点”窗格。"::: 

1. 如果要在中心部署防火墙并将其作为下一个跃点，请将“传播默认路由”设置为“启用”。 

   启用后，如果在中心部署防火墙时中心已学习默认路由或其他连接的站点强制启用隧道，才会将虚拟 WAN 中心传播到连接。 默认路由不源自虚拟 WAN 中心。  

1. 选择“连接”  。 几分钟后该站点会显示连接和连接状态。

   :::image type="content" source="../../includes/media/virtual-wan-tutorial-connect-vpn-site-include/status.png" alt-text="屏幕截图为站点到站点连接和连接状态。" lightbox="../../includes/media/virtual-wan-tutorial-connect-vpn-site-include/status.png":::

   连接状态：将 VPN 站点连接到 Azure 中心 VPN 网关的连接的 Azure 资源的状态。 控制平面操作成功后，Azure VPN 网关和本地 VPN 设备会建立连接。

   连接状态：中心和 VPN 站点中 Azure VPN 网关之间的实际连接（数据路径）状态。 可以显示以下任一状态：

    * 未知：如果后端系统正在转换到另一状态，则通常会显示此状态。
    * **连接**：Azure VPN 网关正在尝试连接实际的本地 VPN 站点。
    * 已连接：Azure VPN 网关和本地 VPN 站点之间已建立连接。
    * 已断开连接：如果出于任何原因断开连接（在本地或 Azure 中），则通常会显示此状态



1. 下载 VPN 配置文件，并将其应用于本地终结点。  
   
   1. 在“VPN (站点到站点)”页上顶部附近，选择“下载 VPN 配置”。Azure 会在资源组“microsoft-network-\[location\]”中创建一个存储帐户，其中，location 是 WAN 的位置。 将配置应用到 VPN 设备后，可以删除此存储帐户。

   1. 创建后，选择相应的链接下载该文件。 

   1. 将配置应用到本地 VPN 设备。

   有关服务配置文件的详细信息，请参阅[关于 VPN 设备服务配置文件](../virtual-wan/virtual-wan-site-to-site-portal.md#about-the-vpn-device-configuration-file)。

1. 在虚拟 WAN 中心修补 Azure VMware 解决方案 ExpressRoute。 

   >[!IMPORTANT]
   >必须先创建私有云才能修补平台。 


   [!INCLUDE [request-authorization-key](includes/request-authorization-key.md)]

1. 将 Azure VMware 解决方案和 VPN 网关一起链接到虚拟 WAN 中心内。 使用上一步的授权密钥和 ExpressRoute ID（对等线路 URI）。

   1. 选择 ExpressRoute 网关，然后选择“兑换授权密钥”。

      :::image type="content" source="media/create-ipsec-tunnel/redeem-authorization-key.png" alt-text="私有云的 ExpressRoute 页的屏幕截图，其中选择了“兑换授权密钥”。":::

   1. 将授权密钥粘贴到“授权密钥”字段。

   1. 将 ExpressRoute ID 粘贴到“对等线路 URI”字段。 

   1. 选择“自动将此 ExpressRoute 线路与中心相关联”复选框。 

   1. 选择“添加”以建立链接。 

1. 通过[创建一个 NSX-T 段](./tutorial-nsx-t-network-segment.md)并在网络中预配一个 VM 来测试连接。 对本地终结点和 Azure VMware 解决方案终结点执行 Ping。

   >[!NOTE]
   >等待大约 5 分钟，然后再测试 ExpressRoute 线路后面的客户端（例如，先前创建的 VNet 中的 VM）的连接性。
