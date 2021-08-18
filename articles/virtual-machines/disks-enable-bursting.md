---
title: 启用按需磁盘突发
description: 启用托管磁盘上的按需磁盘突发。
author: roygara
ms.author: rogarana
ms.date: 06/29/2021
ms.topic: conceptual
ms.service: storage
ms.subservice: disks
ms.custom: references_regions, devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 1546ac2317f66bc0e09f674b98b129bd4f623d27
ms.sourcegitcommit: 82d82642daa5c452a39c3b3d57cd849c06df21b0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2021
ms.locfileid: "113358541"
---
# <a name="enable-on-demand-bursting"></a>启用按需突发

高级固态硬盘 (SSD) 有两个突发模型；基于信用的突发和按需突发。 本文介绍可如何切换到按需突发。 使用按需模型的磁盘可能会超出其原始预配目标。 按需突发会按工作负荷所需的频率发生，直到达到最大的突发目标。 按需突发会产生额外的费用。

有关磁盘突发的详细信息，请参阅“[托管磁盘突发](disk-bursting.md)”。

> [!IMPORTANT]
> 如果使用基于信用的突发，则无需按照本文中的步骤进行操作。 默认情况下，在所有合格磁盘上启用基于信用的突发。

启用按需突发之前，请先了解以下内容：

[!INCLUDE [managed-disk-bursting-regions-limitations](../../includes/managed-disk-bursting-regions-limitations.md)]

### <a name="regional-availability"></a>区域可用性

[!INCLUDE [managed-disk-bursting-availability](../../includes/managed-disk-bursting-availability.md)]

## <a name="get-started"></a>入门

可以通过 Azure PowerShell 模块、Azure CLI 或 Azure Resource Manager 模板启用按需突发。 以下示例介绍了如何在启用按需突发功能的情况下创建新磁盘，并在现有磁盘上启用按需突发。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

在 5.5.0 版及更新版本的 Az 模块中提供了按需突发 cmdlet。 或者，可以使用 [Azure Cloud Shell](https://shell.azure.com/)。
### <a name="create-an-empty-data-disk-with-on-demand-bursting"></a>创建具有按需突发的空数据磁盘

托管磁盘必须大于 512 GiB，才能启用按需突发。 替换参数 `<myResourceGroupDisk>` 和 `<myDataDisk>`，然后运行以下脚本，以创建具有按需突发的高级 SSD：

```azurepowershell
Set-AzContext -SubscriptionName <yourSubscriptionName>

$diskConfig = New-AzDiskConfig -Location 'WestCentralUS' -CreateOption Empty -DiskSizeGB 1024 -SkuName Premium_LRS -BurstingEnabled $true

$dataDisk = New-AzDisk -ResourceGroupName <myResourceGroupDisk> -DiskName <myDataDisk> -Disk $diskConfig
```

### <a name="enable-on-demand-bursting-on-an-existing-disk"></a>启用现有磁盘上的按需突发

托管磁盘必须大于 512 GiB，才能启用按需突发。 替换参数 `<myResourceGroupDisk>` 和 `<myDataDisk>`，并运行以下命令，在现有磁盘上启用按需突发操作：

```azurepowershell
New-AzDiskUpdateConfig -BurstingEnabled $true | Update-AzDisk -ResourceGroupName <myResourceGroupDisk> -DiskName <myDataDisk> //Set the flag to $false to disable on-demand bursting
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

在 2.19.0 版及更新版本的“[Azure CLI 模块](/cli/azure/install-azure-cli)”中提供了按需突发 cmdlet。 或者，可以使用 [Azure Cloud Shell](https://shell.azure.com/)。

### <a name="create-and-attach-a-on-demand-bursting-data-disk"></a>创建并附加按需突发数据磁盘

托管磁盘必须大于 512 GiB，才能启用按需突发。 替换参数 `<yourDiskName>`、`<yourResourceGroup>` 和 `<yourVMName>`，然后运行以下命令，以创建具有按需突发的高级 SSD：

```azurecli
az disk create -g <yourResourceGroup> -n <yourDiskName> --size-gb 1024 --sku Premium_LRS -l westcentralus --enable-bursting true

az vm disk attach --vm-name <yourVMName> --name <yourDiskName> --resource-group <yourResourceGroup>
```

### <a name="enable-on-demand-bursting-on-an-existing-disk---cli"></a>启用现有磁盘上的按需突发 - CLI

托管磁盘必须大于 512 GiB，才能启用按需突发。 替换参数 `<myResourceGroupDisk>` 和 `<yourDiskName>`，并运行以下命令，在现有磁盘上启用按需突发操作：

```azurecli
az disk update --name <yourDiskName> --resource-group <yourResourceGroup> --enable-bursting true //Set the flag to false to disable on-demand bursting
```

# <a name="azure-resource-manager"></a>[Azure 资源管理器](#tab/azure-resource-manager)

使用`2020-09-30`磁盘 API，可以在新创建或现有的高级 SSD 大于 512 GiB 时启用按需突发。 `2020-09-30`API 引入了新的属性`burstingEnabled` 。 默认情况下，该属性设置为 false。 下面的示例模板在 West Central US 中创建了 1TiB 高级 SSD，且启用了磁盘突发：

```
{
  "$schema": "http://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "diskSkuName": {
        "type": "string",
        "defaultValue": "Premium_LRS" //Supported on premium SSDs only
},
    "dataDiskSizeInGb": {
      "type": "string",
      "defaultValue": "1024" //Supported on disk size > 512 GiB
},
    "location": {
      "type": "string",
      "defaultValue": "westcentralus" //Preview regions: West Central US
},
"diskApiVersion": {
      "type": "string",
      "defaultValue": "2020-09-30" //Preview supported version: 2020-09-30 or above
    }
  },
  "resources": [
    {
      "apiVersion": "[parameters('diskApiVersion')]",
      "type": "Microsoft.Compute/disks",
      "name": "[parameters('diskName')]",
      "location": "[parameters(location)]",
      "properties": {
        "creationData": {
          "createOption": "Empty"
        },
        "diskSizeGB": "[parameters('dataDiskSizeInGb')]",
        "burstingEnabled": "true" //Feature flag to enable disk bursting on disks > 512 GiB
      },
      "sku": {
        "name": "[parameters('diskSkuName')]"
      }
    ]
}
```
---
 
## <a name="next-steps"></a>后续步骤

如需了解如何深入了解突发资源，请参阅“[磁盘突发指标](disks-metrics.md)”。
