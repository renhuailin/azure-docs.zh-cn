---
title: 将 Windows 虚拟机从非托管磁盘转换为托管磁盘
description: 如何在资源管理器部署模型中使用 PowerShell 将 Windows VM 从非托管磁盘转换为托管磁盘
author: roygara
ms.service: virtual-machines
ms.subservice: disks
ms.topic: how-to
ms.date: 07/12/2018
ms.author: rogarana
ms.openlocfilehash: 7b1c270f83565e2968e6677f07b81968dcf61ac7
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121734583"
---
# <a name="convert-a-windows-virtual-machine-from-unmanaged-disks-to-managed-disks"></a>将 Windows 虚拟机从非托管磁盘转换为托管磁盘

如果有使用非托管磁盘的现有 Windows 虚拟机 (VM)，可通过 [Azure 托管磁盘](../managed-disks-overview.md)服务将 VM 转换为使用托管磁盘。 此过程同时转换操作系统 (OS) 磁盘和任何附加的数据磁盘。


 

## <a name="before-you-begin"></a>准备阶段


* 请查看[计划迁移到托管磁盘](on-prem-to-azure.md#plan-for-the-migration-to-managed-disks)。

* 请查看[有关迁移到托管磁盘的常见问题](/azure/virtual-machines/faq-for-disks#migrate-to-managed-disks)。

[!INCLUDE [virtual-machines-common-convert-disks-considerations](../../../includes/virtual-machines-common-convert-disks-considerations.md)]

* 不会删除在转换之前由 VM 使用的原始 VHD 和存储帐户。 它们会继续产生费用。 若要避免这些项目产生的费用，请在验证转换已完成后删除原始 VHD Blob。 如果需要找到这些未附加的磁盘以删除它们，请参阅我们的文章[查找并删除未附加的 Azure 托管和非托管磁盘](find-unattached-disks.md)。


## <a name="convert-single-instance-vms"></a>转换单实例 VM
本节介绍如何将单实例 Azure VM 从非托管磁盘转换为托管磁盘。 （如果 VM 位于可用性集中，请参阅下一部分。） 

1. 使用 [Stop-AzVM](/powershell/module/az.compute/stop-azvm) cmdlet 解除分配 VM。 以下示例在名为 `myResourceGroup` 的资源组中解除分配名为 `myVM` 的 VM： 

   ```azurepowershell-interactive
   $rgName = "myResourceGroup"
   $vmName = "myVM"
   Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force
   ```

2. 使用 [ConvertTo-AzVMManagedDisk](/powershell/module/az.compute/convertto-azvmmanageddisk) cmdlet 将 VM 转换为托管磁盘。 以下过程转换之前的 VM，包括 OS 磁盘和任何数据磁盘，并启用虚拟机：

   ```azurepowershell-interactive
   ConvertTo-AzVMManagedDisk -ResourceGroupName $rgName -VMName $vmName
   ```



## <a name="convert-vms-in-an-availability-set"></a>在可用性集中转换 VM

如果要转换为托管磁盘的 VM 位于可用性集中，则需要先将可用性集转换为托管可用性集。

1. 使用 [Update-AzAvailabilitySet](/powershell/module/az.compute/update-azavailabilityset) cmdlet 转换可用性集。 以下示例在名为 `myResourceGroup` 的资源组中更新名为 `myAvailabilitySet` 的可用性集：

   ```azurepowershell-interactive
   $rgName = 'myResourceGroup'
   $avSetName = 'myAvailabilitySet'

   $avSet = Get-AzAvailabilitySet -ResourceGroupName $rgName -Name $avSetName
   Update-AzAvailabilitySet -AvailabilitySet $avSet -Sku Aligned 
   ```

   如果可用性集所在的区域只有 2 个托管容错域，但却有 3 个非托管容错域，则此命令会显示类似于“指定的容错域计数 3 必须在 1 到 2 这个范围内”的错误消息。 若要解决此错误，请将容错域更新为 2，并按如下所示将 `Sku` 更新为 `Aligned`：

   ```azurepowershell-interactive
   $avSet.PlatformFaultDomainCount = 2
   Update-AzAvailabilitySet -AvailabilitySet $avSet -Sku Aligned
   ```

2. 解除分配 VM，并转换可用性集中的 VM。 以下脚本使用 [Stop-AzVM](/powershell/module/az.compute/stop-azvm) cmdlet 解除分配每个 VM，使用 [ConvertTo-AzVMManagedDisk](/powershell/module/az.compute/convertto-azvmmanageddisk) 进行转换，并在转换进程结束后自动将其重启：

   ```azurepowershell-interactive
   $avSet = Get-AzAvailabilitySet -ResourceGroupName $rgName -Name $avSetName

   foreach($vmInfo in $avSet.VirtualMachinesReferences)
   {
     $vm = Get-AzVM -ResourceGroupName $rgName | Where-Object {$_.Id -eq $vmInfo.id}
     Stop-AzVM -ResourceGroupName $rgName -Name $vm.Name -Force
     ConvertTo-AzVMManagedDisk -ResourceGroupName $rgName -VMName $vm.Name
   }
   ```


## <a name="troubleshooting"></a>故障排除

- 在转换之前，确保所有 VM 扩展都处于“配置成功”状态，否则转换将失败，并出现错误代码 409。
- 如果转换过程中出现错误，或先前转换中的问题导致 VM 处于“失败”状态，请再次运行 `ConvertTo-AzVMManagedDisk` cmdlet。 简单的重试通常可以解决这种情况。
- 如果要将 Linux VM 转换为托管磁盘，请使用最新版本的 Azure Linux 代理。 使用 Azure Linux 代理 2.2.0 及更低版本的操作可能会失败。 也不支持在通用 VM 或属于经典可用性集的 VM 上运行转换。
- 如果转换失败并出现“SnapshotCountExceeded”错误，请删除一些快照并重试该操作。


## <a name="convert-using-the-azure-portal"></a>使用 Azure 门户进行转换

还可以使用 Azure 门户将非托管磁盘转换为托管磁盘。

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 从门户的 VM 列表中选择 VM。
3. 在 VM 的边栏选项卡中，从菜单中选择“磁盘”。
4. 在“磁盘”边栏选项卡的顶部，选择“迁移到托管磁盘”。
5. 如果 VM 位于可用性集中，则“迁移到托管磁盘”边栏选项卡上会出现“首先需要转换可用性集”的警告。 此警告应该有一个链接，单击该链接即可转换可用性集。 转换可用性集后，或者如果 VM 不在可用性集中，请单击“迁移”以启动将磁盘迁移到托管磁盘的过程。

VM 将会停止并在完成迁移后重新启动。

## <a name="next-steps"></a>后续步骤

[将标准托管磁盘转换为高级托管磁盘](convert-disk-storage.md)

使用[快照](snapshot-copy-managed-disk.md)获取 VM 的只读副本。
