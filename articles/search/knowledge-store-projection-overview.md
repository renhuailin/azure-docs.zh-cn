---
title: 投影概念
titleSuffix: Azure Cognitive Search
description: 将 AI 扩充索引编制管道中的扩充数据保存并整型到知识存储中，以便在除了全文搜索以外的方案中使用。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/08/2021
ms.openlocfilehash: 841cd106f1c54e1c35d3b2785eb942842d77f825
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2021
ms.locfileid: "129706768"
---
# <a name="knowledge-store-projections-in-azure-cognitive-search"></a>Azure 认知搜索中的知识存储“投影”

投影是[知识存储](knowledge-store-concept-intro.md)定义元素，用于指定数据在 Azure 存储中的物理表示。 投影定义确定数据结构在 Azure 存储中的数量和类型。

## <a name="types-of-data-structures"></a>数据结构类型

知识存储是一种逻辑构造，其在 Azure 存储中以物理形式表示为表、JSON 对象或二进制映像文件。

| 投影 | 存储 | 使用情况 |
|------------|---------|-------|
| 表 | Azure 表存储 | 用于行和列为最好表示形式的数据。 使用表投影可以定义架构化的形状或投影。 只能将有效的 JSON 对象投影为表。 由于扩充文档可以包含不是已命名 JSON 对象的节点，因此将添加[“整形程序”技能或在技能内使用内联整形](knowledge-store-projection-shape.md)来创建有效的 JSON。 |
| 对象 | Azure Blob 存储 | 需要 JSON 表示形式的数据和扩充内容时使用。 与表投影一样，只能将有效的 JSON 对象投影为对象，并且可借助整形实现此目的。 |
| 文件 | Azure Blob 存储 | 需要保存规范化的二进制映像文件时使用。 |

可以在扩充数据时定义数据的多个投影。 希望以不同的方式为各种用例塑造相同的数据时，多个投影非常有用。

## <a name="basic-definition"></a>基本定义

投影是[技能集对象](/rest/api/searchservice/create-skillset)中 `knowledgeStore` 定义下的复杂集合数组。 

每组表、对象和文件都是一个项目组，如果存储要求中要求支持不同的工具和方案，则可以有多个组。 在单个组内，可以有多个表、对象和文件。 

通常只使用一个组，但以下示例演示了两个组以强化多个组的理念。

```json
"knowledgeStore" : {
    "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
    "projections": [
        {
            "tables": [],
            "objects": [],
            "files": []
        }, 
        {
            "tables": [],
            "objects": [],
            "files": []
        }
    ]
}
```

## <a name="data-isolation-and-relatedness"></a>数据隔离和相关性

具有多种表对象文件组合有助于支持不同方案。 可使用一组来设计和调试技能组，并捕获输出以供进一步检查，使用另一组收集用于联机应用的输出，使用第三组用于数据科学工作负载。

投影组具有以下重要的互斥性和相关性特征。 

| 原则 | 说明 |
|-----------|-------------|
| 互斥性 | 投影到单个组的所有内容独立于投影到其他投影组的数据。 此独立性意味着，能够以不同的方式塑造相同的数据，不过需要在每个投影组中重复该操作。 每个组都从同一个源（扩充树）获取数据，同时完全独立于任何其他投影组的表对象文件组合。|
| 相关性 | 投影支持组中的相关性。 在组中，表中的内容与对象或文件中的内容相关。 在不同的表和对象中投影某个扩充树（例如 `/document/translated_text`）的单个节点时，对于同一组中的不同类型（表、对象和文件），相应的关系将保留不变。 在表中，关系基于生成的键，每个子节点保留对父节点的引用。 例如，假设有一个文档包含图像和文本。 你可以将文本投影到表或对象，将图像投影到文件，但文件中的表或对象必须具有包含文件 URL 的列/属性。|

是否创建更多投影组通常取决于对数据表示的要求。 如果需要不同的数据关系，则可以这样做。 在某个集内，数据是相关的，前提是这些关系存在并且可以检测到它们。 如果创建更多的集，每个组中的文档将永远不相关。 例如，如果你希望将相同的投影数据用于联机系统，而这些数据需要以特定的方式表示，同时，你还希望将相同的投影数据用于以不同方式表示的数据科学管道，则可以使用多个投影组。

<!-- ## Knowledge Store composition

The knowledge store consists of an annotation cache and projections. The *cache* is used by the service internally to cache the results from skills and track changes. A *projection* defines the schema and structure of the enrichments that match your intended use.

Within Azure Storage, projections can be articulated as tables, objects, or files.

+ As an object, the projection maps to Blob storage, where the projection is saved to a container, within which are the objects or hierarchical representations in JSON for scenarios like a data science pipeline.

+ As a table, the projection maps to Table storage. A tabular representation preserves relationships for scenarios like data analysis or export as data frames for machine learning. The enriched projections can then be easily imported into other data stores. 

You can create multiple projections in a knowledge store to accommodate various constituencies in your organization. A developer might need access to the full JSON representation of an enriched document, while data scientists or analysts might want granular or modular data structures shaped by your skillset.

For instance, if one of the goals of the enrichment process is to also create a dataset used to train a model, projecting the data into the object store would be one way to use the data in your data science pipelines. Alternatively, if you want to create a quick Power BI dashboard based on the enriched documents the tabular projection would work well. -->

## <a name="table-projections"></a>表投影

建议对需要数据浏览的方案使用表投影，如使用 Power BI 进行分析时。 表定义是要投影的表列表。 每个表需要三个属性：

+ tableName：Azure 表存储中的表名称。

+ generatedKeyName：用于唯一标识此行的键的列名。

+ source：扩充树中用作扩充来源的节点。 此节点通常是定义表形状的整形程序技能的输出。 表具有行和列，整形是指定行和列的机制。 可以使用[“整形程序”技能或内联形状](knowledge-store-projection-shape.md)。 “整形程序”技能生成有效的 JSON，但可以是任何技能的输出，只要是有效 JSON。 

如此示例中所示，关键短语和实体已建模到不同的表中，将包含对每行的父级 (MainTable) 的反向引用。 

```json
"knowledgeStore": {
  "storageConnectionString": "an Azure storage connection string",
  "projections" : [
    {
      "tables": [
        { "tableName": "MainTable", "generatedKeyName": "SomeId", "source": "/document/EnrichedShape" },
        { "tableName": "KeyPhrases", "generatedKeyName": "KeyPhraseId", "source": "/document/EnrichedShape/*/KeyPhrases/*" },
        { "tableName": "Entities", "generatedKeyName": "EntityId", "source": "/document/EnrichedShape/*/Entities/*" }
      ]
    },
    {
      "objects": [ ]
    },
    {
      "files": [ ]
    }
  ]
}
```

“源”中指定的扩充节点可进行切分以投影到多个表中。 对“EnrichedShape”的引用是整形程序技能的输出（不显示）。 技能的输入确定表组合和填充表的行。 在每个表中生成的键和公共字段为表关系提供基础。

## <a name="object-projections"></a>对象投影

对象投影是可以从任何节点寻源的扩充树的 JSON 表示形式。 在许多情况下，可以使用用于创建表投影的同一个“整形程序”技能来生成对象投影。 

生成对象投影需要几个特定于对象的属性：

+ storageContainer：对象将保存到的 blob 容器

+ source：扩充树中充当投影根的节点的路径

```json
"knowledgeStore": {
  "storageConnectionString": "an Azure storage connection string",
  "projections" : [
    {
      "tables": [ ]
    },
    {
      "objects": [
        {
          "storageContainer": "hotelreviews", 
          "source": "/document/hotel"
        }
      ]
    },
    {
        "files": [ ]
    }
  ]
}
```

## <a name="file-projections"></a>文件投影

文件投影仅作用于 `normalized_images` 集合，但在其他方面类似于对象投影，因为它们保存在 blob 容器中，并具有文档 ID 的 base64 编码值的文件夹前缀。 

文件投影不能与对象投影共享同一个容器，需要将其投影到不同的容器。 文件投影与对象投影具有相同的属性：

+ storageContainer：对象将保存到的 blob 容器

+ source：扩充树中充当投影根的节点的路径

```json
"knowledgeStore": {
  "storageConnectionString": "an Azure storage connection string",
  "projections" : [
    {
      "tables": [ ]
    },
    {
      "objects": [ ]
    },
    {
        "files": [
              {
              "storageContainer": "ReviewImages",
              "source": "/document/normalized_images/*"
            }
        ]
    }
  ]
}
```

## <a name="projection-shaping"></a>投影整形

如果扩充树中存在与投影架构匹配的对象（无论是表还是对象），则可以更轻松地定义投影。 基于使用计划对数据进行整形或构建结构通常通过向技能组添加[整形程序技能](cognitive-search-skill-shaper.md)来实现。 从整形程序技能生成的形状充当投影的 `source`，但也可以充当其他技能的输入。

简而言之，对于表投影，整形程序技能确定表中的列或字段。 整形程序技能的输入由扩充树中的节点构成。 输出是在表投影中指定的结构。 在下面的示例中，名为“mytableprojection”的表投影将由整形程序技能指定的输入构成。

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "name": "ShaperForTables",
    "description": null,
    "context": "/document",
    "inputs": [
        {
            "name": "metadata_storage_path",
            "source": "/document/metadata_storage_path",
            "sourceContext": null,
            "inputs": []
        },
        {
          "name": "reviews_text",
          "source": "/document/reviews_text"
        }, 
        {
          "name": "reviews_title",
          "source": "/document/reviews_title"
        },
        {
          "name": "reviews_username",
          "source": "/document/reviews_username"
        },
    ],
    "outputs": [
      {
        "name": "output",
        "targetName": "mytableprojection"
      }
    ]
}
```

使用整形程序技能可以从扩充树的不同节点编写对象，并将其指定为新节点下的父级。 还可以定义包含嵌套对象的复杂类型。 有关示例，请参阅[整形程序技能](cognitive-search-skill-shaper.md)文档。

## <a name="projection-slicing"></a>投影切片

在表投影组内，可以将扩充树中的单个节点切分为多个相关表，以便每个表都包含一类数据。 这对于分析非常有用，可控制在分析中是否以及如何聚合相关数据。

若要创建多个子表，请先创建父表，然后创建从父级源中生成的其他表。 在此示例中，“KeyPhrases”和“Entities”采用“/document/EnrichedShape”切片。

```json
"tables": [
  { "tableName": "MainTable", "generatedKeyName": "SomeId", "source": "/document/EnrichedShape" },
  { "tableName": "KeyPhrases", "generatedKeyName": "KeyPhraseId", "source": "/document/EnrichedShape/*/KeyPhrases/*" },
  { "tableName": "Entities", "generatedKeyName": "EntityId", "source": "/document/EnrichedShape/*/Entities/*" }
]
```

投影到多个表时，除非子节点是同一个组中其他表的源，否则整个形状将投影到每个表。 添加源路径为现有投影的子级的投影将导致子节点从父节点中分割出来，并投影到新的但相关的表或对象中。 利用此方法，可以使用整形程序技能定义单个节点，该节点可以作为所有投影的源。

## <a name="projection-lifecycle"></a>投影生命周期

投影具有一个与数据源中源数据绑定的生命周期。 更新源数据并重建其索引时，将使用扩充的结果更新投影，以确保投影最终与数据源中的数据保持一致。 不过，投影在 Azure 存储中也是独立存储的。 删除索引器或搜索服务本身时不会删除投影。 

## <a name="using-projections"></a>使用投影

运行索引器后，可以在通过投影指定的容器或表中读取投影的数据。

分析时，可以方便地在 Power BI 中浏览数据，只需将 Azure 表存储设置为数据源即可。 可以利用数据内部的关系，轻松地基于数据创建一组可视化效果。

另外，如果你需要在数据科学管道中使用扩充的数据，则可以[将数据从 blob 加载到 Pandas 数据帧](/azure/architecture/data-science-process/explore-data-blob)。

最后，如果需要从知识存储导出数据，可以使用 Azure 数据工厂提供的连接器来导出数据，然后将其载入所选的数据库。 

## <a name="next-steps"></a>后续步骤

接下来，请使用示例数据遵照说明创建第一个知识存储。

> [!div class="nextstepaction"]
> [在 REST 中创建知识存储](knowledge-store-create-rest.md)。

有关更高级的概念（如切分、内联整形和关系），请参阅[在知识存储中定义投影](knowledge-store-projections-examples.md)。
