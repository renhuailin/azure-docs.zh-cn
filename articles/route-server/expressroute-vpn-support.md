---
title: 关于 Azure 路由服务器 (预览版) 支持 ExpressRoute 和 Azure VPN
description: 了解 Azure 路由服务器如何与 ExpressRoute 和 Azure VPN 网关交互。
services: route-server
author: duongau
ms.service: route-server
ms.topic: conceptual
ms.date: 03/02/2021
ms.author: duau
ms.openlocfilehash: 6e588c7c0381c6825bcf75cbbe28a1dd6b865940
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101679210"
---
# <a name="about-azure-route-server-preview-support-for-expressroute-and-azure-vpn"></a>关于 Azure 路由服务器 (预览版) 支持 ExpressRoute 和 Azure VPN

Azure 路由服务器不仅支持 (NVA) 在 Azure 上运行的第三方网络虚拟设备，还支持与 ExpressRoute 和 Azure VPN 网关无缝集成。 不需要在网关和 Azure 路由服务器之间配置或管理 BGP 对等互连。 可以在网关与 Azure 路由服务器之间启用路由交换，只需要进行一次简单的 [配置更改](quickstart-configure-route-server-powershell.md#route-exchange)。

> [!IMPORTANT]
> Azure 路由服务器 (预览版) 目前为公共预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="how-does-it-work"></a>它是如何工作的？

在默认情况下，在部署 Azure 路由服务器以及 ExpressRoute 网关和虚拟网络中的 NVA 时，Azure 路由服务器不会将其从 NVA 和 ExpressRoute 网关接收的路由传播到两者之间。 启用路由交换后，ExpressRoute 和 NVA 将了解彼此的路由。

例如，在下图中：

* SDWAN 设备将从 Azure 路由服务器接收路由，该路由从连接到 ExpressRoute 的 "本地 2"，以及虚拟网络路由。

* ExpressRoute 网关将接收来自 "本地 1" 的路由，该路由连接到 SDWAN 设备，同时还从 Azure 路由服务器接收虚拟网络路由。

    ![显示已配置路由服务器的 ExpressRoute 的关系图。](./media/expressroute-vpn-support/expressroute-with-route-server.png)

你还可以将 SDWAN 设备替换为 Azure VPN 网关。 由于 Azure VPN 网关和 ExpressRoute 是完全托管的，因此你只需为两个本地网络启用路由交换即可相互通信。

> [!IMPORTANT] 
> 必须在 [**主动-主动**](../vpn-gateway/vpn-gateway-activeactive-rm-powershell.md) 模式下配置 Azure VPN 网关。
>

![显示已配置路由服务器的 ExpressRoute 和 VPN 网关的关系图。](./media/expressroute-vpn-support/expressroute-and-vpn-with-route-server.png)

## <a name="next-steps"></a>后续步骤

- 详细了解 [Azure 路由服务器](route-server-faq.md)。
- 了解如何 [配置 Azure 路由服务器](quickstart-configure-route-server-powershell.md)。
- 了解有关 [Azure ExpressRoute 和 AZURE VPN 共存](../expressroute/expressroute-howto-coexist-resource-manager.md)的详细信息。
