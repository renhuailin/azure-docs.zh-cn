---
title: 在 vWAN 中为 Azure VMware 解决方案配置站点到站点 VPN
description: 了解如何在 Azure VMware 解决方案中建立 VPN（IPsec IKEv1 和 IKEv2）站点到站点隧道。
ms.topic: how-to
ms.date: 06/11/2021
ms.openlocfilehash: f3fbd3d9507e0203bc58494c2c1a748f1be7e585
ms.sourcegitcommit: 942a1c6df387438acbeb6d8ca50a831847ecc6dc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2021
ms.locfileid: "112021389"
---
# <a name="configure-a-site-to-site-vpn-in-vwan-for-azure-vmware-solution"></a>在 vWAN 中为 Azure VMware 解决方案配置站点到站点 VPN

在本文中，我们将逐步建立一个在 Microsoft Azure 虚拟 WAN 中心终止的 VPN（IPsec IKEv1 和 IKEv2）站点到站点隧道。 中心包含 Azure VMware 解决方案 ExpressRoute 网关和站点到站点 VPN 网关。 该中心将本地 VPN 设备与 Azure VMware 解决方案终结点相连接。

:::image type="content" source="media/create-ipsec-tunnel/vpn-s2s-tunnel-architecture.png" alt-text=" 为 VPN 站点到站点隧道体系结构示意图" border="false":::

在操作方法部分，你将：

- 使用附加公共 IP 地址创建 Azure 虚拟 WAN 中心和 VPN 网关。 

- 创建 Azure ExpressRoute 网关并建立 Azure VMware 解决方案终结点。 

- 启用基于策略的 VPN 本地设置。 

## <a name="prerequisites"></a>先决条件
必须在本地 VPN 设备上终止面向公众的 IP 地址。

## <a name="step-1-create-an-azure-virtual-wan"></a>步骤 1。 创建 Azure 虚拟 WAN

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-create-vwan-include.md)]

## <a name="step-2-create-a-virtual-wan-hub-and-gateway"></a>步骤 2。 创建虚拟 WAN 中心和网关

>[!TIP]
>还可以[在现有中心创建网关](../virtual-wan/virtual-wan-expressroute-portal.md#existinghub)。

1. 选择上一步创建的虚拟 WAN。

1. 选择“创建虚拟中心”，输入必填字段，然后选择“下一步：站点到站点”。 

   输入使用 `/24`（最小）的子网。

   :::image type="content" source="media/create-ipsec-tunnel/create-virtual-hub.png" alt-text="显示“创建虚拟中心”页的屏幕截图。":::

4. 选择“站点到站点”选项卡，在“网关缩放单元”下拉菜单设置聚合吞吐量，以此定义站点到站点网关。 

   >[!TIP]
   >缩放单元成对冗余，每个单元均支持 500 Mbps（一个缩放单元=500 Mbps)。 
  
   :::image type="content" source="../../includes/media/virtual-wan-tutorial-hub-include/site-to-site.png" alt-text="屏幕截图为站点到站点的详细信息。":::

5. 选择 "ExpressRoute"选项卡并创建 ExpressRoute 网关。 

   :::image type="content" source="../../includes/media/virtual-wan-tutorial-er-hub-include/hub2.png" alt-text="ExpressRoute 设置屏幕截图。":::

   >[!TIP]
   >缩放单元值为 2 Gbps。 

    创建每个中心大约需要 30 分钟。 

## <a name="step-3-create-a-site-to-site-vpn"></a>步骤 3. 创建站点到站点 VPN

1. 在 Azure 门户选择先前创建的虚拟 WAN。

2. 在虚拟中心的“概述”选择“连接性” > “VPN（站点对站点)” > “创建新 VPN 站点”。

   :::image type="content" source="media/create-ipsec-tunnel/create-vpn-site-basics.png" alt-text="虚拟中心“概述”页的屏幕截图，其中选择了“VPN (站点到站点)”和“创建新的 VPN 站点”。":::  
 
3. 在“基本信息”选项卡输入必填字段。 

   :::image type="content" source="media/create-ipsec-tunnel/create-vpn-site-basics2.png" alt-text="新 VPN 站点的“基本信息”选项卡屏幕截图。" lightbox="media/create-ipsec-tunnel/create-vpn-site-basics2.png":::  

   1. 在列表中选择 **区域**。

   1. 提供站点到站点 VPN 的 **名称**。

   1. 提供本地 VPN 设备的 **设备供应商**，例如 Cisco。
   
   1. 提供 **专用地址空间**。 使用本地 CIDR 块，通过隧道路由所有发往本地的流量。 只有当你无需[在 Azure VPN 网关上配置边界网关协议 (BGP)](../vpn-gateway/bgp-howto.md) 时才需要配置 CIDR 块

1. 选择“下一步：链接”并填写必填字段。 指定链接和提供程序名称可以区分最终可作为中心一部分创建的任意数量的网关。  [BGP](../vpn-gateway/vpn-gateway-bgp-overview.md) 和自治系统编号 (ASN) 在组织中必须唯一。 BGP 可确保 Azure VMware 解决方案和本地服务器在隧道中播发路由。 如果禁用，则必须手动维护需要播发的子网。 如果缺少子网，HCX 将无法形成服务网格。 
 
   >[!IMPORTANT]
   >默认情况下，Azure 会将 GatewaySubnet 前缀范围内的一个专用 IP 地址自动分配为 Azure VPN 网关上的 Azure BGP IP 地址。 如果本地 VPN 设备使用 APIPA 地址（169.254.0.1 到169.254.255.254）作为 BGP IP，则需要一个自定义 Azure APIPA BGP 地址。 Azure VPN 网关会选择自定义 APIPA 地址的前提是，相应的本地网络网关资源（本地网络）将 APIPA 地址作为 BGP 对等节点 IP。 如果本地网络网关使用常规 IP 地址（而不是 APIPA），则 Azure VPN 网关会恢复使用 GatewaySubnet 范围内的专用 IP 地址。

   :::image type="content" source="media/create-ipsec-tunnel/create-vpn-site-links.png" alt-text="屏幕截图为链接的详细信息。" lightbox="media/create-ipsec-tunnel/create-vpn-site-links.png":::

1. 选择“查看 + 创建”。 

1. 导航到所需的虚拟中心，然后取消选择“中心关联”，将 VPN 站点连接到中心。
 
   :::image type="content" source="../../includes/media/virtual-wan-tutorial-site-include/connect.png" alt-text="屏幕截图为已准备好预共享密钥和相关设置的“虚拟中心”“连接站点”窗格。":::   

## <a name="step-4-optional-create-policy-based-vpn-site-to-site-tunnels"></a>步骤 4. （可选）创建基于策略的 VPN 站点到站点隧道

>[!IMPORTANT]
>该步骤为可选项，仅适用于基于策略的 VPN。 

基于策略的 VPN 设置需要指定本地和 Azure VMware 解决方案网络，包括中心范围。  中心范围指定基于策略的 VPN 隧道本地终结点的加密域。  Azure VMware 解决方案端只要求启用基于策略的流量选择器指示器。 

1. 在 Azure 门户中转到你的虚拟 WAN 中心站点。 在“连接”下，选择“VPN (站点到站点)”。 

2. 选择你的 VPN 站点名称，选择最右侧的省略号图标 (...)，然后选择“编辑与此中心的 VPN 连接”。
 
   :::image type="content" source="media/create-ipsec-tunnel/edit-vpn-section-to-this-hub.png" alt-text="Azure 中虚拟 WAN 中心站点页的屏幕截图，其中显示已选择用于访问“编辑与此中心的 VPN 连接”的省略号图标。" lightbox="media/create-ipsec-tunnel/edit-vpn-section-to-this-hub.png":::

3. 编辑 VPN 站点与中心之间的连接，然后选择“保存”。

   - 对于“Internet 协议安全性”(IPSec)，请选择“自定义”。

   - 对于“使用基于策略的流量选择器”，请选择“启用”。

   - 指定“IKE 阶段 1”和“IKE 阶段 2 (ipsec)”的详细信息。  
 
   :::image type="content" source="media/create-ipsec-tunnel/edit-vpn-connection.png" alt-text="“编辑 VPN 连接”页的屏幕截图。"::: 
 
   流量选择器或属于基于策略的加密域的子网应是：
    
   - 虚拟 WAN 中心`/24`

   - Azure VMware 解决方案私有云`/22`

   - 已连接的 Azure 虚拟网络（如果存在）

## <a name="step-5-connect-your-vpn-site-to-the-hub"></a>步骤 5。 将 VPN 站点连接到中心

1. 选择你的 VPN 站点名称，然后选择“连接 VPN 站点”。 

1. 在“预共享密钥”字段中，输入先前为本地终结点定义的密钥。 

   >[!TIP]
   >如果先前未定义密钥，可将此字段留空。 系统将自动为你生成密钥。 

   :::image type="content" source="../../includes/media/virtual-wan-tutorial-connect-vpn-site-include/connect.png" alt-text="屏幕截图为已准备好预共享密钥和相关设置的“虚拟中心”“连接站点”窗格。"::: 

1. 如果要在中心部署防火墙并将其作为下一个跃点，请将“传播默认路由”设置为“启用”。 

   启用后，如果在中心部署防火墙时中心已学习默认路由或其他连接的站点强制启用隧道，才会将虚拟 WAN 中心传播到连接。 默认路由不源自虚拟 WAN 中心。  

1. 选择“连接”  。 几分钟后该站点会显示连接和连接状态。

   :::image type="content" source="../../includes/media/virtual-wan-tutorial-connect-vpn-site-include/status.png" alt-text="屏幕截图为站点到站点连接和连接状态。" lightbox="../../includes/media/virtual-wan-tutorial-connect-vpn-site-include/status.png":::

1. [下载本地终结点的 VPN 配置文件](../virtual-wan/virtual-wan-site-to-site-portal.md#device)。  

3. 在虚拟 WAN 中心修补 Azure VMware 解决方案 ExpressRoute。 

   >[!IMPORTANT]
   >必须先创建私有云才能修补平台。 

   [!INCLUDE [request-authorization-key](includes/request-authorization-key.md)]

4. 将 Azure VMware 解决方案和 VPN 网关一起链接到虚拟 WAN 中心内。 使用上一步的授权密钥和 ExpressRoute ID（对等线路 URI）。

   1. 选择 ExpressRoute 网关，然后选择“兑换授权密钥”。

      :::image type="content" source="media/create-ipsec-tunnel/redeem-authorization-key.png" alt-text="私有云的 ExpressRoute 页的屏幕截图，其中选择了“兑换授权密钥”。":::

   1. 将授权密钥粘贴到“授权密钥”字段。

   1. 将 ExpressRoute ID 粘贴到“对等线路 URI”字段。 

   1. 选择“自动将此 ExpressRoute 线路与中心相关联”复选框。 

   1. 选择“添加”以建立链接。 

5. 通过[创建一个 NSX-T 段](./tutorial-nsx-t-network-segment.md)并在网络中预配一个 VM 来测试连接。 对本地终结点和 Azure VMware 解决方案终结点执行 Ping。
