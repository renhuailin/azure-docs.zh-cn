---
title: 教程：将 VNet 链接到 ExpressRoute 线路 - Azure CLI
description: 本教程介绍如何使用资源管理器部署模型和 Azure CLI 将虚拟网络 (VNet) 链接到 ExpressRoute 线路。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 08/10/2021
ms.author: duau
ms.openlocfilehash: b7a6cf1bdb490683faa5285811c83d220ac80cca
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128662875"
---
# <a name="tutorial-connect-a-virtual-network-to-an-expressroute-circuit-using-cli"></a>教程：使用 CLI 将虚拟网络连接到 ExpressRoute 线路

本教程介绍如何使用 Azure CLI 将虚拟网络 (VNet) 链接到 Azure ExpressRoute 线路。 若要使用 Azure CLI 进行链接，必须使用资源管理器部署模型创建虚拟网络。 它们可以在同一个订阅中，也可以属于另一个订阅。 如果想使用不同的方法将 VNet 连接到 ExpressRoute 线路，请从以下列表中选择一篇文章进行参阅：

> [!div class="op_single_selector"]
> * [Azure 门户](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [Azure CLI](howto-linkvnet-cli.md)
> * [视频 - Azure 门户](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell（经典）](expressroute-howto-linkvnet-classic.md)
> 

在本教程中，你将了解如何执行以下操作：
> [!div class="checklist"]
> - 将同一订阅中的虚拟网络连接到线路
> - 将另一订阅中的虚拟网络连接到线路
> - 修改虚拟网络连接
> - 配置 ExpressRoute FastPath

## <a name="prerequisites"></a>必备条件

* 需要最新版本的命令行接口 (CLI)。 有关详细信息，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。
* 在开始配置之前，请先查看[先决条件](expressroute-prerequisites.md)、[路由要求](expressroute-routing.md)和[工作流](expressroute-workflows.md)。
* 必须有一个活动的 ExpressRoute 线路。 
  * 请按说明[创建 ExpressRoute 线路](howto-circuit-cli.md)，并通过连接提供商启用该线路。 
  * 请确保为线路配置 Azure 专用对等互连。 有关路由说明，请参阅[配置路由](howto-routing-cli.md)一文。 
  * 请确保已配置 Azure 专用对等互连。 必须建立网络和 Microsoft 之间的 BGP 对等互连，使你能够启用端到端的连接。
  * 确保已创建并完全预配一个虚拟网络和一个虚拟网络网关。 请按照说明[为 ExpressRoute 配置虚拟网络网关](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md)。 请务必使用 `--gateway-type ExpressRoute`。
* 最多可以将 10 个虚拟网络链接到一条标准 ExpressRoute 线路。 使用标准 ExpressRoute 线路时，所有虚拟网络必须都位于同一地缘政治区域。 
* 单个 VNet 可最多连接到 16 条 ExpressRoute 线路。 使用以下流程为要连接的每条 ExpressRoute 线路创建新的连接对象。 ExpressRoute 线路可在同一订阅、不同订阅或两者兼有。
* 如果启用 ExpressRoute 高级版加载项，则可以链接 ExpressRoute 线路的地缘政治区域外部的虚拟网络。 通过高级版加载项，你还可以根据所选带宽，将 10 个以上的虚拟网络连接到 ExpressRoute 线路。 有关高级外接程序的更多详细信息，请参阅[常见问题解答](expressroute-faqs.md)。

* 如果要创建从 ExpressRoute 线路到目标 ExpressRoute 虚拟网络网关的连接，则从本地或对等互连虚拟网络播发的地址空间数必须等于或小于 200。 成功创建连接后，可以将其他地址空间（最多 1000 个）添加到本地或对等互连虚拟网络。

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>将同一订阅中的虚拟网络连接到线路

可以使用以下示例将虚拟网络网关连接到 ExpressRoute 线路。 在运行此命令之前，请确保虚拟网络网关已创建并可用于进行链接。

```azurecli-interactive
az network vpn-connection create --name ERConnection --resource-group ExpressRouteResourceGroup --vnet-gateway1 VNet1GW --express-route-circuit2 MyCircuit
```

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>将另一订阅中的虚拟网络连接到线路

用户可以在多个订阅之间共享 ExpressRoute 线路。 下图是在多个订阅之间共享 ExpressRoute 线路的简单示意图。

> [!NOTE]
> 不支持在 Azure 主权云和公共 Azure 云之间连接虚拟网络。 只能链接来自同一云中不同订阅的虚拟网络。

大型云中的每个较小云用于表示属于组织中不同部门的订阅。 组织内的每个部门使用自己的订阅部署其服务，但可以共享单个 ExpressRoute 线路以连接回本地网络。 单个部门（在此示例中为 IT 部门）可以拥有 ExpressRoute 线路。 组织内的其他订阅可以使用 ExpressRoute 线路。

> [!NOTE]
> 专用线路的连接和带宽费用将应用于 ExpressRoute 线路所有者。 所有虚拟网络共享相同的带宽。
> 
> 

![跨订阅连接](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)

### <a name="administration---circuit-owners-and-circuit-users"></a>管理 - 线路所有者和线路用户

“线路所有者”是 ExpressRoute 线路资源的已授权超级用户。 线路所有者可以创建可由“线路用户”兑换的授权。 线路用户是虚拟网络网关的所有者（这些网关与 ExpressRoute 线路位于不同的订阅中）。 线路用户可以兑现授权（每个虚拟网络需要一个授权）。

线路所有者有权随时修改和撤销授权。 撤销授权后，会从撤销了访问权限的订阅中删除所有链路连接。

### <a name="circuit-owner-operations"></a>线路所有者操作

**若要创建授权**

线路所有者创建授权，这将创建授权密钥，供线路用户用于将其虚拟网络网关连接到 ExpressRoute 线路。 一个授权只可用于一个连接。

以下示例说明如何创建授权：

```azurecli-interactive
az network express-route auth create --circuit-name MyCircuit -g ExpressRouteResourceGroup -n MyAuthorization
```

此响应包含授权密钥和状态：

```output
"authorizationKey": "0a7f3020-541f-4b4b-844a-5fb43472e3d7",
"authorizationUseStatus": "Available",
"etag": "W/\"010353d4-8955-4984-807a-585c21a22ae0\"",
"id": "/subscriptions/81ab786c-56eb-4a4d-bb5f-f60329772466/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit/authorizations/MyAuthorization1",
"name": "MyAuthorization1",
"provisioningState": "Succeeded",
"resourceGroup": "ExpressRouteResourceGroup"
```

**若要查看授权**

线路所有者可以运行以下示例来查看针对特定线路发布的所有授权：

```azurecli-interactive
az network express-route auth list --circuit-name MyCircuit -g ExpressRouteResourceGroup
```

**若要添加授权**

线路所有者可以使用以下示例来添加授权：

```azurecli-interactive
az network express-route auth create --circuit-name MyCircuit -g ExpressRouteResourceGroup -n MyAuthorization1
```

**若要删除授权**

线路所有者可以运行以下示例来撤销/删除对用户的授权：

```azurecli-interactive
az network express-route auth delete --circuit-name MyCircuit -g ExpressRouteResourceGroup -n MyAuthorization1
```

### <a name="circuit-user-operations"></a>线路用户操作

线路用户需要对等 ID 以及线路所有者提供的授权密钥。 授权密钥是一个 GUID。

```azurecli-interactive
az network express-route show -n MyCircuit -g ExpressRouteResourceGroup
```

**若要兑换连接授权**

线路用户可以运行以下示例来兑现链接授权：

```azurecli-interactive
az network vpn-connection create --name ERConnection --resource-group ExpressRouteResourceGroup --vnet-gateway1 VNet1GW --express-route-circuit2 MyCircuit --authorization-key "^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^"
```

**若要释放连接授权**

可以通过删除 ExpressRoute 线路与虚拟网络之间的连接释放授权。

## <a name="modify-a-virtual-network-connection"></a>修改虚拟网络连接
可以更新虚拟网络连接的某些属性。 

**若要更新连接权重**

虚拟网络可以连接到多条 ExpressRoute 线路。 可以从多条 ExpressRoute 线路收到相同的前缀。 若要选择使用哪个连接发送目标为此前缀的流量，可以更改连接的 *RoutingWeight*。 将在具有最高 *RoutingWeight* 的连接上发送流量。

```azurecli-interactive
az network vpn-connection update --name ERConnection --resource-group ExpressRouteResourceGroup --routing-weight 100
```

*RoutingWeight* 的范围是 0 到 32000。 默认值为 0。

## <a name="configure-expressroute-fastpath"></a>配置 ExpressRoute FastPath 
如果虚拟网络网关是超高性能网关或 ErGw3AZ，则可以启用 [ExpressRoute FastPath](expressroute-about-virtual-network-gateways.md)。 FastPath 可提高数据路径性能，例如本地网络和虚拟网络之间的每秒数据包数和每秒连接数。 

对新的连接配置 FastPath

```azurecli-interactive
az network vpn-connection create --name ERConnection --resource-group ExpressRouteResourceGroup --express-route-gateway-bypass true --vnet-gateway1 VNet1GW --express-route-circuit2 MyCircuit
```

更新现有连接以启用 FastPath

```azurecli-interactive
az network vpn-connection update --name ERConnection --resource-group ExpressRouteResourceGroup --express-route-gateway-bypass true
```

> [!NOTE]
> 可以使用[连接监视器](how-to-configure-connection-monitor.md)来验证流量是否使用 FastPath 到达目标。
>

## <a name="enroll-in-expressroute-fastpath-features-preview"></a>注册 ExpressRoute FastPath 功能（预览版）

FastPath 对虚拟网络对等互连的支持现以公共预览版提供。 只能通过 Azure PowerShell 进行注册。 有关如何注册的说明，请参阅 [FastPath 预览功能](expressroute-howto-linkvnet-arm.md#enroll-in-expressroute-fastpath-features-preview)。

> [!NOTE] 
> 目标订阅中为 FastPath 配置的任何连接都将注册到此预览版中。 建议不要在生产订阅中启用此预览版。
> 如果已经配置了 FastPath 并且想要注册预览功能，则需要执行以下操作：
> 1. 使用上述 Azure PowerShell 命令注册 FastPath 预览功能。
> 1. 在目标连接上禁用 FastPath，然后重新启用它。

## <a name="clean-up-resources"></a>清理资源

如果不再需要 ExpressRoute 连接，请使用 `az network vpn-connection delete` 命令，从该网关所在的订阅删除网关和线路之间的链接。

```azurecli-interactive
az network vpn-connection delete --name ERConnection --resource-group ExpressRouteResourceGroup
```

## <a name="next-steps"></a>后续步骤

在本教程中，你已了解如何将虚拟网络连接到同一订阅和不同订阅中的线路。 有关 ExpressRoute 网关的详细信息，请参阅： 

> [!div class="nextstepaction"]
> [关于 ExpressRoute 虚拟网络网关](expressroute-about-virtual-network-gateways.md)
