---
title: 配置 Blob 索引器
titleSuffix: Azure Cognitive Search
description: 设置一个 Azure Blob 索引器，以便自动为 Azure 认知搜索中的全文搜索操作编制 Blob 内容的索引。
manager: nitinme
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/03/2021
ms.custom: contperf-fy21q3
ms.openlocfilehash: 74813fabec4d5fe43cd158bb4aa359c2a3b0188a
ms.sourcegitcommit: 7e117cfec95a7e61f4720db3c36c4fa35021846b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/09/2021
ms.locfileid: "99988716"
---
# <a name="how-to-configure-blob-indexing-in-cognitive-search"></a>如何在认知搜索中配置 blob 索引

本文介绍如何配置 blob 索引器，以便在 Azure 认知搜索中将基于文本的文档 (例如 Pdf、Microsoft Office 文档和其他) 。 如果你不熟悉索引器概念，请从 [Azure 认知搜索中的索引器](search-indexer-overview.md) 开始，并在深入了解 blob 索引之前 [创建搜索索引器](search-howto-create-indexers.md) 。

<a name="SupportedFormats"></a>

## <a name="supported-document-formats"></a>支持的文档格式

Azure 认知搜索 blob 索引器可以从以下文档格式提取文本：

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

## <a name="data-source-definitions"></a>数据源定义

Blob 索引器与任何其他索引器之间的差异是分配给索引器的数据源定义。 数据源封装了所有属性，这些属性指定要编制索引的内容的类型、连接和位置。

Blob 数据源定义类似于以下示例：

```http
{
    "name" : "my-blob-datasource",
    "type" : "azureblob",
    "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
    "container" : { "name" : "my-container", "query" : "<optional-virtual-directory-name>" }
}
```

`"credentials"`属性可以是连接字符串（如上面的示例所示），也可以是下一部分中所述的替代方法之一。 `"container"`属性提供 Azure 存储中内容的位置，并 `"query"` 用于指定容器中的子文件夹。 有关数据源定义的详细信息，请参阅 [Create Data source (REST) ](/rest/api/searchservice/create-data-source)。

<a name="Credentials"></a>

## <a name="credentials"></a>凭据

可通过以下一种方式提供 blob 容器的凭据：

**托管标识连接字符串**：`{ "connectionString" : "ResourceId=/subscriptions/<your subscription ID>/resourceGroups/<your resource group name>/providers/Microsoft.Storage/storageAccounts/<your storage account name>/;" }`

此连接字符串不需要帐户密钥，但必须按照说明 [使用托管标识设置到 Azure 存储帐户的连接](search-howto-managed-identities-storage.md)。

**完全访问存储帐户连接字符串**：`{ "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your account key>;" }`

可通过导航到“存储帐户”边栏选项卡 >“设置”>“密钥”（对于经典存储帐户）或“设置”>“访问密钥”（对于 Azure 资源管理器存储帐户），从 Azure 门户获取连接字符串。

**存储帐户共享访问签名** (SAS) 连接字符串：`{ "connectionString" : "BlobEndpoint=https://<your account>.blob.core.windows.net/;SharedAccessSignature=?sv=2016-05-31&sig=<the signature>&spr=https&se=<the validity end time>&srt=co&ss=b&sp=rl;" }`

SAS 应具有容器和对象（本例中为 blob）的列表和读取权限。

**容器共享访问签名**：`{ "connectionString" : "ContainerSharedAccessUri=https://<your storage account>.blob.core.windows.net/<container name>?sv=2016-05-31&sr=c&sig=<the signature>&se=<the validity end time>&sp=rl;" }`

SAS 应具有容器的列表和读取权限。 有关存储共享访问签名的详细信息，请参阅[使用共享访问签名](../storage/common/storage-sas-overview.md)。

> [!NOTE]
> 如果使用 SAS 凭据，则需使用续订的签名定期更新数据源凭据，以防止其过期。 如果 SAS 凭据过期，索引器将失败并出现一条错误消息，类似于 "连接字符串中提供的凭据无效或已过期"。  

## <a name="index-definitions"></a>索引定义

索引指定文档、属性和其他构造中可以塑造搜索体验的字段。 下面的示例使用 [创建索引 (REST API) ](/rest/api/searchservice/create-index)创建简单索引。 

```http
POST https://[service name].search.windows.net/indexes?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
      "name" : "my-target-index",
      "fields": [
        { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
        { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
      ]
}
```

索引定义需要集合中的一个字段 `"fields"` 作为文档键。 索引定义还应包括内容和元数据的字段。

**`content`** 字段用于存储从 blob 中提取的文本。 此字段的定义可能类似于上面所示。 不需要使用此名称，但这样做可以利用隐式字段映射。 Blob 索引器可以将 blob 内容发送到索引中的 content Edm。字符串字段，无需字段映射。

你还可以为索引中所需的任何 blob 元数据添加字段。 索引器可以读取自定义元数据属性、 [标准元数据](#indexing-blob-metadata) 属性和 [内容特定的元数据](search-blob-metadata-properties.md) 属性。 有关索引的详细信息，请参阅 [创建索引](search-what-is-an-index.md)。

<a name="DocumentKeys"></a>

### <a name="defining-document-keys-and-field-mappings"></a>定义文档键和字段映射

在搜索索引中，文档键唯一标识每个文档。 您选择的字段必须为类型 `Edm.String` 。 对于 blob 内容，文档键的最佳候选项是 blob 上的元数据属性。

+ **`metadata_storage_name`** -此属性是候选项，但前提是在要编制索引的所有容器和文件夹中，名称是唯一的。 不管 blob 位置如何，最终结果是在索引所有内容后，文档键 (名称) 在搜索索引中必须是唯一的。 

  存储名称的另一个潜在问题是，它可能包含对文档键无效的字符，如虚线。 您可以使用 `base64Encode` [字段映射函数](search-indexer-field-mappings.md#base64EncodeFunction)处理无效字符。 如果这样做，还应记得在将文档键传入 API 调用（如 [查找文档 (REST) ](/rest/api/searchservice/lookup-document)）时对其进行编码。 在 .NET 中，可以使用 [httpserverutility.urltokenencode 方法](/dotnet/api/system.web.httpserverutility.urltokenencode) 对字符进行编码。

+ **`metadata_storage_path`** -使用完整路径可确保唯一性，但路径一定会包含 `/` [在文档键中无效](/rest/api/searchservice/naming-rules)的字符。 如上所述，可以使用 `base64Encode` [函数](search-indexer-field-mappings.md#base64EncodeFunction) 对字符进行编码。

+ 第三种选择是将自定义元数据属性添加到 Blob。 此选项要求 blob 上传过程将该元数据属性添加到所有 blob。 由于密钥是必需的属性，因此缺少值的任何 blob 都将无法进行索引。

> [!IMPORTANT]
> 如果索引中没有键字段的显式映射，Azure 认知搜索会自动使用 `metadata_storage_path` 作为键并对键值进行 base-64 编码（上述第二个选项）。
>

#### <a name="example"></a>示例

下面的示例演示了 `metadata_storage_name` 文档键。 假设索引具有一个名为的键字段 `key` ，另一个名 `fileSize` 为的字段用于存储文档大小。 索引器定义中的[字段映射](search-indexer-field-mappings.md)建立了字段关联，并 `metadata_storage_name` 具有[ `base64Encode` 字段映射函数](search-indexer-field-mappings.md#base64EncodeFunction)来处理不受支持的字符。

```http
PUT https://[service name].search.windows.net/indexers/my-blob-indexer?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  "dataSourceName" : "my-blob-datasource ",
  "targetIndexName" : "my-target-index",
  "schedule" : { "interval" : "PT2H" },
  "fieldMappings" : [
    { "sourceFieldName" : "metadata_storage_name", "targetFieldName" : "key", "mappingFunction" : { "name" : "base64Encode" } },
    { "sourceFieldName" : "metadata_storage_size", "targetFieldName" : "fileSize" }
  ]
}
```

#### <a name="how-to-make-an-encoded-field-searchable"></a>如何使编码字段 "可搜索"

有时，您需要使用类似于关键字的编码版本的字段 `metadata_storage_path` ，但在搜索索引中不) 编码的情况下，还需要该字段 (。 若要支持这两个用例，您可以映射 `metadata_storage_path` 到两个字段; 一个字段用于 (编码的键) ，另一个用于在索引架构中被视为 "可搜索" 的路径字段。 下面的示例演示了的两个字段映射 `metadata_storage_path` 。

```http
PUT https://[service name].search.windows.net/indexers/blob-indexer?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  "dataSourceName" : " blob-datasource ",
  "targetIndexName" : "my-target-index",
  "schedule" : { "interval" : "PT2H" },
  "fieldMappings" : [
    { "sourceFieldName" : "metadata_storage_path", "targetFieldName" : "key", "mappingFunction" : { "name" : "base64Encode" } },
    { "sourceFieldName" : "metadata_storage_path", "targetFieldName" : "path" }
  ]
}
```

<a name="PartsOfBlobToIndex"></a>

## <a name="index-content-and-metadata"></a>索引内容和元数据

Blob 包含内容和元数据。 可以使用 `dataToExtract` 配置参数控制要为 Blob 中的哪些部分编制索引。 该参数采用以下值：

+ `contentAndMetadata` - 指定要为从 Blob 中提取的所有元数据和文本内容编制索引。 这是默认值。

+ `storageMetadata` - 指定仅为[标准 Blob 属性和用户指定的元数据](../storage/blobs/storage-blob-container-properties-metadata.md)编制索引。

+ `allMetadata` -指定从 blob 内容中提取标准 blob 属性和所 [查找内容类型的任何元数据](search-blob-metadata-properties.md) 。

例如，如果只要为存储元数据编制索引，请使用：

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "dataToExtract" : "storageMetadata" } }
}
```

<a name="how-azure-search-indexes-blobs"></a>

### <a name="indexing-blob-content"></a>为 blob 内容编制索引

默认情况下，具有结构化内容的 blob （如 JSON 或 CSV）作为单个文本块进行索引。 但是，如果 JSON 或 CSV 文档具有内部结构 (分隔符) ，则可以分配分析模式，以便为每个行或元素生成单个搜索文档。 有关详细信息，请参阅为 [JSON Blob 编制索引](search-howto-index-json-blobs.md) 和为 [CSV blob 编制索引](search-howto-index-csv-blobs.md)。

复合或嵌入式文档（例如 ZIP 存档、嵌入了带附件 Outlook 电子邮件的 Word 文档或带附件的 .MSG 文件）也以单一文档的形式编制索引。 例如，从 .MSG 文件的附件中提取的所有图像将在 normalized_images 字段中返回。

文档的文本内容将提取到名为 `content` 的字符串字段中。

  > [!NOTE]
  > Azure 认知搜索会根据定价层限制其提取的文本量。 对于免费层，当前 [服务限制](search-limits-quotas-capacity.md#indexer-limits) 为32000个字符，对于 8000000 4000000 Basic 为64000，对于标准 S2 则限制为16000000，为标准 S3 限制为个字符。 已截断的文本会在索引器状态响应中出现一条警告。  

<a name="indexing-blob-metadata"></a>

### <a name="indexing-blob-metadata"></a>为 Blob 元数据编制索引

索引器还可以为 blob 元数据编制索引。 首先，可以逐字提取任何用户指定的元数据属性。 若要接收这些值，必须在类型的搜索索引中定义字段 `Edm.String` ，其名称与 blob 的元数据密钥的名称相同。 例如，如果一个 blob 的元数据键的 `Sensitivity` 值为 `High` ，则应在搜索索引中定义一个名为的字段， `Sensitivity` 并将其填充值 `High` 。

其次，可以将标准 blob 元数据属性提取到下面列出的字段中。 Blob 索引器自动创建这些 blob 元数据属性的内部字段映射。 您仍然必须添加要使用索引定义的字段，但是可以在索引器中省略创建字段映射。

  + **metadata_storage_name** (`Edm.String`) -blob 的文件名。 例如，对于 Blob /my-container/my-folder/subfolder/resume.pdf 而言，此字段的值是 `resume.pdf`。

  + **metadata_storage_path** (`Edm.String`) -BLOB 的完整 URI，包括存储帐户。 例如 `https://myaccount.blob.core.windows.net/my-container/my-folder/subfolder/resume.pdf`

  + **metadata_storage_content_type** (`Edm.String`) -内容类型，由用于上传 blob 的代码指定。 例如，`application/octet-stream`。

  + **metadata_storage_last_modified** (`Edm.DateTimeOffset`) -上次修改 blob 的时间戳。 Azure 认知搜索使用此时间戳来识别已更改的 Blob，避免在初次编制索引之后再次为所有内容编制索引。

  + **metadata_storage_size** (`Edm.Int64`) -blob 大小（以字节为单位）。

  + **metadata_storage_content_md5** `Edm.String` blob 内容 () -md5 哈希（如果可用）。

  + **metadata_storage_sas_token** (`Edm.String`) -可由 [自定义技能](cognitive-search-custom-skill-interface.md) 用于获取对 blob 的访问权限的临时 sas 令牌。 不应存储此令牌供以后使用，因为它可能会过期。

最后，任何特定于要编制索引的 blob 的文档格式的元数据属性，也可以在索引架构中表示。 有关内容特定的元数据的详细信息，请参阅 [内容元数据属性](search-blob-metadata-properties.md)。

必须指出的是，无需在搜索索引中定义上述所有属性的字段-只需捕获应用程序所需的属性即可。

<a name="WhichBlobsAreIndexed"></a>

## <a name="how-to-control-which-blobs-are-indexed"></a>如何控制要为哪些 blob 编制索引

你可以通过 blob 的文件类型控制要对哪些 blob 进行索引，或通过在 blob 本身上设置属性来控制哪些 blob，从而导致索引器跳过这些 blob。

### <a name="include-specific-file-extensions"></a>包含特定文件扩展名

使用 `indexedFileNameExtensions` 提供以逗号分隔的文件扩展名列表，以使用前导点) 来索引 (。 例如，如果只要为 .PDF 和 .DOCX Blob 编制索引，请执行以下操作：

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "indexedFileNameExtensions" : ".pdf,.docx" } }
}
```

### <a name="exclude-specific-file-extensions"></a>排除特定的文件扩展名

使用 `excludedFileNameExtensions` 来提供以逗号分隔的文件扩展名列表，以便再次跳过 (，前导点) 。 例如，若要为所有 Blob 编制索引，但要排除具有 .PNG 和 .JPEG 扩展名的 Blob，请执行以下操作：

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "excludedFileNameExtensions" : ".png,.jpeg" } }
}
```

如果 `indexedFileNameExtensions` 和 `excludedFileNameExtensions` 参数均存在，则索引器首先查看 `indexedFileNameExtensions` ，然后是 `excludedFileNameExtensions` 。 如果这两个列表中有相同的文件扩展名，则会将其从索引中排除。

### <a name="add-skip-metadata-the-blob"></a>在 blob 中添加 "skip" 元数据

索引器配置参数适用于容器或文件夹中的所有 blob。 有时，您需要控制 *各个 blob* 的索引方式。 为此，可将以下元数据属性和值添加到 Blob 存储中的 blob。 当索引器遇到此属性时，它将跳过索引运行中的 blob 或其内容。

| 属性名称 | 属性值 | 说明 |
| ------------- | -------------- | ----------- |
| `AzureSearch_Skip` |`"true"` |指示 Blob 索引器完全跳过该 Blob， 既不尝试提取元数据，也不提取内容。 如果特定的 Blob 反复失败并且中断编制索引过程，此属性非常有用。 |
| `AzureSearch_SkipContent` |`"true"` |此属性等效于[上面](#PartsOfBlobToIndex)所述的与特定 Blob 相关的 `"dataToExtract" : "allMetadata"` 设置。 |

## <a name="index-large-datasets"></a>为大型数据集编制索引

Blob 编制索引可能是一个耗时的过程。 如果有数百万个要编制索引的 blob，可以通过对数据进行分区并使用多个索引器来 [并行处理数据](search-howto-large-index.md#parallel-indexing)，从而加快索引。 设置方法如下：

+ 将数据分区到多个 Blob 容器或虚拟文件夹

+ 设置多个数据源，每个容器或文件夹一个。 若要指向某个 Blob 文件夹，请使用 `query` 参数：

    ```json
    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : "my-folder" }
    }
    ```

+ 为每个数据源创建相应的索引器。 所有索引器都应指向同一目标搜索索引。  

+ 服务中的每个搜索单位在任何给定的时间都只能运行一个索引器。 只有当索引器实际上并行运行时，如上所述创建多个索引器才很有用。

  若要并行运行多个索引器，请通过创建合适数量的分区和副本来横向扩展搜索服务。 例如，如果搜索服务有 6 个搜索单位（例如，2 个分区 x 3 个副本），则 6 个索引器可以同时运行，导致索引吞吐量增加六倍。 若要详细了解缩放和容量规划，请参阅[调整 Azure 认知搜索服务的容量](search-capacity-planning.md)。

<a name="DealingWithErrors"></a>

## <a name="handling-errors"></a>处理错误

在索引过程中经常发生的错误包括不支持的内容类型、缺少的内容或超大的 blob。

默认情况下，Blob 索引器一旦遇到包含不受支持内容类型（例如图像）的 Blob 时，就会立即停止。 可以使用 `excludedFileNameExtensions` 参数跳过某些内容类型。 但是，你可能希望在出现错误时继续编制索引，然后在以后调试各个文档。 有关索引器错误的详细信息，请参阅 [排查常见索引器问题](search-indexer-troubleshooting.md) 和 [索引器错误和警告](cognitive-search-common-errors-warnings.md)。

### <a name="respond-to-errors"></a>响应错误

出现错误时，可以通过四个索引器属性控制索引器的响应。 下面的示例演示如何在索引器定义中设置这些属性。 如果索引器已存在，则可以通过在门户中编辑定义来添加这些属性。

#### <a name="maxfaileditems-and-maxfaileditemsperbatch"></a>`"maxFailedItems"` 和 `"maxFailedItemsPerBatch"`

如果在任意处理时间点发生错误，则在分析 blob 或向索引添加文档时继续编制索引。 将这些属性设置为可接受的失败数。 如果值为 `-1` ，则不管发生多少错误，都可以进行处理。 否则，该值为正整数。

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 10 }
}
```

#### <a name="failonunsupportedcontenttype-and-failonunprocessabledocument"></a>`"failOnUnsupportedContentType"` 和 `"failOnUnprocessableDocument"` 

对于某些 blob，Azure 认知搜索无法确定内容类型，或者无法处理其他受支持的内容类型的文档。 若要忽略这些失败条件，请将配置参数设置为 `false` ：

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "failOnUnsupportedContentType" : false, "failOnUnprocessableDocument" : false } }
}
```

### <a name="relax-indexer-constraints"></a>放宽索引器约束

还可以设置可有效确定是否存在错误情况的 [blob 配置属性](/rest/api/searchservice/create-indexer#blob-configuration-parameters) 。 下面的属性可以放宽约束，禁止显示发生的错误。

+ `"indexStorageMetadataOnlyForOversizedDocuments"` 为太大而无法处理的 blob 内容编制索引。 过大的 blob 会被默认视为错误。 有关 blob 大小的限制，请参阅 [服务限制](search-limits-quotas-capacity.md)。

## <a name="see-also"></a>另请参阅

+ [Azure 认知搜索中的索引器](search-indexer-overview.md)
+ [创建索引器](search-howto-create-indexers.md)
+ [AI 扩充 over blob 概述](search-blob-ai-integration.md)
+ [搜索 blob 概述](search-blob-storage-integration.md)