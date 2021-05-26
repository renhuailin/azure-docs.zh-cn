---
title: 管理和还原软删除的 Blob
titleSuffix: Azure Storage
description: 使用 Azure 门户或 Azure 存储客户端库管理和还原软删除的 Blob 与快照。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/27/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: 7e951de46b5220e5c2edde2fcd84673c9a16cebc
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/26/2021
ms.locfileid: "110477699"
---
# <a name="manage-and-restore-soft-deleted-blobs"></a>管理和还原软删除的 Blob

Blob 软删除会在系统中将已删除的数据保留指定的一段时间，可以在意外删除或覆盖单个 Blob 及其版本、快照和元数据时提供保护。 在保留期内，可以将 Blob 还原到它在删除时的状态。 在指定的保留期已过后，Blob 将永久被删除。 有关 Blob 软删除的详细信息，请参阅 [Blob 的软删除](soft-delete-blob-overview.md)。

Blob 软删除是针对 Blob 数据的综合性数据保护策略的一部分。 若要详细了解 Microsoft 的数据保护建议，请参阅[数据保护概述](data-protection-overview.md)。

## <a name="manage-soft-deleted-blobs-with-the-azure-portal"></a>使用 Azure 门户管理软删除的 Blob

可以使用 Azure 门户来查看和还原软删除的 Blob 与快照。

### <a name="view-deleted-blobs"></a>查看已删除的 Blob

将 Blob 软删除后，默认情况下它们不会显示在 Azure 门户中。 若要查看软删除的 Blob，请导航到容器的“概述”页，并切换“显示已删除的 Blob”设置 。 软删除的 Blob 显示为“已删除”状态。

:::image type="content" source="media/soft-delete-blob-manage/soft-deleted-blobs-list-portal.png" alt-text="显示如何在 Azure 门户中列出软删除的 Blob 的屏幕截图":::

接下来，从 Blob 列表中选择已删除的 Blob，以显示其属性。 在“概述”选项卡下，可以看到 Blob 的状态设置为“已删除” 。 门户还会显示在永久删除该 Blob 之前剩余的天数。

:::image type="content" source="media/soft-delete-blob-manage/soft-deleted-blob-properties-portal.png" alt-text="显示 Azure 门户中软删除的 Blob 的属性的屏幕截图":::

### <a name="view-deleted-snapshots"></a>显示已删除的快照

删除某个 Blob 也会删除与该 Blob 关联的所有快照。 如果软删除的 Blob 具有快照，则也可以在门户中显示已删除的快照。 显示软删除的 Blob 的属性，导航到“快照”选项卡，然后切换为“显示已删除的快照” 。

:::image type="content" source="media/soft-delete-blob-manage/soft-deleted-blob-snapshots-portal.png" alt-text="屏幕截图":::

### <a name="restore-soft-deleted-objects-when-versioning-is-disabled"></a>禁用版本控制时还原软删除的对象

若要在未启用 Blob 版本控制的情况下在 Azure 门户中还原软删除的某个 Blob，请先显示该 Blob 的属性，然后选择“概述”选项卡上的“取消删除”按钮。还原某个 Blob 也会还原在软删除保留期内删除的所有快照 。

:::image type="content" source="media/soft-delete-blob-manage/undelete-soft-deleted-blob-portal.png" alt-text="显示如何在 Azure 门户中还原软删除的 Blob 的屏幕截图":::

若要将软删除的快照提升到基础 Blob，请先确保已还原 Blob 的已软删除快照。 选择“取消删除”按钮以还原 Blob 的已软删除快照，即使基础 Blob 本身尚未软删除。 接下来，选择要提升的快照，并使用“提升快照”按钮来用快照内容覆盖基础 Blob。

:::image type="content" source="media/soft-delete-blob-manage/promote-snapshot.png" alt-text="显示如何将快照提升到基础 Blob 的屏幕截图":::

### <a name="restore-soft-deleted-blobs-when-versioning-is-enabled"></a>启用版本控制时还原软删除的 Blob

若要在启用了版本控制的情况下在 Azure 门户中还原软删除的 Blob，请选择软删除的 Blob 以显示其属性，然后选择“版本”选项卡。选择要提升为当前版本的版本，然后选择“设为当前版本” 。  

:::image type="content" source="media/soft-delete-blob-manage/soft-deleted-blob-promote-version-portal.png" alt-text="显示如何在 Azure 门户中提升版本以还原 Blob 的屏幕截图":::

若要在启用了版本控制的情况下还原已删除的版本或快照，请显示 Blob 的属性，然后选择“概述”选项卡上的“取消删除”按钮 。

> [!NOTE]
> 启用版本控制后，对已删除的 Blob 选择“取消删除”按钮会还原所有已软删除的版本或快照，但不会还原基础 Blob。 若要还原基础 Blob，必须提升前一个版本。

## <a name="manage-soft-deleted-blobs-with-code"></a>使用代码管理软删除的 Blob

可以使用 Azure 存储客户端库来还原软删除的 Blob 或快照。 以下示例演示如何使用 .NET 客户端库。

### <a name="restore-soft-deleted-objects-when-versioning-is-disabled"></a>禁用版本控制时还原软删除的对象

# <a name="net-v12-sdk"></a>[.NET v12 SDK](#tab/dotnet)

若要在未启用版本控制的情况下还原已删除的 Blob，请对这些 Blob 调用[取消删除 Blob](/rest/api/storageservices/undelete-blob) 操作。 “取消删除 Blob”操作还原已软删除的 Blob，以及与这些 Blob 关联的所有已删除的快照。

对未删除的 Blob 调用“取消删除 Blob”不会起作用。 以下示例对容器中的所有 Blob 调用“取消删除 Blob”，并还原已软删除的 Blob 及其快照：

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/DataProtection.cs" id="Snippet_RecoverDeletedBlobs":::

若要还原特定的版本，请先对基础 Blob 或版本调用“取消删除 Blob”操作，然后将所需版本复制到基础 Blob。 以下示例将块 Blob 还原到最近保存的版本：

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/DataProtection.cs" id="Snippet_RecoverSpecificBlobSnapshot":::

# <a name="net-v11-sdk"></a>[.NET v11 SDK](#tab/dotnet11)

若要在未启用版本控制的情况下还原已删除的 Blob，请对这些 Blob 调用[取消删除 Blob](/rest/api/storageservices/undelete-blob) 操作。 “取消删除 Blob”操作还原已软删除的 Blob，以及与这些 Blob 关联的所有已删除的快照。

对未删除的 Blob 调用“取消删除 Blob”不会起作用。 以下示例对容器中的所有 Blob 调用“取消删除 Blob”，并还原已软删除的 Blob 及其快照：

```csharp
// Restore all blobs in a container.
foreach (CloudBlob blob in container.ListBlobs(useFlatBlobListing: true, blobListingDetails: BlobListingDetails.Deleted))
{
       await blob.UndeleteAsync();
}
```

若要还原特定的快照，请先对基础 Blob 调用“取消删除 Blob”操作，然后将所需快照复制到基础 Blob。 以下示例将块 Blob 还原到其最近生成的快照：

```csharp
// Restore the block blob.
await blockBlob.UndeleteAsync();

// List all blobs and snapshots in the container, prefixed by the blob name.
IEnumerable<IListBlobItem> allBlobSnapshots = container.ListBlobs(
    prefix: blockBlob.Name, useFlatBlobListing: true, blobListingDetails: BlobListingDetails.Snapshots);

// Copy the most recently generated snapshot to the base blob.
CloudBlockBlob copySource = allBlobSnapshots.First(snapshot => ((CloudBlockBlob)version).IsSnapshot &&
    ((CloudBlockBlob)snapshot).Name == blockBlob.Name) as CloudBlockBlob;
blockBlob.StartCopy(copySource);
```  

---

### <a name="restore-soft-deleted-blobs-when-versioning-is-enabled"></a>启用版本控制时还原软删除的 Blob

若要在启用了版本控制的情况下还原软删除的 Blob，请使用[复制 Blob](/rest/api/storageservices/copy-blob) 或[从 URL 复制 Blob](/rest/api/storageservices/copy-blob-from-url) 操作将前一个版本复制到基础 Blob。  

# <a name="net-v12-sdk"></a>[.NET v12 SDK](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/DataProtection.cs" id="Snippet_RestorePreviousVersion":::

# <a name="net-v11-sdk"></a>[.NET v11 SDK](#tab/dotnet11)

不适用。 Blob 版本控制仅在 Azure 存储客户端库版本 12.x 和更高版本中受支持。

---

## <a name="next-steps"></a>后续步骤

- [Blob 存储的软删除](./soft-delete-blob-overview.md)
- [为 blob 启用软删除](soft-delete-blob-enable.md)
- [Blob 版本控制](versioning-overview.md)
