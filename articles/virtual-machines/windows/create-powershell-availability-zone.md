---
title: 使用 Azure PowerShell 创建分区的 VM
description: 使用 Azure PowerShell 在可用性区域中创建虚拟机
author: cynthn
ms.service: virtual-machines
ms.topic: conceptual
ms.workload: infrastructure
ms.date: 03/27/2018
ms.author: cynthn
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 5e059cd4112b39565ba07b39d80d2db4a53c9d46
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122693470"
---
# <a name="create-a-virtual-machine-in-an-availability-zone-using-azure-powershell"></a>使用 Azure PowerShell 在可用性区域中创建虚拟机

**适用于：** :heavy_check_mark: Windows VM 

本文详细介绍如何使用 Azure PowerShell 在 Azure 可用性区域中创建运行 Windows Server 2016 的 Azure 虚拟机。 [可用性区域](../../availability-zones/az-overview.md)是 Azure 区域中物理上独立的区域。 使用可用性区域可以在整个数据中心发生故障或服务中断（这种情况很少见）时保护应用和数据。

若要使用可用性区域，请在[受支持的 Azure 区域](../../availability-zones/az-region.md)中创建虚拟机。

 

## <a name="sign-in-to-azure"></a>登录 Azure

运行 `Connect-AzAccount` 命令以登录 Azure 订阅，并按照屏幕上的说明操作。

```powershell
Connect-AzAccount
```

## <a name="check-vm-sku-availability"></a>查看 VM SKU 可用性
VM 大小或 SKU 的可用性可能因地区和区域而异。 可以按 Azure 区域列出可用的 VM SKU，以便规划可用性区域的使用。 此功能可确保选择适当的 VM 大小，并跨区域获取所需的复原能力。 有关不同 VM 类型和大小的详细信息，请参阅 [VM 大小概述](../sizes.md)。

可以使用 [Get-AzComputeResourceSku](/powershell/module/az.compute/get-azcomputeresourcesku) 命令来查看可用的 VM SKU。 以下示例列出了 *eastus2* 区域中可用的 VM SKU：

```powershell
Get-AzComputeResourceSku | where {$_.Locations.Contains("eastus2")};
```

输出类似于以下浓缩版示例，其中显示了每种 VM 大小都可用的可用性区域：

```powershell
ResourceType                Name  Location      Zones   [...]
------------                ----  --------      -----
virtualMachines  Standard_DS1_v2   eastus2  {1, 2, 3}
virtualMachines  Standard_DS2_v2   eastus2  {1, 2, 3}
[...]
virtualMachines     Standard_F1s   eastus2  {1, 2, 3}
virtualMachines     Standard_F2s   eastus2  {1, 2, 3}
[...]
virtualMachines  Standard_D2s_v3   eastus2  {1, 2, 3}
virtualMachines  Standard_D4s_v3   eastus2  {1, 2, 3}
[...]
virtualMachines   Standard_E2_v3   eastus2  {1, 2, 3}
virtualMachines   Standard_E4_v3   eastus2  {1, 2, 3}
```


## <a name="create-resource-group"></a>创建资源组

使用 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) 创建 Azure 资源组。 资源组是在其中部署和管理 Azure 资源的逻辑容器。 本示例在 *eastus2* 区域中创建名为 *myResourceGroup* 的资源组。 

```powershell
New-AzResourceGroup -Name myResourceGroup -Location EastUS2
```

## <a name="create-networking-resources"></a>创建网络资源

### <a name="create-a-virtual-network-subnet-and-a-public-ip-address"></a>创建虚拟网络、子网和公共 IP 地址 
这些资源用来与虚拟机建立网络连接，以及连接到 Internet。 在可用性区域（在本示例中为 *2*）中创建 IP 地址。 在后面的步骤中，请在用来创建 IP 地址的区域中创建 VM。

```powershell
# Create a subnet configuration
$subnetConfig = New-AzVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzVirtualNetwork -ResourceGroupName myResourceGroup -Location eastus2 `
    -Name myVNet -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig

# Create a public IP address in an availability zone and specify a DNS name
$pip = New-AzPublicIpAddress -ResourceGroupName myResourceGroup -Location eastus2 -Zone 2 `
    -AllocationMethod Static -IdleTimeoutInMinutes 4 -Name "mypublicdns$(Get-Random)" -Sku Standard
```

### <a name="create-a-network-security-group-and-a-network-security-group-rule"></a>创建网络安全组和网络安全组规则 
网络安全组使用入站和出站规则保护虚拟机。 在本例中，会为端口 3389 创建一个入站规则，该规则允许传入的远程桌面连接。 我们还需要为端口 80 创建入站规则，以允许传入的 Web 流量。

```powershell
# Create an inbound network security group rule for port 3389
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
    -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
    -DestinationPortRange 3389 -Access Allow

# Create an inbound network security group rule for port 80
$nsgRuleWeb = New-AzNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleWWW  -Protocol Tcp `
    -Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
    -DestinationPortRange 80 -Access Allow

# Create a network security group
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName myResourceGroup -Location eastus2 `
    -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP,$nsgRuleWeb
```

### <a name="create-a-network-card-for-the-virtual-machine"></a>为虚拟机创建网卡 
使用 [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) 为虚拟机创建网卡。 网卡将虚拟机连接到子网、网络安全组和公共 IP 地址。

```powershell
# Create a virtual network card and associate with public IP address and NSG
$nic = New-AzNetworkInterface -Name myNic -ResourceGroupName myResourceGroup -Location eastus2 `
    -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id
```

## <a name="create-virtual-machine"></a>创建虚拟机

创建虚拟机配置。 此配置包括部署虚拟机时使用的设置，例如虚拟机映像、大小和身份验证配置。 可用性区域支持本示例中的 *Standard_DS1_v2* 大小。 此配置还指定了创建 IP 地址时所设置的可用性区域。 运行此步骤时，会提示输入凭据。 输入的值将配置为用于虚拟机的用户名和密码。

```powershell
# Define a credential object
$cred = Get-Credential

# Create a virtual machine configuration
$vmConfig = New-AzVMConfig -VMName myVM -VMSize Standard_DS1_v2 -Zone 2 | `
    Set-AzVMOperatingSystem -Windows -ComputerName myVM -Credential $cred | `
    Set-AzVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer `
    -Skus 2016-Datacenter -Version latest | Add-AzVMNetworkInterface -Id $nic.Id
```

使用 [New-AzVM](/powershell/module/az.compute/new-azvm) 创建虚拟机。

```powershell
New-AzVM -ResourceGroupName myResourceGroup -Location eastus2 -VM $vmConfig
```

## <a name="confirm-zone-for-managed-disk"></a>为托管磁盘确认区域

已在与 VM 相同的可用性区域中创建了 VM 的 IP 地址资源。 在相同的可用性区域中创建了 VM 的托管磁盘资源。 可以使用 [Get-AzDisk](/powershell/module/az.compute/get-azdisk) 验证此结果：

```powershell
Get-AzDisk -ResourceGroupName myResourceGroup
```

输出显示，托管磁盘与 VM 位于相同的可用性区域中：

```powershell
ResourceGroupName  : myResourceGroup
AccountType        : PremiumLRS
OwnerId            : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.
                     Compute/virtualMachines/myVM
ManagedBy          : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx//resourceGroups/myResourceGroup/providers/Microsoft.
                     Compute/virtualMachines/myVM
Sku                : Microsoft.Azure.Management.Compute.Models.DiskSku
Zones              : {2}
TimeCreated        : 9/7/2017 6:57:26 PM
OsType             : Windows
CreationData       : Microsoft.Azure.Management.Compute.Models.CreationData
DiskSizeGB         : 127
EncryptionSettings :
ProvisioningState  : Succeeded
Id                 : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.
                     Compute/disks/myVM_OsDisk_1_bd921920bb0a4650becfc2d830000000
Name               : myVM_OsDisk_1_bd921920bb0a4650becfc2d830000000
Type               : Microsoft.Compute/disks
Location           : eastus2
Tags               : {}
```


## <a name="next-steps"></a>后续步骤

本文介绍了如何在可用性区域中创建 VM。 详细了解 Azure VM 的[可用性](../availability.md)。
