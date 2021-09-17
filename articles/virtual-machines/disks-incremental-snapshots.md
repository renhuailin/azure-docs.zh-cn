---
title: 创建增量快照
description: 了解托管磁盘的增量快照，包括如何使用 Azure 门户、Azure PowerShell 模块和 Azure 资源管理器创建增量快照。
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 08/10/2021
ms.author: rogarana
ms.subservice: disks
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 8f00a0f69bf00c42ef120250cbc4a770fb1b7a09
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122689414"
---
# <a name="create-an-incremental-snapshot-for-managed-disks"></a>为托管磁盘创建增量快照

**适用于：** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 灵活规模集 :heavy_check_mark: 统一规模集

[!INCLUDE [virtual-machines-disks-incremental-snapshots-description](../../includes/virtual-machines-disks-incremental-snapshots-description.md)]

## <a name="restrictions"></a>限制

[!INCLUDE [virtual-machines-disks-incremental-snapshots-restrictions](../../includes/virtual-machines-disks-incremental-snapshots-restrictions.md)]

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

可以使用 Azure CLI 创建增量快照。 需要最新版本的 Azure CLI。 请参阅以下文章，了解如何[安装](/cli/azure/install-azure-cli)或[更新](/cli/azure/update-azure-cli) Azure CLI。

以下脚本将创建特定磁盘的增量快照：

```azurecli
# Declare variables
diskName="yourDiskNameHere"
resourceGroupName="yourResourceGroupNameHere"
snapshotName="desiredSnapshotNameHere"

# Get the disk you need to backup
yourDiskID=$(az disk show -n $diskName -g $resourceGroupName --query "id" --output tsv)

# Create the snapshot
az snapshot create -g $resourceGroupName -n $snapshotName --source $yourDiskID --incremental true
```

可以使用快照的 `SourceResourceId` 属性识别同一磁盘中的增量快照。 `SourceResourceId` 是父磁盘的 Azure 资源管理器资源 ID。

可以使用 `SourceResourceId` 创建一个与特定磁盘关联的所有快照的列表。 将 `yourResourceGroupNameHere` 替换为你的值，然后可以使用以下示例列出现有增量快照：


```azurecli
# Declare variables and create snapshot list
subscriptionId="yourSubscriptionId"
resourceGroupName="yourResourceGroupNameHere"
diskName="yourDiskNameHere"

az account set --subscription $subscriptionId

diskId=$(az disk show -n $diskName -g $resourceGroupName --query [id] -o tsv)

az snapshot list --query "[?creationData.sourceResourceId=='$diskId' && incremental]" -g $resourceGroupName --output table
```


# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

可以使用 Azure PowerShell 模块创建增量快照。 需要最新版本的 Azure PowerShell 模块。 以下命令将安装最新版本或将现有安装更新到最新版本：

```PowerShell
Install-Module -Name Az -AllowClobber -Scope CurrentUser
```

安装之后，使用 `Connect-AzAccount` 登录到 PowerShell 会话。

若要使用 Azure PowerShell 创建增量快照，请使用含有 `-Incremental` 参数的 [New-AzSnapShotConfig](/powershell/module/az.compute/new-azsnapshotconfig) 设置配置，然后将其作为变量通过 `-Snapshot` 参数传递给 [New-AzSnapshot](/powershell/module/az.compute/new-azsnapshot)。

```PowerShell
$diskName = "yourDiskNameHere>"
$resourceGroupName = "yourResourceGroupNameHere"
$snapshotName = "yourDesiredSnapshotNameHere"

# Get the disk that you need to backup by creating an incremental snapshot
$yourDisk = Get-AzDisk -DiskName $diskName -ResourceGroupName $resourceGroupName

# Create an incremental snapshot by setting the SourceUri property with the value of the Id property of the disk
$snapshotConfig=New-AzSnapshotConfig -SourceUri $yourDisk.Id -Location $yourDisk.Location -CreateOption Copy -Incremental 
New-AzSnapshot -ResourceGroupName $resourceGroupName -SnapshotName $snapshotName -Snapshot $snapshotConfig 
```

可以使用快照的 `SourceResourceId` 和 `SourceUniqueId` 属性标识同一磁盘中的增量快照。 `SourceResourceId` 是父磁盘的 Azure 资源管理器资源 ID。 `SourceUniqueId` 是从磁盘的 `UniqueId` 属性继承的值。 如果要删除某个磁盘，然后创建具有相同名称的新磁盘，则 `UniqueId` 属性的值将更改。

可以使用 `SourceResourceId` 和 `SourceUniqueId` 来创建与特定磁盘关联的所有快照的列表。 将 `yourResourceGroupNameHere` 替换为你的值，然后可以使用以下示例列出现有增量快照：

```PowerShell
$resourceGroupName = "yourResourceGroupNameHere"
$snapshots = Get-AzSnapshot -ResourceGroupName $resourceGroupName

$incrementalSnapshots = New-Object System.Collections.ArrayList
foreach ($snapshot in $snapshots)
{
    
    if($snapshot.Incremental -and $snapshot.CreationData.SourceResourceId -eq $yourDisk.Id -and $snapshot.CreationData.SourceUniqueId -eq $yourDisk.UniqueId){

        $incrementalSnapshots.Add($snapshot)
    }
}

$incrementalSnapshots
```

# <a name="portal"></a>[门户](#tab/azure-portal)
[!INCLUDE [virtual-machines-disks-incremental-snapshots-portal](../../includes/virtual-machines-disks-incremental-snapshots-portal.md)]

# <a name="resource-manager-template"></a>[资源管理器模板](#tab/azure-resource-manager)

还可以使用 Azure 资源管理器模板创建增量快照。 需要确保 apiVersion 设置为 2019-03-01 且增量属性也设置为 true。 以下代码片段是使用资源管理器模板创建增量快照的示例：

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "diskName": {
      "type": "string",
      "defaultValue": "contosodisk1"
    },
  "diskResourceId": {
    "defaultValue": "<your_managed_disk_resource_ID>",
    "type": "String"
  }
  }, 
  "resources": [
  {
    "type": "Microsoft.Compute/snapshots",
    "name": "[concat( parameters('diskName'),'_snapshot1')]",
    "location": "[resourceGroup().location]",
    "apiVersion": "2019-03-01",
    "properties": {
      "creationData": {
        "createOption": "Copy",
        "sourceResourceId": "[parameters('diskResourceId')]"
      },
      "incremental": true
    }
  }
  ]
}
```
---

## <a name="next-steps"></a>后续步骤

要查看演示使用 .NET 的增量快照的差异功能的示例代码，请参阅[通过增量快照差异功能将 Azure 托管磁盘备份复制到另一区域](https://github.com/Azure-Samples/managed-disks-dotnet-backup-with-incremental-snapshots)。
