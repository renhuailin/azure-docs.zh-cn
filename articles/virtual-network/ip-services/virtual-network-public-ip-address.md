---
title: 创建、更改或删除 Azure 公共 IP 地址
titleSuffix: Azure Virtual Network
description: 管理公共 IP 地址。 了解公共 IP 地址如何成为具有可配置设置的资源。
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/20/2021
ms.author: allensu
ms.openlocfilehash: 116a36875c63e515725c90edc3d6fe2a86c9374c
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129367557"
---
# <a name="create-change-or-delete-an-azure-public-ip-address"></a>创建、更改或删除 Azure 公共 IP 地址

了解公共 IP 地址，以及如何创建、更改和删除此类地址。 公共 IP 地址是一个具有可配置设置的资源。 将公共 IP 地址分配给支持公共 IP 地址的 Azure 资源以启用：

- 从 Internet 到资源的入站通信，如 Azure 虚拟机 (VM)、Azure 应用程序网关、Azure 负载均衡器、Azure VPN 网关等。
- 使用可预测的 IP 地址与 Internet 建立出站连接。

[!INCLUDE [ephemeral-ip-note.md](../../../includes/ephemeral-ip-note.md)]

## <a name="create-a-public-ip-address"></a>创建公共 IP 地址

有关如何使用门户、PowerShell、CLI 或资源管理器模板来创建公共 IP 地址的说明，请参阅以下页面：

 * [创建公共 IP 地址 - 门户](../../virtual-network/create-public-ip-portal.md?tabs=option-create-public-ip-standard-zones)
 * [创建公共 IP 地址 - PowerShell](../../virtual-network/create-public-ip-powershell.md?tabs=option-create-public-ip-standard-zones)
 * [创建公共 IP 地址 - Azure CLI](../../virtual-network/create-public-ip-cli.md?tabs=option-create-public-ip-standard-zones)
 * [创建公共 IP 地址 - 模板](../../virtual-network/create-public-ip-template.md)

>[!NOTE]
>虽然门户提供了用于创建两个公共 IP 地址资源（一个 IPv4 和一个 IPv6）的选项，但 PowerShell 和 CLI 命令可使用任一 IP 版本的地址创建一个资源。 如果需要两个公共 IP 地址资源（每个 IP 版本一个），必须运行此命令两次，为公共 IP 地址资源指定不同的名称和 IP 版本。

如需详细了解创建过程中公共 IP 地址的特定属性，请参阅下表：

   |设置|必需？|详细信息|
   |---|---|---|
   |IP 版本|是| 选择 IPv4 或 IPv6 或“两者”。 选择“两者”将导致创建 2 个公共 IP 地址，1 个 IPv4 地址和 1个 IPv6 地址。 详细了解 [Azure VNET 中的 IPv6](ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。|
   |SKU|是|引入 SKU 之前创建的所有公共 IP 地址均为基本 SKU 公共 IP 地址。 创建公共 IP 地址后，无法更改此 SKU。 独立虚拟机、可用性集内的虚拟机或虚拟机规模集可使用基本 SKU 或标准 SKU。 不允许在可用性集或规模集或独立 VM 内的虚拟机之间混用 SKU。 基本 SKU：如果要在支持可用性区域的区域内创建公共 IP 地址，“可用性区域”设置默认设为“无”。 基本公共 IP 不支持可用性区域。 **标准** SKU：标准 SKU 公共 IP 可关联到虚拟机或负载均衡器前端。 如果要在支持可用性区域的区域内创建公共 IP 地址，“可用性区域”设置默认设为“区域冗余”。 有关可用性区域的详细信息，请参阅“可用性区域”设置。 将地址关联到标准负载均衡器时需使用标准 SKU。 若要了解标准 负载均衡器的详细信息，请参阅 [Azure 负载均衡器标准 SKU](../../load-balancer/load-balancer-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。 将标准 SKU 公共 IP 地址分配到虚拟机的网络接口时，必须使用[网络安全组](../../virtual-network/network-security-groups-overview.md#network-security-groups)显式允许预期流量。 创建并关联网络安全组且显式允许所需流量之后，才可与资源通信。|
   |层|是|指示 IP 地址是否与区域（“区域”）关联，或是来自多个区域（“全球”）的“任意播”。 请注意，“全球层”IP 是标准 IP 的预览功能，目前仅用于跨区域负载均衡器。|
   |名称|是|名称在所选资源组中必须唯一。|
   |IP 地址分配|是|**动态：** 将某个公共 IP 地址关联到 Azure 资源并首次启动后，才会分配动态地址。 如果某个资源（例如虚拟机）停止（解除分配）后又重启，则动态地址可能会更改。 如果虚拟机重启或停止，该地址将保持不变。 从资源中删除公共 IP 地址资源时，将释放动态地址。<br> **静态：** 静态地址是在创建公共 IP 地址时分配的。 除非删除公共 IP 地址资源，否则不会释放静态地址。 <br>注意：如果选择 IPv6 作为“IP 版本”，则对于基本 SKU，分配方法必须为“动态”。  对于 IPv4 和 IPv6，标准 SKU 地址均为“静态”。 |
  |路由首选项 |是| 默认情况下，公共 IP 地址的路由首选项设置为“Microsoft 网络”，该网络通过 Microsoft 的全球广域网向用户传送流量。  相比传输 ISP 网络，选择“Internet”可最大限度减少 Microsoft 网络漫游，从而按成本优化费率传送流量。  创建后便无法更改公共 IP 地址路由首选项。  有关路由首选项的详细信息，请参阅[什么是路由首选项（预览）？](routing-preference-overview.md)。   |
   |空闲超时(分钟)|否|在不依赖客户端发送保持连接消息的情况下，TCP 或 HTTP 连接持续打开的分钟数。 如果选择 IPv6 作为“IP 版本”，则此值设置为 4 分钟且不能更改。 |
   |DNS 名称标签|否|必须在创建名称的 Azure 位置中（在所有订阅和所有客户中）保持唯一。 Azure 会在其 DNS 中自动注册该名称和 IP 地址，使你能够连接到使用该名称的资源。 Azure 将默认子网（如 location. cloudappp.azure.com）追加到你提供的名称后面，以创建完全限定的 DNS 名称。 如果选择同时创建这两个地址版本，则会将相同的 DNS 名称分配给 IPv4 和 IPv6 地址。 Azure 的默认 DNS 包含 IPv4 A 和 IPv6 AAAA 名称记录。 默认 DNS 在 DNS 查找期间使用这两条记录进行响应。 客户端选择要与哪个地址（IPv4 或 IPv6）通信。 可使用 Azure DNS 服务来配置带有自定义后缀（可解析为公共 IP 地址）的 DNS 名称。 有关详细信息，请参阅[将 Azure DNS 与 Azure 公共 IP 地址配合使用](../../dns/dns-custom-domain.md?toc=%2fazure%2fvirtual-network%2ftoc.json#public-ip-address)。|
   |名称（仅选择“两者”的 IP 版本时才可见）|是，如果选择“两者”的 IP 版本|该名称必须不同于在此列表中的第一个“名称”中输入的名称。 如果选择同时创建 IPv4 和 IPv6 地址，门户将创建两个单独的公共 IP 地址资源，每个资源中分配有一个 IP 地址版本。|
   |IP 地址分配（仅选择“两者”的 IP 版本时才可见）|是，如果选择“两者”的 IP 版本| 与上面的 IP 地址分配有相同的限制。 |
   |订阅|是|必须与你要将公共 IP 关联到的资源存在于同一[订阅](../../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription)中。|
   |资源组|是|可以与你将公共 IP 关联到的资源存在于同一或不同的[资源组](../../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group)中。|
   |位置|是|必须与你将公共 IP 关联到的资源存在于同一[位置](https://azure.microsoft.com/regions)（也称为区域）中。|
   |可用性区域| 否 | 选择受支持的位置和 IP 地址类型时才会显示此设置。 “基本”SKU 公共 IP 和“全球”层公共 IP 不支持可用性区域。  可以选择无区域（默认选项）、特定区域或区域冗余。 请根据特定的域故障要求做出选择。 有关受支持位置的列表以及有关可用区的更多信息，请参阅[可用区概述](../../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。  

## <a name="view-modify-settings-for-or-delete-a-public-ip-address"></a>查看、修改公共 IP 地址的设置或删除公共 IP 地址

   - **查看/列出**：查看公共 IP 的设置（包括 SKU、地址和任何关联项）。 关联项可以是负载均衡器前端、虚拟机和其他 Azure 资源。
   - **修改**：使用 [创建公共 IP 地址](#create-a-public-ip-address)中的信息来修改设置。 设置，例如空闲超时、DNS 名称标签或分配方法。 有关将公共 IP SKU 从基本升级到标准的完整过程，请参阅[升级 Azure 公共 IP 地址](../../virtual-network/public-ip-upgrade-portal.md)。
   
   >[!WARNING]
   >若要将公共 IP 的分配方法从静态更改为动态，必须先从任何适用的 IP 配置中删除地址（请参阅“删除”部分）。 将分配方法从静态更改为动态时，将丢失分配给公共 IP 地址的 IP 资源。 尽管 Azure 公共 DNS 服务器会保留静态或动态地址与任何 DNS 名称标签（若已定义）之间的映射，但如果虚拟机在处于停止（解除分配）状态之后启动，动态 IP 地址可能更改。 为防止地址变化，请分配静态 IP 地址。
   
|操作|Azure 门户|Azure PowerShell|Azure CLI|
|---|---|---|---|
|查看 | 在公共 IP 的“概述”部分中 |使用 [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) 来检索公共 IP 地址对象并查看其设置| 使用 [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show) 来显示设置|
|列出 | 在“公共 IP 地址”类别下 |使用 [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) 来检索一个或多个公共 IP 地址对象并查看其设置|使用 [az network public-ip list](/cli/azure/network/public-ip#az_network_public_ip_list) 来列出公共 IP 地址|
|修改 | 对于取消关联的 IP，请选择“配置”来实现以下操作： </br> 修改空闲超时。 </br> DNS 名称标签。 </br> 将 IP 的分配方式从静态更改为动态。 </br> 将基本 IP 升级到标准 IP。 |使用 [Set-AzPublicIpAddress](/powershell/module/az.network/set-azpublicipaddress) 来更新设置 |使用 [az network public-ip update](/cli/azure/network/public-ip#az_network_public_ip_update) 进行更新 |

   - **删除**：若要删除公共 IP，需要该公共 IP 对象不与任何 IP 配置或虚拟机 NIC 关联。 有关详细信息，请参阅下表。

|资源|Azure 门户|Azure PowerShell|Azure CLI|
|---|---|---|---|
|[虚拟机](../../virtual-network/remove-public-ip-address-vm.md)|选择“取消关联”，以将该 IP 地址与 NIC 配置取消关联，然后选择“删除” 。|使用 [Set-AzPublicIpAddress](/powershell/module/az.network/set-azpublicipaddress) 来将该 IP 地址与 NIC 配置取消关联；使用 [Remove-AzPublicIpAddress](/powershell/module/az.network/remove-azpublicipaddress) 来删除|使用 [az network public-ip update --remove](/cli/azure/network/public-ip#az_network_public_ip_update) 将该 IP 地址与 NIC 配置取消关联。 使用 [az network public-ip delete](/cli/azure/network/public-ip#az_network_public_ip_delete) 删除公共 IP。 |
|负载均衡器前端 | 导航到未使用的公共 IP 地址并选择“关联”。 选取具有相关前端 IP 配置的负载均衡器来替换 IP。 可以使用与用于虚拟机的同一方法来删除旧 IP。  | 使用 [Set-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/set-azloadbalancerfrontendipconfig) 将新的前端 IP 配置关联到公共负载均衡器。 使用 [Remove-AzPublicIpAddress](/powershell/module/az.network/remove-azpublicipaddress) 删除公共 IP。 也可使用 [Remove-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/remove-azloadbalancerfrontendipconfig) 删除前端 IP 配置（如果有多个）。 | 使用 [az network lb frontend-ip update](/cli/azure/network/lb/frontend-ip#az_network_lb_frontend_ip_update) 将新的前端 IP 配置关联到公共负载均衡器。 使用 [Remove-AzPublicIpAddress](/powershell/module/az.network/remove-azpublicipaddress) 删除公共 IP。 也可使用 [az network lb frontend-ip delete](/cli/azure/network/lb/frontend-ip#az_network_lb_frontend_ip_delete) 删除前端 IP 配置（如果有多个）。 |
|防火墙|空值| 使用[解除分配](../../firewall/firewall-faq.yml#how-can-i-stop-and-start-azure-firewall)来解除分配防火墙并删除所有 IP 配置 | 使用 [az network firewall ip-config delete](/cli/azure/network/firewall/ip-config#az_network_firewall_ip_config_delete) 删除 IP。 先使用 PowerShell 解除分配。 |

## <a name="virtual-machine-scale-sets"></a>虚拟机规模集

在使用具有公共 IP 的虚拟机规模集时，不存在与单个虚拟机实例关联的独立公共 IP 对象。 但是，公共 IP 前缀对象[可用于生成实例 IP](https://azure.microsoft.com/resources/templates/vmms-with-public-ip-prefix/)。

若要列出虚拟机规模集上的公共 IP，可使用 PowerShell ([Get-AzPublicIpAddress -VirtualMachineScaleSetName](/powershell/module/az.network/get-azpublicipaddress)) 或 CLI ([az virtual machine scale set list-instance-public-ips](/cli/azure/vmss#az_vmss_list_instance_public_ips))。

有关详细信息，请参阅 [Azure 虚拟机规模集的网络](../../virtual-machine-scale-sets/virtual-machine-scale-sets-networking.md#public-ipv4-per-virtual-machine)。

## <a name="assign-a-public-ip-address"></a>分配公共 IP 地址

了解如何将公共 IP 地址分配给以下资源：

- [Windows](../../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 或 [Linux](../../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 虚拟机（在创建时）。 将 IP 添加到[现有虚拟机](../../virtual-network/virtual-network-network-interface-addresses.md#add-ip-addresses)。
- [公共负载均衡器](../../load-balancer/quickstart-load-balancer-standard-public-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [应用程序网关](../../application-gateway/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [使用 VPN 网关的站点到站点连接](../../vpn-gateway/tutorial-site-to-site-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [虚拟机规模集](../../virtual-machine-scale-sets/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [NAT 网关](../nat-gateway/tutorial-create-nat-gateway-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Azure Bastion](../../bastion/quickstart-host-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Azure 防火墙](../../firewall/tutorial-firewall-deploy-portal-policy.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [跨区域负载均衡器](../../load-balancer/tutorial-cross-region-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

## <a name="permissions"></a>权限

若要管理公共 IP 地址，必须将你的帐户分配给[网络参与者](../../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)角色。 还支持[自定义](../../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)角色。 必须为自定义角色分配下表中列出的适当操作：

| 操作                                                             | 名称                                                           |
| ---------                                                          | -------------                                                  |
| Microsoft.Network/publicIPAddresses/read                           | 读取公共 IP 地址                                          |
| Microsoft.Network/publicIPAddresses/write                          | 创建或更新公共 IP 地址                           |
| Microsoft.Network/publicIPAddresses/delete                         | 删除公共 IP 地址                                     |
| Microsoft.Network/publicIPAddresses/join/action                    | 将公共 IP 地址关联到资源                    |

## <a name="next-steps"></a>后续步骤

公共 IP 地址会产生少许费用。 若要查看定价，请参阅 [IP 地址定价](https://azure.microsoft.com/pricing/details/ip-addresses)页。

- 使用 [PowerShell](../../virtual-network/powershell-samples.md) 或 [Azure CLI](../../virtual-network/cli-samples.md) 示例脚本或使用 Azure [资源管理器模板](../../virtual-network/template-samples.md)创建公共 IP 地址
- 为公共 IP 地址创建和分配 [Azure Policy 定义](../../virtual-network/policy-reference.md)