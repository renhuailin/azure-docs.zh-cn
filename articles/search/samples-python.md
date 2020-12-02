---
title: Python 示例
titleSuffix: Azure Cognitive Search
description: 查找使用 Azure .NET SDK for Python 或 REST 的 Azure 认知搜索演示 Python 代码示例。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/17/2020
ms.openlocfilehash: 244408cc512a51683ecb3c062113170b1c7f4781
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2020
ms.locfileid: "96499554"
---
# <a name="python-code-samples-for-azure-cognitive-search"></a>适用于 Azure 认知搜索的 Python 代码示例

了解演示 Azure 认知搜索的特性和功能的 Python 代码示例。 主要存储库如下所示：

| 存储库 | 说明 |
|------------|-------------|
| [azure-for python/tree/master/sdk/搜索/azure 搜索-文档/示例/](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents/samples) | 由 Azure SDK 团队生成的示例由 SDK 中 Azure.Search.Documents 客户端库附带。 你还可以查看客户端库的 [单元测试](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents/tests) ，以查看如何调用各种 api。 |
| [Azure-示例/azure-搜索-python-示例](https://github.com/Azure-Samples/azure-search-python-samples) | 操作指南文章随附的代码示例，包括 [快速入门：在 Python 中创建搜索索引](search-get-started-python.md)。|

> [!Tip]
> 尝试使用 [示例浏览器](/samples/browse/?languages=csharp&products=azure-cognitive-search) 在 Github 中搜索 Microsoft 代码示例，并按产品、服务和语言进行筛选。

## <a name="python-sdk-samples"></a>Python SDK 示例

用于 Python 的 Azure SDK 包括许多示例和入门 [页面](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents/samples) ，其中包括先决条件和包安装。 此页还包含指向以下示例的链接，这些示例将在此处列出以便您方便。

| 示例 | 说明 |
|---------|-------------|
| [身份验证](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_authentication.py) | 演示如何配置客户端并向服务进行身份验证。 | 
| [索引创建-读取-更新-删除操作](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_index_crud_operations.py) | 演示如何创建、更新、获取、列出和删除 [搜索索引](search-what-is-an-index.md)。 |
| [索引器创建-读取-更新-删除操作](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_indexers_operations.py) | 演示如何创建、更新、获取、列出、重置和删除 [索引器](search-indexer-overview.md)。 |
| [搜索索引器数据源](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_indexer_datasource_skillset.py) | 演示如何创建、更新、获取、列出和删除索引器数据源，这些数据源是 [支持的 Azure 数据源](search-indexer-overview.md#supported-data-sources)的索引编制索引所必需的。 |
| [同义词](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_synonym_map_operations.py) | 演示如何创建、更新、获取、列出和删除 [同义词映射](search-synonyms.md)。  |
| [加载文档](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_crud_operations.py) | 演示如何在 [数据导入](search-what-is-data-import.md) 操作中将文档上载或合并到索引中。 |
| [简单查询](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_simple_query.py) | 演示如何设置 [基本查询](search-query-overview.md)。 |
| [筛选器查询](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_filter_query.py) | 说明如何设置 [筛选器表达式](search-filters.md)。 |
| [Facet 查询](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_facet_query.py) | 演示如何使用 [方面](search-filters-facets.md)。 |

## <a name="documentation-samples"></a>文档示例

以下示例在 [Azure 认知搜索文档](./index.yml)中有一个相关的文章。

| 示例 | 说明 | 
|---------|-------------|
| [起步](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/Quickstart) | [快速入门的源代码：在 Python 中创建搜索索引](search-get-started-python.md)。  |
| [教程-ai-扩充](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/Tutorial-AI-Enrichment)  | 教程的源代码 [：使用 Python 和 AI 从 Azure blob 生成可搜索的内容](cognitive-search-tutorial-blob-python.md)。  |
| [AzureML-自定义技能](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/AzureML-Custom-Skill)  | 示例中的源代码 [：使用 Python 创建自定义技能](cognitive-search-custom-skill-python.md)。  |