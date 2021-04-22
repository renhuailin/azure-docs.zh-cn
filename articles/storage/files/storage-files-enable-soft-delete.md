---
title: 启用软删除 - Azure 文件共享
description: 了解如何在 Azure 文件共享上启用软删除，以进行数据恢复和防止意外删除。
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 03/23/2021
ms.author: rogarana
ms.subservice: files
services: storage
ms.openlocfilehash: 428ef41340cd565bef0fa3c1e6519fb8862b091a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "105727563"
---
# <a name="enable-soft-delete-on-azure-file-shares"></a>在 Azure 文件共享上启用软删除

Azure 存储为文件共享提供软删除，以便在应用程序或其他存储帐户用户错误地删除了数据后，可以更轻松地恢复数据。 若要了解有关软删除的详细信息，请参阅[如何防止意外删除 Azure 文件共享](storage-files-prevent-file-share-deletion.md)。

以下部分说明如何在现有存储帐户上为 Azure 文件共享启用和使用软删除：

# <a name="portal"></a>[Portal](#tab/azure-portal)

## <a name="getting-started"></a>入门

1. 登录到 [Azure 门户](https://portal.azure.com/)。
1. 导航到存储帐户，然后在“文件服务”下选择“文件共享” 。
1. 为“软删除所有文件共享”选择“已启用” 。
1. 选择“以天为单位的文件共享保持期”，然后输入选择的数字。
1. 选择“保存”以确认数据保留设置。

:::image type="content" source="media/storage-how-to-recover-deleted-account/enable-soft-delete-files.png" alt-text="存储帐户软删除设置窗格的屏幕截图。突出显示文件共享软删除部分，启用切换，设置保持期并保存。这将为你的存储帐户中的所有文件共享启用软删除。":::

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

在 2.1.3 版及更新版本的 [Azure CLI 模块](/cli/azure/install-azure-cli)中提供了软删除 cmdlet。

## <a name="getting-started-with-cli"></a>CLI 入门

若要启用软删除，则必须更新文件客户端的服务属性。 下面的示例为存储帐户中的所有文件共享启用了软删除：

```azurecli
az storage account file-service-properties update --enable-delete-retention true -n yourStorageaccount -g yourResourceGroup
```

可以通过以下命令验证是否已启用软删除并查看其保留策略：

```azurecli
az storage account file-service-properties show -n yourStorageaccount -g yourResourceGroup
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

## <a name="prerequisite"></a>先决条件

在 4.8.0 版及更新版本的 Az.Storage 模块中提供了软删除 cmdlet。 

## <a name="getting-started-with-powershell"></a>PowerShell 入门

若要启用软删除，则必须更新文件客户端的服务属性。 下面的示例为存储帐户中的所有文件共享启用了软删除：

```azurepowershell-interactive
$rgName = "yourResourceGroupName"
$accountName = "yourStorageAccountName"

Update-AzStorageFileServiceProperty -ResourceGroupName $rgName -StorageAccountName $accountName -EnableShareDeleteRetentionPolicy $true -ShareRetentionDays 7
```

可以通过以下命令验证是否已启用软删除并查看其保留策略：

```azurepowershell-interactive
Get-AzStorageFileServiceProperty -ResourceGroupName $rgName -StorageAccountName $accountName
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

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

在 2.1.3 版 Azure CLI 中提供了软删除 cmdlet。 若要还原已软删除的文件共享，必须先获取该共享的 `--deleted-version` 值。 若要获取该值，请使用以下命令列出存储帐户的所有已删除共享：

```azurecli
az storage share-rm list --storage-account yourStorageaccount --include-deleted
```

确定要还原的共享后，可以将其与以下命令一起使用来还原它：

```azurecli
az storage share-rm restore -n deletedshare --deleted-version 01D64EB9886F00C4 -g yourResourceGroup --storage-account yourStorageaccount
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

在 4.8.0 版及更新版本的 Az.Storage 模块中提供了软删除 cmdlet。 若要还原已软删除的文件共享，必须先获取该共享的 `-DeletedShareVersion` 值。 若要获取该值，请使用以下命令列出存储帐户的所有已删除共享：

```azurepowershell-interactive
Get-AzRmStorageShare -ResourceGroupName $rgname -StorageAccountName $accountName -IncludeDeleted
```

确定要还原的共享后，可以将其与以下命令一起使用来还原它：

```azurepowershell-interactive
Restore-AzRmStorageShare -ResourceGroupName $rgname -StorageAccountName $accountName -DeletedShareVersion 01D5E2783BDCDA97
```
---

## <a name="disable-soft-delete"></a>禁用软删除

如果希望停止使用软删除，请按照以下说明操作。 若要永久删除已软删除的文件共享，必须将其撤销删除并禁用软删除，然后再次将其删除。 

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. 导航到存储帐户，然后在“文件服务”下选择“文件共享” 。
1. 为“软删除所有文件共享”选择“已禁用” 。
1. 选择“保存”以确认数据保留设置。

    :::image type="content" source="media/storage-how-to-recover-deleted-account/disable-soft-delete-files.png" alt-text="禁用软删除将允许在空闲时立即永久删除存储帐户中的所有文件共享。":::

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

在 2.1.3 版 Azure CLI 中提供了软删除 cmdlet。 可以使用以下命令在存储帐户上禁用软删除：

```azurecli
az storage account file-service-properties update --enable-delete-retention false -n yourStorageaccount -g yourResourceGroup
```
# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

在 4.8.0 版及更新版本的 Az.Storage 模块中提供了软删除 cmdlet。 可以使用以下命令在存储帐户上禁用软删除：

```azurepowershell-interactive
Update-AzStorageFileServiceProperty -ResourceGroupName $rgName -StorageAccountName $accountName -EnableShareDeleteRetentionPolicy $false
```
---

## <a name="next-steps"></a>后续步骤

若要了解其他形式的数据保护和恢复，请参阅 [Azure 文件存储的共享快照概述](storage-snapshots-files.md)一文。