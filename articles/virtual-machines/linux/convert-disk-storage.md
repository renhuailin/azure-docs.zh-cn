---
title: 使用 Azure CLI 在不同的磁盘类型之间转换托管磁盘存储
description: 如何使用 Azure CLI 在不同磁盘类型之间转换 Azure 托管磁盘。
author: roygara
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.date: 02/13/2021
ms.author: albecker
ms.subservice: disks
ms.openlocfilehash: 385d801d43a41bb836e04398427fff6c01b1e357
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122692127"
---
# <a name="convert-azure-managed-disks-storage-from-standard-to-premium-or-premium-to-standard"></a>将 Azure 托管磁盘存储从标准类型转换为高级类型，或者从高级类型转换为标准类型

**适用于：** :heavy_check_mark: Linux VM :heavy_check_mark: 灵活规模集 

Azure 托管磁盘有四种磁盘类型：Azure 超级磁盘、高级 SSD、标准 SSD 和标准 HDD。 可以根据性能需求在高级 SSD、标准 SSD 和标准 HDD 之间进行切换。 尚无法与超级磁盘进行切换，必须部署一个新磁盘。

非托管磁盘不支持此功能。 但是，可以轻松[将非托管磁盘转换为托管磁盘](convert-unmanaged-to-managed-disks.md)，然后即可切换磁盘类型。

本文介绍了如何使用 Azure CLI 将托管磁盘从一种磁盘类型转换为另一种类型。 若要安装或升级 CLI 工具，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

## <a name="before-you-begin"></a>准备阶段

* 磁盘转换需要重启虚拟机 (VM)，因此请在预先存在的维护时段内计划磁盘存储迁移。
* 对于非托管磁盘，请先[转换为托管磁盘](convert-unmanaged-to-managed-disks.md)，以便可以在存储选项之间切换。


## <a name="switch-all-managed-disks-of-a-vm-between-from-one-account-to-another"></a>将 VM 的所有托管磁盘从一个帐户切换到另一个帐户

此示例展示了如何将 VM 的所有磁盘转换为高级存储。 不过，通过在此示例中更改 SKU 变量，可以将 VM 的磁盘类型转换为标准 SSD 或标准 HDD。 请注意，若要使用高级托管磁盘，VM 必须使用支持高级存储的 [VM 大小](../sizes.md)。 此示例还会切换到支持高级存储的大小。

 ```azurecli

#resource group that contains the virtual machine
rgName='yourResourceGroup'

#Name of the virtual machine
vmName='yourVM'

#Premium capable size 
#Required only if converting from Standard to Premium
size='Standard_DS2_v2'

#Choose between Standard_LRS, StandardSSD_LRS and Premium_LRS based on your scenario
sku='Premium_LRS'

#Deallocate the VM before changing the size of the VM
az vm deallocate --name $vmName --resource-group $rgName

#Change the VM size to a size that supports Premium storage 
#Skip this step if converting storage from Premium to Standard
az vm resize --resource-group $rgName --name $vmName --size $size

#Update the SKU of all the data disks 
az vm show -n $vmName -g $rgName --query storageProfile.dataDisks[*].managedDisk -o tsv \
 | awk -v sku=$sku '{system("az disk update --sku "sku" --ids "$1)}'

#Update the SKU of the OS disk
az vm show -n $vmName -g $rgName --query storageProfile.osDisk.managedDisk -o tsv \
| awk -v sku=$sku '{system("az disk update --sku "sku" --ids "$1)}'

az vm start --name $vmName --resource-group $rgName

```
## <a name="switch-individual-managed-disks-from-one-disk-type-to-another"></a>将各个托管磁盘从一种磁盘类型切换为另一种磁盘类型

对于开发/测试工作负荷，可以混合使用标准磁盘和高级磁盘来降低成本。 可以选择仅升级需要更高性能的磁盘。 此示例展示了如何将单个 VM 磁盘从标准存储转换为高级存储。 不过，通过在此示例中更改 SKU 变量，可以将 VM 的磁盘类型转换为标准 SSD 或标准 HDD。 若要使用高级托管磁盘，VM 必须使用支持高级存储的 [VM 大小](../sizes.md)。 此示例还会切换到支持高级存储的大小。

 ```azurecli

#resource group that contains the managed disk
rgName='yourResourceGroup'

#Name of your managed disk
diskName='yourManagedDiskName'

#Premium capable size 
#Required only if converting from Standard to Premium
size='Standard_DS2_v2'

#Choose between Standard_LRS, StandardSSD_LRS and Premium_LRS based on your scenario
sku='Premium_LRS'

#Get the parent VM Id 
vmId=$(az disk show --name $diskName --resource-group $rgName --query managedBy --output tsv)

#Deallocate the VM before changing the size of the VM
az vm deallocate --ids $vmId 

#Change the VM size to a size that supports Premium storage 
#Skip this step if converting storage from Premium to Standard
az vm resize --ids $vmId --size $size

# Update the SKU
az disk update --sku $sku --name $diskName --resource-group $rgName 

az vm start --ids $vmId 
```

## <a name="switch-managed-disks-from-one-disk-type-to-another"></a>将托管磁盘从一种磁盘类型切换为另一种磁盘类型

执行以下步骤：

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 在“虚拟机”列表中选择 VM。
3. 如果该 VM 未停止，请在 VM“概述”窗格的顶部选择“停止”，然后等待该 VM 停止。 
4. 在 VM 对应的窗格中，从菜单中选择“磁盘”。
5. 选择要转换的磁盘。
6. 在菜单中选择“配置”。
7. 将“帐户类型”从原始磁盘类型更改为所需的磁盘类型。
8. 选择“保存”并关闭磁盘窗格。

磁盘类型会即时更新。 转换后，可以重启 VM。

## <a name="next-steps"></a>后续步骤

使用[快照](snapshot-copy-managed-disk.md)创建 VM 的只读副本。