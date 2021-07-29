---
title: 配置 Always-On VPN 隧道
titleSuffix: Azure Virtual WAN
description: 了解如何为虚拟 WAN 配置 Always On VPN 设备隧道。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 05/26/2021
ms.author: cherylmc
ms.openlocfilehash: 69899179b65400e8a3b81f497e950aa3123c624e
ms.sourcegitcommit: e1d5abd7b8ded7ff649a7e9a2c1a7b70fdc72440
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2021
ms.locfileid: "110579730"
---
# <a name="configure-an-always-on-vpn-device-tunnel-for-virtual-wan"></a>为虚拟 WAN 配置 Always On VPN 设备隧道

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

## <a name="prerequisites"></a>先决条件

必须创建点到站点配置并编辑虚拟中心分配。 有关说明，请参阅以下部分：

* [创建 P2S 配置](virtual-wan-point-to-site-portal.md#p2sconfig)
* [使用 P2S 网关创建中心](virtual-wan-point-to-site-portal.md#hub)

## <a name="configure-the-device-tunnel"></a>配置设备隧道

[!INCLUDE [device tunnel](../../includes/vpn-gateway-vwan-always-on-device.md)]

## <a name="to-remove-a-profile"></a>删除配置文件

若要删除配置文件，请运行以下命令：

![屏幕截图显示了运行 Remove-VpnConnection -Name MachineCertTest 命令的 PowerShell 窗口。](./media/howto-always-on-device-tunnel/cleanup.png)

## <a name="next-steps"></a>后续步骤

有关虚拟 WAN 的详细信息，请参阅[常见问题解答](virtual-wan-faq.md)。