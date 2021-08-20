---
title: Azure 认知搜索简介
titleSuffix: Azure Cognitive Search
description: Azure 认知搜索是 Microsoft 提供的完全托管式云搜索服务。 了解用例、开发工作流、与其他 Microsoft 搜索产品的比较，以及入门方法。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: overview
ms.date: 07/21/2021
ms.custom: contperf-fy21q1
ms.openlocfilehash: 803916184fb79c1db249b195dde0ed0b95c5c547
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114459636"
---
# <a name="what-is-azure-cognitive-search"></a>Azure 认知搜索是什么？

Azure 认知搜索（[以前称为“Azure 搜索”](whats-new.md#new-service-name)）是一个云搜索服务，它为开发人员提供基础结构、API 和工具，用于基于 Web、移动和企业应用程序中的专用异类内容构建丰富的搜索体验。

搜索是任何向用户展示文本内容的应用的基础，其常见方案包括目录或文档搜索、零售产品搜索或数据科学知识挖掘等。

创建搜索服务时，将使用以下功能：

+ 一个搜索引擎，它支持全文搜索，并可在搜索索引中存储用户拥有的内容
+ 丰富索引编制、文本分析以及用于提取和转换高级内容的可选 [AI 扩充](cognitive-search-concept-intro.md)
+ 丰富查询功能，包括简单语法、完整 Lucene 语法和自动提示搜索
+ 通过 REST API 和适用于 .NET、Python、Java 与 JavaScript 的 Azure SDK 中的客户端库实现的可编程性
+ 数据层、机器学习层和 AI（认知服务）级别的 Azure 集成

从体系结构方面来讲，搜索服务位于外部数据存储（包含未编入索引的数据）与客户端应用（向搜索索引发送查询请求并处理响应）之间。

![Azure 认知搜索体系结构](media/search-what-is-azure-search/azure-search-diagram.svg "Azure 认知搜索体系结构")

认知搜索可以以“索引器”（自动从 Azure 数据源引入/检索数据）和“技能组”（引入认知服务（例如图像和文本分析）中的可消耗 AI，或者引入你在 Azure 机器学习中创建的或在 Azure Functions 内包装的自定义 AI）的形式与其他 Azure 服务集成 。

## <a name="inside-a-search-service"></a>在搜索服务中

在搜索服务本身，两个主要工作负荷是索引编制和查询 。 

+ [编制索引](search-what-is-an-index.md)是向搜索服务加载内容并使其可供搜索的引入过程。 在内部，将入站文本处理到令牌中，并将其存储在逆选索引中，以便快速扫描。 你可以上传 JSON 文档格式的任何内容。

  此外，如果内容包含多种类型的文件，则可以选择通过[认知技能](cognitive-search-working-with-skillsets.md)添加 AI 扩充。 AI 扩充可以提取嵌入在应用程序文件中的文本，还可以通过分析内容从非文本文件中推断文本和结构。 

  提供这种分析的技能是 Microsoft 提供的预定义技能，或你创建的自定义技能。 后续的分析和转换可能会导致生成以前不存在的新信息和结构，为许多搜索和知识挖掘方案提供高实用性。

+ 当客户端应用将查询请求发送到搜索服务并处理响应时，索引中填充了可搜索的文本后，就会发生[查询](search-query-overview.md)。 所有查询执行都基于你在服务中创建、拥有和存储的搜索索引。 在客户端应用中，搜索体验是使用 Azure 认知搜索中的 API 定义的，可能包括相关性调整、自动完成、同义词匹配、模糊匹配、模式匹配、筛选和排序。

功能通过简单的 [REST API](/rest/api/searchservice/) 或 [.NET SDK](search-howto-dotnet-sdk.md) 公开，消除了信息检索固有的复杂性。 你还可以使用 Azure 门户，通过用于原型制作以及查询索引和技能组的工具进行服务管理和内容管理。 因为服务在云中运行，所以基础结构和可用性由 Microsoft 管理。

## <a name="why-use-cognitive-search"></a>为何使用认知搜索？

Azure 认知搜索非常适合以下应用方案：

+ 将异构内容整合成专用的用户定义的搜索索引。 将索引编制和查询工作负载分散到专用的搜索服务。

+ 轻松实现搜索相关的功能：相关性优化、分面导航、筛选器（包括地理空间搜索）、同义词映射和自动完成。

+ 将 Azure Blob 存储或 Cosmos DB 中存储的大型无差别文本、图像文件或应用程序文件转换为可搜索的 JSON 文档。 这是通过[添加外部处理](cognitive-search-concept-intro.md)的认知技能在编制索引期间实现的。

+ 添加语言或自定义文本分析。 如果你使用非英语内容，Azure 认知搜索支持 Lucene 分析器和 Microsoft 的自然语言处理器。 还可以配置分析器以实现原始内容的专业处理，例如筛选出标注字符，或识别并保留字符串中的模式。

有关特定功能的详细信息，请参阅 [Azure 认知搜索的 功能](search-features-list.md)

## <a name="how-to-get-started"></a>如何开始使用

可以通过以下四个步骤来实现核心搜索功能的端到端探索：

1. 在共享的免费层或[付费层](https://azure.microsoft.com/pricing/details/search/)（其中的资源专供你的服务使用）上[创建搜索服务](search-create-service-portal.md)。 所有快速入门和教程都可以通过共享服务完成。

1. 使用门户、[REST API](/rest/api/searchservice/create-index)、[.NET SDK](search-howto-dotnet-sdk.md) 或其他 SDK[创建搜索索引](search-what-is-an-index.md)。 索引架构决定了可搜索内容的结构。

1. 使用[“推送”模型](tutorial-optimize-indexing-push-api.md)[上传内容](search-what-is-data-import.md)，以从任意源推送 JSON 文档，如果数据源在 Azure 上，则使用[“拉取”模型（索引器）](search-indexer-overview.md)。

1. 使用门户 [REST API](search-get-started-rest.md)、[.NET SDK](/dotnet/api/azure.search.documents.searchclient.search) 或其他 SDK 中的[搜索资源管理器](search-explorer.md)[查询索引](search-query-overview.md)。

为了进行初步探索，请从[导入数据向导](search-get-started-portal.md)和内置 Azure 数据源开始，在几分钟内创建、加载和查询索引。

若要获取有关复杂或自定义解决方案的帮助，请与在认知搜索技术方面具有深厚专业知识的[合作伙伴](resource-partners-knowledge-mining.md)联系。

## <a name="compare-search-options"></a>比较搜索选项

客户常常询问 Azure 认知搜索与其他搜索相关解决方案有何不同。 下表总结主要区别。

| 比较对象 | 主要区别 |
|-------------|-----------------|
| Microsoft Search | [Microsoft 搜索](/microsoftsearch/overview-microsoft-search)适用于需要在 SharePoint 中查询内容的经过 Microsoft 365 身份验证的用户。 它作为现成可用的搜索体验提供，由管理员进行启用和配置，能够通过连接器接受来自 Microsoft 和其他来源的外部内容。 如果这与你的场景一致，则 Microsoft 365 的 Microsoft 搜索是一个值得探索的诱人选项。<br/><br/>相对地，Azure 认知搜索对你定义的索引执行查询，填充你拥有的数据和文档（常常来自多个不同的源）。 Azure 认知搜索具有通过[索引器](search-indexer-overview.md)爬取一些 Azure 数据源的功能，但你也可将符合你的索引架构的所有 JSON 文档推送到单个统一的可搜索资源。 你还可自定义索引管道，将机器学习和词法分析器纳入其中。 由于认知搜索被构建为更大型的解决方案中的一个插件组件，因此你可通过任意平台在几乎任意应用中集成搜索功能。|
|必应 | [必应 Web 搜索 API](../cognitive-services/bing-web-search/index.yml) 在 Bing.com 上搜索索引以匹配提交的搜索词。 索引从 HTML、XML 和公共网站上的其他 Web 内容生成。 [必应自定义搜索](/azure/cognitive-services/bing-custom-search/)构建于同一基础之上，针对 Web 内容类型提供相同的爬网技术，范围覆盖单个网站。<br/><br/>在认知搜索中，可定义并填充索引。 可使用[索引器](search-indexer-overview.md)在 Azure 数据源上爬取数据，或者将所有与索引一致的 JSON 文档推送到搜索服务。 |
|数据库搜索 | 许多数据库平台都包含内置的搜索体验。 SQL Server 具有[全文搜索](/sql/relational-databases/search/full-text-search)。 Cosmos DB 及类似技术具有可查询的索引。 在评估结合使用搜索和存储的产品时，确定要采用哪种方式可能颇具挑战性。 许多解决方案同时使用两种：使用 DBMS 进行存储，使用 Azure 认知搜索获取专业搜索功能。<br/><br/>与 DBMS 搜索相比，Azure 认知搜索存储来自不同来源的内容，并提供专用文本处理功能，例如 [56 种语言](/rest/api/searchservice/language-support)中的语言感知文本处理（词干化、词元化、词形式）。 它还支持拼写错误单词的自动更正、[同义词](/rest/api/searchservice/synonym-map-operations)、[建议](/rest/api/searchservice/suggestions)、[评分控制](/rest/api/searchservice/add-scoring-profiles-to-a-search-index)，[Facet](./search-filters-facets.md) 和[自定义词汇切分](/rest/api/searchservice/custom-analyzers-in-azure-search)。 Azure 认知搜索中的[全文搜索引擎](search-lucene-query-architecture.md)基于 Apache Lucene，它是信息检索方面的行业标准。 虽然 Azure 认知搜索以倒排索引的形式持久存储数据，但它不能替代真正的数据存储，建议不要在该容量中使用它。 有关详细信息，请参阅此[论坛帖子](https://stackoverflow.com/questions/40101159/can-azure-search-be-used-as-a-primary-database-for-some-data)。 <br/><br/>资源利用是这个类别的另一个转折点。 索引和一些查询操作通常是计算密集型的。 将搜索从 DBMS 卸载到云中的专用解决方案可以节省用于事务处理的系统资源。 此外，通过将搜索外部化，可以根据查询量轻松调整规模。|
|专用搜索解决方案 | 假设已决定使用全频谱功能进行专用搜索，则需要在本地解决方案或云服务之间进行最终的分类比较。 许多搜索技术提供对索引和查询管道的控制、对更丰富查询和筛选语法的访问、对设置级别和相关性的控制以及自导智能搜索功能。 <br/><br/>如果想要获得一个开销和维护工作量极少且规模可调的统包解决方案，则云服务是适当的选择。 <br/><br/>在云的范式中，许多提供程序提供相当的基线功能，以及全文搜索、地理搜索，并且能够处理搜索输入中一定程度的模糊性。 通常，它是一项[专用功能](search-features-list.md)，或者是 API、工具以及用于确定最匹配项的管理功能的易化和总体简化。 |

在所有云提供程序中，对于主要依赖于信息检索搜索和内容导航的应用，Azure 认知搜索在处理 Azure 上的内容存储和数据库的全文搜索工作负荷方面最为强大。 

主要优势包括：

+ 在索引层的 Azure 数据集成（爬网程序）
+ Azure 专用链接集成，支持断网安全要求
+ 与 AI 处理的集成，使不可搜索的内容类型变成可通过文本进行搜索。
+ 语言分析和自定义分析，提供分析器，用于支持以 56 种语言进行可靠的全文搜索
+ [关键功能](search-features-list.md)：丰富的查询语言、相关性优化、分面、自动补全、同义词、异地搜索和结果组合。
+ Azure 可伸缩性、可靠性和世界一流的可用性

在我们的所有客户中，能够利用 Azure 认知搜索中最广泛功能的客户包括在线目录、业务线程序以及文档发现应用程序。

## <a name="watch-this-video"></a>观看此视频

通过这段 15 分钟的视频了解 Azure 认知搜索的主要功能。

>[!VIDEO https://www.youtube.com/embed/kOJU0YZodVk?version=3]