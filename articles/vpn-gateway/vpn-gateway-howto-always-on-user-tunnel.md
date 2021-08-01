---
title: 配置 Always-On VPN 用户隧道
titleSuffix: Azure VPN Gateway
description: 了解如何为 VPN 网关配置 Always On VPN 用户隧道。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 04/29/2021
ms.author: cherylmc
ms.openlocfilehash: 59c12333c6cf8ea9d251ce6387e3dce4ff23468b
ms.sourcegitcommit: fc9fd6e72297de6e87c9cf0d58edd632a8fb2552
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2021
ms.locfileid: "108287313"
---
# <a name="configure-an-always-on-vpn-user-tunnel"></a>配置 Always On VPN 用户隧道

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

## <a name="configure-the-gateway"></a>配置网关

 按照[配置点到站点 VPN 连接](vpn-gateway-howto-point-to-site-resource-manager-portal.md)一文中的说明，将 VPN 网关配置为使用 IKEv2 和基于证书的身份验证。

## <a name="configure-a-user-tunnel"></a>配置用户隧道

[!INCLUDE [user configuration](../../includes/vpn-gateway-vwan-always-on-user.md)]

## <a name="to-remove-a-profile"></a>删除配置文件

若要删除配置文件，请执行以下步骤：

1. 运行以下命令：

   ```powershell
   C:\> Remove-VpnConnection UserTest  
   ```

1. 断开连接，清除“自动连接”复选框。 

   ![清理](./media/vpn-gateway-howto-always-on-user-tunnel/disconnect.jpg)

## <a name="next-steps"></a>后续步骤

若要排查可能会发生的任何连接问题，请参阅 [Azure 点到站点连接问题](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)。
