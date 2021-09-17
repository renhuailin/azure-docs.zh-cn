---
title: 从上传的通用化 Windows VHD 创建 VM
description: 将通用化 Windows VHD 上传到 Azure 并使用它来创建新的 VM（使用 Resource Manager 部署模型）。
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 12/12/2019
ms.author: cynthn
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 4f31fff34dcaaa8cc30b1a894e24f3eda8e5764d
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122688583"
---
# <a name="upload-a-generalized-windows-vhd-and-use-it-to-create-new-vms-in-azure"></a>上传通用化 Windows VHD 并使用它在 Azure 中创建新的 VM

适用于：:heavy_check_mark: Windows VM :heavy_check_mark: 灵活规模集 

本文逐步讲解如何使用 PowerShell 将通用化 VM 的 VHD 上传到 Azure、从该 VHD 创建映像，然后从该映像创建新 VM。 可以上传从本地虚拟化工具或其他云导出的 VHD。 对新的 VM 使用[托管磁盘](../managed-disks-overview.md)可以简化 VM 管理，在将 VM 置于可用性集中时提供更好的可用性。 

有关示例脚本，请参阅[将 VHD 上传到 Azure 并创建新的 VM 的示例脚本](/previous-versions/azure/virtual-machines/scripts/virtual-machines-windows-powershell-upload-generalized-script)。

## <a name="before-you-begin"></a>准备阶段

- 将任何 VHD 上传到 Azure 之前，应该遵循[准备要上传到 Azure 的 Windows VHD 或 VHDX](prepare-for-upload-vhd-image.md)中的步骤操作。
- 开始迁移到[托管磁盘](../managed-disks-overview.md)之前，请先查看[规划迁移到托管磁盘](on-prem-to-azure.md#plan-for-the-migration-to-managed-disks)。

 
## <a name="generalize-the-source-vm-by-using-sysprep"></a>使用 Sysprep 通用化源 VM

需要先对 VM 运行 Sysprep（如果尚未这样做），然后再将 VHD 上传到 Azure。 Sysprep 将删除所有个人帐户信息及其他某些数据，并准备好要用作映像的计算机。 有关 Sysprep 的详细信息，请参阅 [Sysprep 概述](/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview)。

确保 Sysprep 支持计算机上运行的服务器角色。 有关详细信息，请参阅 [Sysprep Support for Server Roles](/windows-hardware/manufacture/desktop/sysprep-support-for-server-roles)（Sysprep 对服务器角色的支持）。

> [!IMPORTANT]
> 如果计划在首次将 VHD 上传到 Azure 之前运行 Sysprep，请确保先[准备好 VM](prepare-for-upload-vhd-image.md)。 
> 
> 

1. 登录到 Windows 虚拟机。
1. 以管理员身份打开“命令提示符”窗口。 
1. 删除 panther 目录 (C:\Windows\Panther)。
1. 将目录切换到 %windir%\system32\sysprep，然后运行 `sysprep.exe`。
1. 在“系统准备工具”对话框中，选择“进入系统全新体验(OOBE)”，确保已选中“通用化”复选框。  
1. 在“关机选项”中选择“关机”。
1. 选择“确定”。
   
    ![启动 Sysprep](./media/upload-generalized-managed/sysprepgeneral.png)
1. 在 Sysprep 完成时，它会关闭虚拟机。 不要重新启动 VM。


## <a name="upload-the-vhd"></a>上传 VHD 

现在可以直接将 VHD 上传到托管磁盘中。 有关说明，请参阅[使用 Azure PowerShell 将 VHD 上传到 Azure](disks-upload-vhd-to-managed-disk-powershell.md)。



将 VHD 上传到托管磁盘后，需要使用 [Get-AzDisk](/powershell/module/az.compute/get-azdisk) 获取托管磁盘。

```azurepowershell-interactive
$disk = Get-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'myDiskName'
```

## <a name="create-the-image"></a>创建映像
从通用化 OS 托管磁盘创建托管映像。 将以下值替换为自己的信息。

首先，设置一些变量：

```powershell
$location = 'East US'
$imageName = 'myImage'
$rgName = 'myResourceGroup'
```

使用托管磁盘创建映像。

```azurepowershell-interactive
$imageConfig = New-AzImageConfig `
   -Location $location
$imageConfig = Set-AzImageOsDisk `
   -Image $imageConfig `
   -OsState Generalized `
   -OsType Windows `
   -ManagedDiskId $disk.Id
```

创建映像。

```azurepowershell-interactive
$image = New-AzImage `
   -ImageName $imageName `
   -ResourceGroupName $rgName `
   -Image $imageConfig
```

## <a name="create-the-vm"></a>创建 VM

现在，你已有了一个映像，可以从该映像创建一个或多个新 VM。 本示例从 *myResourceGroup* 中的 *myImage* 创建名为 *myVM* 的 VM。


```powershell
New-AzVm `
    -ResourceGroupName $rgName `
    -Name "myVM" `
    -Image $image.Id `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNSG" `
    -PublicIpAddressName "myPIP" `
    -OpenPorts 3389
```


## <a name="next-steps"></a>后续步骤

登录新虚拟机。 有关详细信息，请参阅 [How to connect and log on to an Azure virtual machine running Windows](connect-logon.md)（如何连接并登录到运行 Windows 的 Azure 虚拟机）。
