---
title: 针对虚拟网络对等互连配置 VPN 网关传输
description: 为虚拟网络对等互连配置网关传输，为连接性将两个 Azure 虚拟网络无缝连接到一个。
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 11/30/2020
ms.author: cherylmc
ms.openlocfilehash: 2fc12385c78135269b6a73038fd0ad810ebaedd6
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2020
ms.locfileid: "96576144"
---
# <a name="configure-vpn-gateway-transit-for-virtual-network-peering"></a>针对虚拟网络对等互连配置 VPN 网关传输

本文介绍如何针对虚拟网络对等互连配置网关传输。 [虚拟网络对等互连](../virtual-network/virtual-network-peering-overview.md)可以无缝地连接两个 Azure 虚拟网络，为了连接目的将两个虚拟网络合并成一个。 [网关传输](../virtual-network/virtual-network-peering-overview.md#gateways-and-on-premises-connectivity) 是一种对等互连属性，它允许一个虚拟网络在对等互连虚拟网络中使用 VPN 网关进行跨界连接或 VNet 到 vnet 连接。 下图说明了在虚拟网络对等互连中使用网关传输的工作原理。

![网关传输示意图](./media/vpn-gateway-peering-gateway-transit/gatewaytransit.png)

在图中，对等互连的虚拟网络通过网关传输来使用 Hub-RM 中的 Azure VPN 网关。 在 VPN 网关上提供的连接（包括 S2S 连接、P2S 连接和 VNet 到 VNet 连接）适用于所有三种虚拟网络。 传输选项可用于相同或不同部署模型之间的对等互连。 如果要在不同的部署模型之间配置传输，中心虚拟网络网关和虚拟网络网关必须位于资源管理器部署模型，而不是经典部署模型中。
>

在中心辐射型网络体系结构中，辐射虚拟网络可以通过网关传输共享中心的 VPN 网关，不必在每个辐射虚拟网络中部署 VPN 网关。 通往网关连接的虚拟网络或本地网络的路由会通过网关传输传播到对等互连的虚拟网络的路由表。 可以禁用源自 VPN 网关的自动路由传播。 使用“禁用 BGP 路由传播”选项创建一个路由表，将路由表关联到子网，防止将路由分发到这些子网。 有关详细信息，请参阅[虚拟网络路由表](../virtual-network/manage-route-table.md)。

本文介绍两个方案：

* **相同的部署模型**：在资源管理器部署模型中创建两个虚拟网络。
* **不同的部署模型**：在经典部署模型中创建辐射虚拟网络，中心虚拟网络和网关位于资源管理器部署模型中。

>[!NOTE]
> 如果更改网络拓扑并且具有 VPN 客户端，必须再次下载和安装 Windows 客户端的 VPN 客户端包，以使更改应用于客户端。
>

## <a name="prerequisites"></a>先决条件

在开始之前，请确认你具有以下虚拟网络和权限：

### <a name="virtual-networks"></a><a name="vnet"></a>虚拟网络

|VNet|部署模型| 虚拟网络网关|
|---|---|---|---|
| Hub-RM| [资源管理器](vpn-gateway-howto-site-to-site-resource-manager-portal.md)| [是](tutorial-create-gateway-portal.md)|
| 轮辐-RM | [资源管理器](vpn-gateway-howto-site-to-site-resource-manager-portal.md)| 否 |
| Spoke-Classic | [经典](vpn-gateway-howto-site-to-site-classic-portal.md#CreatVNet) | 否 |

### <a name="permissions"></a><a name="permissions"></a>Permissions

用于创建虚拟网络对等互连的帐户必须具有所需的角色或权限。 在以下示例中，如果对等互连两个名为 " **中心-RM** " 和 " **轮辐-经典**" 的虚拟网络，则你的帐户必须具有每个虚拟网络的以下角色或权限：

|VNet|部署模型|角色|权限|
|---|---|---|---|
|Hub-RM|资源管理器|[网络参与者](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write|
| |经典|[经典网络参与者](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|空值|
|Spoke-Classic|资源管理器|[网络参与者](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/peer|
||经典|[经典网络参与者](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|Microsoft.ClassicNetwork/virtualNetworks/peer|

详细了解[内置角色](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)以及将特定的权限分配到[自定义角色](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)（仅限资源管理器）。

## <a name="same-deployment-model"></a><a name="same"></a>相同的部署模型

在此方案中，虚拟网络位于资源管理器部署模型中。 使用以下步骤来创建或更新虚拟网络对等互连以启用网关传输。

### <a name="to-add-a-peering-and-enable-transit"></a>添加对等互连并启用传输

1. 在 [Azure 门户](https://portal.azure.com)中，通过中心-RM 创建或更新虚拟网络对等互连。 导航到 " **中心-RM** " 虚拟网络。 依次选择 " **对等互连**"、" **+ 添加** " 以打开 " **添加对等互连**"。
1. 在 " **添加对等互连** " 页上，为 **此虚拟网络** 配置值。

   * 对等链接名称：为链接命名。 示例： **HubRMToSpokeRM**
   * 到远程虚拟网络的流量： **允许**
   * 从远程虚拟网络转发的流量： **允许**
   * 虚拟网络网关： **使用此虚拟网络的网关**

     :::image type="content" source="./media/vpn-gateway-peering-gateway-transit/peering-vnet.png" alt-text="屏幕截图显示添加对等互连。":::

1. 在同一页上，继续配置 **远程虚拟网络** 的值。

   * 对等链接名称：为链接命名。 示例： **SpokeRMtoHubRM**
   * 部署模型： **资源管理器**
   * 虚拟网络： **轮辐-RM**
   * 到远程虚拟网络的流量： **允许**
   * 从远程虚拟网络转发的流量： **允许**
   * 虚拟网络网关： **使用远程虚拟网络的网关**

     :::image type="content" source="./media/vpn-gateway-peering-gateway-transit/peering-remote.png" alt-text="屏幕截图显示远程虚拟网络的值。":::

1. 选择 " **添加** " 以创建对等互连。
1. 验证两个虚拟网络上是否 **已连接** 的对等互连状态。

### <a name="to-modify-an-existing-peering-for-transit"></a>修改现有对等互连

如果已创建对等互连，则可以修改传输的对等互连。

1. 导航到虚拟网络。 选择 " **对等互连** "，然后选择要修改的对等互连。

   :::image type="content" source="./media/vpn-gateway-peering-gateway-transit/peering-modify.png" alt-text="屏幕截图显示选择对等互连。":::

1. 更新 VNet 对等互连。

   * 到远程虚拟网络的流量： **允许**
   * 转发到虚拟网络的流量; **允许**
   * 虚拟网络网关： **使用远程虚拟网络网关**

     :::image type="content" source="./media/vpn-gateway-peering-gateway-transit/modify-peering-settings.png" alt-text="屏幕截图显示 &quot;修改对等互连网关&quot;。":::

1. **保存** 对等互连设置。

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

在此配置中，轮辐 VNet **辐射** 是经典部署模型，而中心 vnet **集线器-RM** 位于资源管理器部署模型中。 在配置部署模型之间的传输时，必须为资源管理器 VNet 配置虚拟网络网关，而不是经典 VNet。

对于此配置，你只需配置 **中心-RM** 虚拟网络。 不需要在 **辐射-经典** VNet 上配置任何内容。

1. 在 Azure 门户中，导航到 **中心-RM** 虚拟网络，选择 **对等互连**，然后选择 " **+ 添加**"。
1. 在 " **添加对等互连** " 页上，配置下列值：

   * 对等链接名称：为链接命名。 示例： **HubRMToClassic**
   * 到远程虚拟网络的流量： **允许**
   * 从远程虚拟网络转发的流量： **允许**
   * 虚拟网络网关： **使用此虚拟网络的网关**
   * 远程虚拟网络： **经典**

     :::image type="content" source="./media/vpn-gateway-peering-gateway-transit/peering-classic.png" alt-text="添加辐射的对等页-经典":::

1. 验证订阅是否正确，并从下拉列表中选择虚拟网络。
1. 选择 " **添加** " 添加对等互连。
1. 验证中心-RM 虚拟 **网络上的对等** 互连状态。 

对于此配置，无需在 **辐射-经典** 虚拟网络上配置任何内容。 状态显示为 " **已连接**" 后，辐射虚拟网络可以通过中心虚拟网络中的 VPN 网关使用连接。

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
* [创建具有相同部署模型的虚拟网络对等互连](../virtual-network/tutorial-connect-virtual-networks-portal.md)。
* [创建具有不同部署模型的虚拟网络对等互连](../virtual-network/create-peering-different-deployment-models.md)。