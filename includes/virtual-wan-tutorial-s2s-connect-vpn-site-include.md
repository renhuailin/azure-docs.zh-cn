---
ms.author: cherylmc
author: cherylmc
ms.date: 08/18/2021
ms.service: virtual-wan
ms.topic: include
ms.openlocfilehash: 53c6980ea6e5d868a842b1e14031f27ff51f655a
ms.sourcegitcommit: d43193fce3838215b19a54e06a4c0db3eda65d45
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2021
ms.locfileid: "122638234"
---
1. 导航到“虚拟中心”->“VPN (站点到站点)”。

1. 可能需要单击“中心关联: 连接到此中心”以便清除筛选器并查看站点。

1. 选中要连接的站点的复选框，然后单击“连接 VPN 站点”。

   :::image type="content" source="./media/virtual-wan-tutorial-connect-vpn-site-include/connect-site.png" alt-text="屏幕截图显示“连接站点”。":::

1. 在“连接站点”页上，配置所需的设置。

   :::image type="content" source="./media/virtual-wan-tutorial-connect-vpn-site-include/connect.png" alt-text="屏幕截图显示“虚拟中心”的“连接站点”窗格，已准备好预共享密钥和相关设置。":::

   * 预共享密钥 (PSK)：输入 VPN 设备使用的预共享密钥。 如果未输入密钥，Azure 会自动生成一个。 随后在配置 VPN 设备时会使用该密钥。
   * 协议和 IPsec：可以保留协议的默认设置 (IKEv2) 和 IPsec 的默认设置（默认值），也可以配置自定义设置。 有关详细信息，请参阅[默认/自定义 IPsec](../articles/virtual-wan/virtual-wan-ipsec.md)。
   * 传播默认路由：仅当知道要传播默认路由时，才将此设置更改为“启用”。 否则，请将其保留为“禁用”。 始终可以在以后修改此设置。 
   
     “启用”选项允许虚拟中心将获知的默认路由传播到此连接  。 只有当虚拟 WAN 中心由于在中心部署防火墙而获知默认路由或另一个连接的站点已启用强制隧道时，此标志才会将默认路由传播到连接。 默认路由不源自虚拟 WAN 中心。 
   * 使用基于策略的流量选择器：将此设置保留为“禁用”，除非配置与使用此设置的设备的连接。

1. 在页面底部，单击“连接”。

1. 单击“连接”后，连接状态会显示“正在更新” 。 在更新完成之后，站点会显示连接和连接状态。

   :::image type="content" source="./media/virtual-wan-tutorial-connect-vpn-site-include/connectivity-status.png" alt-text="屏幕截图显示站点到站点连接和连接状态。" lightbox="./media/virtual-wan-tutorial-connect-vpn-site-include/connectivity-status.png":::

   连接预配状态：这是将 VPN 站点连接到 Azure 中心 VPN 网关的连接的 Azure 资源状态。 控制平面操作成功后，Azure VPN 网关和本地 VPN 设备将继续建立连接。

   连接状态：这是中心和 VPN 站点中 Azure VPN 网关之间的实际连接（数据路径）状态。 更新完成后，它可以显示以下任何状态：

    * **未知**：如果后端系统正在转换到另一状态，则通常会显示此状态。
    * 正在连接：Azure VPN 网关正在尝试连接实际的本地 VPN 站点。
    * 已连接：VPN 网关和本地 VPN 站点之间已建立连接。
   * 未连接：未建立连接。
    * **断开连接**：如果（在本地或 Azure 中）出于任何原因连接断开，则会显示此状态。
1. 如果要对站点进行更改，请选择站点，然后单击“...”上下文菜单。

   :::image type="content" source="./media/virtual-wan-tutorial-connect-vpn-site-include/edit.png" alt-text="屏幕截图显示编辑、删除和下载。" lightbox="./media/virtual-wan-tutorial-connect-vpn-site-include/expand/edit.png":::

   在此页中，可以执行以下操作： 

   * 编辑或删除 VPN 连接。
   * 删除指向此中心的 VPN 连接。
   * 下载特定于分支的配置以获取有关 Azure 站点的详细信息。 如果要下载与中心内所有连接的站点相关的配置文件，请改为从页面顶部的菜单中选择“下载 VPN 配置”。
