---
title: 充当事件网格源的 Azure Blob 存储
description: 介绍为 Azure 事件网格 Blob 存储事件提供的属性
ms.topic: conceptual
ms.date: 05/12/2021
ms.openlocfilehash: 37637a486bd80e9d0018495e6fd4713bab36e8ff
ms.sourcegitcommit: bd65925eb409d0c516c48494c5b97960949aee05
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/06/2021
ms.locfileid: "111541968"
---
# <a name="azure-blob-storage-as-an-event-grid-source"></a>充当事件网格源的 Azure Blob 存储

本文提供 Blob 存储事件的属性和架构。  有关事件架构的简介，请参阅 [Azure 事件网格事件架构](event-schema.md)。 它还提供了一个快速入门和教程的列表，这些快速入门和教程介绍如何使用 Azure Blob 存储作为事件源。


>[!NOTE]
> 只有种类为“StorageV2 (常规用途 v2)”、“BlockBlobStorage”和“BlobStorage”的存储帐户支持事件集成。   “存储(常规用途 v1)”不支持与事件网格集成。

## <a name="available-event-types"></a>可用事件类型

### <a name="list-of-events-for-blob-rest-apis"></a>Blob REST API 的事件列表

当客户端通过调用 Blob REST API 创建、替换或删除 Blob 时，将触发这些事件。

> [!NOTE]
> `$logs` 和 `$blobchangefeed` 容器未与事件网格集成，因此这些容器中的活动将不会生成事件。 此外，将 DFS 终结点 `(abfss://URI) ` 用于启用了非分层命名空间的帐户将不会生成事件，但 blob 终结点 `(wasb:// URI)` 会生成事件。

 |事件名称 |说明|
 |----------|-----------|
 |**Microsoft.Storage.BlobCreated** |创建或替换 Blob 时触发。 <br>具体而言，当客户端使用 Blob REST API 中可用的 `PutBlob`、`PutBlockList` 或 `CopyBlob` 操作，并且当块 blob 完全提交时，将触发此事件。 <br>如果客户端对启用了分层命名空间功能的帐户使用 `CopyBlob` 操作，则 `CopyBlob` 操作的工作方式略有不同。 在这种情况下，Microsoft.Storage.BlobCreated 事件会在 `CopyBlob` 操作启动时触发，而不是在块 blob 完全提交时触发。   |
 |**Microsoft.Storage.BlobDeleted** |删除 Blob 时触发。 <br>具体而言，当客户端调用 Blob REST API 中可用的 `DeleteBlob` 操作时，将触发此事件。 |
 |**Microsoft.Storage.BlobTierChanged** |在更改 blob 访问层时触发。 具体而言，当客户端调用 Blob REST API 中可用的 `Set Blob Tier` 操作时，在层更改完成后会触发此事件。 |

### <a name="list-of-the-events-for-azure-data-lake-storage-gen-2-rest-apis"></a>Azure Data Lake Storage Gen 2 REST API 的事件列表

如果在存储帐户上启用分层命名空间，并且客户端使用 Azure Data Lake Storage Gen2 REST API，则会触发这些事件。 有关 Azure Data Lake Storage Gen2 的详细信息，请参阅 [Azure Data Lake Storage Gen2 简介](../storage/blobs/data-lake-storage-introduction.md)。

|事件名称|说明|
|----------|-----------|
|**Microsoft.Storage.BlobCreated** | 创建或替换 Blob 时触发。 <br>具体而言，当客户端使用 Azure Data Lake Storage Gen2 REST API 中提供的 `CreateFile` 和 `FlushWithClose` 操作时，会触发此事件。 |
|**Microsoft.Storage.BlobDeleted** |删除 Blob 时触发。 <br>具体而言，当客户端调用 Azure Data Lake Storage Gen2 REST API 中提供的 `DeleteFile` 操作时，也会触发此事件。 |
|**Microsoft.Storage.BlobRenamed**|重命名 Blob 时触发。 <br>具体而言，当客户端使用 Azure Data Lake Storage Gen2 REST API 中提供的 `RenameFile` 操作时，会触发此事件。|
|**Microsoft.Storage.DirectoryCreated**|创建目录时触发。 <br>具体而言，当客户端使用 Azure Data Lake Storage Gen2 REST API 中提供的 `CreateDirectory` 操作时，会触发此事件。|
|**Microsoft.Storage.DirectoryRenamed**|重命名目录时触发。 <br>具体而言，当客户端使用 Azure Data Lake Storage Gen2 REST API 中提供的 `RenameDirectory` 操作时，会触发此事件。|
|**Microsoft.Storage.DirectoryDeleted**|删除目录时触发。 <br>具体而言，当客户端使用 Azure Data Lake Storage Gen2 REST API 中提供的 `DeleteDirectory` 操作时，会触发此事件。|

> [!NOTE]
> 对于 Azure Data Lake Storage Gen2，若要确保 Microsoft.Storage.BlobCreated 事件仅在块 Blob 完全提交后触发，请为 `FlushWithClose` REST API 调用筛选此事件。 此 API 调用仅在数据已完全提交到块 Blob 后才触发 **Microsoft.Storage.BlobCreated** 事件。 若要了解如何创建筛选器，请参阅[筛选事件网格的事件](./how-to-filter-events.md)。

## <a name="example-event"></a>示例事件
触发某个事件后，事件网格服务会将有关该事件的数据发送到订阅终结点。 本部分包含有关每个 Blob 存储事件的数据外观示例。

# <a name="event-grid-event-schema"></a>[事件网格事件架构](#tab/event-grid-event-schema)

### <a name="microsoftstorageblobcreated-event"></a>Microsoft.Storage.BlobCreated 事件

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/test-container/blobs/new-file.txt",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "PutBlockList",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "eTag": "\"0x8D4BCC2E4835CD0\"",
    "contentType": "text/plain",
    "contentLength": 524288,
    "blobType": "BlockBlob",
    "url": "https://my-storage-account.blob.core.windows.net/testcontainer/new-file.txt",
    "sequencer": "00000000000004420000000000028963",
    "storageDiagnostics": {
      "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstorageblobcreated-event-data-lake-storage-gen2"></a>Microsoft.Storage.BlobCreated 事件 (Data Lake Storage Gen2)

如果 Blob 存储帐户具有分层命名空间，则数据看起来会与上一个示例类似，但以下这些更改除外：

* `dataVersion` 键设置为值 `2`。

* `data.api` 键设置为字符串 `CreateFile` 或 `FlushWithClose`。

* `contentOffset` 键包含在数据集中。

> [!NOTE]
> 如果应用程序使用 `PutBlockList` 操作将新的 Blob 上传到帐户，则数据不会包含这些更改。

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/new-file.txt",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "CreateFile",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "eTag": "\"0x8D4BCC2E4835CD0\"",
    "contentType": "text/plain",
    "contentLength": 0,
    "contentOffset": 0,
    "blobType": "BlockBlob",
    "url": "https://my-storage-account.dfs.core.windows.net/my-file-system/new-file.txt",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "2",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstorageblobdeleted-event"></a>Microsoft.Storage.BlobDeleted 事件

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/testcontainer/blobs/file-to-delete.txt",
  "eventType": "Microsoft.Storage.BlobDeleted",
  "eventTime": "2017-11-07T20:09:22.5674003Z",
  "id": "4c2359fe-001e-00ba-0e04-58586806d298",
  "data": {
    "api": "DeleteBlob",
    "requestId": "4c2359fe-001e-00ba-0e04-585868000000",
    "contentType": "text/plain",
    "blobType": "BlockBlob",
    "url": "https://my-storage-account.blob.core.windows.net/testcontainer/file-to-delete.txt",
    "sequencer": "0000000000000281000000000002F5CA",
    "storageDiagnostics": {
      "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstorageblobdeleted-event-data-lake-storage-gen2"></a>Microsoft.Storage.BlobDeleted 事件 (Data Lake Storage Gen2)

如果 Blob 存储帐户具有分层命名空间，则数据看起来会与上一个示例类似，但以下这些更改除外：

* `dataVersion` 键设置为值 `2`。

* `data.api` 键设置为字符串 `DeleteFile`。

* `url` 键包含路径 `dfs.core.windows.net`。

> [!NOTE]
> 如果应用程序使用 `DeleteBlob` 操作从帐户中删除 Blob，则数据不会包含这些更改。

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/file-to-delete.txt",
  "eventType": "Microsoft.Storage.BlobDeleted",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
    "data": {
    "api": "DeleteFile",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "contentType": "text/plain",
    "blobType": "BlockBlob",
    "url": "https://my-storage-account.dfs.core.windows.net/my-file-system/file-to-delete.txt",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "2",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstorageblobtierchanged-event"></a>Microsoft.Storage.BlobTierChanged 事件

```json
{
    "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
    "subject": "/blobServices/default/containers/testcontainer/blobs/Auto.jpg",
    "eventType": "Microsoft.Storage.BlobTierChanged",
    "id": "0fdefc06-b01e-0034-39f6-4016610696f6",
    "data": {
        "api": "SetBlobTier",
        "clientRequestId": "68be434c-1a0d-432f-9cd7-1db90bff83d7",
        "requestId": "0fdefc06-b01e-0034-39f6-401661000000",
        "contentType": "image/jpeg",
        "contentLength": 105891,
        "blobType": "BlockBlob",
        "url": "https://my-storage-account.blob.core.windows.net/testcontainer/Auto.jpg",
        "sequencer": "000000000000000000000000000089A4000000000018d6ea",
        "storageDiagnostics": {
            "batchId": "3418f7a9-7006-0014-00f6-406dc6000000"
        }
    },
    "dataVersion": "",
    "metadataVersion": "1",
    "eventTime": "2021-05-04T15:00:00.8350154Z"
}
```

### <a name="microsoftstorageblobrenamed-event"></a>Microsoft.Storage.BlobRenamed 事件

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/my-renamed-file.txt",
  "eventType": "Microsoft.Storage.BlobRenamed",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "RenameFile",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "destinationUrl": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-renamed-file.txt",
    "sourceUrl": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-original-file.txt",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstoragedirectorycreated-event"></a>Microsoft.Storage.DirectoryCreated 事件

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/my-new-directory",
  "eventType": "Microsoft.Storage.DirectoryCreated",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "CreateDirectory",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "url": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-new-directory",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstoragedirectoryrenamed-event"></a>Microsoft.Storage.DirectoryRenamed 事件

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/my-renamed-directory",
  "eventType": "Microsoft.Storage.DirectoryRenamed",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "RenameDirectory",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "destinationUrl": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-renamed-directory",
    "sourceUrl": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-original-directory",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstoragedirectorydeleted-event"></a>Microsoft.Storage.DirectoryDeleted 事件

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/directory-to-delete",
  "eventType": "Microsoft.Storage.DirectoryDeleted",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "DeleteDirectory",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "url": "https://my-storage-account.dfs.core.windows.net/my-file-system/directory-to-delete",
    "recursive": "true", 
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

# <a name="cloud-event-schema"></a>[云事件架构](#tab/cloud-event-schema)

### <a name="microsoftstorageblobcreated-event"></a>Microsoft.Storage.BlobCreated 事件

```json
[{
  "source": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/test-container/blobs/new-file.txt",
  "type": "Microsoft.Storage.BlobCreated",
  "time": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "PutBlockList",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "eTag": "\"0x8D4BCC2E4835CD0\"",
    "contentType": "text/plain",
    "contentLength": 524288,
    "blobType": "BlockBlob",
    "url": "https://my-storage-account.blob.core.windows.net/testcontainer/new-file.txt",
    "sequencer": "00000000000004420000000000028963",
    "storageDiagnostics": {
      "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "specversion": "1.0"
}]
```

### <a name="microsoftstorageblobcreated-event-data-lake-storage-gen2"></a>Microsoft.Storage.BlobCreated 事件 (Data Lake Storage Gen2)

如果 Blob 存储帐户具有分层命名空间，则数据看起来会与上一个示例类似，但以下这些更改除外：

* `data.api` 键设置为字符串 `CreateFile` 或 `FlushWithClose`。
* `contentOffset` 键包含在数据集中。

> [!NOTE]
> 如果应用程序使用 `PutBlockList` 操作将新的 Blob 上传到帐户，则数据不会包含这些更改。

```json
[{
  "source": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/new-file.txt",
  "type": "Microsoft.Storage.BlobCreated",
  "time": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "CreateFile",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "eTag": "\"0x8D4BCC2E4835CD0\"",
    "contentType": "text/plain",
    "contentLength": 0,
    "contentOffset": 0,
    "blobType": "BlockBlob",
    "url": "https://my-storage-account.dfs.core.windows.net/my-file-system/new-file.txt",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "specversion": "1.0"
}]
```

### <a name="microsoftstorageblobdeleted-event"></a>Microsoft.Storage.BlobDeleted 事件

```json
[{
  "source": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/testcontainer/blobs/file-to-delete.txt",
  "type": "Microsoft.Storage.BlobDeleted",
  "time": "2017-11-07T20:09:22.5674003Z",
  "id": "4c2359fe-001e-00ba-0e04-58586806d298",
  "data": {
    "api": "DeleteBlob",
    "requestId": "4c2359fe-001e-00ba-0e04-585868000000",
    "contentType": "text/plain",
    "blobType": "BlockBlob",
    "url": "https://my-storage-account.blob.core.windows.net/testcontainer/file-to-delete.txt",
    "sequencer": "0000000000000281000000000002F5CA",
    "storageDiagnostics": {
      "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "specversion": "1.0"
}]
```

### <a name="microsoftstorageblobdeleted-event-data-lake-storage-gen2"></a>Microsoft.Storage.BlobDeleted 事件 (Data Lake Storage Gen2)

如果 Blob 存储帐户具有分层命名空间，则数据看起来会与上一个示例类似，但以下这些更改除外：


* `data.api` 键设置为字符串 `DeleteFile`。
* `url` 键包含路径 `dfs.core.windows.net`。

> [!NOTE]
> 如果应用程序使用 `DeleteBlob` 操作从帐户中删除 Blob，则数据不会包含这些更改。

```json
[{
  "source": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/file-to-delete.txt",
  "type": "Microsoft.Storage.BlobDeleted",
  "time": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
    "data": {
    "api": "DeleteFile",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "contentType": "text/plain",
    "blobType": "BlockBlob",
    "url": "https://my-storage-account.dfs.core.windows.net/my-file-system/file-to-delete.txt",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "specversion": "1.0"
}]
```

### <a name="microsoftstorageblobrenamed-event"></a>Microsoft.Storage.BlobRenamed 事件

```json
[{
  "source": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/my-renamed-file.txt",
  "type": "Microsoft.Storage.BlobRenamed",
  "time": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "RenameFile",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "destinationUrl": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-renamed-file.txt",
    "sourceUrl": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-original-file.txt",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "specversion": "1.0"
}]
```

### <a name="microsoftstoragedirectorycreated-event"></a>Microsoft.Storage.DirectoryCreated 事件

```json
[{
  "source": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/my-new-directory",
  "type": "Microsoft.Storage.DirectoryCreated",
  "time": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "CreateDirectory",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "url": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-new-directory",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "specversion": "1.0"
}]
```

### <a name="microsoftstoragedirectoryrenamed-event"></a>Microsoft.Storage.DirectoryRenamed 事件

```json
[{
  "source": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/my-renamed-directory",
  "type": "Microsoft.Storage.DirectoryRenamed",
  "time": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "RenameDirectory",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "destinationUrl": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-renamed-directory",
    "sourceUrl": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-original-directory",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "specversion": "1.0"
}]
```

### <a name="microsoftstoragedirectorydeleted-event"></a>Microsoft.Storage.DirectoryDeleted 事件

```json
[{
  "source": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/directory-to-delete",
  "type": "Microsoft.Storage.DirectoryDeleted",
  "time": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "DeleteDirectory",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "url": "https://my-storage-account.dfs.core.windows.net/my-file-system/directory-to-delete",
    "recursive": "true", 
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "specversion": "1.0"
}]
```

---


## <a name="event-properties"></a>事件属性

# <a name="event-grid-event-schema"></a>[事件网格事件架构](#tab/event-grid-event-schema)

事件具有以下顶级数据：

| 属性 | 类型 | 说明 |
| -------- | ---- | ----------- |
| `topic` | string | 事件源的完整资源路径。 此字段不可写入。 事件网格提供此值。 |
| `subject` | string | 事件主题的发布者定义路径。 |
| `eventType` | string | 此事件源的一个注册事件类型。 |
| `eventTime` | string | 基于提供程序 UTC 时间的事件生成时间。 |
| `id` | 字符串 | 事件的唯一标识符。 |
| `data` | object | Blob 存储事件数据。 |
| `dataVersion` | string | 数据对象的架构版本。 发布者定义架构版本。 |
| `metadataVersion` | string | 事件元数据的架构版本。 事件网格定义顶级属性的架构。 事件网格提供此值。 |

# <a name="cloud-event-schema"></a>[云事件架构](#tab/cloud-event-schema)

事件具有以下顶级数据：

| 属性 | 类型 | 说明 |
| -------- | ---- | ----------- |
| `source` | string | 事件源的完整资源路径。 此字段不可写入。 事件网格提供此值。 |
| `subject` | string | 事件主题的发布者定义路径。 |
| `type` | string | 此事件源的一个注册事件类型。 |
| `time` | string | 基于提供程序 UTC 时间的事件生成时间。 |
| `id` | 字符串 | 事件的唯一标识符。 |
| `data` | object | Blob 存储事件数据。 |
| `specversion` | 字符串 | CloudEvents 架构规范版本。 |

---

数据对象具有以下属性：

| 属性 | 类型 | 说明 |
| -------- | ---- | ----------- |
| `api` | string | 触发事件的操作。 |
| `clientRequestId` | string | 客户端提供的用于存储 API 操作的请求 ID。 此 ID 可用于通过 Azure 存储诊断日志中的“client-request-id”字段关联到这些日志，并且可以通过“x-ms-client-request-id”标头提供到客户端请求中。 请参阅[日志格式](/rest/api/storageservices/storage-analytics-log-format)。 |
| `requestId` | string | 服务生成的用于存储 API 操作的请求 ID。 可用于通过 Azure 存储诊断日志中的“request-id-header”字段关联到这些日志，并且由“x-ms-request-id”标头中的初始化 API 调用返回。 请参阅[日志格式](/rest/api/storageservices/storage-analytics-log-format)。 |
| `eTag` | string | 可用于根据条件运行操作的值。 |
| `contentType` | string | 为 Blob 指定的内容类型。 |
| `contentLength` | integer | Blob 大小，以字节为单位。 |
| `blobType` | string | Blob 的类型。 有效值为“BlockBlob”或“PageBlob”。 |
| `contentOffset` | number | 在事件触发应用程序完成写入文件时执行的写入操作的偏移量（以字节为单位）。 <br>只有对具有分层命名空间的 Blob 存储帐户触发的事件才显示此值。|
| `destinationUrl` |string | 操作完成后存在的文件的 URL。 例如，如果重命名了某个文件，则 `destinationUrl` 属性将包含新文件名的 URL。 <br>只有对具有分层命名空间的 Blob 存储帐户触发的事件才显示此值。|
| `sourceUrl` |string | 执行操作之前存在的文件的 URL。 例如，如果重命名了某个文件，则 `sourceUrl` 将包含执行重命名操作之前的原始文件名的 URL。 <br>只有对具有分层命名空间的 Blob 存储帐户触发的事件才显示此值。 |
| `url` | string | Blob 的路径。 <br>如果客户端使用 Blob REST API，则 URL 采用以下结构：`<storage-account-name>.blob.core.windows.net\<container-name>\<file-name>`。 <br>如果客户端使用 Data Lake Storage REST API，则 URL 采用以下结构：`<storage-account-name>.dfs.core.windows.net/<file-system-name>/<file-name>`。 |
| `recursive` | string | 若要对所有子目录执行该操作，则此值为 `True`；否则此值为 `False`。 <br>只有对具有分层命名空间的 Blob 存储帐户触发的事件才显示此值。 |
| `sequencer` | string | 一个不透明的字符串值，表示任何特定 blob 名称的事件的逻辑顺序。  用户可以使用标准字符串比较，了解同一个 blob 名称上两个事件的相对序列。 |
| `storageDiagnostics` | object | Azure 存储服务中偶尔附带的诊断数据。 如果存在，事件使用者应忽略它。 |

## <a name="tutorials-and-how-tos"></a>教程和操作指南
|标题  |说明  |
|---------|---------|
| [快速入门：使用 Azure CLI 将 Blob 存储事件路由到自定义 Web 终结点](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | 介绍如何使用 Azure CLI 将 Blob 存储事件发送到 WebHook。 |
| [快速入门：使用 PowerShell 将 Blob 存储事件路由到自定义 Web 终结点](../storage/blobs/storage-blob-event-quickstart-powershell.md?toc=%2fazure%2fevent-grid%2ftoc.json) | 介绍如何使用 Azure PowerShell 将 Blob 存储事件发送到 WebHook。 |
| [快速入门：使用 Azure 门户创建和路由 Blob 存储事件](blob-event-quickstart-portal.md) | 介绍如何使用门户将 Blob 存储事件发送到 WebHook。 |
| [Azure CLI：订阅 Blob 存储帐户的事件](./scripts/event-grid-cli-blob.md) | 用于订阅 Blob 存储帐户的事件的示例脚本。 它将事件发送到 WebHook。 |
| [PowerShell：订阅 Blob 存储帐户的事件](./scripts/event-grid-powershell-blob.md) | 用于订阅 Blob 存储帐户的事件的示例脚本。 它将事件发送到 WebHook。 |
| [资源管理器模板：创建 Blob 存储和订阅](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.eventgrid/event-grid-subscription-and-storage) | 部署 Azure Blob 存储帐户并订阅该存储帐户的事件。 它将事件发送到 WebHook。 |
| [概述：响应 Blob 存储事件](../storage/blobs/storage-blob-event-overview.md) | 概述 Blob 存储与事件网格的集成。 |

## <a name="next-steps"></a>后续步骤

* 有关 Azure 事件网格的简介，请参阅[什么是事件网格？](overview.md)
* 有关创建 Azure 事件网格订阅的详细信息，请参阅[事件网格订阅架构](subscription-creation-schema.md)。
* 有关使用 Blob 存储事件的简介，请参阅[路由 Blob 存储事件 - Azure CLI](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)。 
