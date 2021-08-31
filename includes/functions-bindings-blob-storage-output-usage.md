---
title: include 文件
description: include 文件
services: functions
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.topic: include
ms.date: 08/02/2019
ms.author: cshoe
ms.custom: include file
ms.openlocfilehash: 4a25423cbdd5b0efbd037138a3cdf072cb90ebed
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121802677"
---
### <a name="default"></a>默认

可绑定到以下类型以编写 blob：

* `TextWriter`
* `out string`
* `out Byte[]`
* `CloudBlobStream`
* `Stream`
* `CloudBlobContainer`<sup>1</sup>
* `CloudBlobDirectory`
* `ICloudBlob`<sup>2</sup>
* `CloudBlockBlob`<sup>2</sup>
* `CloudPageBlob`<sup>2</sup>
* `CloudAppendBlob`<sup>2</sup>

<sup>1</sup> function.json 中需有 "in" 绑定 `direction` 或 C# 类库中需有 `FileAccess.Read`。 但是，可以使用运行时提供的容器对象来执行写入操作，例如将 Blob 上传到容器。

<sup>2</sup> function.json 中需有 "inout" 绑定 `direction` 或 C# 类库中需有 `FileAccess.ReadWrite`。

如果尝试绑定到某个存储 SDK 类型并收到错误消息，请确保已引用[正确的存储 SDK 版本](../articles/azure-functions/functions-bindings-storage-blob.md#azure-storage-sdk-version-in-functions-1x)。

由于整个 Blob 内容都会加载到内存中，因此，只有当 Blob 较小时才建议绑定到 `string` 或 `Byte[]`。 平时，最好使用 `Stream` 或 `CloudBlockBlob` 类型。 有关详细信息，请参阅本文前文中的[并发和内存使用情况](../articles/azure-functions/functions-bindings-storage-blob-trigger.md#concurrency-and-memory-usage)。

### <a name="additional-types"></a>其他类型

应用如果使用 [5.0.0 版或更高版本的存储扩展](../articles/azure-functions/functions-bindings-storage-blob.md#storage-extension-5x-and-higher)，还可以使用[用于 .NET 的 Azure SDK](/dotnet/api/overview/azure/storage.blobs-readme) 中的类型。 此版本为了支持以下类型，删除了对旧的 `CloudBlobContainer`、`CloudBlobDirectory`、`ICloudBlob``CloudBlockBlob`、`CloudPageBlob` 和 `CloudAppendBlob` 类型的支持：

- [BlobContainerClient](/dotnet/api/azure.storage.blobs.blobcontainerclient)<sup>1</sup>
- [BlobClient](/dotnet/api/azure.storage.blobs.blobclient)<sup>2</sup>
- [BlockBlobClient](/dotnet/api/azure.storage.blobs.specialized.blockblobclient)<sup>2</sup>
- [PageBlobClient](/dotnet/api/azure.storage.blobs.specialized.pageblobclient)<sup>2</sup>
- [AppendBlobClient](/dotnet/api/azure.storage.blobs.specialized.appendblobclient)<sup>2</sup>
- [BlobBaseClient](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient)<sup>2</sup>

<sup>1</sup> function.json 中需有 "in" 绑定 `direction` 或 C# 类库中需有 `FileAccess.Read`。 但是，可以使用运行时提供的容器对象来执行写入操作，例如将 Blob 上传到容器。

<sup>2</sup> function.json 中需有 "inout" 绑定 `direction` 或 C# 类库中需有 `FileAccess.ReadWrite`。

有关使用这些类型的示例，请参阅[扩展的 GitHub 存储库](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Microsoft.Azure.WebJobs.Extensions.Storage.Blobs#examples)。 详细了解这些新类型的不同之处以及如何迁移到它们，请参阅 [Azure.Storage.Blobs 迁移指南](https://github.com/Azure/azure-sdk-for-net/blob/main/sdk/storage/Azure.Storage.Blobs/AzureStorageNetMigrationV12.md)。
