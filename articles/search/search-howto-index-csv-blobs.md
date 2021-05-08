---
title: 搜索 CSV Blob
titleSuffix: Azure Cognitive Search
description: 使用 delimitedText 分析模式通过 Azure Blob 存储提取和导入 CSV。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/01/2021
ms.openlocfilehash: d9633031ca8358ab0498c2e806b22e6c4ddd3eab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "99430473"
---
# <a name="how-to-index-csv-blobs-using-delimitedtext-parsing-mode-and-blob-indexers-in-azure-cognitive-search"></a>如何使用 Azure 认知搜索中的 delimitedText 分析模式和 Blob 索引器为 CSV blob 编制索引

Azure 认知搜索 [Blob 索引器](search-howto-indexing-azure-blob-storage.md) 为 CSV 文件提供 `delimitedText` 分析模式，该模式将 CSV 中的每一行都视为一个单独的搜索文档。 例如，如果给定以下以逗号分隔的文本，`delimitedText` 就会在搜索索引中产生两个文档： 

```text
id, datePublished, tags
1, 2016-01-12, "azure-search,azure,cloud"
2, 2016-07-07, "cloud,mobile"
```

如果不使用 `delimitedText` 分析模式，CSV 文件的全部内容都会被视为一个搜索文档。

每次从一个 Blob 创建多个搜索文档时，请务必查看[为 Blob 编制索引以生成多个搜索文档](search-howto-index-one-to-many-blobs.md)，以了解文档键分配的工作方式。 Blob 索引器能够查找或生成独一无二地定义每个新文档的值。 具体而言，它可以创建一个在 Blob 解析为较小部分时生成的暂时性 `AzureSearch_DocumentKey`，其中的值随后会在索引中用作搜索文档的键。

## <a name="setting-up-csv-indexing"></a>设置 CSV 索引

若要对 CSV blob 编制索引，请使用 `delimitedText` 分析模式根据[创建索引器](/rest/api/searchservice/create-indexer)请求创建或更新索引器定义：

```http
{
  "name" : "my-csv-indexer",
  ... other indexer properties
  "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "firstLineContainsHeaders" : true } }
}
```

`firstLineContainsHeaders` 指示每个 blob 的第一行（非空）包含标头。
如果 blob 未包含初始标头行，则应在索引器配置中指定标头： 

```http
"parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } } 
```

可以使用 `delimitedTextDelimiter` 配置设置来自定义分隔符字符。 例如：

```http
"parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextDelimiter" : "|" } }
```

> [!NOTE]
> 目前，仅支持 UTF-8 编码。 如果需要支持其他编码，请在 [UserVoice](https://feedback.azure.com/forums/263029-azure-search) 上为其投票。

> [!IMPORTANT]
> 当使用分隔文本分析模式时，Azure 认知搜索假定数据源中的所有 blob 都将是 CSV。 如果需要在同一数据源中支持混用 CSV 和非 CSV blob，请在 [UserVoice](https://feedback.azure.com/forums/263029-azure-search) 上为其投票。
>

## <a name="request-examples"></a>请求示例

汇总后，以下是完整的有效负载示例。 

数据源： 

```http
POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "my-blob-datasource",
    "type" : "azureblob",
    "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
    "container" : { "name" : "my-container", "query" : "<optional, my-folder>" }
}   
```

索引器：

```http
POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  "name" : "my-csv-indexer",
  "dataSourceName" : "my-blob-datasource",
  "targetIndexName" : "my-target-index",
  "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } }
}
```


