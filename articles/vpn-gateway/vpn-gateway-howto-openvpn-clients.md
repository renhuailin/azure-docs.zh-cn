---
title: 如何为 P2S VPN 网关配置 OpenVPN 客户端
titleSuffix: Azure VPN Gateway
description: 了解如何为 Azure VPN 网关配置 OpenVPN 客户端。 本文有助于配置 Windows、Linux、iOS 和 Mac 客户端。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 04/29/2021
ms.author: cherylmc
ms.openlocfilehash: 5a7b125ef89d5d598e22fc4458b5b1c7f8c4a41c
ms.sourcegitcommit: fc9fd6e72297de6e87c9cf0d58edd632a8fb2552
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2021
ms.locfileid: "108289347"
---
# <a name="configure-openvpn-clients-for-azure-vpn-gateway"></a>为 Azure VPN 网关配置 OpenVPN 客户端

本文可帮助你配置 OpenVPN &reg; 协议  客户端。

## <a name="before-you-begin"></a>准备阶段

验证你已完成为 VPN 网关配置 OpenVPN 的步骤。 有关详细信息，请参阅[为 Azure VPN 网关配置 OpenVPN](vpn-gateway-howto-openvpn.md)。

[!INCLUDE [configuration steps](../../includes/vpn-gateway-vwan-config-openvpn-clients.md)]

## <a name="next-steps"></a>后续步骤

如果希望 VPN 客户端能够访问另一个 VNet 中的资源，请遵照 [VNet 到 VNet](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) 文章中的说明设置 VNet 到 VNet 连接。 确保在网关和连接中启用 BGP，否则流量不会流动。

**“OpenVPN”是 OpenVPN Inc. 的商标。**
