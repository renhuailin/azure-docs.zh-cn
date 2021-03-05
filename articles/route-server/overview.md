---
title: 什么是 Azure 路由服务器（预览版）？
description: 了解 Azure 路由服务器如何简化网络虚拟设备 (NVA) 与虚拟网络之间的路由。
services: route-server
author: duongau
ms.service: route-server
ms.topic: overview
ms.date: 03/02/2021
ms.author: duau
ms.openlocfilehash: 099f9b3769179076491c7c2098ec56faff9847dd
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102039829"
---
# <a name="what-is-azure-route-server-preview"></a>什么是 Azure 路由服务器（预览版）？ 

Azure 路由服务器简化了网络虚拟设备 (NVA) 与虚拟网络之间的动态路由。 利用此功能，可以在 Azure 虚拟网络 (VNET) 中任何支持 BGP 路由协议的 NVA 与 Azure 软件定义的网络 (SDN) 之间，直接通过“边界网关协议 (BGP)”路由协议交换路由信息，而无需手动配置或维护路由表。 Azure 路由服务器是一项完全托管的服务，并且配置为具有高可用性。

> [!IMPORTANT]
> Azure 路由服务器（预览版）目前为公共预览版状态。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="how-does-it-work"></a>它是如何工作的？

下图说明了 Azure 路由服务器如何与虚拟网络中的 SDWAN NVA 和安全 NVA 配合使用。 在建立了 BGP 对等互连后，Azure 路由服务器将会从 SDWAN 设备接收本地路由 (10.250.0.0/16)，并从防火墙接收默认路由 (0.0.0.0/0)。 然后，在虚拟网络中的 VM 上将会自动配置这些路由。 因此，所有流向本地网络的流量都将会发送到 SDWAN 设备。 而所有 Internet 绑定流量都将会发送到防火墙。 在相反的方向上，Azure 路由服务器将会向两个 NVA 都发送虚拟网络地址 (10.1.0.0/16)。 SDWAN 设备可以进一步将该地址传播到本地网络。

![显示虚拟网络中配置的 Azure 路由服务器的关系图。](./media/overview/route-server-overview.png)

## <a name="key-benefits"></a>主要优点 

Azure 路由服务器简化了虚拟网络中 NVA 的配置、管理和部署。  

* 每当虚拟网络地址更新时，你不再需要手动更新 NVA 上的路由表。 

* 每当 NVA 公布新路由或撤消旧路由时，你不再需要手动更新[用户定义的路由](../virtual-network/virtual-networks-udr-overview.md)。 

* 不再需要出于复原能力或性能方面的原因在 NVA 的前面配置负载均衡器。 在将 NVA 的多个实例与 Azure 路由服务器对等互连时，可以在 NVA 中配置 BGP 属性。 通过这些 BGP 属性，Azure 路由服务器可以知道哪个 NVA 实例应是主动的，哪个 NVA 实例应是被动的。 

* NVA 与 Azure 路由服务器之间的接口基于通用标准协议。 只要 NVA 支持 BGP，你就可以将其与 Azure 路由服务器对等互连。 有关详细信息，请参阅[路由服务器支持的路由协议](route-server-faq.md#protocol)。

* 可以在任何新的或现有的虚拟网络中部署 Azure 路由服务器。 

## <a name="faq"></a>常见问题解答

有关 Azure 路由服务器的常见问题，请参阅 [Azure 路由服务器常见问题解答](route-server-faq.md)。

## <a name="next-steps"></a>后续步骤

- [了解如何配置 Azure 路由服务器](quickstart-configure-route-server-powershell.md)
- [了解 Azure 路由服务器如何与 Azure ExpressRoute 和 Azure VPN 配合使用](expressroute-vpn-support.md)
