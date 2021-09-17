---
title: Azure 中的虚拟网络和虚拟机
description: 了解网络，因其与 Azure 中的虚拟机存在关联。
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.topic: conceptual
ms.date: 08/17/2021
ms.author: allensu
ms.openlocfilehash: 27c4340071e80cdcac806b80c28873e0955cd586
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2021
ms.locfileid: "122444706"
---
# <a name="virtual-networks-and-virtual-machines-in-azure"></a>Azure 中的虚拟网络和虚拟机

创建虚拟机 (VM) 时，需要创建[虚拟网络](../virtual-network/virtual-networks-overview.md)或使用现有虚拟网络。 确定如何在虚拟网络上访问虚拟机。 一定要[在创建资源之前做好规划](../virtual-network/virtual-network-vnet-plan-design-arm.md)，确保了解[网络资源的限制](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits)。

在下图中，虚拟机表示为 Web 服务器和应用程序服务器。 每一组虚拟机都将分配到虚拟网络的单独子网中。

:::image type="content" source="./media/network-overview/load-balancer.png" alt-text="多层多子网应用程序的示意图。" border="true":::

可以在创建虚拟机前创建虚拟网络，也可以在创建虚拟机的过程中创建虚拟网络。 

需要创建以下资源来支持与虚拟机进行通信：

- 网络接口
- IP 地址
- 虚拟网络和子网

此外，请考虑以下可选资源：

- 网络安全组
- 负载均衡器

## <a name="network-interfaces"></a>网络接口

[网络接口 (NIC)](../virtual-network/virtual-network-network-interface.md) 是虚拟机与虚拟网络之间互相连接的桥梁。 虚拟机中至少具有一个 NIC。 虚拟机可以具有多个 NIC，具体取决于所创建 VM 的大小。 若要了解每个虚拟机大小支持的 NIC 数，请参阅 [VM 大小](../virtual-machines/sizes.md)。

可以创建具有多个 NIC 的 VM，并可在 VM 的整个生命周期中添加或删除 NIC。 多个 NIC 允许 VM 连接到不同的子网。

附加到 VM 的每个 NIC 必须在与 VM 相同的位置和订阅中。 每个 NIC 必须连接到与 NIC 位于相同 Azure 位置和订阅中的 VNet。 创建 VM 后，可以更改其连接到的子网。 不得更改虚拟网络。 附加到 VM 的每个 NIC 将分配有一个 MAC 地址，在删除 VM 之前，该地址不会变化。

下表列出了可用于创建网络接口的方法。

| 方法 | 说明 |
| ------ | ----------- |
| [Azure 门户](https://portal.azure.com) | 在 Azure 门户中创建 VM 时，系统会自动创建网络接口。 门户将创建只包含一个 NIC 的 VM。 如果想要创建包含多个 NIC 的 VM，必须使用其他方法创建 VM。 |
| [Azure PowerShell](../virtual-machines/windows/multiple-nics.md) | 配合使用 [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) 与 `-PublicIpAddressId` 参数，以提供事先创建的公共 IP 地址的标识符。 |
| [Azure CLI](../virtual-machines/linux/multiple-nics.md) | 若要提供事先创建的公共 IP 地址的标识符，请配合使用 [az network nic create ](/cli/azure/network/nic)与 `--public-ip-address` 参数。 |
| [模板](../virtual-network/template-samples.md) | 如需了解使用模板部署网络接口的相关信息，请参阅[虚拟网络中使用公共 IP 地址的网络接口](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/nic-publicip-dns-vnet)。 |

## <a name="ip-addresses"></a>IP 地址

可以为 Azure 中的 NIC 分配以下类型的 [IP 地址](../virtual-network/public-ip-addresses.md)：

- 公共 IP 地址：用来与 Internet 及未连接到虚拟网络的其他 Azure 资源建立入站和出站通信（不提供网络地址转换 (NAT)）。 向 NIC 分配公共 IP 地址是可选操作。 公共 IP 地址会产生少许费用，并且每个订阅可使用的最大公共 IP 地址数目有限制。
- 专用 IP 地址：用于在虚拟网络、本地网络和 Internet 中建立通信（提供 NAT）。 必须至少为 VM 分配一个专用 IP 地址。 若要详细了解 Azure 中的 NAT，请阅读[了解 Azure 中的出站连接](../load-balancer/load-balancer-outbound-connections.md)。

可以为以下项目分配公共 IP 地址：

* 虚拟机
* 公共负载均衡器

可以为以下项目分配专用 IP 地址：

* 虚拟机
* 内部负载均衡器

可以使用网络接口将 IP 地址分配给 VM。

有两种方法可用来给资源指定 IP 地址——动态或静态。 Azure 默认以动态方法提供 IP 地址。 IP 地址不会在其创建时提供， 而是在创建 VM 或启动已停止的 VM 时提供。 停止或删除该 VM 时，会释放该 IP 地址。

要确保 VM 的 IP 地址保持不变，可将分配方法显式设置为静态。 在这种情况下，IP 地址是即时分配的。 只有在删除 VM 或将其分配方法更改为动态时，才会释放该地址。

下表列出了可用于创建 IP 地址的方法。

| 方法 | 说明 |
| ------ | ----------- |
| [Azure 门户](../virtual-network/virtual-network-deploy-static-pip-arm-portal.md) | 默认情况下，公共 IP 地址是动态的。 关闭或删除 VM 时，IP 地址可能会发生变化。 为确保 VM 始终使用同一个公共 IP 地址，需创建静态公共 IP 地址。 默认情况下，门户会在创建 VM 时向 NIC 分配一个动态专用 IP 地址。 创建 VM 后，可将此 IP 地址更改为静态地址。|
| [Azure PowerShell](../virtual-network/virtual-network-deploy-static-pip-arm-ps.md) | 将 [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) 与值为 Dynamic 或 Static 的 `-AllocationMethod` 参数配合使用。 |
| [Azure CLI](../virtual-network/virtual-network-deploy-static-pip-arm-cli.md) | 将 [az network public-ip create](/cli/azure/network/public-ip) 与值为 Dynamic 或 Static 的 `--allocation-method` 参数配合使用。 |
| [模板](../virtual-network/template-samples.md) | 如需了解使用模板部署公共 IP 地址的更多信息，请参阅[虚拟网络中使用公共 IP 地址的网络接口](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/nic-publicip-dns-vnet)。 |

创建公共 IP 地址后，可将它分配到 NIC，从而将它与 VM 相关联。

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]

## <a name="virtual-network-and-subnets"></a>虚拟网络和子网

子网是虚拟网络中某个范围内的 IP 地址。 出于组织安全性考虑，可将一个虚拟网络划分为多个子网。 VM 中的每个 NIC 都会连接到一个虚拟网络中的某个子网。 连接到虚拟网络中的子网（不管是相同的子网还是不同的子网）的 NIC 可以互相通信，无需进行任何额外配置。

设置虚拟网络时，需指定拓扑，包括可用的地址空间和子网。 如果虚拟网络已连接到其他虚拟网络或本地网络，请选择不重叠的地址范围。 IP 地址是专用地址，无法从 Internet 访问。 Azure 将任何地址范围视为专用虚拟网络 IP 地址空间的一部分。 只能在虚拟网络内部、互连的虚拟网络内部以及从本地位置访问地址范围。

如果内部网络由组织中的其他某人负责，则应在选择地址空间前，先咨询该负责人。 确保地址空间中不存在重叠问题。 告诉他们要使用的空间，以免其尝试使用相同的 IP 地址范围。

默认情况下，子网之间不设安全边界。 每个子网中的虚拟机都可以进行通信。 如果部署需要设置安全边界，可以使用网络安全组 (NSG) 来控制流入或流出子网及 VM 的流量。

下表列出了可用于创建虚拟网络和子网的方法。

| 方法 | 说明 |
| ------ | ----------- |
| [Azure 门户](../virtual-network/quick-create-portal.md) | 若要在创建 VM 时让 Azure 创建虚拟网络，则虚拟网络的名称应由包含虚拟网络的资源组名称和 `-vnet` 组合而成。 地址空间为 10.0.0.0/24，所需的子网名称为 **default**，子网地址范围为 10.0.0.0/24。 |
| [Azure PowerShell](../virtual-network/quick-create-powershell.md) | 可以使用 [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworkSubnetConfig) 和 [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) 来创建子网和虚拟网络。 还可以使用 [Add-AzVirtualNetworkSubnetConfig](/powershell/module/Az.Network/Add-AzVirtualNetworkSubnetConfig) 向现有虚拟网络中添加子网。 |
| [Azure CLI](../virtual-network/quick-create-cli.md) | 同时创建子网和虚拟网络。 为 [az network vnet create](/cli/azure/network/vnet) 提供 `--subnet-name` 参数并指定子网名称。 |
| [模板](../virtual-network/template-samples.md) | 有关使用模板创建虚拟网络和子网的详细信息，请参阅[包含两个子网的虚拟网络](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/vnet-two-subnets)。 |

## <a name="network-security-groups"></a>网络安全组

[网络安全组 (NSG)](../virtual-network/virtual-network-vnet-plan-design-arm.md) 包含一系列访问控制列表 (ACL) 规则，这些规则可以允许或拒绝流向子网和/或 NIC 的网络流量。 NSG 可与子网或者已连接到子网的各个 NIC 相关联。 当 NSG 与某个子网相关联时，ACL 规则将应用到该子网中的所有 VM。 可以通过将 NSG 直接关联到 NIC 来限制流向单个 NIC 的流量。

NSG 包含两组规则：入站规则和出站规则。 在每组中，规则的优先级必须保持唯一。 

每个规则都具有以下属性：

* 协议
* 源端口和目标端口范围
* 地址前缀
* 流量方向
* 优先度
* 访问类型

所有 NSG 都包含一组默认规则。 不能删除默认规则。 这些规则的分配优先级最低，无法被你创建的规则覆写。

将 NSG 关联到 NIC 时，NSG 中的网络访问规则只会应用到该 NIC。 若在包含多个 NIC 的 VM 中将 NSG 应用到单个 NIC，则流向其他 NIC 的流量不会受到影响。 可将不同的 NSG 关联到 NIC（或 VM，具体取决于部署模型）以及 NIC 或 VM 绑定到的子网。 优先级是根据流量方向指定的。

在制定虚拟机和虚拟网络创建计划时，请确保[规划好](../virtual-network/virtual-network-vnet-plan-design-arm.md) NSG。

下表列出了可用于创建网络安全组的方法。

| 方法 | 说明 |
| ------ | ----------- |
| [Azure 门户](../virtual-network/tutorial-filter-network-traffic.md) | 在 Azure 门户中创建 VM 时，会自动创建 NSG 并将其关联到门户创建的 NIC。 NSG 的名称由 VM 名称和 `-nsg` 组合而成。 </br> 此 NSG 包含一个入站规则： </br> 优先级为 1000。 </br> 服务设置为 RDP。 </br> 协议设置为 TCP。 </br> 端口设置为 3389。 </br> 操作设置为“允许”。 </br> 若想要允许任何其他流量流入 VM，请另外创建一个或一些规则。 |
| [Azure PowerShell](../virtual-network/tutorial-filter-network-traffic.md) | 使用 [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) 并提供所需的规则信息。 使用 [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) 创建 NSG。 使用 [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) 为子网配置 NSG。 使用 [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) 向虚拟网络中添加 NSG。 |
| [Azure CLI](../virtual-network/tutorial-filter-network-traffic-cli.md) | 首先使用 [az network nsg create](/cli/azure/network/nsg) 创建 NSG。 使用 [az network nsg rule create](/cli/azure/network/nsg/rule) 向 NSG 添加规则。 使用 [az network vnet subnet update](/cli/azure/network/vnet/subnet) 将 NSG 添加到子网。 |
| [模板](../virtual-network/template-samples.md) | 参考[创建网络安全组](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/security-group-create)，使用模板部署网络安全组。 |

## <a name="load-balancers"></a>负载均衡器

[Azure 负载均衡器](../load-balancer/load-balancer-overview.md)可提高应用程序的可用性和网络性能。 可以配置负载均衡器，[对传入 VM 的 Internet 流量进行均衡](../load-balancer/components.md#frontend-ip-configurations)，或者[对 VNet 中 VM 之间的流量进行均衡](../load-balancer/components.md#frontend-ip-configurations)。 负载均衡器还可以均衡跨界网络中本地计算机与 VM 之间的流量，或者将外部流量转发到特定的 VM。

负载均衡器会将流入和流出流量映射到：  

* 负载均衡器上的公共 IP 地址和端口。

* VM 的专用 IP 地址和端口。

创建负载均衡器时，还必须考虑以下配置要素：

- **前端 IP 配置** - 单个负载均衡器可包含一个或多个前端 IP 地址。 这些 IP 地址充当流量的入口。
- **后端地址池** – 与负载分配到的 NIC 关联的 IP 地址。
- [端口转发](../load-balancer/tutorial-load-balancer-port-forwarding-portal.md) - 使用入站 NAT 规则定义入站流量如何流经前端 IP 并分配到后端 IP。
- **负载均衡器规则** – 将给定的前端 IP 和端口组合映射到一组后端 IP 地址和端口组合。 单个负载均衡器可具有多个负载均衡规则。 每个规则都包含前端 IP 和端口，以及与 VM 关联的后端 IP 和端口。
- **[探测](../load-balancer/load-balancer-custom-probe-overview.md)** - 监视 VM 的运行状况。 当探测无法响应时，负载均衡器会停止向状况不良的 VM 发送新连接。 现有连接不受影响，新连接将发送至运行状况良好的 VM。
- **[出站规则](../load-balancer/load-balancer-outbound-connections.md#outboundrules)** - 出站规则为标准负载均衡器的后端池识别的、要转换为前端的所有虚拟机或实例配置出站网络地址转换 (NAT)。

下表列出了可用于创建面向 Internet 的负载均衡器的方法。

| 方法 | 说明 |
| ------ | ----------- |
| Azure 门户 |  可以[通过 Azure 门户对从 Internet 到 VM 的流量进行负载均衡](../load-balancer/quickstart-load-balancer-standard-public-portal.md)。 |
| [Azure PowerShell](../load-balancer/quickstart-load-balancer-standard-public-powershell.md) | 若要提供事先创建的公共 IP 地址的标识符，请配合使用 [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig) 与 `-PublicIpAddress` 参数。 使用 [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) 创建后端地址池的配置。 使用 [New-AzLoadBalancerInboundNatRuleConfig](/powershell/module/az.network/new-azloadbalancerinboundnatruleconfig) 创建与你创建的前端 IP 配置关联的入站 NAT 规则。 使用 [New-AzLoadBalancerProbeConfig](/powershell/module/az.network/new-azloadbalancerprobeconfig) 创建所需的探测。 使用 [New-AzLoadBalancerRuleConfig](/powershell/module/az.network/new-azloadbalancerruleconfig) 创建负载均衡器配置。 使用 [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer) 创建负载均衡器。|
| [Azure CLI](../load-balancer/quickstart-load-balancer-standard-public-cli.md) | 使用 [az network lb create](/cli/azure/network/lb) 创建初始负载均衡器配置。 使用 [az network lb frontend-ip create](/cli/azure/network/lb/frontend-ip) 添加事先创建的公共 IP 地址。 使用 [az network lb address-pool create](/cli/azure/network/lb/address-pool) 添加后端地址池的配置。 使用 [az network lb inbound-nat-rule create](/cli/azure/network/lb/inbound-nat-rule) 添加 NAT 规则。 使用 [az network lb rule create](/cli/azure/network/lb/rule) 添加负载均衡器规则。 使用 [az network lb probe create](/cli/azure/network/lb/probe) 添加探测。 |
| [模板](../load-balancer/quickstart-load-balancer-standard-public-template.md) | 参考[负载均衡器中的 3 个 VM](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/load-balancer-standard-create)，使用模板部署负载均衡器。 |

下表列出了可用于创建内部负载均衡器的方法。

| 方法 | 说明 |
| ------ | ----------- |
| Azure 门户 | 可以[在 Azure 门户中通过负载均衡器对内部流量负载进行均衡](../load-balancer/quickstart-load-balancer-standard-internal-portal.md)。 |
| [Azure PowerShell](../load-balancer/quickstart-load-balancer-standard-internal-powershell.md) | 若要提供网络子网中的专用 IP 地址，请配合使用 [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig) 与 `-PrivateIpAddress` 参数。 使用 [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) 创建后端地址池的配置。 使用 [New-AzLoadBalancerInboundNatRuleConfig](/powershell/module/az.network/new-azloadbalancerinboundnatruleconfig) 创建与你创建的前端 IP 配置关联的入站 NAT 规则。 使用 [New-AzLoadBalancerProbeConfig](/powershell/module/az.network/new-azloadbalancerprobeconfig) 创建所需的探测。 使用 [New-AzLoadBalancerRuleConfig](/powershell/module/az.network/new-azloadbalancerruleconfig) 创建负载均衡器配置。 使用 [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer) 创建负载均衡器。|
| [Azure CLI](../load-balancer/quickstart-load-balancer-standard-internal-cli.md) | 使用 [az network lb create](/cli/azure/network/lb) 命令创建初始负载均衡器配置。 若要定义专用 IP 地址，请配合使用 [az network lb frontend-ip create](/cli/azure/network/lb/frontend-ip) 与 `--private-ip-address` 参数。 使用 [az network lb address-pool create](/cli/azure/network/lb/address-pool) 添加后端地址池的配置。 使用 [az network lb inbound-nat-rule create](/cli/azure/network/lb/inbound-nat-rule) 添加 NAT 规则。 使用 [az network lb rule create](/cli/azure/network/lb/rule) 添加负载均衡器规则。 使用 [az network lb probe create](/cli/azure/network/lb/probe) 添加探测。|
| [模板](../load-balancer/quickstart-load-balancer-standard-internal-template.md) | 参考[负载均衡器中的 2 个 VM](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/2-vms-internal-load-balancer)，使用模板部署负载均衡器。 |

## <a name="virtual-machine-scale-sets"></a>虚拟机规模集

有关负载均衡器和虚拟机规模集的详细信息，请参阅 [Azure 虚拟机规模集的网络](../virtual-machine-scale-sets/virtual-machine-scale-sets-networking.md)。

## <a name="virtual-machines"></a>虚拟机

虚拟机可以在同一虚拟网络中创建，并且可以使用专用 IP 地址相互连接。 可以连接位于不同子网中的虚拟机， 而且无需配置网关或使用公共 IP 地址。 若要在虚拟网络中部署 VM，请先创建虚拟网络。 创建每个 VM 时，应将其分配给虚拟网络和子网。 VM 将在部署或启动期间获取其网络设置。

部署虚拟机时，该虚拟机将获分配 IP 地址。 若将多个 VM 部署到虚拟网络或子网，则 VM 启动时，系统将为其分配 IP 地址。 你还可以为 VM 分配静态 IP。 如果分配静态 IP，应考虑使用特定子网，以避免意外重复使用另一个 VM 的静态 IP。

如果已经创建 VM，之后又想要将它迁移到虚拟网络，则需进行更为复杂的配置更改。 将 VM 重新部署到虚拟网络。 最简单的重新部署方法是删除该 VM（但不要删除 VM 上附加的任何磁盘），并在虚拟网络中使用原始磁盘重新创建 VM。

下表列出了可用于在 VNet 中创建 VM 的方法。

| 方法 | 说明 |
| ------ | ----------- |
| [Azure 门户](../virtual-machines/windows/quick-create-portal.md) | 使用前面所述的默认网络设置创建包含单个 NIC 的 VM。 若要创建包含多个 NIC 的 VM，必须使用其他方法。 |
| [Azure PowerShell](../virtual-machines/windows/tutorial-manage-vm.md) | 使用 [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface) 将事先创建的 NIC 添加到 VM 配置中。 |
| [Azure CLI](../virtual-machines/linux/create-cli-complete.md) | 创建 VM，并将其连接到虚拟网络、子网和作为单个步骤生成的 NIC。 |
| [模板](../virtual-machines/windows/ps-template.md) | 参考 [Very simple deployment of a Windows VM](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/vm-simple-windows)（非常简单的 Windows VM 部署），使用模板部署 VM。 |

## <a name="virtual-network-nat"></a>虚拟网络 NAT

虚拟网络 NAT（网络地址转换）简化了虚拟网络的仅限出站 Internet 连接。 在子网中配置后，所有出站连接将使用指定的静态公共 IP 地址。 无需使用负载均衡器或将公共 IP 地址直接附加到虚拟机，即可建立出站连接。 NAT 是完全托管式的，且具有很高的复原能力。

可为使用 NAT 的每个子网定义出站连接。 同一虚拟网络中的多个子网可以使用不同的 NAT。 可以通过指定要使用的 NAT 网关资源来配置子网。 任何虚拟机实例中的所有 UDP 和 TCP 出站流都会使用 NAT。
NAT 与标准 SKU 公共 IP 地址资源、公共 IP 前缀资源或两者的组合相兼容。 可以直接使用公共 IP 前缀，或者在多个 NAT 网关资源之间分配前缀的公共 IP 地址。 NAT 会将所有流量梳理到前缀的 IP 地址范围。 部署中的任何 IP 筛选过程都很简单。

NAT 会自动处理子网的所有出站流量，而无需完成任何客户配置。 不需要指定用户定义的路由。 NAT 优先于其他出站方案，可替代子网的默认 Internet 目标。

有关 NAT 网关资源和虚拟网络 NAT 的详细信息，请参阅[什么是 Azure 虚拟网络 NAT？](./nat-gateway/nat-overview.md)

下表列出了可用于创建 NAT 网关资源的方法。

| 方法 | 说明 |
| ------ | ----------- |
| [Azure 门户](./nat-gateway/tutorial-create-nat-gateway-portal.md) | 创建虚拟网络、子网、公共 IP、NAT 网关和虚拟机以测试 NAT 网关资源。 |
| [Azure PowerShell](./nat-gateway/tutorial-create-nat-gateway-powershell.md) | 包括使用 [New-AzNatGateway](/powershell/module/az.network/new-aznatgateway) 来创建 NAT 网关资源。 创建虚拟网络、子网、公共 IP、NAT 网关和虚拟机以测试 NAT 网关资源。 |
| [Azure CLI](./nat-gateway/tutorial-create-nat-gateway-cli.md) | 包括使用 [az network nat gateway create](/cli/azure/network/nat#az_network_nat_gateway_create) 来创建 NAT 网关资源。 创建虚拟网络、子网、公共 IP、NAT 网关和虚拟机以测试 NAT 网关资源。 |
| [模板](./nat-gateway/quickstart-create-nat-gateway-template.md) | 创建虚拟网络、子网、公共 IP 和 NAT 网关资源。 |

## <a name="azure-bastion"></a>Azure Bastion 

部署 Azure Bastion 的目的是提供与虚拟网络中虚拟机的安全管理连接。 使用 Azure Bastion 服务能够通过 RDP 和 SSH 安全无缝地连接到虚拟网络中的 VM。 无需在 VM 上公开公共 IP，Azure Bastion 就能启用连接。 连接是直接通过 Azure 门户建立的，而无需额外安装客户端/代理或软件。 Azure Bastion 支持标准 SKU 公共 IP 地址。

有关 Azure Bastion 的详细信息，请参阅[什么是 Azure Bastion](../bastion/bastion-overview.md)。

下表列出了可用于创建 Azure Bastion 部署的方法。

| 方法 | 说明 |
| ------ | ----------- |
| [Azure 门户](../bastion/quickstart-host-portal.md) | 创建虚拟网络、子网、公共 IP、堡垒主机和虚拟机。 |
| [Azure PowerShell](../bastion/bastion-create-host-powershell.md) | 创建虚拟网络、子网、公共 IP 和堡垒主机。 包括使用 [New-AzBastion](/powershell/module/az.network/new-azbastion) 来创建堡垒主机。 |
| [Azure CLI](../bastion/create-host-cli.md) | 创建虚拟网络、子网、公共 IP 和堡垒主机。 包括使用 [az network bastion create](/cli/azure/network/bastion#az_network_bastion_create) 来创建堡垒主机。 |
| [模板](../virtual-network/template-samples.md) | 有关将 Azure Bastion 主机与示例部署集成的模板部署示例，请参阅[快速入门：使用 ARM 模板创建公共负载均衡器，以便对 VM 进行负载均衡](../load-balancer/quickstart-load-balancer-standard-public-template.md)。 |

## <a name="next-steps"></a>后续步骤
有关如何为 VM 管理 Azure 虚拟网络的 VM 特定步骤，请参阅 [Windows](../virtual-machines/windows/tutorial-virtual-network.md) 或 [Linux](../virtual-machines/linux/tutorial-virtual-network.md) 教程。

还有关于如何对 VM 进行负载均衡以及如何创建适用于 [Windows](../virtual-machines/windows/tutorial-load-balancer.md) 或 [Linux](../virtual-machines/linux/tutorial-load-balancer.md) 的高度可用应用程序的教程。

- 了解如何配置[用户定义的路由和 IP 转发](../virtual-network/virtual-networks-udr-overview.md)。
- 了解如何配置 [VNet 到 VNet 连接](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)。
- 了解如何[排查路由问题](../virtual-network/diagnose-network-routing-problem.md)。
- 了解有关[虚拟机网络带宽](../virtual-network/virtual-machine-network-throughput.md)的详细信息。
