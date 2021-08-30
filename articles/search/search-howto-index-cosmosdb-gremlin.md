---
title: Gremlin API 中的索引数据（预览）
titleSuffix: Azure Cognitive Search
description: 设置 Azure Cosmos DB 索引器，以便自动为 Azure 认知搜索中的全文搜索编制 Gremlin API 内容的索引。
author: MarkHeff
ms.author: maheff
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/11/2021
ms.openlocfilehash: 69642aa1b9d977591bee6dbb8464cdf74ca7be1e
ms.sourcegitcommit: f2eb1bc583962ea0b616577f47b325d548fd0efa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/28/2021
ms.locfileid: "114731003"
---
# <a name="index-data-using-azure-cosmos-db-gremlin-api"></a>使用 Azure Cosmos DB Gremlin API 编制数据索引

> [!IMPORTANT]
> Cosmos DB Gremlin API 索引器根据[补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)处于公共预览阶段。 [请求访问](https://aka.ms/azure-cognitive-search/indexer-preview)此功能，并在启用访问后，使用[预览版 REST API（2020-06-30-preview 或更高版本）](search-api-preview.md)为内容编制索引。 目前提供有限的门户支持，不提供 .NET SDK 支持。

本文介绍如何配置 Azure Cosmos DB 索引器以提取内容，并使内容在 Azure 认知搜索中可搜索。 此工作流会在 Azure 认知搜索上创建搜索索引，并通过现有内容（使用 Gremlin API 从 Azure Cosmos DB 中提取）加载此索引。

由于术语可能会造成混淆，特此提示，[Azure Cosmos DB 索引编制](../cosmos-db/index-overview.md)和 [Azure 认知搜索索引编制](search-what-is-an-index.md)属于不同的操作，且是每个服务中特有的操作。 在开始执行 Azure 认知搜索索引编制之前，Azure Cosmos DB 数据库必须已存在且包含数据。

## <a name="prerequisites"></a>先决条件

为了使 Azure 认知搜索能够通过 Gremlin API 索引 Cosmos DB 中的数据，还必须启用 [Cosmos DB 自己的索引](../cosmos-db/index-overview.md)，并将其设置为[一致](../cosmos-db/index-policy.md#indexing-mode)。 这是 Cosmos DB 的默认配置。 如果未启用 Cosmos DB 的索引，Azure 认知搜索索引将无法正常工作。

## <a name="get-started"></a>开始使用

可以遵循 Azure 认知搜索中所有索引器通用的一个三部分工作流，使用[预览 REST API](/rest/api/searchservice/index-preview) 为 Gremlin API 提供的 Azure Cosmos DB 数据编制索引：创建数据源、创建索引、创建索引器。 在下面的过程中，提交创建索引器请求时便开始从 Cosmos DB 提取数据。

默认情况下，Azure 认知搜索 Cosmos DB Gremlin API 索引器会使图中的每个顶点都成为索引中的文档。 边缘将被忽略。 或者可以将查询设置为边缘编制索引。

### <a name="step-1---assemble-inputs-for-the-request"></a>步骤 1 - 汇集请求的输入

对于每个请求，必须提供 Azure 搜索的服务名称和管理密钥（在 POST 标头中）。 可以使用 [Postman](./search-get-started-rest.md) 或任何 REST API 客户端将 HTTPS 请求发送到 Azure 认知搜索。

复制并保存以下值以在请求中使用：

+ Azure 认知搜索服务名称
+ Azure 认知搜索管理密钥
+ Cosmos DB Gremlin API 连接字符串

可以在 Azure 门户中找到以下值：

1. 在 Azure 认知搜索的门户页中，从“概述”页复制搜索服务 URL。

2. 在左侧导航窗格中单击“密钥”，然后复制主密钥或辅助密钥。

3. 切换到 Cosmos DB 帐户的门户页。 在左侧导航窗格中的“设置”下，单击“密钥”。  此页提供一个 URI、两组连接字符串和两组密钥。 请将其中一个连接字符串复制到记事本。

### <a name="step-2---create-a-data-source"></a>步骤 2 - 创建数据源

**数据源** 指定要编制索引的数据、凭据和用于识别数据更改（如修改或删除了集合内的文档）的策略。 数据源定义为独立的资源，以便它可以被多个索引器使用。

若要创建数据源，请构建 POST 请求：

```http
    POST https://[service name].search.windows.net/datasources?api-version=2020-06-30-Preview
    Content-Type: application/json
    api-key: [Search service admin key]
    
    {
        "name": "mycosmosdbgremlindatasource",
        "type": "cosmosdb",
        "credentials": {
            "connectionString": "AccountEndpoint=https://myCosmosDbEndpoint.documents.azure.com;AccountKey=myCosmosDbAuthKey;ApiKind=Gremlin;Database=myCosmosDbDatabaseId"
        },
        "container": { "name": "myGraphId", "query": null }
    }
```

请求正文包含数据源定义，其中应包括以下字段：

| 字段   | 说明 |
|---------|-------------|
| name | 必需。 选择任意名称来表示你的数据源对象。 |
|type| 必需。 必须是 `cosmosdb`。 |
|**凭据** | 必需。 connectionString 必须包含 AccountEndpoint、AccountKey、ApiKind 和数据库。 API 种类是 Gremlin。</br></br>例如：<br/>`AccountEndpoint=https://<Cosmos DB account name>.documents.azure.com;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>;ApiKind=Gremlin`<br/><br/>AccountEndpoint 必须使用 `*.documents.azure.com` 终结点。
| **容器** | 包含下列元素： <br/>**名称**：必需。 指定图形的 ID。<br/>**查询**：可选。 默认为 `g.V()`。 要为边缘编制索引，请将查询设置为 `g.E()`。 |
| **dataChangeDetectionPolicy** | 默认情况下，使用 `_ts` 作为高水位标记列启用增量进度。 |
|**dataDeletionDetectionPolicy** | 可选。 请参阅[为已删除的文档编制索引](#DataDeletionDetectionPolicy)部分。|

### <a name="step-3---create-a-target-search-index"></a>步骤 3 - 创建目标搜索索引

[创建目标 Azure 认知搜索索引](/rest/api/searchservice/create-index)（如果没有）。 下面的示例创建带有 ID、标签和描述字段的索引：

```http
    POST https://[service name].search.windows.net/indexes?api-version=2020-06-30-Preview
    Content-Type: application/json
    api-key: [Search service admin key]

    {
       "name": "mysearchindex",
       "fields": [
        {
            "name": "rid",
            "type": "Edm.String",
            "facetable": false,
            "filterable": false,
            "key": true,
            "retrievable": true,
            "searchable": true,
            "sortable": false,
            "analyzer": "standard.lucene",
            "indexAnalyzer": null,
            "searchAnalyzer": null,
            "synonymMaps": [],
            "fields": []
        },{
            "name": "id",
            "type": "Edm.String",
            "searchable": true,
            "filterable": false,
            "retrievable": true,
            "sortable": false,
            "facetable": false,
            "key": false,
            "indexAnalyzer": null,
            "searchAnalyzer": null,
            "analyzer": "standard.lucene",
            "synonymMaps": []
        }, {
            "name": "label",
            "type": "Edm.String",
            "searchable": true,
            "filterable": false,
            "retrievable": true,
            "sortable": false,
            "facetable": false,
            "key": false,
            "indexAnalyzer": null,
            "searchAnalyzer": null,
            "analyzer": "standard.lucene",
            "synonymMaps": []
       }]
     }
```

请确保目标索引的架构与图形兼容。

对于已分区集合，默认文档键是 Azure Cosmos DB 的 `_rid` 属性，Azure 认知搜索会自动将其重命名为 `rid`，因为字段名称不能以下划线字符开头。 此外，Azure Cosmos DB 的 `_rid` 值包含了在 Azure 认知搜索键中无效的字符。 出于此原因，如果希望将 `_rid` 值作为文档键，则应采用 Base64 编码。

### <a name="mapping-between-json-data-types-and-azure-cognitive-search-data-types"></a>JSON 数据类型与 Azure 认知搜索数据类型之间的映射

| JSON 数据类型 | 兼容的目标索引字段类型 |
| --- | --- |
| Bool |Edm.Boolean、Edm.String |
| 类似于整数的数字 |Edm.Int32、Edm.Int64、Edm.String |
| 类似于浮点的数字 |Edm.Double、Edm.String |
| String |Edm.String |
| 基元类型的数组，如 ["a", "b", "c"] |集合 (Edm.String) |
| 类似于日期的字符串 |Edm.DateTimeOffset、Edm.String |
| GeoJSON 对象，例如 { "type":"Point", "coordinates": [long, lat] } |Edm.GeographyPoint |
| 其他 JSON 对象 |空值 |

### <a name="step-4---configure-and-run-the-indexer"></a>步骤 4 - 配置和运行索引器

创建索引和数据源后，就可以准备创建索引器了：

```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      "name": "mycosmosdbgremlinindexer",
      "description": "My Cosmos DB Gremlin API indexer",
      "dataSourceName": "mycosmosdbgremlindatasource",
      "targetIndexName": "mysearchindex"
    }
```

此索引器在创建后开始运行，并且只运行一次。 可以向请求添加可选计划参数，以将索引器设置为按计划运行。 若要详细了解如何定义索引器计划，请参阅[如何为 Azure 认知搜索计划索引器](search-howto-schedule-indexers.md)。

有关创建索引器 API 的更多详细信息，请参阅[创建索引器](/rest/api/searchservice/create-indexer)。

<a name="DataDeletionDetectionPolicy"></a>

## <a name="indexing-deleted-documents"></a>为已删除的文档编制索引

删除图形数据后，可能还需要从搜索索引中删除其相应的文档。 数据删除保护策略的目的是有效识别已删除的数据项，并删除索引中的完整文档。 数据删除检测策略并不是要删除部分文档信息。 目前，唯一支持的策略是 `Soft Delete` 策略（删除标有某种类型的标志），它按如下所示指定：

```http
    {
        "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
        "softDeleteColumnName" : "the property that specifies whether a document was deleted",
        "softDeleteMarkerValue" : "the value that identifies a document as deleted"
    }
```

下面的示例创建具有软删除策略的数据源：

```http
    POST https://[service name].search.windows.net/datasources?api-version=2020-06-30-Preview
    Content-Type: application/json
    api-key: [Search service admin key]
    
    {
        "name": "mycosmosdbgremlindatasource",
        "type": "cosmosdb",
        "credentials": {
            "connectionString": "AccountEndpoint=https://myCosmosDbEndpoint.documents.azure.com;AccountKey=myCosmosDbAuthKey;ApiKind=Gremlin;Database=myCosmosDbDatabaseId"
        },
        "container": { "name": "myCollection" },
        "dataChangeDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
            "highWaterMarkColumnName": "`_ts`"
        },
        "dataDeletionDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
            "softDeleteColumnName": "isDeleted",
            "softDeleteMarkerValue": "true"
        }
    }
```

<a name="MappingGraphData"></a>

## <a name="mapping-graph-data-to-a-search-index"></a>将图形数据映射到搜索索引

Cosmos DB Gremlin API 索引器自动为你映射几段图形数据：

1. 索引器会将 `_rid` 映射到索引器中的 `rid` 字段（如果存在）。 请注意，如果要使用 `rid` 值作为索引中的键，则应该采用 Base64 对键进行编码，因为 `_rid` 包含在 Azure 认知搜索文档键中无效的字符。

1. 索引器会将 `_id` 映射到索引器中的 `id` 字段（如果存在）。

1. 在使用 Gremlin API 查询 Cosmos DB 数据库时，你可能会注意到，每个属性的 JSON 输出都具有 `id` 和 `value`。 Azure 认知搜索 Cosmos DB 索引器会自动将属性 `value` 映射到搜索索引中与属性同名的字段（如果存在）。 在下面的示例中，450 将映射到搜索索引中的 `pages` 字段。

```http
    {
        "id": "Cookbook",
        "label": "book",
        "type": "vertex",
        "properties": {
          "pages": [
            {
              "id": "48cf6285-a145-42c8-a0aa-d39079277b71",
              "value": "450"
            }
          ]
        }
    }
```

你可能会发现，为了将查询输出映射到索引中的字段，需要使用[输出字段映射](cognitive-search-output-field-mapping.md)。 你可能需要使用输出字段映射，而不是[字段映射](search-indexer-field-mappings.md)，因为自定义查询可能具有复杂的数据。

例如，假设查询生成以下输出：

```json
    [
      {
        "vertex": {
          "id": "Cookbook",
          "label": "book",
          "type": "vertex",
          "properties": {
            "pages": [
              {
                "id": "48cf6085-a211-42d8-a8ea-d38642987a71",
                "value": "450"
              }
            ],
          }
        },
        "written_by": [
          {
            "yearStarted": "2017"
          }
        ]
      }
    ]
```

如果要将上述 JSON 中的 `pages` 值映射到索引中的 `totalpages` 字段，可以将以下[输出字段映射](cognitive-search-output-field-mapping.md)添加到索引器定义：

```json
    ... // rest of indexer definition 
    "outputFieldMappings": [
        {
          "sourceFieldName": "/document/vertex/pages",
          "targetFieldName": "totalpages"
        }
    ]
```

请注意输出字段映射如何以 `/document` 开头，并且不包括对 JSON 中属性键的引用。 这是因为索引器会在引入关系图数据时将每个文档放置在 `/document` 节点下，并且索引器还自动让你能够引用 `pages` 的值，方式是直接引用 `pages`，而不必引用 `pages` 数组中的第一个对象。

## <a name="next-steps"></a>后续步骤

+ 要了解有关 Azure Cosmos DB Gremlin API 的详细信息，请参阅 [Azure Cosmos DB：Graph API 简介]()../cosmos-db/graph-introduction.md)。

+ 有关 Azure 认知搜索方案和定价的详细信息，请参阅 [azure.microsoft.com 上的搜索服务页](https://azure.microsoft.com/services/search/)。