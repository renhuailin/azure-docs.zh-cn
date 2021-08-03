---
title: 使用 Azure 认知搜索索引器连接到 Azure MySQL 内容并为其编制索引（预览）
titleSuffix: Azure Cognitive Search
description: 将 Azure MySQL 中的数据导入 Azure 认知搜索中的可搜索索引。 索引器可自动为所选数据源（如 MySQL）引入数据。
author: markheff
manager: luisca
ms.author: maheff
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 05/17/2021
ms.openlocfilehash: 4dee2250d49d437d47148b873cfe8c7ce1e8f5ea
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/09/2021
ms.locfileid: "111754432"
---
# <a name="connect-to-and-index-azure-mysql-content-using-an-azure-cognitive-search-indexer-preview"></a>使用 Azure 认知搜索索引器连接到 Azure MySQL 内容并为其编制索引（预览）

> [!IMPORTANT] 
> MySQL 支持目前以公共预览版提供。 提供的预览版功能不附带服务级别协议，我们不建议将其用于生产工作负荷。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 可以填写[此表单](https://aka.ms/azure-cognitive-search/indexer-preview)来请求访问预览版。 [REST API 版本 2020-06-30-Preview](search-api-preview.md) 提供此功能。 目前尚无 SDK 支持，且不支持门户。

用于 MySQL 的 Azure 认知搜索索引器将在 Azure 中抓取 MySQL 数据库，提取可搜索的数据并在 Azure 认知搜索中编制索引。 此索引器将获取 MySQL 数据库的所有更改、上传和删除，并在 Azure 认知搜索中反映这些更改。

## <a name="create-an-azure-mysql-indexer"></a>创建 Azure MySQL 索引器

若要在 Azure 上为 MySQL 编制索引，请执行以下步骤。

### <a name="step-1-create-a-data-source"></a>步骤 1：创建数据源

为创建数据源，请发送以下请求：

```http

    POST https://[search service name].search.windows.net/datasources?api-version=2020-06-30-Preview
    Content-Type: application/json
    api-key: [admin key]
    
    {   
        "name" : "[Data source name]"
        "description" : "[Description of MySQL data source]",
        "type" : "mysql",
        "credentials" : { 
            "connectionString" : 
                "Server=[MySQLServerName].MySQL.database.azure.com; Port=3306; Database=[DatabaseName]; Uid=[UserName]; Pwd=[Password]; SslMode=Preferred;" 
        },
        "container" : { 
            "name" : "[TableName]" 
        },
        "dataChangeDetectionPolicy" : { 
            "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
            "highWaterMarkColumnName": "[HighWaterMarkColumn]"
        }
    }

```

### <a name="step-2-create-an-index"></a>步骤 2：创建索引

创建目标 Azure 认知搜索索引（如果还没有）。

```http

    POST https://[service name].search.windows.net/indexes?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
       "name": "[Index name]",
       "fields": [{
         "name": "id",
         "type": "Edm.String",
         "key": true,
         "searchable": false
       }, {
         "name": "description",
         "type": "Edm.String",
         "filterable": false,
         "searchable": true,
         "sortable": false,
         "facetable": false
       }]
    }

```

### <a name="step-3-create-the-indexer"></a>步骤 3：创建索引器

创建索引和数据源后，便可以准备创建索引器。

```http

    POST https://[search service name].search.windows.net/indexers?api-version=2020-06-30-Preview
    Content-Type: application/json
    api-key: [admin key]
    
    {
        "name" : "[Indexer name]"
        "description" : "[Description of MySQL indexer]",
        "dataSourceName" : "[Data source name]",
        "targetIndexName" : "[Index name]"
    }

```

## <a name="run-indexers-on-a-schedule"></a>按计划运行索引器
还可以排列索引器，以按计划定期运行。 若要执行此操作，在创建或更新索引器时添加 **计划** 属性。 下面的示例显示了用于更新索引器的 PUT 请求：

```http
    PUT https://[search service name].search.windows.net/indexers/[Indexer name]?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin-key]

    {
        "dataSourceName" : "[Data source name]",
        "targetIndexName" : "[Index name]",
        "schedule" : { 
            "interval" : "PT10M", 
            "startTime" : "2021-01-01T00:00:00Z"
        }
    }
```

**间隔** 参数是必需的。 间隔是指开始两个连续的索引器执行之间的时间。 允许的最小间隔为 5 分钟；最长为一天。 必须将其格式化为 XSD“dayTimeDuration”值（[ISO 8601 持续时间](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration)值的受限子集）。 它的模式为：`P(nD)(T(nH)(nM))`。 示例：`PT15M` 为每隔 15 分钟，`PT2H` 为每隔 2 小时。

若要详细了解如何定义索引器计划，请参阅[如何为 Azure 认知搜索计划索引器](search-howto-schedule-indexers.md)。

## <a name="capture-new-changed-and-deleted-rows"></a>捕获新的、更改的和删除的行

Azure 认知搜索使用  “增量索引编制”来避免索引器每次运行时都必须为整个表或视图重新编制索引。

<a name="HighWaterMarkPolicy"></a>

### <a name="high-water-mark-change-detection-policy"></a>高使用标记更改检测策略

此更改检测策略依赖于对版本或行的上次更新时间进行捕获的一个“高使用标记”列。 如果在使用视图，则必须使用高使用标记策略。 高使用标记列必须满足以下要求。

#### <a name="requirements"></a>要求 

* 所有插入都为列指定一个值。
* 对某个项目的所有更新也会更改该列的值。
* 此列的值随每次插入或更新而增加。
* 具有以下 WHERE 和 ORDER BY 子句的查询可以高效执行：`WHERE [High Water Mark Column] > [Current High Water Mark Value] ORDER BY [High Water Mark Column]`

#### <a name="usage"></a>使用情况

若要使用高使用标记策略，请按如下所示创建或更新数据源：

```http
    {
        "name" : "[Data source name]",
        "type" : "mysql",
        "credentials" : { "connectionString" : "[connection string]" },
        "container" : { "name" : "[table or view name]" },
        "dataChangeDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
           "highWaterMarkColumnName" : "[last_updated column name]"
      }
    }
```

> [!WARNING]
> 如果源表在高使用标记列上没有索引，MySQL 索引器使用的查询可能会超时。特别是，当表中包含多个行时，`ORDER BY [High Water Mark Column]` 子句需要索引才能有效运行。

### <a name="soft-delete-column-deletion-detection-policy"></a>软删除列删除检测策略
从源表中删除行时，可能还希望从搜索索引中删除这些行。 如果以物理方式从表中删除行，Azure 认知搜索无法推断出不再存在的记录是否存在。  但是，可使用“软删除”技术以逻辑方式删除行，无需从表中删除它们。 将列添加到表或视图，并使用该列将行标记为已删除。

使用软删除技术时，可在创建或更新数据源时，按如下方式指定软删除策略：

```http
    {
        …,
        "dataDeletionDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
           "softDeleteColumnName" : "[a column name]",
           "softDeleteMarkerValue" : "[the value that indicates that a row is deleted]"
        }
    }
```

**SoftDeleteMarkerValue** 必须是字符串 - 使用实际值的字符串表示形式。 例如，如果有一个整数列（使用值 1 标记删除的行），则使用 `"1"`。 如果有一个 BIT 列（使用布尔值 true 标记删除的行），请使用字符串文本 `True` 或 `true`（不区分大小写）。

<a name="TypeMapping"></a>

## <a name="mapping-between-mysql-and-azure-cognitive-search-data-types"></a>MySQL 数据类型与 Azure 认知搜索数据类型之间的映射

> [!NOTE]
> 预览不支持几何图形类型和 blob。

| MySQL 数据类型 | 允许的目标索引字段类型 |
| --- | --- |
| bool、boolean | Edm.Boolean、Edm.String |
| tinyint，smallint，mediumint，int，integer，year | Edm.Int32、Edm.Int64、Edm.String |
| bigint | Edm.Int64、Edm.String |
| float、double、real | Edm.Double、Edm.String |
| date、datetime、timestamp | Edm.DateTimeOffset、Edm.String |
| char、varchar、tinytext、mediumtext、text、longtext、enum、set、time | Edm.String |
| 无符号数值数据、serial、decimal、dec、bit、blob、binary、geometry | 空值 |


## <a name="next-steps"></a>后续步骤

祝贺你！ 你已了解如何使用索引器将 MySQL 与 Azure 认知搜索集成。

+ 若要详细了解索引器，请参阅[在 Azure 认知搜索中创建索引器](search-howto-create-indexers.md)
