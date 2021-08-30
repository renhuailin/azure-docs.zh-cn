---
title: 使用 Blob 索引标记管理和查找 Azure Blob 存储上的数据
description: 查看相关示例，了解如何使用 Blob 索引标记对 Blob 对象进行分类、管理和查询。
author: normesta
ms.author: normesta
ms.date: 06/14/2021
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.reviewer: klaasl
ms.custom: devx-track-csharp
ms.openlocfilehash: 0814f2896ec429650668a08590ffe7165fb120a3
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/17/2021
ms.locfileid: "112282018"
---
# <a name="use-blob-index-tags-to-manage-and-find-data-on-azure-blob-storage"></a>使用 Blob 索引标记管理和查找 Azure Blob 存储上的数据

Blob 索引标记使用键值标记属性对存储帐户中的数据进行分类。 这些标记会自动索引，并作为可搜索的多维索引公开，便于你轻松查找数据。 本文介绍了如何使用 blob 索引标记来设置、获取和查找数据。

要详细了解此功能以及已知问题和限制，请参阅[通过 Blob 索引标记管理和查找 Azure Blob 数据](storage-manage-find-blobs.md)。

## <a name="prerequisites"></a>先决条件

# <a name="portal"></a>[Portal](#tab/azure-portal)

- 已注册且获准可访问的 Azure 订阅
- 可访问对 [Azure 门户](https://portal.azure.com/)

# <a name="net-v12-sdk"></a>[.NET v12 SDK](#tab/net)

1. 设置 Visual Studio 项目，开始使用适用于 .NET 的 Azure Blob 存储客户端库 v12。 若要了解详细信息，请参阅 [.NET 快速入门](storage-quickstart-blobs-dotnet.md)

2. 在 NuGet 包管理器中，查找 Azure.Storage.Blobs 包，并将 12.7.0 版本或更新版本安装到你的项目 。 还可运行 PowerShell 命令：`Install-Package Azure.Storage.Blobs -Version 12.7.0`

   若要了解如何操作，请参阅[查找并安装包](/nuget/consume-packages/install-use-packages-visual-studio#find-and-install-a-package)。

3. 将下列 using 语句添加到你的代码文件的顶部。

    ```csharp
    using Azure;
    using Azure.Storage.Blobs;
    using Azure.Storage.Blobs.Models;
    using Azure.Storage.Blobs.Specialized;
    using System;
    using System.Collections.Generic;
    using System.Threading.Tasks;
    ```

---

## <a name="upload-a-new-blob-with-index-tags"></a>上传带有索引标记的新 blob

此任务可由[存储 Blob 数据所有者](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)执行，也可由已通过自定义 Azure 角色获得 `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write` [Azure 资源提供商操作](../../role-based-access-control/resource-provider-operations.md#microsoftstorage)权限的安全主体来执行。

# <a name="portal"></a>[门户](#tab/azure-portal)

1. 在 [Azure 门户](https://portal.azure.com/)中，选择你的存储帐户。 

2. 导航到“数据存储”下的“容器”选项，然后选择你的容器 。

3. 选择“上传”按钮，并浏览本地文件系统，找到要作为块 blob 上传的文件。

4. 展开“高级”下拉列表，然后转到“Blob 索引标记”部分 。

5. 输入要应用于数据的键/值 Blob 索引标记。

6. 选择“上传”按钮以上传 Blob。 

:::image type="content" source="media/storage-blob-index-concepts/blob-index-upload-data-with-tags.png" alt-text="Azure 门户的屏幕截图，其中显示如何通过索引标记上传 Blob。":::

# <a name="net-v12-sdk"></a>[.NET v12 SDK](#tab/net)

下面的示例展示了如何使用在创建过程中设置的标记创建一个追加 blob。

```csharp
static async Task BlobIndexTagsOnCreate()
   {
      BlobServiceClient serviceClient = new BlobServiceClient(ConnectionString);
      BlobContainerClient container = serviceClient.GetBlobContainerClient("mycontainer");

      try
      {
          // Create a container
          await container.CreateIfNotExistsAsync();

          // Create an append blob
          AppendBlobClient appendBlobWithTags = container.GetAppendBlobClient("myAppendBlob0.logs");

          // Blob index tags to upload
          AppendBlobCreateOptions appendOptions = new AppendBlobCreateOptions();
          appendOptions.Tags = new Dictionary<string, string>
          {
              { "Sealed", "false" },
              { "Content", "logs" },
              { "Date", "2020-04-20" }
          };

          // Upload data with tags set on creation
          await appendBlobWithTags.CreateAsync(appendOptions);
      }
      finally
      {
      }
   }
```

---

## <a name="get-set-and-update-blob-index-tags"></a>获取、设置和更新 blob 索引标记

获取 Blob 索引标记的操作可由[存储 Blob 数据所有者](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)执行，也可由已通过自定义 Azure 角色获得 `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/read` [Azure 资源提供商操作](../../role-based-access-control/resource-provider-operations.md#microsoftstorage)权限的安全主体来执行。

设置和更新 Blob 索引标记的操作可由[存储 Blob 数据所有者](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)执行，也可由已通过自定义 Azure 角色获得 `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write` [Azure 资源提供商操作](../../role-based-access-control/resource-provider-operations.md#microsoftstorage)权限的安全主体来执行。

# <a name="portal"></a>[门户](#tab/azure-portal)

1. 在 [Azure 门户](https://portal.azure.com/)中，选择你的存储帐户。

2. 导航到“数据存储”下的“容器”选项，选择你的容器 。

3. 从所选容器的 Blob 列表中选择你的 Blob。

4. Blob 概览选项卡将显示 Blob 的属性，包括任何“Blob 索引标记”。

5. 可以获取、设置、修改或删除 Blob 的任何键/值索引标记。

6. 选择“保存”按钮以确认对 Blob 所做的任何更新。

:::image type="content" source="media/storage-blob-index-concepts/blob-index-get-set-tags.png" alt-text="Azure 门户的屏幕截图，其中显示如何获取、设置、更新和删除 Blob 上的索引标记。":::

# <a name="net-v12-sdk"></a>[.NET v12 SDK](#tab/net)

```csharp
static async Task BlobIndexTagsExample()
   {
      BlobServiceClient serviceClient = new BlobServiceClient(ConnectionString);
      BlobContainerClient container = serviceClient.GetBlobContainerClient("mycontainer");

      try
      {
          // Create a container
          await container.CreateIfNotExistsAsync();

          // Create a new append blob
          AppendBlobClient appendBlob = container.GetAppendBlobClient("myAppendBlob1.logs");
          await appendBlob.CreateAsync();

          // Set or update blob index tags on existing blob
          Dictionary<string, string> tags = new Dictionary<string, string>
          {
              { "Project", "Contoso" },
              { "Status", "Unprocessed" },
              { "Sealed", "true" }
          };
          await appendBlob.SetTagsAsync(tags);

          // Get blob index tags
          Response<IDictionary<string, string>> tagsResponse = await appendBlob.GetTagsAsync();
          Console.WriteLine(appendBlob.Name);
          foreach (KeyValuePair<string, string> tag in tagsResponse.Value)
          {
              Console.WriteLine($"{tag.Key}={tag.Value}");
          }

          // List blobs with all options returned including blob index tags
          await foreach (BlobItem blobItem in container.GetBlobsAsync(BlobTraits.All))
          {
              Console.WriteLine(Environment.NewLine + blobItem.Name);
              foreach (KeyValuePair<string, string> tag in blobItem.Tags)
              {
                  Console.WriteLine($"{tag.Key}={tag.Value}");
              }
          }

          // Delete existing blob index tags by replacing all tags
          Dictionary<string, string> noTags = new Dictionary<string, string>();
          await appendBlob.SetTagsAsync(noTags);

      }
      finally
      {
      }
   }
```

---

## <a name="filter-and-find-data-with-blob-index-tags"></a>通过 Blob 索引标记筛选和查找数据

此任务可由[存储 Blob 数据所有者](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)执行，也可由已通过自定义 Azure 角色获得 `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/filter/action` [Azure 资源提供商操作](../../role-based-access-control/resource-provider-operations.md#microsoftstorage)权限的安全主体来执行。

# <a name="portal"></a>[Portal](#tab/azure-portal)

在 Azure 门户中，“Blob 索引标记”筛选器会自动应用 `@container` 参数来限制所选容器的范围。 如果希望在整个存储帐户中筛选和查找带标记的数据，请使用我们的 REST API、SDK 或工具。

1. 在 [Azure 门户](https://portal.azure.com/)中，选择存储帐户。 

2. 导航到“数据存储”下的“容器”选项，选择你的容器 。

3. 选择“Blob 索引标记筛选器”按钮，在所选容器内进行筛选。

4. 输入 Blob 索引标记键和标记值。

5. 选择“Blob 索引标记筛选器”按钮，以添加更多标记筛选器（最多 10 个）。

:::image type="content" source="media/storage-blob-index-concepts/blob-index-tag-filter-within-container.png" alt-text="Azure 门户的屏幕截图，其中显示如何使用索引标记筛选和查找带标记的 Blob":::

# <a name="net-v12-sdk"></a>[.NET v12 SDK](#tab/net)

```csharp
static async Task FindBlobsByTagsExample()
   {
      BlobServiceClient serviceClient = new BlobServiceClient(ConnectionString);
      BlobContainerClient container1 = serviceClient.GetBlobContainerClient("mycontainer");
      BlobContainerClient container2 = serviceClient.GetBlobContainerClient("mycontainer2");

      // Blob index queries and selection
      String singleEqualityQuery = @"""Archive"" = 'false'";
      String andQuery = @"""Archive"" = 'false' AND ""Priority"" = '01'";
      String rangeQuery = @"""Date"" >= '2020-04-20' AND ""Date"" <= '2020-04-30'";
      String containerScopedQuery = @"@container = 'mycontainer' AND ""Archive"" = 'false'";

      String queryToUse = containerScopedQuery;

      try
      {
          // Create a container
          await container1.CreateIfNotExistsAsync();
          await container2.CreateIfNotExistsAsync();

          // Create append blobs
          AppendBlobClient appendBlobWithTags0 = container1.GetAppendBlobClient("myAppendBlob00.logs");
          AppendBlobClient appendBlobWithTags1 = container1.GetAppendBlobClient("myAppendBlob01.logs");
          AppendBlobClient appendBlobWithTags2 = container1.GetAppendBlobClient("myAppendBlob02.logs");
          AppendBlobClient appendBlobWithTags3 = container2.GetAppendBlobClient("myAppendBlob03.logs");
          AppendBlobClient appendBlobWithTags4 = container2.GetAppendBlobClient("myAppendBlob04.logs");
          AppendBlobClient appendBlobWithTags5 = container2.GetAppendBlobClient("myAppendBlob05.logs");
           
          // Blob index tags to upload
          CreateAppendBlobOptions appendOptions = new CreateAppendBlobOptions();
          appendOptions.Tags = new Dictionary<string, string>
          {
              { "Archive", "false" },
              { "Priority", "01" },
              { "Date", "2020-04-20" }
          };
          
          CreateAppendBlobOptions appendOptions2 = new CreateAppendBlobOptions();
          appendOptions2.Tags = new Dictionary<string, string>
          {
              { "Archive", "true" },
              { "Priority", "02" },
              { "Date", "2020-04-24" }
          };

          // Upload data with tags set on creation
          await appendBlobWithTags0.CreateAsync(appendOptions);
          await appendBlobWithTags1.CreateAsync(appendOptions);
          await appendBlobWithTags2.CreateAsync(appendOptions2);
          await appendBlobWithTags3.CreateAsync(appendOptions);
          await appendBlobWithTags4.CreateAsync(appendOptions2);
          await appendBlobWithTags5.CreateAsync(appendOptions2);

          // Find Blobs given a tags query
          Console.WriteLine("Find Blob by Tags query: " + queryToUse + Environment.NewLine);

          List<TaggedBlobItem> blobs = new List<TaggedBlobItem>();
          await foreach (TaggedBlobItem taggedBlobItem in serviceClient.FindBlobsByTagsAsync(queryToUse))
          {
              blobs.Add(taggedBlobItem);
          }

          foreach (var filteredBlob in blobs)
          {
              Console.WriteLine($"BlobIndex result: ContainerName= {filteredBlob.ContainerName}, " +
                  $"BlobName= {filteredBlob.Name}");
          }

      }
      finally
      {
      }
   }
```

---

## <a name="lifecycle-management-with-blob-index-tag-filters"></a>使用 blob 索引标记筛选器进行生命周期管理

# <a name="portal"></a>[门户](#tab/azure-portal)

1. 在 [Azure 门户](https://portal.azure.com/)中，选择你的存储帐户。 

2. 导航到“Blob 服务”下的“生命周期管理”选项 

3. 选择“添加规则”，然后填写操作集窗体字段

4. 选择“筛选器”集，添加可选筛选器来进行前缀匹配和 Blob 索引匹配

  :::image type="content" source="media/storage-blob-index-concepts/blob-index-match-lifecycle-filter-set.png" alt-text="Azure 门户的屏幕截图，其中显示如何为生命周期管理添加索引标记。":::

5. 选择“查看 + 添加”以查看规则设置

  :::image type="content" source="media/storage-blob-index-concepts/blob-index-lifecycle-management-example.png" alt-text="Azure 门户的屏幕截图，其中显示生命周期管理规则及 Blob 索引标记筛选器示例":::

6. 选择“添加”，将新规则应用于生命周期管理策略

# <a name="net-v12-sdk"></a>[.NET v12 SDK](#tab/net)

[生命周期管理](storage-lifecycle-management-concepts.md)策略在控制平面级别应用于每个存储帐户。 对于 .NET，请安装 [Microsoft Azure 管理存储库](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage/)版本 16.0.0 或更高版本。

---

## <a name="next-steps"></a>后续步骤

 - 有关 Blob 索引标记的详细信息，请参阅[通过 Blob 索引标记管理和查找 Azure Blob 数据](storage-manage-find-blobs.md )
 - 有关生命周期管理的详细信息，请参阅[管理 Azure Blob 存储生命周期](storage-lifecycle-management-concepts.md)
