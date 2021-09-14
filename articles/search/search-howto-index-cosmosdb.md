---
title: 为来自 Azure Cosmos DB 的数据编制索引
titleSuffix: Azure Cognitive Search
description: 设置搜索索引器，为 Azure Cosmos DB 中存储的数据编制索引，以便在 Azure 认知搜索中进行全文搜索。 本文介绍如何使用 SQL 和 MongoDB API 协议为数据编制索引。
author: mgottein
ms.author: magottei
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/14/2021
ms.openlocfilehash: 389ecc550fd2b9e0fa41b7437b47aa5b40af3712
ms.sourcegitcommit: 43dbb8a39d0febdd4aea3e8bfb41fa4700df3409
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123450910"
---
# <a name="index-data-from-azure-cosmos-db-using-sql-or-mongodb-apis"></a>使用 SQL 或 MongoDB API 为 Azure Cosmos DB 中的数据编制索引

> [!IMPORTANT] 
> SQL API 已推出正式版。
> 根据[补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)，MongoDB API 支持当前以公共预览版提供。 [请求访问](https://aka.ms/azure-cognitive-search/indexer-preview)，并在启用访问后，使用[预览版 REST API（2020-06-30-preview 或更高版本）](search-api-preview.md)访问数据。 目前提供有限的门户支持，不提供 .NET SDK 支持。

本文介绍如何配置 Azure Cosmos DB [索引器](search-indexer-overview.md)以提取内容，并使内容在 Azure 认知搜索中可搜索。 此工作流将创建一个 Azure 认知搜索索引，然后连同从 Azure Cosmos DB 中提取的现有文本一起加载该索引。

由于术语可能会造成混淆，特此提示，[Azure Cosmos DB 索引编制](../cosmos-db/index-overview.md)和 [Azure 认知搜索索引编制](search-what-is-an-index.md)属于不同的操作，且是每个服务中特有的操作。 在开始执行 Azure 认知搜索索引编制之前，Azure Cosmos DB 数据库必须已存在且包含数据。

Azure 认知搜索中的 Cosmos DB 索引器可以对通过以下协议访问的 [Azure Cosmos DB 项](../cosmos-db/account-databases-containers-items.md#azure-cosmos-items)进行爬网。

+ 对于 [SQL API](../cosmos-db/sql-query-getting-started.md)（正式版），可以使用[门户](#cosmos-indexer-portal)、[REST API](/rest/api/searchservice/indexer-operations)、[.NET SDK](/dotnet/api/azure.search.documents.indexes.models.searchindexer) 或其他 Azure SDK 来创建数据源和索引器。

+ 对于 [MongoDB API（预览版）](../cosmos-db/mongodb-introduction.md)，可以使用[门户](#cosmos-indexer-portal)或 [REST API 版本 2020-06-30-Preview](search-api-preview.md) 创建数据源和索引器。

## <a name="prerequisites"></a>先决条件

Azure 认知搜索仅支持将[索引策略](../cosmos-db/index-policy.md)设为[一致](../cosmos-db/index-policy.md#indexing-mode)的 Cosmos DB 集合。 不建议使用延迟索引策略为集合编制索引，这可能会导致数据丢失。 不支持禁用索引的集合。

<a name="cosmos-indexer-portal"></a>

## <a name="use-the-portal"></a>使用门户

> [!Note]
> 门户目前支持 SQL API 和 MongoDB API（预览版）。

为 Azure Cosmos DB 项编制索引的最简单方法是使用 [Azure 门户](https://portal.azure.com/)中的向导。 通过数据采样并读取容器中的元数据，Azure 认知搜索中的 [**导入数据**](search-import-data-portal.md)向导可以创建默认索引、将源字段映射到目标索引字段，并以单个操作加载索引。 根据源数据的大小和复杂性，在数分钟内就能创建一个有效的全文搜索索引。

我们建议对 Azure 认知搜索和 Azure Cosmos DB 使用同一个区域或位置，以降低延迟并避免带宽费用。

### <a name="step-1---prepare-source-data"></a>步骤 1 - 准备源数据

应准备好一个 Cosmos DB 帐户、一个已映射到 SQL API 或 MongoDB API（预览版）的 Azure Cosmos DB 数据库，以及数据库中的内容。

确保 Cosmos DB 数据库包含数据。 [导入数据向导](search-import-data-portal.md)会读取元数据并执行数据采样以推断索引架构，但它还会从 Cosmos DB 加载数据。 如果缺少数据，该向导会停止并出现以下错误：“从数据源检测索引架构时出错:由于数据源 'emptycollection' 未返回任何数据，无法生成原型索引”。

### <a name="step-2---start-import-data-wizard"></a>步骤 2 - 启动“导入数据”向导

可以从 Azure 认知搜索服务页中的命令栏[启动向导](search-import-data-portal.md)；如果要连接到 Cosmos DB SQL API，可以在 Cosmos DB 帐户左侧导航窗格的“设置”部分单击“添加 Azure 认知搜索”。

   :::image type="content" source="media/search-import-data-portal/import-data-cmd.png" alt-text="“导入数据”命令的屏幕截图" border="true":::

### <a name="step-3---set-the-data-source"></a>步骤 3 - 设置数据源

在“数据源”页中，源必须是“Cosmos DB”，其规范如下：

+ “名称”是数据源对象的名称。 创建后，可以选择将它用于其他工作负荷。

+ Cosmos DB 帐户应采用以下格式之一：
    1. Cosmos DB 中的主要或辅助连接字符串，采用以下格式：`AccountEndpoint=https://<Cosmos DB account name>.documents.azure.com;AccountKey=<Cosmos DB auth key>;`。
        + 对于 3.2 和 3.6 版 **MongoDB 集合**，请对 Azure 门户中的 Cosmos DB 帐户使用以下格式：`AccountEndpoint=https://<Cosmos DB account name>.documents.azure.com;AccountKey=<Cosmos DB auth key>;ApiKind=MongoDb`
    1.  采用以下格式的托管标识连接字符串（不包含帐户密钥）：`ResourceId=/subscriptions/<your subscription ID>/resourceGroups/<your resource group name>/providers/Microsoft.DocumentDB/databaseAccounts/<your cosmos db account name>/;(ApiKind=[api-kind];)`。 若要使用此连接字符串格式，请按照有关[使用托管标识设置与 Cosmos DB 数据库的索引器连接](search-howto-managed-identities-cosmos-db.md)的说明进行操作。

+ “数据库”是帐户中的现有数据库。 

+ “集合”是文档的容器。 若要成功导入，文档必须存在。 

+ 若要查询所有文档，可将“查询”留空；否则，可以输入一个选择文档子集的查询。 “查询”仅适用于 SQL API。

   ![Cosmos DB 数据源定义](media/search-howto-index-cosmosdb/cosmosdb-datasource.png "Cosmos DB 数据源定义")

### <a name="step-4---skip-the-enrich-content-page-in-the-wizard"></a>步骤 4 - 跳过向导中的“扩充内容”页

完成导入并不要求添加认知技能（或扩充）。 除非你有具体的理由需要[将 AI 扩充添加](cognitive-search-concept-intro.md)到索引管道，否则可以跳过此步骤。

若要跳过该步骤，请单击页面底部的“下一步”和“跳过”蓝色按钮。

### <a name="step-5---set-index-attributes"></a>步骤 5 - 设置索引属性

在“索引”页中，应会看到带有数据类型的字段列表，以及一系列用于设置索引属性的复选框。 向导可以通过源数据采样，基于元数据生成字段列表。 

可以通过单击属性列顶部的复选框，来批量选择属性。 对于应该返回给客户端应用并且需要接受全文搜索处理的每个字段，请选择“可检索”和“可搜索”。  你会注意到，无法对整数进行全文搜索或模糊搜索（数字按原义评估，通常在筛选器中使用）。

有关详细信息，请查看[索引属性](/rest/api/searchservice/create-index#bkmk_indexAttrib)和[语言分析器](/rest/api/searchservice/language-support)的说明。 

花费片刻时间来检查所做的选择。 运行向导后，将创建物理数据结构，到时，除非删除再重新创建所有对象，否则无法编辑这些字段。

   ![Cosmos DB 索引定义](media/search-howto-index-cosmosdb/cosmosdb-index-schema.png "Cosmos DB 索引定义")

### <a name="step-6---create-indexer"></a>步骤 6 - 创建索引器

完全指定设置后，向导将在搜索服务中创建三个不同的对象。 数据源对象和索引对象作为命名的资源保存在 Azure 认知搜索服务中。 最后一个步骤创建索引器对象。 为索引器命名可让它作为独立的资源存在，无论在同一向导序列中创建了哪种索引和数据源对象，都可以计划和管理该索引器。

如果你不熟悉索引器，请记住，索引器是 Azure 认知搜索中的一个资源，它可以抓取外部数据源，以检索可搜索的内容。 “导入数据”向导的输出是抓取 Cosmos DB 数据源、提取可搜索内容，然后将此内容导入 Azure 认知搜索中的某个索引的索引器。

以下屏幕截图显示了默认的索引器配置。 若要运行索引器一次，可以切换为“一次”。 单击“提交”运行向导并创建所有对象。 随后会立即开始编制索引。

   ![Cosmos DB 索引器定义](media/search-howto-index-cosmosdb/cosmosdb-indexer.png "Cosmos DB 索引器定义")

可以在门户页监视数据导入。 进度通知指示索引状态以及已上传的文档数。 

索引编制完成后，可以使用[搜索浏览器](search-explorer.md)来查询索引。

> [!NOTE]
> 如果未看到所需的数据，可能需要在更多字段中设置更多的属性。 删除刚刚创建的索引和索引器，再次完成向导中的每个步骤，并修改在步骤 5 中对索引属性所做的选择。 

<a name="cosmosdb-indexer-rest"></a>

## <a name="use-rest-apis"></a>使用 REST API

可以遵循 Azure 认知搜索中所有索引器通用的三部分工作流，使用 REST API 为 Azure Cosmos DB 数据编制索引：创建数据源、创建索引、创建索引器。 在下面的过程中，提交创建索引器请求时便开始从 Cosmos DB 提取数据。

本文前面已指出，[Azure Cosmos DB 索引编制](../cosmos-db/index-overview.md)和 [Azure 认知搜索索引编制](search-what-is-an-index.md)属于不同的操作。 对于 Cosmos DB 索引编制，默认情况下会为所有文档自动编制索引。 如果关闭自动索引编制，则只能通过文档本身的链接或使用文档 ID 进行查询的方法访问文档。 Azure 认知搜索索引编制要求在将由 Azure 认知搜索编制索引的集合中启用 Cosmos DB 自动索引编制。

> [!WARNING]
> Azure Cosmos DB 是下一代 DocumentDB。 在以前的 API 版本 **2017-11-11** 中，可以使用 `documentdb` 语法。 这意味着，可将数据源类型指定为 `cosmosdb` 或 `documentdb`。 从 API 版本 **2019-05-06** 开始，Azure 认知搜索 API 和门户都仅支持本文中所述的 `cosmosdb` 语法。 这意味着，若要连接到 Cosmos DB 终结点，数据源类型必须是 `cosmosdb`。

### <a name="step-1---assemble-inputs-for-the-request"></a>步骤 1 - 汇集请求的输入

对于每个请求，必须提供 Azure 认知搜索的服务名称和管理密钥（在 POST 标头中），以及 Blob 存储的存储帐户名称和密钥。 可以使用 [Postman](search-get-started-rest.md) 或 [Visual Studio Code](search-get-started-vs-code.md) 将 HTTP 请求发送到 Azure 认知搜索。

复制以下三个值以便在请求中使用：

+ Azure 认知搜索服务名称
+ Azure 认知搜索管理密钥
+ Cosmos DB 连接字符串

可以在门户中找到这些值：

1. 在 Azure 认知搜索的门户页中，从“概述”页复制搜索服务 URL。

2. 在左侧导航窗格中单击“密钥”，然后复制主密钥或辅助密钥。

3. 切换到 Cosmos 存储帐户的门户页。 在左侧导航窗格中的“设置”下，单击“密钥”。  此页提供一个 URI、两组连接字符串和两组密钥。 请将其中一个连接字符串复制到记事本。

### <a name="step-2---create-a-data-source"></a>步骤 2 - 创建数据源

**数据源** 指定要编制索引的数据、凭据和用于识别数据更改（如修改或删除了集合内的文档）的策略。 数据源定义为独立的资源，以便它可以被多个索引器使用。

若要创建数据源，请构建 POST 请求：

```http

    POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
    Content-Type: application/json
    api-key: [Search service admin key]

    {
        "name": "mycosmosdbdatasource",
        "type": "cosmosdb",
        "credentials": {
            "connectionString": "AccountEndpoint=https://myCosmosDbEndpoint.documents.azure.com;AccountKey=myCosmosDbAuthKey;Database=myCosmosDbDatabaseId"
        },
        "container": { "name": "myCollection", "query": null },
        "dataChangeDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
            "highWaterMarkColumnName": "_ts"
        }
    }
```

请求正文包含数据源定义，其中应包括以下字段：

| 字段   | 说明 |
|---------|-------------|
| name | 必需。 选择任意名称来表示你的数据源对象。 |
|type| 必需。 必须是 `cosmosdb`。 |
|**凭据** | 必需。 必须是 Cosmos DB 连接字符串。<br/><br/>对于 **SQL 集合**，连接字符串采用以下格式：`AccountEndpoint=https://<Cosmos DB account name>.documents.azure.com;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>`<br/><br/>对于 3.2 和 3.6 版 **MongoDB 集合**，请对连接字符串使用以下格式：`AccountEndpoint=https://<Cosmos DB account name>.documents.azure.com;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>;ApiKind=MongoDb`<br/><br/>避免在终结点 URL 中包含端口号。 如果包含端口号，Azure 认知搜索将无法为 Azure Cosmos DB 数据库编制索引。|
| **容器** | 包含下列元素： <br/>**名称**：必需。 指定要编制索引的数据库集合的 ID。<br/>**查询**：可选。 可以指定一个查询来将一个任意 JSON 文档平整成 Azure 认知搜索可编制索引的平面架构。<br/>MongoDB API 不支持查询。 |
| **dataChangeDetectionPolicy** | 推荐。 请参阅[为已更改的文档编制索引](#DataChangeDetectionPolicy)部分。|
|**dataDeletionDetectionPolicy** | 可选。 请参阅[为已删除的文档编制索引](#DataDeletionDetectionPolicy)部分。|

### <a name="using-queries-to-shape-indexed-data"></a>使用查询形成索引数据
可以指定一个 SQL 查询来平展嵌套的属性或数组、投影 JSON 属性并筛选要编制索引的数据。 

> [!WARNING]
> MongoDB API 不支持自定义查询：必须将 `container.query` 参数设置为 null，或将其省略。 

示例文档：

```http
    {
        "userId": 10001,
        "contact": {
            "firstName": "andy",
            "lastName": "hoh"
        },
        "company": "microsoft",
        "tags": ["azure", "cosmosdb", "search"]
    }
```

筛选查询：

```sql
SELECT * FROM c WHERE c.company = "microsoft" and c._ts >= @HighWaterMark ORDER BY c._ts
```

平展查询：

```sql
SELECT c.id, c.userId, c.contact.firstName, c.contact.lastName, c.company, c._ts FROM c WHERE c._ts >= @HighWaterMark ORDER BY c._ts
```

投影查询：

```sql
SELECT VALUE { "id":c.id, "Name":c.contact.firstName, "Company":c.company, "_ts":c._ts } FROM c WHERE c._ts >= @HighWaterMark ORDER BY c._ts
```

数组平展查询：

```sql
SELECT c.id, c.userId, tag, c._ts FROM c JOIN tag IN c.tags WHERE c._ts >= @HighWaterMark ORDER BY c._ts
```

<a name="SelectDistinctQuery"></a>

#### <a name="distinct-and-group-by"></a>DISTINCT 和 GROUP BY

不支持使用 [DISTINCT 关键字](../cosmos-db/sql-query-keywords.md#distinct)或 [GROUP BY 子句](../cosmos-db/sql-query-group-by.md)进行查询。 Azure 认知搜索依赖 [SQL 查询分页](../cosmos-db/sql-query-pagination.md)来完全枚举查询的结果。 DISTINCT 关键字或 GROUP BY 子句与用于对结果进行分页的[延续令牌](../cosmos-db/sql-query-pagination.md#continuation-tokens)均不兼容。

不受支持的查询示例：
```sql
SELECT DISTINCT c.id, c.userId, c._ts FROM c WHERE c._ts >= @HighWaterMark ORDER BY c._ts

SELECT DISTINCT VALUE c.name FROM c ORDER BY c.name

SELECT TOP 4 COUNT(1) AS foodGroupCount, f.foodGroup FROM Food f GROUP BY f.foodGroup
```

尽管 Cosmos DB 有一种变通方法来支持[使用 ORDER BY 子句对具有 DISTINCT 关键字的 SQL 查询进行分页](../cosmos-db/sql-query-pagination.md#continuation-tokens)，但它与 Azure 认知搜索不兼容。 查询将返回一个 JSON 值，而 Azure 认知搜索需要一个 JSON 对象。

```sql
-- The following query returns a single JSON value and isn't supported by Azure Cognitive Search
SELECT DISTINCT VALUE c.name FROM c ORDER BY c.name
```

### <a name="step-3---create-a-target-search-index"></a>步骤 3 - 创建目标搜索索引 

[创建目标 Azure 认知搜索索引](/rest/api/searchservice/create-index)（如果没有）。 以下示例创建带有 ID 和说明字段的索引：

```http
    POST https://[service name].search.windows.net/indexes?api-version=2020-06-30
    Content-Type: application/json
    api-key: [Search service admin key]

    {
       "name": "mysearchindex",
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
         "facetable": false,
         "suggestions": true
       }]
     }
```

确保目标索引的架构与源 JSON 文档的架构或自定义查询投影的输出的架构兼容。

> [!NOTE]
> 对于已分区集合，默认文档键是 Azure Cosmos DB 的 `_rid` 属性，Azure 认知搜索会自动将其重命名为 `rid`，因为字段名称不能以下划线字符开头。 此外，Azure Cosmos DB 的 `_rid` 值包含了在 Azure 认知搜索键中无效的字符。 因此，`_rid` 值采用 Base64 编码。
> 
> 对于 MongoDB 集合，Azure 认知搜索会自动将 `_id` 属性重命名为 `id`。  

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
      "name" : "mycosmosdbindexer",
      "dataSourceName" : "mycosmosdbdatasource",
      "targetIndexName" : "mysearchindex",
      "schedule" : { "interval" : "PT2H" }
    }
```

此索引器每两小时运行一次（已将计划间隔设置为“PT2H”）。 若要每隔 30 分钟运行一次索引器，可将间隔设置为“PT30M”。 支持的最短间隔为 5 分钟。 计划是可选的 - 如果省略，则索引器在创建后只运行一次。 但是，可以随时根据需要运行索引器。   

有关创建索引器 API 的更多详细信息，请参阅[创建索引器](/rest/api/searchservice/create-indexer)。

若要详细了解如何定义索引器计划，请参阅[如何为 Azure 认知搜索计划索引器](search-howto-schedule-indexers.md)。

## <a name="use-net"></a>使用 .NET

正式版 .NET SDK 完全可与正式版 REST API 搭配使用。 我们建议查看前面的 REST API 部分，以了解相关概念、工作流和要求。 然后，可以参阅以下 .NET API 参考文档，在托管代码中实现 JSON 索引器。

+ [azure.search.documents.indexes.models.searchindexerdatasourceconnection](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourceconnection)
+ [azure.search.documents.indexes.models.searchindexerdatasourcetype](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourcetype)
+ [azure.search.documents.indexes.models.searchindex](/dotnet/api/azure.search.documents.indexes.models.searchindex)
+ [azure.search.documents.indexes.models.searchindexer](/dotnet/api/azure.search.documents.indexes.models.searchindexer)

<a name="DataChangeDetectionPolicy"></a>

## <a name="indexing-changed-documents"></a>为已更改的文档编制索引

数据更改检测策略旨在有效识别已更改的数据项。 目前，唯一支持的策略是使用 Azure Cosmos DB 提供的 `_ts`（时间戳）属性的 [`HighWaterMarkChangeDetectionPolicy`](/dotnet/api/azure.search.documents.indexes.models.highwatermarkchangedetectionpolicy)，该属性按如下所示指定：

```http
    {
        "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
        "highWaterMarkColumnName" : "_ts"
    }
```

强烈建议使用此策略，以确保索引器性能良好。 

如果使用自定义查询，请确保查询投影 `_ts` 属性。

<a name="IncrementalProgress"></a>

### <a name="incremental-progress-and-custom-queries"></a>增量操作和自定义查询

索引编制过程中的增量操作可确保由于暂时性故障或执行时间限制而中断索引器执行时，索引器能够在下次运行时从中断位置运行，而不是从头开始重新为整个集合编制索引。 在为大型集合编制索引时，这一点尤其重要。 

要在使用自定义查询时启用增量操作，请确保查询按照 `_ts` 列对结果进行排序。 这会启用定期检查点，在出现故障时，Azure 认知搜索可以利用检查点提供增量操作。   

在某些情况下，即使查询包含 `ORDER BY [collection alias]._ts` 子句，Azure 认知搜索也可能不会推断出查询是按照 `_ts` 进行排序的。 可以告知 Azure 认知搜索，结果是通过使用 `assumeOrderByHighWaterMarkColumn` 配置属性进行排序的。 要指定此提示，请按如下所示创建或更新索引器： 

```http
    {
     ... other indexer definition properties
     "parameters" : {
            "configuration" : { "assumeOrderByHighWaterMarkColumn" : true } }
    } 
```

<a name="DataDeletionDetectionPolicy"></a>

## <a name="indexing-deleted-documents"></a>为已删除的文档编制索引

从集合中删除行时，通常还需要从搜索索引中删除这些行。 数据删除检测策略旨在有效识别已删除的数据项。 目前，唯一支持的策略是 `Soft Delete` 策略（删除标有某种类型的标志），它按如下所示指定：

```http
    {
        "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
        "softDeleteColumnName" : "the property that specifies whether a document was deleted",
        "softDeleteMarkerValue" : "the value that identifies a document as deleted"
    }
```

如果使用自定义查询，请确保查询投影由 `softDeleteColumnName` 引用的属性。

下面的示例创建具有软删除策略的数据源：

```http
    POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
    Content-Type: application/json
    api-key: [Search service admin key]

    {
        "name": "mycosmosdbdatasource",
        "type": "cosmosdb",
        "credentials": {
            "connectionString": "AccountEndpoint=https://myCosmosDbEndpoint.documents.azure.com;AccountKey=myCosmosDbAuthKey;Database=myCosmosDbDatabaseId"
        },
        "container": { "name": "myCosmosDbCollectionId" },
        "dataChangeDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
            "highWaterMarkColumnName": "_ts"
        },
        "dataDeletionDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
            "softDeleteColumnName": "isDeleted",
            "softDeleteMarkerValue": "true"
        }
    }
```

## <a name="next-steps"></a><a name="NextSteps"></a>后续步骤

祝贺！ 你已了解如何使用索引器将 Azure Cosmos DB 与 Azure 认知搜索集成。

* 若要详细了解 Azure Cosmos DB，请参阅 [Azure Cosmos DB 服务页](https://azure.microsoft.com/services/cosmos-db/)。
* 若要详细了解 Azure 认知搜索，请参阅[搜索服务页](https://azure.microsoft.com/services/search/)。