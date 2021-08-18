---
title: 启用软删除 - Azure 文件共享
description: 了解如何在 Azure 文件共享上启用软删除，以进行数据恢复和防止意外删除。
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 04/05/2021
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurepowershell
services: storage
ms.openlocfilehash: 314d1ee8ee957b21679735594ad2f7d7f50f4d38
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/15/2021
ms.locfileid: "112118314"
---
# <a name="enable-soft-delete-on-azure-file-shares"></a>在 Azure 文件共享上启用软删除
Azure 文件存储为文件共享提供软删除，方便你在应用程序或其他存储帐户用户错误地删除数据后更轻松地恢复数据。 若要了解有关软删除的详细信息，请参阅[如何防止意外删除 Azure 文件共享](storage-files-prevent-file-share-deletion.md)。

## <a name="applies-to"></a>适用于
| 文件共享类型 | SMB | NFS |
|-|:-:|:-:|
| 标准文件共享 (GPv2)、LRS/ZRS | ![是](../media/icons/yes-icon.png) | ![否](../media/icons/no-icon.png) |
| 标准文件共享 (GPv2)、GRS/GZRS | ![是](../media/icons/yes-icon.png) | ![否](../media/icons/no-icon.png) |
| 高级文件共享 (FileStorage)、LRS/ZRS | ![是](../media/icons/yes-icon.png) | ![否](../media/icons/no-icon.png) |

## <a name="prerequisites"></a>先决条件
- 如果你打算使用 Azure PowerShell，请[安装最新版本](/powershell/azure/install-az-ps)。
- 如果你打算使用 Azure CLI，请[安装最新版本](/cli/azure/install-azure-cli)。

## <a name="getting-started"></a>入门
以下部分说明如何在现有存储帐户上为 Azure 文件共享启用和使用软删除：

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. 登录到 [Azure 门户](https://portal.azure.com/)。
1. 导航到存储帐户，然后在“数据存储”下选择“文件共享” 。
1. 选择“软删除”旁边的“已启用” 。
1. 为“软删除所有文件共享”选择“已启用” 。
1. 选择“以天为单位的文件共享保持期”，然后输入选择的数字。
1. 选择“保存”以确认数据保留设置。

    :::image type="content" source="media/storage-how-to-recover-deleted-account/files-enable-soft-delete-new-ui.png" alt-text="存储帐户软删除设置窗格的屏幕截图。突出显示文件共享软删除部分，启用切换，设置保持期并保存。这将为你的存储帐户中的所有文件共享启用软删除。":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
若要启用软删除，必须更新所有 Azure 文件共享的设置（也称为 `FileService` 属性）。 下面的示例为存储帐户中的所有文件共享启用了软删除。 请记得将 `<resource-group>` 和 `<storage-account>` 替换为适合环境的值。

```PowerShell
$resourceGroupName = "<resource-group>"
$storageAccountName = "<storage-account>"

Update-AzStorageFileServiceProperty `
    -ResourceGroupName $resourceGroupName `
    -StorageAccountName $storageAccountName `
    -EnableShareDeleteRetentionPolicy $true `
    -ShareRetentionDays 7
```

可以通过以下命令验证是否已启用软删除并查看其保留策略：

```PowerShell
Get-AzStorageFileServiceProperty `
    -ResourceGroupName $resourceGroupName `
    -StorageAccountName $storageAccountName
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
若要启用软删除，则必须更新文件客户端的服务属性。 下面的示例为存储帐户中的所有文件共享启用了软删除。 请记得将 `<resource-group>` 和 `<storage-account>` 替换为适合环境的值。

```bash
resourceGroupName="<resource-group>"
storageAccountName="<storage-account>"

az storage account file-service-properties update \
    --resource-group $resourceGroupName \
    --account-name $storageAccountName \
    --enable-delete-retention true \
    --delete-retention-days 7
```

可以通过以下命令验证是否已启用软删除并查看其保留策略：

```bash
az storage account file-service-properties show \
    -resource-group $resourceGroupName \
    -account-name $storageAccountName
```
---

## <a name="restore-soft-deleted-file-share"></a>还原软删除的文件共享

# <a name="portal"></a>[Portal](#tab/azure-portal)
若要还原软删除的文件共享：

1. 导航到存储帐户，然后选择“文件共享”。
1. 在“文件共享”边栏选项卡上，启用“显示删除的共享”显示已软删除的所有共享。

    这会显示当前处于“已删除”状态的所有共享。

    :::image type="content" source="media/storage-how-to-recover-deleted-account/undelete-file-share.png" alt-text="如果将名称列旁边的状态列设置为“已删除”，则文件共享处于软删除的状态。在指定的保持期后，它将被永久删除。":::

1. 选择共享，然后选择“取消删除”，这将还原共享。

    可以确认共享已还原，因为其状态切换为“活动”。

    :::image type="content" source="media/storage-how-to-recover-deleted-account/restored-file-share.png" alt-text="如果将名称列旁边的状态列设置为“活动”，则文件共享已还原。":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
若要还原已软删除的文件共享，必须先获取该共享的 `-DeletedShareVersion` 值。 若要获取该值，请使用以下命令列出存储帐户的所有已删除共享。

```PowerShell
Get-AzRmStorageShare `
    -ResourceGroupName $resourceGroupName `
    -StorageAccountName $storageAccountName `
    -IncludeDeleted
```

确定要还原的共享后，可以将其与以下命令一起使用来还原它：

```PowerShell
Restore-AzRmStorageShare `
    -ResourceGroupName $resourceGroupName `
    -StorageAccountName $storageAccountName `
    -DeletedShareVersion 01D5E2783BDCDA97 # replace with your deleted version number
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
若要还原已软删除的文件共享，必须先获取该共享的 `--deleted-version` 值。 若要获取该值，请使用以下命令列出存储帐户的所有已删除共享。

```bash
az storage share-rm list \
    --resource-group $resourceGroupName \
    --storage-account $storageAccountName \
    --include-deleted
```

确定要还原的共享后，可以将其与以下命令一起使用来还原它：

```bash
az storage share-rm restore -n deletedshare --deleted-version 01D64EB9886F00C4 -g yourResourceGroup --storage-account yourStorageaccount
```

---

## <a name="disable-soft-delete"></a>禁用软删除
如果希望停止使用软删除，请按照以下说明操作。 若要永久删除已软删除的文件共享，必须将其撤销删除并禁用软删除，然后再次将其删除。 

# <a name="portal"></a>[门户](#tab/azure-portal)

1. 导航到存储帐户，然后在“数据存储”下选择“文件共享” 。
1. 选择“软删除”旁边的链接。
1. 为“软删除所有文件共享”选择“已禁用” 。
1. 选择“保存”以确认数据保留设置。

    :::image type="content" source="media/storage-how-to-recover-deleted-account/files-disable-soft-delete.png" alt-text="禁用软删除将允许在空闲时立即永久删除存储帐户中的所有文件共享。":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
可以使用以下命令在存储帐户上禁用软删除。

```PowerShell
Update-AzStorageFileServiceProperty `
    -ResourceGroupName $resourceGroupName `
    -StorageAccountName $storageAccountName `
    -EnableShareDeleteRetentionPolicy $false
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
可以使用以下命令在存储帐户上禁用软删除。

```bash
az storage account file-service-properties update \
    --resource-group $resourceGroupName \
    --storage-account $storageAccountName \
    --enable-delete-retention false
```

---

## <a name="next-steps"></a>后续步骤
若要了解其他形式的数据保护和恢复，请参阅 [Azure 文件存储的共享快照概述](storage-snapshots-files.md)一文。
