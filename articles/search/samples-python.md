---
title: Python 示例
titleSuffix: Azure Cognitive Search
description: 查找 Azure 认知搜索 Python 代码示例演示，这些示例使用适用于 Python 或 REST 的 Azure .NET SDK。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/27/2021
ms.openlocfilehash: 0d09851cf8e68cead4a67615aaa792512482f351
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2021
ms.locfileid: "98955116"
---
# <a name="python-code-samples-for-azure-cognitive-search"></a>Azure 认知搜索的 Python 代码示例

了解演示 Azure 认知搜索解决方案的功能和工作流的 Python 代码示例。 这些示例使用 azure [**SDK For Python**](/azure/developer/python/)的 [**azure 认知搜索客户端库**](/python/api/overview/azure/search-documents-readme)，你可以浏览以下链接。

| 目标 | 链接 |
|--------|------|
| 包下载 | [pypi.org/project/azure-search-documents/](https://pypi.org/project/azure-search-documents/) |
| API 参考 | [azure-搜索-文档](/python/api/azure-search-documents)  |
| API 测试用例 | [github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents/tests](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents/tests) |
| 源代码 | [github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents)  |

## <a name="sdk-samples"></a>SDK 示例

Azure SDK 开发团队中的代码示例演示了 API 使用。 可在 GitHub 上 [**的适用于 python/tree/master/sdk/搜索/azure 搜索文档/示例**](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents/samples) 中找到这些示例。

| 示例 | 说明 |
|---------|-------------|
| [身份验证](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_authentication.py) | 演示如何配置客户端以及向服务进行身份验证。 | 
| [索引创建-读取-更新-删除操作](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_index_crud_operations.py) | 演示如何创建、更新、获取、列出和删除[搜索索引](search-what-is-an-index.md)。 |
| [索引器创建-读取-更新-删除操作](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_indexers_operations.py) | 演示如何创建、更新、获取、列出、重置和删除[索引器](search-indexer-overview.md)。 |
| [搜索索引器数据源](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_indexer_datasource_skillset.py) | 演示如何创建、更新、获取、列出和删除索引器数据源，对于基于索引器为[支持的 Azure 数据源](search-indexer-overview.md#supported-data-sources)编制索引而言，这些数据源是必需的。 |
| [同义词](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_synonym_map_operations.py) | 演示如何创建、更新、获取、列出和删除[同义词映射](search-synonyms.md)。  |
| [加载文档](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_crud_operations.py) | 演示如何在[数据导入](search-what-is-data-import.md)操作中将文档上传或合并到索引中。 |
| [简单查询](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_simple_query.py) | 演示如何设置[基本查询](search-query-overview.md)。 |
| [筛选器查询](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_filter_query.py) | 演示如何设置[筛选器表达式](search-filters.md)。 |
| [Facet 查询](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_facet_query.py) | 演示如何使用 [facet](search-filters-facets.md)。 |

## <a name="doc-samples"></a>文档示例

认知搜索团队中的代码示例演示了功能和工作流。 其中的许多示例在教程、快速入门和操作指南文章中进行了引用。 可在 GitHub 上的 [**Azure 示例/azure-搜索-python 示例**](https://github.com/Azure-Samples/azure-search-python-samples) 中找到这些示例。

| 示例 | 文章 |
|---------|---------|
| [快速入门](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/Quickstart) | [快速入门：使用 Python 创建搜索索引](search-get-started-python.md)的源代码。 本文介绍了使用示例数据创建、加载和查询搜索索引的基本工作流。 |
| [tutorial-ai-enrichment](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/Tutorial-AI-Enrichment)  | [教程：使用 Python 和 AI 从 Azure Blob 生成可搜索的内容](cognitive-search-tutorial-blob-python.md)的源代码。 本文介绍如何创建包含认知技能组合的 blob 索引器，其中技能组合创建并转换原始内容，使其可搜索或使用。 |
| [AzureML-Custom-Skill](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/AzureML-Custom-Skill)  | [示例：使用 Python 创建自定义技能](cognitive-search-custom-skill-python.md)的源代码。 本文介绍如何在 Azure 机器学习中与深度学习模型进行索引器和技能组合集成。 |

> [!Tip]
> 请尝试使用[示例浏览器](/samples/browse/?languages=python&products=azure-cognitive-search)来搜索 Github 中的 Microsoft 代码示例，搜索时可按产品、服务和语言进行筛选。