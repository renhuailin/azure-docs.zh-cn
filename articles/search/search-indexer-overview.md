---
title: 索引器概述
titleSuffix: Azure Cognitive Search
description: 对 Azure SQL 数据库、SQL 托管实例、Azure Cosmos DB 或 Azure 存储进行爬网，提取可搜索的数据并填充 Azure 认知搜索索引。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/28/2021
ms.openlocfilehash: 0d0ec6d6512655277a278db9a1e05b6ca58bfc92
ms.sourcegitcommit: 1a98b3f91663484920a747d75500f6d70a6cb2ba
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2021
ms.locfileid: "99063156"
---
# <a name="indexers-in-azure-cognitive-search"></a>Azure 认知搜索中的索引器

Azure 认知搜索中的 *索引器* 是一种爬网程序，它从外部 Azure 数据源提取可搜索的文本和元数据，并使用源数据和索引之间的字段到字段映射填充搜索索引。 由于不需要编写任何将数据添加到索引的代码，该服务就能拉取数据，因此这种方法有时也称为“拉取模式”。

索引器仅适用于 azure，其中包含 [AZURE SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)的单个索引器、 [Azure Cosmos DB](search-howto-index-cosmosdb.md)、 [Azure 表存储](search-howto-indexing-azure-tables.md) 和 [Blob 存储](search-howto-indexing-azure-blob-storage.md)。 配置索引器时，您将指定 (源) 的数据源，以及 (目标) 的索引。 一些源（例如 Blob 存储）具有特定于该内容类型的其他配置属性。

可以按需运行索引器，也可以采用每 5 分钟运行一次的定期数据刷新计划来运行索引器。 要进行更频繁的更新，则需要采用“推送模式”，便于同时更新 Azure 认知搜索和外部数据源中的数据。

## <a name="usage-scenarios"></a>使用方案

您可以使用索引器作为数据引入的唯一方法，也可以使用包括仅在索引中加载某些字段的方法组合，还可以根据情况转换或浓缩内容。 下表汇总了主要方案。

| 方案 |策略 |
|----------|---------|
| 单一源 | 此模式是最简单的模式：一个数据源是搜索索引的唯一内容提供程序。 在源中，您将标识一个字段，其中包含用于在搜索索引中充当文档键的唯一值。 将使用唯一值作为标识符。 所有其他源字段将隐式或显式映射到索引中的相应字段。 </br></br>一个重要的要点在于是，文档键的值源自源数据。 搜索服务不生成键值。 在后续运行中，将添加包含新密钥的传入文档，而包含现有密钥的传入文档会合并或覆盖，具体取决于索引字段是 null 还是填充。 |
| 多个源| 索引可以接受来自多个源的内容，其中每个源都从不同的源中引入了新的内容。 </br></br>其中一项结果可能是在每个索引器运行后获得文档的索引，其中的整个文档完全由每个源创建。 例如，文档1-100 来自 Blob 存储，文档101-200 来自 Azure SQL 等。 此方案的难题在于设计适用于所有传入数据的索引架构，以及在搜索索引中统一的文档键结构。 在本机中，唯一标识文档的值在 blob 容器中 metadata_storage_path，在 SQL 表中是主键。 您可以想象，无论内容来源如何，都必须修改一个或两个源来提供公共格式的键值。 对于这种情况，应执行一定程度的预处理来 homogenize 数据，以便将数据提取到单个索引中。</br></br>另一种结果可能是搜索在第一次运行时部分填充的文档，然后后续运行会进一步填充这些文档以引入来自其他源的值。 例如，1-10 域是从 Blob 存储、11-20 到 Azure SQL 等。 此模式的难题是确保每个索引运行都面向相同的文档。 若要将字段合并到现有文档中，则需要对文档键进行匹配。 有关此方案的演示，请参阅 [教程：来自多个数据源的索引](tutorial-multiple-data-sources.md)。 |
| 内容转换 | 认知搜索支持可选的 [AI 扩充](cognitive-search-concept-intro.md) 行为，这些行为可添加图像分析和自然语言处理，以创建新的可搜索内容和结构。 AI 扩充通过附加 [技能组合](cognitive-search-working-with-skillsets.md)由索引器驱动。 若要执行 AI 扩充，索引器仍需要索引和数据源，但在此方案中，会将技能组合处理添加到索引器执行。 |

<a name="supported-data-sources"></a>

## <a name="supported-data-sources"></a>支持的数据源

索引器在 Azure 上抓取数据存储。

+ [Azure Blob 存储](search-howto-indexing-azure-blob-storage.md)
+ [Azure Data Lake Storage Gen2](search-howto-index-azure-data-lake-storage.md)（预览版）
+ [Azure 表存储](search-howto-indexing-azure-tables.md)
+ [Azure Cosmos DB](search-howto-index-cosmosdb.md)
+ [Azure SQL 数据库](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [SQL 托管实例](search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers.md)
+ [Azure 虚拟机中的 SQL Server](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md)

## <a name="stages-of-indexing"></a>索引阶段

在首次运行时，如果索引为空，索引器将读取表或容器中提供的所有数据。 在后续运行中，索引器通常可以只检测并检索已更改的数据。 对于 blob 数据，更改检测是自动进行的。 对于其他数据源（如 Azure SQL 或 Cosmos DB），必须启用更改检测。

对于收到的每个文档，索引器实现或协调多个步骤，从文档检索到最终的搜索引擎 "移交" 进行索引。 （可选）如果定义了技能组，索引器还有助于推动技能组的执行和输出。

:::image type="content" source="media/search-indexer-overview/indexer-stages.png" alt-text="索引器阶段" border="false":::

### <a name="stage-1-document-cracking"></a>第 1 阶段：文档破解

文档破解是打开文件并提取内容的过程。 根据数据源的类型，索引器将尝试执行不同的操作来提取可能可索引的内容。  

示例：  

+ 如果文档是 [Azure SQL 数据源](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)中的记录，则索引器将提取记录中的每个字段。
+ 如果文档是 [Azure Blob 存储数据源](search-howto-indexing-azure-blob-storage.md)中的 PDF 文件，则索引器将提取文本、图像和元数据。
+ 如果文档是 [Cosmos DB 数据源](search-howto-index-cosmosdb.md)中的记录，则索引器将提取 Cosmos DB 文档中的字段和子字段。

### <a name="stage-2-field-mappings"></a>第 2 阶段：字段映射 

索引器提取源字段中的文本，并将其发送到索引或知识存储中的目标字段。 当字段名称和类型一致时，路径会被清除。 不过，如果希望输出中有不同的名称或类型，则需要告知索引器如何映射字段。 

当索引器从源文档读取时，此步骤需在文档破解后、转换之前进行。 在定义[字段映射](search-indexer-field-mappings.md)时，源字段的值将按原样发送到目标字段，而不进行任何修改。 

### <a name="stage-3-skillset-execution"></a>第 3 阶段：技能组执行

技能组执行是一个可选步骤，它调用内置或自定义 AI 处理。 对于光学字符识别，您可能需要使用它，如果源数据是二进制图像，则可以使用图像分析形式 (OCR) ，如果内容采用不同的语言，则可能需要语言翻译。 

无论是哪种转换，技能组执行都是扩充的途径。 如果索引器是管道，则可将[技能组](cognitive-search-defining-skillset.md)视为“管道内的管道”。

### <a name="stage-4-output-field-mappings"></a>阶段 4：输出字段映射

如果包括技能组合，则很可能需要包含输出字段映射。 技能组合的输出实际上是一棵称为 "扩充的 *文档*" 的信息树。 通过输出字段映射，可以选择此树中哪些部分要映射到索引中的字段。 了解如何[定义输出字段映射](cognitive-search-output-field-mapping.md)。

字段映射将数据源中的逐字值与目标字段关联起来，而输出字段映射会告知索引器如何将已放大文档中的已转换值关联到索引中的目标字段。 与被视为可选的字段映射不同，你始终需要为需要驻留在索引中的任何已转换内容定义输出字段映射。

下图显示了索引器阶段的示例索引器 [调试会话](cognitive-search-debug-session.md) 表示形式： "文档破解"、"字段映射"、"技能组合执行" 和 "输出字段映射"。

:::image type="content" source="media/search-indexer-overview/sample-debug-session.png" alt-text="示例调试会话" lightbox="media/search-indexer-overview/sample-debug-session.png":::

## <a name="basic-workflow"></a>基本工作流

索引器可提供数据源独有的功能。 因此，索引器或数据源配置的某些方面会因索引器类型而不同。 但是，所有索引器的基本构成元素和要求都相同。 下面介绍所有索引器都适用的共同步骤。

### <a name="step-1-create-a-data-source"></a>步骤 1：创建数据源

索引器需要提供连接字符串的 *数据源* 对象和可能的凭据。  (REST) 或[SearchIndexerDataSourceConnection 类](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourceconnection)调用 "[创建数据源](/rest/api/searchservice/create-data-source)" 来创建资源。

数据源的配置和管理独立于使用数据源的索引器，这意味着多个索引器可使用一个数据源，同时加载多个索引。

### <a name="step-2-create-an-index"></a>步骤 2：创建索引

索引器会自动执行某些与数据引入相关的任务，但通常不会自动创建索引。 先决条件是必须具有预定义的索引，且索引的字段必须与外部数据源中的字段匹配。 字段需按名称和数据类型进行匹配。 如果不是，可以 [定义字段映射](search-indexer-field-mappings.md) 以建立关联。 有关构造索引的详细信息，请参阅 [ (REST) ](/rest/api/searchservice/Create-Index) 或 [SearchIndex 类](/dotnet/api/azure.search.documents.indexes.models.searchindex)创建索引。

> [!Tip]
> 虽然不能使用索引器来生成索引，但可以使用门户中的 **导入数据** 向导。 大多数情况下，该向导可以根据源中现有的元数据推断索引架构，提供一个初级索引架构，该架构在向导处于活动状态时可以进行内联编辑。 在服务上创建索引以后，若要在门户中进一步进行编辑，多数情况下只能添加新字段。 可以将向导视为索引的创建工具而非修订工具。 如需手动方式的学习，请一步步完成[门户演练](search-get-started-portal.md)。

### <a name="step-3-create-and-run-or-schedule-the-indexer"></a>步骤3：创建并运行 (或计划) 索引器

首次在搜索服务中 [创建索引](/rest/api/searchservice/Create-Indexer) 器时，将运行一个索引器。 只有在创建或运行索引器时，才会发现数据源是可访问的，或者技能组合是有效的。 首次运行后，可以使用 " [运行索引器](/rest/api/searchservice/run-indexer)" 按需重新运行它，也可以 [定义定期计划](search-howto-schedule-indexers.md)。 

可以在门户中或通过 [获取索引器状态 API](/rest/api/searchservice/get-indexer-status)来监视索引器状态。 

## <a name="next-steps"></a>后续步骤

现在，您已介绍了，下一步是查看索引器属性和参数、计划和索引器监视。 或者，您可以返回到 [支持的数据源](#supported-data-sources) 列表，以获取有关特定源的详细信息。

+ [创建索引器](search-howto-create-indexers.md)
+ [计划索引器](search-howto-schedule-indexers.md)
+ [定义字段映射](search-indexer-field-mappings.md)
+ [监视器索引器状态](search-howto-monitor-indexers.md)