---
title: .NET 示例
titleSuffix: Azure Cognitive Search
description: '查找使用 .NET 客户端库的 Azure 认知搜索演示 c # 代码示例。'
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/17/2020
ms.openlocfilehash: ab6408621616a4be62631391456f73e90fced752
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2020
ms.locfileid: "96498993"
---
# <a name="net-c-code-samples-for-azure-cognitive-search"></a>适用于 Azure 的 .NET (c # ) 代码示例认知搜索

了解演示 Azure 认知搜索的特性和功能的 c # 代码示例。 主要存储库如下所示：

| 存储库 | 说明 |
|------------|-------------|
| [azure-用于-net/sdk/search/Azure.Search.Documents/samples/](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/search/Azure.Search.Documents/samples) | 由 Azure SDK 团队生成的示例由 SDK 中 Azure.Search.Documents 客户端库附带。 你还可以查看客户端库的 [单元测试](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/search/Azure.Search.Documents/tests) ，以查看如何调用各种 api。 |
| [Azure-示例/azure-dotnet-示例](https://github.com/Azure-Samples/azure-search-dotnet-samples) | 文档中附带操作说明文章的示例，包括 [如何使用 .net 客户端库](search-howto-dotnet-sdk.md)。|
| [Azure-示例/dotnet-入门](https://github.com/Azure-Samples/search-dotnet-getting-started) | 文档中的快速入门和教程附带的示例。|

> [!Tip]
> 尝试使用 [示例浏览器](/samples/browse/?languages=csharp&products=azure-cognitive-search) 在 Github 中搜索 Microsoft 代码示例，并按产品、服务和语言进行筛选。

## <a name="net-sdk-samples"></a>.NET SDK 示例

适用于 .NET 的 Azure SDK 包含许多示例和介绍每个示例的 [自述文件](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/README.md) 。 为方便起见，下面提供了此列表。

| 示例 | 说明 |
|---------|-------------|
| ["Hello world"，同步](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample01a_HelloWorld.md) | 演示如何使用同步方法创建客户端、身份验证和处理错误。|
| ["Hello world"，异步](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample01b_HelloWorldAsync.md) | 演示如何使用异步方法创建客户端、身份验证和处理错误。  |
| [服务级别操作](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample02_Service.md) | 演示如何创建索引、索引器、数据源、技能集和同义词映射。 此示例还演示如何获取服务统计信息以及如何查询索引。  |
| [索引操作](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample03_Index.md) | 演示如何对现有索引执行操作，在此示例中，获取索引中存储的文档数。  |
| [FieldBuilderIgnore](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample04_FieldBuilderIgnore.md) | 演示使用不受支持的数据类型的方法。  |
| [ (推送模型) 索引文档 ](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample05_IndexingDocuments.md) | "推送" 模型索引，将 JSON 有效负载发送到服务的索引。   |
| [加密密钥示例](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample06_EncryptedIndex.md) | 演示如何使用客户托管的加密密钥来添加对敏感内容的额外保护层。  |

## <a name="documentation-samples"></a>文档示例

以下示例在 [Azure 认知搜索文档](./index.yml)中有一个相关的文章。

| 示例 | 说明 |
|---------|-------------|
| [起步](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/quickstart) | [快速入门的源代码：创建搜索索引](search-get-started-dotnet.md)。  |
| [DotNetHowTo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo)  | [如何使用 .net 客户端库](search-howto-dotnet-sdk.md)的源代码 |
| [DotNetHowToSynonyms](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToSynonyms)  | 同义词列表用于查询扩展，提供索引外部的匹配术语。 此示例包括在 [示例中：在 c # 中添加同义词](search-synonyms-tutorial-sdk.md)。 |
| [DotNetToIndexers](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToIndexers) | 各个项目中与索引器相关的代码段的源代码。 此示例演示如何配置具有计划、字段映射和参数的索引器。  |
| [DotNetHowToEncryptionUsingCMK](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToEncryptionUsingCMK)  | [如何配置用于数据加密的客户托管密钥](search-security-manage-encryption-keys.md)的源代码 |
| [在 C 中创建第一个应用#](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11) |  教程的源代码 [：创建第一个搜索应用](tutorial-csharp-create-first-app.md)。 虽然大多数示例都是控制台应用程序，但此 MVC 示例使用的是示例酒店索引的顶层，其中演示了基本搜索、分页、自动完成和建议的查询、方面和筛选器。 |
| [多数据源](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/multiple-data-sources)  | 教程的源代码 [：来自多个数据源的索引](tutorial-multiple-data-sources.md)。 |
|  [优化-数据索引](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/optimize-data-indexing) | 教程的源代码 [：通过推送 API 优化索引](tutorial-optimize-indexing-push-api.md)。  |
| [教程-ai-扩充](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/tutorial-ai-enrichment)  | 教程的源代码 [： AI-使用 .NET SDK 从 Azure blob 生成的可搜索内容](cognitive-search-tutorial-blob-dotnet.md)。  |

## <a name="standalone-samples-and-solutions"></a>独立示例和解决方案

| 示例 | 说明 |
|---------|-------------|
| [azure-搜索-强大技能](https://github.com/Azure-Samples/azure-search-power-skills)  | 可在你的赢单解决方案中引入的可耗用自定义技能的源代码。  |
| [知识挖掘解决方案加速器](/samples/azure-samples/azure-search-knowledge-mining/azure-search-knowledge-mining/) | 包括模板、支持文件和分析报表，以帮助你建立端到端知识挖掘解决方案的原型。  |
| [Covid-19 搜索应用存储库](https://github.com/liamca/covid19search) | 基于认知搜索[Covid-19 搜索应用](https://covid19search.azurewebsites.net/)的源代码存储库 |
| [JFK](https://github.com/Microsoft/AzureSearch_JFK_Files) | 了解有关 [JFK 解决方案](https://www.microsoft.com/ai/ai-lab-jfk-files)的详细信息。 |