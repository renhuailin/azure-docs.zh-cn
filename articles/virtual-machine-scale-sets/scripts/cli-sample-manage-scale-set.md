---
title: 用于虚拟机规模集管理的 Azure CLI 示例
description: 此示例演示如何将磁盘添加到虚拟机规模集。 可以升级磁盘并将虚拟机添加到 Azure AD 身份验证。
author: mimckitt
ms.author: mimckitt
ms.date: 02/04/2021
ms.topic: sample
ms.service: virtual-machine-scale-sets
ms.devlang: azurecli
ms.custom: devx-track-azurecli
ms.openlocfilehash: 1abdf7ae15753d78ac8728f57e9b0cd5dcd9165e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "101672593"
---
# <a name="create-and-manage-virtual-machine-scale-set"></a>创建和管理虚拟机规模集

使用以下示例命令，通过使用 Azure CLI 对虚拟机规模集进行原型制作。

这些示例命令演示了以下操作：

* 创建虚拟机规模集。
* 将新磁盘或现有磁盘添加并升级到规模集或集的实例。
* 将规模集添加到 Azure Active Directory (Azure AD) 身份验证。

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="sample-commands"></a>示例命令

```azurecli
# Create a resource group
az group create --name MyResourceGroup --location eastus

# Create virtual machine scale set
az vmss create --resource-group MyResourceGroup --name myScaleSet --instance-count 2 \
    --image UbuntuLTS --upgrade-policy-mode automatic --admin-username azureuser \
    --generate-ssh-keys

# Attach a new managed disk to your scale set
az vmss disk attach --resource-group MyResourceGroup --vmss-name myScaleSet --size-gb 50
```

添加新数据磁盘后，格式化并装载该磁盘。 对于 Windows 虚拟机，请参阅[使用 Azure 门户将托管数据磁盘附加到 Windows VM](../../virtual-machines/windows/attach-managed-disk-portal.md)。 对于 Linux 虚拟机，请参阅[将磁盘添加到 Linux VM](../../virtual-machines/linux/add-disk.md)。

```azurecli
# Attach an existing managed disk to a virtual machine instance in your scale set
az vmss disk attach --resource-group MyResourceGroup --disk myDataDisk \
    --vmss-name myScaleSet --instance-id 0

# See the instances in your virtual machine scale set
az vmss list-instances --resource-group MyResourceGroup --name myScaleSet --output table

# See the disks for your virtual machine
az disk list --resource-group MyResourceGroup \
    --query "[*].{Name:name,Gb:diskSizeGb,Tier:accountType}" --output table

# Deallocate the virtual machine
az vmss deallocate --resource-group MyResourceGroup --name myScaleSet --instance-ids 0 

# Resize the disk
az disk update --resource-group MyResourceGroup --name myDataDisk --size-gb 200

# Restart the disk
az vmss restart --resource-group MyResourceGroup --name myScaleSet --instance-ids 0
```

若要使用扩展的磁盘，请扩展基础分区。 有关详细信息，请参阅[扩展磁盘分区和文件系统](../../virtual-machines/linux/expand-disks.md#expand-a-disk-partition-and-filesystem)。

此示例调整了数据磁盘的大小。 可以使用此相同的过程来更新 OS 磁盘。 有关 Windows 虚拟机的详细信息，请参阅[如何扩展虚拟机的 OS 驱动器](../../virtual-machines/windows/expand-os-disk.md)。 有关 Linux 虚拟机的详细信息，请参阅[使用 Azure CLI 扩展 Linux VM 上的虚拟硬盘](../../virtual-machines/linux/expand-disks.md)。

```azurecli
# Enable managed service identity on your scale set. This is required to authenticate and interact with other Azure services using bearer tokens.
az vmss identity assign --resource-group MyResourceGroup --name myScaleSet --role Owner \
    --scope /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyResourceGroup

# Connect to Azure AD authentication
az vmss extension set --resource-group MyResourceGroup --name AADLoginForWindows \
    --publisher Microsoft.Azure.ActiveDirectory --vmss-name myScaleSet

# Upgrade one instance of a scale set virtual machine
az vmss update-instances --resource-group MyResourceGroup --name myScaleSet --instance-ids 0 

# Remove a managed disk from the scale set
az vmss disk detach --resource-group MyResourceGroup --vmss-name myScaleSet --lun 0

# Remove a managed disk from an instance
az vmss disk detach --resource-group MyResourceGroup --vmss-name myScaleSet --instance-id 1 --lun 0

# Delete the pre-existing disk
az disk delete --resource-group MyResourceGroup --disk myDataDisk
```

## <a name="clean-up-resources"></a>清理资源

使用这些命令后，运行以下命令以删除资源组及其相关的所有资源。

```azurecli
az group delete --name MyResourceGroup
```

## <a name="azure-cli-references-used-in-this-article"></a>本文中使用的 Azure CLI 引用

* [az disk delete](/cli/azure/disk#az_disk_delete)
* [az disk list](/cli/azure/disk#az_disk_list)
* [az disk update](/cli/azure/disk#az_disk_update)
* [az group create](/cli/azure/group#az_group_create)
* [az vmss create](/cli/azure/vmss#az_vmss_create)
* [az virtual machine scale set deallocate](/cli/azure/vmss#az_vmss_deallocate)
* [az vmss disk attach](/cli/azure/vmss/disk#az_vmss_disk_attach)
* [az vmss disk detach](/cli/azure/vmss/disk#az_vmss_disk_detach)
* [az vmss extension set](/cli/azure/vmss/extension#az_vmss_extension_set)
* [az vmss identity assign](/cli/azure/vmss/identity#az_vmss_identity_assign)
* [az vmss list-instances](/cli/azure/vmss#az_vmss_list_instances)
* [az vmss restart](/cli/azure/vmss#az_vmss_restart)
* [az vmss update-instances](/cli/azure/vmss#az_vmss_update_instances)