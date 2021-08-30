---
title: 关于 ExpressRoute 虚拟网络网关 - Azure | Microsoft Docs
description: 了解 ExpressRoute 的虚拟网络网关。 本文包含有关网关 SKU 和类型的信息。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 04/23/2021
ms.author: duau
ms.openlocfilehash: 672fac2b33ef1d8fd9be1948d0c7da332f8ce43b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121733860"
---
# <a name="about-expressroute-virtual-network-gateways"></a>关于 ExpressRoute 虚拟网络网关

若要通过 ExpressRoute 连接 Azure 虚拟网络和本地网络，必须首先创建虚拟网络网关。 虚拟网络网关有两个用途：在网络之间交换 IP 路由和路由网络流量。 本文介绍网关类型、网关 SKU 和按 SKU 估算的性能。 本文还介绍了 ExpressRoute [FastPath](#fastpath)，这是一项功能，可让你本地网络中的网络流量绕过虚拟网络网关，从而提高性能。

## <a name="gateway-types"></a>网关类型

创建虚拟网络网关时，需要指定几项设置。 其中一个必要设置“-GatewayType”指定是否将网关用于 ExpressRoute 或 VPN 流量。 两种网关类型是：

* **Vpn** - 若要通过公共 Internet 发送加密流量，请使用网关类型“Vpn”。 这也称为 VPN 网关。 站点到站点连接、点到站点连接和 VNet 到 VNet 连接都使用 VPN 网关。

* **ExpressRoute** - 若要在专用连接上发送网络流量，请使用网关类型“ExpressRoute”。 这也称为 ExpressRoute 网关，是配置 ExpressRoute 时使用的网关类型。

对于每种网关类型，每个虚拟网络只能有一个虚拟网络网关。 例如，一个虚拟网络网关使用 -GatewayType Vpn，另一个使用 -GatewayType ExpressRoute。

## <a name="gateway-skus"></a><a name="gwsku"></a>网关 SKU
[!INCLUDE [expressroute-gwsku-include](../../includes/expressroute-gwsku-include.md)]

如果想要将网关升级为功能更强大的网关 SKU，在大多数情况下，可以使用“Resize-AzVirtualNetworkGateway”PowerShell cmdlet。 此方法适用于升级到 Standard 和 HighPerformance SKU。 但是，若要将非可用性区域 (AZ) 网关升级到 UltraPerformance SKU，需要重新创建网关。 重新创建网关会导致停机。 无需删除并重新创建网关来升级已启用 AZ 的 SKU。
### <a name="feature-support-by-gateway-sku"></a><a name="gatewayfeaturesupport"></a>网关 SKU 的功能支持
下表显示了每种网关类型支持的功能。

|**网关 SKU**|**VPN 网关和 ExpressRoute 共存**|**FastPath**|**最大线路连接数**|
| --- | --- | --- | --- |
|**标准 SKU/ErGw1AZ**|是|否|4|
|**高 Perf SKU/ERGw2Az**|是|否|8
|**超高性能 SKU/ErGw3AZ**|是|是|16

### <a name="estimated-performances-by-gateway-sku"></a><a name="aggthroughput"></a>预估性能（按网关 SKU）
下表显示网关类型和估计性能规模数。 这些数字派生自以下测试条件，且表示最大支持限制。 实际性能可能会有所不同，具体取决于流量复制测试条件的紧密程度。

### <a name="testing-conditions"></a>测试条件
##### <a name="standardergw1az"></a>**标准/ERGw1Az** #####

- 线路带宽：1Gbps
- 网关播发的路由数：500
- 已学习的路由数：4,000
##### <a name="high-performanceergw2az"></a>**高性能/ERGw2Az** #####

- 线路带宽：1Gbps
- 网关播发的路由数：500
- 已学习的路由数：9,500
##### <a name="ultra-performanceergw3az"></a>**超高性能/ErGw3Az** #####

- 线路带宽：1Gbps
- 网关播发的路由数：500
- 已学习的路由数：9,500

 此表适用于 Resource Manager 与经典部署模型。
 
|**网关 SKU**|**每秒连接数**|**每秒兆位**|**每秒的数据包数**|**虚拟网络中受支持的 VM 数量**|
| --- | --- | --- | --- | --- |
|**标准/ERGw1Az**|7,000|1,000|100,000|2,000|
|**高性能/ERGw2Az**|14,000|2,000|250,000|4,500|
|**超高性能/ErGw3Az**|16,000|10,000|1,000,000|11,000|

> [!IMPORTANT]
> 应用程序性能取决于多种因素，例如端到端延迟和应用程序打开的流量数。 表中的数字表示应用程序在理想环境下理论上可达到的上限。

>[!NOTE]
> 所有网关可从同一对等互连位置连接到同一虚拟网络的 ExpressRoute 线路的最大数目为 4。
>

## <a name="gateway-subnet"></a><a name="gwsub"></a>网关子网

在创建 ExpressRoute 网关之前，必须创建一个网关子网。 网关子网包含虚拟网络网关 VM 和服务使用的 IP 地址。 创建虚拟网络网关时，会将网关 VM 部署到网关子网，并使用所需的 ExpressRoute 网关设置进行配置。 永远不要将任何其他设备（例如，其他 VM）部署到网关子网。 网关子网必须命名为“GatewaySubnet”才能正常工作。 将网关子网命名为“GatewaySubnet”，可以让 Azure 知道这就是要将虚拟网络网关 VM 和服务部署到的目标子网。

>[!NOTE]
>[!INCLUDE [vpn-gateway-gwudr-warning.md](../../includes/vpn-gateway-gwudr-warning.md)]
>

创建网关子网时，请指定子网包含的 IP 地址数。 将网关子网中的 IP 地址分配到网关 VM 和网关服务。 有些配置需要具有比其他配置更多的 IP 地址。 

规划网关子网大小时，请参阅你计划创建的配置的相关文档。 例如，ExpressRoute/VPN 网关共存配置所需的网关子网大于大多数其他配置。 此外，可能需要确保网关子网包含足够多的 IP 地址，以便应对将来可能会添加的配置。 尽管网关子网最小可创建为 /29，但建议创建 /27 或更大（/27、/26 等）的网关子网（如果你有可用的地址空间来执行此操作）。 如果计划将 16 个 ExpressRoute 线路连接到网关，则必须创建 /26 或更大的网关子网。 如果创建的是双堆栈网关子网，建议还是使用 /64 或更大的 IPv6 范围。 这将适合大多数配置。

以下 Resource Manager PowerShell 示例显示名为 GatewaySubnet 的网关子网。 可以看到，CIDR 表示法指定了 /27，这可提供足够的 IP 地址供大多数现有配置使用。

```azurepowershell-interactive
Add-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/27
```

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

### <a name="zone-redundant-gateway-skus"></a><a name="zrgw"></a>区域冗余型网关 SKU

也可以在 Azure 可用性区域中部署 ExpressRoute 网关。 这在物理上和逻辑上将它们分成不同的可用区域，从而保护本地网络与 Azure 的连接免受区域级故障的影响。

![区域冗余型 ExpressRoute 网关](./media/expressroute-about-virtual-network-gateways/zone-redundant.png)

区域冗余型网关使用 ExpressRoute 网关的特定新网关 SKU。

* ErGw1AZ
* ErGw2AZ
* ErGw3AZ

新的网关 SKU 还支持其他部署选项，以最好地满足你的需求。 使用新网关 SKU 创建虚拟网络网关时，还可以选择在特定区域中部署网关。 这称为区域网关。 部署区域网关时，网关的所有实例都部署在同一可用性区域中。

> [!IMPORTANT]
> 如果打算在 ExpressRoute 上使用基于 IPv6 的专用对等互连，请确保为在双堆栈网关子网中部署的网关选择 AZ SKU。
> 
>

## <a name="fastpath"></a><a name="fastpath"></a>FastPath

ExpressRoute 虚拟网络网关旨在交换网络路由和路由网络流量。 FastPath 旨在改善本地网络和虚拟网络之间的数据路径性能。 启用后，FastPath 会绕过网关直接将网络流量发送到虚拟网络中的虚拟机。

有关 FastPath 的详细信息，包括限制和要求，请参阅[关于 FastPath](about-fastpath.md)。

## <a name="rest-apis-and-powershell-cmdlets"></a><a name="resources"></a>REST API 和 PowerShell cmdlet
有关将 REST API 和 PowerShell cmdlet 用于虚拟网络网关配置的其他技术资源和特定语法要求，请参阅以下页面：

| **经典** | **资源管理器** |
| --- | --- |
| [PowerShell](/powershell/module/servicemanagement/azure.service/#azure) |[PowerShell](/powershell/module/az.network#networking) |
| [REST API](/previous-versions/azure/reference/jj154113(v=azure.100)) |[REST API](/rest/api/virtual-network/) |

## <a name="next-steps"></a>后续步骤

有关可用连接配置的详细信息，请参阅 [ExpressRoute 概述](expressroute-introduction.md)。

有关创建 ExpressRoute 网关的详细信息，请参阅[创建 ExpressRoute 的虚拟网络网关](expressroute-howto-add-gateway-resource-manager.md)。

有关配置区域冗余型网关的详细信息，请参阅[创建区域冗余型虚拟网络网关](../../articles/vpn-gateway/create-zone-redundant-vnet-gateway.md)。

有关 FastPath 的详细信息，请参阅[关于 FastPath](about-fastpath.md)。
