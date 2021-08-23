---
title: 索引器概述
titleSuffix: Azure Cognitive Search
description: 对 Azure SQL 数据库、SQL 托管实例、Azure Cosmos DB 或 Azure 存储进行爬网，提取可搜索的数据并填充 Azure 认知搜索索引。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/29/2021
ms.openlocfilehash: 7a0ac8a344bc48a9d1ce14b326724236c229d096
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121734962"
---
# <a name="indexers-in-azure-cognitive-search"></a>Azure 认知搜索中的索引器

Azure 认知搜索中的索引器是一种爬网程序，它从外部 Azure 数据源提取可搜索的文本和元数据，并使用源数据与索引之间字段到字段的映射填充搜索索引。 由于不需要编写任何将数据添加到索引的代码，该服务就能拉取数据，因此这种方法有时也称为“拉取模式”。 索引器还驱动认知搜索的 [AI 扩充](cognitive-search-concept-intro.md)功能，在索引的路由中集成对内容的外部处理。

索引器仅适用于 Azure，其中包含适用于 [Azure SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)、[Azure Cosmos DB](search-howto-index-cosmosdb.md)、[Azure 表存储](search-howto-indexing-azure-tables.md) 和 [Blob 存储](search-howto-indexing-azure-blob-storage.md)的单个索引器。 配置索引器时，将指定数据源（原点）和索引（目标）。 Blob 存储等源具有特定于该内容类型的其他配置属性。

可以按需运行索引器，也可以采用每 5 分钟运行一次的定期数据刷新计划来运行索引器。 要进行更频繁的更新，则需要采用[“推送模式”](search-what-is-data-import.md)，便于同时更新 Azure 认知搜索和外部数据源中的数据。

## <a name="usage-scenarios"></a>使用方案

可以使用索引器作为数据引入的唯一方法，或者作为加载和选择性地转换或在此过程中扩充内容的技术组合的一部分。 下表汇总了主要方案。

| 方案 |策略 |
|----------|---------|
| 单个数据源 | 此模式是最简单的模式：一个数据源是搜索索引的唯一内容提供程序。 在源中，将标识一个字段，其中包含在搜索索引中充当文档键的唯一值。 唯一值将用作标识符。 所有其他源字段将隐式或显式映射到索引中的相应字段。 </br></br>一个重要结论是，文档键的值来自源数据。 搜索服务不生成键值。 在后续运行中，将添加包含新键的传入文档，同时合并或覆盖包含现有键的传入文档，具体取决于索引字段是 Null 还是已填充。 |
| 多个数据源 | 索引可以接受来自多个源的内容，其中每个运行都从不同的源中引入了新的内容。 </br></br>一种可能的结果是一个索引，它在每个索引器运行后获取文档，并在每个源中创建所有文档。 例如，文档 1-100 来自 Blob 存储，而文档 101-200 来自 Azure SQL 等。 此方案的难点在于设计适用于所有传入数据的索引架构和在搜索索引中保持一致的文档键结构。 在本机中，唯一标识文档的值是 Blob 容器中的 metadata_storage_path 和 SQL 表中的主键。 你可以设想一下，不考虑内容原点，必须修改一个或两个源来提供通用格式的键值。 对于这种情况，应执行一定程度的预处理来同质化数据，以便将数据拉取到单个索引中。 </br></br>另一种可能的结果是搜索文档，这些文档在第一次运行时只进行部分填充，在后续运行时再进一步填充以引入来自其他源的值。 例如，字段 1-10 来自 Blob 存储，而字段 11-20 来自 Azure SQL 等。 此模式的难点在于确保每个索引的运行都面向相同的文档。 若要将字段合并到现有文档中，则需要对文档键进行匹配。 有关此方案的演示，请参阅[教程：来自多个数据源的索引](tutorial-multiple-data-sources.md)。 |
| 多个索引器 | 使用多个数据源时，如果需要改变运行时参数、计划或字段映射，则可能还需要多个索引器。 虽然多个索引器数据源集能够以同一索引为目标，但要注意可以覆盖索引中现有值的索引器运行。 如果第二个索引器数据源以相同的文档和字段为目标，则将覆盖第一次运行中的任何值。 字段值将完全替换；索引器无法将多个运行中的值合并为同一字段。</br></br>另一个多索引器用例是[认知搜索的跨区域横向扩展](search-performance-optimization.md#data-sync)。 在不同区域中可能具有相同搜索索引的副本。 若要同步搜索索引内容，可从同一数据源中提取多个索引器，其中每个索引器都以不同的搜索索引为目标。</br></br>非常大的数据集的[并行索引](search-howto-large-index.md#parallel-indexing)还需要多索引器策略。 每个索引器都以数据的一个子集为目标。 |
| 内容转换 | 认知搜索支持可选的 [AI 扩充](cognitive-search-concept-intro.md)行为，这些行为可添加图像分析和自然语言处理，以创建新的可搜索内容和结构。 AI 扩充通过附加的[技能组](cognitive-search-working-with-skillsets.md)由索引器驱动。 若要执行 AI 扩充，索引器仍需要索引和 Azure 数据源，但在此方案中，会将技能组处理添加到索引器执行。 |

<a name="supported-data-sources"></a>

## <a name="supported-data-sources"></a>支持的数据源

索引器在 Azure 上和 Azure 外部抓取数据存储。

+ [Amazon Redshift](search-how-to-index-power-query-data-sources.md)（预览版）
+ [Azure Blob 存储](search-howto-indexing-azure-blob-storage.md)
+ [Azure Cosmos DB](search-howto-index-cosmosdb.md)
+ [Azure Data Lake Storage Gen2](search-howto-index-azure-data-lake-storage.md)
+ [Azure MySQL](search-howto-index-mysql.md)（预览版）
+ [Azure SQL 数据库](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Azure 表存储](search-howto-indexing-azure-tables.md)
+ [Elasticsearch](search-how-to-index-power-query-data-sources.md)（预览版）
+ [PostgreSQL](search-how-to-index-power-query-data-sources.md)（预览版）
+ [Salesforce Objects](search-how-to-index-power-query-data-sources.md)（预览版）
+ [Salesforce Reports](search-how-to-index-power-query-data-sources.md)（预览版）
+ [Smartsheet](search-how-to-index-power-query-data-sources.md)（预览版）
+ [Snowflake](search-how-to-index-power-query-data-sources.md)（预览版）
+ [SQL 托管实例](search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers.md)
+ [Azure 虚拟机中的 SQL Server](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md)

可以使用标准的 Internet 连接（公共），或者在将 Azure 虚拟网络用于客户端应用时使用加密的专用连接，以实现索引器与远程数据源的连接。 还可以建立使用可信服务标识进行身份验证的连接。 有关安全连接的详细信息，请参阅[通过专用终结点授予访问权限](search-indexer-securing-resources.md#granting-access-via-private-endpoints)和[使用托管标识连接到数据源](search-howto-managed-identities-data-sources.md)。

## <a name="stages-of-indexing"></a>索引阶段

在首次运行时，如果索引为空，索引器将读取表或容器中提供的所有数据。 在后续运行中，索引器通常可以只检测并检索已更改的数据。 对于 blob 数据，更改检测是自动进行的。 对于其他数据源（如 Azure SQL 或 Cosmos DB），必须启用更改检测。

对于它接收的每个文档，索引器将执行或协调多个步骤来编制索引，从文档检索到最终的搜索引擎“移交”。 索引器还可以驱动[技能组执行和输出](cognitive-search-concept-intro.md)（假设定义了技能组）。

:::image type="content" source="media/search-indexer-overview/indexer-stages.png" alt-text="索引器阶段" border="false":::

<a name="document-cracking"></a>

### <a name="stage-1-document-cracking"></a>第 1 阶段：文档破解

文档破解是打开文件并提取内容的过程。 可以从服务上的文件、表中的行或容器或集合中的项提取基于文本的内容。 如果将技能组和[图像技能](cognitive-search-concept-image-scenarios.md)添加到索引器，文档破解还可以提取图像并让其排队等待处理。

索引器会根据数据源尝试不同的操作来提取潜在的可索引内容：

+ 当文档是文件时（例如，文件是 PDF 格式或 [Azure Blob 存储](search-howto-indexing-azure-blob-storage.md#supported-document-formats)中支持的其他文件格式），索引器会打开该文件并提取文本、图像和元数据。 索引器还可以从 [SharePoint](search-howto-index-sharepoint-online.md#supported-document-formats) 和 [Azure Data Lake Storage Gen2](search-howto-index-azure-data-lake-storage.md#supported-document-formats) 打开文件。

+ 当文档是 [Azure SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md) 中的记录时，索引器会从每条记录的每个字段中提取非二进制内容。

+ 当文档是 [Cosmos DB](search-howto-index-cosmosdb.md) 中的记录时，索引器会从 Cosmos DB 文档的字段和子字段中提取非二进制内容。

### <a name="stage-2-field-mappings"></a>第 2 阶段：字段映射 

索引器提取源字段中的文本，并将其发送到索引或知识存储中的目标字段。 当字段名称和类型一致时，路径会被清除。 不过，如果希望输出中有不同的名称或类型，则需要告知索引器如何映射字段。 

当索引器从源文档读取时，此步骤需在文档破解后、转换之前进行。 在定义[字段映射](search-indexer-field-mappings.md)时，源字段的值将按原样发送到目标字段，而不进行任何修改。 

### <a name="stage-3-skillset-execution"></a>第 3 阶段：技能组执行

技能组执行是一个可选步骤，它调用内置或自定义 AI 处理。 如果源数据是二进制图像，则可能需要它以图像分析的形式进行光学字符识别 (OCR)，或者如果内容采用不同的语言，则可能需要语言翻译。 

无论是哪种转换，技能组执行都是扩充的途径。 如果索引器是管道，则可将[技能组](cognitive-search-defining-skillset.md)视为“管道内的管道”。

### <a name="stage-4-output-field-mappings"></a>阶段 4：输出字段映射

如果包括技能组，则很可能需要包含输出字段映射。 技能组的输出实际上是一棵称为“扩充文档”的信息树。 通过输出字段映射，可以选择此树中哪些部分要映射到索引中的字段。 了解如何[定义输出字段映射](cognitive-search-output-field-mapping.md)。

输出字段映射会指示索引器如何将扩充文档中已转换的值关联到索引中的目标字段，但是字段映射会将数据源中的逐字值关联到目标字段。 与被视为可选的字段映射不同，你始终需要为需要驻留在索引中的任何已转换内容定义输出字段映射。

下图显示了索引器不同阶段的示例索引器[调试会话](cognitive-search-debug-session.md)的表示形式：“文档破解”、“字段映射”、“技能组执行”和“输出字段映射”。

:::image type="content" source="media/search-indexer-overview/sample-debug-session.png" alt-text="示例调试会话" lightbox="media/search-indexer-overview/sample-debug-session.png":::

## <a name="basic-workflow"></a>基本工作流

索引器可提供数据源独有的功能。 因此，索引器或数据源配置的某些方面会因索引器类型而不同。 但是，所有索引器的基本构成元素和要求都相同。 下面介绍所有索引器都适用的共同步骤。

### <a name="step-1-create-a-data-source"></a>步骤 1：创建数据源

索引器需要提供连接字符串的数据源对象和可能的凭据。 调用[创建数据源 (REST)](/rest/api/searchservice/create-data-source) 或 [SearchIndexerDataSourceConnection 类](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourceconnection)以创建资源。

数据源的配置和管理独立于使用数据源的索引器，这意味着多个索引器可使用一个数据源，同时加载多个索引。

### <a name="step-2-create-an-index"></a>步骤 2：创建索引

索引器会自动执行某些与数据引入相关的任务，但通常不会自动创建索引。 先决条件是必须具有预定义的索引，且索引的字段必须与外部数据源中的字段匹配。 字段需按名称和数据类型进行匹配。 如果不是，可以[定义字段映射](search-indexer-field-mappings.md)以建立关联。 有关构建索引的详细信息，请参阅[创建索引 (REST)](/rest/api/searchservice/Create-Index) 或 [SearchIndex 类](/dotnet/api/azure.search.documents.indexes.models.searchindex)。

> [!Tip]
> 虽然不能使用索引器来生成索引，但可以使用门户中的 **导入数据** 向导。 大多数情况下，该向导可以根据源中现有的元数据推断索引架构，提供一个初级索引架构，该架构在向导处于活动状态时可以进行内联编辑。 在服务上创建索引以后，若要在门户中进一步进行编辑，多数情况下只能添加新字段。 可以将向导视为索引的创建工具而非修订工具。 如需手动方式的学习，请一步步完成[门户演练](search-get-started-portal.md)。

### <a name="step-3-create-and-run-or-schedule-the-indexer"></a>步骤 3：创建和运行（或计划）索引器

在搜索服务上首次[创建索引器](/rest/api/searchservice/Create-Indexer)时，该索引器会运行。 只有在创建或运行索引器时，才会发现数据源是否可以访问，或者技能组是否有效。 首次运行后，可以使用[运行索引器](/rest/api/searchservice/run-indexer)按需重新运行它，也可以[定义定期计划](search-howto-schedule-indexers.md)。 

可以通过门户或[获取索引器状态 API](/rest/api/searchservice/get-indexer-status) 监视索引器状态。 还应[对索引运行查询](search-query-create.md)，以验证结果是否符合预期。

## <a name="next-steps"></a>后续步骤

现在已经介绍完了，下一步是查看索引器属性和参数、计划和索引器监视。 或者，可以返回到[支持的数据源](#supported-data-sources)列表来获取有关特定源的详细信息。

+ [创建索引器](search-howto-create-indexers.md)
+ [重置并运行索引器](search-howto-run-reset-indexers.md)
+ [计划索引器](search-howto-schedule-indexers.md)
+ [定义字段映射](search-indexer-field-mappings.md)
+ [监视索引器状态](search-howto-monitor-indexers.md)