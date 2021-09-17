---
ms.author: cherylmc
author: cherylmc
ms.date: 08/19/2021
ms.service: virtual-wan
ms.topic: include
ms.openlocfilehash: 6e09f3d115ad11bc6876092b8c4e87cc56c3c22a
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/24/2021
ms.locfileid: "122779888"
---
1. 在虚拟 WAN 页的左侧窗格上，选择“中心” 。 在“中心”页上，选择“+新建中心”。

   :::image type="content" source="media/virtual-wan-p2s-hub/new-hub.png" alt-text="新建中心的屏幕截图。":::

1. 在“创建虚拟中心”页上，查看“基本信息”选项卡 。

   :::image type="content" source="media/virtual-wan-p2s-hub/create-hub.png" alt-text="创建虚拟中心的屏幕截图。":::

1. 在“基本信息”选项卡上，配置以下设置：

   * **区域**：选择要在其中部署虚拟中心的区域。
   * **名称**：想要用于称呼虚拟中心的名称。
   * **中心专用地址空间**：用 CIDR 表示法来表示的中心地址范围。

1. 单击“点到站点”选项卡，打开点到站点的配置页。 若要查看点到站点设置，请单击“是”。

   :::image type="content" source="media/virtual-wan-p2s-hub/create-point-to-site.png" alt-text="选择了点到站点的虚拟中心配置的屏幕截图。":::

1. 配置下列设置：

   * 网关缩放单元 - 这表示用户 VPN 网关的聚合容量。 如果选择 40 或更多网关缩放单元，请相应地规划客户端地址池。 有关此设置如何影响客户端地址池的信息，请参阅[关于客户端地址池](../articles/virtual-wan/about-client-address-pools.md)。 有关网关缩放单元的信息，请参阅[常见问题解答](../articles/virtual-wan/virtual-wan-faq.md#for-user-vpn-point-to-site--how-many-clients-are-supported)。
   * 点到站点配置 - 选择你在上一步中创建的用户 VPN 配置。
   * 路由首选项 - Azure 路由首选项允许你选择流量在 Azure 和 Internet 之间的路由方式。 可以选择通过 Microsoft 网络或通过 ISP 网络（公共 Internet）来路由流量。 这些选项也分别称为“冷土豆路由”和“热土豆路由”。 虚拟 WAN 中的公共 IP 地址由服务基于所选路由选项分配。 有关通过 Microsoft 网络或 ISP 设置路由首选项的详细信息，请参阅[路由首选项](../articles/virtual-network/routing-preference-overview.md)一文。
   * 客户端地址池 - 自动从中将 IP 地址分配到 VPN 客户端的地址池。 有关详细信息，请参阅[关于客户端地址池](../articles/virtual-wan/about-client-address-pools.md)。
   * 自定义 DNS 服务器 - 客户端将使用的 DNS 服务器的 IP 地址。 最多可以指定 5 个。

1. 选择“查看 + 创建”以验证自己的设置  。

1. 验证通过后，选择“创建”。 创建一个中心可能需要 30 分钟或更长时间才能完成。