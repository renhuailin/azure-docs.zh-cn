---
author: cherylmc
ms.author: cherylmc
ms.date: 02/23/2021
ms.service: virtual-wan
ms.topic: include
ms.openlocfilehash: 567c0bb75c30a1f0ccdcde7ec1b0f04f5d6e54c5
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102048245"
---
[!INCLUDE [Portal feature rollout](virtual-wan-portal-feature-rollout.md)]

1. 导航到“所有资源”，选择已创建的虚拟 WAN，并从左侧菜单中选择“用户 VPN 配置”。
1. 在“用户 VPN 配置”页上，选择页面顶部的“+创建用户 VPN 配置”，以便打开“创建新的用户 VPN 配置”页  。

   :::image type="content" source="media/virtual-wan-p2s-configuration/user-vpn.png" alt-text="“用户 VPN 配置”页的屏幕截图。":::

1. 在“基本信息”选项卡上的“实例详细信息”下，输入要分配给 VPN 配置的“名称”。
1. 对于“隧道类型”，请从下拉列表中选择所需的隧道类型。 隧道类型选项包括：“IKEV2 VPN”、“OpenVPN”以及“OpenVPN 和 IKEv2”。
1. 使用与所选隧道类型相对应的以下步骤。 指定所有值后，单击“查看 + 创建”，然后单击“创建”以创建配置。

   **IKEv2 VPN**

   * **要求：** 选择“IKEv2”隧道类型时，会显示一条消息，指示你选择一种身份验证方法。 对于 IKEv2，你只能指定一种身份验证方法。 可以选择“Azure 证书”、“Azure Active Directory”或“基于 RADIUS 的身份验证”。

   * **IPSec 自定义参数：** 若要自定义 IKE 阶段 1 和 IKE 阶段 2 的参数，请将 IPsec 开关切换到“自定义”，然后选择参数值。 有关可自定义参数的详细信息，请参阅[自定义 IPsec](../articles/virtual-wan/point-to-site-ipsec.md) 一文。

     :::image type="content" source="media/virtual-wan-p2s-configuration/custom.png" alt-text="已切换到“自定义”的 IPsec 开关的屏幕截图。":::

   * **身份验证：** 导航到要使用的身份验证机制，具体方式是单击页面底部的“下一步”以转到身份验证方法，或单击页面顶部的相应选项卡。 将开关切换到“是”以选择该方法。

     在此示例中，选择了“RADIUS 身份验证”。 对于基于 RADIUS 的身份验证，可以提供辅助 RADIUS 服务器 IP 地址和服务器机密。

     :::image type="content" source="media/virtual-wan-p2s-configuration/ike-radius.png" alt-text="IKE 的屏幕截图。":::

   **OpenVPN**

   * **要求：** 选择“OpenVPN”隧道类型时，会显示一条消息，指示你选择一种身份验证机制。 如果选择“OpenVPN”作为隧道类型，则可以指定多种身份验证方法。 可以选择“Azure 证书”、“Azure Active Directory”和“基于 RADIUS 的身份验证”的任何子集。 对于基于 RADIUS 的身份验证，可以提供辅助 RADIUS 服务器 IP 地址和服务器机密。

   * **身份验证：** 导航到要使用的身份验证方法，具体方式是单击页面底部的“下一步”以转到身份验证方法，或单击页面顶部的相应选项卡。
   对于要选择的每种方法，请将开关切换到“是”，然后输入相应的值。

     在此示例中，选择了“Azure Active Directory”。

     :::image type="content" source="media/virtual-wan-p2s-configuration/openvpn.png" alt-text="“OpenVPN”页的屏幕截图。":::
