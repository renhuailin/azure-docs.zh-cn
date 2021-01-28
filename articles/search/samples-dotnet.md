---
title: .NET 示例
titleSuffix: Azure Cognitive Search
description: 查找使用 .NET 客户端库的 Azure 认知搜索演示 C# 代码示例。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/27/2021
ms.openlocfilehash: 5567cf3bf606b08ce430f9189467d796498ae691
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2021
ms.locfileid: "98953897"
---
# <a name="net-c-code-samples-for-azure-cognitive-search"></a>Azure 认知搜索的 .NET (C#) 代码示例

了解演示 Azure 认知搜索解决方案的功能和工作流的 c # 代码示例。 这些示例使用适用于 [**.net 的 AZURE SDK**](/dotnet/azure/)的 [**azure 认知搜索客户端库**](/dotnet/api/overview/azure/search)，你可以浏览以下链接。

| 目标 | 链接 |
|--------|------|
| 包下载 | [www.nuget.org/packages/Azure.Search.Documents/](https://www.nuget.org/packages/Azure.Search.Documents/) |
| API 参考 | [azure.search.documents](/dotnet/api/azure.search.documents)  |
| API 测试用例 | [github.com/Azure/azure-sdk-for-net/tree/master/sdk/search/Azure.Search.Documents/测试](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/search/Azure.Search.Documents/tests) |
| 源代码 | [github.com/Azure/azure-sdk-for-net/tree/master/sdk/search/Azure.Search.Documents/src](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/search/Azure.Search.Documents/src)  |

## <a name="sdk-samples"></a>SDK 示例

Azure SDK 开发团队中的代码示例演示了 API 使用。 可以在 [**azure/azure-sdk 中找到这些示例，了解 GitHub 上的 net/tree/master/sdk/search/Azure.Search.Documents/示例**](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/) 。

| 示例 | 说明 |
|---------|-------------|
| [“Hello world”，同步](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample01a_HelloWorld.md) | 演示如何使用同步方法创建客户端、验证身份和处理错误。|
| [“Hello world”，异步](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample01b_HelloWorldAsync.md) | 演示如何使用异步方法创建客户端、验证身份和处理错误。  |
| [服务级操作](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample02_Service.md) | 演示如何创建索引、索引器、数据源、技能组和同义词映射。 此示例还演示如何获取服务统计信息以及如何查询索引。  |
| [索引操作](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample03_Index.md) | 演示如何对现有索引执行操作，在此示例中，演示如何获取索引中存储的文档数。  |
| [FieldBuilderIgnore](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample04_FieldBuilderIgnore.md) | 演示用于处理不受支持的数据类型的方法。  |
| [为文档编制索引（推送模型）](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample05_IndexingDocuments.md) | 编制索引的“推送”模型，用于将 JSON 有效负载发送到服务的索引。   |
| [加密密钥示例](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample06_EncryptedIndex.md) | 演示如何使用客户管理的加密密钥为敏感内容添加额外保护层。  |

## <a name="doc-samples"></a>文档示例

认知搜索团队中的代码示例演示了功能和工作流。 其中的许多示例在教程、快速入门和操作指南文章中进行了引用。 可以在 [**azure-示例/azure-dotnet-示例**](https://github.com/Azure-Samples/azure-search-dotnet-samples) 和 GitHub 上的 [**azure-示例/dotnet-**](https://github.com/Azure-Samples/search-dotnet-getting-started/) 入门中找到这些示例。

| 示例 | 文章  |
|---------|-------------|
| [快速入门](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/quickstart) | [快速入门：创建搜索索引](search-get-started-dotnet.md)的源代码。 本文介绍了使用示例数据创建、加载和查询搜索索引的基本工作流。 |
| [DotNetHowTo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo)  | [如何使用 .net 客户端库](search-howto-dotnet-sdk.md)的源代码。 本文逐步讲解基本工作流，但更详细地讨论了 API 使用情况。  |
| [DotNetHowToSynonyms](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToSynonyms)  | [示例中的源代码：在 c # 中添加同义词](search-synonyms-tutorial-sdk.md)。 同义词列表用于扩展查询，可提供索引外部的匹配术语。 |
| [DotNetToIndexers](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToIndexers) | 教程的源代码 [：使用 .NET SDK 为 AZURE SQL 数据编制索引](search-indexer-tutorial.md)。 本文介绍如何配置具有计划、字段映射和参数的 Azure SQL 索引器。  |
| [DotNetHowToEncryptionUsingCMK](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToEncryptionUsingCMK)  | 用于 [配置数据加密的客户托管密钥](search-security-manage-encryption-keys.md)的源代码。 |
| [使用 C# 创建自己的第一个应用](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11) |  [教程：创建自己的第一个搜索应用](tutorial-csharp-create-first-app.md)的源代码。 虽然大多数示例都是控制台应用程序，但此 MVC 示例使用网页作为酒店索引示例的前端，演示了基本搜索、分页、自动完成以及建议的查询、分面和筛选。 |
| [multiple-data-sources](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/multiple-data-sources)  | [教程：从多个数据源编制索引](tutorial-multiple-data-sources.md)的源代码。 |
|  [optimize-data-indexing](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/optimize-data-indexing) | [教程：使用推送 API 优化索引编制](tutorial-optimize-indexing-push-api.md)的源代码。  |
| [tutorial-ai-enrichment](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/tutorial-ai-enrichment)  | [教程：AI 使用 .NET SDK 从 Azure Blob 生成可搜索内容](cognitive-search-tutorial-blob-dotnet.md)的源代码。  |

> [!Tip]
> 请尝试使用[示例浏览器](/samples/browse/?languages=csharp&products=azure-cognitive-search)来搜索 Github 中的 Microsoft 代码示例，搜索时可按产品、服务和语言进行筛选。

## <a name="other-samples"></a>其他示例

以下示例也由认知搜索团队发布，但在文档中未被引用。 相关的自述文件提供了使用说明。

| 示例 | 说明 |
|---------|-------------|
| [azure-search-power-skills](https://github.com/Azure-Samples/azure-search-power-skills)  | 可合并到你自己的解决方案中的可耗用自定义技能的源代码。  |
| [知识挖掘解决方案加速器](/samples/azure-samples/azure-search-knowledge-mining/azure-search-knowledge-mining/) | 包括模板、支持文件和分析报表，以帮助你建立端到端知识挖掘解决方案的原型。  |
| [Covid-19 搜索应用存储库](https://github.com/liamca/covid19search) | 基于认知搜索的 [Covid-19 搜索应用](https://covid19search.azurewebsites.net/)的源代码存储库 |
| [JFK](https://github.com/Microsoft/AzureSearch_JFK_Files) | 详细了解 [JFK 解决方案](https://www.microsoft.com/ai/ai-lab-jfk-files)。 |