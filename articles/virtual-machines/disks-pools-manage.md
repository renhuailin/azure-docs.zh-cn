---
title: 管理 Azure 磁盘池（预览版）
description: 了解如何将托管磁盘添加到 Azure 磁盘池或在磁盘上禁用 iSCSI 支持。
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 07/19/2021
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 4811894a3483bbfce1724b744d904fa328c51ded
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114436980"
---
# <a name="manage-an-azure-disk-pool-preview"></a>管理 Azure 磁盘池（预览版）

本文介绍如何将托管磁盘添加到 Azure 磁盘池（预览版）以及如何在已添加到磁盘池的磁盘上禁用 iSCSI 支持。

## <a name="prerequisites"></a>先决条件

安装 Azure PowerShell 模块 [6.1.0 或更高版本](/powershell/module/az.diskpool/?view=azps-6.1.0&preserve-view=true)。

## <a name="add-a-disk-to-a-pool"></a>将磁盘添加到池

磁盘必须满足以下要求才能添加到磁盘池：
- 必须是与磁盘池位于同一区域和可用区的高级 SSD 或超级磁盘。
    - 超级磁盘的磁盘扇区大小必须为 512 字节。
- 必须是 maxShares 值至少为 2 的共享磁盘。
- 你需要[为将添加到磁盘池的磁盘提供 StoragePool 资源提供程序 RBAC 权限](disks-pools-deploy.md#assign-storagepool-resource-provider-permissions)。

以下脚本将额外的磁盘添加到磁盘池并通过 iSCSI 公开。 它将现有磁盘保留在磁盘池中而不做任何更改。

```azurepowershell
#Initialize input parameters
$resourceGroupName ="<yourResourceGroupName>"
$diskPoolName = "<yourDiskPoolName>"
$iscsiTargetName = "<youriSCSITargetName>"
$diskName ="<yourDiskName>" #Provide the name of the disk you want to add
$lunName ='LunName>' #Provide the Lun name of the added disk
$diskIds = @()

#Add the disk to disk pool
$DiskPool = Get-AzDiskPool -Name $diskPoolName -ResourceGroupName $resourceGroupName
$DiskPoolDiskIDs = $DiskPool.Disk.Id
foreach ($Id in $DiskPoolDiskIDs)
{
$diskIds += ($Id)
}

$disk = Get-AzDisk -ResourceGroupName $resourceGroupName -DiskName $diskName
$diskIds += ,($disk.Id)
Update-AzDiskPool -ResourceGroupName $resourceGroupName -Name $diskPoolName -DiskId $diskIds

#Get the existing iSCSI LUNs and add the new disk
$target = Get-AzDiskPoolIscsiTarget -name $iscsiTargetName -DiskPoolName $diskPoolName -ResourceGroupName $resourceGroupName
$existingLuns = $target.Lun
$luns = @()
foreach ($lun in $existingLuns)
{
$tmpLunName = $lun.Name
$tmpId = $lun.ManagedDiskAzureResourceId
$tmplun = New-AzDiskPoolIscsiLunObject -ManagedDiskAzureResourceId $tmpId -Name $tmpLunName
$luns += ,($tmplun)
}

$newlun = New-AzDiskPoolIscsiLunObject -ManagedDiskAzureResourceId $disk.Id -Name $lunName
$luns += ,($newlun)
Update-AzDiskPoolIscsiTarget -Name $iscsiTargetName -DiskPoolName $diskPoolName -ResourceGroupName $resourceGroupName -Lun $luns
```

## <a name="disable-iscsi-on-a-disk-and-remove-it-from-the-pool"></a>在磁盘上禁用 iSCSI，并将它从池中移除

在磁盘上禁用 iSCSI 支持之前，请确认磁盘公开为的 iSCSI LUN 没有未完成的 iSCSI 连接。 从磁盘池中移除磁盘时，不会自动将它删除。 这可以防止任何数据丢失，但仍会对存储数据计费。 如果不需要存储在磁盘中的数据，则可以手动删除该磁盘。 这会删除磁盘和其中存储的所有数据，并防止进一步的费用。

```azurepowershell
#Initialize input parameters
$resourceGroupName ="<yourResourceGroupName>"
$diskPoolName = "<yourDiskPoolName>"
$iscsiTargetName = "<youriSCSITargetName>"
$diskName ="<NameOfDiskYouWantToRemove>" #Provide the name of the disk you want to remove
$lunName ='<LUNForDiskYouWantToRemove>' #Provide the Lun name of the disk you want to remove
$diskIds = @()

#Get the existing iSCSI LUNs and remove it from iSCS target
$target = Get-AzDiskPoolIscsiTarget -name $iscsiTargetName -DiskPoolName $diskPoolName -ResourceGroupName $resourceGroupName
$existingLuns = $target.Lun
$luns = @()
foreach ($lun in $existingLuns)
{
if ($lun.Name -notlike $lunName)
{
$tmpLunName = $lun.Name
$tmpId = $lun.ManagedDiskAzureResourceId
$tmplun = New-AzDiskPoolIscsiLunObject -ManagedDiskAzureResourceId $tmpId -Name $tmpLunName
$luns += ,($tmplun)
}
}

Update-AzDiskPoolIscsiTarget -Name $iscsiTargetName -DiskPoolName $diskPoolName -ResourceGroupName $resourceGroupName -Lun $luns

#Remove the disk from disk pool
$disk = Get-AzDisk -ResourceGroupName $resourceGroupName -DiskName $diskName
$DiskPool = Get-AzDiskPool -Name $diskPoolName -ResourceGroupName $resourceGroupName
$DiskPoolDiskIDs = $DiskPool.Disk.Id
foreach ($Id in $DiskPoolDiskIDs)
{
if ($Id -notlike $disk.Id)
{
$diskIds += ($Id)
}
}

Update-AzDiskPool -ResourceGroupName $resourceGroupName -Name $diskPoolName -DiskId $diskIds
```

## <a name="next-steps"></a>后续步骤

- 若要了解如何将磁盘池移动到另一个订阅，请参阅[将磁盘池移动到不同的订阅](disks-pools-move-resource.md)。
- 若要了解如何取消设置磁盘池，请参阅[取消设置 Azure 磁盘池](disks-pools-deprovision.md)。