---
title: .NET 示例
titleSuffix: Azure Cognitive Search
description: 查找使用 .NET 客户端库的 Azure 认知搜索演示 C# 代码示例。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/01/2021
ms.openlocfilehash: 0f4ce35a43702f958656b1ad077e6c0f84bc88bb
ms.sourcegitcommit: 079426f4980fadae9f320977533b5be5c23ee426
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/04/2021
ms.locfileid: "129418701"
---
# <a name="net-c-code-samples-for-azure-cognitive-search"></a>Azure 认知搜索的 .NET (C#) 代码示例

了解演示 Azure 认知搜索解决方案的功能和工作流的 C# 代码示例。 这些示例使用了适用于 [Azure SDK for .NET](/dotnet/azure/) 的 [Azure 认知搜索客户端库](/dotnet/api/overview/azure/search)，你可以通过以下链接来探究这些库 。

| 目标 | 链接 |
|--------|------|
| 包下载 | [www.nuget.org/packages/Azure.Search.Documents/](https://www.nuget.org/packages/Azure.Search.Documents/) |
| API 参考 | [azure.search.documents](/dotnet/api/azure.search.documents)  |
| API 测试用例 | [github.com/Azure/azure-sdk-for-net/tree/master/sdk/search/Azure.Search.Documents/tests](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/search/Azure.Search.Documents/tests) |
| 源代码 | [github.com/Azure/azure-sdk-for-net/tree/master/sdk/search/Azure.Search.Documents/src](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/search/Azure.Search.Documents/src)  |

## <a name="sdk-samples"></a>SDK 示例

Azure SDK 开发团队中的代码示例演示 API 用法。 可以在 GitHub 上的 [Azure/azure-sdk-for-net/tree/master/sdk/search/Azure.Search.Documents/samples](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/) 中找到这些示例。

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

认知搜索团队中的代码示例演示功能和工作流。 其中许多示例引自教程、快速入门和操作指南文章。 可以在 GitHub 上的 [Azure-Samples/azure-search-dotnet-samples](https://github.com/Azure-Samples/azure-search-dotnet-samples) 和 [Azure-Samples/search-dotnet-getting-started](https://github.com/Azure-Samples/search-dotnet-getting-started/) 中找到这些示例 。

| 示例 | 项目  |
|---------|-------------|
| [快速入门](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/quickstart) | [快速入门：创建搜索索引](search-get-started-dotnet.md)的源代码。 介绍了使用示例数据来创建、加载和查询搜索索引的基本工作流。 |
| [search-website](https://github.com/azure-samples/azure-search-dotnet-samples/tree/master/search-website) | [《教程：向 Web 应用添加搜索》](tutorial-csharp-overview.md)的源代码。 演示一个端到端搜索应用，其中包含一个富客户端以及用于托管应用和处理搜索请求的组件。|
| [DotNetHowTo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo)  | [如何使用 .NET 客户端库](search-howto-dotnet-sdk.md)的源代码。 不仅逐步讲解基本工作流，还详细介绍了 API 的用法。  |
| [DotNetHowToSynonyms](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToSynonyms)  | [示例：在 C# 中添加同义词](search-synonyms-tutorial-sdk.md)的源代码。 同义词列表用于扩展查询，可提供索引外部的匹配术语。 |
| [DotNetToIndexers](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToIndexers) | [教程：使用 .NET SDK 为 Azure SQL 数据编制索引](search-indexer-tutorial.md)的源代码。 本文演示如何配置具有计划、字段映射和参数的 Azure SQL 索引器。  |
| [DotNetHowToEncryptionUsingCMK](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToEncryptionUsingCMK)  | [如何配置客户管理的密钥以进行数据加密](search-security-manage-encryption-keys.md)的源代码。 |
| [使用 C# 创建自己的第一个应用](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11) |  [教程：创建自己的第一个搜索应用](tutorial-csharp-create-first-app.md)的源代码。 虽然大多数示例都是控制台应用程序，但此 MVC 示例使用网页作为酒店索引示例的前端，演示了基本搜索、分页、自动完成以及建议的查询、分面和筛选。 |
| [multiple-data-sources](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/multiple-data-sources)  | [教程：从多个数据源编制索引](tutorial-multiple-data-sources.md)的源代码。 |
|  [optimize-data-indexing](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/optimize-data-indexing) | [教程：使用推送 API 优化索引编制](tutorial-optimize-indexing-push-api.md)的源代码。  |
| [tutorial-ai-enrichment](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/tutorial-ai-enrichment)  | [教程：AI 使用 .NET SDK 从 Azure Blob 生成可搜索内容](cognitive-search-tutorial-blob-dotnet.md)的源代码。  |

> [!Tip]
> 请尝试使用[示例浏览器](/samples/browse/?languages=csharp&products=azure-cognitive-search)来搜索 GitHub 中的 Microsoft 代码示例，搜索时可按产品、服务和语言进行筛选。

## <a name="other-samples"></a>其他示例

以下示例也由认知搜索团队发布，但没有在文档中引用。 相关的自述文件提供了使用说明。

| 示例 | 说明 |
|---------|-------------|
| [使用 Azure AD 为 Data Lake Gen2 编制索引](https://github.com/Azure-Samples/azure-search-dotnet-samples/blob/master/data-lake-gen2-acl-indexing/README.md) | 源代码，用于演示索引器连接，以及为通过 Azure AD 和基于角色的访问控制进行保护的 Azure Data Lake Gen2 文件和文件夹编制索引。 |
| [azure-search-power-skills](https://github.com/Azure-Samples/azure-search-power-skills)  | 可合并到你自己的解决方案中的可耗用自定义技能的源代码。  |
| [知识挖掘解决方案加速器](/samples/azure-samples/azure-search-knowledge-mining/azure-search-knowledge-mining/) | 包括模板、支持文件和分析报表，以帮助你建立端到端知识挖掘解决方案的原型。  |
| [Covid-19 搜索应用存储库](https://github.com/liamca/covid19search) | 基于认知搜索的 [Covid-19 搜索应用](https://covid19search.azurewebsites.net/)的源代码存储库 |
| [JFK](https://github.com/Microsoft/AzureSearch_JFK_Files) | 详细了解 [JFK 解决方案](https://www.microsoft.com/ai/ai-lab-jfk-files)。 |
| [搜索 + QnA Maker 加速器](https://github.com/Azure-Samples/search-qna-maker-accelerator) | 一种[解决方案](https://techcommunity.microsoft.com/t5/azure-ai/qna-with-azure-cognitive-search/ba-p/2081381)，结合了搜索和 QnA Maker 的功能。 请参阅实时[演示网站](https://aka.ms/qnaWithAzureSearchDemo)。 |
