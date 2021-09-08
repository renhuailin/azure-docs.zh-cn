---
title: 将存档的 Blob 解冻到联机层
titleSuffix: Azure Storage
description: 在读取存档层中的 Blob 之前，必须先将其解除冻结到热层或冷层。 可以通过将 Blob 从存档层复制到联机层，或将其层从存档层更改为热层或冷层来解除冻结 Blob。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 08/25/2021
ms.author: tamram
ms.reviewer: fryu
ms.custom: devx-track-azurepowershell
ms.subservice: blobs
ms.openlocfilehash: c033920b88f2863d34f43bf0affe4b9165995a3a
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2021
ms.locfileid: "123258789"
---
# <a name="rehydrate-an-archived-blob-to-an-online-tier"></a>将存档的 Blob 解冻到联机层

若要读取存档层中的 Blob，必须先将 Blob 解除冻结到热层或冷层。 可以通过以下两种方式之一解除冻结 Blob：

- 使用[复制 Blob](/rest/api/storageservices/copy-blob-from-url) 或[从 URL 复制 Blob](/rest/api/storageservices/copy-blob) 操作将其复制到热层或冷层中的新 Blob。 Microsoft 建议在大多数情况下使用此种方式。
- 使用[设置 Blob 层](/rest/api/storageservices/set-blob-tier)操作将其层从存档层更改为热层或冷层。

解除冻结操作可能需要 15 个小时才能完成。 你可以配置 Azure 事件网格，用于在解除冻结完成时触发事件，并运行应用程序代码进行响应。 若要了解如何在 Blob 解除冻结操作完成后处理运行 Azure 函数的事件，请参阅[运行 Azure 函数以响应 Blob 解除冻结事件](archive-rehydrate-handle-event.md)。

## <a name="rehydrate-a-blob-with-a-copy-operation"></a>使用复制操作解除冻结 Blob

若要通过将 Blob 从存档层复制到联机层来解除冻结 Blob，请使用 PowerShell、Azure CLI 或任一 Azure 存储客户端库。 请注意，当你将存档的 Blob 复制到联机层时，源 Blob 和目标 Blob 必须使用不同的名称。

复制操作完成后，目标 Blob 将显示在存档层中。 然后，目标 Blob 将被解除冻结到你在复制操作中指定的联机层。 目标 Blob 完全解除冻结后，它将在新的联机层中可用。

下面的示例展示了如何使用 PowerShell 或 Azure CLI 复制存档的 Blob。

### <a name="azure-portal"></a>[Azure 门户](#tab/portal)

不可用

### <a name="powershell"></a>[PowerShell](#tab/powershell)

若要使用 PowerShell 将存档的 Blob 复制到联机层，请调用 [Start-AzStorageBlobCopy](/powershell/module/az.storage/start-azstorageblobcopy) 命令，并指定目标层和解除冻结优先级。 请记得将尖括号中的占位符替换为你自己的值：

```powershell
# Initialize these variables with your values.
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$srcContainerName = "<source-container>"
$destContainerName = "<dest-container>"
$srcBlobName = "<source-blob>"
$destBlobName = "<dest-blob>"

# Get the storage account context
$ctx = (Get-AzStorageAccount `
        -ResourceGroupName $rgName `
        -Name $accountName).Context

# Copy the source blob to a new destination blob in hot tier with standard priority.
Start-AzStorageBlobCopy -SrcContainer $srcContainerName `
    -SrcBlob $srcBlobName `
    -DestContainer $destContainerName `
    -DestBlob $destBlobName `
    -StandardBlobTier Hot `
    -RehydratePriority Standard `
    -Context $ctx
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要使用 Azure CLI 将存档的 Blob 复制到联机层，请调用 [az storage blob copy start](/cli/azure/storage/blob/copy#az_storage_blob_copy_start) 命令，并指定目标层和解除冻结优先级。 请记得将尖括号中的占位符替换为你自己的值：

```azurecli
az storage blob copy start \
    --source-container <source-container> \
    --source-blob <source-blob> \
    --destination-container <dest-container> \
    --destination-blob <dest-blob> \
    --account-name <storage-account> \
    --tier hot \
    --rehydrate-priority standard \
    --auth-mode login
```

---

## <a name="rehydrate-a-blob-by-changing-its-tier"></a>通过更改 Blob 层解除冻结 Blob

若要通过将 Blob 层从存档操作更改为热操作或冷操作来解除冻结 Blob，可以使用 Azure 门户、PowerShell 或 Azure CLI。

### <a name="azure-portal"></a>[Azure 门户](#tab/portal)

若要在 Azure 门户中将 Blob 层从存档层更改为热层或冷层，请执行以下步骤：

1. 在 Azure 门户中找到要解除冻结的 Blob。
1. 选择页面右侧的“更多”按钮。
1. 选择“更改层”。
1. 从“访问层”下拉菜单中选择目标访问层。
1. 从“解除冻结优先级”下拉菜单中，选择所需的解除冻结优先级。 请注意，将“解除冻结优先级”设置为“高”通常会加快解除冻结速度，但同时也会增加成本。

    :::image type="content" source="media/archive-rehydrate-to-online-tier/rehydrate-change-tier-portal.png" alt-text="显示如何在 Azure 门户中解除冻结存档层中的 Blob 的屏幕截图":::

1. 选择“保存”按钮。

### <a name="powershell"></a>[PowerShell](#tab/powershell)

若要使用 PowerShell 将 Blob 层从存档层更改为热层或冷层，请使用 Blob 的 ICloudBlob 属性返回对 Blob 的 .NET 引用，然后对该引用调用 SetStandardBlobTier 方法。 请记得将尖括号中的占位符替换为你自己的值：

```powershell
# Initialize these variables with your values.
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$containerName = "<container>"
$blobName = "<archived-blob>"

# Get the storage account context
$ctx = (Get-AzStorageAccount `
        -ResourceGroupName $rgName `
        -Name $accountName).Context

# Change the blob’s access tier to hot with standard priority.
$blob = Get-AzStorageBlob -Container $containerName -Blob $blobName -Context $ctx
$blob.BlobClient.SetAccessTier("Hot", $null, "High")
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要使用 Azure CLI 将 Blob 层从存档层更改为热层或冷层，请调用 [az storage blob set-tier](/cli/azure/storage/blob#az_storage_blob_set_tier) 命令。 请记得将尖括号中的占位符替换为你自己的值：

```azurecli
az storage blob set-tier \
    --container-name <container> \
    --name <archived-blob> \
    --tier Hot \
    --account-name <account-name> \
    --rehydrate-priority High \
    --auth-mode login
```

---

## <a name="rehydrate-a-large-number-of-blobs"></a>解除冻结大量 blob

要一次解除冻结大量 blob，请调用 [Blob 批处理](/rest/api/storageservices/blob-batch) 操作，将[设置 Blob 层](/rest/api/storageservices/set-blob-tier)作为批量操作调用。 有关演示如何执行批处理操作的代码示例，请参阅 [AzBulkSetBlobTier](/samples/azure/azbulksetblobtier/azbulksetblobtier/)。

## <a name="check-the-status-of-a-rehydration-operation"></a>查看解除冻结操作的状态

在对 Blob 解除冻结时，可以使用 Azure 门户、PowerShell 或 Azure CLI 查看其状态和解除冻结优先级。 状态属性可能返回 rehydrate-pending-to-hot 或 rehydrate-pending-to-cool，具体取决于解除冻结操作的目标层。 解除冻结优先级属性会返回“标准”或“高” 。

请注意，解除冻结存档的 Blob 可能需要 15 个小时才能完成，如果通过反复轮询 Blob 的状态来确定解除冻结是否完成，效率会很低。 通过使用 Azure 事件网格捕获在解除冻结完成时触发的事件，能够提高性能并优化成本。 若要了解如何在因 Blob 解除冻结而触发事件时运行 Azure 函数，请参阅[运行 Azure 函数以响应 Blob 解除冻结事件](archive-rehydrate-handle-event.md)。

### <a name="azure-portal"></a>[Azure 门户](#tab/portal)

若要在 Azure 门户中查看挂起的解除冻结操作的状态和优先级，请显示 Blob 的“更改层”对话框：

:::image type="content" source="media/archive-rehydrate-to-online-tier/rehydration-status-portal.png" alt-text="显示 Azure 门户中 Blob 的解除冻结状态的屏幕截图":::

解除冻结完成后，可以在 Azure 门户看到，完全解除冻结的 Blob 现在显示在目标联机层中。

:::image type="content" source="media/archive-rehydrate-to-online-tier/set-blob-tier-rehydrated.png" alt-text="显示冷层中已解除冻结的 Blob 以及事件处理程序写入的日志 Blob 的屏幕截图":::

### <a name="powershell"></a>[PowerShell](#tab/powershell)

若要使用 PowerShell 查看挂起的解除冻结操作的状态和优先级，请调用 [Get-AzStorageBlob](/powershell/module/az.storage/get-azstorageblob) 命令，并查看 Blob 的 ArchiveStatus 和 RehydratePriority 属性 。 如果解除冻结是一个复制操作，请在目标 Blob 上查看上述属性。 请记得将尖括号中的占位符替换为你自己的值：

```powershell
$rehydratingBlob = Get-AzStorageBlob -Container $containerName -Blob $blobName -Context $ctx
$rehydratingBlob.BlobProperties.ArchiveStatus
$rehydratingBlob.BlobProperties.RehydratePriority
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要使用 Azure CLI 查看挂起的解除冻结操作的状态和优先级，请调用 [az storage blob show](/cli/azure/storage/blob#az_storage_blob_show) 命令，并查看目标 Blob 的 rehydrationStatus 和 rehydratePriority 属性。 请记得将尖括号中的占位符替换为你自己的值：

```azurecli
az storage blob show \
    --account-name <storage-account> \
    --container-name <container> \
    --name <destination-blob> \
    --query '[rehydratePriority, properties.rehydrationStatus]' \
    --output tsv \
    --auth-mode login
```

---

## <a name="see-also"></a>另请参阅

- [Azure Blob 存储：热访问层、冷访问层和存档访问层](storage-blob-storage-tiers.md)。
- [从存档层中解除冻结 Blob 的概述](archive-rehydrate-overview.md)
- [运行 Azure 函数以响应 Blob 解除冻结事件](archive-rehydrate-handle-event.md)
- [响应 Blob 存储事件](storage-blob-event-overview.md)
