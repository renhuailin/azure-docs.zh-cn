---
title: Python 示例
titleSuffix: Azure Cognitive Search
description: 查找 Azure 认知搜索 Python 代码示例演示，这些示例使用适用于 Python 或 REST 的 Azure .NET SDK。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/11/2021
ms.openlocfilehash: 17ce9f711572d1d760e44676ad8e8b51c5333985
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129218402"
---
# <a name="python-code-samples-for-azure-cognitive-search"></a>Azure 认知搜索的 Python 代码示例

了解演示 Azure 认知搜索的功能和工作流的 Python 代码示例。 这些示例使用 [Azure 认知搜索客户端库](/python/api/overview/azure/search-documents-readme)进行[用于 Python 的 Azure SDK](/azure/developer/python/)，你可以通过以下链接浏览它们。

| 目标 | 链接 |
|--------|------|
| 包下载 | [pypi.org/project/azure-search-documents/](https://pypi.org/project/azure-search-documents/) |
| API 参考 | [azure-search-documents](/python/api/azure-search-documents)  |
| API 测试用例 | [github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents/tests](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents/tests) |
| 源代码 | [github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents)  |

## <a name="sdk-samples"></a>SDK 示例

Azure SDK 开发团队中的代码示例演示 API 用法。 可在 GitHub 的 [azure-sdk-for-python/tree/master/sdk/search/azure-search-documents/samples](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents/samples) 中找到这些示例。

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
| [Facet 查询](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_facet_query.py) | 演示如何使用 [facet](search-faceted-navigation.md)。 |

## <a name="doc-samples"></a>文档示例

认知搜索团队中的代码示例演示功能和工作流。 其中许多示例引自教程、快速入门和操作指南文章。 可在 GitHub 的 [Azure-Samples/azure-search-python-samples](https://github.com/Azure-Samples/azure-search-python-samples) 中找到这些示例。

| 示例 | 项目 |
|---------|---------|
| [快速入门](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/Quickstart) | [快速入门：使用 Python 创建搜索索引](search-get-started-python.md)的源代码。 本文介绍了使用示例数据来创建、加载和查询搜索索引的基本工作流。 |
| [search-website](https://github.com/azure-samples/azure-search-python-samples/tree/master/search-website) | [《教程：向 Web 应用添加搜索》](tutorial-python-overview.md)的源代码。 演示一个端到端搜索应用，其中包含一个富客户端以及用于托管应用和处理搜索请求的组件。|
| [tutorial-ai-enrichment](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/Tutorial-AI-Enrichment)  | [教程：使用 Python 和 AI 从 Azure Blob 生成可搜索的内容](cognitive-search-tutorial-blob-python.md)的源代码。 本文介绍了如何使用认知技能组创建 blob 索引器，其中技能组可创建并转换原始内容，使其方便搜索或使用。 |
| [AzureML-Custom-Skill](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/AzureML-Custom-Skill)  | [示例：使用 Python 创建自定义技能](cognitive-search-custom-skill-python.md)的源代码。 本文演示了索引器和技能组与 Azure 机器学习中的深度学习模型的集成。 |

> [!Tip]
> 请尝试使用[示例浏览器](/samples/browse/?languages=python&products=azure-cognitive-search)来搜索 GitHub 中的 Microsoft 代码示例，搜索时可按产品、服务和语言进行筛选。
