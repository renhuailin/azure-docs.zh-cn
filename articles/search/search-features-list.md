---
title: 功能概述
titleSuffix: Azure Cognitive Search
description: 了解 Azure 认知搜索的功能类别。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/15/2020
ms.openlocfilehash: fca6e59d3aa5b95db0b9ad28f652226ae2eb30ba
ms.sourcegitcommit: 832e92d3b81435c0aeb3d4edbe8f2c1f0aa8a46d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/07/2021
ms.locfileid: "111556522"
---
# <a name="features-of-azure-cognitive-search"></a>Azure 认知搜索的功能

Azure 认知搜索提供了全文搜索引擎、持久的搜索索引存储、在编制索引期间用来提取更多文本和结构的集成 AI，以及 API 和工具。 下表按类别汇总了功能。 若要详细了解认知搜索与其他搜索技术的对比情况，请参阅[什么是 Azure 认知搜索？](search-what-is-azure-search.md)。

## <a name="indexing-features"></a>索引编制功能

| 类别&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | 功能 |
|-------------------|----------|
| 数据源 | 搜索索引可以接受来自任何源的文本，只要该文本是以 JSON 文档格式提交的。 <br/><br/> [**索引器**](search-indexer-overview.md)自动从受支持的 Azure 数据源中引入数据，并处理 JSON 序列化。 连接到 [Azure SQL 数据库](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)、[Azure Cosmos DB](search-howto-index-cosmosdb.md) 或 [Azure Blob 存储](search-howto-indexing-azure-blob-storage.md)，以提取主要数据存储中的可搜索内容。 Azure Blob 索引器可以执行“文档破解”[从主要文件格式提取文本](search-howto-indexing-azure-blob-storage.md)，包括 Microsoft Office、PDF 和 HTML 文档。 |
| 分层的嵌套数据结构 | 使用 [**复杂类型**](search-howto-complex-data-types.md)和集合，几乎可以在搜索索引中对任何类型的 JSON 结构建模。 可以通过集合、复杂类型和复杂类型集合，以本机方式表示一对多和多对多基数。|
| 语言分析 | 分析器是在编制索引和搜索操作期间用于处理文本的组件。 默认情况下，你可以使用常规用途标准 Lucene 分析器，也可以将其替代为某个语言分析器、你配置的自定义分析器或以所需格式生成令牌的其他预定义分析器。 <br/><br/>Lucene 或 Microsoft 的[语言分析器](index-add-language-analyzers.md)用于智能处理特定于语言的语言学，包括谓词时态、词性、不规则复数名词（例如“mouse”与“mice”）、词取消复合、词拆分（对于不带空格的语言）等。 <br/><br/>[**自定义词汇分析器**](index-add-custom-analyzers.md)用于复杂查询形式，例如拼音匹配和正则表达式。<br/><br/> |

## <a name="ai-enrichment-and-knowledge-mining"></a>AI 扩充和知识挖掘

| 类别&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | 功能 |
|-------------------|----------|
|在编制索引期间进行 AI 处理 | 适用于图像和文本分析的 [**AI 扩充**](cognitive-search-concept-intro.md)可以应用于索引管道，以从原始内容中提取文本信息。 [内置技术](cognitive-search-predefined-skills.md)的一些示例包括：光学字符识别（使扫描的 JPEG 变得可搜索）、实体识别（标识组织、名称或位置）、关键短语识别。 也可[将自定义技术编码](cognitive-search-create-custom-skill-example.md)，以便将其附加到管道。 也可[集成 Azure 机器学习创作技能](./cognitive-search-tutorial-aml-custom-skill.md)。 |
| 存储丰富的内容以供在非搜索场景中分析和使用 | [**知识存储**](knowledge-store-concept-intro.md)是索引编制管道的替代输出。 可将索引编制管道创建的扩充文档发送到知识存储（位于 Azure Blob 存储或表存储中，依配置而定），而不是将已标记的词语发送到索引。 知识存储是通过基于 AI 的索引编制（技能组）创建的。 知识存储的用途是支持下游分析或处理。 利用知识存储中的新信息和结构，你可以将其附加到机器学习流程，或从 Power BI 进行连接来浏览数据。<br/><br/> |
| 缓存内容 | [**增量扩充（预览版）**](cognitive-search-incremental-indexing-conceptual.md)将处理限制为仅处理通过对管道进行特定编辑而更改的文档，对未更改的管道部分使用缓存内容。 |

## <a name="query-and-user-experience"></a>查询和用户体验

| 类别&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | 功能 |
|-------------------|----------|
|自由格式文本搜索 | [全文搜索](search-lucene-query-architecture.md)是大多数基于搜索的应用的主要用例。 查询可以使用支持的语法进行陈述。 <br/><br/>[简单查询语法](query-simple-syntax.md)提供逻辑运算符、短语搜索运算符、后缀运算符和优先运算符。<br/><br/>[**完整的 Lucene 查询语法**](query-lucene-syntax.md)包括简单语法中的所有操作，以及模糊搜索、邻近搜索、术语提升和正则表达式的扩展。|
| 相关性 | [**简单计分**](index-add-scoring-profiles.md)是 Azure 认知搜索的主要优势。 计分配置文件用于在文档中自行将相关性建模为值的函数。 例如，你可能希望较新产品或打折产品显示在搜索结果的顶部位置。 也可以基于已跟踪和单独存储的客户搜索首选项将标记用于个性化计分，来生成计分配置文件。 |
| 地理搜索 | Azure 认知搜索可以处理、筛选和显示地理位置。 它可以让用户基于搜索结果与物理位置的临近程度浏览数据。 [观看此视频](https://channel9.msdn.com/Shows/Data-Exposed/Azure-Search-and-Geospatial-Data)或[查看此示例](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs)了解详细信息。 |
| 筛选器和分面导航 | 通过单个查询参数实现 [**分面导航**](search-faceted-navigation.md)。 Azure 认知搜索返回一个分面导航结构，可以将该结构用作类别列表背后的代码，用于自定向筛选（例如，按价格范围或品牌来筛选目录项）。 <br/><br/> 可以使用 [**筛选器**](query-odata-filter-orderby-syntax.md)将分面导航纳入到应用程序的 UI 中，改进查询表述，以及基于用户或开发人员指定的条件进行筛选。 可以使用 OData 语法创建筛选器。 |
| 用户体验 | 可以为搜索栏中预先键入的查询启用[自动完成](search-add-autocomplete-suggestions.md)。 <br/><br/>[**搜索建议**](/rest/api/searchservice/suggesters)也基于搜索栏中的部分文本输入开始工作，但结果是索引中的实际文档而不是查询术语。 <br/><br/>[**同义词**](search-synonyms.md)功能无需用户提供替换术语，便可关联隐式扩展查询范围的等效术语。 <br/><br/>[命中项突出显示](/rest/api/searchservice/Search-Documents)向搜索结果中的匹配关键字应用文本格式设置。 可以选择哪些字段返回突出显示的片段。<br/><br/>[**排序**](/rest/api/searchservice/Search-Documents)通过索引架构覆盖多个字段，可以使用一个搜索参数在查询时进行切换。<br/><br/> 通过 Azure 认知搜索所提供的对搜索结果的优化控制，[**分页**](search-pagination-page-layout.md)和限制搜索结果将变得更简单。  <br/><br/>|

## <a name="security-features"></a>安全功能

| 类别&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | 功能 |
|-------------------|----------|
| 数据加密 | [Microsoft 托管的静态加密](search-security-overview.md#encryption)内置在内部存储层中，它是不可撤消的。 <br/><br/>你在 Azure Key Vault 中创建和管理的 [**客户管理的加密密钥**](search-security-manage-encryption-keys.md)可以用于索引和同义词映射的补充加密。 对于 2020 年 8 月 1 日后创建的服务，CMK 加密延伸到临时磁盘上的数据，以对索引内容进行完全双重加密。|
| Endpoint Protection | 利用[用于入站防火墙的 IP 规则支持](service-configure-firewall.md)，可以设置搜索服务将接受其中请求的 IP 范围。<br/><br/>使用 Azure 专用链接[创建专用终结点](service-create-private-endpoint.md)，以通过虚拟网络强制执行所有请求。 |
| 出站安全（索引器） | 利用[通过专用终结点的数据访问](search-indexer-howto-access-private.md)，索引器可以连接到通过 Azure 专用链接保护的 Azure 资源。<br/><br/>[使用可信标识的数据访问](search-howto-managed-identities-data-sources.md)意味着到外部数据源的连接字符串可以省略用户名和密码。 在索引器连接到数据源时，如果搜索服务以前已注册为受信任的服务，则资源会允许该连接。 |

## <a name="portal-features"></a>门户功能

| 类别&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | 功能 |
|-------------------|----------|
| 用于原型制作和检查的工具 | [**添加索引**](search-what-is-an-index.md)是门户中的一个索引设计器，可用于创建包含特性化字段和一些其他设置的基本架构。 保存索引后，可以使用 SDK 或 REST API 填充索引以提供数据。 <br/><br/>[**导入数据向导**](search-import-data-portal.md)创建索引、索引器、技能组和数据源定义。 如果你的数据存在于 Azure 中，则此向导可以节省大量时间和精力，特别是对于概念证明调查和探索。 <br/><br/>[**搜索浏览器**](search-explorer.md)用来测试查询和优化计分概要文件。<br/><br/>[**创建演示应用**](search-create-app-portal.md)用来生成可用来测试搜索体验的 HTML 页面。  |
| 监视和诊断 | [启用监视功能](search-monitor-usage.md)可查看除门户中始终可见的一目了然指标外的其他指标。 门户页面中会捕获并报告关于每秒查询数、延迟和限制的指标，无需额外进行配置。|

## <a name="programmability"></a>可编程性

| 类别&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | 功能 |
|-------------------|----------|
| REST | [**服务 REST API**](/rest/api/searchservice/) 用于数据平面操作，包括与索引编制、查询和 AI 扩充相关的所有操作。 你还可以使用此客户端库来检索系统信息和统计信息。 <br/><br/>[**管理 REST API**](/rest/api/searchmanagement/) 用于通过 Azure 资源管理器创建和清理服务。 你还可以使用此 API 来管理密钥和预配服务。|
| 用于 .NET 的 Azure SDK | [**Azure.Search.Documents**](/dotnet/api/overview/azure/search.documents-readme) 用于数据平面操作，包括与索引编制、查询和 AI 扩充相关的所有操作。 你还可以使用此客户端库来检索系统信息和统计信息。 <br/><br/>[**Microsoft.Azure.Management.Search**](/dotnet/api/microsoft.azure.management.search) 用于通过 Azure 资源管理器创建和清理服务。 你还可以使用此 API 来管理密钥和预配服务。|
| 用于 Java 的 Azure SDK | [**com.azure.search.documents**](/java/api/com.azure.search.documents) 用于数据平面操作，包括与索引编制、查询和 AI 扩充相关的所有操作。 你还可以使用此客户端库来检索系统信息和统计信息。 <br/><br/>[**com.microsoft.azure.management.search**](/java/api/overview/azure/search/management) 用于通过 Azure 资源管理器创建和清理服务。 你还可以使用此 API 来管理密钥和预配服务。|
| 用于 Python 的 Azure SDK | [**azure-search-documents**](/python/api/overview/azure/search-documents-readme) 用于数据平面操作，包括与索引编制、查询和 AI 扩充相关的所有操作。 你还可以使用此客户端库来检索系统信息和统计信息。 <br/><br/>[**azure-mgmt-search**](/python/api/overview/azure/search/management) 用于通过 Azure 资源管理器创建和清理服务。 你还可以使用此 API 来管理密钥和预配服务。 |
| 用于 JavaScript/TypeScript 的 Azure SDK | [**azure/search-documents**](/javascript/api/@azure/search-documents/) 用于数据平面操作，包括与索引编制、查询和 AI 扩充相关的所有操作。 你还可以使用此客户端库来检索系统信息和统计信息。 <br/><br/>[**azure/arm-search**](/javascript/api/@azure/arm-search/) 用于通过 Azure 资源管理器创建和清理服务。 你还可以使用此 API 来管理密钥和预配服务。 |

## <a name="see-also"></a>另请参阅

+ [认知搜索中的新增功能](whats-new.md)

+ [认知搜索中的预览功能](search-api-preview.md)