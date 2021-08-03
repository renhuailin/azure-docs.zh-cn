---
title: Azure 托管磁盘的冗余选项
description: 了解 Azure 托管磁盘的区域冗余存储和本地冗余存储。
author: roygara
ms.author: rogarana
ms.date: 05/26/2021
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions, devx-track-azurepowershell
ms.openlocfilehash: 5bb6d0b66e365904e7f0fe4f523f3c19a48d5361
ms.sourcegitcommit: df574710c692ba21b0467e3efeff9415d336a7e1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/28/2021
ms.locfileid: "110673424"
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

### <a name="billing-implications"></a>计费影响

有关详细信息，请参阅 [Azure 定价页](https://azure.microsoft.com/pricing/details/managed-disks/)。

### <a name="comparison-with-other-disk-types"></a>与其他磁盘类型的比较

除了写入延迟更长之外，使用 ZRS 的磁盘与使用 LRS 的磁盘完全相同。 它们的性能目标相同。 建议执行[磁盘基准测试](disks-benchmarks.md)来模拟应用程序的工作负载，以比较 LRS 和 ZRS 磁盘之间的延迟。 

### <a name="limitations"></a>限制

预览期间，托管磁盘的 ZRS 具有以下限制：

- 仅支持高级固态硬盘 (SSD) 和标准 SSD。
- 目前仅在美国西部 2、西欧、北欧和法国中部区域可用。
- 只能使用下列方法之一创建 ZRS 磁盘：
    -  Azure 资源管理器模板使用公共预览版 `2020-12-01` API。
    - 最新 Azure CLI


### <a name="create-zrs-managed-disks"></a>创建 ZRS 托管磁盘

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

#### <a name="prerequisites"></a>先决条件

必须为订阅启用该功能。 使用以下步骤为订阅启用该功能：

1.  执行以下命令，为订阅注册此功能

    ```azurecli
    az feature register --namespace Microsoft.Compute --name SsdZrsManagedDisks
    ```
 
2.  在试用该功能之前，请使用以下命令确认注册状态是否为“已注册”（可能需要几分钟）。

    ```azurecli
    az feature show --namespace Microsoft.Compute --name SsdZrsManagedDisks
    ```

#### <a name="create-a-vm-with-zrs-disks"></a>创建具有 ZRS 磁盘的 VM

```azurecli
rgName=yourRGName
vmName=yourVMName
location=westus2
vmSize=Standard_DS2_v2
image=UbuntuLTS 
osDiskSku=StandardSSD_ZRS
dataDiskSku=Premium_ZRS


az vm create -g $rgName \
-n $vmName \
-l $location \
--image $image \
--size $vmSize \
--generate-ssh-keys \
--data-disk-sizes-gb 128 \
--storage-sku os=$osDiskSku 0=$dataDiskSku
```
#### <a name="create-vms-with-a-shared-zrs-disk-attached-to-the-vms-in-different-zones"></a>创建其 ZRS 共享磁盘附加到了位于不同区域的虚拟机的 VM
```azurecli

location=westus2
rgName=yourRGName
vmNamePrefix=yourVMNamePrefix
vmSize=Standard_DS2_v2
image=UbuntuLTS
osDiskSku=StandardSSD_LRS
sharedDiskName=yourSharedDiskName
sharedDataDiskSku=Premium_ZRS


az disk create -g $rgName \
-n $sharedDiskName \
-l $location \
--size-gb 1024 \
--sku $sharedDataDiskSku \
--max-shares 2


sharedDiskId=$(az disk show -g $rgName -n $sharedDiskName --query 'id' -o tsv)

az vm create -g $rgName \
-n $vmNamePrefix"01" \
-l $location \
--image $image \
--size $vmSize \
--generate-ssh-keys \
--zone 1 \
--attach-data-disks $sharedDiskId \
--storage-sku os=$osDiskSku \
--vnet-name $vmNamePrefix"_vnet" \
--subnet $vmNamePrefix"_subnet"

az vm create -g $rgName \
-n $vmNamePrefix"02" \
-l $location \
--image $image \
--size $vmSize \
--generate-ssh-keys \
--zone 2 \
--attach-data-disks $sharedDiskId \
--storage-sku os=$osDiskSku \
--vnet-name $vmNamePrefix"_vnet" \
--subnet $vmNamePrefix"_subnet"

```
#### <a name="create-a-virtual-machine-scale-set-with-zrs-disks"></a>创建具有 ZRS 磁盘的虚拟机规模集
```azurecli
location=westus2
rgName=yourRGName
vmssName=yourVMSSName
vmSize=Standard_DS3_V2
image=UbuntuLTS 
osDiskSku=StandardSSD_ZRS
dataDiskSku=Premium_ZRS

az vmss create -g $rgName \
-n $vmssName \
--encryption-at-host \
--image UbuntuLTS \
--upgrade-policy automatic \
--generate-ssh-keys \
--data-disk-sizes-gb 128 \
--storage-sku os=$osDiskSku 0=$dataDiskSku
```

# <a name="resource-manager-template"></a>[资源管理器模板](#tab/azure-resource-manager)

搭配 `2020-12-01` API 使用 Azure 资源管理器模板来创建 ZRS 磁盘。

#### <a name="prerequisites"></a>先决条件

必须为订阅启用该功能。 使用以下步骤为订阅启用该功能：

1.  执行以下命令，为订阅注册此功能

    ```powershell
     Register-AzProviderFeature -FeatureName "SsdZrsManagedDisks" -ProviderNamespace "Microsoft.Compute" 
    ```

2.  在试用该功能之前，请使用以下命令确认注册状态是否为“已注册”（可能需要几分钟）。

    ```powershell
     Get-AzProviderFeature -FeatureName "SsdZrsManagedDisks" -ProviderNamespace "Microsoft.Compute"  
    ```
    
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
---

## <a name="next-steps"></a>后续步骤

- 查看更多[用于创建具有 ZRS 磁盘的 VM 的 Azure 资源管理器模板](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/ZRSDisks)。
