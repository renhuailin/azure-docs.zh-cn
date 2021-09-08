---
title: 创建技能集
titleSuffix: Azure Cognitive Search
description: 定义数据提取、自然语言处理或图像分析步骤，从 Azure 认知搜索使用的数据扩充和提取结构化信息。
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/15/2021
ms.openlocfilehash: 4425a4b7c29bc0f4bc237c021610087c933631d8
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2021
ms.locfileid: "123224036"
---
# <a name="create-a-skillset-in-azure-cognitive-search"></a>在 Azure 认知搜索中创建技能组

![索引器阶段](media/cognitive-search-defining-skillset/indexer-stages-skillset.png "索引器阶段")

技能组定义提取和扩充数据以使其可搜索的操作。 在文档破解之后、从源文档中提取文本和图像内容时，以及在将源文档中的任何字段选择性地映射到索引或知识存储中的目标字段中之后，技能组都会执行。

在本文中，你将了解创建技能组的步骤。 作为参考，本文使用了[创建技能组 (REST API)](/rest/api/searchservice/create-skillset)。 

技能组的一些使用规则包括：

+ 技能组是顶级资源，这意味着它可以创建一次，并由多个索引器引用。
+ 技能组必须包含至少一种技能。
+ 技能组可以重复相同类型的技能（例如，多个整形程序技能）。

请记住，索引器驱动技能组的执行，这意味着你还需要创建[索引器](search-howto-create-indexers.md)、[数据源](search-data-sources-gallery.md)和[搜索索引](search-what-is-an-index.md)，然后才能测试技能组。

> [!TIP]
> 启用[扩充缓存](cognitive-search-incremental-indexing-conceptual.md)来重复使用已处理的内容并降低开发成本。

## <a name="skillset-definition"></a>技能集定义

从基本结构开始。 在 [REST API](/rest/api/searchservice/create-skillset) 中，技能组采用 JSON 创作而成，包含以下各部分：

```json
{
   "name":"skillset-template",
   "description":"A description makes the skillset self-documenting (comments aren't allowed in JSON itself)",
   "skills":[
      
   ],
   "cognitiveServices":{
      "@odata.type":"#Microsoft.Azure.Search.CognitiveServicesByKey",
      "description":"A Cognitive Services resource in the same region as Azure Cognitive Search",
      "key":"<Your-Cognitive-Services-Multiservice-Key>"
   },
   "knowledgeStore":{
      "storageConnectionString":"<Your-Azure-Storage-Connection-String>",
      "projections":[
         {
            "tables":[ ],
            "objects":[ ],
            "files":[ ]
         }
      ]
    },
    "encryptionKey":{ }
}
```

在 name 和 description 之后，技能组具有四个主要属性：

+ `skills` 数组，一个无序的[技能集合](cognitive-search-predefined-skills.md)，搜索服务根据每项技能所需的输入来确定这些技能的执行顺序。 如果技能各自独立，它们将并行执行。 技能可以是实用技能（比如拆分文本）、转换技能（基于认知服务的 AI）或者你提供的自定义技能。 下一节中提供了技能数组的示例。

+ `cognitiveServices`，用于调用认知服务 API 的[计费技能](cognitive-search-predefined-skills.md)。 如果未使用计费技能或自定义实体查找，请删除此部分。 否则，请[附加资源](cognitive-search-attach-cognitive-services.md)。

+ `knowledgeStore`（可选），指定 Azure 存储帐户以及用于将技能组输出投影到 Azure 存储中的表、Blob 和文件的设置。 如果不需要，请删除此部分；如果需要，[指定一个知识存储](knowledge-store-create-rest.md)。

+ `encryptionKey`（可选），指定一个 Azure 密钥保管库和用于对技能组定义中的敏感内容进行加密的[客户管理的密钥](search-security-manage-encryption-keys.md)。 如果不使用客户管理的加密，请删除此属性。

## <a name="add-a-skills-array"></a>添加技能数组

在技能组定义中，技能数组指定要执行的技能。 下面的示例通过展示两个不相关的[内置技能](cognitive-search-predefined-skills.md)向你介绍它的构成。 请注意，每项技能都有一个 type、context、inputs 和 outputs。 

```json
"skills":[
  {
    "@odata.type": "#Microsoft.Skills.Text.V3.EntityRecognitionSkill",
    "context": "/document",
    "categories": [ "Organization" ],
    "defaultLanguageCode": "en",
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "organizations",
        "targetName": "orgs"
      }
    ]
  },
  {
    "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
    "context": "/document",
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "score",
        "targetName": "mySentiment"
      }
    ]
  }
]
```

> [!NOTE]
> 你可以构建带循环和分支的复杂技能组，并使用[条件技能](cognitive-search-skill-conditional.md)来创建表达式。 语法基于 [JSON 指针](https://tools.ietf.org/html/rfc6901)路径表示法，做了少量的修改来标识扩充树中的节点。 `"/"` 遍历树中的较低级别，`"*"` 充当上下文中的 for-each 运算符。 本文中的许多示例阐释了此语法。 

### <a name="how-built-in-skills-are-structured"></a>内置技能的构造方式

每项技能在其输入值和所采用的参数方面都是唯一的。 [有关每项技能的文档](cognitive-search-predefined-skills.md)描述了给定技能的所有参数和属性。 尽管存在差异，但大多数技能共享一个通用集，且具有相似的模式。 [实体识别技能](cognitive-search-skill-entity-recognition-v3.md)提供了一个示例，可以说明几个要点：

```json
{
  "@odata.type": "#Microsoft.Skills.Text.V3.EntityRecognitionSkill",
  "context": "/document",
  "categories": [ "Organization" ],
  "defaultLanguageCode": "en",
  "inputs": [
    {
      "name": "text",
      "source": "/document/content"
    }
  ],
  "outputs": [
    {
      "name": "organizations",
      "targetName": "orgs"
    }
  ]
}
```

常见参数包括“odata.type”、“inputs”和“outputs”。 其他参数（即“categories”和“defaultLanguageCode”）是特定于实体识别的参数示例。 

+ “odata.type”唯一地标识每项技能。 可以在[技能引用文档](cognitive-search-predefined-skills.md)中找到该类型。

+ “context”是扩充树的一个节点，它代表了操作发生的级别。 所有技能都具有此属性。 如果未显式设置“context”字段，则默认上下文为 `"/document"`。 在示例中，上下文是整个文档，这意味着，每个文档调用实体识别技能一次。

  上下文还决定了在扩充树中生成输出的位置。 在此示例中，该技能返回一个名为 `"organizations"` 的属性，捕获为 `orgs`，该属性作为 `"/document"` 的子节点添加。 在下游技能中，此新创建的扩充节点的路径为 `"/document/orgs"`。 对于特定的文档，`"/document/orgs"` 的值是从文本提取的组织数组（例如 `["Microsoft", "LinkedIn"]`）。 有关路径语法的详细信息，请参阅[在技能组中引用注释](cognitive-search-concept-annotations-syntax.md)。

+ “inputs”指定传入数据的源及其使用方式。 对于实体识别，其中一个输入是 `"text"`，它是要为实体分析的内容。 此内容源于扩充树中的 `"/document/content"` 节点。 在扩充树中，`"/document"` 是根节点。 对于使用 Azure Blob 索引器检索的文档，每个文档的 `content` 字段是索引器创建的标准字段。 

+ “outputs”表示技能的输出。 每项技能旨在发出特定类型的输出，在技能组中按名称引用这些输出。 对于实体识别，`"organizations"` 是其支持的输出之一。 有关每项技能的文档描述了它可以生成的输出。

输出只会在处理期间存在。 若要将此输出链接到下游技能的输入，请以 `"/document/orgs"` 的形式引用输出。 若要将输出发送到搜索索引中的字段，请在索引器中[创建一个输出字段映射](cognitive-search-output-field-mapping.md)。 若要将输出发送到知识库，请[创建投影](knowledge-store-projection-overview.md)。

一种技能的输出可能与另一种技能的输出发生冲突。 如果有多项技能返回相同的输出，请使用 `"targetName"` 在扩充节点路径中进行名称消歧。

在某些情况下，需要单独引用数组的每个元素。 例如，假设你想要将 `"/document/orgs"` 的每个元素单独传递到另一个技能。 为此，请向此路径添加星号：`"/document/orgs/*"` 

第二个情绪分析技能遵循与第一个扩充器相同的模式。 它采用 `"/document/content"` 作为输入，并返回每个内容实例的情绪评分。 由于未显式设置“context”字段，所以输出 (mySentiment) 现在是 `"/document"` 的子级。

```json
{
  "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
  "inputs": [
    {
      "name": "text",
      "source": "/document/content"
    }
  ],
  "outputs": [
    {
      "name": "score",
      "targetName": "mySentiment"
    }
  ]
}
```

## <a name="adding-a-custom-skill"></a>添加自定义技能

下面是[自定义技能](cognitive-search-custom-skill-web-api.md)的一个示例。 URI 指向一个 Azure 函数，该函数反过来调用你提供的模型或转换。 有关详细信息，请参阅[定义自定义接口](cognitive-search-custom-skill-interface.md)。

尽管自定义技能正在执行管道外部的代码，但在技能数组中，这只是另一种技能。 与内置技能类似，它具有一个 type、context、inputs 和 outputs。 像内置技能一样，它也在扩充树中进行读取和写入。 请注意，“上下文”字段设置为包含星号的 `"/document/orgs/*"`，这意味着，将对 `"/document/orgs"` 下的每个组织调用扩充步骤。

将为识别到的每个组织生成输出（在本例中为公司说明）。 引用下游步骤中的说明时（例如，在关键短语提取中），应该使用路径 `"/document/orgs/*/companyDescription"` 执行此操作。 

```json
{
  "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
  "description": "This skill calls an Azure function, which in turn calls custom code",
  "uri": "https://indexer-e2e-webskill.azurewebsites.net/api/InvokeCode?code=foo",
  "httpHeaders": {
      "Ocp-Apim-Subscription-Key": "foobar"
  },
  "context": "/document/orgs/*",
  "inputs": [
    {
      "name": "query",
      "source": "/document/orgs/*"
    }
  ],
  "outputs": [
    {
      "name": "description",
      "targetName": "companyDescription"
    }
  ]
}
```

## <a name="send-output-to-an-index"></a>将输出发送到索引

每项技能执行时，将其输出作为节点添加到文档的扩充树中。 扩充的文档在管道中作为临时数据结构存在。 若要创建永久的数据结构，并全面了解技能的实际生成内容，需要将输出发送到搜索索引或[知识存储](knowledge-store-concept-intro.md)。

在技能组评估的早期阶段，需要以最少的工作量来检查初步结果。 建议使用搜索索引，因为其设置更为简单。 对于每个技能输出，在索引器中[定义一个输出字段映射](cognitive-search-output-field-mapping.md)，并在索引中定义一个字段。

:::image type="content" source="media/cognitive-search-defining-skillset/skillset-indexer-index-combo.png" alt-text="显示在技能输出、索引器字段映射和索引字段中定义人员实体的方式的对象图。":::

运行索引器后，可以使用[搜索资源管理器](search-explorer.md)从索引返回文档，并检查每个字段的内容，以确定技能组检测到或创建的内容。

以下示例显示了实体识别技能的结果，该技能在文本块中检测到人员、位置、组织和其他实体。 在搜索资源管理器中查看结果可以帮助你确定技能是否可为你的解决方案增加价值。

:::image type="content" source="media/cognitive-search-defining-skillset/doc-in-search-explorer.png" alt-text="搜索资源管理器中文档的屏幕截图。":::

## <a name="tips-for-a-first-skillset"></a>有关第一个技能组的技巧

+ 组装具有代表性的 Blob 存储或其他受支持的索引器数据源内容示例，并运行“导入数据”向导来创建技能组、索引、索引器和数据源对象。 

  该向导自动执行第一次可能不易执行的一些步骤，包括定义索引中的字段、在索引器中定义输出字段映射，以及知识存储（如果使用）中的投影。 对于某些技能（例如 OCR 或图像分析），向导将添加实用工具技能，用于合并在文档破解期间分隔的图像和文本内容。

+ 或者，可以导入技能 Postman 集合，这些集合提供评估技能所需的所有对象定义的完整示例，从技能集到可通过查询来查看转换结果的索引。

## <a name="next-steps"></a>后续步骤

上下文和输入源字段是扩充树中节点的路径。 下一步，请详细了解用于设置扩充树中节点的路径的语法。

> [!div class="nextstepaction"]
> [在技能组中引用注释](cognitive-search-concept-annotations-syntax.md)
