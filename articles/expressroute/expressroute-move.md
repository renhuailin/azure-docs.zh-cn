---
title: ExpressRoute：将线路从经典部署迁移到 Azure 资源管理器部署
description: 了解将 Azure ExpressRoute 线路从经典部署模型迁移到 Azure 资源管理器部署模型时会发生什么。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 12/15/2020
ms.author: duau
ms.openlocfilehash: dcba2e9de2b37e8c432f94781b3c4c369ad52395
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "97807935"
---
# <a name="moving-expressroute-circuits-from-the-classic-to-the-resource-manager-deployment-model"></a>将 ExpressRoute 线路从经典部署模型转移到 Resource Manager 部署模型
本文概述了将 Azure ExpressRoute 线路从经典部署模型转移到 Azure 资源管理器部署模型时会发生什么。

可使用一条 ExpressRoute 线路连接到同时在经典部署模型和资源管理器部署模型中部署的虚拟网络。

![跨两种部署模型链接到虚拟网络的 ExpressRoute 线路](./media/expressroute-move/expressroute-move-1.png)

## <a name="expressroute-circuits-that-are-created-in-the-classic-deployment-model"></a>在经典部署模型中创建的 ExpressRoute 线路
在经典部署模型中创建的 ExpressRoute 线路需要首先迁移到资源管理器部署模型。 只有这样才能同时启用到经典部署模型和资源管理器部署模型的连接。 转移连接时，不会发生连接丢失或断开的情况。 经典部署模型中所有从线路到虚拟网络的链接都会保留，而无论这些链接是在同一订阅还是不同订阅中。

成功完成转移后，ExpressRoute 线路的行为将与在资源管理器部署模型中创建的 ExpressRoute 线路完全相同。 现在，可以在 Resource Manager 部署模型中建立与虚拟网络的连接。

将 ExpressRoute 线路转移到资源管理器部署模型后，只能在资源管理器部署模型中对其进行管理。 而且，将只能通过资源管理器部署模型执行管理对等互连、更新线路属性和删除线路的操作。 若要详细了解如何管理对这两个部署模型的访问，请参阅以下部分。

无需连接服务提供商操作，你就可将线路转移到资源管理器部署模型。

## <a name="expressroute-circuits-that-are-created-in-the-resource-manager-deployment-model"></a>在 Resource Manager 部署模型中创建的 ExpressRoute 线路
可以允许从这两种部署模型访问在 Resource Manager 部署模型中创建的 ExpressRoute 线路。 订阅中的任何 ExpressRoute 线路都可配置为从这两种部署模型访问。

* 默认情况下，在资源管理器部署模型中创建的 ExpressRoute 线路无法访问经典部署模型。
* 默认情况下，可从这两种部署模型访问从经典部署模型转移到资源管理器部署模型的 ExpressRoute 线路。
* 无论是在资源管理器部署模型还是经典部署模型中创建的，ExpressRoute 线路始终都可访问资源管理器部署模型。 可按照[如何链接虚拟网络](expressroute-howto-linkvnet-arm.md)中的说明，创建到虚拟网络的连接。
* 对经典部署模型的访问权限由 ExpressRoute 线路中的 **allowClassicOperations** 参数控制。

> [!IMPORTANT]
> 将应用 [服务限制](../azure-resource-manager/management/azure-subscription-service-limits.md) 页中所述的所有配额。 例如，标准线路最多可以有 10 个跨经典部署模型和 Resource Manager 部署模型的虚拟网络链接/连接。
> 
> 

## <a name="controlling-access-to-the-classic-deployment-model"></a>控制对经典部署模型的访问权限
可在这两种部署模型中启用 ExpressRoute 线路，以链接到虚拟网络。 为此，请在 ExpressRoute 线路上设置 allowClassicOperations 参数。

将 **allowClassicOperations** 设置为 TRUE 即可从这两种部署模型中的虚拟网络链接到 ExpressRoute 线路。 
* 若要链接经典部署模型中的虚拟网络，请参阅[如何链接经典部署模型的虚拟网络](expressroute-howto-linkvnet-classic.md)。
* 若要链接资源管理器部署模型中的虚拟网络，请参阅[如何链接资源管理器部署模型中的虚拟网络](expressroute-howto-linkvnet-arm.md)。

将 **allowClassicOperations** 设置为 FALSE 会阻止从经典部署模型访问线路。 不过，经典部署模型中链接的所有虚拟网络仍会保留。 ExpressRoute 线路不会在经典部署模型中显示。

## <a name="supported-operations-in-the-classic-deployment-model"></a>经典部署模型中支持的操作
将 **allowClassicOperations** 设置为 TRUE 时，ExpressRoute 线路支持以下经典操作。

* 获取 ExpressRoute 线路信息
* 创建/更新/获取/删除到经典虚拟网络的虚拟网络链接
* 创建/更新/获取/删除跨订阅连接的虚拟网络链接授权

但是，将 allowClassicOperations 设置为 TRUE 时，无法执行以下经典操作：

* 创建/更新/获取/删除 Azure 专用对等互连、Azure 公共对等互连和 Microsoft 对等互连的边界网关协议 (BGP) 对等互连
* 删除 ExpressRoute 线路

## <a name="communication-between-the-classic-and-the-resource-manager-deployment-models"></a>经典部署模型和 Resource Manager 部署模型之间的通信
ExpressRoute 线路相当于经典部署模型与 Resource Manager 部署模型之间的桥梁。 这两个部署模型的虚拟网络之间的流量可通过 ExpressRoute 线路，前提是这两种虚拟网络都链接到同一线路。

聚合吞吐量受限于虚拟网络网关的吞吐容量。 在这种情况下，流量不会进入连接服务提供商的网络，也不进入你的网络。 虚拟网络之间的流量完全包含在 Microsoft 网络中。

## <a name="access-to-azure-public-and-microsoft-peering-resources"></a>对 Azure 公共对等互连资源和 Microsoft 对等互连资源的访问权限
可以继续访问通常可通过 Azure 公共对等互连和 Microsoft 对等互连访问的资源，而不会出现任何中断。  

## <a name="whats-supported"></a>支持的操作
本部分介绍可通过 ExpressRoute 线路执行的操作：

* 可以使用一条 ExpressRoute 线路访问在经典部署模型和 Resource Manager 部署模型中部署的虚拟网络。
* 可以将 ExpressRoute 线路从经典部署模型转移到 Resource Manager 部署模型。 转移后，ExpressRoute 线路将继续像在资源管理器部署模型中创建的任何其他 ExpressRoute 线路一样运行。
* 只能转移 ExpressRoute 线路。 无法通过此操作转移线路链接、虚拟网络和 VPN 网关。
* 将 ExpressRoute 线路转移到 Resource Manager 部署模型后，只能使用 Resource Manager 部署模型来管理 ExpressRoute 线路的生命周期。 这意味着，某些操作只能在资源管理器部署模型中运行，例如添加/更新/删除对等互连、更新线路属性（例如带宽、SKU 和计费类型），以及删除线路。
* ExpressRoute 线路相当于经典部署模型与 Resource Manager 部署模型之间的桥梁。 经典虚拟网络中的虚拟机与资源管理器虚拟网络中的虚拟机之间的流量可通过 ExpressRoute 进行通信，前提是这两种虚拟网络都链接到同一 ExpressRoute 线路。
* 经典部署模型和 Resource Manager 部署模型都支持跨订阅连接。
* 将 ExpressRoute 线路从经典模型移到 Azure 资源管理器模型后，即可[迁移链接到 ExpressRoute 线路的虚拟网络](expressroute-migration-classic-resource-manager.md)。

## <a name="whats-not-supported"></a>不支持的功能
本部分介绍不可通过 ExpressRoute 线路执行的操作：

* 从经典部署模型管理 ExpressRoute 线路的生命周期。
* 针对经典部署模型的 Azure 基于角色的访问控制 (Azure RBAC) 支持。 无法对经典部署模型中的线路运行 Azure RBAC 控制。 订阅的任何管理员/共同管理员都可以将虚拟网络链接到线路，也都可以取消此类链接。

## <a name="configuration"></a>配置
遵循 [将 ExpressRoute 线路从经典部署模型转移到 Resource Manager 部署模型](expressroute-howto-move-arm.md)中所述的说明。

## <a name="next-steps"></a>后续步骤
* [将链接到 ExpressRoute 线路的虚拟网络从经典模型迁移到 Azure 资源管理器模型](expressroute-migration-classic-resource-manager.md)
* 有关工作流信息，请参阅 [ExpressRoute 线路预配工作流和线路状态](expressroute-workflows.md)。
* 配置 ExpressRoute 连接的步骤：
  
  * [创建 ExpressRoute 线路](expressroute-howto-circuit-arm.md)
  * [配置路由](expressroute-howto-routing-arm.md)
  * [将虚拟网络链接到 ExpressRoute 线路](expressroute-howto-linkvnet-arm.md)

