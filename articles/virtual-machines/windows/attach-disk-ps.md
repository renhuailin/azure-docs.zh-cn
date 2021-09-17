---
title: 使用 PowerShell 将数据磁盘附加到 Azure 中的 Windows VM
description: 如何配合使用 PowerShell 和 Resource Manager 部署模型将新磁盘或现有数据磁盘附加到 Windows VM。
author: roygara
ms.service: virtual-machines
ms.collection: windows
ms.topic: how-to
ms.date: 10/16/2018
ms.author: rogarana
ms.subservice: disks
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: f8fbcbb70ae73c9fdfe0aa388c07d23d6c4f8e20
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122692616"
---
# <a name="attach-a-data-disk-to-a-windows-vm-with-powershell"></a>使用 PowerShell 将数据磁盘附加到 Windows VM

**适用于：** :heavy_check_mark: Windows VM :heavy_check_mark: 灵活规模集 

本文介绍了如何使用 PowerShell 将新磁盘和现有磁盘附加到 Windows 虚拟机。 

首先，查看以下提示：

* 虚拟机的大小决定了可以附加多少个磁盘。 有关详细信息，请参阅[虚拟机的大小](../sizes.md)。
* 若要使用高级 SSD，则需要使用[支持高级存储的 VM 类型](../sizes-memory.md)，例如 DS 系列或 GS 系列虚拟机。

本文在 [Azure Cloud Shell](../../cloud-shell/overview.md) 中使用 PowerShell，后者会不断更新到最新版本。 若要打开 Cloud Shell，请从任何代码块的顶部选择“试一试”  。

## <a name="add-an-empty-data-disk-to-a-virtual-machine"></a>将空数据磁盘添加到虚拟机

此示例演示了如何将空数据磁盘添加到现有虚拟机。

### <a name="using-managed-disks"></a>使用托管磁盘

```azurepowershell-interactive
$rgName = 'myResourceGroup'
$vmName = 'myVM'
$location = 'East US'
$storageType = 'Premium_LRS'
$dataDiskName = $vmName + '_datadisk1'

$diskConfig = New-AzDiskConfig -SkuName $storageType -Location $location -CreateOption Empty -DiskSizeGB 128
$dataDisk1 = New-AzDisk -DiskName $dataDiskName -Disk $diskConfig -ResourceGroupName $rgName

$vm = Get-AzVM -Name $vmName -ResourceGroupName $rgName
$vm = Add-AzVMDataDisk -VM $vm -Name $dataDiskName -CreateOption Attach -ManagedDiskId $dataDisk1.Id -Lun 1

Update-AzVM -VM $vm -ResourceGroupName $rgName
```

### <a name="using-managed-disks-in-an-availability-zone"></a>在可用性区域中使用托管磁盘

若要在可用性区域中创建磁盘，请将 [New-AzDiskConfig](/powershell/module/az.compute/new-azdiskconfig) 与 `-Zone` 参数一起使用。 以下示例在区域 *1* 中创建一个磁盘。

```powershell
$rgName = 'myResourceGroup'
$vmName = 'myVM'
$location = 'East US 2'
$storageType = 'Premium_LRS'
$dataDiskName = $vmName + '_datadisk1'

$diskConfig = New-AzDiskConfig -SkuName $storageType -Location $location -CreateOption Empty -DiskSizeGB 128 -Zone 1
$dataDisk1 = New-AzDisk -DiskName $dataDiskName -Disk $diskConfig -ResourceGroupName $rgName

$vm = Get-AzVM -Name $vmName -ResourceGroupName $rgName
$vm = Add-AzVMDataDisk -VM $vm -Name $dataDiskName -CreateOption Attach -ManagedDiskId $dataDisk1.Id -Lun 1

Update-AzVM -VM $vm -ResourceGroupName $rgName
```

### <a name="initialize-the-disk"></a>初始化磁盘

添加空磁盘后，需要对其进行初始化。 要初始化该磁盘，可以登录到一个 VM，并使用磁盘管理进行初始化。 如果在创建 VM 时在其上启用了 [WinRM](/windows/desktop/winrm/portal) 和证书，则可以使用远程 PowerShell 初始化该磁盘。 还可以使用自定义脚本扩展：

```azurepowershell-interactive
    $location = "location-name"
    $scriptName = "script-name"
    $fileName = "script-file-name"
    Set-AzVMCustomScriptExtension -ResourceGroupName $rgName -Location $locName -VMName $vmName -Name $scriptName -TypeHandlerVersion "1.4" -StorageAccountName "mystore1" -StorageAccountKey "primary-key" -FileName $fileName -ContainerName "scripts"
```

脚本文件可以包含用来初始化磁盘的代码，例如：

```azurepowershell-interactive
    $disks = Get-Disk | Where partitionstyle -eq 'raw' | sort number

    $letters = 70..89 | ForEach-Object { [char]$_ }
    $count = 0
    $labels = "data1","data2"

    foreach ($disk in $disks) {
        $driveLetter = $letters[$count].ToString()
        $disk |
        Initialize-Disk -PartitionStyle MBR -PassThru |
        New-Partition -UseMaximumSize -DriveLetter $driveLetter |
        Format-Volume -FileSystem NTFS -NewFileSystemLabel $labels[$count] -Confirm:$false -Force
    $count++
    }
```

## <a name="attach-an-existing-data-disk-to-a-vm"></a>将现有数据磁盘附加到 VM

可以将现有的托管磁盘作为数据磁盘附加到虚拟机。

```azurepowershell-interactive
$rgName = "myResourceGroup"
$vmName = "myVM"
$dataDiskName = "myDisk"
$disk = Get-AzDisk -ResourceGroupName $rgName -DiskName $dataDiskName

$vm = Get-AzVM -Name $vmName -ResourceGroupName $rgName

$vm = Add-AzVMDataDisk -CreateOption Attach -Lun 0 -VM $vm -ManagedDiskId $disk.Id

Update-AzVM -VM $vm -ResourceGroupName $rgName
```

## <a name="next-steps"></a>后续步骤

还可以使用模板部署托管磁盘。 有关详细信息，请参阅[使用 Azure 资源管理器模板中的托管磁盘](../using-managed-disks-template-deployments.md)或[快速入门模板](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/vm-multiple-data-disk)以部署多个数据磁盘。
