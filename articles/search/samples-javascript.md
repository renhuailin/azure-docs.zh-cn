---
title: JavaScript 示例
titleSuffix: Azure Cognitive Search
description: 查找 Azure 认知搜索 JavaScript 代码示例演示，这些示例使用适用于 JavaScript 的 Azure .NET SDK。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/11/2021
ms.openlocfilehash: 6bc5a504f1716ff10b56fd30b8991f6d9e4009c7
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/24/2021
ms.locfileid: "122770210"
---
# <a name="javascript-code-samples-for-azure-cognitive-search"></a>Azure 认知搜索的 JavaScript 代码示例

了解演示了 Azure 认知搜索解决方案的功能和工作流的 JavaScript 代码示例。 这些示例将 [Azure 认知搜索客户端库](/javascript/api/overview/azure/search-documents-readme)用于 [Azure SDK for JavaScript](/azure/developer/javascript/)，你可以通过以下链接浏览它们。

| 目标 | 链接 |
|--------|------|
| 包下载 | [www.npmjs.com/package/@azure/search-documents](https://www.npmjs.com/package/@azure/search-documents) |
| API 参考 | [@azure/search-documents](/javascript/api/@azure/search-documents/)  |
| API 测试用例 | [github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/test](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/test) |
| 源代码 | [github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents)  |

## <a name="sdk-samples"></a>SDK 示例

Azure SDK 开发团队中的代码示例演示 API 用法。 可以在 GitHub 上的 [azure-sdk-for-js/tree/master/sdk/search/search-documents/samples](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples) 中找到这些示例。

### <a name="javascript-sdk-samples"></a>JavaScript SDK 示例

| 示例 | 说明 |
|---------|-------------|
| [索引](https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/search/search-documents/samples/v11/javascript) | 演示如何创建、更新、获取、列出和删除[搜索索引](search-what-is-an-index.md)。 此样本类别还包括一个服务统计信息示例。 |
| [dataSourceConnections（用于索引器）](https://github.com/Azure/azure-sdk-for-js/blob/main/sdk/search/search-documents/samples/v11/javascript/dataSourceConnectionOperations.js) | 演示如何创建、更新、获取、列出和删除索引器数据源，对于基于索引器为[支持的 Azure 数据源](search-indexer-overview.md#supported-data-sources)编制索引而言，这些数据源是必需的。 |
| [索引器](https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/search/search-documents/samples/v11/javascript) |  演示如何创建、更新、获取、列出、重置和删除[索引器](search-indexer-overview.md)。|
| [skillSet](https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/search/search-documents/samples/v11/javascript) |   演示如何创建、更新、获取、列出和删除[技能组](cognitive-search-working-with-skillsets.md)，这些技能组是附加的索引器，并且在编制索引期间执行基于 AI 的扩充。 |
| [synonymMaps](https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/search/search-documents/samples/v11/javascript) | 演示如何创建、更新、获取、列出和删除[同义词映射](search-synonyms.md)。  |

### <a name="typescript-samples"></a>TypeScript 示例

| 示例 | 说明 |
|---------|-------------|
| [索引](https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/search/search-documents/samples/v11/typescript/src) | 演示如何创建、更新、获取、列出和删除[搜索索引](search-what-is-an-index.md)。 此样本类别还包括一个服务统计信息示例。 |
| [dataSourceConnections（用于索引器）](https://github.com/Azure/azure-sdk-for-js/blob/main/sdk/search/search-documents/samples/v11/typescript/src/dataSourceConnectionOperations.ts) | 演示如何创建、更新、获取、列出和删除索引器数据源，对于基于索引器为[支持的 Azure 数据源](search-indexer-overview.md#supported-data-sources)编制索引而言，这些数据源是必需的。 |
| [索引器](https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/search/search-documents/samples/v11/typescript/src) |  演示如何创建、更新、获取、列出、重置和删除[索引器](search-indexer-overview.md)。|
| [skillSet](https://github.com/Azure/azure-sdk-for-js/blob/main/sdk/search/search-documents/samples/v11/typescript/src/skillSetOperations.ts) |   演示如何创建、更新、获取、列出和删除[技能组](cognitive-search-working-with-skillsets.md)，这些技能组是附加的索引器，并且在编制索引期间执行基于 AI 的扩充。 |
| [synonymMaps](https://github.com/Azure/azure-sdk-for-js/blob/main/sdk/search/search-documents/samples/v11/typescript/src/synonymMapOperations.ts) | 演示如何创建、更新、获取、列出和删除[同义词映射](search-synonyms.md)。  |

## <a name="doc-samples"></a>文档示例

认知搜索团队中的代码示例演示功能和工作流。 其中许多示例在教程、快速入门和操作说明文章中都有引用。 可以在 GitHub 上的 [Azure-Samples/azure-search-javascript-samples](https://github.com/Azure-Samples/azure-search-javascript-samples) 中找到这些示例。

| 示例 | 项目 |
|---------|---------|
| [quickstart](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/quickstart/v11) | [快速入门：在 JavaScript 中创建搜索索引](search-get-started-javascript.md)的源代码。 介绍使用示例数据来创建、加载和查询搜索索引的基本工作流。 |
| [search-website](https://github.com/azure-samples/azure-search-javascript-samples/tree/master/search-website) | [《教程：向 Web 应用添加搜索》](tutorial-javascript-overview.md)的源代码。 演示一个端到端搜索应用，其中包含一个富客户端以及用于托管应用和处理搜索请求的组件。|

> [!Tip]
> 请尝试使用[示例浏览器](/samples/browse/?languages=javascript&products=azure-cognitive-search)来搜索 GitHub 中的 Microsoft 代码示例（按产品、服务和语言进行筛选）。

## <a name="other-samples"></a>其他示例

以下示例也由认知搜索团队发布，但没有在文档中引用。 相关的自述文件提供了使用说明。

| 示例 | 说明 |
|---------|-------------|
| [azure-search-react-template](https://github.com/dereklegenzoff/azure-search-react-template) | Azure 认知搜索的 React 模板 (github.com) |
