---
title: 使用 Azure PowerShell 创建 Bastion 主机 | Microsoft Docs
description: 了解如何使用 PowerShell 创建 Azure Bastion 主机。
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 07/12/2021
ms.author: cherylmc
ms.openlocfilehash: 167c6f7eab4c9b553cf74779d4a4ad11c9f58dfb
ms.sourcegitcommit: 75ad40bab1b3f90bb2ea2a489f8875d4b2da57e4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/12/2021
ms.locfileid: "113640612"
---
# <a name="create-an-azure-bastion-host-using-azure-powershell"></a>使用 Azure PowerShell 创建 Azure Bastion 主机

本文介绍如何使用 PowerShell 创建 Azure Bastion 主机。 在虚拟网络中预配 Azure Bastion 服务后，即可在该虚拟网络中的所有 VM 上获得无缝的 RDP/SSH 体验。 Azure Bastion 部署是按虚拟网络进行的，而不是按订阅/帐户或虚拟机进行的。

或者，可使用以下方法创建 Azure Bastion 主机：
* [Azure 门户](./tutorial-create-host-portal.md)
* [Azure CLI](create-host-cli.md)

[!INCLUDE [Note about SKU limitations for preview.](../../includes/bastion-preview-sku-note.md)]

## <a name="prerequisites"></a>先决条件

确保拥有 Azure 订阅。 如果还没有 Azure 订阅，可以激活 [MSDN 订户权益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details)或注册获取[免费帐户](https://azure.microsoft.com/pricing/free-trial)。

[!INCLUDE [PowerShell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

 > [!NOTE]
 > 目前不支持一起使用 Azure Bastion 和 Azure 专用 DNS 区域。 开始之前，请确保计划在其中部署 Bastion 资源的虚拟网络未链接到专用 DNS 区域。
 >

## <a name="create-a-bastion-host"></a><a name="createhost"></a>创建 Bastion 主机

本部分帮助你使用 Azure PowerShell 创建新的 Azure Bastion 资源。

1. 创建虚拟网络和 Azure Bastion 子网。 必须使用名称值 AzureBastionSubnet 创建 Azure Bastion 子网。 此值告知 Azure 要将 Bastion 资源部署到哪个子网。 这不同于 VPN 网关子网。

   [!INCLUDE [Note about BastionSubnet size.](../../includes/bastion-subnet-size.md)]

   ```azurepowershell-interactive
   $subnetName = "AzureBastionSubnet"
   $subnet = New-AzVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
   $vnet = New-AzVirtualNetwork -Name "myVnet" -ResourceGroupName "myBastionRG" -Location "westeurope" -AddressPrefix 10.0.0.0/16 -Subnet $subnet
   ```

2. 为 Azure Bastion 创建一个公共 IP 地址。 此公共 IP 是将在其上访问 RDP/SSH（通过端口 443）的 Bastion 资源的公共 IP 地址。 公共 IP 地址必须与要创建的 Bastion 资源位于同一区域。

   ```azurepowershell-interactive
   $publicip = New-AzPublicIpAddress -ResourceGroupName "myBastionRG" -name "myPublicIP" -location "westeurope" -AllocationMethod Static -Sku Standard
   ```

3. 在虚拟网络的 AzureBastionSubnet 中创建新的 Azure Bastion 资源。 创建和部署 Bastion 资源大约需要 5 分钟。

   ```azurepowershell-interactive
   $bastion = New-AzBastion -ResourceGroupName "myBastionRG" -Name "myBastion" -PublicIpAddress $publicip -VirtualNetwork $vnet
   ```

## <a name="next-steps"></a>后续步骤

* 有关其他信息，请参阅 [Bastion 常见问题解答](bastion-faq.md)。
* 若要在 Azure Bastion 子网中使用网络安全组，请参阅[使用 NSG](bastion-nsg.md)。
