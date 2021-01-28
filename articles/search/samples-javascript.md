---
title: JavaScript 示例
titleSuffix: Azure Cognitive Search
description: 查找 Azure 认知搜索 JavaScript 代码示例演示，这些示例使用适用于 JavaScript 的 Azure .NET SDK。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/27/2021
ms.openlocfilehash: f83767813ea3923d85db2ca3f0164776c610525e
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2021
ms.locfileid: "98955014"
---
# <a name="javascript-code-samples-for-azure-cognitive-search"></a>Azure 认知搜索的 JavaScript 代码示例

了解演示 Azure 认知搜索解决方案的功能和工作流的 JavaScript 代码示例。 这些示例使用 azure [**SDK For JavaScript**](/azure/developer/javascript/)的 [**azure 认知搜索客户端库**](/javascript/api/overview/azure/search-documents-readme)，你可以浏览以下链接。

| 目标 | 链接 |
|--------|------|
| 包下载 | [www.npmjs.com/package/@azure/search-documents](https://www.npmjs.com/package/@azure/search-documents) |
| API 参考 | [@azure/search-documents](/javascript/api/@azure/search-documents/)  |
| API 测试用例 | [github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/test](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/test) |
| 源代码 | [github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents)  |

## <a name="sdk-samples"></a>SDK 示例

Azure SDK 开发团队中的代码示例演示了 API 使用。 你可以在 GitHub 上 [**的 azure-sdk/tree/master/sdk/搜索/搜索文档/示例**](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples) 中找到这些示例。

### <a name="javascript-sdk-samples"></a>JavaScript SDK 示例

| 示例 | 说明 |
|---------|-------------|
| [索引](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/indexes) | 演示如何创建、更新、获取、列出和删除[搜索索引](search-what-is-an-index.md)。 此样本类别还包括一个服务统计信息示例。 |
| [dataSourceConnections（用于索引器）](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/dataSourceConnections) | 演示如何创建、更新、获取、列出和删除索引器数据源，对于基于索引器为[支持的 Azure 数据源](search-indexer-overview.md#supported-data-sources)编制索引而言，这些数据源是必需的。 |
| [索引器](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/indexers) |  演示如何创建、更新、获取、列出、重置和删除[索引器](search-indexer-overview.md)。|
| [skillSet](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/skillSets) |   演示如何创建、更新、获取、列出和删除[技能组](cognitive-search-working-with-skillsets.md)，这些技能组是附加的索引器，并且在编制索引期间执行基于 AI 的扩充。 |
| [synonymMaps](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/synonymMaps) | 演示如何创建、更新、获取、列出和删除[同义词映射](search-synonyms.md)。  |
| [查询](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/search/search-documents/samples/javascript/src/readonlyQuery.js) | 演示如何针对 Microsoft 托管的只读公共索引执行查询。  |

### <a name="typescript-samples"></a>TypeScript 示例

| 示例 | 说明 |
|---------|-------------|
| [索引](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/indexes) | 演示如何创建、更新、获取、列出和删除[搜索索引](search-what-is-an-index.md)。 此样本类别还包括一个服务统计信息示例。 |
| [dataSourceConnections（用于索引器）](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/dataSourceConnections) | 演示如何创建、更新、获取、列出和删除索引器数据源，对于基于索引器为[支持的 Azure 数据源](search-indexer-overview.md#supported-data-sources)编制索引而言，这些数据源是必需的。 |
| [索引器](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/indexers) |  演示如何创建、更新、获取、列出、重置和删除[索引器](search-indexer-overview.md)。|
| [skillSet](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/skillSets) |   演示如何创建、更新、获取、列出和删除[技能组](cognitive-search-working-with-skillsets.md)，这些技能组是附加的索引器，并且在编制索引期间执行基于 AI 的扩充。 |
| [synonymMaps](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/synonymMaps) | 演示如何创建、更新、获取、列出和删除[同义词映射](search-synonyms.md)。  |
| [查询](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/search/search-documents/samples/typescript/src/readonlyQuery.ts) | 演示如何针对 Microsoft 托管的只读公共索引执行查询。  |

## <a name="doc-samples"></a>文档示例

认知搜索团队中的代码示例演示了功能和工作流。 其中的许多示例在教程、快速入门和操作指南文章中进行了引用。 你可以在 GitHub 上的 [**Azure 示例/azure--**](https://github.com/Azure-Samples/azure-search-javascript-samples) ------javascript-示例中找到这些示例。

| 示例 | 文章 |
|---------|---------|
| [quickstart](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/quickstart/v11) | [快速入门：在 JavaScript 中创建搜索索引](search-get-started-javascript.md)的源代码。 本文介绍了使用示例数据创建、加载和查询搜索索引的基本工作流。 |

> [!Tip]
> 请尝试使用[示例浏览器](/samples/browse/?languages=javascript&products=azure-cognitive-search)来搜索 Github 中的 Microsoft 代码示例，搜索时可按产品、服务和语言进行筛选。

## <a name="other-samples"></a>其他示例

以下示例也由认知搜索团队发布，但在文档中未被引用。 相关的自述文件提供了使用说明。

| 示例 | 说明 |
|---------|-------------|
| [azure-search-react-template](https://github.com/dereklegenzoff/azure-search-react-template) | Azure 认知搜索的 React 模板 (github.com) |