---
title: 虚拟网络到 Azure SAP HANA（大型实例）的连接设置 | Microsoft Docs
description: 设置虚拟网络连接以使用 Azure SAP HANA（大型实例）。
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/25/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 540247752be27af268a0485ea9eb68d121a25240
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/22/2021
ms.locfileid: "104775299"
---
# <a name="connect-a-virtual-network-to-hana-large-instances"></a>将虚拟网络连接到 HANA 大型实例

创建 Azure 虚拟网络后，可将该网络连接到 Azure SAP HANA 大型实例。 在虚拟网络上创建 Azure ExpressRoute 网关。 通过此网关，可将虚拟网络链接到与 HANA 大型实例标记上的客户租户相连接的 ExpressRoute 线路。

> [!NOTE] 
> 完成此步骤最多需要 30 分钟。 在指定的 Azure 订阅中创建新的网关，然后将其连接到指定的 Azure 虚拟网络。

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

如果网关已存在，请检查它是否为 ExpressRoute 网关。 如果不是 ExpressRoute 网关，请删除该网关，然后重新创建 ExpressRoute 网关。 如果已建立 ExpressRoute 网关，请参阅本文的以下部分“链接虚拟网络”。 

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

Azure SAP HANA （大型实例）唯一支持的网关 SKU 为 UltraPerformance。

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
> AzVirtualNetworkGatewayConnection 命令的最后一个参数 ExpressRouteGatewayBypass 是启用 ExpressRoute Fast Path 的新参数。 ExpressRoute Fast Path 功能可缩短 HANA 大型实例单元和 Azure VM 之间的网络延迟。 此功能于 2019 年 5 月添加。 有关更多详细信息，请参阅文章 [SAP HANA（大型实例）网络体系结构](./hana-network-architecture.md)。 运行命令之前，请确保运行的是最新版本的 PowerShell cmdlet。

若要将网关连接到与订阅关联的多个 ExpressRoute 线路，建议多次运行这一步骤。 例如，可能要将同一个虚拟网络网关连接到将虚拟网络连接到本地网络的 ExpressRoute 线路。

## <a name="applying-expressroute-fast-path-to-existing-hana-large-instance-expressroute-circuits"></a>将 ExpressRoute Fast Path 应用到现有 HANA 大型实例 ExpressRoute 线路
目前为止，本文档介绍了如何将通过 HANA 大型实例部署创建的新 ExpressRoute 线路连接到某个 Azure 虚拟网络的 Azure ExpressRoute 网关。 但许多客户已设置好 ExpressRoute 线路，其虚拟网络也已连接到 HANA 大型实例。 由于新 ExpressRoute Fast Path 缩短了网络延迟，因此建议应用更改以使用此功能。 用于连接新 ExpreesRoute 线路和更改现有 ExpressRoute 线路的命令相同。 因此，需要运行此 PowerShell 命令序列，以更改要使用的现有线路 

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

务必添加如上所示的最后一个参数，以启用 ExpressRoute Fast Path 功能


## <a name="expressroute-global-reach"></a>ExpressRoute Global Reach
如果要为以下两种方案或其中一个启用 Global Reach：

 - 执行 HANA 系统复制（没有任何额外的代理或防火墙）
 - 在两个不同区域中的 HANA 大型实例单元之间复制备份，以执行系统复制或系统刷新

需要考虑以下事项：

- 需要提供一个 /29 地址空间的地址空间范围。 该地址范围不能与目前将 HANA 大型实例连接到 Azure 时所用的任何其他地址空间范围重叠，也不能与在 Azure 或本地的其他任何位置使用的任何 IP 地址范围重叠。
- 可用于将本地路由播发到 HANA 大型实例的 ASN（自治系统编号）存在限制。 本地不能播发专用 ASN 为 65000 - 65020 或 65515 的任何路由。 
- 对于为实现本地直接访问连接到 HANA 大型实例的方案，需要计算连接到 Azure 的线路的费用。 请查看 [Global Reach 附加产品](https://azure.microsoft.com/pricing/details/expressroute/)价格来了解定价。

若要在部署中应用这两个或其中一个方案，请使用 Azure 打开支持消息，如[为 HANA 大型实例打开支持请求](./hana-li-portal.md#open-a-support-request-for-hana-large-instances)中所述

所需的数据和让 Microsoft 能够按请求进行路由和执行的关键字如下所示：

- 服务：SAP HANA 大型实例
- 问题类型：配置和设置
- 问题子类型：上面未列出我的问题
- 主题“修改我的网络 - 添加 Global Reach”
- 详细信息：“将 Global Reach 添加到‘HANA 大型实例到 HANA 大型实例租户’”或“将 Global Reach 添加到‘本地到 HANA 大型实例租户’。
- HANA 大型实例到 HANA 大型实例租户的其他详细信息：需要定义要连接的两个租户所在的两个 Azure 区域，并且需要提交 /29 IP 地址范围  
- 本地到 HANA 大型实例租户的其他详细信息：需要定义想直接连接到的 HANA 大型实例租户所部署的 Azure区域。 此外，还需要提供在本地与 Azure 之间建立 ExpressRoute 线路时收到的 Auth GUID 和线路对等 ID 。 此外，还需要命名 ASN。 最后需提供的是 ExpressRoute Global Reach 的 /29 IP 地址范围。

> [!NOTE]
> 如果要同时处理这两类事例，需要提供两个不同的不会与目前使用的任何其他 IP 地址范围重叠的 /29 IP 地址范围。 




## <a name="next-steps"></a>后续步骤

- [HLI 的其他网络要求](hana-additional-network-requirements.md)
