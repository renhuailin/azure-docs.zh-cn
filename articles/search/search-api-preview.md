---
title: 预览功能列表
titleSuffix: Azure Cognitive Search
description: 发布了预览功能，以便客户可以提供有关其设计和实用工具的反馈。 本文完整列出了当前预览版中的所有功能。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/21/2021
ms.openlocfilehash: 7078c07540ca1613b17b81e471bde519c7a2136c
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121725223"
---
# <a name="preview-features-in-azure-cognitive-search"></a>Azure 认知搜索中的预览版功能

本文完整列出了公共预览版中的所有功能。 提供的预览版功能不附带服务级别协议，我们不建议将其用于生产工作负荷。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

此列表中未包括已转变为正式版的预览版功能。 如果某个功能未在下面列出，你可以认为该功能已正式发布。 有关正式版的公告，请查看[服务更新](https://azure.microsoft.com/updates/?product=search)或[新增功能](whats-new.md)。

|功能&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | 类别 | 说明 | 可用性  |
|---------|------------------|-------------|---------------|
| [搜索 REST API 2021-04-30 预览版](/rest/api/searchservice/index-preview) | 安全 | 修改了“[创建或更新数据源](/rest/api/searchservice/preview-api/create-or-update-data-source)”，以支持 Azure Active Directory 下连接到外部数据源的索引器的托管标识。 | 公共预览版、[搜索 REST API 2021-04-30 预览版](/rest/api/searchservice/index-preview) |
| [**Azure RBAC 支持**](search-security-rbac.md) | 安全 | 使用新的内置角色可控制对索引和索引情况的访问，消除或降低对 API 密钥的依赖。 | 公开预览版（[根据要求提供](https://aka.ms/azure-cognitive-search/rbac-preview)）。 订阅加入后，使用 Azure 门户或管理 REST API 版本 2021-04-01 预览版可为数据平面身份验证配置搜索服务。 |
| [**管理 REST API 2021-04-01 预览版**](/rest/api/searchmanagement/) | 安全 | 修改了“[创建或更新服务](/rest/api/searchmanagement/2021-04-01-preview/services/create-or-update)”以支持新的 [DataPlaneAuthOptions](/rest/api/searchmanagement/2021-04-01-preview/services/create-or-update#dataplaneauthoptions)。 | 公共预览版、[管理 REST API](/rest/api/searchmanagement/)、API 版本的 2021-04-01 预览版。|
| [**重置文档**](search-howto-run-reset-indexers.md) | 索引 | 重新处理了索引器工作负载中单独选择的搜索文档。 | 使用[重置文档 REST API](/rest/api/searchservice/preview-api/reset-documents)、API 版本的 2021-04-30 预览版或 2020-06-30 预览版。 |
|  [**Power Query 连接器**](search-how-to-index-power-query-data-sources.md) | 索引器数据源 | 索引器现可从其他云平台建立索引。 若要使用索引器抓取外部数据源来建立索引，现可使用 Power Query 连接器连接到 Amazon Redshift、Elasticsearch、PostgreSQL、Salesforce 对象、Salesforce 报告、Smartsheet 和 Snowflake。 | 你需要[注册](https://aka.ms/azure-cognitive-search/indexer-preview)，然后我们才能在后端为你的订阅启用支持。 使用[创建或更新数据源](/rest/api/searchservice/preview-api/create-or-update-data-source)、API 版本的 2021-04-30 预览版或 2020-06-30 预览版或者 Azure 门户。|
| [**SharePoint Online 索引器**](search-howto-index-sharepoint-online.md) | 索引器数据源 | 用于 SharePoint 内容的基于索引器索引的新数据源。 | 你需要[注册](https://aka.ms/azure-cognitive-search/indexer-preview)，然后我们才能在后端为你的订阅启用支持。 使用[创建或更新数据源](/rest/api/searchservice/preview-api/create-or-update-data-source)、API 版本的 2021-04-30 预览版或 2020-06-30 预览版或者 Azure 门户。 |
|  [**MySQL 索引器数据源**](search-howto-index-mysql.md) | 索引器数据源 | 从 Auzre MySQL 数据源中索引内容和元数据。| 你需要[注册](https://aka.ms/azure-cognitive-search/indexer-preview)，然后我们才能在后端为你的订阅启用支持。 使用[创建或更新数据源](/rest/api/searchservice/preview-api/create-or-update-data-source)、API 版本的 2021-04-30 预览版或 2020-06-30 预览版、[.NET SDK 11.2.1](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourcetype.mysql) 或者 Azure 门户配置此数据源。 |
| [**Cosmos DB 索引器：MongoDB API、Gremlin API**](search-howto-index-cosmosdb.md) | 索引器数据源 | 对于 Cosmos DB，SQL API 已正式发布，但 MongoDB 和 Gremlin API 为预览版。 | 对于 MongoDB 和 Gremlin，请[首先注册](https://aka.ms/azure-cognitive-search/indexer-preview)，然后我们才能在后端为你的订阅启用支持。 MongoDB 数据源可以在门户中配置。 使用[创建或更新数据源](/rest/api/searchservice/preview-api/create-or-update-data-source)、API 版本的 2021-04-30 预览版或 2020-06-30 预览版配置此数据源。 |
| [本机 blob 软删除](search-howto-index-changed-deleted-blobs.md) | 索引器数据源 | Azure 认知搜索中的 Azure Blob 存储索引器会识别处于软删除状态的 blob，并在编制索引过程中删除相应的搜索文档。 | 使用[创建或更新数据源](/rest/api/searchservice/preview-api/create-or-update-data-source)、API 版本的 2021-04-30 预览版或 2020-06-30 预览版配置此数据源。 |
| [**语义搜索**](semantic-search-overview.md) | 相关性（评分） | 结果、描述文字和答案的语义排名。 | 使用[搜索文档](/rest/api/searchservice/preview-api/search-documents)、API 版本的 2021-04-30 预览版或 2020-06-30 预览版以及 Search Explorer（门户）配置语义搜索。 |
| [**拼写检查器**](cognitive-search-aml-skill.md) | 查询 | 对于简单、完整和语义查询的查询字词输入的可选拼写更正。 | [搜索预览 REST API](/rest/api/searchservice/preview-api/search-documents)、API 版本的 2021-04-30 预览版或 2020-06-30 预览版，以及 Search Explorer（门户）。 |
| [**Normalizers**](search-normalizers.md) | 查询 |  Normalizers 提供简单的文本预处理：一致的大小写、删除重音和 ASCII 折叠，无需调用全文分析链。| 使用[搜索文档](/rest/api/searchservice/preview-api/search-documents)、API 版本的 2021-04-30 预览版或 2020-06-30 预览版。|
| [featuresMode 参数](/rest/api/searchservice/preview-api/search-documents#query-parameters) | 相关性（评分） | 展开相关性分数以显示详细信息：单字段相似度得分、单字段术语频率，以及单字段匹配的唯一标记数。 你可以在[自定义评分解决方案](https://github.com/Azure-Samples/search-ranking-tutorial)中使用这些数据点。 | 使用[搜索文档](/rest/api/searchservice/preview-api/search-documents)、API 版本的 2021-04-30 预览版、2020-06-30 预览版或 2019-05-06 预览版添加此查询参数。 |
| [**Azure 机器学习 (AML) 技能**](cognitive-search-aml-skill.md) | AI 扩充（技能） | 集成 Azure 机器学习推理终结点的新技能类型。 请先查看[此教程](cognitive-search-tutorial-aml-custom-skill.md)。 | 使用[搜索预览 REST API](/rest/api/searchservice/)、API 版本的 2021-04-30 预览版、2020-06-30 预览版或 2019-05-06 预览版。 如果在同一订阅中部署了认知搜索和 Azure ML 服务，则门户的技能组设计中也会提供此技能。 |
| [增量扩充](cognitive-search-incremental-indexing-conceptual.md) | AI 扩充（技能） | 将缓存添加到扩充管道。如果有针对性的修改（例如更新技能集或另一对象）不会更改内容，则此功能可让你重复使用现有的输出。 缓存仅适用于技能集生成的扩充文档。| 使用[创建或更新索引器预览版 REST API](/rest/api/searchservice/create-indexer)、API 版本的 2021-04-30 预览版、2020-06-30 预览版或 2019-05-06 预览版添加此配置。 |
| [**调试会话**](cognitive-search-debug-session.md) | 门户、AI 扩充（技能） | 用于调查和解决技能组问题的会话中技能组编辑器。 可以将调试会话期间应用的修复保存到服务中的技能组。 | 仅适用于门户，使用“概述”页的页中链接打开调试会话。 |
| [moreLikeThis](search-more-like-this.md) | 查询 | 查找与特定文档相关的文档。 早期预览版中已有此功能。 | 在[搜索文档预览版 REST API](/rest/api/searchservice/search-documents) 调用中添加此查询参数，API 版本为 2021-04-30 预览版、2020-06-30 预览版、2019-05-06 预览版、2016-09-01 预览版或 2017-11-11 预览版。 |

## <a name="how-to-call-a-preview-rest-api"></a>如何调用预览版 REST API

Azure 认知搜索始终先通过 REST API 预发布实验功能，再通过 .NET SDK 的预发布版本进行发布。

预览功能可用于目的为针对功能设计和实现收集反馈的测试和试验。 为此，预览功能可能会随着时间的推移以破坏后向兼容性的方式进行更改。 这与 GA 版本中的功能形成鲜明对比，除了小型的后向兼容性修补程序和增强功能外，这些功能很稳定，不太可能发生变化。 此外，预览功能并不总是纳入 GA 版本。

虽然某些预览版功能在门户和 .NET SDK 中可能可用，但 REST API 始终具有预览版功能。

+ 对于搜索操作，当前预览版版本为 [ **`2021-04-30-Preview`**](/rest/api/searchservice/index-preview)。

+ 对于管理操作，当前预览版版本为 [ **`2021-04-01-Preview`**](/rest/api/searchmanagement/management-api-versions)。

早期预览版仍然可用，但随着时间推移会变得过时。 如果代码调用 `api-version=2019-05-06-Preview`、`api-version=2016-09-01-Preview` 或 `api-version=2017-11-11-Preview`，则这些调用仍然有效，但这些版本不会包括新功能，且无法保证提供 Bug 修补程序。

以下示例语法说明了对预览 API 版本的调用。

```HTTP
POST https://[service name].search.windows.net/indexes/hotels-idx/docs/search?api-version=2021-04-30-Preview  
  Content-Type: application/json  
  api-key: [admin key]
```

Azure 认知搜索服务可用于多个版本和客户端库。 有关详细信息，请参阅 [API 版本](search-api-versions.md)。

## <a name="next-steps"></a>后续步骤

查看搜索 REST 预览版 API 参考文档。 如果遇到问题，请通过 [Stack Overflow](https://stackoverflow.com/) 向我们寻求帮助，或[联系支持人员](https://azure.microsoft.com/support/community/?product=search)。

> [!div class="nextstepaction"]
> [搜索服务 REST API 参考（预览版）](/rest/api/searchservice/index-preview)
