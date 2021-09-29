---
title: 对块 blob 数据执行时间点还原
titleSuffix: Azure Storage
description: 了解如何使用时间点还原将一个块 blob 集还原到以前某个给定时间点的状态。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 01/29/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: c4dc8b3e079f224dbefde3bc12b5d79a4db32faa
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128589599"
---
# <a name="perform-a-point-in-time-restore-on-block-blob-data"></a>对块 blob 数据执行时间点还原

可以使用时间点还原将一个或多个块 blob 集还原到以前的状态。 本文介绍如何为存储帐户启用时间点还原，以及如何执行还原操作。

有关时间点还原的详细信息，请参阅[块 blob 的时间点还原](point-in-time-restore-overview.md)。

> [!CAUTION]
> 时间点还原仅支持对块 blob 执行还原操作。 无法还原对容器的操作。 如果通过调用[删除容器](/rest/api/storageservices/delete-container)操作从存储帐户中删除了某个容器，将无法使用还原操作来还原该容器。 如果要在以后还原它们，请删除单个 blob，而不是删除整个容器。 此外，Microsoft 建议为容器和 blob 启用软删除，以防止意外删除。 有关详细信息，请参阅[容器软删除](soft-delete-container-overview.md)和 [blob 软删除](soft-delete-blob-overview.md)。

## <a name="enable-and-configure-point-in-time-restore"></a>启用和配置时间点还原

在启用和配置时间点还原之前，请先为存储帐户启用其先决条件：软删除、更改源和 blob 版本控制。 若要详细了解如何启用上述每项功能，请参阅以下文章：

- [为 blob 启用软删除](./soft-delete-blob-enable.md)
- [启用和禁用更改源](storage-blob-change-feed.md#enable-and-disable-the-change-feed)
- [启用和管理 blob 版本控制](versioning-enable.md)

> [!IMPORTANT]
> 启用软删除、更改源和 blob 版本控制可能会产生额外费用。 有关详细信息，请参阅 [Blob 软删除](soft-delete-blob-overview.md)、[在 Azure Blob 存储中更改源支持](storage-blob-change-feed.md)和 [Blob 版本控制](versioning-overview.md)。

# <a name="azure-portal"></a>[Azure 门户](#tab/portal)

要使用 Azure 门户配置时间点还原，请执行以下步骤：

1. 导航到 Azure 门户中的存储帐户。
1. 在“设置”下，选择“数据保护”。
1. 选择“打开时间点还原”。 选择此选项后，还会启用 Blob 软删除、版本控制和更改源。
1. 以天为单位设置时间点还原的最大还原点。 此数字必须至少为一天，并小于为 blob 软删除指定的保持期。
1. 保存所做更改。

下图显示了一个已配置时间点还原的存储帐户，其还原点配置为七天前，Blob 软删除的保持期为 14 天。

:::image type="content" source="media/point-in-time-restore-manage/configure-point-in-time-restore-portal.png" alt-text="显示如何在 Azure 门户中配置时间点还原的屏幕截图":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

要通过 PowerShell 配置时间点还原，请首先安装 [Az.Storage](https://www.powershellgallery.com/packages/Az.Storage) 模块版本 2.6.0 或更高版本。 然后，调用 [Enable-AzStorageBlobRestorePolicy](/powershell/module/az.storage/enable-azstorageblobrestorepolicy) 命令为存储帐户启用时间点还原。

以下示例将启用软删除并设置软删除保持期，启用更改源和版本控制，然后启用时间点还原。 运行此示例时，请务必将尖括号中的值替换为你自己的值：

```powershell
# Set resource group and account variables.
$rgName = "<resource-group>"
$accountName = "<storage-account>"

# Enable blob soft delete with a retention of 14 days.
Enable-AzStorageBlobDeleteRetentionPolicy -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -RetentionDays 14

# Enable change feed and versioning.
Update-AzStorageBlobServiceProperty -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -EnableChangeFeed $true `
    -IsVersioningEnabled $true

# Enable point-in-time restore with a retention period of 7 days.
# The retention period for point-in-time restore must be at least
# one day less than that set for soft delete.
Enable-AzStorageBlobRestorePolicy -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -RestoreDays 7

# View the service settings.
Get-AzStorageBlobServiceProperty -ResourceGroupName $rgName `
    -StorageAccountName $accountName
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

要使用 Azure CLI 配置时间点还原，请首先安装 Azure CL 版本 2.2.0 或更高版本。 然后，调用 [az storage account blob-service-properties update](/cli/azure/storage/account/blob-service-properties#az_storage_account_blob_service_properties_update) 命令来启用时间点还原，以及存储帐户所需的其他数据保护设置。

以下示例将启用软删除并将软删除保持期设置为 14 天，启用更改源和版本控制，然后启用时间点还原，还原期为 7 天。 运行此示例时，请务必将尖括号中的值替换为你自己的值：

```azurecli
az storage account blob-service-properties update \
    --resource-group <resource_group> \
    --account-name <storage-account> \
    --enable-delete-retention true \
    --delete-retention-days 14 \
    --enable-versioning true \
    --enable-change-feed true \
    --enable-restore-policy true \
    --restore-days 7
```

---

## <a name="choose-a-restore-point"></a>选择还原点

还原点是指要将数据还原到的日期和时间。 Azure 存储始终使用 UTC 日期/时间值作为还原点。 不过，Azure 门户支持以本地时间指定还原点，然后再将日期/时间值转换为 UTC 日期/时间值来执行还原操作。

使用 PowerShell 或 Azure CLI 执行还原操作时，应将还原点指定为 UTC 日期/时间值。 如果使用本地时间值（而不是 UTC 时间值）指定还原点，在某些情况下，还原操作可能仍会按预期方式运行。 例如，如果本地时间为 UTC 减去五个小时，则指定本地时间值将导致还原点比提供的值早五个小时。 如果在这五个小时期间内没有对要还原范围内的数据进行更改，则还原操作将生成相同的结果，不论提供的时间值为何。 建议为还原点指定 UTC 时间，以避免造成意外结果。

## <a name="perform-a-restore-operation"></a>执行还原操作

可以还原存储帐户中的所有容器，也可以还原一个或多个容器中的一系列 Blob。 Blob 范围是按字典顺序定义的。 每个还原操作最多支持 10 个字典范围。 范围起始值包括在内，但不包括范围结束值。

为开始范围和结束范围指定的容器模式必须包含至少三个字符（即最小值为 3）。 用于分隔容器名称与 blob 名称的正斜杠 (/) 不计入该最小值。

字典范围内不支持通配符。 任何通配符都将被视为标准字符。

可以还原 `$root` 和 `$web` 容器中的 blob，方法是：在传递给还原操作的范围中显式指定它们。 `$root` 和 `$web` 容器只有在显式指定的情况下才会进行还原。 无法还原其他系统容器。

仅还原块 blob。 页 blob 和追加 blob 不在还原操作的范围之内。 有关追加 blob 相关限制的详细信息，请参阅[块 blob 的时间点还原](point-in-time-restore-overview.md)。

> [!IMPORTANT]
> 执行还原操作时，Azure 存储会阻止对在操作期间还原的范围内的 blob 执行数据操作。 读取、写入和删除操作在主位置中被阻止。 出于此原因，在执行还原操作时，Azure 门户中的操作（如列出容器）可能不会按预期执行。
>
> 如果存储帐户是异地复制的，则在还原操作期间，从辅助位置读取的操作可以继续。
>
> 还原数据集所用的时间取决于选取还原作业最多一小时后在还原期间执行的写入和删除操作的次数。 例如，如果一个帐户有 1 百万个对象，每天增加 3,000 个对象，每天删除 1,000 个对象，将需要大约两三个小时才能还原到过去 30 天的点。 进行少量更改的还原最多需要一小时进行还原。 对于具有此变化率的帐户，不建议在过去保留时间和还原超过90天。

### <a name="restore-all-containers-in-the-account"></a>还原帐户中的所有容器

可以还原存储帐户中的所有容器，使其恢复以前某个给定时间点的状态。

# <a name="azure-portal"></a>[Azure 门户](#tab/portal)

要使用 Azure 还原存储帐户中的所有容器和 blob，请执行以下步骤：

1. 导航到存储帐户的容器列表。
1. 在工具栏上，选择“还原容器”，然后选择“全部还原”。
1. 在“还原所有容器”窗格中，通过提供日期和时间来指定还原点。
1. 选中框，确认想要继续。
1. 选择“还原”以开始还原操作。

    :::image type="content" source="media/point-in-time-restore-manage/restore-all-containers-portal.png" alt-text="显示如何将所有容器还原到指定还原点的屏幕截图":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

要使用 PowerShell 还原存储帐户中的所有容器和 blob，请调用 Restore-AzStorageBlobRange 命令，并以 UTC 日期/时间值的形式提供还原点。 默认情况下，Restore-AzStorageBlobRange 命令以异步方式运行，并返回 PSBlobRestoreStatus 类型的对象，可以使用该对象来检查还原操作的状态。

以下示例以异步方式将存储帐户中的容器还原到当前时间 12 个小时之前的状态，并检查还原操作的某些属性：

```powershell
# Specify -TimeToRestore as a UTC value
$restoreOperation = Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).ToUniversalTime().AddHours(-12)

# Get the status of the restore operation.
$restoreOperation.Status
# Get the ID for the restore operation.
$restoreOperation.RestoreId
# Get the restore point in UTC time.
$restoreOperation.Parameters.TimeToRestore
```

要同步运行还原操作，请在命令中加入 -WaitForComplete 参数。 存在 -WaitForComplete 参数时，PowerShell 会显示一则消息（其中包含操作的还原 ID），然后在还原操作完成前阻止执行。 请谨记，还原操作所需的时间长度取决于要还原的数据量，大型还原操作可能需要长达一小时才能完成。

```powershell
Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).AddHours(-12) -WaitForComplete
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

要使用 Azure CLI 还原存储帐户中的所有容器和 blob，请调用 [az storage blob restore](/cli/azure/storage/blob#az_storage_blob_restore) 命令，并以 UTC 日期/时间值的形式提供还原点。

以下示例将异步运行，将存储帐户中的所有容器还原到指定日期和时间 12 个小时之前的状态。 要检查还原操作的状态，请调用 [az storage account show](/cli/azure/storage/account#az_storage_account_show)：

```azurecli
az storage blob restore \
    --resource-group <resource_group> \
    --account-name <storage-account> \
    --time-to-restore 2021-01-14T06:31:22Z \
    --no-wait
```

要检查还原操作的属性，请调用 [az storage account show](/cli/azure/storage/account#az_storage_account_show)，并展开 blobRestoreStatus 属性。 以下示例演示如何检查 status 属性。

```azurecli
az storage account show \
    --name <storage-account> \
    --resource-group <resource_group> \
    --expand blobRestoreStatus \
    --query blobRestoreStatus.status \
    --output tsv
```

要同步运行 az storage blob restore 命令并在还原操作完成前阻止执行，请忽略 `--no-wait` 参数。

---

### <a name="restore-ranges-of-block-blobs"></a>还原多个范围的块 blob

可以在单个容器内或跨多个容器还原一个或多个字典范围的 blob，将这些 blob 恢复到它们在以前某个给定时间点的状态。

# <a name="azure-portal"></a>[Azure 门户](#tab/portal)

要使用 Azure 门户还原一个或多个容器中某个范围的 blob，请执行以下步骤：

1. 导航到存储帐户的容器列表。
1. 选择要还原的一个或多个容器。
1. 在工具栏上，选择“还原容器”，然后选择“还原所选容器”。
1. 在“还原所选容器”窗格中，通过提供日期和时间来指定还原点。
1. 指定要还原的范围。 可以使用正斜杠 (/) 来分隔容器名称和 blob 前缀。
1. 默认情况下，“还原所选容器”窗格会指定一个包含容器中所有 blob 的范围。 如果不想还原整个容器，可删除此范围。 默认范围如下图所示。

    :::image type="content" source="media/point-in-time-restore-manage/delete-default-blob-range.png" alt-text="显示指定自定义范围前默认要删除的 blob 范围的屏幕截图":::

1. 选中框，确认想要继续。
1. 选择“还原”以开始还原操作。

下图显示了对一组范围的还原操作。

:::image type="content" source="media/point-in-time-restore-manage/restore-multiple-container-ranges-portal.png" alt-text="显示如何还原一个或多个容器中 blob 范围的屏幕截图":::

图中所示的还原操作将执行以下操作：

- 还原 container1 的完整内容。
- 还原 container2 中 blob1 至 blob5 字典范围的 blob。   例如，此范围将还原以下名称的 blob：blob1、blob11、blob100、blob2，以此类推。 由于不包括范围结束值，所以还原操作会还原名称以 blob4 开头的 blob，但不会还原名称以 blob5 开头的 blob。
- 还原 container3 和 container4 中的所有 blob。 因为不包括范围结束值，所以此范围不会还原 container5。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

要还原单个范围的 blob，请调用 Restore-AzStorageBlobRange 命令，并为 `-BlobRestoreRange` 参数指定容器和 blob 名称的字典范围。 例如，要还原单个容器 container1 内的 blob，可以指定一个范围，以 container1  开头，并以 container2 结束。 不要求在开始和结束范围中指定的容器存在。 因为不包括范围的结束值，所以即使存储帐户中包含名为 container2 的容器，也只会还原名为 container1 的容器：

```powershell
$range = New-AzStorageBlobRangeToRestore -StartRange container1 `
    -EndRange container2
```

要指定还原容器中的一个 blob 子集，请使用正斜杠 (/) 将容器名称与 blob 前缀模式隔开。 例如，下面的范围选择单个容器中名称以字母 *d* 到 *f* 开头的 blob：

```powershell
$range = New-AzStorageBlobRangeToRestore -StartRange container1/d `
    -EndRange container1/g
```

接下来，提供执行 Restore-AzStorageBlobRange 命令的范围。 通过为 `-TimeToRestore` 参数提供一个 UTC **日期/时间** 值来指定还原点。 下面的示例将指定范围内的 blob 还原到当前时间后推 3 天的状态：

```powershell
# Specify -TimeToRestore as a UTC value
Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -BlobRestoreRange $range `
    -TimeToRestore (Get-Date).AddDays(-3)
```

默认情况下，Restore-AzStorageBlobRange 命令以异步方式运行。 在异步启动还原操作时，PowerShell 会立即显示此操作的属性表：

```powershell
Status     RestoreId                            FailureReason Parameters.TimeToRestore     Parameters.BlobRanges
------     ---------                            ------------- ------------------------     ---------------------
InProgress 459c2305-d14a-4394-b02c-48300b368c63               2020-09-15T23:23:07.1490859Z ["container1/d" -> "container1/g"]
```

若要还原多个范围的块 blob，请为 `-BlobRestoreRange` 参数指定一个范围数组。 以下示例指定了两个范围，以便将 container1 和 container4 的完整内容还原到其 24 小时之前的状态，并将结果保存到变量：

```powershell
# Specify a range that includes the complete contents of container1.
$range1 = New-AzStorageBlobRangeToRestore -StartRange container1 `
    -EndRange container2
# Specify a range that includes the complete contents of container4.
$range2 = New-AzStorageBlobRangeToRestore -StartRange container4 `
    -EndRange container5

$restoreOperation = Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).AddHours(-24) `
    -BlobRestoreRange @($range1, $range2)

# Get the status of the restore operation.
$restoreOperation.Status
# Get the ID for the restore operation.
$restoreOperation.RestoreId
# Get the blob ranges specified for the operation.
$restoreOperation.Parameters.BlobRanges
```

要同步运行还原操作并在还原完成前阻止执行，请在命令中包含 -WaitForComplete 参数。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

要还原某个范围的 blob，请调用 [az storage blob restore](/cli/azure/storage/blob#az_storage_blob_restore) 命令，并为 `--blob-range` 参数指定容器和 blob 名称的字典范围。 要指定多个范围，请为每个非重复范围提供 `--blob-range` 参数。

例如，要还原单个容器 container1 内的 blob，可以指定一个范围，以 container1  开头，并以 container2 结束。 不要求在开始和结束范围中指定的容器存在。 因为不包括范围的结束值，所以即使存储帐户中包含名为 container2 的容器，也只会还原名为 container1 的容器。

要指定还原容器中的一个 blob 子集，请使用正斜杠 (/) 将容器名称与 blob 前缀模式隔开。 以下示例将异步还原容器中名称从字母 `d` 开始至 `f` 结束的范围的 blob。

```azurecli
az storage blob restore \
    --account-name <storage-account> \
    --time-to-restore 2021-01-14T06:31:22Z \
    --blob-range container1 container2
    --blob-range container3/d container3/g
    --no-wait
```

要同步运行 az storage blob restore 命令并在还原操作完成前阻止执行，请忽略 `--no-wait` 参数。

---

## <a name="next-steps"></a>后续步骤

- [块 blob 的时间点还原](point-in-time-restore-overview.md)
- [软删除](./soft-delete-blob-overview.md)
- [更改源](storage-blob-change-feed.md)
- [Blob 版本控制](versioning-overview.md)
