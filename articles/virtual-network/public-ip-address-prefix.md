---
title: Azure 公共 IP 地址前缀
titleSuffix: Azure Virtual Network
description: 了解 Azure 公共 IP 地址前缀的涵义以及如何借助该前缀向资源分配公共 IP 地址。
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: conceptual
ms.date: 05/20/2021
ms.author: allensu
ms.openlocfilehash: 32f5cbc7be833b823259052152be57c2d18cec25
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121729649"
---
# <a name="public-ip-address-prefix"></a>公共 IP 地址前缀

公共 IP 地址前缀是 Azure 中预留的[公共 IP 地址](./public-ip-addresses.md#public-ip-addresses)范围。 公共 IP 前缀是从各 Azure 区域的地址池分配的。 通过指定名称和前缀大小，在 Azure 区域和订阅中创建公共 IP 地址前缀。 前缀大小是可供使用的地址数。 公共 IP 地址前缀由 IPv4 或 IPv6 地址组成。  在部署了可用性区域的区域中，可以创建区域冗余的或与特定可用性区域关联的公共 IP 地址前缀。  创建公共 IP 前缀后，可以创建公共 IP 地址。

## <a name="benefits"></a>优点

- 可以从已知范围创建静态公共 IP 地址资源。 使用前缀创建的地址可以分配到可向其分配标准 SKU 公共 IP 地址的任何 Azure 资源。
- 删除单个公共 IP 后，它们会被返回到保留的范围，以便以后重复使用。 将为你保留公共 IP 地址前缀中的 IP 地址以供你使用，直到你将其删除为止。
- 你可以看到在前缀范围内提供的和可用的IP地址。

## <a name="prefix-sizes"></a>前缀大小

可使用以下公共 IP 前缀：

-  /28 (IPv4) 或 /124 (IPv6) = 16 个地址
-  /29 (IPv4) 或 /125 (IPv6) = 8 个地址
-  /30 (IPv4) 或 /126 (IPv6) = 4 个地址
-  /31 (IPv4) 或 /127 (IPv6) = 2 个地址

前缀大小指定为无类域间路由 (CIDR) 掩码大小。

订阅中创建的前缀数量没有限制。 创建的范围数不能超过订阅中允许的静态公共 IP 地址。 有关详细信息，请参阅 [Azure 限制](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)。

## <a name="scenarios"></a>方案
可将以下资源关联到来自前缀的静态公共 IP 地址：

|资源|方案|步骤|
|---|---|---|
|虚拟机| 将来自某个前缀的公共 IP 关联到 Azure 中的虚拟机可在将 IP 地址添加到防火墙中的允许列表时降低管理开销。 可以使用单个防火墙规则添加整个前缀。 缩放 Azure 中的虚拟机时，可关联来自同一前缀的 IP，从而节省成本、时间和管理开销。| 将来自某个前缀的 IP 关联到虚拟机： </br> 1.[创建前缀。](manage-public-ip-address-prefix.md) </br> 2.[从前缀创建 IP。](manage-public-ip-address-prefix.md) </br> 3.[将 IP 关联到虚拟机网络接口。](virtual-network-network-interface-addresses.md#add-ip-addresses) </br> 也可以[将 IP 关联到虚拟机规模集](https://azure.microsoft.com/resources/templates/vmms-with-public-ip-prefix/)。
| 标准负载均衡器 | 将来自某个前缀的公共 IP 关联到负载均衡器的前端 IP 配置或出站规则可确保简化 Azure 公共 IP 地址空间。 可以通过清理源自某个连续 IP 地址范围的出站连接来简化你的方案。 | 若要将来自某个前缀的 IP 关联到负载均衡器，请执行以下操作： </br> 1.[创建前缀。](manage-public-ip-address-prefix.md) </br> 2.[从前缀创建 IP。](manage-public-ip-address-prefix.md) </br> 3.创建负载均衡器时，请选择或更新在前面第 2 步中创建的 IP 作为负载均衡器的前端 IP。 |
| Azure 防火墙 | 可使用来自前缀的公共 IP 地址作为出站 SNAT。 所有出站虚拟网络流量都会转换为 [Azure 防火墙](../firewall/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)公共 IP。 | 若要将来自某个前缀的 IP 关联到防火墙，请执行以下操作： </br> 1.[创建前缀。](manage-public-ip-address-prefix.md) </br> 2.[从前缀创建 IP。](manage-public-ip-address-prefix.md) </br> 3.[部署 Azure 防火墙](../firewall/tutorial-firewall-deploy-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-the-firewall)时，请务必选择你以前从该前缀提供的 IP。|
| VPN 网关 (AZ SKU)、应用程序网关 v2、NAT 网关 | 可使用来自前缀的公共 IP 地址作为网关 | 若要将来自某个前缀的 IP 关联到网关，请执行以下操作： </br> 1.[创建前缀。](manage-public-ip-address-prefix.md) </br> 2.[从前缀创建 IP。](manage-public-ip-address-prefix.md) </br> 3. 部署 [VPN 网关](../vpn-gateway/tutorial-create-gateway-portal.md)[应用程序网关](../application-gateway/quick-create-portal.md#create-an-application-gateway)或 [NAT 网关](./nat-gateway/tutorial-create-nat-gateway-portal.md)时，请务必选择你以前从该前缀提供的 IP。|

此外，公共 IP 地址前缀资源可直接由某些资源使用：

资源|方案|步骤|
|---|---|---|
|虚拟机规模集 | 你可以使用公共 IP 地址前缀在虚拟机规模集中生成实例级 IP，但系统不会创建单个公共 IP 资源。 | 使用带有说明的[模板](https://azure.microsoft.com/resources/templates/vmms-with-public-ip-prefix/)在规模集创建过程中将此前缀用于公共 IP 配置。 |
| 标准负载均衡器 | 可通过[将范围内的所有 IP 用于出站连接](../load-balancer/outbound-rules.md#scale)，使用公共 IP 地址前缀来缩放负载均衡器。 | 要将前缀关联到负载均衡器： </br> 1.[创建前缀。](manage-public-ip-address-prefix.md) </br> 2. 创建负载均衡器时，请选择与负载均衡器的前端相关联的 IP 前缀。 |
| NAT 网关 | 公共 IP 地址前缀可以用于缩放 NAT 网关，方法是使用前缀中的公共 IP 进行出站连接。 | 要将前缀关联到 NAT 网关： </br> 1.[创建前缀。](manage-public-ip-address-prefix.md) </br> 2. 创建 NAT 网关时，请选择 IP 前缀作为出站 IP。 |
| VPN 网关 (AZ SKU) 或应用程序网关 v2 | 可以使用区域冗余 VPN 或应用程序网关 v2 前缀中的公共 IP。 | 若要将来自某个前缀的 IP 关联到网关，请执行以下操作： </br> 1.[创建前缀。](manage-public-ip-address-prefix.md) </br> 2.[从前缀创建 IP。](manage-public-ip-address-prefix.md) </br> 3. 部署 [VPN 网关](../vpn-gateway/tutorial-create-gateway-portal.md)或[应用程序网关](../application-gateway/quick-create-portal.md#create-an-application-gateway)时，请务必选择你以前从该前缀提供的 IP。|

## <a name="limitations"></a>限制

- 不能指定前缀的 IP 地址。 Azure 将根据你指定的大小提供前缀的 IP 地址。  此外，从该前缀创建的所有公共 IP 地址都必须存在于该前缀所在的 Azure 区域和订阅中。 地址必须分配给同一区域和订阅中的资源。
- 最多可以创建具有 16 个 IP 地址的前缀。 查看[网络限制增加请求](../azure-portal/supportability/networking-quota-requests.md)和 [Azure 限制](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)以获取详细信息。
- 创建前缀后，无法修改范围的大小。
- 仅使用标准 SKU 创建的静态公共 IP 地址可从前缀范围进行分配。 若要详细了解公共 IP 地址 SKU，请参阅[公共 IP 地址](./public-ip-addresses.md#public-ip-addresses)。
- 范围中的地址只能分配到 Azure 资源管理器资源。 这些地址不能分配到经典部署模型中的资源。
- 如果前缀中的任何地址被分配到与某个资源关联的公共 IP 地址资源，则无法删除该前缀。 首先应取消关联所有公共 IP 地址资源，这些资源通过前缀分配有 IP 地址。 有关解除公共 IP 地址关联的详细信息，请参阅[管理公共 IP 地址](./virtual-network-public-ip-address.md#view-modify-settings-for-or-delete-a-public-ip-address)。
- 公共 IP 地址前缀目前与 Internet、路由首选项或全局层（对于跨区域负载均衡）不兼容。
- 仅具有动态分配的基本公共 IP 支持 IPv6。 动态分配是指在 Azure 中删除和重新部署资源后，IPv6 地址将发生更改。 
- 标准 IPv6 公共 IP 支持静态（保留）分配。 
- 标准内部负载均衡器支持从它们被分配到的子网内进行动态分配。  

## <a name="pricing"></a>定价
 
有关 Azure 公共 IP（单个 IP 地址和 IP 范围）的使用成本，请参阅[公共 IP 地址定价](https://azure.microsoft.com/pricing/details/ip-addresses/)。

## <a name="next-steps"></a>后续步骤

- [创建](manage-public-ip-address-prefix.md)公共 IP 地址前缀
