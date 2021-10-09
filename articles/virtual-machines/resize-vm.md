---
title: 调整虚拟机的大小
description: 更改用于 Azure 虚拟机的 VM 大小。
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure
ms.topic: how-to
ms.date: 09/13/2021
ms.author: cynthn
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 80387608b8c8ab7069ed989078472102e3d9afe8
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128699387"
---
# <a name="change-the-size-of-a-virtual-machine"></a>更改虚拟机的大小 

适用于：:heavy_check_mark: Windows VM :heavy_check_mark: 灵活规模集 

本文介绍如何将 VM 切换到不同的 [VM 大小](sizes.md)。

完成创建虚拟机 (VM) 后，可以通过更改 VM 大小来扩大或缩小 VM。 在某些情况下，必须先解除分配 VM。 如果新大小在当前托管 VM 的硬件群集上不可用，则可能会出现这种情况。

如果虚拟机使用高级存储，请确保选择 **s** 版本的大小以获得高级存储支持。 例如，选择 Standard_E4 **s** _v3，而不是 Standard_E4_v3。

## <a name="change-the-vm-size"></a>更改 VM 大小

### <a name="portal"></a>[门户](#tab/portal)

1. 打开 [Azure 门户](https://portal.azure.com)。
1. 在虚拟机所对应的页。
1. 在左侧菜单中，选择“大小”。
1. 从可用大小列表中选取新大小，然后选择“重设大小”。


如果虚拟机当前正在运行，更改其大小会导致其重启。 

如果 VM 仍在运行并且你没有在列表中看到所需的大小，则停止虚拟机可能会显示更多大小。

### <a name="cli"></a>[CLI](#tab/cli)

若要重设 VM 大小，需要安装最新版 [Azure CLI](/cli/azure/install-az-cli2)，并使用 [az login](/cli/azure/reference-index) 登录 Azure 帐户。

1. 使用 [az vm list-vm-resize-options](/cli/azure/vm) 查看托管 VM 的硬件群集上可用的 VM 大小的列表。 以下示例列出资源组 `myResourceGroup` 区域中名为 `myVM` 的 VM 的 VM 大小：
   
    ```azurecli-interactive
    az vm list-vm-resize-options \
    --resource-group myResourceGroup \
    --name myVM --output table
    ```

2. 如果列出了所需的 VM 大小，则使用 [az vm resize](/cli/azure/vm) 调整 VM 大小。 以下示例将名为 `myVM` 的 VM 调整为 `Standard_DS3_v2` 大小：
   
    ```azurecli-interactive
    az vm resize \
    --resource-group myResourceGroup \
    --name myVM \
    --size Standard_DS3_v2
    ```
   
    在此过程中，VM 将重新启动。 重新启动后，现有 OS 和数据磁盘将重新映射。 临时磁盘上的所有内容会丢失。

3. 如果所需的 VM 大小未列出，则需先使用 [az vm deallocate](/cli/azure/vm)解除分配 VM。 然后，可以通过此过程将 VM 的大小调整为区域支持的任何可用大小，再启动该 VM。 以下步骤会将名为 `myResourceGroup` 的资源组中名为 `myVM` 的 VM 解除分配、调整大小并启动：
   
    ```azurecli-interactive
    # Variables will make this easier. Replace the values with your own.
    resourceGroup=myResourceGroup
    vm=myVM
    size=Standard_DS3_v2

    az vm deallocate \
    --resource-group $resourceGroup \
    --name myVM
    az vm resize \
    --resource-group $resourceGroup \
    --name $vm \
    --size $size
    az vm start \
    --resource-group $resourceGroup \
    --name $vm
    ```
   
   > [!WARNING]
   > 解除分配 VM 也会释放分配给该 VM 的所有动态 IP 地址。 OS 和数据磁盘不受影响。

### <a name="powershell"></a>[PowerShell](#tab/powershell)

使用 PowerShell 重设不在可用性集中的 VM 的大小。

设置一些变量。 将值替换为自己的信息。

```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$vmName = "myVM"
```

列出托管 VM 的区域中可用的 VM 大小。 
   
```azurepowershell-interactive
Get-AzVMSize -ResourceGroupName $resourceGroup -VMName $vmName 
```

如果列出了所需大小，运行以下命令即可重设虚拟机大小。 如果未列出所需大小，请转到步骤 3。
   
```azurepowershell-interactive
$vm = Get-AzVM -ResourceGroupName $resourceGroup -VMName $vmName
$vm.HardwareProfile.VmSize = "<newVMsize>"
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
```

如果未列出所需大小，运行以下命令即可解除分配虚拟机、重设其大小，并重新启动虚拟机。 将 **\<newVMsize>** 替换为所需大小。
   
```azurepowershell-interactive
Stop-AzVM -ResourceGroupName $resourceGroup -Name $vmName -Force
$vm = Get-AzVM -ResourceGroupName $resourceGroup -VMName $vmName
$vm.HardwareProfile.VmSize = "<newVMSize>"
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
Start-AzVM -ResourceGroupName $resourceGroup -Name $vmName
```

> [!WARNING]
> 解除分配 VM 会释放分配给该 VM 的所有动态 IP 地址。 OS 和数据磁盘不受影响。 
> 
> 

**使用 PowerShell 重设可用性集中的 VM 的大小**

如果可用性集中 VM 的新大小在当前托管 VM 的硬件群集上不可用，则需要解除分配可用性集中的所有 VM 以调整 VM 大小。 已调整一个 VM 的大小后，可能还需要更新可用性集中其他 VM 的大小。 若要调整可用性集中 VM 的大小，请执行以下步骤。

```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$vmName = "myVM"
```

列出托管 VM 的硬件群集上可用的 VM 大小。 
   
```azurepowershell-interactive
Get-AzVMSize `
-ResourceGroupName $resourceGroup `
-VMName $vmName 
```

如果列出了所需大小，请运行以下命令来调整 VM 的大小。 如果未列出所需大小，请转到下一部分。
   
```azurepowershell-interactive
$vm = Get-AzVM `
-ResourceGroupName $resourceGroup `
-VMName $vmName 
$vm.HardwareProfile.VmSize = "<newVmSize>"
Update-AzVM `
-VM $vm `
-ResourceGroupName $resourceGroup
```
    
如果未列出所需大小，继续执行以下步骤即可解除分配可用性集中的所有 VM、重设 VM 大小，并重新启动它们。

停止可用性集中的所有 VM。
   
```azurepowershell-interactive
$availabilitySetName = "<availabilitySetName>"
$as = Get-AzAvailabilitySet `
-ResourceGroupName $resourceGroup `
-Name $availabilitySetName
$virtualMachines = $as.VirtualMachinesReferences |  Get-AzResource | Get-AzVM
$virtualMachines |  Stop-AzVM -Force -NoWait  
```

调整可用性集中 VM 的大小并重新启动 VM。
   
```azurepowershell-interactive
$availabilitySetName = "<availabilitySetName>"
$newSize = "<newVmSize>"
$as = Get-AzAvailabilitySet -ResourceGroupName $resourceGroup -Name $availabilitySetName
$virtualMachines = $as.VirtualMachinesReferences |  Get-AzResource | Get-AzVM
$virtualMachines | Foreach-Object { $_.HardwareProfile.VmSize = $newSize }
$virtualMachines | Update-AzVM
$virtualMachines | Start-AzVM
```

---

## <a name="next-steps"></a>后续步骤

若要提高可伸缩性，请运行多个 VM 实例并进行横向扩展。有关详细信息，请参阅[自动缩放虚拟机规模集中的虚拟机](../virtual-machine-scale-sets/tutorial-autoscale-powershell.md)。
