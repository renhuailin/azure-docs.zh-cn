---
title: Azure 认知搜索中的新增功能
description: 新增功能和增强功能的通告，包括 Azure 搜索服务已重命名为 Azure 认知搜索。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: overview
ms.date: 07/20/2021
ms.custom: references_regions
ms.openlocfilehash: b3c84a46e19e4cf24459424458340233869ea683
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121736293"
---
# <a name="whats-new-in-azure-cognitive-search"></a>Azure 认知搜索中的新增功能

了解服务中的新增功能。 请将本页加入书签，以随时了解该服务的最新信息。 访问[预览功能列表](search-api-preview.md)，查看尚未正式发布的功能的完整列表。

## <a name="july-2021"></a>2021 年 7 月

|功能&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  |  说明 | 可用性  |
|------------------------------------|--------------|---------------|
| [搜索 REST API 2021-04-30-预览版](/rest/api/searchservice/index-preview) | 添加了对使用[托管标识](search-howto-managed-identities-data-sources.md)建立的索引器连接以及 Azure Active Directory (Azure AD) 身份验证的 REST API 支持。 | 公共预览版 |
| [基于角色的授权（预览版）](search-security-rbac.md) | 使用 Azure Active Directory 和新的内置角色，对索引和索引编制的数据平面访问权限进行身份验证，消除或减少对 API 密钥的依赖。 | 公开预览版（[根据要求提供](https://aka.ms/azure-cognitive-search/rbac-preview)）。 订阅加入后，请使用 Azure 门户或管理 REST API 版本 2021-04-01-预览版为数据平面身份验证配置搜索服务。|
| [管理 REST API 2021-04-01-预览版](/rest/api/searchmanagement/) | 修改[创建或更新服务](/rest/api/searchmanagement/2021-04-01-preview/services/create-or-update)以支持新的 [DataPlaneAuthOptions](/rest/api/searchmanagement/2021-04-01-preview/services/create-or-update#dataplaneauthoptions)。 | 公共预览版 |

## <a name="may-2021"></a>2021 年 5 月

|功能&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  |  说明 | 可用性  |
|------------------------------------|--------------|---------------|
| [Power Query 连接器支持（预览）](search-how-to-index-power-query-data-sources.md) | 索引器现可从其他云平台建立索引。 若要使用索引器抓取外部数据源来建立索引，现可使用 Power Query 连接器连接到 Amazon Redshift、Elasticsearch、PostgreSQL、Salesforce 对象、Salesforce 报告、Smartsheet 和 Snowflake。 </br></br>[公告（techcommunity 博客）](https://techcommunity.microsoft.com/t5/azure-ai/azure-cognitive-search-indexers-allow-you-to-ingest-data-from/ba-p/2381988)  | 公共预览版（[通过请求获取](https://aka.ms/azure-cognitive-search/indexer-preview)），使用 REST api-version=2020-06-30-Preview 和 Azure 门户。 |
|[Azure Data Lake Storage Gen2](search-howto-index-azure-data-lake-storage.md) | 索引器使用的 ADLS Gen2 数据源现已正式发布。 | 正式发布，使用 REST api-version=2020-06-30 和 Azure 门户。 |
|[MySQL 支持（预览）](search-howto-index-mysql.md) | 对于基于索引器的索引，宣布推出 Azure MySQL 的数据源支持预览版。 | 公共预览版，REST api-version=2020-06-30-Preview、[.NET SDK 11.2.1](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourcetype.mysql) 和 Azure 门户。 |
| [面向拼写检查和语义结果的更多 queryLanguage](/rest/api/searchservice/preview-api/search-documents#queryLanguage) | 对于调用拼写检查或 queryType=semantic 的查询请求，现可将 queryLanguage 设置为 [38 种](/rest/api/searchservice/preview-api/search-documents#queryLanguage)非英语语言。 </br></br>[公告（techcommunity 博客）](https://techcommunity.microsoft.com/t5/azure-ai/introducing-multilingual-support-for-semantic-search-on-azure/ba-p/2385110) | 公开预览版（[根据要求提供](https://aka.ms/SemanticSearchPreviewSignup)）。 </br></br>使用[搜索文档 (REST)](/rest/api/searchservice/preview-api/search-documents) api-version=2020-06-30-Preview、[Azure.Search.Documents 11.3.0-beta.2](https://www.nuget.org/packages/Azure.Search.Documents/11.3.0-beta.2) 或 Azure 门户中的[搜索资源管理器](search-explorer.md)。 </br></br>需遵守[区域和层级](semantic-search-overview.md#availability-and-pricing)限制。 |
| [双重加密可用性](search-security-manage-encryption-keys.md#double-encryption) | 对于通过客户托管的密钥进行加密的搜索索引和对象，当前在所有受支持的区域中都实现了双重加密（静态和临时磁盘的加密）。 | 在所有区域中，受[服务创建日期](search-security-manage-encryption-keys.md#double-encryption)的限制。 |

## <a name="april-2021"></a>2021 年 4 月

|功能&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  |  说明 | 可用性  |
|------------------------------|---------------|---------------|
| [Gremlin API 支持（预览）](search-howto-index-cosmosdb-gremlin.md) | 对于基于索引器的索引，现可创建一个数据源，用于从通过 Gremlin API 访问的 Cosmos DB 中检索内容。 | 公共预览版（[通过请求获取](https://aka.ms/azure-cognitive-search/indexer-preview)），使用 api-version=2020-06-30-Preview。 |

## <a name="march-2021"></a>2021 年 3 月

|功能&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  |  说明 | 可用性  |
|------------------------------|---------------|---------------|
| [语义搜索（预览）](semantic-search-overview.md) | 一组与查询相关的功能，这些功能通过对查询请求进行最小调整，显著提高了搜索结果的相关性。 </br></br>[语义排名](semantic-ranking.md)使用单词和内容背后的语义含义来计算相关性分数。 </br></br>[语义标题](semantic-how-to-query-request.md)返回文档中最能概括文档的相关段落，其中突出显示了最重要的术语或短语。 </br></br>[语义答案](semantic-answers.md)返回从搜索文档中提取的关键段落，这些段落被表述为对看似问题的查询的直接回答。 | 公开预览版（[根据要求提供](https://aka.ms/SemanticSearchPreviewSignup)）。 </br></br>在 Azure 门户中使用[搜索文档 (REST)](/rest/api/searchservice/preview-api/search-documents) api-version=2020-06-30-Preview 或[搜索资源管理器](search-explorer.md)。 </br></br>需遵守区域和层级限制。 |
| [检查查询词的拼写（预览）](speller-how-to-add.md) | 在查询词到达搜索引擎之前，你可检查其是否存在拼写错误。 `speller` 选项适用于任何查询类型（简单、完整或语义查询）。 |  公共预览版，仅限 REST，api-version=2020-06-30-Preview|
| [SharePoint Online 索引器（预览）](search-howto-index-sharepoint-online.md) | 此索引器可将你连接到 SharePoint Online 网站，这样你即可索引文档库中的内容。 | 公共预览版，仅限 REST，api-version=2020-06-30-Preview |
| [Normalizers（预览）](search-normalizers.md) | Normalizers 提供简单的文本预处理：一致的大小写、删除重音和 ASCII 折叠，无需调用全文分析链。| 公共预览版，仅限 REST，api-version=2020-06-30-Preview |
| [自定义实体查找技能](cognitive-search-skill-custom-entity-lookup.md ) |  可在用户自定义的单词和短语列表中查找文本的一项认知技能。 它使用此列表为包含任何匹配实体的所有文档加上标签。 该技能还支持一定程度的模糊匹配，应用此匹配方法可以查找类似但不完全相同的匹配项。 | 正式发布。 |

## <a name="february-2021"></a>2021 年 2 月

|功能&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  |  说明 | 可用性  |
|------------------------------|---------------|---------------|
| [重置文档（预览）](search-howto-run-reset-indexers.md) |  重新处理了索引器工作负载中单独选择的搜索文档。 | [搜索 REST API 2020-06-30-Preview](/rest/api/searchservice/index-preview) |
| [可用性区域](search-performance-optimization.md#availability-zones)| 在某些区域（如[性能缩放](search-performance-optimization.md#availability-zones)中所列）具有两个或更多副本的搜索服务凭借在两个或更多不同的物理位置具有副本来实现复原。  | 搜索服务创建的区域和日期确定了它是否可用。 有关详细信息，请参阅性能缩放一文。 |
| [Azure CLI](/cli/azure/search) </br>[Azure PowerShell](/powershell/module/az.search/) | 新的修订版现在提供管理 REST API 2020-08-01 中的全部操作，包括对 IP 防火墙规则和专用终结点的支持。 | 正式发布。 |

## <a name="january-2021"></a>2021 年 1 月

|功能&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  |  说明 | 可用性  |
|------------------------------|-------------|---------------|
| [适用于 Azure 认知搜索和 QnA Maker 的解决方案加速器](https://github.com/Azure-Samples/search-qna-maker-accelerator) | 从文档中拉取问题和答案，并建议最相关的答案。 可在 [https://aka.ms/qnaWithAzureSearchDemo](https://aka.ms/qnaWithAzureSearchDemo) 找到实时演示应用。  | 开源项目（无 SLA） |

## <a name="2020-archive"></a>2020 年存档

| 月份 | 功能 | 说明 |
|-------|---------|-------------|
| 11 月 | [客户管理的密钥加密（已扩展）](search-security-manage-encryption-keys.md) | 将客户管理的加密扩展到由搜索服务创建和管理的全范围资产。 正式发布。|
| 9 月 | [适用于 Azure 认知搜索的 Visual Studio Code 扩展](search-get-started-vs-code.md) | 添加用于创建索引、索引器、数据源和技能组的工作区、导航、IntelliSense 和模板。 此功能目前处于公开预览状态。| 
| 9 月 | [托管服务标识（索引器）](search-howto-managed-identities-data-sources.md) | 正式发布。  |
| 9 月 | [使用专用链接的出站请求](search-indexer-howto-access-private.md) | 正式发布。  |
| 9 月 | [管理 REST API (2020-08-01)](/rest/api/searchmanagement/management-api-versions) | 正式发布。 |
| 9 月 | [管理 REST API (2020-08-01-Preview)](/rest/api/searchmanagement/management-api-versions) | 添加了适用于 Azure Functions 和 Azure SQL for MySQL 数据库的共享专用链接资源。 |
| 9 月 | [管理 .NET SDK 4.0](/dotnet/api/overview/azure/search/management) |  适用于管理 SDK 的 Azure SDK 更新，面向 REST API 版本 2020-08-01。 正式发布。|
| 8 月 | [双重加密](search-security-overview.md#encryption) | 已对以下区域中 2020 年 8 月 1 日之后创建的所有搜索服务公开发布：美国西部 2、美国东部、美国中南部、US Gov 弗吉尼亚州、US Gov 亚利桑那州。 |
| 7 月 | [Azure.Search.Documents 客户端库](/dotnet/api/overview/azure/search.documents-readme) | Azure SDK for .NET，已正式发布。 |
| 7 月 | [azure.search.documents 客户端库](/python/api/overview/azure/search-documents-readme)  | Azure SDK for Python，已正式发布。 |
| 7 月 | [@azure/search-documents 客户端库](/javascript/api/overview/azure/search-documents-readme)  | Azure SDK for JavaScript，已正式发布。 |
| June | [知识存储](knowledge-store-concept-intro.md) | 正式发布。 |
| June | [搜索 REST API 2020-06-30](/rest/api/searchservice/) | 正式发布。 |
| June | [搜索 REST API 2020-06-30-Preview](/rest/api/searchservice/) | 添加重置技能组和增量扩充，前者便于选择性地重新处理技能。 |
| June | [Okapi BM25 相关性算法](index-ranking-similarity.md) | 正式发布。 |
| June |  **executionEnvironment**（应用于使用 Azure 专用链接的搜索服务。） | 正式发布。 |
| June | [AML 技能（预览）](cognitive-search-aml-skill.md) | 一项使用自定义 Azure 机器学习 (AML) 模型扩展 AI 扩充的认知技能。 |
| 五月 | [调试会话（预览）](cognitive-search-debug-session.md) | 门户中的技能组调试程序。  |
| 五月 | [适用于入站防火墙支持的 IP 规则](service-configure-firewall.md) | 正式发布。  |
| 五月 | [专用搜索终结点的 Azure 专用链接](service-create-private-endpoint.md) | 正式发布。  |
| 五月 | [托管服务标识（索引器）-（预览）](search-howto-managed-identities-data-sources.md) | 使用托管标识连接到 Azure 数据源。  |
| 五月 | [sessionId 查询参数](index-similarity-and-scoring.md)，[scoringStatistics=global parameter](index-similarity-and-scoring.md#scoring-statistics)  | 全球搜索统计信息，适用于[针对搜索相关性的机器学习 (LearnToRank) 模型](https://github.com/Azure-Samples/search-ranking-tutorial)。  |
| 五月 | [featuresMode 相关性分数扩展（预览版）](index-similarity-and-scoring.md#featuresMode-param)  |   |
|3 月  | [本机 blob 软删除（预览版）](search-howto-index-changed-deleted-blobs.md) | 如果在 Blob 存储中软删除了源 Blob，则删除搜索文档。 |
|3 月  | [管理 REST API (2020-03-13)](/rest/api/searchmanagement/management-api-versions) | 正式发布。 |
|February | [PII 检测技能（预览）](cognitive-search-skill-pii-detection.md)  | 一种用于提取和掩盖个人信息的认知技能。 |
|February | [自定义实体查找技能（预览）](cognitive-search-skill-custom-entity-lookup.md) | 一种认知技能，它会从列表中查找字词和短语并标记具有匹配实体的所有文档。  |
|1 月 | [客户管理的密钥加密](search-security-manage-encryption-keys.md) | 正式发布  |
|1 月 | [适用于入站防火墙支持的 IP 规则（预览版）](service-configure-firewall.md) | [CreateOrUpdate API](/rest/api/searchmanagement/2020-08-01/services/create-or-update) 中新增了 IpRule 和 NetworkRuleSet 属性 。  |
|1 月 | [创建专用终结点（预览）](service-create-private-endpoint.md) | 设置专用链接用于安全连接到搜索服务。 此预览功能在解决方案中附带依赖项 - [Azure 专用链接](../private-link/private-link-overview.md)和 [Azure 虚拟网络](../virtual-network/virtual-networks-overview.md)。 |

## <a name="2019-archive"></a>2019 年存档

| 月份 | 功能 | 说明 |
|-------|---------|-------------|
|12 月 | [创建演示应用](search-create-app-portal.md) | 一个向导，它会生成可下载且可对索引进行查询（只读）访问的 HTML 文件，旨在用作一项验证和测试工具，而不是完整客户端应用的快捷方式。|
|11 月 | [增量扩充（预览）](cognitive-search-incremental-indexing-conceptual.md) | 缓存技能组处理，供将来重复使用。  |
|11 月 | [文档提取技能（预览）](cognitive-search-skill-document-extraction.md) | 技能组中一种从文件提取内容的认知技能。|
|11 月 | [文本翻译技能](cognitive-search-skill-text-translation.md) | 一种在索引期间用于评估和翻译文本的认知技能。 正式发布。|
|11 月 | [Power BI 模板](https://github.com/Azure-Samples/cognitive-search-templates/blob/master/README.md) | 用于在知识存储中直观显示内容的模板 |
|11 月 | [Azure Data Lake Storage Gen2（预览版）](search-howto-index-azure-data-lake-storage.md)和 [Cosmos DB Gremlin API（预览版）](search-howto-index-cosmosdb.md) | 以公共预览版形式提供新的索引器数据源。 |
|7 月 | [Azure 政府云支持](https://azure.microsoft.com/global-infrastructure/services/?regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-texas,usgov-virginia&products=search) | 正式发布。|

<a name="new-service-name"></a>

## <a name="new-service-name"></a>新服务名称

Azure 搜索已于 2019 年 10 月更名为“Azure 认知搜索”，以反映认知技能和 AI 处理在核心操作中更广泛的用途（但仍为可选）。 API 版本、NuGet 包、命名空间和终结点未有变化。 新的和现有的搜索解决方案不受服务名称更改的影响。

## <a name="service-updates"></a>服务更新

在 Azure 网站上可以找到 Azure 认知搜索的[服务更新通告](https://azure.microsoft.com/updates/?product=search&status=all)。