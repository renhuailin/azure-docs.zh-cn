---
title: 教程：创建 NAT 网关 - PowerShell
titlesuffix: Azure Virtual Network NAT
description: 开始使用 Azure PowerShell 创建 NAT 网关。
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: nat
ms.topic: tutorial
ms.date: 03/09/2021
ms.custom: template-tutorial, devx-track-azurepowershell
ms.openlocfilehash: 8984a6d887afd092c82bc3544a84af32a8245dbc
ms.sourcegitcommit: beff1803eeb28b60482560eee8967122653bc19c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2021
ms.locfileid: "113438755"
---
# <a name="tutorial-create-a-nat-gateway-using-azure-powershell"></a>教程：使用 Azure PowerShell 创建 NAT 网关

本教程介绍如何使用 Azure 虚拟网络 NAT 服务。 你将创建一个 NAT 网关，以便为 Azure 中的虚拟机提供出站连接。 

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 创建虚拟网络。
> * 创建虚拟机。
> * 创建 NAT 网关并将其与虚拟网络关联。
> * 连接到虚拟机并验证 NAT IP 地址。

## <a name="prerequisites"></a>先决条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 本地安装的 Azure PowerShell 或 Azure Cloud Shell

如果选择在本地安装并使用 PowerShell，则本文需要 Azure PowerShell 模块 5.4.1 或更高版本。 运行 `Get-Module -ListAvailable Az` 查找已安装的版本。 如果需要进行升级，请参阅 [Install Azure PowerShell module](/powershell/azure/install-Az-ps)（安装 Azure PowerShell 模块）。 如果在本地运行 PowerShell，则还需运行 `Connect-AzAccount` 以创建与 Azure 的连接。

## <a name="create-a-resource-group"></a>创建资源组

使用 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) 创建资源组。 Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。

以下示例在 **eastus2** 位置创建名为 **myResourceGroupNAT** 的资源组。

```azurepowershell-interactive
$rsg = @{
    Name = 'myResourceGroupNAT'
    Location = 'eastus2'
}
New-AzResourceGroup @rsg
```
## <a name="create-the-nat-gateway"></a>创建 NAT 网关

本部分介绍如何创建 NAT 网关和支持资源。

* 若要访问 Internet，需要提供 NAT 网关的一个或多个公共 IP 地址。 使用 [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) 在 **myResourceGroupNAT** 中创建名为 **myPublicIP** 的公共 IP 地址资源。 

* 使用 [New-AzNatGateway](/powershell/module/az.network/new-aznatgateway) 创建全局 Azure NAT 网关。 此命令的结果就是创建名为“myNATgateway”的网关资源，该资源使用公共 IP 地址“myPublicIP” 。 空闲超时设置为 10 分钟。  

* 使用 [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) 在 **myResourceGroup** 中创建名为 **myVnet** 的虚拟网络，并使用 [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) 创建名为 **mySubnet** 的子网。 虚拟网络的 IP 地址空间为 10.1.0.0/16。 虚拟网络中的子网为 10.1.0.0/24。

* 创建名为“myBastionHost”的 Azure Bastion 主机以访问虚拟机。 使用 [New-AzBastion](/powershell/module/az.network/new-azbastion) 创建堡垒主机。 使用 [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) 为堡垒主机创建公共 IP 地址。

```azurepowershell-interactive
## Create public IP address for NAT gateway ##
$ip = @{
    Name = 'myPublicIP'
    ResourceGroupName = 'myResourceGroupNAT'
    Location = 'eastus2'
    Sku = 'Standard'
    AllocationMethod = 'Static'
}
$publicIP = New-AzPublicIpAddress @ip

## Create NAT gateway resource ##
$nat = @{
    ResourceGroupName = 'myResourceGroupNAT'
    Name = 'myNATgateway'
    IdleTimeoutInMinutes = '10'
    Sku = 'Standard'
    Location = 'eastus2'
    PublicIpAddress = $publicIP
}
$natGateway = New-AzNatGateway @nat

## Create subnet config and associate NAT gateway to subnet##
$subnet = @{
    Name = 'mySubnet'
    AddressPrefix = '10.1.0.0/24'
    NatGateway = $natGateway
}
$subnetConfig = New-AzVirtualNetworkSubnetConfig @subnet 

## Create Azure Bastion subnet. ##
$bastsubnet = @{
    Name = 'AzureBastionSubnet' 
    AddressPrefix = '10.1.1.0/24'
}
$bastsubnetConfig = New-AzVirtualNetworkSubnetConfig @bastsubnet

## Create the virtual network ##
$net = @{
    Name = 'myVNet'
    ResourceGroupName = 'myResourceGroupNAT'
    Location = 'eastus2'
    AddressPrefix = '10.1.0.0/16'
    Subnet = $subnetConfig,$bastsubnetConfig
}
$vnet = New-AzVirtualNetwork @net

## Create public IP address for bastion host. ##
$ip = @{
    Name = 'myBastionIP'
    ResourceGroupName = 'myResourceGroupNAT'
    Location = 'eastus2'
    Sku = 'Standard'
    AllocationMethod = 'Static'
}
$publicip = New-AzPublicIpAddress @ip

## Create bastion host ##
$bastion = @{
    ResourceGroupName = 'myResourceGroupNAT'
    Name = 'myBastion'
    PublicIpAddress = $publicip
    VirtualNetwork = $vnet
}
New-AzBastion @bastion -AsJob

```

## <a name="virtual-machine"></a>虚拟机

在本部分中，你将创建虚拟机来测试 NAT 网关并验证出站连接的公共 IP 地址。

* 使用 [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) 创建网络接口。

* 使用 [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential) 设置 VM 的管理员用户名和密码。

* 使用以下内容创建虚拟机：
    * [New-AzVM](/powershell/module/az.compute/new-azvm)
    * [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig)
    * [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem)
    * [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage)
    * [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface)
    
```azurepowershell-interactive
# Set the administrator and password for the VMs. ##
$cred = Get-Credential

## Place the virtual network into a variable. ##
$vnet = Get-AzVirtualNetwork -Name 'myVNet' -ResourceGroupName 'myResourceGroupNAT'

## Create network interface for virtual machine. ##
$nic = @{
    Name = "myNicVM"
    ResourceGroupName = 'myResourceGroupNAT'
    Location = 'eastus2'
    Subnet = $vnet.Subnets[0]
}
$nicVM = New-AzNetworkInterface @nic

## Create a virtual machine configuration for VMs ##
$vmsz = @{
    VMName = "myVM"
    VMSize = 'Standard_DS1_v2'  
}
$vmos = @{
    ComputerName = "myVM"
    Credential = $cred
}
$vmimage = @{
    PublisherName = 'MicrosoftWindowsServer'
    Offer = 'WindowsServer'
    Skus = '2019-Datacenter'
    Version = 'latest'    
}
$vmConfig = New-AzVMConfig @vmsz `
    | Set-AzVMOperatingSystem @vmos -Windows `
    | Set-AzVMSourceImage @vmimage `
    | Add-AzVMNetworkInterface -Id $nicVM.Id

## Create the virtual machine for VMs ##
$vm = @{
    ResourceGroupName = 'myResourceGroupNAT'
    Location = 'eastus2'
    VM = $vmConfig
}
New-AzVM @vm

```

请先等待虚拟机创建完成，然后再转到下一部分。

## <a name="test-nat-gateway"></a>测试 NAT 网关

在本部分中，我们将测试 NAT 网关。 首先，我们将发现 NAT 网关的公共 IP。 然后，我们将连接到测试虚拟机，并通过 NAT 网关验证出站连接。
    
1. 登录到 [Azure 门户](https://portal.azure.com)

1. 在“概述”屏幕上找到 NAT 网关的公共 IP 地址。 在左侧菜单中选择“所有服务”，选择“所有资源”，然后选择“myPublicIP”。

2. 记下公共 IP 地址：

    :::image type="content" source="./media/tutorial-create-nat-gateway-portal/find-public-ip.png" alt-text="发现 NAT 网关的公共 IP 地址" border="true":::

3. 在左侧菜单中选择“所有服务”，选择“所有资源”，然后从资源列表中选择位于“myResourceGroupNAT”资源组中的“myVM”   。

4. 在“概述”页上，选择“连接”，然后选择“Bastion”  。

5. 选择蓝色的“使用堡垒”按钮。

6. 输入在 VM 创建过程中输入的用户名和密码。

7. 在 myTestVM 中打开 Internet Explorer 。

8. 在地址栏中输入“https://whatsmyip.com”。

9. 验证显示的 IP 地址与你在上一步中记下的 NAT 网关地址是否匹配：

    :::image type="content" source="./media/tutorial-create-nat-gateway-portal/my-ip.png" alt-text="显示外部出站 IP 的 Internet Explorer" border="true":::

## <a name="clean-up-resources"></a>清理资源

如果你不打算继续使用此应用程序，请按以下步骤删除虚拟网络、虚拟机和 NAT 网关：

```azurepowershell-interactive
Remove-AzResourceGroup -Name 'myResourceGroupNAT' -Force
```

## <a name="next-steps"></a>后续步骤

有关 Azure 虚拟网络 NAT 的详细信息，请参阅：
> [!div class="nextstepaction"]
> [虚拟网络 NAT 概述](nat-overview.md)
