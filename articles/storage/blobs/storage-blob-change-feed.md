---
title: Blob 存储中的更改源
titleSuffix: Azure Storage
description: 了解 Azure Blob 存储中的更改源日志以及如何使用这些日志。
author: tamram
ms.author: tamram
ms.date: 10/01/2021
ms.topic: how-to
ms.service: storage
ms.subservice: blobs
ms.reviewer: sadodd
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: bed39bd544ac1b586dcefefb890927fe1ce472b2
ms.sourcegitcommit: 03e84c3112b03bf7a2bc14525ddbc4f5adc99b85
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/03/2021
ms.locfileid: "129402143"
---
# <a name="change-feed-support-in-azure-blob-storage"></a>Azure Blob 存储中的更改源支持

更改源的用途是提供存储帐户中 Blob 和 Blob 元数据发生的所有更改的事务日志。 更改源提供这些更改的有序、有保证、持久、不可变、只读的日志。     客户端应用程序可以在流式处理或批处理模式下随时读取这些日志。 使用更改源可以生成高效且可缩放的解决方案，因此能够以较低的成本处理 Blob 存储帐户中发生的更改事件。

## <a name="how-the-change-feed-works"></a>更改源的工作原理

更改源作为 [Blob](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs) 存储在存储帐户中的特殊容器内，按标准的 [Blob 定价](https://azure.microsoft.com/pricing/details/storage/blobs/)计费。 你可以根据要求控制这些文件的保留期（请参阅当前版本的[条件](#conditions)）。 更改事件根据 [Apache Avro](https://avro.apache.org/docs/1.8.2/spec.html) 格式（一种简洁且快速的二进制格式，通过内联架构提供丰富的数据结构）规范以记录的形式追加到更改源。 这种格式广泛用于 Hadoop 生态系统、流分析和 Azure 数据工厂。

可通过异步、增量或整体方式处理这些日志。 任意数目的客户端应用程序都可以按照自身的步调单独并行读取更改源。 分析应用程序（例如 [Apache Drill](https://drill.apache.org/docs/querying-avro-files/) 或 [Apache Spark](https://spark.apache.org/docs/latest/sql-data-sources-avro.html)）可以直接将日志用作 Avro 文件，使你能够以较低的成本和较高的带宽处理这些日志，而无需编写自定义应用程序。

下图显示了如何将记录添加到更改源：

:::image type="content" source="media/storage-blob-change-feed/change-feed-diagram.png" alt-text="图中显示更改源如何提供 Blob 更改的有序日志":::

更改源支持非常适合基于已更改的对象处理数据的方案。 例如，应用程序可以执行以下操作：

- 更新辅助索引，与缓存、搜索引擎或任何其他内容管理方案同步。
- 基于对象发生的更改提取业务分析见解和指标，不管是采用流式处理方式还是批处理模式。
- 针对企业数据管理的安全性、合规性或智能，存储、审核和分析对象在任意时间段内发生的更改。
- 生成解决方案来备份、镜像或复制帐户中的对象状态，以满足灾难管理或合规性要求。
- 生成连接的应用程序管道，以便根据创建的或更改的对象来响应更改事件或计划执行。

更改源是[对象复制](object-replication-overview.md)和[块 blob 的时间点还原](point-in-time-restore-overview.md)的先决条件功能。

> [!NOTE]
> 更改源提供一个持久且有序的日志模型来记录发生在 Blob 中的更改。 在发生更改后的几分钟内，这些更改就会写入并出现在更改源日志中。 如果应用程序必须以比这快得多的速度对事件做出反应，请考虑改用 [Blob 存储事件](storage-blob-event-overview.md)。 [Blob 存储事件](storage-blob-event-overview.md)提供实时的一次性事件，使 Azure Functions 或应用程序能够快速对 Blob 中发生的更改做出反应。

## <a name="enable-and-disable-the-change-feed"></a>启用和禁用更改源

必须在存储帐户中启用更改源，才能开始捕获和记录更改。 禁用更改源可停止捕获更改。 可以在门户或 PowerShell 中使用 Azure 资源管理器模板来启用和禁用更改。

启用更改源时，请注意以下几个事项。

- 每个存储帐户中的 Blob 服务只有一个更改源，该更改源存储在 $blobchangefeed 容器中。

- 仅在 Blob 服务级别捕获“创建”、“更新”和“删除”更改。

- 更改源会捕获帐户中发生的所有可用事件的所有更改。 客户端应用程序可根据需要筛选出事件类型。 （请参阅当前版本的[条件](#conditions)）。

- 只有标准常规用途 v2、高级块 Blob 和 Blob 存储帐户才能启用更改源。 当前不支持启用了分层命名空间的帐户。 不支持常规用途 v1 存储帐户，但可以在不停机的情况下将其升级到常规用途 v2。有关详细信息，请参阅[升级到 GPv2 存储帐户](../common/storage-account-upgrade.md)。

### <a name="portal"></a>[Portal](#tab/azure-portal)

使用 Azure 门户在存储帐户中启用更改源：

1. 在 [Azure 门户](https://portal.azure.com/)中，选择存储帐户。
1. 导航到“数据管理”下的“数据保护”选项 。
1. 在“跟踪”下，选择“启用 blob 更改源” 。
1. 选择“保存”按钮以确认数据保护设置。

    :::image type="content" source="media/storage-blob-change-feed/change-feed-enable-portal.png" alt-text="显示如何在 Azure 门户中启用更改源的屏幕截图":::

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

使用 PowerShell 启用更改源：

1. 安装最新的 PowershellGet。

   ```powershell
   Install-Module PowerShellGet –Repository PSGallery –Force
   ```

2. 关闭 PowerShell 控制台，然后重新将其打开。

3. 安装 Az.Storage 模块 2.5.0 版或更高版本。

   ```powershell
   Install-Module Az.Storage –Repository PSGallery -RequiredVersion 2.5.0 –AllowClobber –Force
   ```

4. 使用 `Connect-AzAccount` 命令登录到 Azure 订阅，然后按照屏幕上的说明进行身份验证。

   ```powershell
   Connect-AzAccount
   ```

5. 为存储帐户启用更改源。

   ```powershell
   Update-AzStorageBlobServiceProperty -EnableChangeFeed $true
   ```

### <a name="template"></a>[模板](#tab/template)

通过 Azure 门户使用 Azure 资源管理器模板在现有存储帐户中启用更改源：

1. 在 Azure 门户中，选择“创建资源”。

2. 在“搜索市场”中键入“模板部署”，然后按 **ENTER**。 

3. 选择“[部署自定义模板](https://portal.azure.com/#create/Microsoft.Template)”，然后选择“在编辑器中生成自己的模板”。 

4. 在模板编辑器中粘贴以下 JSON。 将 `<accountName>` 占位符替换为存储帐户的名称。

   ```json
   {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {},
       "variables": {},
       "resources": [{
           "type": "Microsoft.Storage/storageAccounts/blobServices",
           "apiVersion": "2019-04-01",
           "name": "<accountName>/default",
           "properties": {
               "changeFeed": {
                   "enabled": true
               }
           } 
        }]
   }
   ```

5. 选择“保存”按钮，指定帐户的资源组，然后选择“购买”按钮来部署模板并启用更改源。 

---

## <a name="consume-the-change-feed"></a>使用更改源

更改源生成多个元数据文件和日志文件。 这些文件位于存储帐户的 $blobchangefeed 容器中。

> [!NOTE]
> 在当前版本中，$blobchangefeed 容器仅在 Azure 门户中可见，而在 Azure 存储资源管理器中不可见。 目前，在调用 ListContainers API 时无法看到 $blobchangefeed 容器，但可以直接对容器调用 ListBlobs API 来查看 Blob

客户端应用程序可以通过更改源处理器 SDK 随附的 Blob 更改源处理器库来使用更改源。

参阅[处理 Azure Blob 存储中的更改源日志](storage-blob-change-feed-how-to.md)。

## <a name="understand-change-feed-organization"></a>了解更改源的组织方式

<a id="segment-index"></a>

### <a name="segments"></a>段数

更改源是按小时段组织的更改日志，但系统每隔几分钟就会在其中追加和更新内容。 仅当在该小时内发生了 Blob 更改事件时，才会创建这些段。 因此，客户端应用程序可以使用在特定时间范围内发生的更改，不必搜索整个日志。 有关详细信息，请参阅[规范](#specifications)。

更改源的可用小时段在清单文件中描述，该文件指定了该段的更改源文件的路径。 `$blobchangefeed/idx/segments/` 虚拟目录的列表按时间顺序显示这些段。 段的路径描述该段所代表的小时时间范围的开始时间。 可以使用该列表筛选出你感兴趣的日志段。

```output
Name                                                                    Blob Type    Blob Tier      Length  Content Type    
----------------------------------------------------------------------  -----------  -----------  --------  ----------------
$blobchangefeed/idx/segments/1601/01/01/0000/meta.json                  BlockBlob                      584  application/json
$blobchangefeed/idx/segments/2019/02/22/1810/meta.json                  BlockBlob                      584  application/json
$blobchangefeed/idx/segments/2019/02/22/1910/meta.json                  BlockBlob                      584  application/json
$blobchangefeed/idx/segments/2019/02/23/0110/meta.json                  BlockBlob                      584  application/json
```

> [!NOTE]
> 启用更改源时，会自动创建 `$blobchangefeed/idx/segments/1601/01/01/0000/meta.json`。 可以放心地忽略此文件。 它是一个始终为空的初始化文件。

段清单文件 (`meta.json`) 显示该段的更改源文件在 `chunkFilePaths` 属性中的路径。 下面是段清单文件的示例。

```json
{
    "version": 0,
    "begin": "2019-02-22T18:10:00.000Z",
    "intervalSecs": 3600,
    "status": "Finalized",
    "config": {
        "version": 0,
        "configVersionEtag": "0x8d698f0fba563db",
        "numShards": 2,
        "recordsFormat": "avro",
        "formatSchemaVersion": 1,
        "shardDistFnVersion": 1
    },
    "chunkFilePaths": [
        "$blobchangefeed/log/00/2019/02/22/1810/",
        "$blobchangefeed/log/01/2019/02/22/1810/"
    ],
    "storageDiagnostics": {
        "version": 0,
        "lastModifiedTime": "2019-02-22T18:11:01.187Z",
        "data": {
            "aid": "55e507bf-8006-0000-00d9-ca346706b70c"
        }
    }
}
```

> [!NOTE]
> 只有在帐户中启用更改源功能之后，才会显示 `$blobchangefeed` 容器。 启用更改源后，必须先等待几分钟，然后才能列出容器中的 Blob。

<a id="log-files"></a>

### <a name="change-event-records"></a>更改事件记录

更改源文件包含一系列更改事件记录。 每条更改事件记录对应于单个 Blob 发生的一项更改。 使用 [Apache Avro](https://avro.apache.org/docs/1.8.2/spec.html) 格式规范序列化记录并将其写入文件。 可以使用 Avro 文件格式规范读取记录。 有多个库可用于处理该格式的文件。

更改源文件以[追加 Blob](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-append-blobs) 的形式存储在 `$blobchangefeed/log/` 虚拟目录中。 每个路径下的第一个更改源文件的文件名包含 `00000`（例如 `00000.avro`）。 添加到该路径的每个后续日志文件的名称编号将递增 1（例如：`00001.avro`）。

更改源记录中捕获以下事件类型：
- BlobCreated
- BlobDeleted
- BlobPropertiesUpdated
- BlobSnapshotCreated

下面是更改源文件中已转换为 JSON 的更改事件记录的示例。

```json
{
     "schemaVersion": 1,
     "topic": "/subscriptions/dd40261b-437d-43d0-86cf-ef222b78fd15/resourceGroups/sadodd/providers/Microsoft.Storage/storageAccounts/mytestaccount",
     "subject": "/blobServices/default/containers/mytestcontainer/blobs/mytestblob",
     "eventType": "BlobCreated",
     "eventTime": "2019-02-22T18:12:01.079Z",
     "id": "55e5531f-8006-0000-00da-ca3467000000",
     "data": {
         "api": "PutBlob",
         "clientRequestId": "edf598f4-e501-4750-a3ba-9752bb22df39",
         "requestId": "00000000-0000-0000-0000-000000000000",
         "etag": "0x8D698F13DCB47F6",
         "contentType": "application/octet-stream",
         "contentLength": 128,
         "blobType": "BlockBlob",
         "url": "",
         "sequencer": "000000000000000100000000000000060000000000006d8a",
         "storageDiagnostics": {
             "bid": "11cda41c-13d8-49c9-b7b6-bc55c41b3e75",
             "seq": "(6,5614,28042,28038)",
             "sid": "591651bd-8eb3-c864-1001-fcd187be3efd"
         }
  }
}
```

有关每个属性的说明，请参阅 [Blob 存储的 Azure 事件网格事件架构](../../event-grid/event-schema-blob-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#event-properties)。 BlobPropertiesUpdated 和 BlobSnapshotCreated 事件当前是更改源独有的事件，尚不支持用于 Blob 存储事件。

> [!NOTE]
> 创建某个段后，该段的更改源文件不会立即显示。 延迟时长处于正常的更改源发布延迟间隔范围内，而该间隔为更改后的几分钟内。

<a id="specifications"></a>

## <a name="specifications"></a>规范

- 更改事件记录只会追加到更改源。 一旦追加这些记录，它们就不可变，且记录位置是稳定的。 客户端应用程序可以在更改源的读取位置维护其自身的检查点。

- 在更改后的几分钟内，就会追加更改事件记录。 客户端应用程序可以选择在追加记录后使用这些记录以进行流式访问，或者在任何其他时间批量使用这些记录。

- 更改事件记录按每个 Blob 的修改顺序排序。 Azure Blob 存储中不会定义跨 Blob 的更改顺序。 前一段中的所有更改位于后续段中任何更改的前面。

- 使用 [Apache Avro 1.8.2](https://avro.apache.org/docs/1.8.2/spec.html) 格式规范将更改事件记录序列化为日志文件。

- `eventType` 值为 `Control` 的更改事件记录是内部系统记录，不反映对帐户中的对象所做的更改。 可以放心地忽略这些记录。

- `storageDiagnostics` 属性包中的值仅供内部使用，按设计不供应用程序使用。 应用程序不应该对该数据存在协定性依赖关系。 可以放心地忽略这些属性。

- 段所代表的时间是近似时间，其时长为 15 分钟。 因此，为了确保能够使用指定时间内的所有记录，请使用连续的前一小时和后一小时段。

- 由于对日志流进行了内部分区以管理发布吞吐量，因此每个段可以包含不同数量的 `chunkFilePaths`。 保证每个 `chunkFilePath` 中的日志文件包含互斥的 Blob，并且可以并行使用和处理这些文件，而不会在迭代期间违反每个 Blob 的修改顺序。

- 段的最初状态为“`Publishing`”。 一旦完成将记录追加到段的过程，段的状态将变成“`Finalized`”。 应用程序不应使用在 `$blobchangefeed/meta/Segments.json` 文件中 `LastConsumable` 属性日期之后创建的任何段中的日志文件。 下面是 `$blobchangefeed/meta/Segments.json` 文件中 `LastConsumable` 属性的示例：

```json
{
    "version": 0,
    "lastConsumable": "2019-02-23T01:10:00.000Z",
    "storageDiagnostics": {
        "version": 0,
        "lastModifiedTime": "2019-02-23T02:24:00.556Z",
        "data": {
            "aid": "55e551e3-8006-0000-00da-ca346706bfe4",
            "lfz": "2019-02-22T19:10:00.000Z"
        }
    }
}

```

<a id="conditions"></a>

## <a name="conditions-and-known-issues"></a>条件和已知问题

本部分介绍当前的更改源版本中的已知问题和条件。

- 任何一项更改的更改事件记录可能会在更改源中出现多次。
- 暂时无法通过对更改源日志文件设置基于时间的保留策略来管理其生存期，且无法删除 Blob。
- 日志文件的 `url` 属性目前始终是空的。
- segments.json 文件的 `LastConsumable` 属性不会列出更改源最终处理的第一个段。 此问题只会在对第一个段进行最终处理之后才出现。 第一个小时之后的所有后续段会准确捕获到 `LastConsumable` 属性中。
- 目前，在调用 ListContainers API 时看不到 $blobchangefeed 容器，且在 Azure 门户或存储资源管理器中也看不到该容器。 可以通过直接在 $blobchangefeed 容器上调用 ListBlobs API 来查看内容。
- 以前启动了[帐户故障转移](../common/storage-disaster-recovery-guidance.md)的存储帐户可能会出现不显示日志文件的问题。 将来的任何帐户故障转移也可能会影响日志文件。

## <a name="feature-support"></a>功能支持

下表显示你的帐户如何支持此功能，以及启用某些功能后对支持的影响。

| 存储帐户类型 | Blob 存储（默认支持） | Data Lake Storage Gen2 <sup>1</sup> | NFS 3.0 <sup>1</sup> |
|--|--|--|--|
| 标准常规用途 v2 | ![是](../media/icons/yes-icon.png) | ![否](../media/icons/no-icon.png) | ![否](../media/icons/no-icon.png) |
| 高级块 blob | ![是](../media/icons/yes-icon.png) | ![否](../media/icons/no-icon.png) | ![否](../media/icons/no-icon.png) |

<sup>1</sup> Data Lake Storage Gen2 和网络文件系统 (NFS) 3.0 协议都需要已启用分层命名空间的存储帐户。

## <a name="faq"></a>常见问题

### <a name="what-is-the-difference-between-the-change-feed-and-storage-analytics-logging"></a>更改源与存储分析日志记录有何区别？

分析日志包含所有读取、写入、列出和删除操作的记录，以及对所有操作的成功和失败请求的记录。 分析日志的记录原则是“尽量记录”，不保证记录顺序。

更改源解决方案提供对帐户成功执行的变动或更改操作（例如创建、修改和删除 Blob）的事务日志。 更改源保证按照每个 Blob 的成功更改顺序记录和显示所有事件，因此你无需从大量的读取操作或失败请求中筛选出干扰性的信息。 从根本上讲，更改源是针对需要特定保证的应用程序开发进行设计和优化的。

### <a name="should-i-use-the-change-feed-or-storage-events"></a>应该使用更改源还是存储事件？

可以利用这两项功能，因为更改源和 [Blob 存储事件](storage-blob-event-overview.md)以相同的交付可靠性保证提供相同的信息，其主要差别在于事件记录的延迟、排序和存储。 在更改后的几分钟内，更改源就会将记录发布到日志，同时还保证每个 Blob 的更改操作顺序。 存储事件将实时推送，可能不会排序。 更改源事件根据你自己定义的保留期，以只读的稳定日志形式持久存储在存储帐户中，而存储事件由事件处理程序即时使用，除非你显式存储这些事件。 借助更改源，任意数量的应用程序都可以通过 Blob API 或 SDK 在方便的时间使用日志。

## <a name="next-steps"></a>后续步骤

- 通过示例了解如何使用 .NET 客户端应用程序读取更改源。 参阅[处理 Azure Blob 存储中的更改源日志](storage-blob-change-feed-how-to.md)。
- 了解如何对事件做出实时反应。 参阅[对 Blob 存储事件做出反应](storage-blob-event-overview.md)
- 详细了解所有请求的成功和失败操作的详细日志记录信息。 参阅 [Azure 存储分析日志记录](../common/storage-analytics-logging.md)
