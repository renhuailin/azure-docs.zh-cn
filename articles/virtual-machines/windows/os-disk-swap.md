---
title: 使用 PowerShell 更换 Azure VM 的 OS 磁盘
description: 使用 PowerShell 更改 Azure 虚拟机使用的操作系统磁盘。
author: cynthn
ms.service: virtual-machines
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 04/24/2018
ms.author: cynthn
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 2892ae3ff10110c2e45d29edd009a9116da0b166
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122687504"
---
# <a name="change-the-os-disk-used-by-an-azure-vm-using-powershell"></a>使用 PowerShell 更改 Azure VM 使用的 OS 磁盘

适用于：:heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 灵活规模集 

如果当前具备 VM，但希望将磁盘更换为备份磁盘或其他 OS 磁盘，可使用 Azure PowerShell 更换 OS 磁盘。 无需删除和重新创建 VM。 甚至可在另一资源组中使用托管磁盘，只要该磁盘尚未使用。

 

需要停止/取消分配 VM，然后才可将该托管磁盘的资源 ID 替换为其他托管磁盘的资源 ID。

请确保 VM 大小和存储类型与要附加的磁盘兼容。 例如，如果要使用的磁盘位于高级存储中，则 VM 需要能使用高级存储（如 DS 系列大小）。 这两个磁盘的大小也必须相同。
此外，不支持将未加密的 VM 与加密的 OS 磁盘混合使用。 如果 VM 不使用 Azure 磁盘加密，则要交换的 OS 磁盘不应使用 Azure 磁盘加密。 如果磁盘使用磁盘加密集，则这两个磁盘应属于同一个磁盘加密集。

使用 [Get-AzDisk](/powershell/module/az.compute/get-azdisk) 获取资源组中的磁盘列表

```azurepowershell-interactive
Get-AzDisk -ResourceGroupName myResourceGroup | Format-Table -Property Name
```
 
如果知道要使用的磁盘的名称，请将其设置为该 VM 的 OS 磁盘。 此示例停止/取消分配名为 myVM 的 VM，并将名为 newDisk 的磁盘分配为新的 OS 磁盘 。 
 
```azurepowershell-interactive 
# Get the VM 
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM 

# Make sure the VM is stopped\deallocated
Stop-AzVM -ResourceGroupName myResourceGroup -Name $vm.Name -Force

# Get the new disk that you want to swap in
$disk = Get-AzDisk -ResourceGroupName myResourceGroup -Name newDisk

# Set the VM configuration to point to the new disk  
Set-AzVMOSDisk -VM $vm -ManagedDiskId $disk.Id -Name $disk.Name 

# Update the VM with the new OS disk
Update-AzVM -ResourceGroupName myResourceGroup -VM $vm 

# Start the VM
Start-AzVM -Name $vm.Name -ResourceGroupName myResourceGroup

```

**后续步骤**

要创建磁盘副本，请参阅[拍摄磁盘快照](snapshot-copy-managed-disk.md)。
