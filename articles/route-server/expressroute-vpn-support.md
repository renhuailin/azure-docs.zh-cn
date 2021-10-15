---
title: 关于 ExpressRoute 和 Azure VPN 的 Azure 路由服务器支持
description: 了解 Azure 路由服务器如何与 ExpressRoute 和 Azure VPN 网关进行交互。
services: route-server
author: duongau
ms.service: route-server
ms.topic: conceptual
ms.date: 10/01/2021
ms.author: duau
ms.openlocfilehash: 61a712fa747252b07a513cfa6e18e3f3b4a67e6c
ms.sourcegitcommit: 03e84c3112b03bf7a2bc14525ddbc4f5adc99b85
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/03/2021
ms.locfileid: "129400717"
---
# <a name="about-azure-route-server-support-for-expressroute-and-azure-vpn"></a>关于 ExpressRoute 和 Azure VPN 的 Azure 路由服务器支持

Azure 路由服务器不仅支持在 Azure 上运行的第三方网络虚拟设备 (NVA)，还支持与 ExpressRoute 和 Azure VPN 网关进行无缝集成。 无需在网关与 Azure 路由服务器之间配置或管理 BGP 对等互连。 通过简单的[配置更改](quickstart-configure-route-server-powershell.md#route-exchange)，就能在网关和 Azure 路由服务器之间启用路由交换。


## <a name="how-does-it-work"></a>它是如何工作的？

默认情况下，在虚拟网络中部署 Azure 路由服务器以及 ExpressRoute 网关和 NVA 时，Azure 路由服务器不会互相传播它从 NVA 和 ExpressRoute 网关接收到的路由。 启用路由交换后，ExpressRoute 和 NVA 将了解彼此的路由。

以下面的示意图为例：

* SDWAN 设备会从 Azure 路由服务器接收来自“On-prem 2”的路由（该路由连接到 ExpressRoute）以及虚拟网络路由。

* ExpressRoute 网关将接收来自“On-prem 1”的路由（该路由连接到 SDWAN 设备）以及来自 Azure 路由服务器的虚拟网络路由。

    ![示意图：使用路由服务器配置的 ExpressRoute。](./media/expressroute-vpn-support/expressroute-with-route-server.png)

还可以将 SDWAN 设备替换为 Azure VPN 网关。 由于 Azure VPN 网关和 ExpressRoute 是完全托管的，因此只需为两个本地网络启用路由交换，以便彼此通信。

> [!IMPORTANT] 
> 必须在[主动-主动](../vpn-gateway/vpn-gateway-activeactive-rm-powershell.md)模式下配置 Azure VPN 网关，并将 ASN 设置为 65515。
>

![示意图：使用路由服务器配置的 ExpressRoute 和 VPN 网关。](./media/expressroute-vpn-support/expressroute-and-vpn-with-route-server.png)

## <a name="next-steps"></a>后续步骤

- 详细了解 [Azure 路由服务器](route-server-faq.md)。
- 了解如何[配置 Azure 路由服务器](quickstart-configure-route-server-powershell.md)。
- 详细了解 [Azure ExpressRoute 和 Azure VPN 共存](../expressroute/expressroute-howto-coexist-resource-manager.md)。
