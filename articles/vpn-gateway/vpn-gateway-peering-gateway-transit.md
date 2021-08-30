---
title: 针对虚拟网络对等互连配置 VPN 网关传输
description: 了解如何为虚拟网络对等互连配置网关传输，以便将两个 Azure 虚拟网络无缝连接成一个来加强连接。
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 04/28/2021
ms.author: cherylmc
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: d4eef7e7470e9dbdd37613165f0ea5cb3b295762
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121729474"
---
# <a name="configure-vpn-gateway-transit-for-virtual-network-peering"></a>针对虚拟网络对等互连配置 VPN 网关传输

本文介绍如何针对虚拟网络对等互连配置网关传输。 [虚拟网络对等互连](../virtual-network/virtual-network-peering-overview.md)可以无缝地连接两个 Azure 虚拟网络，为了连接目的将两个虚拟网络合并成一个。 [网关传输](../virtual-network/virtual-network-peering-overview.md#gateways-and-on-premises-connectivity)是一个对等互连属性，它允许一个虚拟网络使用对等互连的虚拟网络中的 VPN 网关进行跨界连接或 VNet 到 VNet 连接。 下图说明了在虚拟网络对等互连中使用网关传输的工作原理。

![网关传输示意图](./media/vpn-gateway-peering-gateway-transit/gatewaytransit.png)

在图中，对等互连的虚拟网络通过网关传输来使用 Hub-RM 中的 Azure VPN 网关。 在 VPN 网关上提供的连接（包括 S2S 连接、P2S 连接和 VNet 到 VNet 连接）适用于所有三种虚拟网络。 此传输选项适用于在相同的或不同的部署模型之间进行对等互连。 如果要配置不同部署模型之间的传输，则中心虚拟网络和虚拟网络网关必须采用[资源管理器部署模型](../azure-resource-manager/management/deployment-models.md)，而不是经典部署模型。
>

在中心辐射型网络体系结构中，辐射虚拟网络可以通过网关传输共享中心的 VPN 网关，不必在每个辐射虚拟网络中部署 VPN 网关。 通往网关连接的虚拟网络或本地网络的路由会通过网关传输传播到对等互连的虚拟网络的路由表。 可以禁用源自 VPN 网关的自动路由传播。 使用“禁用 BGP 路由传播”选项创建一个路由表，将路由表关联到子网，防止将路由分发到这些子网。 有关详细信息，请参阅[虚拟网络路由表](../virtual-network/manage-route-table.md)。

本文介绍了两个方案：

* 相同的部署模型：两个虚拟网络都采用资源管理器部署模型创建。
* 不同的部署模型：辐射虚拟网络是采用经典部署模型创建，中心虚拟网络和网关采用资源管理器部署模型创建。

>[!NOTE]
> 如果更改网络拓扑并且具有 VPN 客户端，必须再次下载和安装 Windows 客户端的 VPN 客户端包，以使更改应用于客户端。
>

## <a name="prerequisites"></a>先决条件

在开始之前，请验证你是否具有以下虚拟网络和权限：

### <a name="virtual-networks"></a><a name="vnet"></a>虚拟网络

|VNet|部署模型| 虚拟网络网关|
|---|---|---|---|
| Hub-RM| [资源管理器](./tutorial-site-to-site-portal.md)| [是](tutorial-create-gateway-portal.md)|
| Spoke-RM | [资源管理器](./tutorial-site-to-site-portal.md)| 否 |
| Spoke-Classic | [经典](vpn-gateway-howto-site-to-site-classic-portal.md#CreatVNet) | 否 |

### <a name="permissions"></a><a name="permissions"></a>Permissions

用于创建虚拟网络对等互连的帐户必须具有所需的角色或权限。 在以下示例中，若要将两个名为 Hub-RM 和 Spoke-Classic 的虚拟网络进行对等互连，帐户必须具有适用于每个虚拟网络的以下角色或权限 ：

|VNet|部署模型|角色|权限|
|---|---|---|---|
|Hub-RM|资源管理器|[网络参与者](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write|
| |经典|[经典网络参与者](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|空值|
|Spoke-Classic|资源管理器|[网络参与者](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/peer|
||经典|[经典网络参与者](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|Microsoft.ClassicNetwork/virtualNetworks/peer|

详细了解[内置角色](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)以及将特定的权限分配到[自定义角色](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)（仅限资源管理器）。

## <a name="same-deployment-model"></a><a name="same"></a>相同的部署模型

在此场景中，两个虚拟网络都采用资源管理器部署模型。 使用以下步骤创建或更新虚拟网络对等互连，以便启用网关传输。

### <a name="to-add-a-peering-and-enable-transit"></a>添加对等互连并启用传输

1. 在 [Azure 门户](https://portal.azure.com)中，创建或更新从 Hub-RM 进行对等互连的虚拟网络。 导航到“Hub-RM”虚拟网络。 选择“对等互连”，然后选择“+ 添加”，以便打开“添加对等互连”。
1. 在“添加对等互连”页面上，配置此虚拟网络的值。

   * 对等互连链接名称：为链接命名。 示例：HubRMToSpokeRM
   * 到远程虚拟网络的流量：**Allow**
   * 从远程虚拟网络转发的流量：**Allow**
   * 虚拟网络网关：使用此虚拟网络的网关

     :::image type="content" source="./media/vpn-gateway-peering-gateway-transit/peering-vnet.png" alt-text="屏幕截图显示了“添加对等互连”。":::

1. 在同一页面上，继续配置远程虚拟网络的值。

   * 对等互连链接名称：为链接命名。 示例：SpokeRMtoHubRM
   * 部署模型：**Resource Manager**
   * 虚拟网络：Spoke-RM
   * 到远程虚拟网络的流量：**Allow**
   * 从远程虚拟网络转发的流量：**Allow**
   * 虚拟网络网关：使用远程虚拟网络的网关

     :::image type="content" source="./media/vpn-gateway-peering-gateway-transit/peering-remote.png" alt-text="屏幕截图显示了远程虚拟网络的值。":::

1. 选择“添加”以创建对等互连。
1. 验证两个虚拟网络上的对等互连状态是否为“已连接”。

### <a name="to-modify-an-existing-peering-for-transit"></a>修改用于传输的现有对等互连

如果已创建对等互连，则可以修改用于传输的对等互连。

1. 导航到虚拟网络。 选择“对等互连”，然后选择要修改的对等互连。

   :::image type="content" source="./media/vpn-gateway-peering-gateway-transit/peering-modify.png" alt-text="屏幕截图显示了“选择对等互连”。":::

1. 更新 VNet 对等互连。

   * 到远程虚拟网络的流量：**Allow**
   * 转发到远程虚拟网络的流量；允许
   * 虚拟网络网关：使用远程虚拟网络的网关

     :::image type="content" source="./media/vpn-gateway-peering-gateway-transit/modify-peering-settings.png" alt-text="屏幕截图显示了“修改对等互连网关”。":::

1. 保存对等互连设置。

### <a name="powershell-sample"></a><a name="ps-same"></a>PowerShell 示例

也可使用 PowerShell 来创建或更新以上示例的对等互连。 将变量替换为虚拟网络和资源组的名称。

```azurepowershell-interactive
$SpokeRG = "SpokeRG1"
$SpokeRM = "Spoke-RM"
$HubRG   = "HubRG1"
$HubRM   = "Hub-RM"

$spokermvnet = Get-AzVirtualNetwork -Name $SpokeRM -ResourceGroup $SpokeRG
$hubrmvnet   = Get-AzVirtualNetwork -Name $HubRM -ResourceGroup $HubRG

Add-AzVirtualNetworkPeering `
  -Name SpokeRMtoHubRM `
  -VirtualNetwork $spokermvnet `
  -RemoteVirtualNetworkId $hubrmvnet.Id `
  -UseRemoteGateways

Add-AzVirtualNetworkPeering `
  -Name HubRMToSpokeRM `
  -VirtualNetwork $hubrmvnet `
  -RemoteVirtualNetworkId $spokermvnet.Id `
  -AllowGatewayTransit
```

## <a name="different-deployment-models"></a><a name="different"></a>不同的部署模型

在此配置中，辐射 VNet Spoke-Classic 采用经典部署模型，而中心 VNet Hub-RM 采用资源管理器部署模型。 配置部署模型之间的传输时，必须为资源管理器 VNet 配置虚拟网络网关，不需要为经典 VNet 配置虚拟网络网关。

对于此配置，你只需要配置 Hub-RM 虚拟网络。 不需要在 Spoke-Classic VNet 上配置任何内容。

1. 在 Azure 门户中，导航到“Hub-RM”虚拟网络，选择“对等互连”，然后选择“+ 添加”。
1. 在“添加对等互连”页面上，配置以下值：

   * 对等互连链接名称：为链接命名。 示例：HubRMToClassic
   * 到远程虚拟网络的流量：**Allow**
   * 从远程虚拟网络转发的流量：**Allow**
   * 虚拟网络网关：使用此虚拟网络的网关
   * 远程虚拟网络：**经典**

     :::image type="content" source="./media/vpn-gateway-peering-gateway-transit/peering-classic.png" alt-text="Spoke-Classic 的“添加对等互连”页面":::

1. 验证订阅是否正确，然后从下拉列表中选择虚拟网络。
1. 选择“添加”以添加对等互连。
1. 验证 Hub-RM 虚拟网络上的对等互连状态是否为“已连接”。 

对于此配置，无需在 Spoke-Classic 虚拟网络上配置任何内容。 在状态显示“已连接”后，辐射虚拟网络可以通过中心虚拟网络中的 VPN 网关使用连接。

### <a name="powershell-sample"></a><a name="ps-different"></a>PowerShell 示例

也可使用 PowerShell 来创建或更新以上示例的对等互连。 将变量和订阅 ID 替换为虚拟网络和资源组以及订阅的值。 只需在中心虚拟网络上创建虚拟网络对等互连。

```azurepowershell-interactive
$HubRG   = "HubRG1"
$HubRM   = "Hub-RM"

$hubrmvnet   = Get-AzVirtualNetwork -Name $HubRM -ResourceGroup $HubRG

Add-AzVirtualNetworkPeering `
  -Name HubRMToClassic `
  -VirtualNetwork $hubrmvnet `
  -RemoteVirtualNetworkId "/subscriptions/<subscription Id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/Spoke-Classic" `
  -AllowGatewayTransit
```

## <a name="next-steps"></a>后续步骤

* 在为生产用途创建虚拟网络对等互连之前，请详细了解[虚拟网络对等互连约束和行为](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints)和[虚拟网络对等互连设置](../virtual-network/virtual-network-manage-peering.md#create-a-peering)。
* 了解如何使用虚拟网络对等互连和网关传输[创建中心辐射型网络拓扑](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke#virtual-network-peering)。
* [使用相同的部署模型创建虚拟网络对等互连](../virtual-network/tutorial-connect-virtual-networks-portal.md)。
* [使用不同的部署模型创建虚拟网络对等互连](../virtual-network/create-peering-different-deployment-models.md)。
