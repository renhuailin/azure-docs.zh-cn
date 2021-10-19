---
title: 教程 - 使用 Azure PowerShell 管理 Azure 磁盘
description: 本教程介绍如何使用 Azure PowerShel 为虚拟机创建和管理 Azure 磁盘
author: roygara
ms.author: rogarana
ms.service: storage
ms.subservice: disks
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.date: 10/08/2021
ms.custom: template-tutorial, devx-track-azurepowershell
ms.openlocfilehash: 52c5f10b0a41ec2362af09c972f29ed079ae70d7
ms.sourcegitcommit: 54e7b2e036f4732276adcace73e6261b02f96343
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/12/2021
ms.locfileid: "129808060"
---
# <a name="tutorial-manage-disks-with-azure-powershell"></a>教程：使用 Azure PowerShell 管理磁盘

Azure 虚拟机 (VM) 使用磁盘来存储操作系统 (OS)、应用程序和数据。 创建 VM 时，请务必选择适用于所需工作负载的磁盘大小和配置。

本教程介绍 VM 磁盘的部署和管理。 在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 创建、附加和初始化数据磁盘
> * 验证磁盘状态
> * 初始化磁盘
> * 扩展和升级磁盘
> * 拆离和删除磁盘

## <a name="prerequisites"></a>必备条件

你的 Azure 帐户必须具有有效订阅。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-vm"></a>创建 VM

本教程中的练习需要用到 VM。 请遵循本部分中的步骤进行创建。

在开始之前，找到位于示例代码第一行中的 `$azRegion` 变量，并更新该值以反应所需的区域。 例如，使用 `$azRegion = "Central US"` 指定“美国中部”区域。 接着，使用代码将 VM 部署到新的资源组中。 系统提示输入 VM 本地管理员帐户的用户名和密码值。

```azurepowershell-interactive
$azRegion = "[Your Region]"
$azResourceGroup = "myDemoResourceGroup"
$azVMName = "myDemoVM"
$azDataDiskName = "myDemoDataDisk"

New-AzVm `
    -Location $azRegion `
    -ResourceGroupName $azResourceGroup `
    -Name $azVMName `
    -Size "Standard_D2s_v3" `
    -VirtualNetworkName "myDemoVnet" `
    -SubnetName "myDemoSubnet" `
    -SecurityGroupName "myDemoNetworkSecurityGroup" `
    -PublicIpAddressName "myDemoPublicIpAddress"
```

输出确认 VM 已成功创建。

```Output
ResourceGroupName        : myDemoResourceGroup
Id                       : /subscriptions/{GUID}/resourceGroups/myDemoResourceGroup/providers/Microsoft.Compute/virtualMachines/myDemoTestVM
VmId                     : [{GUID}]
Name                     : myDemoVM
Type                     : Microsoft.Compute/virtualMachines
Location                 : centralus
Tags                     : {}
HardwareProfile          : {VmSize}
NetworkProfile           : {NetworkInterfaces}
OSProfile                : {ComputerName, AdminUsername, WindowsConfiguration, AllowExtensionOperations, RequireGuestProvisionSignal}
ProvisioningState        : Succeeded
StorageProfile           : {ImageReference, OsDisk, DataDisks}
FullyQualifiedDomainName : mydemovm-abc123.Central US.cloudapp.azure.com
```

VM 已预配，并将自动创建及附加两个磁盘。

- 操作系统磁盘承载虚拟机的操作系统。
- 临时磁盘，主要用于临时数据处理等操作。

## <a name="add-a-data-disk"></a>添加数据磁盘

建议尽可能将应用程序和用户数据与 OS 相关数据分开。 如果需要在 VM 上存储用户或应用程序数据，通常将创建并附加其他数据磁盘。

按照本部分的步骤，在 VM 上创建、附加和初始化数据磁盘。

### <a name="create-the-data-disk"></a>创建数据磁盘

本部分将指导你创建数据磁盘。

1. 在创建数据磁盘之前，必须先创建磁盘对象。 下面的代码示例使用 [New-AzDiskConfig](/powershell/module/az.compute/new-azdiskconfig) cmdlet 配置磁盘对象。

    ```azurepowershell-interactive
    $diskConfig = New-AzDiskConfig `
        -Location $azRegion `
        -CreateOption Empty `
        -DiskSizeGB 128 `
        -SkuName "Standard_LRS"
    ```

1. 创建磁盘对象后，使用 [New-AzDisk](/powershell/module/az.compute/new-azdisk) cmdlet 预配数据磁盘。

    ```azurepowershell-interactive
    $dataDisk = New-AzDisk `
        -ResourceGroupName $azResourceGroup `
        -DiskName $azDataDiskName `
        -Disk $diskConfig
    ```

    可以使用 [Get-AzDisk](/powershell/module/az.compute/get-azdisk) cmdlet 验证是否已创建磁盘。

    ```azurepowershell-interactive
    Get-AzDisk `
        -ResourceGroupName $azResourceGroup `
        -DiskName $azDataDiskName
    ```

    此示例中，输出确认磁盘已创建。 `DiskState` 和 `ManagedBy` 属性值确认磁盘尚未附加。

    ```Output
    ResourceGroupName            : myDemoResourceGroup
    ManagedBy                    :
    ManagedByExtended            : {}
    OsType                       :
    DiskSizeGB                   : 128
    DiskSizeBytes                : 137438953472
    ProvisioningState            : Succeeded
    DiskIOPSReadWrite            : 500
    DiskMBpsReadWrite            : 60
    DiskState                    : Unattached
    Name                         : myDemoDataDisk
    ```

### <a name="attach-the-data-disk"></a>附加数据磁盘

必须先将数据磁盘附加到 VM，然后 VM 才能访问。 完成本部分中的步骤，为 VM 创建引用、连接磁盘并更新 VM 的配置。

1. 获取要附加数据磁盘的 VM。 以下示例代码使用 [Get-AzVM](/powershell/module/az.compute/get-azvm) cmdlet 创建对 VM 的引用。

    ```azurepowershell-interactive
    $vm = Get-AzVM `
        -ResourceGroupName $azResourceGroup `
        -Name $azVMName
    ```

1. 接着，使用 [Add-AzVMDataDisk](/powershell/module/az.compute/add-azvmdatadisk) cmdlet 将数据磁盘附加到 VM 配置。

    ```azurepowershell-interactive
    $vm = Add-AzVMDataDisk `
        -VM $vm `
        -Name $azDataDiskName `
        -CreateOption Attach `
        -ManagedDiskId $dataDisk.Id `
        -Lun 1
    ```

1. 最后，使用 [Update-AzVM](/powershell/module/az.compute/add-azvmdatadisk) cmdlet 更新 VM 配置。

    ```azurepowershell-interactive
    Update-AzVM `
        -ResourceGroupName $azResourceGroup `
        -VM $vm
    ```

    短暂暂停后，输出将确认附加成功。

    ```Output
    RequestId IsSuccessStatusCode StatusCode ReasonPhrase
    --------- ------------------- ---------- ------------
                             True         OK OK
    ```

### <a name="initialize-the-data-disk"></a>初始化数据磁盘

将数据磁盘附加到 VM 后，需要将 OS 配置为使用该磁盘。 以下部分提供有关如何连接到远程 VM 并对所添加的第一个磁盘进行配置的指导。

1. 登录 [Azure 门户](https://portal.azure.com)。

1. 找到已附加数据磁盘的 VM。 创建远程桌面协议 (RDP) 连接，然后以本地管理员身份登录。

1. 建立与远程 VM 的 RDP 连接后，选择 Windows“开始”菜单。 在搜索框中输入 PowerShell，然后选择 Windows PowerShell 打开 PowerShell 窗口 。

    [![远程桌面连接窗口的图片。](media\tutorial-manage-data-disk\initialize-disk-sml.png)](media\tutorial-manage-data-disk\initialize-disk-lrg.png#lightbox)

1. 在打开的 PowerShell 窗口中，运行以下脚本。

    ```powershell
    Get-Disk | Where PartitionStyle -eq 'raw' |
        Initialize-Disk -PartitionStyle MBR -PassThru |
        New-Partition -AssignDriveLetter -UseMaximumSize |
        Format-Volume -FileSystem NTFS -NewFileSystemLabel "myDemoDataDisk" -Confirm:$false
    ```

    输出确认初始化成功。

    ```Output
    DriveLetter FileSystemLabel FileSystem DriveType HealthStatus OperationalStatus SizeRemaining   Size
    ----------- --------------- ---------- --------- ------------ ----------------- -------------   ----
    F           myDemoDataDisk  NTFS       Fixed     Healthy      OK                    127.89 GB 128 GB
    ```

## <a name="expand-a-disk"></a>扩展磁盘

当 VM 的可用磁盘空间不足时，可以扩展 Azure 磁盘以提供额外的存储容量。

在某些情况下，需要将数据存储在 OS 磁盘上。 例如，可能需要支持将组件安装在 OS 驱动器上的旧应用程序。 可能还需要迁移具有较大 OS 驱动器的本地物理 PC 或 VM。 在这种情况下，可能有必要扩展 VM 的 OS 磁盘。

不支持收缩现有磁盘，收缩现有磁盘可能导致数据丢失。

### <a name="update-the-disks-size"></a>更新磁盘大小

按照以下步骤重设 OS 磁盘或数据磁盘的大小。

1. 使用 `Get-AzVM` cmdlet 选择包含将重设其大小的磁盘的 VM。

    ```azurepowershell-interactive
     $vm = Get-AzVM `
       -ResourceGroupName $azResourceGroup `
       -Name $azVMName
    ```

1. 在重设 VM 磁盘大小之前，必须先停止 VM。 使用 `Stop-AzVM` cmdlet 停止 VM。 系统会提示进行确认。

    > [!IMPORTANT]
    > 启动 VM 关闭之前，请始终确认没有可能会丢失的重要资源或数据。

    ```azurepowershell-interactive
    Stop-AzVM `
        -ResourceGroupName $azResourceGroup `
        -Name $azVMName
    ```

    短暂暂停后，输出将确认计算机已成功停止。

    ```Output
    OperationId : abcd1234-ab12-cd34-123456abcdef
    Status      : Succeeded
    StartTime   : 9/13/2021 7:10:23 PM
    EndTime     : 9/13/2021 7:11:12 PM
    Error       :
    ```

1. 停止 VM 后，使用 `Get-AzDisk` cmdlet 获取对附加到 VM 的 OS 或数据磁盘的引用。

    以下示例选择 VM 的 OS 磁盘。

    ```azurepowershell-interactive
    $disk= Get-AzDisk `
        -ResourceGroupName $azResourceGroup `
        -DiskName $vm.StorageProfile.OsDisk.Name
    ```

    以下示例选择 VM 的第一个数据磁盘。

    ```azurepowershell-interactive
        $disk= Get-AzDisk `
            -ResourceGroupName $azResourceGroup `
            -DiskName $vm.StorageProfile.DataDisks[0].Name
    ```

1. 获得对磁盘的引用后，将磁盘大小设置为 250 GiB。

    > [!IMPORTANT]
    > 新大小应该大于现有磁盘大小。 OS 磁盘允许的最大值为 4,095 GB。

    ```azurepowershell-interactive
    $disk.DiskSizeGB = 250
    ```

1. 接下来，使用 `Update-AzDisk` cmdlet 更新磁盘映像。

    ```azurepowershell-interactive
    Update-AzDisk `
        -ResourceGroupName $azResourceGroup `
        -Disk $disk -DiskName $disk.Name
    ```

    磁盘映像已更新，输出确认磁盘更新后的大小。

    ```Output
    ResourceGroupName            : myDemoResourceGroup
    ManagedBy                    : /subscriptions/{GUID}/resourceGroups/myDemoResourceGroup/providers/Microsoft.Compute/virtualMachines/myDemoVM
    Sku                          : Microsoft.Azure.Management.Compute.Models.DiskSku
    TimeCreated                  : 9/135/2021 6:41:10 PM
    CreationData                 : Microsoft.Azure.Management.Compute.Models.CreationData
    DiskSizeGB                   : 250
    DiskSizeBytes                : 268435456000
    UniqueId                     : {GUID}
    ProvisioningState            : Succeeded
    DiskIOPSReadWrite            : 500
    DiskMBpsReadWrite            : 60
    DiskState                    : Reserved
    Encryption                   : Microsoft.Azure.Management.Compute.Models.Encryption
    Id                           : /subscriptions/{GUID}/resourceGroups/myDemoResourceGroup/providers/Microsoft.Compute/disks/myDemoDataDisk
    Name                         : myDemoDataDisk
    Type                         : Microsoft.Compute/disks
    Location                     : centralus

1. Finally, restart the VM with the `Start-AzVM` cmdlet.

    ```azurepowershell-interactive
    Start-AzVM `
        -ResourceGroupName $azResourceGroup `
        -Name $azVMName
    ```

    短暂暂停后，输出将确认计算机已成功启动。

    ```Output
    OperationId : abcd1234-ab12-cd34-123456abcdef
    Status      : Succeeded
    StartTime   : 9/13/2021 7:44:54 PM
    EndTime     : 9/13/2021 7:45:15 PM
    Error       :
    ```

### <a name="expand-the-disk-volume-in-the-os"></a>扩展 OS 中的磁盘卷

在利用新的磁盘大小之前，需先扩展 OS 中的卷。 按照以下步骤扩展磁盘卷，并充分利用新的磁盘大小。

1. 登录 [Azure 门户](https://portal.azure.com)。

1. 找到已附加数据磁盘的 VM。 创建远程桌面协议 (RDP) 连接并登录。 如果不再有管理帐户的访问权限，则使用 [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential) cmdlet 为指定的用户名和密码创建凭据对象。

1. 与远程 VM 建立 RDP 连接后，选择 Windows“开始”菜单。 在搜索框中输入 PowerShell，然后选择 Windows PowerShell 打开 PowerShell 窗口 。

    [![远程桌面连接窗口的图片。](media\tutorial-manage-data-disk\initialize-disk-sml.png)](media\tutorial-manage-data-disk\initialize-disk-lrg.png#lightbox)

1. 打开 PowerShell 并运行以下脚本。 根据情况更改 `-DriveLetter` 变量的值。 例如，若要重设“F:”驱动器上的分区大小，请使用 `$driveLetter = "F"`。

    ```powershell
    $driveLetter = "[Drive Letter]" `
    $size = (Get-PartitionSupportedSize -DriveLetter $driveLetter) `
    Resize-Partition `
        -DriveLetter $driveLetter `
        -Size $size.SizeMax
    ```

1. 最小化 RDP 窗口并切换回 Azure Cloud Shell。 使用 `Get-AzDisk` cmdlet 验证磁盘大小是否重设成功。

    ```azurepowershell-interactive
    Get-AzDisk `
        -ResourceGroupName $azResourceGroup | Out-Host -Paging
    ```

## <a name="upgrade-a-disk"></a>升级磁盘

有多种方法可以响应组织工作负载的变化。 例如，可以选择将标准 HDD 升级到高级 SSD 来处理增加的需求。

按照本部分的步骤，将托管磁盘从标准升级到高级。

1. 使用 `Get-AzVM` cmdlet 选择包含将升级的磁盘的 VM。

    ```azurepowershell-interactive
     $vm = Get-AzVM `
       -ResourceGroupName $azResourceGroup `
       -Name $azVMName
    ```

1. 在升级 VM 的磁盘之前，必须先停止 VM。 使用 `Stop-AzVM` cmdlet 停止 VM。 系统会提示进行确认。

    > [!IMPORTANT]
    > 启动 VM 关闭之前，请始终确认没有可能会丢失的重要资源或数据。

    ```azurepowershell-interactive
    Stop-AzVM `
        -ResourceGroupName $azResourceGroup `
        -Name $azVMName
    ```

    短暂暂停后，输出将确认计算机已成功停止。

    ```Output
    OperationId : abcd1234-ab12-cd34-123456abcdef
    Status      : Succeeded
    StartTime   : 9/13/2021 7:10:23 PM
    EndTime     : 9/13/2021 7:11:12 PM
    Error       :
    ```

1. 停止 VM 后，使用 `Get-AzDisk` cmdlet 获取对附加到 VM 的 OS 或数据磁盘的引用。

    以下示例选择 VM 的 OS 磁盘。

    ```azurepowershell-interactive
    $disk= Get-AzDisk `
        -ResourceGroupName $azResourceGroup `
        -DiskName $vm.StorageProfile.OsDisk.Name
    ```

    以下示例选择 VM 的第一个数据磁盘。

    ```azurepowershell-interactive
        $disk= Get-AzDisk `
            -ResourceGroupName $azResourceGroup `
            -DiskName $vm.StorageProfile.DataDisks[0].Name
    ```

1. 现在，你已拥有对磁盘的引用，请将磁盘的 SKU 设置为 Premium_LRS。

    ```azurepowershell-interactive
    $disk.Sku = [Microsoft.Azure.Management.Compute.Models.DiskSku]::new('Premium_LRS')
    ```

1. 接下来，使用 `Update-AzDisk` cmdlet 升级磁盘映像。

    ```azurepowershell-interactive
    Update-AzDisk `
        -ResourceGroupName $azResourceGroup `
        -Disk $disk -DiskName $disk.Name
    ```

    磁盘映像已更新。 使用以下示例代码验证磁盘的 SKU 是否已升级。

    ```azurepowershell-interactive
    $disk.Sku.Name
    ```

    输出将确认磁盘的新 SKU。

    ```Output
    Premium_LRS
    ```

1. 最后，使用 `Start-AzVM` cmdlet 重启 VM。

    ```azurepowershell-interactive
    Start-AzVM `
        -ResourceGroupName $azResourceGroup `
        -Name $azVMName
    ```

    短暂暂停后，输出将确认计算机已成功启动。

    ```Output
    OperationId : abcd1234-ab12-cd34-123456abcdef
    Status      : Succeeded
    StartTime   : 9/13/2021 7:44:54 PM
    EndTime     : 9/13/2021 7:45:15 PM
    Error       :
    ```

## <a name="detach-a-data-disk"></a>分离数据磁盘

如果要将数据磁盘附加到其他 VM，或者不再需要它，可以将数据磁盘从 VM 中拆离。 默认情况下，不会删除已拆离的磁盘，以防意外丢失数据。 拆离的磁盘将继续产生存储费用，直到被删除。

1. 首先，使用 `Get-AzVM` cmdlet 选择要将磁盘附加到的 VM。

    ```azurepowershell-interactive
    $vm = Get-AzVM `
       -ResourceGroupName $azResourceGroup `
       -Name $azVMName
    ```

1. 使用 `Get-AzDisk` cmdlet 选择要删除的磁盘。

     ```azurepowershell-interactive
    $vm = Get-AzVM `
       -ResourceGroupName $azResourceGroup `
       -Name $azVMName
    ```

1. 接下来，使用 `Remove-AzVMDataDisk` cmdlet 将磁盘从 VM 拆离。

    ```azurepowershell-interactive
    Remove-AzVMDataDisk `
        -VM $vm `
        -Name $azDataDiskName
    ```

1. 使用 `Update-AzVM` cmdlet 对 VM 的状态进行更新，以删除数据磁盘。

    ```azurepowershell-interactive
    Update-AzVM `
        -ResourceGroupName $azResourceGroup `
        -VM $vm
    ```

    短暂暂停后，输出确认 VM 已成功更新。

    ```output
    RequestId IsSuccessStatusCode StatusCode ReasonPhrase
    --------- ------------------- ---------- ------------
                             True         OK OK
    ```

## <a name="delete-a-data-disk"></a>删除数据磁盘

删除 VM 时，附加到 VM 的数据磁盘将保持预配状态，并在删除之前持续产生费用。 此默认行为有助于防止因无意中删除而导致的数据丢失。

可以使用以下示例 PowerShell 脚本来删除未附加的磁盘。 磁盘检索仅限于 myDemoResourceGroup，因为在 `Get-AzDisk` cmdlet 中使用了 `-ResourceGroupName` 开关。

```azurepowershell
# Get all disks in resource group $azResourceGroup
$allDisks = Get-AzDisk -ResourceGroupName $azResourceGroup

# Determine the number of disks in the collection
if($allDisks.Count -ne 0) {

    Write-Host "Found $($allDisks.Count) disks."

    # Iterate through the collection
    foreach ($disk in $allDisks) {

        # Use the disk's "ManagedBy" property to determine if it is unattached
        if($disk.ManagedBy -eq $null) {

            # Confirm that the disk can be deleted
            Write-Host "Deleting unattached disk $($disk.Name)."
            $confirm = Read-Host "Continue? (Y/N)"
            if ($confirm.ToUpper() -ne 'Y') { break }
            else {

                # Delete the disk
                $disk | Remove-AzDisk -Force 
                Write-Host "Unattached disk $($disk.Name) deleted."
            }
        }
    }
}
```

将删除未附加的数据磁盘，如输出所示。

```output
Name      : abcd1234-ab12-cd34-ef56-abcdef123456
StartTime : 9/13/2021 10:14:05 AM
EndTime   : 9/13/2021 10:14:35 AM
Status    : Succeeded
Error     :
```

## <a name="clean-up-resources"></a>清理资源

请删除不再需要的资源组、VM 及所有相关资源。 你可以使用以下示例 PowerShell 脚本删除在本教程前面创建的资源组。  

> [!CAUTION]
> 删除资源组时要格外小心。 在删除资源组之前，请始终确认该资源组中没有任何重要的资源或数据，以避免丢失重要数据。

```azurepowershell-interactive
    Remove-AzResourceGroup -Name $azResourceGroup
```

系统会提示进行确认。 短暂停顿后，`True` 响应确认 myDemoResourceGroup 已成功删除。
    
```Output
Confirm
Are you sure you want to remove resource group 'myDemoResourceGroup'
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y
True
```

## <a name="next-steps"></a>后续步骤

在本教程中，你了解了如何执行以下操作：

> [!div class="checklist"]
> * 创建、附加和初始化数据磁盘
> * 验证磁盘状态
> * 初始化磁盘
> * 扩展和升级磁盘
> * 拆离和删除磁盘

转到下一教程，了解如何自动配置 VM。

> [!div class="nextstepaction"]
> [自动配置 VM](./tutorial-automate-vm-deployment.md)
