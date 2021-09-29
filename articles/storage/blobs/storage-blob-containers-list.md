---
title: 使用 .NET 列出 Blob 容器 - Azure 存储
description: 了解如何使用 .NET 客户端库列出 Azure 存储帐户中的 Blob 容器。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 10/14/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: b6caf7170130b75689de2915e5d15c8484ba231a
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128680198"
---
# <a name="list-blob-containers-with-net"></a>使用 .NET 列出 Blob 容器

通过代码列出 Azure 存储帐户中的容器时，可以指定多个选项来管理如何从 Azure 存储返回结果。 本文介绍如何使用[适用于 .NET 的 Azure 存储客户端库](/dotnet/api/overview/azure/storage)列出容器。

## <a name="understand-container-listing-options"></a>了解容器列出选项

若要列出存储帐户中的容器，请调用以下方法之一：

# <a name="net-v12-sdk"></a>[.NET v12 SDK](#tab/dotnet)

- [GetBlobContainers](/dotnet/api/azure.storage.blobs.blobserviceclient.getblobcontainers)
- [GetBlobContainersAsync](/dotnet/api/azure.storage.blobs.blobserviceclient.getblobcontainersasync)

# <a name="net-v11-sdk"></a>[.NET v11 SDK](#tab/dotnet11)

- [ListContainersSegmented](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listcontainerssegmented)
- [ListContainersSegmentedAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listcontainerssegmentedasync)

---

这些方法的重载提供更多选项用于管理列出操作返回容器的方式。 后续部分将介绍这些选项。

### <a name="manage-how-many-results-are-returned"></a>管理要返回的结果数

默认情况下，列出操作每次最多返回 5000 个结果。 若要返回更小的结果集，请为要返回的结果页的大小提供非零值。

如果存储帐户包含 5000 个以上的容器，或者你指定了页面大小，以便列表操作返回存储帐户中的容器子集，则 Azure 存储将返回一个包含容器列表的延续令牌。 继续标记是一个不透明值，可用于从 Azure 存储中检索下一组结果。

在代码中检查延续令牌的值，以确定它是为空（适用于 .NET v12）还是为 NULL（适用于 .NET v11 及更低版本）。 如果继续标记为 null，则表示结果集是完整的。 如果延续令牌不为 NULL，则再次调用列出方法，并传入延续令牌以检索下一组结果，直到延续令牌为 NULL。

### <a name="filter-results-with-a-prefix"></a>使用前缀筛选结果

若要筛选容器列表，请为 `prefix` 参数指定一个字符串。 前缀字符串可以包含一个或多个字符。 然后，Azure 存储只返回其名称以该前缀开头的容器。

### <a name="return-metadata"></a>返回元数据

若要将容器元数据与结果一起返回，请为 [BlobContainerTraits](/dotnet/api/azure.storage.blobs.models.blobcontainertraits) 枚举（适用于 .NET v12）或 [ContainerListingDetails](/dotnet/api/microsoft.azure.storage.blob.containerlistingdetails) 枚举（适用于 .NET v11 及更低版本）指定“Metadata”值）。 Azure 存储包含返回的每个容器的元数据，因此你也不需要提取容器元数据。

## <a name="example-list-containers"></a>示例：列出容器

以下示例以异步方式列出存储帐户中以指定的前缀开头的容器。 该示例列出了以指定前缀开头的容器，并且每次对列表操作的调用均返回指定数量的结果。 然后，它使用延续令牌获取下一段结果。 该示例还会连同结果一起返回容器元数据。

# <a name="net-v12-sdk"></a>[.NET v12 SDK](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Containers.cs" id="Snippet_ListContainers":::

# <a name="net-v11-sdk"></a>[.NET v11 SDK](#tab/dotnet11)

```csharp
private static async Task ListContainersWithPrefixAsync(CloudBlobClient blobClient,
                                                        string prefix,
                                                        int? segmentSize)
{
    Console.WriteLine("List containers beginning with prefix {0}, plus container metadata:", prefix);

    BlobContinuationToken continuationToken = null;
    ContainerResultSegment resultSegment;

    try
    {
        do
        {
            // List containers beginning with the specified prefix,
            // returning segments of 5 results each.
            // Passing in null for the maxResults parameter returns the maximum number of results (up to 5000).
            // Requesting the container's metadata as part of the listing operation populates the metadata,
            // so it's not necessary to call FetchAttributes() to read the metadata.
            resultSegment = await blobClient.ListContainersSegmentedAsync(
                prefix, ContainerListingDetails.Metadata, segmentSize, continuationToken, null, null);

            // Enumerate the containers returned.
            foreach (var container in resultSegment.Results)
            {
                Console.WriteLine("\tContainer:" + container.Name);

                // Write the container's metadata keys and values.
                foreach (var metadataItem in container.Metadata)
                {
                    Console.WriteLine("\t\tMetadata key: " + metadataItem.Key);
                    Console.WriteLine("\t\tMetadata value: " + metadataItem.Value);
                }
            }

            // Get the continuation token.
            continuationToken = resultSegment.ContinuationToken;

        } while (continuationToken != null);

        Console.WriteLine();
    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

---

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="see-also"></a>请参阅

- [列出容器](/rest/api/storageservices/list-containers2)
- [枚举 Blob 资源](/rest/api/storageservices/enumerating-blob-resources)
