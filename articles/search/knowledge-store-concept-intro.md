---
title: 知识存储概念
titleSuffix: Azure Cognitive Search
description: 将扩充文档发送到 Azure 存储，随后可以在 Azure 认知搜索和其他应用中查看、整形和使用扩充文档。
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/02/2021
ms.openlocfilehash: 1463c4e879b364f8242d55aa58b424a8cc6f9cf9
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2021
ms.locfileid: "129706802"
---
# <a name="knowledge-store-in-azure-cognitive-search"></a>Azure 认知搜索中的知识存储

“知识存储”是一种数据接收器，由认知搜索 [AI 扩充管道](cognitive-search-concept-intro.md)创建，它在 Azure 存储中的表和 Blob 容器中存储已扩充的内容，以便在非搜索场景（例如知识挖掘）中进行独立分析或下游处理。

如果你过去使用过认知技能，那么你已经知道技能组可通过扩充序列调用原子转换（如识别实体或翻译文本）来移动文档。 结果可以是搜索索引，也可以是知识存储中的投影。 这两个输出（搜索索引和知识存储）是相同管道的互斥性产品；它们派生自相同的输入，但生成的输出将在不同应用程序中进行结构化、存储和使用。

:::image type="content" source="media/knowledge-store-concept-intro/knowledge-store-concept-intro.svg" alt-text="管道与技能组" border="false":::

在物理上，知识存储是一个 [Azure 存储](../storage/common/storage-account-overview.md)，可以是 Azure 表存储和/或 Azure Blob 存储。 任何可以连接到 Azure 存储的工具或进程都可以使用知识存储的内容。

知识存储可通过存储资源管理器查看，只是表、对象或文件的一个集合。 下面的示例显示由三个表组成的一个知识存储，其中的字段要么是从数据源沿用的，要么是通过扩充创建的（请查看“sentiment score”和“translated_text”）。

:::image type="content" source="media/knowledge-store-concept-intro/kstore-in-storage-explorer.png" alt-text="从扩充树中进行读取和写入的技能" border="true":::

## <a name="benefits-of-knowledge-store"></a>知识存储的优势

知识存储的主要优点有两个方面：灵活访问内容和形状数据处理能力。

与只能通过认知搜索中的查询进行访问的搜索索引不同，知识存储可以通过支持与 Azure 存储连接的任何工具、应用或进程进行访问。 这种灵活性为使用由扩充管道生成的经过分析和扩充的内容提供了新方案。

同一技能组除了可以扩充数据，还可以用于绘制数据。 某些工具（如 Power BI）更适合与表配合使用，而数据科学工作负载可能需要采用 blob 格式的复杂数据结构。 将[整形程序技能](cognitive-search-skill-shaper.md)添加到技能组，可用于控制数据形状。 然后，可将这些形状传递到投影（表或 blob），以创建与数据使用目的一致的物理数据结构。

以下视频介绍了这两个优点以及更多内容。

> [!VIDEO https://www.youtube.com/embed/XWzLBP8iWqg?version=3]

## <a name="knowledge-store-definition"></a>知识存储定义

知识存储在技能组定义内部进行定义，其中包含两个组件： 

+ 获取 Azure 存储的连接字符串

+ 确定知识存储是否包含表、对象或文件的[投影](knowledge-store-projection-overview.md)。 

投影元素是一个数组。 一个知识存储中可以创建多组“表-对象-文件”组合。

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

+ `files` 将图像文件投影到 Blob 存储中。 `file` 是从文档中提取的图像，它按原样传输到 Blob 存储。 尽管命名为“files”，但它显示在 Blob 存储中，而不是在文件存储中。

## <a name="create-a-knowledge-store"></a>创建知识存储

若要创建知识存储，请使用门户或 API。 需要 [Azure 存储](../storage/index.yml)、一个[技能组](cognitive-search-working-with-skillsets.md)和一个[索引器](search-indexer-overview.md)。 由于索引器需要搜索索引，因此还需要提供索引定义。

使用门户方法是创建知识存储的最快路径。 或者选择 REST API，以便更深入地了解如何定义和关联对象。

### <a name="azure-portal"></a>[**Azure 门户**](#tab/kstore-portal)

使用“导入数据”向导，通过四个步骤[创建第一个知识存储](knowledge-store-create-portal.md)。

1. 定义数据源

1. 定义技能组并指定知识存储。

1. 创建索引架构。 向导需要该架构，并可以推断一个架构。

1. 运行向导。 提取、扩充和存储操作在此最后一个步骤中发生。

向导会自动执行原本需要手动处理的任务。 具体而言，就是为你创建整形和投影（Azure 存储中的物理数据结构定义）。 

### <a name="rest"></a>[**REST**](#tab/kstore-rest)

请参阅[使用 Postman 创建第一个知识库](knowledge-store-create-rest.md)，该教程将引导你了解属于此[知识库集合](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/knowledge-store)的对象和请求。

REST API 版本 `2020-06-30` 可用于通过为技能组添加内容来创建知识存储。

+ [创建技能组 (api-version=2020-06-30)](/rest/api/searchservice/create-skillset)
+ [更新技能组 (api-version=2020-06-30)](/rest/api/searchservice/update-skillset)

在技能组中：

+ 指定要在 Azure 存储中生成的投影（表、对象、文件）
+ 在技能组中包含整形程序技能以确定投影的架构和内容
+ 将命名形状分配给投影

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
> 虽然可以编辑投影中的数据，但如果源数据中的文档被更新，则任何编辑都会在下一次管道调用时被覆盖。 

### <a name="changes-in-source-data"></a>源数据中的更改

对于支持更改跟踪的数据源，索引器将处理新文档和更改的文档，并绕过已处理的现有文档。 时间戳信息因数据源而异，但在 Blob 容器中，索引器将查看 `lastmodified` 日期以确定哪些 Blob 需要引入。

### <a name="changes-to-a-skillset"></a>对技能组的更改

如果要对技能组进行更改，应[允许缓存扩充文档](cognitive-search-incremental-indexing-conceptual.md)，以便尽可能重用现有的扩充。

如果不使用增量缓存，索引器将始终按高使用标记的顺序处理文档，而不是向后进行处理。 对于 Blob，索引器将处理按 `lastModified` 排序的 Blob，而不考虑索引器设置或技能组的任何更改。 如果你更改了技能组，系统不会更新以前处理的文档来反映新的技能组。 技能组更改后处理的文档将使用新的技能组，因此索引文档混合使用新旧技能集。

如果使用增量缓存，那么在技能组更新后，索引器将重用不受技能组更改影响的任何扩充。 上游扩充是从缓存中拉取的，任何独立且不受技能更改影响的扩充也是如此。

### <a name="deletions"></a>删除

尽管索引器在 Azure 存储中创建和更新结构和内容，但不会将它们删除。 即使删除了索引器或技能组，投影仍然存在。 作为存储帐户的所有者，你应在不再需要投影时将其删除。 

## <a name="next-steps"></a>后续步骤

知识存储提供扩充文档的持久性，在设计技能集，或者在创建新的结构和内容供可访问 Azure 存储帐户的任何客户端应用程序使用时，知识存储非常有用。

创建扩充文档的最简单方法是[使用门户](knowledge-store-create-portal.md)，但也可使用 Postman 和 REST API，这两个工具对于深入了解如何以编程方式创建和引用对象更有用。

> [!div class="nextstepaction"]
> [使用 Postman 和 REST 创建知识存储](knowledge-store-create-rest.md)
