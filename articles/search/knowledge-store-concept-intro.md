---
title: 知识存储概念
titleSuffix: Azure Cognitive Search
description: 将扩充文档发送到 Azure 存储，随后可以在 Azure 认知搜索和其他应用中查看、整形和使用扩充文档。
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/10/2021
ms.openlocfilehash: baa4a78e23b83fa7c138e71b92dba12ba23a3ab6
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121736314"
---
# <a name="knowledge-store-in-azure-cognitive-search"></a>Azure 认知搜索中的知识存储

知识存储是 Azure 认知搜索的一项功能，它可以将 [AI 扩充管道](cognitive-search-concept-intro.md)的输出保存到 Azure 存储中，供独立分析或进行下游处理。 扩充文档是管道的输出，是基于使用 AI 流程提取、结构化和分析的内容创建的。 在标准的 AI 管道中，扩充文档是临时的，仅在编制索引期间使用，然后被丢弃。 创建知识存储可以保留扩充文档，以供检查或其他知识挖掘方案使用。

如果你过去曾经用过认知技能，则已经知道技能集可以通过一系列扩充来移动文档。 结果可以是搜索索引，也可以是知识存储中的投影。 两个输出（搜索索引和知识存储）是相同管道的产品；派生自相同输入，但会生成在不同应用程序中进行结构化、存储和使用的输出。

:::image type="content" source="media/knowledge-store-concept-intro/knowledge-store-concept-intro.svg" alt-text="管道与技能组" border="false":::

<!-- previous version of the architecture diagram
:::image type="content" source="media/knowledge-store-concept-intro/annotationstore_sans_internalcache.png" alt-text="Knowledge store in pipeline diagram" border="false"::: -->

在物理上，知识存储是一个 [Azure 存储](../storage/common/storage-account-overview.md)，可以是 Azure 表存储和/或 Azure Blob 存储。 任何可以连接到 Azure 存储的工具或进程都可以使用知识存储的内容。

通过存储资源管理器查看，知识存储只是表、对象或文件的一个集合。 下面的示例显示由三个表组成的一个知识存储，其中的字段要么是从数据源沿用的，要么是通过扩充创建的（请查看“sentiment score”和“translated_text”）。

:::image type="content" source="media/knowledge-store-concept-intro/kstore-in-storage-explorer.png" alt-text="从扩充树中进行读取和写入的技能" border="true":::

## <a name="benefits-of-knowledge-store"></a>知识存储的优势

知识存储提供结构、上下文和实际内容 - 这些内容收集自非结构化和半结构化数据文件（例如 Blob）、已经过分析的图像文件甚至结构化数据，并整形为新的形式。 在[分步演练](knowledge-store-create-rest.md)中可以获得有关密集的 JSON 文档如何分区成子结构、如何重新构造成新结构，以及如何可用于下游过程（例如机器学习和数据科学工作负荷）的第一手资料。

尽管了解 AI 扩充管道可以生成哪些内容会很有用，但知识存储的真正潜力是能够整形数据。 你可以从基本技能集入手，然后循环访问它以添加越来越多的结构级别，这样就能将它们合并成新结构，可用于除 Azure 认知搜索以外的其他应用。

知识存储的优势已枚举如下：

+ 在除搜索以外的[分析和报表工具](#tools-and-apps)中使用扩充文档。 包含 Power Query 的 Power BI 就是个极具吸引力的选择，但只要是能连接到 Azure 存储，任何工具或应用都可以从你创建的知识存储中提取文档。

+ 优化 AI 索引管道，同时调试步骤和技能集定义。 知识存储展示 AI 索引管道中的技能集定义的结果。 这些结果可用于设计更好的技能集，因为你可以清楚地看到扩充是什么样的。 可以使用 Azure 存储中的[存储资源管理器](../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows)来查看知识存储的内容。

+ 将数据整形到新表单中。 重塑在技能组中进行编码，但 Azure 认知搜索的[整形程序技能](cognitive-search-skill-shaper.md)提供了显式控制以及创建多个形状的功能。 通过整形，可以定义与数据预期用途保持一致的投影，同时保留关系。

> [!VIDEO https://www.youtube.com/embed/XWzLBP8iWqg?version=3]

## <a name="knowledge-store-definition"></a>知识存储定义

在索引器执行期间，使用 Azure 表存储和/或 Azure Blob 存储在 [Azure 存储帐户](../storage/common/storage-account-overview.md)中创建知识存储。 

Azure 存储中的数据结构是通过技能组中 `knowledgeStore` 定义的 `projections` 元素指定的。 [投影](knowledge-store-projection-overview.md)可以表述为表、对象或文件，并且你可以使用多个组（或投影组）为不同目的创建多个数据结构。

```json
"knowledgeStore":{
   "storageConnectionString":"<YOUR-AZURE-STORAGE-ACCOUNT-CONNECTION-STRING>",
   "projections":[
      {
         "tables":[ ],
         "objects":[ ],
         "files":[ ]
      }
   }
```

在此结构中指定的投影类型确定了知识存储使用的存储类型。

+ `tables` 将扩充内容投影到表存储中。 如果需要使用表格式报告结构作为分析工具的输入或者要以数据帧的形式导出到其他数据存储，请定义表投影。 可指定同一投影组中的多个 `tables` 以获取扩充文档的子集或交叉部分。 在同一投影组内，将会保留表关系，以便可以使用所有表。

+ `objects` 将 JSON 文档投影到 Blob 存储中。 `object` 的物理表示形式是一个表示扩充文档的分层 JSON 结构。

+ `files` 将图像文件投影到 Blob 存储中。 `file` 是从文档中提取的图像，它按原样传输到 Blob 存储。

## <a name="create-a-knowledge-store"></a>创建知识存储

若要创建知识存储，请使用门户或 API。 需要 [Azure 存储](../storage/index.yml)、一个[技能组](cognitive-search-working-with-skillsets.md)和一个[索引器](search-indexer-overview.md)。 由于索引器需要搜索索引，因此还需要提供索引定义。

### <a name="azure-portal"></a>[**Azure 门户**](#tab/kstore-portal)

使用“导入数据”向导，[通过四个步骤创建第一个知识存储](knowledge-store-connect-power-bi.md)。 本向导将引导你完成以下任务：

1. 选择支持的数据源以提供原始内容。

1. 指定扩充：附加认知服务资源，选择技能并指定知识存储。 在这一步，需要选择一个 Azure 存储帐户，并选择是创建对象、表还是同时创建这两者。

1. 创建索引架构。 向导需要该架构，并可以推断一个架构。

1. 运行向导。 提取、扩充和存储操作在此最后一个步骤中发生。

使用向导时，系统会在内部处理几个附加任务，否则你必须在代码中处理这些任务。 同时为你创建了整形和投影（Azure 存储中物理数据结构的定义）。 手动创建知识存储时，你的代码将需要涵盖这些步骤。

### <a name="rest"></a>[**REST**](#tab/kstore-rest)

REST API 版本 `2020-06-30` 可用于通过添加技能组来创建知识存储。

+ [创建技能组 (api-version=2020-06-30)](/rest/api/searchservice/create-skillset)
+ [更新技能组 (api-version=2020-06-30)](/rest/api/searchservice/update-skillset)

`knowledgeStore` 是在[技能集](cognitive-search-working-with-skillsets.md)中定义的，后者又由[索引器](search-indexer-overview.md)调用。 在扩充期间，Azure 认知搜索会在 Azure 存储帐户中创建一个空间，并根据配置将扩充文档投影到 Blob 或表。

你的代码将需要处理以下任务：

+ 指定要在 Azure 存储中生成的投影（表、对象、文件）
+ 在技能组中包含整形程序技能以确定投影的架构和内容
+ 将命名形状分配给投影

一种简单的探索方法是[使用 Postman 创建第一个知识存储](knowledge-store-create-rest.md)。

### <a name="net-sdk"></a>[**.NET SDK**](#tab/kstore-dotnet)

对于 .NET 开发人员，请使用 Azure.Search.Documents 客户端库中的 [KnowledgeStore 类](/dotnet/api/azure.search.documents.indexes.models.knowledgestore)。

---

<a name="tools-and-apps"></a>

## <a name="connect-with-apps"></a>与应用连接

只要扩充存在于存储中，连接到 Azure Blob 存储或表存储的任何工具或技术就都可用于浏览、分析或使用内容。 请从以下列表入手：

+ [存储资源管理器](knowledge-store-view-storage-explorer.md)：用于查看扩充文档的结构和内容。 将这视为用于查看知识存储内容的基线工具。

+ 用于报告和分析的 [Power BI](knowledge-store-connect-power-bi.md)。 

+ [Azure 数据工厂](../data-factory/index.yml)：用于进一步操作。

## <a name="content-lifecycle"></a>内容生命周期

每次运行索引器和技能组时，如果技能组或基础源数据已更改，知识存储就会更新。 索引器选取的任何更改都将通过扩充过程传播到知识存储中的投影，从而确保投影数据是原始数据源中内容的当前表示形式。 

> [!Note]
> 虽然可以编辑投影中的数据，但如果源数据中的文档已更新，则任何编辑都会在下一次管道调用时被覆盖。 

### <a name="changes-in-source-data"></a>源数据中的更改

对于支持更改跟踪的数据源，索引器将处理新文档和更改的文档，并绕过已处理的现有文档。 时间戳信息因数据源而异，但在 Blob 容器中，索引器将查看 `lastmodified` 日期以确定哪些 Blob 需要引入。

### <a name="changes-to-a-skillset"></a>对技能组的更改

如果要对技能组进行更改，你应[允许缓存扩充文档](cognitive-search-incremental-indexing-conceptual.md)，以便尽可能重用现有的扩充。

如果不使用增量缓存，索引器将始终按高使用标记的顺序处理文档，而不是向后进行处理。 对于 Blob，索引器将按 `lastModified` 排序处理 Blob，而不考虑索引器设置或技能组的任何更改。 如果你更改了技能组，系统不会更新以前处理的文档来反映新的技能组。 技能组更改后处理的文档将使用新的技能组，因此索引文档混合使用新旧技能集。

如果使用增量缓存，在技能组更新后，索引器将重用不受技能组更改影响的任何扩充。 上游扩充是从缓存中拉取的，任何独立且不受技能更改影响的扩充也是如此。

### <a name="deletions"></a>删除

尽管索引器在 Azure 存储中创建和更新结构和内容，但不会将它们删除。 即使删除了索引器或技能组，投影仍然存在。 作为存储帐户的所有者，如果不再需要投影，应将其删除。 

## <a name="next-steps"></a>后续步骤

知识存储提供扩充文档的持久性，在设计技能集，或者在创建新的结构和内容供可访问 Azure 存储帐户的任何客户端应用程序使用时，知识存储非常有用。

创建扩充文档的最简单方法是[使用门户](knowledge-store-create-portal.md)，但也可使用 Postman 和 REST API，这两个工具对于深入了解如何以编程方式创建和引用对象非常有用。

> [!div class="nextstepaction"]
> [使用 Postman 和 REST 创建知识存储](knowledge-store-create-rest.md)

或者，深入了解[投影](knowledge-store-projection-overview.md)。 若要演练一个演示高级投影概念（如切片、内联整形和关系）的示例，请从[在知识存储中定义投影](knowledge-store-projections-examples.md)开始。
