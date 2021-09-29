---
title: Azure 中的公共 IP 地址
titleSuffix: Azure Virtual Network
description: 了解 Azure 中的公共 IP 地址。
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: conceptual
ms.date: 04/29/2021
ms.author: allensu
ms.openlocfilehash: 61ba348c93b034b5ed1419c22330e2ce842b7136
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124784197"
---
# <a name="public-ip-addresses"></a>公共 IP 地址

公共 IP 地址允许 Internet 资源与 Azure 资源进行入站通信。 公共 IP 地址使 Azure 资源能够与 Internet 和面向公众的 Azure 服务进行出站通信。 此地址专门用于该资源，直到你对其取消分配。 无公共 IP 的资源可以进行出站通信。 Azure 会动态分配非专用于该资源的可用 IP 地址。 有关 Azure 中的出站连接的详细信息，请参阅[了解出站连接](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。

在 Azure Resource Manager 中，[公共 IP](virtual-network-public-ip-address.md) 地址是具有其自身属性的资源。 可与公共 IP 地址资源关联的部分资源包括：

* 虚拟机网络接口
* 面向 Internet 的负载均衡器
* 虚拟网络网关 (VPN/ER)
* NAT 网关
* 应用程序网关
* Azure 防火墙
* 堡垒主机

对于虚拟机规模集，请使用[公共 IP 前缀](public-ip-address-prefix.md)。

## <a name="at-a-glance"></a>概览

下表显示了将公共 IP 可关联到资源的属性和分配方法。 请注意，目前并非所有资源类型都提供对公共 IPv6 的支持。

| 顶级资源 | IP 地址关联 | 动态 IPv4 | 动态 IPv4 | 动态 IPv6 | 静态 IPv6 |
| --- | --- | --- | --- | --- | --- |
| 虚拟机 |Linux |是 | 是 | 是 | 是 |
| 面向 Internet 的负载均衡器 |前端配置 |是 | 是 | 是 |是 |
| 虚拟网络网关 (VPN) |网关 IP 配置 |是（仅限非 AZ） |是（仅限 AZ） | 否 |否 |
| 虚拟网络网关 (ER) |网关 IP 配置 |是 | 否 | 是（预览版） |否 |
| NAT 网关 |网关 IP 配置 |否 |是 | 否 |否 |
| 应用程序网关 |前端配置 |是（仅限 V1） |是（仅限 V2） | 否 | 否 |
| Azure 防火墙 | 前端配置 | 否 | 是 | 否 | 否 |
| 堡垒主机 | 公共 IP 配置 | 否 | 是 | 否 | 否 |

## <a name="ip-address-version"></a>IP 地址版本

公共 IP 地址可使用 IPv4 或 IPv6 地址创建。 你还可以选择使用 IPv4 和 IPv6 地址创建双堆栈部署。

## <a name="sku"></a>SKU

使用以下 SKU 之一创建公共 IP 地址：

### <a name="standard"></a>标准

标准 SKU 公共 IP 地址：

- 始终使用静态分配方法。
- 具有可调整的入站发起流空闲超时，范围为 4-30 分钟，默认值为 4 分钟，出站发起流的空闲超时固定为 4 分钟。
- 旨在与“默认保护”模型保持一致，并在用作前端时对入站流量关闭。  需要使用[网络安全组](./network-security-groups-overview.md#network-security-groups) (NSG) 将数据平面流量加入允许列表（例如，在附加了标准 SKU 公共 IP 的虚拟机的 NIC 上执行此操作）。
- 可以是区域冗余的（从所有三个区域播发）、区域性的（在特定的预选择可用性区域中提供保证）或者非区域性的（不与特定的预选择可用性区域关联）。 若要详细了解可用性区域，请参阅[可用性区域概述](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)与[标准负载均衡器和可用性区域](../load-balancer/load-balancer-standard-availability-zones.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。 区域冗余 IP 只能在[三个可用性区域均处于活动状态的区域](../availability-zones/az-region.md)中创建。 在区域处于活动状态之前创建的 IP 不会是区域冗余的。
- 可以与[路由首选项](routing-preference-overview.md)一起使用，以便能够更精细地控制如何在 Azure 与 Internet 之间路由流量。
- 可用作[跨区域负载均衡器](../load-balancer/cross-region-overview.md)的任意播前端 IP（预览功能）。
 
> [!NOTE]
> 在创建并关联[网络安全组](./network-security-groups-overview.md#network-security-groups)且显式允许所需入站流量之前，到标准 SKU 资源的入站通信将会失败。

### <a name="basic"></a>基本

基本 SKU 地址：

- 对于 IPv4：可以使用动态或静态分配方法进行分配。  对于 IPv6：只能使用动态分配方法进行分配。
- 具有可调整的入站发起流空闲超时，范围为 4-30 分钟，默认值为 4 分钟，出站发起流的空闲超时固定为 4 分钟。
- 默认情况下处于打开状态。  建议使用网络安全组来对入站或出站流量进行限制，但这是可选的。
- 不支持可用性区域方案。 请为适用区域中的可用性区域方案使用标准 SKU 公共 IP。 若要详细了解可用性区域，请参阅[可用性区域概述](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)与[标准负载均衡器和可用性区域](../load-balancer/load-balancer-standard-availability-zones.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。
- 不支持[路由首选项](routing-preference-overview.md)或[跨区域负载均衡器](../load-balancer/cross-region-overview.md)功能。

> [!NOTE]
> 基本 SKU IPv4 地址可在创建后升级到标准 SKU。  若要了解有关 SKU 升级的信息，请参阅[公共 IP 升级](./public-ip-upgrade-portal.md)。

>[!IMPORTANT]
> 负载均衡器和公共 IP 资源需要匹配的 SKU。 不能混合使用基本 SKU 资源和标准 SKU 资源。 无法将独立的虚拟机、可用性集资源中的虚拟机或虚拟机规模集资源同时附加到两个 SKU。  新的设计应当考虑使用标准 SKU 资源。  有关详细信息，请查看[标准负载均衡器](../load-balancer/load-balancer-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。

## <a name="ip-address-assignment"></a>IP 地址分配

标准公共 IPv4、基本公共 IPv4 和标准公共 IPv6 地址都支持静态分配。  创建资源时，会为资源分配一个 IP 地址。 删除该资源时，将释放此 IP 地址。  

> [!NOTE]
> 即使将分配方法设置为“静态”，也无法通过指定方式将实际 IP 地址分配到公共 IP 地址资源。 Azure 会从创建资源时所在的 Azure 位置的可用 IP 地址池中分配 IP 地址。
>

以下情况通常使用静态公共 IP 地址：

* 必须更新防火墙规则才能与 Azure 资源通信。
* 对 DNS 名称进行解析时，如果更改了 IP 地址，则需更新 A 记录。
* Azure 资源可与使用基于 IP 地址的安全模型的其他应用或服务通信。
* 使用链接到 IP 地址的 TLS/SSL 证书。

基本公共 IPv4 和 IPv6 地址支持动态分配。  如果选择了动态分配，不会在创建资源时向资源提供 IP 地址。  将公共 IP 地址与资源关联时会分配 IP。 停止或删除该资源时，就会释放该 IP 地址。   例如，从名为“资源 A”的资源释放公共 IP 资源。如果重新分配了公共 IP 资源，则“资源 A”将在启动时接收另一个 IP。 如果分配方法从静态更改为动态，就会释放任何关联的 IP 地址 。 可将分配方法设置为静态，以确保 IP 地址始终相同。

> [!NOTE]
> Azure 会从每个 Azure 云中每个区域的唯一地址范围中分配公共 IP 地址。 对于 Azure [公有](https://www.microsoft.com/download/details.aspx?id=56519)云、[美国政府](https://www.microsoft.com/download/details.aspx?id=57063)云、[中国](https://www.microsoft.com/download/details.aspx?id=57062)云和[德国](https://www.microsoft.com/download/details.aspx?id=57064)云，可以下载范围（前缀）的列表。
>

## <a name="dns-name-label"></a>DNS 名称标签

选择该选项为公共 IP 资源指定 DNS 标签。 此功能适用于 IPv4 地址（32 位 A 记录）和 IPv6 地址（128 位 AAAA 记录）。   选择后，会为 domainnamelabel.location.cloudapp.azure.com 创建一个到 Azure 托管 DNS 中的公共 IP 的映射。 

例如，创建公共 IP，其中：

* 将 contoso 作为 domainnamelabel 
*  美国西部 Azure 位置

完全限定的域名 (FQDN) contoso.westus.cloudapp.azure.com 解析为该资源的公共 IP 地址。

> [!IMPORTANT]
> 所创建的每个域名标签在其 Azure 位置必须是唯一的。  

如果自定义域是使用公共 IP 的服务所需要的，则可将 [Azure DNS](../dns/dns-custom-domain.md?toc=%2fazure%2fvirtual-network%2ftoc.json#public-ip-address) 或外部 DNS 提供程序用于 DNS 记录。

## <a name="other-public-ip-address-features"></a>其他公共 IP 地址功能

还有可用于公共 IP 地址的其他属性。  

* 全局层级允许将公共 IP 地址用于跨区域负载均衡器。 
* Internet 的“路由首选项”选项可最大程度地减少流量在 Microsoft 网络上花费的时间，从而降低出口数据传输成本。

> [!NOTE]
> 目前，“层级”和“路由首选项”功能仅适用于标准 SKU IPv4 地址。  它们也不能同时在同一 IP 地址上使用。
>

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]

## <a name="limits"></a>限制 

Azure 中的[网络限制](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits)全面列出了 IP 寻址的限制。 这些限制根据区域和订阅设置。 [联系支持人员](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade)，根据业务需要增加到默认限制以上。

## <a name="pricing"></a>定价

公共 IP 地址会产生少许费用。 有关 Azure 中 IP 地址定价的详细信息，请阅读 [IP 地址定价](https://azure.microsoft.com/pricing/details/ip-addresses)页。

## <a name="limitations-for-ipv6"></a>IPv6 的限制

* VPN 网关不能在启用了 IPv6 的虚拟网络中使用，直接使用或与“UseRemoteGateway”对等互连均不能。
* 公共 IPv6 地址空闲超时 4 分钟就会锁定。
* Azure 不支持容器的 IPv6 通信。
* 不支持使用仅限 IPv6 的虚拟机或虚拟机规模集。 每个 NIC 必须至少包含一个 IPv4 IP 配置（双堆栈）。
* 将 IPv6 添加到现有 IPv4 部署时，无法将 IPv6 范围添加到具有现有资源导航链接的虚拟网络。
* Azure 公共 DNS 支持 IPv6 的正向 DNS。 不支持反向 DNS。
* 不支持路由首选项和跨区域负载均衡。

有关 Azure 中的 IPv6 的详细信息，请参阅[此文](./ipv6-overview.md)。

## <a name="next-steps"></a>后续步骤
* 了解 [Azure 中的专用 IP 地址](private-ip-addresses.md)
* [使用 Azure 门户通过静态公共 IP 部署 VM](virtual-network-deploy-static-pip-arm-portal.md)
