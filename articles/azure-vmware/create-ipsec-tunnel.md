---
title: 创建接入到 Azure VMware 解决方案的 IPSec 隧道
description: 了解如何创建虚拟 WAN 中心，以建立一条接入到 Azure VMware 解决方案的 IPSec 隧道。
ms.topic: how-to
ms.date: 10/02/2020
ms.openlocfilehash: 21df674862b65ef6573a8a3fcfd7538b1053f04e
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/16/2021
ms.locfileid: "103491824"
---
# <a name="create-an-ipsec-tunnel-into-azure-vmware-solution"></a>创建接入到 Azure VMware 解决方案的 IPSec 隧道

在本文中，我们将逐步建立一个在 Microsoft Azure 虚拟 WAN 中心终止的 VPN（IPsec IKEv1 和 IKEv2）站点到站点隧道。 我们将创建一个 Azure 虚拟 WAN 中心，以及一个附加了公共 IP 地址的 VPN 网关。 然后，我们将创建 Azure ExpressRoute 网关，并建立 Azure VMware 解决方案终结点。 我们还将复习有关启用基于策略的 VPN 本地设置的细节。 

## <a name="topology"></a>拓扑

![显示 VPN 站点到站点隧道体系结构的示意图。](media/create-ipsec-tunnel/vpn-s2s-tunnel-architecture.png)

Azure 虚拟中心包含 Azure VMware 解决方案 ExpressRoute 网关和站点到站点 VPN 网关。 该中心将本地 VPN 设备与 Azure VMware 解决方案终结点相连接。

## <a name="before-you-begin"></a>开始之前

若要创建站点到站点 VPN 隧道，需创建一个在本地 VPN 设备上终止的、面向公众的 IP 地址。

## <a name="create-a-virtual-wan-hub"></a>创建虚拟 WAN 中心

1. 在 Azure 门户中搜索“虚拟 WAN”。 选择“+添加”  。 此时会打开“创建 WAN”页。  

2. 在“创建 WAN”页上输入必填字段，然后选择“查看 + 创建”。 
   
   | 字段 | 值 |
   | --- | --- |
   | **订阅** | 为“值”预先填充了属于资源组的订阅。 |
   | **资源组** | 虚拟 WAN 是全局资源，不局限于特定的区域。  |
   | **资源组位置** | 若要创建虚拟 WAN 中心，需要设置资源组的位置。  |
   | **名称** |   |
   | 类型 | 选择“标准”，这样就不仅仅是允许 VPN 网关流量。  |

   :::image type="content" source="media/create-ipsec-tunnel/create-wan.png" alt-text="显示 Azure 门户中“创建 WAN”页的屏幕截图。":::

3. 在 Azure 门户中，选择在上一步骤中创建的虚拟 WAN，选择“创建虚拟中心”，输入必填字段，然后选择“下一步: 站点到站点”。  

   | 字段 | 值 |
   | --- | --- |
   | **区域** | 从管理角度讲，选择区域是必需的。  |
   | **名称** |    |
   | **中心专用地址空间** | 输入使用 `/24`（最小）的子网。  |

   :::image type="content" source="media/create-ipsec-tunnel/create-virtual-hub.png" alt-text="显示“创建虚拟中心”页的屏幕截图。":::

4. 在“站点到站点”选项卡上，通过从“网关缩放单元”下拉菜单设置聚合吞吐量来定义站点到站点网关。  

   >[!TIP]
   >1 个缩放单元 = 500 Mbps。 缩放单元是成对的，用于实现冗余，每个缩放单元支持 500 Mbps。
  
5. 在“ExpressRoute”选项卡上创建 ExpressRoute 网关。 

   >[!TIP]
   >缩放单元值为 2 Gbps。 

    创建每个中心大约需要 30 分钟。 

## <a name="create-a-vpn-site"></a>创建 VPN 站点 

1. 在 Azure 门户上的“最近的资源”中，选择在上一部分创建的虚拟 WAN。

2. 在虚拟中心的“概述”中，选择“连接” > “VPN (站点到站点)”，然后选择“创建新的 VPN 站点”。   

   :::image type="content" source="media/create-ipsec-tunnel/create-vpn-site-basics.png" alt-text="虚拟中心“概述”页的屏幕截图，其中选择了“VPN (站点到站点)”和“创建新的 VPN 站点”。":::  
 
3. 在“基本信息”选项卡上输入必填字段，然后选择“下一步: 链接”。  

   | 字段 | 值 |
   | --- | --- |
   | **区域** | 在上一部分指定的同一区域。  |
   | **名称** |  |
   | **设备供应商** |  |
   | **边界网关协议** | 设置为“启用”，确保 Azure VMware 解决方案和本地服务器通过隧道播发其路由。 如果禁用，则必须手动维护需要播发的子网。 如果缺少子网，HCX 将无法形成服务网格。 有关详细信息，请参阅[关于使用 Azure VPN 网关的 BGP](../vpn-gateway/vpn-gateway-bgp-overview.md)。 |
   | **专用地址空间**  | 输入本地 CIDR 块。  此值用于通过隧道路由所有发往本地的流量。  仅当未启用 BGP 时，才需要 CIDR 块。 |
   | **连接到** |   |

4. 在“链接”选项卡上填写必填字段，然后选择“查看 + 创建”。  指定链接和提供程序名称可以区分最终可作为中心一部分创建的任意数量的网关。 BGP 和自治系统编号 (ASN) 在组织中必须唯一。
 
## <a name="optional-defining-a-vpn-site-for-policy-based-vpn-site-to-site-tunnels"></a>（可选）为基于策略的 VPN 站点到站点隧道定义 VPN 站点

本部分仅适用于基于策略的 VPN。 在大多数情况下，基于策略的（或静态基于路由的）VPN 设置由本地 VPN 设备功能驱动。 这些设置要求指定本地和 Azure VMware 解决方案网络。 对于使用 Azure 虚拟 WAN 中心的 Azure VMware 解决方案，不能选择任一网络， 而必须指定所有相关的本地和 Azure VMware 解决方案虚拟 WAN 中心范围。 这些中心范围用于指定基于策略的 VPN 隧道本地终结点的加密域。 Azure VMware 解决方案端只要求启用基于策略的流量选择器指示器。 

1. 在 Azure 门户中转到你的虚拟 WAN 中心站点。 在“连接”下，选择“VPN (站点到站点)”。 

2. 选择你的 VPN 站点名称，选择最右侧的省略号图标 (...)，然后选择“编辑与此中心的 VPN 连接”。
 
   :::image type="content" source="media/create-ipsec-tunnel/edit-vpn-section-to-this-hub.png" alt-text="Azure 中虚拟 WAN 中心站点页的屏幕截图，其中显示已选择用于访问“编辑与此中心的 VPN 连接”的省略号图标。" lightbox="media/create-ipsec-tunnel/edit-vpn-section-to-this-hub.png":::

3. 编辑 VPN 站点与中心之间的连接，然后选择“保存”。
   - 对于“Internet 协议安全性”(IPSec)，请选择“自定义”。
   - 对于“使用基于策略的流量选择器”，请选择“启用”。
   - 指定“IKE 阶段 1”和“IKE 阶段 2 (ipsec)”的详细信息。  
 
   :::image type="content" source="media/create-ipsec-tunnel/edit-vpn-connection.png" alt-text="“编辑 VPN 连接”页的屏幕截图。"::: 
 
   流量选择器或属于基于策略的加密域的子网应是：
    
   - 虚拟 WAN 中心 /24
   - Azure VMware 解决方案私有云 /22
   - 连接的 Azure 虚拟网络（如果有）

## <a name="connect-your-vpn-site-to-the-hub"></a>将 VPN 站点连接到中心

1. 选择你的 VPN 站点名称，然后选择“连接 VPN 站点”。 
1. 在“预共享密钥”字段中，输入先前为本地终结点定义的密钥。 

   >[!TIP]
   >如果先前未定义密钥，可将此字段留空。 系统将自动为你生成密钥。 
 
   >[!IMPORTANT]
   >仅当要在中心部署一个防火墙，并且该防火墙是通过该隧道建立的连接的下一跃点时，才启用“传播默认路由”。

1. 选择“连接”。 连接状态屏幕将显示隧道创建状态。

2. 转到虚拟 WAN 的概述并打开 VPN 站点页，以下载本地终结点的 VPN 配置文件。  

3. 在虚拟 WAN 中心修补 Azure VMware 解决方案 ExpressRoute。 此步骤要求先创建私有云。

   [!INCLUDE [request-authorization-key](includes/request-authorization-key.md)]

4. 将 Azure VMware 解决方案和 VPN 网关一起链接到虚拟 WAN 中心内。 
   1. 在 Azure 门户中，打开前面创建的虚拟 WAN。 
   1. 选择创建的虚拟 WAN 中心，然后在左侧窗格中选择“ExpressRoute”。 
   1. 选择“+ 兑换授权密钥”。

      :::image type="content" source="media/create-ipsec-tunnel/redeem-authorization-key.png" alt-text="私有云的 ExpressRoute 页的屏幕截图，其中选择了“兑换授权密钥”。":::

   1. 将授权密钥粘贴到“授权密钥”字段中。
   1. 将 ExpressRoute ID 粘贴到“对等线路 URI”字段中。 
   1. 选择“自动将此 ExpressRoute 线路与中心相关联”。 
   1. 选择“添加”以建立链接。 

5. 通过[创建一个 NSX-T 段](./tutorial-nsx-t-network-segment.md)并在网络中预配一个 VM 来测试连接。 对本地终结点和 Azure VMware 解决方案终结点执行 Ping。
