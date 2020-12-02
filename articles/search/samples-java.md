---
title: Java 示例
titleSuffix: Azure Cognitive Search
description: 查找使用 Azure .NET SDK for Java 的 Azure 认知搜索演示 Java 代码示例。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/17/2020
ms.openlocfilehash: c1d21d88ed49cb14aa9f964791154f001a40fbe2
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2020
ms.locfileid: "96499571"
---
# <a name="java-code-samples-for-azure-cognitive-search"></a>适用于 Azure 认知搜索的 Java 代码示例

了解演示 Azure 认知搜索的特性和功能的 Java 代码示例。 主要存储库如下所示：

| 存储库 | 说明 |
|------------|-------------|
| [适用于 java/树/主/sdk/搜索/azure 搜索-文档/src/示例的 azure sdk](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/search/azure-search-documents/src/samples) | 由 Azure SDK 团队生成的示例由 SDK 中 Azure.Search.Documents 客户端库附带。 你还可以查看客户端库的 [单元测试](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/search/azure-search-documents/src/test) ，以查看如何调用各种 api。 |
| [Azure-示例/azure-搜索-java 示例](https://github.com/Azure-Samples/azure-search-java-samples) | 操作方法文章附带的代码示例。 **此存储库中的示例尚未更新为使用 AZURE SDK For Java**。 目前，这些示例在 Java 代码中调用 REST Api。|

> [!Tip]
> 尝试使用 [示例浏览器](/samples/browse/?languages=csharp&products=azure-cognitive-search) 在 Github 中搜索 Microsoft 代码示例，并按产品、服务和语言进行筛选。

## <a name="java-sdk-samples"></a>Java SDK 示例

适用于 Java 的 Azure SDK 包括许多示例和一个涵盖包安装的入门 [页面](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/search/azure-search-documents/src/samples) 。 此页还列出了各种示例。 为了方便起见，下面列出了几个比较常见的操作。

| 示例 | 说明 |
|---------|-------------|
| [搜索索引创建](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateIndexExample.java) | 演示如何创建 [搜索索引](search-what-is-an-index.md)。 |
| [同义词创建](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/SynonymMapsCreateExample.java) | 演示如何创建 [同义词映射](search-synonyms.md)。  |
| [搜索索引器创建](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateIndexerExample.java) | 演示如何创建 [索引器](search-indexer-overview.md)。 |
| [搜索索引器数据源创建](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/DataSourceExample.java) | 演示如何创建索引器数据源，这些数据源是 [支持的 Azure 数据源](search-indexer-overview.md#supported-data-sources)的基于索引器索引所必需的。 |
| [技能组合创建](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateSkillsetExample.java) |  演示如何创建附加了索引器的 [技能集](cognitive-search-working-with-skillsets.md) ，并在索引期间执行基于 AI 的扩充。 |
| [加载文档](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/IndexContentManagementExample.java) | 演示如何在 [数据导入](search-what-is-data-import.md) 操作中将文档上载或合并到索引中。 |
| [查询语法](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/SearchAsyncWithFullyTypedDocumentsExample.java) | 演示如何设置 [基本查询](search-query-overview.md)。 |

## <a name="documentation-samples"></a>文档示例

以下示例在 [Azure 认知搜索文档](./index.yml)中有一个相关的文章。

| 示例 | 说明 | 
|---------|-------------|
| [起步](https://github.com/Azure-Samples/azure-search-java-samples/tree/master/quickstart) | [快速入门的源代码：在 Java 中创建搜索索引](search-get-started-java.md)。 此示例调用 REST Api。 |
| [搜索-java-索引器-演示](https://github.com/Azure-Samples/azure-search-java-samples/tree/master/search-java-indexer-demo) | 演示 Java 中的 Azure Cosmos DB 索引器。 此示例调用 REST Api。 |