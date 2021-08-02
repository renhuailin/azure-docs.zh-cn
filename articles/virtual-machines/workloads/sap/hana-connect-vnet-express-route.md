---
title: 将虚拟网络连接到 Azure SAP HANA（大型实例）| Microsoft Docs
description: 了解如何将虚拟网络连接到 Azure SAP HANA（大型实例）。
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 6/1/2021
ms.author: madhukan
ms.custom: H1Hack27Feb2017, devx-track-azurepowershell
ms.openlocfilehash: e451f969c1518a920f7828d92fdfed65cd80f69c
ms.sourcegitcommit: c385af80989f6555ef3dadc17117a78764f83963
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2021
ms.locfileid: "111412378"
---
# <a name="connect-a-virtual-network-to-hana-large-instances"></a>将虚拟网络连接到 HANA 大型实例

你已[创建 Azure 虚拟网络](hana-connect-azure-vm-large-instances.md)。 现在，可以将该网络连接到 SAP HANA 大型实例（也称为 BareMetal 基础结构实例）。 本文将介绍需要执行的步骤。

## <a name="create-an-azure-expressroute-gateway-on-the-virtual-network"></a>在虚拟网络上创建 Azure ExpressRoute 网关

首先，在虚拟网络上创建 Azure ExpressRoute 网关。 通过此网关，可将虚拟网络链接到与 HANA 大型实例标记上的租户相连接的 ExpressRoute 线路。

> [!NOTE] 
> 完成此步骤最多需要 30 分钟。 在指定的 Azure 订阅中创建新网关，然后将其连接到指定的 Azure 虚拟网络。

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

- 如果网关已存在，请检查它是否为 ExpressRoute 网关。 如果不是 ExpressRoute 网关，请删除该网关，然后重新创建 ExpressRoute 网关。 如果已建立 ExpressRoute 网关，请跳过本文的以下部分[链接虚拟网络](#link-virtual-networks)。 

- 使用 [Azure 门户](https://portal.azure.com/)或 PowerShell 创建与虚拟网络连接的 ExpressRoute VPN 网关。
    - 如果使用 Azure 门户，请添加新的“虚拟网关”，再选择“ExpressRoute”作为网关类型 。
    - 如果使用 PowerShell，请首先下载并使用最新版 [Azure PowerShell SDK](https://azure.microsoft.com/downloads/)。 
 
    以下命令创建 ExpressRoute 网关。 前面带有 $ 的文本是用户定义的变量，应该使用具体信息进行更新。

    ```powershell
    # These Values should already exist, update to match your environment
    $myAzureRegion = "eastus"
    $myGroupName = "SAP-East-Coast"
    $myVNetName = "VNet01"
    
    # These values are used to create the gateway, update for how you wish the GW components to be named
    $myGWName = "VNet01GW"
    $myGWConfig = "VNet01GWConfig"
    $myGWPIPName = "VNet01GWPIP"
    $myGWSku = "UltraPerformance" # Supported values for HANA large instances are: UltraPerformance
    
    # These Commands create the Public IP and ExpressRoute Gateway
    $vnet = Get-AzVirtualNetwork -Name $myVNetName -ResourceGroupName $myGroupName
    $subnet = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
    New-AzPublicIpAddress -Name $myGWPIPName -ResourceGroupName $myGroupName `
    -Location $myAzureRegion -AllocationMethod Dynamic
    $gwpip = Get-AzPublicIpAddress -Name $myGWPIPName -ResourceGroupName $myGroupName
    $gwipconfig = New-AzVirtualNetworkGatewayIpConfig -Name $myGWConfig -SubnetId $subnet.Id `
    -PublicIpAddressId $gwpip.Id
    
    New-AzVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName -Location $myAzureRegion `
    -IpConfigurations $gwipconfig -GatewayType ExpressRoute `
    -GatewaySku $myGWSku -VpnType PolicyBased -EnableBgp $true
    ```

Azure SAP HANA（大型实例）唯一支持的网关 SKU 为 UltraPerformance。

## <a name="link-virtual-networks"></a>链接虚拟网络

Azure 虚拟网络现已包含 ExpressRoute 网关。 使用 Microsoft 提供的授权信息，将 ExpressRoute 网关连接到 SAP HANA 大型实例 ExpressRoute 线路。 可以使用 Azure 门户或 PowerShell 进行连接。 以下是 PowerShell 的使用说明。 

请对每个连接使用不同的 AuthGUID，从而对每个 ExpressRoute 网关运行以下命令。 下面脚本中的前两个条目来自 Microsoft 提供的信息。 此外，AuthGUID 特定于每个虚拟网络及其网关。 如果要添加另一个 Azure 虚拟网络，需要为将 HANA 大型实例连接到 Microsoft Azure 的 ExpressRoute 线路获取另一个 AuthID。 

```powershell
# Populate with information provided by Microsoft Onboarding team
$PeerID = "/subscriptions/9cb43037-9195-4420-a798-f87681a0e380/resourceGroups/Customer-USE-Circuits/providers/Microsoft.Network/expressRouteCircuits/Customer-USE01"
$AuthGUID = "76d40466-c458-4d14-adcf-3d1b56d1cd61"

# Your ExpressRoute Gateway information
$myGroupName = "SAP-East-Coast"
$myGWName = "VNet01GW"
$myGWLocation = "East US"

# Define the name for your connection
$myConnectionName = "VNet01GWConnection"

# Create a new connection between the ER Circuit and your Gateway using the Authorization
$gw = Get-AzVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName
    
New-AzVirtualNetworkGatewayConnection -Name $myConnectionName `
-ResourceGroupName $myGroupName -Location $myGWLocation -VirtualNetworkGateway1 $gw `
-PeerId $PeerID -ConnectionType ExpressRoute -AuthorizationKey $AuthGUID -ExpressRouteGatewayBypass
```

> [!NOTE]
> AzVirtualNetworkGatewayConnection 命令的最后一个参数 ExpressRouteGatewayBypass 是启用 ExpressRoute FastPath 的新参数。 这项于 2019 年 5 月添加的功能可缩短 HANA 大型实例单元和 Azure VM 之间的网络延迟。 有关详细信息，请参阅 [SAP HANA（大型实例）网络体系结构](./hana-network-architecture.md)。 运行命令之前，请确保运行的是最新版本的 PowerShell cmdlet。

可能需要将网关连接到多个与订阅关联的 ExpressRoute 线路。 在这种情况下，需要多次运行此步骤。 例如，可能要将同一个虚拟网络网关连接到将虚拟网络连接到本地网络的 ExpressRoute 线路。

## <a name="applying-expressroute-fastpath-to-existing-hana-large-instance-expressroute-circuits"></a>将 ExpressRoute FastPath 应用到现有 HANA 大型实例 ExpressRoute 线路
你已经知道如何将通过 HANA 大型实例部署创建的新 ExpressRoute 线路连接到某个 Azure 虚拟网络的 Azure ExpressRoute 网关。 但是，如果已设置 ExpressRoute 线路，并且虚拟网络已连接到 HANA 大型实例怎么办？ 

新的 ExpressRoute FastPath 可减少网络延迟。 建议应用更改，以利用这种减少的延迟。 用于连接新 ExpressRoute 线路和更改现有 ExpressRoute 线路的命令相同。 因此，需要运行此 PowerShell 命令序列，以更改现有线路。 

```powershell
# Populate with information provided by Microsoft Onboarding team
$PeerID = "/subscriptions/9cb43037-9195-4420-a798-f87681a0e380/resourceGroups/Customer-USE-Circuits/providers/Microsoft.Network/expressRouteCircuits/Customer-USE01"
$AuthGUID = "76d40466-c458-4d14-adcf-3d1b56d1cd61"

# Your ExpressRoute Gateway information
$myGroupName = "SAP-East-Coast"
$myGWName = "VNet01GW"
$myGWLocation = "East US"

# Define the name for your connection
$myConnectionName = "VNet01GWConnection"

# Create a new connection between the ER Circuit and your Gateway using the Authorization
$gw = Get-AzVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName
    
New-AzVirtualNetworkGatewayConnection -Name $myConnectionName `
-ResourceGroupName $myGroupName -Location $myGWLocation -VirtualNetworkGateway1 $gw `
-PeerId $PeerID -ConnectionType ExpressRoute -AuthorizationKey $AuthGUID -ExpressRouteGatewayBypass
```

务必添加如上所示的最后一个参数，以启用 ExpressRoute Fast Path 功能。


## <a name="expressroute-global-reach"></a>ExpressRoute Global Reach

为以下场景之一启用 Global Reach：

 - HANA 系统复制（没有任何额外的代理或防火墙）。
 - 在两个不同区域中的 HANA 大型实例单元之间复制备份，以执行系统复制或系统刷新。

若要启用 Global Reach：

- 提供一个 /29 地址空间的地址空间范围。 该地址范围可能不会与你目前使用的将 HANA 大型实例连接到 Azure 的任何其他地址空间范围重叠。 地址范围不应与 Azure 或本地其他位置使用的任何 IP 地址范围重叠。
- 可用于将本地路由播发到 HANA 大型实例的自治系统编号 (ASN) 存在限制。 本地不能播发专用 ASN 在 65000 - 65020 或 65515 范围内的任何路由。 
- 在将本地直接访问连接到 HANA 大型实例时，需要计算连接到 Azure 的线路的费用。 有关详细信息，请查看 [Global Reach 附加产品](https://azure.microsoft.com/pricing/details/expressroute/)定价。

若要在部署中应用这两个或其中一个方案，请使用 Azure 打开支持消息，如[为 HANA 大型实例打开支持请求](./hana-li-portal.md#open-a-support-request-for-hana-large-instances)中所述

Microsoft 路由和执行请求所需的数据和关键字如下所示：

- 服务：SAP HANA 大型实例
- 问题类型：配置和设置
- 问题子类型：上面未列出我的问题。
- 主题“修改我的网络 - 添加 Global Reach”
- 详细信息：“将 Global Reach 到 HANA 大型实例添加到 HANA 大型实例租户。” 或“将 Global Reach 到本地添加到 HANA 大型实例租户。”
- HANA 大型实例到 HANA 大型实例租户案例的其他详细信息：需要定义要连接的两个租户所在的两个 Azure 区域，并且需要提交 /29 IP 地址范围。
- 有关本地到 HANA 大型实例租户案例的其他详细信息： 
    - 定义要直接连接到的部署 HANA 大型实例租户的 Azure 区域。 
    - 提供在本地与 Azure 之间建立 ExpressRoute 线路时收到的 Auth GUID 和线路对等 ID。 
    - 命名 ASN。 
    - 为 ExpressRoute Global Reach 提供 /29 IP 地址范围。

> [!NOTE]
> 如果要同时处理这两类事例，需要提供两个不同的不会与目前使用的任何其他 IP 地址范围重叠的 /29 IP 地址范围。 

## <a name="next-steps"></a>后续步骤

了解在 Azure 上部署 SAP HANA 大型实例可能需要的其他网络要求。

> [!div class="nextstepaction"]
> [大型实例的其他网络要求](hana-additional-network-requirements.md)
