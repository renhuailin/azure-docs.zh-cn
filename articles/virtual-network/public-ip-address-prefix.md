---
title: Azure 公共 IP 地址前缀
description: 了解 Azure 公共 IP 地址前缀的涵义以及如何借助该前缀向资源分配可预测的公共 IP 地址。
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/29/2020
ms.author: allensu
ms.openlocfilehash: 7671ccecb4bbda17853b33989b7a7bc2cdbd6f89
ms.sourcegitcommit: a434cfeee5f4ed01d6df897d01e569e213ad1e6f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/09/2021
ms.locfileid: "111809757"
---
# <a name="public-ip-address-prefix"></a>公共 IP 地址前缀

公共 IP 地址前缀是 Azure 中预留的 IP 地址范围。 Azure 根据你指定的数量为你的订阅分配一个连续的地址范围。 

如果不熟悉公共地址，请参阅[公共 IP 地址](./public-ip-addresses.md#public-ip-addresses)。

公共 IP 地址是从各 Azure 区域的地址池分配的。 可[下载](https://www.microsoft.com/download/details.aspx?id=56519) Azure 在每个区域使用的范围列表。 例如，40.121.0.0/16 是 Azure 在美国东部区域中使用的 100 多个范围之一。 该范围包括从 40.121.0.1 到 40.121.255.254 的可用地址。

可通过指定一个名称和希望前缀包括的地址数，在 Azure 区域和订阅中创建公共 IP 地址前缀。 

公共 IP 地址范围将分配有你选择的前缀。 如果创建了前缀 /28，Azure 将从其范围之一提供16个 IP 地址。

除非创建范围，否则你不会知道 Azure 将分配哪个范围给你，但这些地址是连续的。 

公共 IP 地址前缀会产生费用，有关详细信息，请参阅[公共 IP 地址定价](https://azure.microsoft.com/pricing/details/ip-addresses)。

## <a name="why-create-a-public-ip-address-prefix"></a>为什么要创建公共 IP 地址前缀？

创建公共 IP 地址资源时，Azure 会从该区域中使用的任何一个范围分配可用的公共 IP 地址。 

在 Azure 分配 IP 地址之前，你不会知道确切的 IP。 此过程可能会在你创建允许特定 IP 地址的防火墙规则时造成问题。 对于添加的每个 IP 地址，必须添加相应的防火墙规则。

从公共 IP 地址前缀向资源分配地址时，无需防火墙规则更新。 整个范围将添加到规则。

## <a name="benefits"></a>优点

- 可以从已知范围创建公共 IP 地址资源。
- 防火墙规则配置带有范围，这些范围包括你当前已分配的公共 IP 地址以及你尚未分配的地址。 有了这样的配置，便无需在你向新资源分配 IP 地址时更改防火墙规则。
- 可创建的默认范围大小为 /28 或 16 个 IP 地址。
- 可创建的范围的数量没有限制。 Azure 订阅中可包括的静态公共 IP 地址的最大数有一定限制。 你创建的范围数包含的静态公共 IP 地址数不能超过你的订阅中可包含的静态公共 IP 地址数。 有关详细信息，请参阅 [Azure 限制](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)。
- 使用来自前缀的地址创建的地址可以分配到可向其分配公共 IP 地址的任何 Azure 资源。
- 可轻松查看该范围中哪些 IP 地址已提供，哪些 IP 地址尚未提供。

## <a name="scenarios"></a>方案
可将以下资源关联到来自前缀的静态公共 IP 地址：

|资源|方案|步骤|
|---|---|---|
|虚拟机| 将来自某个前缀的公共 IP 关联到 Azure 中的虚拟机可在将 IP 地址添加到防火墙中的允许列表时降低管理开销。 可以使用单个防火墙规则添加整个前缀。 缩放 Azure 中的虚拟机时，可关联来自同一前缀的 IP，从而节省成本、时间和管理开销。| 将来自某个前缀的 IP 关联到虚拟机： </br> 1.[创建前缀。](manage-public-ip-address-prefix.md) </br> 2.[从前缀创建 IP。](manage-public-ip-address-prefix.md) </br> 3.[将 IP 关联到虚拟机网络接口。](virtual-network-network-interface-addresses.md#add-ip-addresses) </br> 也可以[将 IP 关联到虚拟机规模集](https://azure.microsoft.com/resources/templates/vmms-with-public-ip-prefix/)。
| 标准负载均衡器 | 将来自某个前缀的公共 IP 关联到负载均衡器的前端 IP 配置或出站规则可确保简化 Azure 公共 IP 地址空间。 可以通过清理源自某个连续 IP 地址范围的出站连接来简化你的方案。 | 若要将来自某个前缀的 IP 关联到负载均衡器，请执行以下操作： </br> 1.[创建前缀。](manage-public-ip-address-prefix.md) </br> 2.[从前缀创建 IP。](manage-public-ip-address-prefix.md) </br> 3.创建负载均衡器时，请选择或更新在前面第 2 步中创建的 IP 作为负载均衡器的前端 IP。 |
| Azure 防火墙 | 可使用来自前缀的公共 IP 地址作为出站 SNAT。 所有出站虚拟网络流量都会转换为 [Azure 防火墙](../firewall/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)公共 IP。 | 若要将来自某个前缀的 IP 关联到防火墙，请执行以下操作： </br> 1.[创建前缀。](manage-public-ip-address-prefix.md) </br> 2.[从前缀创建 IP。](manage-public-ip-address-prefix.md) </br> 3.[部署 Azure 防火墙](../firewall/tutorial-firewall-deploy-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-the-firewall)时，请务必选择你以前从该前缀提供的 IP。|
| VPN 网关 (AZ SKU) 或应用程序网关 v2 | 可以使用区域冗余 VPN 或应用程序网关 v2 前缀中的公共 IP。 | 若要将来自某个前缀的 IP 关联到网关，请执行以下操作： </br> 1.[创建前缀。](manage-public-ip-address-prefix.md) </br> 2.[从前缀创建 IP。](manage-public-ip-address-prefix.md) </br> 3. 部署 [VPN 网关](../vpn-gateway/tutorial-create-gateway-portal.md)或[应用程序网关](../application-gateway/quick-create-portal.md#create-an-application-gateway)时，请务必选择你以前从该前缀提供的 IP。|

## <a name="constraints"></a>约束

- 不能指定前缀的 IP 地址。 Azure 将根据你指定的大小提供前缀的 IP 地址。
- 默认情况下，可以创建最多 16 个 IP 地址的前缀或默认前缀 /28。 查看[网络限制增加请求](../azure-portal/supportability/networking-quota-requests.md)和 [Azure 限制](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)以获取详细信息。
- 创建前缀后，无法更改该范围。
- 仅使用标准 SKU 创建的静态公共 IP 地址可从前缀范围进行分配。 若要详细了解公共 IP 地址 SKU，请参阅[公共 IP 地址](./public-ip-addresses.md#public-ip-addresses)。
- 范围中的地址只能分配到 Azure 资源管理器资源。 这些地址不能分配到经典部署模型中的资源。
- 从该前缀创建的所有公共 IP 地址都必须存在于该前缀所在的 Azure 区域和订阅中。 地址必须分配给同一区域和订阅中的资源。
- 如果前缀中的任何地址被分配到与某个资源关联的公共 IP 地址资源，则无法删除该前缀。 首先应取消关联所有公共 IP 地址资源，这些资源通过前缀分配有 IP 地址。


## <a name="next-steps"></a>后续步骤

- [创建](manage-public-ip-address-prefix.md)公共 IP 地址前缀