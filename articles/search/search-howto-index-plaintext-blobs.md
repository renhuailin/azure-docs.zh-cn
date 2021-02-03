---
title: 搜索纯文本 blob
titleSuffix: Azure Cognitive Search
description: 配置搜索索引器以从 Azure Blob 中提取纯文本，以便在 Azure 认知搜索中进行全文搜索。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/01/2021
ms.openlocfilehash: b8881d3fa7ade08da103c5af4b828a12e74cc355
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/03/2021
ms.locfileid: "99509446"
---
# <a name="how-to-index-plain-text-blobs-in-azure-cognitive-search"></a>如何在 Azure 认知搜索中为纯文本 blob 编制索引

使用 [blob 索引器](search-howto-indexing-azure-blob-storage.md) 提取全文搜索的可搜索 blob 文本时，可以分配分析模式来获取更好的索引结果。 默认情况下，索引器将 blob 内容分析为单个文本块。 但是，如果所有 blob 都包含采用相同编码的纯文本，则可以通过使用分析模式大幅提高索引性能 `text` 。

使用分析的建议 `text` 包括：

+ 文件类型为 .txt
+ 文件属于任何类型，但内容本身是文本 (例如，程序源代码、HTML、XML 等) 。 对于用标记语言表示的文件，任何语法字符都将作为静态文本。

请记住，所有索引器都序列化为 JSON。 默认情况下，整个文本文件的内容将在一个大型字段内作为索引 `"content": "<file-contents>"` 。 所有新行和返回指令都嵌入在 "内容" 字段中，并表示为 `\r\n\` 。

如果需要更精细的结果，并且文件类型兼容，请考虑以下解决方案：

+ [`delimitedText`](search-howto-index-csv-blobs.md) 分析模式（如果源为 CSV）
+ 如果源是 JSON，则为[ `jsonArray` 或 `jsonLines` ](search-howto-index-json-blobs.md)

将内容分解为多个部分的第三个选项要求使用 [AI 扩充](cognitive-search-concept-intro.md)形式的高级功能。 它添加了分析，用于标识文件块并将其分配给不同的搜索字段。 您可以通过 [内置技能](cognitive-search-predefined-skills.md)找到完整或部分解决方案，但更可能的解决方案是理解您的内容（在自定义学习模型中明确 [表述的内容](cognitive-search-custom-skill-interface.md)）的学习模型。

## <a name="set-up-plain-text-indexing"></a>设置纯文本编制索引

若要对纯文本 blob 编制索引，请使用 `text` 的 `parsingMode` 配置属性根据[创建索引器](/rest/api/searchservice/create-indexer)请求创建或更新索引器：

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "parsingMode" : "text" } }
}
```

默认情况下将采用 `UTF-8` 编码。 若要指定不同的编码，请使用 `encoding` 配置属性： 

```http
{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "parsingMode" : "text", "encoding" : "windows-1252" } }
}
```

## <a name="request-example"></a>请求示例

在索引器定义中指定分析模式。

```http
POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  "name" : "my-plaintext-indexer",
  "dataSourceName" : "my-blob-datasource",
  "targetIndexName" : "my-target-index",
  "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } }
}
```

## <a name="next-steps"></a>后续步骤

+ [Azure 认知搜索中的索引器](search-indexer-overview.md)
+ [如何配置 Blob 索引器](search-howto-indexing-azure-blob-storage.md)
+ [Blob 索引概述](search-blob-storage-integration.md)