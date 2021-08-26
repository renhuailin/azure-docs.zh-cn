---
author: cherylmc
ms.author: cherylmc
ms.date: 07/30/2021
ms.service: virtual-wan
ms.topic: include
ms.openlocfilehash: 21bda32ddb1c87bb07b6679499e648e0b2f7a809
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121734631"
---
1. 导航到你已创建的虚拟 WAN。 

1. 从左侧菜单中选择“用户 VPN 配置”。

1. 在“用户 VPN 配置”页上，选择“+ 创建用户 VPN 配置” 。

   :::image type="content" source="media/virtual-wan-p2s-configuration/user-vpn.png" alt-text="“用户 VPN 配置”页的屏幕截图。":::

1. 在“创建新用户 VPN 配置”页的“基本信息”选项卡上，在“实例详细信息”下，输入要分配给 VPN 配置的“名称”   。 

   :::image type="content" source="media/virtual-wan-p2s-configuration/custom.png" alt-text="已切换到“自定义”的 IPsec 开关的屏幕截图。":::

1. 对于“隧道类型”，请从下拉列表中选择所需的隧道类型。 隧道类型选项包括：“IKEv2 VPN、OpenVPN”以及“OpenVpn 和 IKEv2” 。 每种隧道类型具有不同的必需设置。

   要求和参数：

     **IKEv2 VPN**

     * **要求：** 选择“IKEv2”隧道类型时，会显示一条消息，指示你选择一种身份验证方法。 对于 IKEv2，你只能指定一种身份验证方法。 可以选择“Azure 证书”、“Azure Active Directory”或“基于 RADIUS 的身份验证”。

     * **IPSec 自定义参数：** 若要自定义 IKE 阶段 1 和 IKE 阶段 2 的参数，请将 IPsec 开关切换到“自定义”，然后选择参数值。 有关可自定义参数的详细信息，请参阅[自定义 IPsec](../articles/virtual-wan/point-to-site-ipsec.md) 一文。

     **OpenVPN**

     * **要求：** 选择“OpenVPN”隧道类型时，会显示一条消息，指示你选择一种身份验证机制。 如果选择“OpenVPN”作为隧道类型，则可以指定多种身份验证方法。 可以选择“Azure 证书”、“Azure Active Directory”和“基于 RADIUS 的身份验证”的任何子集。 对于基于 RADIUS 的身份验证，可以提供辅助 RADIUS 服务器 IP 地址和服务器机密。

1. 配置要使用的身份验证方法。 每种身份验证方法都位于单独的选项卡中：“Azure 证书”、“RADIUS 身份验证”和“Azure Active Directory”  。 某些身份验证方法仅适用于特定隧道类型。

   在要配置的身份验证方法选项卡上，选择“是”以显示可用的配置设置。

   :::image type="content" source="media/virtual-wan-p2s-configuration/certificate-no.png" alt-text="“否”屏幕的屏幕截图 - 突出显示“是”":::。

   * 示例 - 证书身份验证

      :::image type="content" source="media/virtual-wan-p2s-configuration/certificate-authentication.png" alt-text="选择了“是”的屏幕截图。":::

   * 示例 - RADIUS 身份验证

      :::image type="content" source="media/virtual-wan-p2s-configuration/radius-authentication.png" alt-text="RADIUS 身份验证页的屏幕截图。":::

   * 示例 - Azure Active Directory 身份验证

      :::image type="content" source="media/virtual-wan-p2s-configuration/azure-active-directory.png" alt-text="Azure Active Directory 身份验证页。":::

1. 完成配置设置后，单击页面底部的“查看 + 创建”。

1. 单击“创建”以创建用户 VPN 配置。
