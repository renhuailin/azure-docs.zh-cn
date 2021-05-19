---
title: Azure 托管磁盘的冗余选项
description: 了解 Azure 托管磁盘的区域冗余存储和本地冗余存储。
author: roygara
ms.author: rogarana
ms.date: 03/02/2021
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: f0f3baf1bf56f958408f789961812c0555f289f1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "102043637"
---
# <a name="redundancy-options-for-managed-disks"></a>托管磁盘的冗余选项

Azure 托管磁盘提供两个存储冗余选项：本地冗余存储和预览版的区域冗余存储 (ZRS)。 ZRS 相比本地冗余存储 (LRS) 可为托管磁盘提供更高的可用性。 但 LRS 磁盘的写入延迟短于 ZRS 磁盘，因为 LRS 磁盘将数据同步写入单个数据中心内的三个副本中。

## <a name="locally-redundant-storage-for-managed-disks"></a>托管磁盘的本地冗余存储

本地冗余存储 (LRS) 在所选区域中的单个数据中心内复制数据三次。 LRS 可以保护数据，使其不受服务器机架和驱动器故障影响。 

你可通过以下几种方法使用 LRS 磁盘来保护应用程序遭受自然灾害或硬件问题所致的整个区域故障：
- 使用诸如 SQL Server AlwaysOn 之类的应用程序，它可将数据同步写入两个区域，并在发生灾难时自动故障转移到另一个区域。
- 使用 ZRS 快照频繁备份 LRS 磁盘。
- 通过 [Azure Site Recovery](../site-recovery/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery.md) 为 LRS 磁盘启用跨区域灾难恢复。 但是，跨区域灾难恢复并不提供零恢复点目标 (RPO)。

如果工作流不支持跨区域的应用程序级同步写入，或者应用程序必须满足零 RPO，则 ZRS 磁盘将是理想之选。

## <a name="zone-redundant-storage-for-managed-disks-preview"></a>托管磁盘的区域冗余存储（预览版）

区域冗余存储 (ZRS) 在所选区域中的三个 Azure 可用性区域同步复制 Azure 托管磁盘。 每个可用性区域都是一个独立的物理位置，具有独立的电源、冷却系统和网络。 

ZRS 磁盘使你可以在可用性区域从故障中恢复。 如果整个区域出现故障，可将 ZRS 磁盘附加到另一个区域的 VM。 还可将 ZRS 磁盘用作共享磁盘，为群集或分散式应用程序（如 SQL FCI、SAP ASCS/SCS 或 GFS2）提供更高的可用性。 你可将 ZRS 共享磁盘附加到不同区域中的主 VM 和辅助 VM，充分利用 ZRS 和[可用性区域](../availability-zones/az-overview.md)。 如果主区域发生故障，就可使用 [SCSI 永久预留](disks-shared-enable.md#supported-scsi-pr-commands)功能快速故障转移到辅助 VM。

### <a name="limitations"></a>限制

预览期间，托管磁盘的 ZRS 具有以下限制：

- 仅支持高级固态硬盘 (SSD) 和标准 SSD。
- 当前仅在 EastUS2EUAP 区域中可用。
- 只能使用 `2020-12-01` API 通过 Azure 资源管理器模板创建 ZRS 磁盘。

请在[此处](https://aka.ms/ZRSDisksPreviewSignUp)注册预览版。

### <a name="billing-implications"></a>计费影响

有关详细信息，请参阅 [Azure 定价页](https://azure.microsoft.com/pricing/details/managed-disks/)。

### <a name="comparison-with-other-disk-types"></a>与其他磁盘类型的比较

除了写入延迟更长之外，使用 ZRS 的磁盘与使用 LRS 的磁盘完全相同。 它们的性能目标相同。

### <a name="create-zrs-managed-disks"></a>创建 ZRS 托管磁盘

搭配 `2020-12-01` API 使用 Azure 资源管理器模板来创建 ZRS 磁盘。

#### <a name="create-a-vm-with-zrs-disks"></a>创建具有 ZRS 磁盘的 VM

```
$vmName = "yourVMName" 
$adminUsername = "yourAdminUsername"
$adminPassword = ConvertTo-SecureString "yourAdminPassword" -AsPlainText -Force
$osDiskType = "StandardSSD_ZRS"
$dataDiskType = "Premium_ZRS"
$region = "eastus2euap"
$resourceGroupName = "yourResourceGroupName"

New-AzResourceGroup -Name $resourceGroupName -Location $region
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
-TemplateUri "https://raw.githubusercontent.com/Azure-Samples/managed-disks-powershell-getting-started/master/ZRSDisks/CreateVMWithZRSDataDisks.json" `
-resourceName $vmName `
-adminUsername $adminUsername `
-adminPassword $adminPassword `
-region $region `
-osDiskType $osDiskType `
-dataDiskType $dataDiskType
```

#### <a name="create-vms-with-a-shared-zrs-disk-attached-to-the-vms-in-different-zones"></a>创建其 ZRS 共享磁盘附加到了位于不同区域的虚拟机的 VM

```
$vmNamePrefix = "yourVMNamePrefix"
$adminUsername = "yourAdminUserName"
$adminPassword = ConvertTo-SecureString "yourAdminPassword" -AsPlainText -Force
$osDiskType = "StandardSSD_LRS"
$sharedDataDiskType = "Premium_ZRS"
$region = "eastus2euap"
$resourceGroupName = "zrstesting1"

New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
-TemplateUri "https://raw.githubusercontent.com/Azure-Samples/managed-disks-powershell-getting-started/master/ZRSDisks/CreateVMsWithASharedDisk.json" `
-vmNamePrefix $vmNamePrefix `
-adminUsername $adminUsername `
-adminPassword $adminPassword `
-region $region `
-osDiskType $osDiskType `
-dataDiskType $sharedDataDiskType
```

#### <a name="create-a-virtual-machine-scale-set-with-zrs-disks"></a>创建具有 ZRS 磁盘的虚拟机规模集

```
$vmssName="yourVMSSName"
$adminUsername="yourAdminName"
$adminPassword=ConvertTo-SecureString "yourAdminPassword" -AsPlainText -Force
$region="eastus2euap"
$osDiskType="StandardSSD_LRS"
$dataDiskType="Premium_ZRS"

New-AzResourceGroupDeployment -ResourceGroupName zrstesting `
-TemplateUri "https://raw.githubusercontent.com/Azure-Samples/managed-disks-powershell-getting-started/master/ZRSDisks/CreateVMSSWithZRSDisks.json" `
-vmssName "yourVMSSName" `
-adminUsername "yourAdminName" `
-adminPassword $password `
-region "eastus2euap" `
-osDiskType "StandardSSD_LRS" `
-dataDiskType "Premium_ZRS" `
```

## <a name="next-steps"></a>后续步骤

- 请使用这些示例 [Azure 资源管理器模板创建具有 ZRS 磁盘的 VM](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/ZRSDisks)。