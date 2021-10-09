---
title: 管理和还原软删除的 Blob
titleSuffix: Azure Storage
description: 使用 Azure 门户或 Azure 存储客户端库管理和还原软删除的 Blob 与快照。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/23/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: 9c66b970a99ad6dd69b9336d1638b2c156868324
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128627931"
---
# <a name="manage-and-restore-soft-deleted-blobs"></a>管理和还原软删除的 Blob

Blob 软删除会在系统中将已删除的数据保留指定的一段时间，可以在意外删除或覆盖单个 Blob 及其版本、快照和元数据时提供保护。 在保留期内，可以将 Blob 还原到它在删除时的状态。 在指定的保留期已过后，Blob 将永久被删除。 有关 Blob 软删除的详细信息，请参阅 [Blob 的软删除](soft-delete-blob-overview.md)。

Blob 软删除是针对 Blob 数据的综合性数据保护策略的一部分。 若要详细了解 Microsoft 的数据保护建议，请参阅[数据保护概述](data-protection-overview.md)。

## <a name="manage-soft-deleted-blobs"></a>管理软删除的 Blob

### <a name="manage-soft-deleted-blobs-with-the-azure-portal"></a>使用 Azure 门户管理软删除的 Blob

可以使用 Azure 门户来查看和还原软删除的 Blob 与快照。

#### <a name="view-deleted-blobs"></a>查看已删除的 Blob

将 Blob 软删除后，默认情况下它们不会显示在 Azure 门户中。 若要查看软删除的 Blob，请导航到容器的“概述”页，并切换“显示已删除的 Blob”设置 。 软删除的 Blob 显示为“已删除”状态。

:::image type="content" source="media/soft-delete-blob-manage/soft-deleted-blobs-list-portal.png" alt-text="显示如何在 Azure 门户中列出软删除的 Blob 的屏幕截图":::

接下来，从 Blob 列表中选择已删除的 Blob，以显示其属性。 在“概述”选项卡下，可以看到 Blob 的状态设置为“已删除” 。 门户还会显示在永久删除该 Blob 之前剩余的天数。

:::image type="content" source="media/soft-delete-blob-manage/soft-deleted-blob-properties-portal.png" alt-text="显示 Azure 门户中软删除的 Blob 的属性的屏幕截图":::

#### <a name="view-deleted-snapshots"></a>显示已删除的快照

删除某个 Blob 也会删除与该 Blob 关联的所有快照。 如果软删除的 Blob 具有快照，则也可以在门户中显示已删除的快照。 显示软删除的 Blob 的属性，导航到“快照”选项卡，然后切换为“显示已删除的快照” 。

:::image type="content" source="media/soft-delete-blob-manage/soft-deleted-blob-snapshots-portal.png" alt-text="屏幕截图":::

#### <a name="restore-soft-deleted-objects-when-versioning-is-disabled"></a>禁用版本控制时还原软删除的对象

若要在未启用 Blob 版本控制的情况下在 Azure 门户中还原软删除的某个 Blob，请先显示该 Blob 的属性，然后选择“概述”选项卡上的“取消删除”按钮。还原某个 Blob 也会还原在软删除保留期内删除的所有快照 。

:::image type="content" source="media/soft-delete-blob-manage/undelete-soft-deleted-blob-portal.png" alt-text="显示如何在 Azure 门户中还原软删除的 Blob 的屏幕截图":::

若要将软删除的快照提升到基础 Blob，请先确保已还原 Blob 的已软删除快照。 选择“取消删除”按钮以还原 Blob 的已软删除快照，即使基础 Blob 本身尚未软删除。 接下来，选择要提升的快照，并使用“提升快照”按钮来用快照内容覆盖基础 Blob。

:::image type="content" source="media/soft-delete-blob-manage/promote-snapshot.png" alt-text="显示如何将快照提升到基础 Blob 的屏幕截图":::

#### <a name="restore-soft-deleted-blobs-when-versioning-is-enabled"></a>启用版本控制时还原软删除的 Blob

若要在启用了版本控制的情况下在 Azure 门户中还原软删除的 Blob，请选择软删除的 Blob 以显示其属性，然后选择“版本”选项卡。选择要提升为当前版本的版本，然后选择“设为当前版本” 。

:::image type="content" source="media/soft-delete-blob-manage/soft-deleted-blob-promote-version-portal.png" alt-text="显示如何在 Azure 门户中提升版本以还原 Blob 的屏幕截图":::

若要在启用了版本控制的情况下还原已删除的版本或快照，请显示 Blob 的属性，然后选择“概述”选项卡上的“取消删除”按钮 。

> [!NOTE]
> 启用版本控制后，对已删除的 Blob 选择“取消删除”按钮会还原所有已软删除的版本或快照，但不会还原基础 Blob。 若要还原基础 Blob，必须提升前一个版本。

### <a name="manage-soft-deleted-blobs-with-code"></a>使用代码管理软删除的 Blob

可以使用 Azure 存储客户端库来还原软删除的 Blob 或快照。 以下示例演示如何使用 .NET 客户端库。

#### <a name="restore-soft-deleted-objects-when-versioning-is-disabled"></a>禁用版本控制时还原软删除的对象

##### <a name="net-v12-sdk"></a>[.NET v12 SDK](#tab/dotnet)

若要在未启用版本控制的情况下还原已删除的 Blob，请对这些 Blob 调用[取消删除 Blob](/rest/api/storageservices/undelete-blob) 操作。 “取消删除 Blob”操作还原已软删除的 Blob，以及与这些 Blob 关联的所有已删除的快照。

对未删除的 Blob 调用“取消删除 Blob”不会起作用。 以下示例对容器中的所有 Blob 调用“取消删除 Blob”，并还原已软删除的 Blob 及其快照：

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/DataProtection.cs" id="Snippet_RecoverDeletedBlobs":::

若要还原特定的软删除快照，请先对基础 Blob 调用“取消删除 Blob”操作，然后将所需快照复制到基础 Blob。 以下示例将块 Blob 还原到其最近生成的快照：

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/DataProtection.cs" id="Snippet_RecoverSpecificBlobSnapshot":::

##### <a name="net-v11-sdk"></a>[.NET v11 SDK](#tab/dotnet11)

若要在未启用版本控制的情况下还原已删除的 Blob，请对这些 Blob 调用[取消删除 Blob](/rest/api/storageservices/undelete-blob) 操作。 “取消删除 Blob”操作还原已软删除的 Blob，以及与这些 Blob 关联的所有已删除的快照。

对未删除的 Blob 调用“取消删除 Blob”不会起作用。 以下示例对容器中的所有 Blob 调用“取消删除 Blob”，并还原已软删除的 Blob 及其快照：

```csharp
// Restore all blobs in a container.
foreach (CloudBlob blob in container.ListBlobs(useFlatBlobListing: true, blobListingDetails: BlobListingDetails.Deleted))
{
       await blob.UndeleteAsync();
}
```

若要还原特定的软删除快照，请先对基础 Blob 调用“取消删除 Blob”操作，然后将所需快照复制到基础 Blob。 以下示例将块 Blob 还原到其最近生成的快照：

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

#### <a name="restore-soft-deleted-blobs-when-versioning-is-enabled"></a>启用版本控制时还原软删除的 Blob

若要在启用了版本控制的情况下还原软删除的 Blob，请使用[复制 Blob](/rest/api/storageservices/copy-blob) 或[从 URL 复制 Blob](/rest/api/storageservices/copy-blob-from-url) 操作将前一个版本复制到基础 Blob。

##### <a name="net-v12-sdk"></a>[.NET v12 SDK](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/DataProtection.cs" id="Snippet_RestorePreviousVersion":::

##### <a name="net-v11-sdk"></a>[.NET v11 SDK](#tab/dotnet11)

不适用。 Blob 版本控制仅在 Azure 存储客户端库版本 12.x 和更高版本中受支持。

---

## <a name="manage-soft-deleted-blobs-and-directories-hierarchical-namespace"></a>管理软删除的 Blob 和目录（分层命名空间）

可以在拥有分层命名空间的帐户中还原软删除的 Blob 和目录。

> [!IMPORTANT]
> 在已启用分层命名空间功能的帐户中进行的软删除目前为预览版，并在全球所有 Azure 区域内均可用。
> 有关 beta 版本、预览版或尚未正式发布的版本的 Azure 功能所适用的法律条款，请参阅 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
>
>
> 若要注册预览版，请参阅[此表单](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR4mEEwKhLjlBjU3ziDwLH-pUOVRVOUpDRUtHVUtDUUtMVTZUR0tUMjZWNy4u)。

### <a name="manage-soft-deleted-blobs-with-the-azure-portal"></a>使用 Azure 门户管理软删除的 Blob

可以使用 Azure 门户来查看和还原软删除的 Blob 与目录。

#### <a name="view-deleted-blobs-and-directories"></a>查看删除的 Blob 和目录

将 Blob 或目录软删除后，默认情况下它们不会显示在 Azure 门户中。 若要查看软删除的 Blob 和目录，请导航到容器的“概述”页，并切换“显示已删除的 Blob”设置 。 软删除的 Blob 和目录显示为“已删除”状态。 下图显示一个软删除的目录。

> [!div class="mx-imgBorder"]
> ![显示如何在 Azure 门户中列出软删除的 Blob（已启用分层命名空间的帐户）的屏幕截图。](media/soft-delete-blob-manage/soft-deleted-blobs-list-portal-hns.png)

> [!NOTE]
> 如果重命名包含软删除项（子目录和 Blob）的目录，则这些软删除项会与目录断开连接，因此当切换“显示已删除 Blob”设置时，它们不会显示在 Azure 门户中。 如果要在 Azure 门户中查看它们，则必须将目录的名称还原为原始名称，或者创建一个使用原始目录名称的单独目录。

接下来，从列表中选择已删除的目录或 Blob，以显示其属性。 在“概述”选项卡下，可以看到状态设置为“已删除” 。 门户还会显示在永久删除该 Blob 之前剩余的天数。

> [!div class="mx-imgBorder"]
> ![显示 Azure 门户中软删除的 Blob 的属性（已启用分层命名空间的帐户）的屏幕截图。](media/soft-delete-blob-manage/soft-deleted-blob-properties-portal-hns.png)

#### <a name="restore-soft-delete-blobs-and-directories"></a>还原软删除的 Blob 和目录

若要在 Azure 门户中还原软删除的 Blob 或目录，请首先显示 Blob 或目录的属性，然后选择“概述”选项卡上的“取消删除”按钮。下图显示了软删除的目录上的“取消删除”按钮。 

> [!div class="mx-imgBorder"]
> ![显示如何在 Azure 门户中还原软删除的 Blob（已启用分层命名空间的帐户）的屏幕截图。](media/soft-delete-blob-manage/undelete-soft-deleted-blob-portal-hns.png)

### <a name="restore-soft-deleted-blobs-and-directories-by-using-powershell"></a>使用 PowerShell 还原软删除的 Blob 和目录

> [!IMPORTANT]
> 本部分仅适用于具有分层命名空间的帐户。

1. 请确保已安装“Az.Storage”预览版模块。 有关详细信息，请参阅[通过 PowerShell 启用 Blob 软删除](soft-delete-blob-enable.md?tabs=azure-powershell#enable-blob-soft-delete-hierarchical-namespace)。

2. 使用存储帐户密钥、连接字符串或 Azure Active Directory (Azure AD) 获取存储帐户授权。 有关详细信息，请参阅[连接到帐户](data-lake-storage-directory-file-acl-powershell.md#connect-to-the-account)。

   以下示例使用存储帐户密钥获取授权。

   ```powershell
   $ctx = New-AzStorageContext -StorageAccountName '<storage-account-name>' -StorageAccountKey '<storage-account-key>'
   ```

3. 若要还原软删除项，请使用 `Restore-AzDataLakeGen2DeletedItem` 命令。

   ```powershell
   $filesystemName = "my-file-system"
   $dirName="my-directory"
   $deletedItems = Get-AzDataLakeGen2DeletedItem -Context $ctx -FileSystem $filesystemName -Path $dirName
   $deletedItems | Restore-AzDataLakeGen2DeletedItem
   ```

   如果重命名包含软删除项的目录，则这些项会与目录断开连接。 如果要还原这些项，则必须将目录的名称还原为原始名称，或者创建一个使用原始目录名称的单独目录。 否则，在尝试还原这些软删除项时，会出现错误。

### <a name="restore-soft-deleted-blobs-and-directories-by-using-azure-cli"></a>使用 Azure CLI 还原软删除的 Blob 和目录

> [!IMPORTANT]
> 本部分仅适用于具有分层命名空间的帐户。

1. 请确保已安装了 `storage-preview` 扩展。 有关详细信息，请参阅[通过使用 PowerShell 启用 Blob 软删除](soft-delete-blob-enable.md?tabs=azure-CLI#enable-blob-soft-delete-hierarchical-namespace)。

2. 获取删除项列表。

   ```azurecli
   $filesystemName = "my-file-system"
   az storage fs list-deleted-path -f $filesystemName --auth-mode login
   ```

3. 若要还原某个项，请使用 `az storage fs undelete-path` 命令。

   ```azurecli
   $dirName="my-directory"
   az storage fs undelete-path -f $filesystemName --deleted-path-name $dirName -deletion-id "<deletionId>" --auth-mode login
   ```

   如果重命名包含软删除项的目录，则这些项会与目录断开连接。 如果要还原这些项，则必须将目录的名称还原为原始名称，或者创建一个使用原始目录名称的单独目录。 否则，在尝试还原这些软删除项时，会出现错误。

### <a name="restore-soft-deleted-blobs-and-directories-by-using-net"></a>使用 .NET 还原软删除的 Blob 和目录

> [!IMPORTANT]
> 本部分仅适用于具有分层命名空间的帐户。

1. 请打开命令提示符或将目录 (`cd`) 更改为项目文件夹，例如：

   ```console
   cd myProject
   ```

2. 使用 `dotnet add package` 命令安装 `Azure.Storage.Files.DataLake -v 12.7.0` 版本的 [Azure.Storage.Files.DataLake](https://www.nuget.org/packages/Azure.Storage.Files.DataLake/) NuGet 包。

   ```console
   dotnet add package Azure.Storage.Files.DataLake -v -v 12.7.0 -s https://pkgs.dev.azure.com/azure-sdk/public/_packaging/azure-sdk-for-net/nuget/v3/index.json
   ```

3. 然后，将这些 using 语句添加到代码文件的顶部。

    ```csharp
    using Azure;
    using Azure.Storage;
    using Azure.Storage.Files.DataLake;
    using Azure.Storage.Files.DataLake.Models;
    using NUnit.Framework;
    using System;
    using System.Collections.Generic;
    using System.Threading.Tasks;
    ```

4. 以下代码将删除目录，然后还原软删除的目录。

   此方法假定你已创建 [DataLakeServiceClient](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) 实例。 若要了解如何创建 [DataLakeServiceClient](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) 实例，请参阅[连接到帐户](data-lake-storage-directory-file-acl-dotnet.md#connect-to-the-account)。

   ```csharp
      public void RestoreDirectory(DataLakeServiceClient serviceClient)
      {
          DataLakeFileSystemClient fileSystemClient =
             serviceClient.GetFileSystemClient("my-container");

          DataLakeDirectoryClient directory =
              fileSystem.GetDirectoryClient("my-directory");

          // Delete the Directory
          await directory.DeleteAsync();

          // List Deleted Paths
          List<PathHierarchyDeletedItem> deletedItems = new List<PathHierarchyDeletedItem>();
          await foreach (PathHierarchyDeletedItem deletedItem in fileSystemClient.GetDeletedPathsAsync())
          {
            deletedItems.Add(deletedItem);
          }

          Assert.AreEqual(1, deletedItems.Count);
          Assert.AreEqual("my-directory", deletedItems[0].Path.Name);
          Assert.IsTrue(deletedItems[0].IsPath);

          // Restore deleted directory.
          Response<DataLakePathClient> restoreResponse = await fileSystemClient.RestorePathAsync(
          deletedItems[0].Path.Name,
          deletedItems[0].Path.DeletionId);

      }

   ```

   如果重命名包含软删除项的目录，则这些项会与目录断开连接。 如果要还原这些项，则必须将目录的名称还原为原始名称，或者创建一个使用原始目录名称的单独目录。 否则，在尝试还原这些软删除项时，会出现错误。

### <a name="restore-soft-deleted-blobs-and-directories-by-using-java"></a>使用 Java 还原软删除的 Blob 和目录

> [!IMPORTANT]
> 本部分仅适用于具有分层命名空间的帐户。

1. 若要开始操作，请在文本编辑器中打开 pom.xml 文件。 将以下依赖项元素添加到依赖项组。

   ```xml
   <dependency>
     <groupId>com.azure</groupId>
     <artifactId>azure-storage-file-datalake</artifactId>
     <version>12.6.0</version>
   </dependency>
   ```

2. 然后将这些 imports 语句添加到代码文件。

   ```java
   Put imports here
   ```

3. 以下代码片段将还原名为 `my-file` 的软删除的文件。

   此方法假定你已创建 **DataLakeServiceClient** 实例。 若要了解如何创建 **DataLakeServiceClient** 实例，请参阅 [连接到帐户](data-lake-storage-directory-file-acl-java.md#connect-to-the-account)。

   ```java

   public void RestoreFile(DataLakeServiceClient serviceClient){

       DataLakeFileSystemClient fileSystemClient =
           serviceClient.getFileSystemClient("my-container");

       DataLakeFileClient fileClient =
           fileSystemClient.getFileClient("my-file");

       String deletionId = null;

       for (PathDeletedItem item : fileSystemClient.listDeletedPaths()) {

           if (item.getName().equals(fileClient.getFilePath())) {
              deletionId = item.getDeletionId();
           }
       }

       fileSystemClient.restorePath(fileClient.getFilePath(), deletionId);
    }

   ```

   如果重命名包含软删除项的目录，则这些项会与目录断开连接。 如果要还原这些项，则必须将目录的名称还原为原始名称，或者创建一个使用原始目录名称的单独目录。 否则，在尝试还原这些软删除项时，会出现错误。

### <a name="restore-soft-deleted-blobs-and-directories-by-using-python"></a>使用 Python 还原软删除的 Blob 和目录

> [!IMPORTANT]
> 本部分仅适用于具有分层命名空间的帐户。

1. 使用 [pip](https://pypi.org/project/pip/) 安装适用于 Python 的 Azure Data Lake Storage 客户端库 `12.4.0` 或更高版本。 此命令将安装适用于 Python 的 Azure Data Lake Storage 客户端库的最新版本。

   ```
   pip install azure-storage-file-datalake
   ```

2. 将这些 import 语句添加到代码文件的顶部。

   ```python
   import os, uuid, sys
   from azure.storage.filedatalake import DataLakeServiceClient
   from azure.storage.filedatalake import FileSystemClient
   ```

3. 以下代码将删除目录，然后还原软删除的目录。

   以下代码示例包含名为 `service_client`、类型为 **DataLakeServiceClient** 的对象。 若要查看如何创建 **DataLakeServiceClient** 实例的示例，请参阅 [连接到帐户](data-lake-storage-directory-file-acl-python.md#connect-to-the-account)。

    ```python
    def restoreDirectory():

        try:
            global file_system_client

            file_system_client = service_client.create_file_system(file_system="my-file-system")

            directory_path = 'my-directory'
            directory_client = file_system_client.create_directory(directory_path)
            resp = directory_client.delete_directory()

            restored_directory_client = file_system_client.undelete_path(directory_client, resp['deletion_id'])
            props = restored_directory_client.get_directory_properties()

            print(props)

        except Exception as e:
            print(e)

    ```

   如果重命名包含软删除项的目录，则这些项会与目录断开连接。 如果要还原这些项，则必须将目录的名称还原为原始名称，或者创建一个使用原始目录名称的单独目录。 否则，在尝试还原这些软删除项时，会出现错误。

## <a name="next-steps"></a>后续步骤

- [Blob 存储的软删除](soft-delete-blob-overview.md)
- [为 blob 启用软删除](soft-delete-blob-enable.md)
- [Blob 版本控制](versioning-overview.md)
