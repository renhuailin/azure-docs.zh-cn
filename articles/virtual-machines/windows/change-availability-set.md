---
title: 使用 Azure PowerShell 更改 VM 可用性集
description: 了解如何使用 Azure PowerShell 更改虚拟机的可用性集。
ms.service: virtual-machines
author: cynthn
ms.topic: how-to
ms.date: 3/8/2021
ms.author: cynthn
ms.reviewer: mimckitt
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: aaf9502699bbd7108af1d037fa05d82115fb0deb
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122697574"
---
# <a name="change-the-availability-set-for-a-vm-using-azure-powershell"></a>使用 Azure PowerShell 更改 VM 的可用性集    

**适用于：** :heavy_check_mark: Windows VM 


以下步骤说明如何使用 Azure PowerShell 来更改 VM 的可用性集。 只能在创建 VM 时将 VM 添加到可用性集。 若要更改可用性集，必须将虚拟机删除，然后重新创建虚拟机。 

本文同时适用于 Linux VM 和 Windows VM。

本文最后一次使用 [Azure Cloud Shell](https://shell.azure.com/powershell) 和 [Az PowerShell 模块](/powershell/azure/install-az-ps)版本 1.2.0 在 2019 年 2 月 12 日进行了测试。

本示例不检查 VM 是否已连接到负载均衡器。 如果 VM 已连接到负载均衡器，则需要更新脚本以处理这种情况。 完成此过程后，某些扩展可能还需要重新安装。


## <a name="change-the-availability-set"></a>更改可用性集 

以下脚本提供一个示例，该示例收集所需的信息、删除原始 VM，并在新可用性集中重新创建 VM。

```powershell
# Set variables
    $resourceGroup = "myResourceGroup"
    $vmName = "myVM"
    $newAvailSetName = "myAvailabilitySet"

# Get the details of the VM to be moved to the Availability Set
    $originalVM = Get-AzVM `
       -ResourceGroupName $resourceGroup `
       -Name $vmName

# Create new availability set if it does not exist
    $availSet = Get-AzAvailabilitySet `
       -ResourceGroupName $resourceGroup `
       -Name $newAvailSetName `
       -ErrorAction Ignore
    if (-Not $availSet) {
    $availSet = New-AzAvailabilitySet `
       -Location $originalVM.Location `
       -Name $newAvailSetName `
       -ResourceGroupName $resourceGroup `
       -PlatformFaultDomainCount 2 `
       -PlatformUpdateDomainCount 2 `
       -Sku Aligned
    }
    
# Remove the original VM
    Remove-AzVM -ResourceGroupName $resourceGroup -Name $vmName    

# Create the basic configuration for the replacement VM. 
    $newVM = New-AzVMConfig `
       -VMName $originalVM.Name `
       -VMSize $originalVM.HardwareProfile.VmSize `
       -AvailabilitySetId $availSet.Id
 
# For a Linux VM, change the last parameter from -Windows to -Linux 
    Set-AzVMOSDisk `
       -VM $newVM -CreateOption Attach `
       -ManagedDiskId $originalVM.StorageProfile.OsDisk.ManagedDisk.Id `
       -Name $originalVM.StorageProfile.OsDisk.Name `
       -Windows

# Add Data Disks
    foreach ($disk in $originalVM.StorageProfile.DataDisks) { 
    Add-AzVMDataDisk -VM $newVM `
       -Name $disk.Name `
       -ManagedDiskId $disk.ManagedDisk.Id `
       -Caching $disk.Caching `
       -Lun $disk.Lun `
       -DiskSizeInGB $disk.DiskSizeGB `
       -CreateOption Attach
    }
    
# Add NIC(s) and keep the same NIC as primary; keep the Private IP too, if it exists. 
    foreach ($nic in $originalVM.NetworkProfile.NetworkInterfaces) {    
    if ($nic.Primary -eq "True")
    {
            Add-AzVMNetworkInterface `
               -VM $newVM `
               -Id $nic.Id -Primary
               }
           else
               {
                 Add-AzVMNetworkInterface `
                -VM $newVM `
                 -Id $nic.Id 
                }
      }

# Recreate the VM
    New-AzVM `
       -ResourceGroupName $resourceGroup `
       -Location $originalVM.Location `
       -VM $newVM `
       -DisableBginfoExtension
```

## <a name="next-steps"></a>后续步骤

通过添加附加[数据磁盘](attach-managed-disk-portal.md)，向 VM 添加附加存储。
