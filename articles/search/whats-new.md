---
title: Azure 认知搜索中的新增功能
description: 新增功能和增强功能的通告，包括 Azure 搜索服务已重命名为 Azure 认知搜索。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: overview
ms.date: 02/09/2021
ms.custom: references_regions
ms.openlocfilehash: 13cb22c178be29af71b57d0f50fdbd0e95718069
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/10/2021
ms.locfileid: "100104597"
---
# <a name="whats-new-in-azure-cognitive-search"></a>Azure 认知搜索中的新增功能

了解服务中的新增功能。 请将本页加入书签，以随时了解该服务的最新信息。 查看[预览功能列表](search-api-preview.md)，以查看公共预览版中的功能。

## <a name="february-2021"></a>2021 年 2 月

|功能&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  |  说明 | 可用性  |
|------------------------------|---------------|---------------|
| [重置文档（预览）](search-howto-run-reset-indexers.md) |  重新处理了索引器工作负载中单独选择的搜索文档。 | [搜索 REST API 2020-06-30-Preview](/rest/api/searchservice/index-preview) |
| [可用性区域](search-performance-optimization.md#availability-zones)| 在某些区域具有两个或更多副本的搜索服务（如[本文](search-performance-optimization.md#availability-zones)所述）凭借在两个或更多不同的物理位置具有副本来实现复原。  | 搜索服务创建的区域和日期确定了它是否可用。 有关详细信息，请查看性能优化文档。 |

## <a name="january-2021"></a>2021 年 1 月

|功能&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  |  说明 | 可用性  |
|------------------------------|-------------|---------------|
| [适用于 Azure 认知搜索和 QnA Maker 的解决方案加速器](https://github.com/Azure-Samples/search-qna-maker-accelerator) | 从文档中拉取问题和答案，并建议最相关的答案。 可在 [https://aka.ms/qnaWithAzureSearchDemo](https://aka.ms/qnaWithAzureSearchDemo) 找到实时演示应用。  | 开源项目（无 SLA） |

## <a name="2020-archive"></a>2020 年存档

| 月份 | 功能 | 说明 |
|-------|---------|-------------|
| 11 月 | [客户管理的密钥加密（已扩展）](search-security-manage-encryption-keys.md) | 将客户管理的加密扩展到由搜索服务创建和管理的全范围资产。 正式发布。|
| 9 月 | [适用于 Azure 认知搜索的 Visual Studio Code 扩展](search-get-started-vs-code.md) | 添加用于创建索引、索引器、数据源和技能组的工作区、导航、IntelliSense 和模板。 | 公共预览版 |
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
|1 月 | [适用于入站防火墙支持的 IP 规则（预览版）](service-configure-firewall.md) | [CreateOrUpdate API](/rest/api/searchmanagement/2019-10-01-preview/createorupdate-service) 中新增了 IpRule 和 NetworkRuleSet 属性 。  |
|1 月 | [创建专用终结点（预览）](service-create-private-endpoint.md) | 设置专用链接用于安全连接到搜索服务。 此预览功能在解决方案中附带依赖项 - [Azure 专用链接](../private-link/private-link-overview.md)和 [Azure 虚拟网络](../virtual-network/virtual-networks-overview.md)。 |

## <a name="2019-archive"></a>2019 年存档

| 月份 | 功能 | 说明 |
|-------|---------|-------------|
|12 月 | [创建演示应用（预览）](search-create-app-portal.md) | 一个向导，它会生成可下载且可对索引进行查询（只读）访问的 HTML 文件，旨在用作一项验证和测试工具，而不是完整客户端应用的快捷方式。|
|11 月 | [增量扩充（预览）](cognitive-search-incremental-indexing-conceptual.md) | 缓存技能组处理，供将来重复使用。  |
|11 月 | [文档提取技能（预览）](cognitive-search-skill-document-extraction.md) | 技能组中一种从文件提取内容的认知技能。|
|11 月 | [文本翻译技能](cognitive-search-skill-text-translation.md) | 一种在索引期间用于评估和翻译文本的认知技能。 正式发布。|
|11 月 | [Power BI 模板](https://github.com/Azure-Samples/cognitive-search-templates/blob/master/README.md) | 用于在知识存储中直观显示内容的模板 |
|11 月 | [Azure Data Lake Storage Gen2（预览版）](search-howto-index-azure-data-lake-storage.md)、[Cosmos DB Gremlin API（预览版）](search-howto-index-cosmosdb.md)和 [Cosmos DB Cassandra API（预览版）](search-howto-index-cosmosdb.md) | 以公共预览版形式提供新的索引器数据源。 |
|7 月 | [Azure 政府云支持](../azure-government/compare-azure-government-global-azure.md#azure-cognitive-search) | 正式发布。|

<a name="new-service-name"></a>

## <a name="new-service-name"></a>新服务名称

Azure 搜索已于 2019 年 10 月更名为“Azure 认知搜索”，以反映认知技能和 AI 处理在核心操作中更广泛的用途（但仍为可选）。 API 版本、NuGet 包、命名空间和终结点未有变化。 新的和现有的搜索解决方案不受服务名称更改的影响。

## <a name="service-updates"></a>服务更新

在 Azure 网站上可以找到 Azure 认知搜索的[服务更新通告](https://azure.microsoft.com/updates/?product=search&status=all)。