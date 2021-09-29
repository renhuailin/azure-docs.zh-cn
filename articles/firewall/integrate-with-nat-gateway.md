---
title: 利用 Azure NAT 网关缩放 SNAT 端口
description: 可以将 Azure 防火墙与 NAT 网关集成，以增加 SNAT 端口数。
services: firewall
author: jocortems
ms.service: firewall
ms.topic: how-to
ms.date: 04/23/2021
ms.author: jocorte
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 591f2882e6886f208a5452ac547a5bb1e4024906
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128610154"
---
# <a name="scale-snat-ports-with-azure-nat-gateway"></a>利用 Azure NAT 网关缩放 SNAT 端口

Azure 防火墙为每个已配置的公共 IP 地址提供 2048 个 SNAT 端口，你最多可关联 [250 个公共 IP 地址](./deploy-multi-public-ip-powershell.md)。 根据体系结构和流量模式，在此配置下可能需要超过 512,000 个可用的 SNAT 端口。 例如，将其用于保护与 Microsoft 365 应用集成的大型 [Windows 虚拟桌面部署](./protect-azure-virtual-desktop.md)。

使用大量公共 IP 地址的另一个难题是存在下游 IP 地址筛选要求。 Azure 防火墙会随机选择用于建立连接的源公共 IP 地址，因此需要允许与防火墙关联的所有公共 IP 地址。 即使使用[公共 IP 地址前缀](../virtual-network/public-ip-address-prefix.md)，并需要关联 250 个公共 IP 地址以满足出站 SNAT 端口要求，也仍需要创建并允许 16 个公共 IP 地址前缀。

最好使用 [NAT 网关资源](../virtual-network/nat-gateway/nat-overview.md)来缩放出站 SNAT 端口。 它为每个公共 IP 地址提供 64,000 个 SNAT 端口，并且最多支持 16 个公共 IP 地址，最多可提供 1,024,000 个出站 SNAT 端口。

NAT 网关资源与 Azure 防火墙子网关联时，所有出站 Internet 流量会自动使用 NAT 网关的公共 IP 地址。 无需配置[用户定义的路由](../virtual-network/tutorial-create-route-table-portal.md)。 响应流量使用 Azure 防火墙公共 IP 地址来维护流对称。 如果有多个 IP 地址与 NAT 网关相关联，则会随机选择 IP 地址。 无法指定要使用的地址。

此体系结构没有双重 NAT。 Azure 防火墙实例使用其专用 IP 地址（而不是 Azure 防火墙公共 IP 地址）将流量发送到 NAT 网关。

> [!NOTE]
> 如果已[跨多个可用性区域部署 Azure 防火墙](deploy-availability-zone-powershell.md)，那么，使用 Azure NAT 网关目前将不兼容 Azure 防火墙。 详细了解 [Azure NAT 网关和可用性区域](../virtual-network/nat-gateway/nat-gateway-resource.md#cross-zone-outbound-scenarios-not-supported)。

## <a name="associate-nat-gateway-with-azure-firewall-subnet---azure-powershell"></a>将 NAT 网关与 Azure 防火墙子网关联 - Azure PowerShell

以下示例使用 Azure PowerShell 创建 NAT 网关并将其附加到 Azure 防火墙子网。

```azurepowershell-interactive
# Create public IP addresses
New-AzPublicIpAddress -Name public-ip-1 -ResourceGroupName nat-rg -Sku Standard -AllocationMethod Static -Location 'South Central US'
New-AzPublicIpAddress -Name public-ip-2 -ResourceGroupName nat-rg -Sku Standard -AllocationMethod Static -Location 'South Central US'

# Create NAT gateway
$PublicIPAddress1 = Get-AzPublicIpAddress -Name public-ip-1 -ResourceGroupName nat-rg
$PublicIPAddress2 = Get-AzPublicIpAddress -Name public-ip-2 -ResourceGroupName nat-rg
New-AzNatGateway -Name firewall-nat -ResourceGroupName nat-rg -PublicIpAddress $PublicIPAddress1,$PublicIPAddress2 -Location 'South Central US' -Sku Standard

# Associate NAT gateway to subnet
$virtualNetwork = Get-AzVirtualNetwork -Name nat-vnet -ResourceGroupName nat-rg
$natGateway = Get-AzNatGateway -Name firewall-nat -ResourceGroupName nat-rg
$firewallSubnet = $virtualNetwork.subnets | Where-Object -Property Name -eq AzureFirewallSubnet
$firewallSubnet.NatGateway = $natGateway
$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="associate-nat-gateway-with-azure-firewall-subnet---azure-cli"></a>将 NAT 网关与 Azure 防火墙子网关联 - Azure CLI

以下示例使用 Azure CLI 创建 NAT 网关并将其附加到 Azure 防火墙子网。

```azurecli-interactive
# Create public IP addresses
az network public-ip create --name public-ip-1 --resource-group nat-rg --sku standard
az network public-ip create --name public-ip-2 --resource-group nat-rg --sku standard

# Create NAT gateway
az network nat gateway create --name firewall-nat --resource-group nat-rg --public-ip-addresses public-ip-1 public-ip-2

# Associate NAT gateway to subnet
az network vnet subnet update --name AzureFirewallSubnet --vnet-name nat-vnet --resource-group nat-rg --nat-gateway firewall-nat
```

## <a name="next-steps"></a>后续步骤

- [使用 NAT 网关资源设计虚拟网络](../virtual-network/nat-gateway/nat-gateway-resource.md)
