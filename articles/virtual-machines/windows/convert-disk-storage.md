---
title: 使用 Azure PowerShell 在不同的磁盘类型之间转换托管磁盘存储
description: 如何使用 Azure PowerShell 在不同磁盘类型之间转换 Azure 托管磁盘。
author: roygara
ms.service: virtual-machines
ms.subservice: disks
ms.topic: how-to
ms.date: 02/13/2021
ms.author: albecker
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 994b24110399057741b9f62f7ecf30a8a505fd6d
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2021
ms.locfileid: "122181765"
---
# <a name="update-the-storage-type-of-a-managed-disk"></a>更新托管磁盘的存储类型

Azure 托管磁盘有四种磁盘类型：Azure 超级磁盘、高级 SSD、标准 SSD 和标准 HDD。 可以根据性能需求在高级 SSD、标准 SSD 和标准 HDD 之间进行切换。 尚无法与超级磁盘进行切换，必须部署一个新磁盘。

非托管磁盘不支持此功能。 但是，可以轻松[将非托管磁盘转换为托管磁盘](convert-unmanaged-to-managed-disks.md)，然后即可切换磁盘类型。



## <a name="before-you-begin"></a>在开始之前

* 由于转换需要重启虚拟机 (VM)，因此请在预先存在的维护时段内计划磁盘存储迁移。
* 对于非托管磁盘，请先[将其转换为托管磁盘](convert-unmanaged-to-managed-disks.md)，以便可以在存储选项之间切换。

## <a name="switch-all-managed-disks-of-a-vm-between-from-one-account-to-another"></a>将 VM 的所有托管磁盘从一个帐户切换到另一个帐户

此示例展示了如何将 VM 的所有磁盘转换为高级存储。 不过，通过在此示例中更改 $storageType 变量，可以将 VM 的磁盘类型转换为标准 SSD 或标准 HDD。 若要使用高级托管磁盘，VM 必须使用支持高级存储的 [VM 大小](../sizes.md)。 此示例还切换到了支持高级存储的大小：

```azurepowershell-interactive
# Name of the resource group that contains the VM
$rgName = 'yourResourceGroup'

# Name of the your virtual machine
$vmName = 'yourVM'

# Choose between Standard_LRS, StandardSSD_LRS and Premium_LRS based on your scenario
$storageType = 'Premium_LRS'

# Premium capable size
# Required only if converting storage from Standard to Premium
$size = 'Standard_DS2_v2'

# Stop and deallocate the VM before changing the size
Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force

$vm = Get-AzVM -Name $vmName -resourceGroupName $rgName

# Change the VM size to a size that supports Premium storage
# Skip this step if converting storage from Premium to Standard
$vm.HardwareProfile.VmSize = $size
Update-AzVM -VM $vm -ResourceGroupName $rgName

# Get all disks in the resource group of the VM
$vmDisks = Get-AzDisk -ResourceGroupName $rgName 

# For disks that belong to the selected VM, convert to Premium storage
foreach ($disk in $vmDisks)
{
    if ($disk.ManagedBy -eq $vm.Id)
    {
        $disk.Sku = [Microsoft.Azure.Management.Compute.Models.DiskSku]::new($storageType)
        $disk | Update-AzDisk
    }
}

Start-AzVM -ResourceGroupName $rgName -Name $vmName
```

## <a name="switch-individual-managed-disks-between-standard-and-premium"></a>在标准类型与高级类型之间切换单个托管磁盘

对于开发/测试工作负荷，可以混合使用标准磁盘和高级磁盘来降低成本。 可以选择仅升级需要更高性能的磁盘。 此示例展示了如何将单个 VM 磁盘从标准存储转换为高级存储。 不过，通过在此示例中更改 $storageType 变量，可以将 VM 的磁盘类型转换为标准 SSD 或标准 HDD。 若要使用高级托管磁盘，VM 必须使用支持高级存储的 [VM 大小](../sizes.md)。 此示例还展示了如何切换到支持高级存储的大小：

```azurepowershell-interactive

$diskName = 'yourDiskName'
# resource group that contains the managed disk
$rgName = 'yourResourceGroupName'
# Choose between Standard_LRS, StandardSSD_LRS and Premium_LRS based on your scenario
$storageType = 'Premium_LRS'
# Premium capable size 
$size = 'Standard_DS2_v2'

$disk = Get-AzDisk -DiskName $diskName -ResourceGroupName $rgName

# Get parent VM resource
$vmResource = Get-AzResource -ResourceId $disk.ManagedBy

# Stop and deallocate the VM before changing the storage type
Stop-AzVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name -Force

$vm = Get-AzVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name 

# Change the VM size to a size that supports Premium storage
# Skip this step if converting storage from Premium to Standard
$vm.HardwareProfile.VmSize = $size
Update-AzVM -VM $vm -ResourceGroupName $rgName

# Update the storage type
$disk.Sku = [Microsoft.Azure.Management.Compute.Models.DiskSku]::new($storageType)
$disk | Update-AzDisk

Start-AzVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name
```

## <a name="switch-managed-disks-from-one-disk-type-to-another"></a>将托管磁盘从一种磁盘类型切换为另一种磁盘类型

执行以下步骤：

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 在“虚拟机”列表中选择 VM。
3. 如果该 VM 未停止，请在 VM“概述”窗格的顶部选择“停止”，然后等待该 VM 停止。 
4. 在 VM 对应的窗格中，从菜单中选择“磁盘”。
5. 选择要转换的磁盘。
6. 从菜单选择“大小 + 性能”。
7. 将“帐户类型”从原始磁盘类型更改为所需的磁盘类型。
8. 选择“保存”并关闭磁盘窗格。

磁盘类型转换会瞬间完成。 可以在转换后启动 VM。

## <a name="next-steps"></a>后续步骤

使用[快照](snapshot-copy-managed-disk.md)创建 VM 的只读副本。
