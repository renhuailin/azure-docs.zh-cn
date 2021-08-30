---
title: 在 Azure 中创建托管映像
description: 在 Azure 中创建通用 VM 或 VHD 的托管映像。 映像可用于创建多个使用托管磁盘的 VM。
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 09/27/2018
ms.author: cynthn
ms.custom: legacy
ms.openlocfilehash: aa377267fb522de03ee181db99963498b5075fc4
ms.sourcegitcommit: ca38027e8298c824e624e710e82f7b16f5885951
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/24/2021
ms.locfileid: "112574337"
---
# <a name="create-a-managed-image-of-a-generalized-vm-in-azure"></a>在 Azure 中创建通用 VM 的托管映像

可通过在存储帐户中存储为托管/非托管磁盘的通用虚拟机 (VM) 创建托管的映像资源。 然后可以使用该映像创建多个 VM。 有关托管映像如何计费的信息，请参阅[托管磁盘定价](https://azure.microsoft.com/pricing/details/managed-disks/)。 

一个托管映像最多支持 20 个同时部署。 如果尝试从同一托管映像同时创建超过 20 个 VM，则可能会由于单个 VHD 的存储性能限制而导致预配超时。 若要同时创建 20 个以上的 VM，请使用为每 20 个并发 VM 部署配置 1 个副本的[共享映像库](../shared-image-galleries.md)映像。

## <a name="prerequisites"></a>先决条件

需要[通用化](../generalize.md) VM 才能创建映像。

## <a name="create-a-managed-image-in-the-portal"></a>在门户中创建托管映像 

1. 转到 [Azure 门户](https://portal.azure.com)以管理 VM 映像。 搜索并选择“虚拟机”。

2. 从列表中选择 VM。

3. 在 VM 的“虚拟机”页面的上方菜单中，选择“捕获”。

   将显示“创建映像”页面。

4. 对于“名称”，可以使用预填充的名称或输入想要为映像使用的名称。

5. 对于“资源组”，选择“新建”并输入名称，或者从下拉列表中选择要使用的资源组。

6. 如果想要在创建映像后删除源 VM，选择“创建映像后自动删除此虚拟机”。

7. 如果希望能够在任何[可用性区域](../../availability-zones/az-overview.md)中使用映像，请为“区域复原”选择“打开”。

8. 选择“创建”以创建映像。

创建映像后，在资源组的资源列表中，你会看到它作为“映像”资源而出现。



## <a name="create-an-image-of-a-vm-using-powershell"></a>使用 PowerShell 创建 VM 映像

 

直接从 VM 创建映像，可确保映像包含所有与该 VM 关联的磁盘，包括 OS 磁盘和任何数据磁盘。 本示例演示如何从使用托管磁盘的 VM 创建托管映像。

在开始之前，请确保有最新版本的 Azure PowerShell 模块。 若要查找版本，请在 PowerShell 中运行 `Get-Module -ListAvailable Az`。 如需升级，请参阅[使用 PowerShellGet 在 Windows 上安装 Azure PowerShell](/powershell/azure/install-az-ps)。 如果在本地运行 PowerShell，则运行 `Connect-AzAccount` 以创建与 Azure 的连接。


> [!NOTE]
> 如果希望将映像存储在具有区域冗余能力的存储中，需要在支持[可用性区域](../../availability-zones/az-overview.md)的区域中创建该映像并在映像配置中包括 `-ZoneResilient` 参数（`New-AzImageConfig` 命令）。

若要创建 VM 映像，请遵循下列步骤：

1. 创建一些变量。

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    ```
2. 确保 VM 已解除分配。

    ```azurepowershell-interactive
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force
    ```
    
3. 将虚拟机的状态设置为“通用化”。 
   
    ```azurepowershell-interactive
    Set-AzVm -ResourceGroupName $rgName -Name $vmName -Generalized
    ```
    
4. 获取虚拟机。 

    ```azurepowershell-interactive
    $vm = Get-AzVM -Name $vmName -ResourceGroupName $rgName
    ```

5. 创建映像配置。

    ```azurepowershell-interactive
    $image = New-AzImageConfig -Location $location -SourceVirtualMachineId $vm.Id 
    ```
6. 创建映像。

    ```azurepowershell-interactive
    New-AzImage -Image $image -ImageName $imageName -ResourceGroupName $rgName
    ``` 

## <a name="create-an-image-from-a-managed-disk-using-powershell"></a>使用 PowerShell 从托管磁盘创建映像

如果仅想创建 OS 磁盘的映像，则将托管磁盘 ID 指定为 OS 磁盘：

    
1. 创建一些变量。 

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    ```

2. 获取 VM。

   ```azurepowershell-interactive
   $vm = Get-AzVm -Name $vmName -ResourceGroupName $rgName
   ```

3. 获取托管磁盘的 ID。

    ```azurepowershell-interactive
    $diskID = $vm.StorageProfile.OsDisk.ManagedDisk.Id
    ```
   
3. 创建映像配置。

    ```azurepowershell-interactive
    $imageConfig = New-AzImageConfig -Location $location
    $imageConfig = Set-AzImageOsDisk -Image $imageConfig -OsState Generalized -OsType Windows -ManagedDiskId $diskID
    ```
    
4. 创建映像。

    ```azurepowershell-interactive
    New-AzImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ``` 


## <a name="create-an-image-from-a-snapshot-using-powershell"></a>使用 PowerShell 从快照创建映像

通过执行以下步骤，可以从通用 VM 的快照创建托管映像：

    
1. 创建一些变量。 

    ```azurepowershell-interactive
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $snapshotName = "mySnapshot"
    $imageName = "myImage"
    ```

2. 获取快照。

   ```azurepowershell-interactive
   $snapshot = Get-AzSnapshot -ResourceGroupName $rgName -SnapshotName $snapshotName
   ```
   
3. 创建映像配置。

    ```azurepowershell-interactive
    $imageConfig = New-AzImageConfig -Location $location
    $imageConfig = Set-AzImageOsDisk -Image $imageConfig -OsState Generalized -OsType Windows -SnapshotId $snapshot.Id
    ```
4. 创建映像。

    ```azurepowershell-interactive
    New-AzImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ``` 


## <a name="create-an-image-from-a-vm-that-uses-a-storage-account"></a>从使用存储帐户的 VM 创建映像

若要从不使用托管磁盘的 VM 创建托管映像，需要存储帐户中 OS VHD 的 URI，其格式如下： https://mystorageaccount.blob.core.windows.net/vhdcontainer/vhdfilename.vhd。 在本示例中，VHD 位于名为 vhdcontainer 的容器中的 mystorageaccount 中，且 VHD 文件名为 vhdfilename.vhd。


1.  创建一些变量。

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    $osVhdUri = "https://mystorageaccount.blob.core.windows.net/vhdcontainer/vhdfilename.vhd"
    ```
2. 停止/解除分配 VM。

    ```azurepowershell-interactive
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force
    ```
    
3. 将 VM 标记为通用。

    ```azurepowershell-interactive
    Set-AzVm -ResourceGroupName $rgName -Name $vmName -Generalized  
    ```
4.  使用通用化的 OS VHD 创建映像。

    ```azurepowershell-interactive
    $imageConfig = New-AzImageConfig -Location $location
    $imageConfig = Set-AzImageOsDisk -Image $imageConfig -OsType Windows -OsState Generalized -BlobUri $osVhdUri
    $image = New-AzImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ```

    
## <a name="next-steps"></a>后续步骤
- [从托管的映像创建 VM](create-vm-generalized-managed.md)。 
