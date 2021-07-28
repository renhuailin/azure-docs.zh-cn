---
title: 为 Azure 虚拟 WAN 配置 OpenVPN 客户端
description: 了解如何为 Azure 虚拟 WAN 配置 OpenVPN 客户端。 本文包括 Windows、Mac、iOS 和 Linux 客户端配置步骤。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 04/27/2021
ms.author: cherylmc
ms.openlocfilehash: 83c70aca81eaa888186807d43fff5a7bbaccb700
ms.sourcegitcommit: e1d5abd7b8ded7ff649a7e9a2c1a7b70fdc72440
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2021
ms.locfileid: "110579594"
---
# <a name="configure-an-openvpn-client-for-azure-virtual-wan"></a>为 Azure 虚拟 WAN 配置 OpenVPN 客户端

本文可帮助你配置 OpenVPN &reg; 协议  客户端。 还可以通过 OpenVPN 协议使用适用于 Windows 10 的 Azure VPN 客户端进行连接。 有关详细信息，请参阅[为 P2S OpenVPN 连接配置 VPN 客户端](openvpn-azure-ad-client.md)。

## <a name="before-you-begin"></a>准备阶段

创建用户 VPN（点到站点）配置。 请确保选择“OpenVPN”作为隧道类型。 有关步骤，请参阅[为 Azure 虚拟 WAN 创建 P2S 配置](virtual-wan-point-to-site-portal.md#p2sconfig)。

[!INCLUDE [configuration steps](../../includes/vpn-gateway-vwan-config-openvpn-clients.md)]

## <a name="next-steps"></a>后续步骤

有关用户 VPN（点到站点）的详细信息，请参阅[创建用户 VPN 连接](virtual-wan-point-to-site-portal.md)。

**“OpenVPN”是 OpenVPN Inc. 的商标。**
