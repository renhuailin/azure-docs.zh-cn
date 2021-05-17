---
title: 为 Azure 虚拟 WAN 配置 OpenVPN 客户端
description: 本文可帮助你为 Azure 虚拟 WAN 配置 OpenVPN 客户端。 其中包括 Windows、Mac、iOS 和 Linux 客户端配置步骤。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 04/27/2021
ms.author: cherylmc
ms.openlocfilehash: 91b4a9a69912f7a5980348cc2b9a874673fa0eb2
ms.sourcegitcommit: 62e800ec1306c45e2d8310c40da5873f7945c657
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2021
ms.locfileid: "108163384"
---
# <a name="configure-an-openvpn-client-for-azure-virtual-wan"></a>为 Azure 虚拟 WAN 配置 OpenVPN 客户端

本文可帮助你配置 OpenVPN &reg; 协议  客户端。 还可以通过 OpenVPN 协议使用适用于 Windows 10 的 Azure VPN 客户端进行连接。 可在[此处](openvpn-azure-ad-client.md)找到更多说明

## <a name="before-you-begin"></a>准备阶段

创建用户 VPN（点到站点）配置。 请确保选择“OpenVPN”作为隧道类型。 有关步骤，请参阅[为 Azure 虚拟 WAN 创建 P2S 配置](virtual-wan-point-to-site-portal.md#p2sconfig)。

[!INCLUDE [configuration steps](../../includes/vpn-gateway-vwan-config-openvpn-clients.md)]

## <a name="next-steps"></a>后续步骤

有关用户 VPN（点到站点）的详细信息，请参阅[创建用户 VPN 连接](virtual-wan-point-to-site-portal.md)。

**“OpenVPN”是 OpenVPN Inc. 的商标。**
