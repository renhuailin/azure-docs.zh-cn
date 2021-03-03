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
ms.openlocfilehash: 6cafbff86a55ad0bed7da17fcef1aea2b0a53d1b
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101679145"
---
# <a name="redundancy-options-for-managed-disks"></a>托管磁盘的冗余选项

Azure 托管磁盘提供两种存储冗余选项、区域冗余存储 (ZRS) 作为预览和本地冗余存储。 ZRS 提供比本地冗余存储 (LRS) 的更高可用性。 但是，LRS 磁盘的写入延迟优于 ZRS 磁盘，因为 LRS 磁盘将数据同步写入单个数据中心内的三个副本。

## <a name="locally-redundant-storage-for-managed-disks"></a>托管磁盘的本地冗余存储

本地冗余存储 (LRS) 在选定区域的单个数据中心内将数据复制三次。 LRS 可以保护数据，使其不受服务器机架和驱动器故障影响。 

可以通过以下几种方法使用 LRS 磁盘来保护应用程序，这是由于自然灾害或硬件问题导致的整个区域故障：
- 使用 SQL Server AlwaysOn 这样的应用程序，该应用程序可以将数据同步写入两个区域，并在发生灾难时自动故障转移到另一个区域。
- 使用 ZRS 快照经常备份 LRS 磁盘。
- 通过 [Azure Site Recovery](../site-recovery/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery.md)为 LRS 磁盘启用跨区域灾难恢复。 但是，跨区域灾难恢复并不提供零恢复点目标 (RPO) 。

如果你的工作流不支持跨区域的应用程序级同步写入，或者你的应用程序必须满足 RPO，则 ZRS 磁盘将十分理想。

## <a name="zone-redundant-storage-for-managed-disks-preview"></a>用于托管磁盘的区域冗余存储 (预览) 

区域冗余存储 (ZRS) 将 Azure 托管磁盘同步复制到所选区域中的三个 Azure 可用性区域。 每个可用性区域都是一个独立的物理位置，具有独立的电源、冷却系统和网络。 

ZRS 磁盘使你可以从可用性区域中的故障中恢复。 如果整个区域关闭，可以将 ZRS 磁盘附加到另一个区域中的 VM。 你还可以将 ZRS 磁盘与共享磁盘结合使用，以提高群集或分布式应用程序（如 SQL FCI、SAP ASCS/SCS 或 GFS2）的可用性。 可以将共享的 ZRS 磁盘附加到不同区域中的主 Vm 和辅助 Vm，以充分利用 ZRS 和 [可用性区域](../availability-zones/az-overview.md)。 如果主要区域发生故障，可以使用 [SCSI 永久保留](disks-shared-enable.md#supported-scsi-pr-commands)快速故障转移到辅助 VM。

### <a name="limitations"></a>限制

预览期间，托管磁盘的 ZRS 具有以下限制：

- 仅支持高级固态驱动器 (SSD) 和标准 Ssd。
- 当前仅在 EastUS2EUAP 区域中可用。
- 只能使用 API 通过 Azure 资源管理器模板创建 ZRS 磁盘 `2020-12-01` 。

在 [此处](https://aka.ms/ZRSDisksPreviewSignUp)注册预览。

### <a name="billing-implications"></a>计费影响

有关详细信息，请参阅 [Azure 定价页](https://azure.microsoft.com/pricing/details/managed-disks/)。

### <a name="comparison-with-other-disk-types"></a>与其他磁盘类型的比较

除了更多写入延迟，使用 ZRS 的磁盘与使用 LRS 的磁盘相同。 它们具有相同的性能目标。

### <a name="create-zrs-managed-disks"></a>创建 ZRS 托管磁盘

需要 `2020-12-01` 通过 Azure 资源管理器模板使用 API 创建 ZRS 磁盘。

#### <a name="create-a-vm-with-zrs-disks"></a>使用 ZRS 磁盘创建 VM

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

#### <a name="create-vms-with-a-shared-zrs-disk-attached-to-the-vms-in-different-zones"></a>使用连接到不同区域中的 Vm 的共享 ZRS 磁盘创建 Vm

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

#### <a name="create-a-virtual-machine-scale-set-with-zrs-disks"></a>使用 ZRS 磁盘创建虚拟机规模集

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
