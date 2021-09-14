---
title: '关于具有 Azure 路由服务器的双宿主网络 '
description: 了解 Azure 路由服务器在双宿主网络中的工作原理。
services: route-server
author: duongau
ms.service: route-server
ms.topic: conceptual
ms.date: 09/01/2021
ms.author: duau
ms.openlocfilehash: 76c1a1e424e4f8ae372f53ceab5da43ca9d40629
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123434567"
---
# <a name="about-dual-homed-network-with-azure-route-server"></a>关于具有 Azure 路由服务器的双宿主网络

Azure 路由服务器支持典型的中心和分支网络拓扑。 此配置适用于路由服务器和网络虚拟设备 (NVA) 均位于中心虚拟网络时。 路由服务器还支持配置称为双宿主网络的其他拓扑。 此配置适用于分支虚拟网络与两个或更多中心虚拟网络对等互连时。 分支虚拟网络中的虚拟机可以通过任一中心虚拟网络与本地或 Internet 进行通信。

## <a name="how-to-set-it-up"></a>设置方法

如下图所示，需执行以下操作：

* 在每个中心虚拟网络中部署 NVA，在分支虚拟网络中部署路由服务器。
* 在中心和分支虚拟网络之间启用 VNet 对等互连。
* 在路由服务器和部署的每个 NVA 之间配置 BGP 对等互连。

:::image type="content" source="./media/about-dual-homed-network/dual-homed-topology.png" alt-text="双宿主拓扑中的路由服务器图。":::

### <a name="how-does-it-work"></a>它是如何工作的？

在控制平面中，NVA 和路由服务器将交换路由，就好像两者部署在同一虚拟网络中一样。 NVA 将从路由服务器获取分支虚拟网络地址。 路由服务器将从每个 NVA 获取路由。 然后，路由服务器将使用所获取的路由对分支虚拟网络中的所有虚拟机进行编程。 

在数据平面中，分支虚拟网络中的虚拟机会将中心内的安全 NVA 或 VPN NVA 视为下一个跃点。 发往 Internet 的流量或混合跨界流量现在将通过中心虚拟网络中的 NVA 进行路由。 可以将这两个中心配置为主动/主动或主动/被动。 如果主动中心出现故障，前往/来自虚拟机的流量会将故障转移到另一个中心。 这些故障包括但不限于：NVA 故障或服务连接故障。 此设置可确保网络配置为高可用性。

## <a name="integration-with-expressroute"></a>与 ExpressRoute 集成

可以生成含两个或更多 ExpressRoute 连接的双宿主网络。 除上述步骤外，还需执行以下操作：

* 在每个具有 ExpressRoute 网关的中心虚拟网络中创建另一个路由服务器。
* 在中心虚拟网络中的 NVA 和路由服务器之间配置 BGP 对等互连。
* 在中心虚拟网络中的 ExpressRoute 网关和路由服务器之间[启用路由交换](quickstart-configure-route-server-portal.md#configure-route-exchange)。
* 确保分支虚拟网络 VNet 对等互连配置中禁用了“使用远程网关或远程路由服务器”。

:::image type="content" source="./media/about-dual-homed-network/dual-homed-topology-expressroute.png" alt-text="具有 ExpressRoute 的双宿主拓扑中的路由服务器图。":::

### <a name="how-does-it-work"></a>它是如何工作的？

在控制平面中，中心虚拟网络中的 NVA 将通过与中心内的路由服务器进行[路由交换](quickstart-configure-route-server-portal.md#configure-route-exchange)从 ExpressRoute 网关获取本地路由。 作为交换，NVA 将通过同一路由服务器将分支虚拟网络地址发送到 ExpressRoute 网关。 然后，分支和中心虚拟网络中的路由服务器将编写各自虚拟网络中虚拟机的本地网络地址。

> [!IMPORTANT]
> BGP 通过验证 AS 路径中的 AS 编号来阻止循环。 如果接收路由器在收到的 BGP 数据包的 AS 路径中发现自己的 AS 编号，则它将丢弃该数据包。 在本示例中，这两个路由服务器具有同一 AS 编号 65515。 为防止每个路由服务器丢弃来自另一路由服务器的路由，NVA 必须在与每个路由服务器对等互连时应用 as-override BGP 策略。 
>

在数据平面中，分支虚拟网络中的虚拟机会先将所有发往本地网络的流量发送到中心虚拟网络中的 NVA。 然后，NVA 会通过 ExpressRoute 将流量转发到本地网络。 来自本地的流量将以相反的方向遍历同一数据路径。 你将会注意到这两个路由服务器均不位于该数据路径。

## <a name="next-steps"></a>后续步骤

* [了解 Azure 路由服务器如何与 ExpressRoute 配合使用](expressroute-vpn-support.md)
* [了解 Azure 路由服务器如何与网络虚拟设备配合使用](resource-manager-template-samples.md)

