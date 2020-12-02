---
title: JavaScript 示例
titleSuffix: Azure Cognitive Search
description: 查找使用 Azure .NET SDK for JavaScript 的 Azure 认知搜索演示 JavaScript 代码示例。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/17/2020
ms.openlocfilehash: 234c70fffb6f353c670d23624cc446fdaf6bd886
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2020
ms.locfileid: "96498959"
---
# <a name="javascript-code-samples-for-azure-cognitive-search"></a>适用于 Azure 认知搜索的 JavaScript 代码示例

了解演示 Azure 认知搜索的特性和功能的 JavaScript 代码示例。 主要存储库如下所示：

| 存储库 | 说明 |
|------------|-------------|
| [azure sdk-用于-js/tree/master/sdk/搜索/搜索-文档](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents) | 由 Azure SDK 团队生成的示例由 SDK 中 Azure.Search.Documents 客户端库附带。 你还可以查看客户端库的 [单元测试](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/test) ，以查看如何调用各种 api。 |
| [Azure-示例/azure--javascript-示例](https://github.com/Azure-Samples/azure-search-javascript-samples) | 操作指南文章随附的代码示例，包括 [快速入门：在 JavaScript 中创建搜索索引](search-get-started-javascript.md)。|

> [!Tip]
> 尝试使用 [示例浏览器](/samples/browse/?languages=csharp&products=azure-cognitive-search) 在 Github 中搜索 Microsoft 代码示例，并按产品、服务和语言进行筛选。

## <a name="javascript-sdk-samples"></a>JavaScript SDK 示例

适用于 Java 的 Azure SDK 包括许多示例和入门 [页面](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/README.md#getting-started) ，涵盖包安装、客户端设置和故障排除。 此页还介绍了下面列出的示例类别，以方便您方便。

| 示例 | 说明 |
|---------|-------------|
| [全文](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/indexes) | 演示如何创建、更新、获取、列出和删除 [搜索索引](search-what-is-an-index.md)。 此示例类别还包括一个服务统计信息示例。 |
| [索引器的 dataSourceConnections () ](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/dataSourceConnections) | 演示如何创建、更新、获取、列出和删除索引器数据源，这些数据源是 [支持的 Azure 数据源](search-indexer-overview.md#supported-data-sources)的索引编制索引所必需的。 |
| [索引器](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/indexers) |  演示如何创建、更新、获取、列出、重置和删除 [索引器](search-indexer-overview.md)。|
| [技能组合](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/skillSets) |   演示如何创建、更新、获取、列出和删除附加了索引器的 [技能集](cognitive-search-working-with-skillsets.md) ，并在索引期间执行基于 AI 的扩充。 |
| [synonymMaps](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/synonymMaps) | 演示如何创建、更新、获取、列出和删除 [同义词映射](search-synonyms.md)。  |
| [查询](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/search/search-documents/samples/javascript/src/readonlyQuery.js) | 演示如何针对 Microsoft 托管的只读公共索引执行查询。  |

## <a name="typescript-samples"></a>TypeScript 示例

SDK 还提供了 TypeScript 示例，为方便起见列出了这些示例。

| 示例 | 说明 |
|---------|-------------|
| [全文](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/indexes) | 演示如何创建、更新、获取、列出和删除 [搜索索引](search-what-is-an-index.md)。 此示例类别还包括一个服务统计信息示例。 |
| [索引器的 dataSourceConnections () ](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/dataSourceConnections) | 演示如何创建、更新、获取、列出和删除索引器数据源，这些数据源是 [支持的 Azure 数据源](search-indexer-overview.md#supported-data-sources)的索引编制索引所必需的。 |
| [索引器](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/indexers) |  演示如何创建、更新、获取、列出、重置和删除 [索引器](search-indexer-overview.md)。|
| [技能组合](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/skillSets) |   演示如何创建、更新、获取、列出和删除附加了索引器的 [技能集](cognitive-search-working-with-skillsets.md) ，并在索引期间执行基于 AI 的扩充。 |
| [synonymMaps](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/synonymMaps) | 演示如何创建、更新、获取、列出和删除 [同义词映射](search-synonyms.md)。  |
| [查询](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/search/search-documents/samples/typescript/src/readonlyQuery.ts) | 演示如何针对 Microsoft 托管的只读公共索引执行查询。  |

## <a name="documentation-samples"></a>文档示例

以下示例在 [Azure 认知搜索文档](./index.yml)中有一个相关的文章。

| 示例 | 说明 | 
|---------|-------------|
| [起步](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/quickstart/v11) | [快速入门的源代码：在 JavaScript 中创建搜索索引](search-get-started-javascript.md)。  |

## <a name="standalone-samples"></a>独立示例

| 示例 | 说明 |
|---------|-------------|
| [azure-搜索-响应-模板](https://github.com/dereklegenzoff/azure-search-react-template) | Azure 认知搜索的响应模板 (github.com)  |