---
title: 使用 REST 创建知识存储
titleSuffix: Azure Cognitive Search
description: 使用 REST API 和 Postman 来创建 Azure 认知搜索知识存储，用于存留技能组的 AI 扩充内容。
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 09/02/2021
ms.openlocfilehash: a19fd4aad4ee8e5bac7dc7cde2a5be4609a346fc
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124796564"
---
# <a name="create-a-knowledge-store-using-rest-and-postman"></a>使用 REST 和 Postman 创建知识库

知识存储是 Azure 认知搜索的一项功能，它可以将技能组输出从 [AI 扩充管道](cognitive-search-concept-intro.md)发送到 Azure 存储，供后续的知识挖掘、数据分析或进行下游处理。 填充知识存储后，可以使用[存储资源管理器](knowledge-store-view-storage-explorer.md)或 [Power BI](knowledge-store-connect-power-bi.md) 等工具来浏览内容。

在本文中，你将使用 REST API 在 Azure 存储的知识存储中引入、扩充和了解一组关于酒店住宿的客户评论。 最终结果是一个知识存储，包含从源提取的原始文本内容，以及 AI 生成的内容，包括情绪评分、关键短语提取、语言检测和非英语客户评论的文本翻译。

为了获得初始数据集，首先要将这些酒店评论导入到 Azure Blob 存储中。 处理完以后，结果将作为知识存储保存在 Azure 表存储中。

> [!NOTE]
> 本文假设使用的是 [Postman 桌面应用程序](https://www.getpostman.com/)。 此外，本文中的[源代码](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/knowledge-store)包含涵盖所有请求的 Postman 集合。 

## <a name="create-services-and-load-data"></a>创建服务并加载数据

此练习使用 Azure 认知搜索、Azure Blob 存储和适用于 AI 的 [Azure 认知服务](https://azure.microsoft.com/services/cognitive-services/)。 

由于工作负载很小，因此，认知服务在幕后会抽调一部分算力来免费处理事务（每天最多 20 个）。 负载很小意味着可以跳过创建或附加认知服务资源的步骤。

1. [下载 HotelReviews_Free.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?sp=r&st=2019-11-04T01:23:53Z&se=2025-11-04T16:00:00Z&spr=https&sv=2019-02-02&sr=b&sig=siQgWOnI%2FDamhwOgxmj11qwBqqtKMaztQKFNqWx00AY%3D)。 此数据是保存在某个 CSV 文件中的酒店评论数据（源自 Kaggle.com），其中包含客户对一家酒店的 19 条反馈。 

1. [创建 Azure 存储帐户](../storage/common/storage-account-create.md?tabs=azure-portal)或[查找现有帐户](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/)。 需要使用 Azure 存储来存储要导入的原始内容，并使用知识存储来存储最终结果。

   选择“StorageV2 (常规用途 V2)”帐户类型。

1. 在 Azure 存储资源中，使用存储资源管理器创建一个名为 hotel-reviews 的 blob 容器 。

1. 选择页面顶部的“上传”，以加载从上一步骤下载的 HotelReviews-Free.csv 文件 。

   :::image type="content" source="media/knowledge-store-create-portal/blob-container-storage-explorer.png" alt-text="包含上传的文件和左侧导航窗格的存储资源管理器屏幕截图" border="true":::

1. 你几乎完成了对此资源的操作，但在退出这些页面之前，请选择左侧导航窗格中的“访问密钥”以获取连接字符串，以便可以使用索引器检索此数据。

1. 在“访问密钥”中，选择页面顶部的“显示密钥”以取消隐藏连接字符串，然后复制 key1 或 key2 的连接字符串 。

   连接字符串具有以下格式：`DefaultEndpointsProtocol=https;AccountName=<YOUR-ACCOUNT-NAME>;AccountKey=<YOUR-ACCOUNT-KEY>;EndpointSuffix=core.windows.net`

## <a name="configure-requests"></a>配置请求

1. 从 GitHub 下载 [azure-search-postman-samples](https://github.com/Azure-Samples/azure-search-postman-samples) 并解压缩文件。 存储库中有多个集合。 你将使用 knowledge-store 文件夹中的集合。

1. 在 Postman 中，选择“文件” > “导入”以导入 KnowledgeStore.postman_collection.json 文件 。

1. 选择“集合”选项卡，然后选择“...”（省略号）按钮。

1. 选择“编辑”。

   ![显示导航栏的 Postman 应用](media/knowledge-store-create-rest/postman-edit-menu.png "转到 Postman 中的“编辑”菜单")

1. 在“编辑”对话框中，选择“变量”选项卡。 

在“变量”选项卡上，可以添加 Postman 每次在遇到双大括号中的值时要替换成的值。 例如，Postman 会将符号 `{{admin-key}}` 替换成为 `admin-key` 设置的当前值。 Postman 将在 URL、标头和请求正文等内容中进行这种替换。 

为 Azure 服务、服务连接和对象名称定义变量。 将服务和连接占位符值替换为搜索服务和存储帐户的实际值。 可以在 Azure 门户中找到这些值。

+ 若要获取 `search-service-name` 和 `search-service-admin-key` 的值，请转到门户中的 Azure 认知搜索服务，并从“概述”和“密钥”页复制值 。

+ 若要获取 `storage-account-name` 和 `storage-account-connection-string` 的值，请查看“访问密钥”页。

![Postman 应用变量选项卡](media/knowledge-store-create-rest/postman-variables-window.png "Postman 的变量窗口")

| 变量    | 从何处获取 |
|-------------|-----------------|
| `admin-key` | 在 Azure 认知搜索服务的“密钥”页上。  |
| `api-version` | 保留为“2020-06-30”。 |
| `datasource-name` | 保留为“hotel-reviews-ds”。 | 
| `indexer-name` | 保留为“hotel-reviews-ixr”。 | 
| `index-name` | 保留为“hotel-reviews-ix”。 | 
| `search-service-name` | Azure 认知搜索服务的名称。 如果 URL 为 `https://mySearchService.search.windows.net`，则应输入的值为 `mySearchService`。 | 
| `skillset-name` | 保留为“hotel-reviews-ss”。 | 
| `storage-account-name` | Azure 存储帐户名称。 | 
| `storage-connection-string` | 在存储帐户的“访问密钥”选项卡上，选择页面顶部的“显示密钥”，然后复制“key1” > “连接字符串”   。 | 
| `storage-container-name` | 保留为“hotel-reviews”。 | 

### <a name="review-the-request-collection-in-postman"></a>查看 Postman 中的请求集合

知识存储是在技能组中定义的，而技能组又会附加到索引器。 创建知识存储需要创建所有上游对象，包括索引、数据源、技能组和索引器。 虽然索引与知识存储无关，但索引器在执行时需要有索引，因此，你需要创建一个索引，这是索引器先决条件。

创建知识存储时，将发出四个 HTTP 请求： 

+ **用于创建索引的 PUT 请求**：此索引将包含在向搜索服务发出的查询请求中返回的可搜索数据。

+ **用于创建数据源的 POST 请求**：此数据源向索引器提供连接信息。 索引器连接到 Azure 存储帐户以检索示例数据。

+ **用于创建技能组的 PUT 请求**：技能组指定应用于数据的扩充。 它还指定知识存储的结构。

+ **用于创建索引器的 PUT 请求**：运行索引器读取数据、应用技能集、在 Azure 存储中创建知识存储并保存结果。 必须在最后运行此请求。

之前设置的变量用于标头和 URL。 以下创建索引请求的屏幕截图显示了这些变量出现在请求中的位置。

![显示 Postman 中标头界面的屏幕截图](media/knowledge-store-create-rest/postman-headers-ui.png)

> [!Note]
> 集合中的所有请求都会设置 `api-key` 和 `Content-type` 标头，这些标头是必需的。 如果 Postman 识别了某个变量，该变量将以橙色文本显示，如上面的屏幕截图中的 `{{admin-key}}` 所示。 如果变量拼写错误，它将以红色文本显示。
>

## <a name="create-an-index"></a>创建索引

使用[创建索引(REST API)](/rest/api/searchservice/create-index)在搜索服务中创建搜索索引。 搜索索引与知识存储无关，但索引器要求你创建一个。 搜索索引将包含与知识存储相同的内容。 如果希望使用其他方法浏览内容，可以通过将查询请求发送到搜索服务来查询此索引。 

通过向 `https://{{search-service-name}}.search.windows.net/indexes/{{index-name}}?api-version={{api-version}}` 发出 PUT 请求来创建索引。 在请求正文中提供索引架构。

```JSON
{
    "name": "{{index-name}}",
    "fields": [
        { "name": "name", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_date", "type": "Edm.DateTimeOffset", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_rating", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_text", "type": "Edm.String", "filterable": false,  "sortable": false, "facetable": false },
        { "name": "reviews_title", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_username", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "AzureSearch_DocumentKey", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false, "key": true },
        { "name": "language", "type": "Edm.String", "filterable": true, "sortable": false, "facetable": true },
        { "name": "translated_text", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false },
        { "name": "sentiment", "type": "Collection(Edm.String)", "searchable": false, "filterable": true, "retrievable": true, "sortable": false, "facetable": true },
        { "name": "keyphrases", "type": "Collection(Edm.String)", "filterable": true, "sortable": false, "facetable": true }
    ]
}
```

此索引定义是要向用户显示的数据的组合。 它包括直接映射到 CSV 的字段（酒店名称、评论日期等）和由技能组创建的字段（情绪、关键短语、语言和翻译文本）。

选择“发送”以发出 PUT 请求。

应会看到状态 `201 - Created`。 如果看到其他状态，请在“正文”窗格中查找包含错误消息的 JSON 响应。 如果使用的是免费搜索服务，请确保有空间容纳新对象（免费服务最多可容纳三个对象）。

此时，索引已创建完毕，但未加载。 导入文档的操作会稍后在你运行索引器时发生。 

## <a name="create-a-data-source"></a>创建数据源

接下来，将 Azure 认知搜索连接到 Blob 存储中存储的酒店数据。 若要创建数据源，请向 `https://{{search-service-name}}.search.windows.net/datasources?api-version={{api-version}}` 发送[创建数据源](/rest/api/searchservice/create-data-source) POST 请求。 

在 Postman 中，转到“创建数据源”请求，然后转到“正文”窗格。 应会看到以下代码：

```json
{
  "name" : "{{datasource-name}}",
  "description" : "Demo files to demonstrate knowledge store capabilities.",
  "type" : "azureblob",
  "credentials" : { "connectionString" : "{{storage-connection-string}}" },
  "container" : { "name" : "{{storage-container-name}}" }
}
```

选择“发送”以发出 POST 请求。 

## <a name="create-a-skillset"></a>创建技能集 

下一步是创建技能组，用于指定要应用的扩充，以及将在其中存储结果的知识存储。 此请求会将[创建技能组](/rest/api/searchservice/create-skillset) PUT 请求发送到 `https://{{search-service-name}}.search.windows.net/skillsets/{{skillset-name}}?api-version={{api-version}}`。

有两个大型顶级对象 `skills` 和 `knowledgeStore`：

+ “skills”是技能组。 `skills` 对象中的每个对象都是一个扩充服务。 每个扩充服务具有 `inputs` 和 `outputs`。 `LanguageDetectionSkill` 包含 `Language` 的输出 `targetName`。 此节点的值由其他大多数技能用作输入。 源为 `document/Language`。 在指定数据如何流入知识存储表的 `ShaperSkill` 中，使用一个节点的输出作为另一个节点的输入这一功能尢为明显。

+ “knowledgeStore”包括存储帐户的连接字符串和一系列投影。 投影数组中的每个投影项都会在 Azure 存储中生成一个表。 表中的列将包含生成的列，这些列用于交叉链接字段和内容字段。 它们还将包含在扩充过程中创建的内容字段。

  投影接受数据形状作为输入。 可以通过整形程序技能提供形状，将技能输出映射到投影，或者可以定义每个投影项的内联形状。 

若要生成技能集，请在 Postman 中选择“发送”按钮来放置 (PUT) 请求：

```json
{
    "name": "{{skillset-name}}",
    "description": "Skillset to detect language, translate text, extract key phrases, and score sentiment",
    "skills": [ 
        {
            "@odata.type": "#Microsoft.Skills.Text.SplitSkill", 
            "context": "/document/reviews_text", "textSplitMode": "pages", "maximumPageLength": 5000,
            "inputs": [ 
                { "name": "text", "source": "/document/reviews_text" }
            ],
            "outputs": [
                { "name": "textItems", "targetName": "pages" }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.V3.SentimentSkill",
            "context": "/document/reviews_text/pages/*",
            "inputs": [
                { "name": "text", "source": "/document/reviews_text/pages/*" },
                { "name": "languageCode", "source": "/document/language" }
            ],
            "outputs": [
                { "name": "sentiment", "targetName": "sentiment" }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
            "context": "/document",
            "inputs": [
                { "name": "text", "source": "/document/reviews_text" }
            ],
            "outputs": [
                { "name": "languageCode", "targetName": "language" }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.TranslationSkill",
            "context": "/document/reviews_text/pages/*",
            "defaultFromLanguageCode": null,
            "defaultToLanguageCode": "en",
            "inputs": [
                { "name": "text", "source": "/document/reviews_text/pages/*" }
            ],
            "outputs": [
                { "name": "translatedText", "targetName": "translated_text" }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
            "context": "/document/reviews_text/pages/*",
            "inputs": [
                { "name": "text",  "source": "/document/reviews_text/pages/*" },
                { "name": "languageCode",  "source": "/document/language" }
            ],
            "outputs": [
                { "name": "keyPhrases" , "targetName": "keyphrases" }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
            "context": "/document",
            "inputs": [
                { "name": "name",  "source": "/document/name" },
                { "name": "reviews_date",  "source": "/document/reviews_date" },
                { "name": "reviews_rating",  "source": "/document/reviews_rating" },
                { "name": "reviews_text",  "source": "/document/reviews_text" },
                { "name": "reviews_title",  "source": "/document/reviews_title" },
                { "name": "reviews_username",  "source": "/document/reviews_username" },
                { "name": "AzureSearch_DocumentKey",  "source": "/document/AzureSearch_DocumentKey" },
                {
                "name": "pages",
                "sourceContext": "/document/reviews_text/pages/*",
                "inputs": [
                    {
                    "name": "languageCode",
                    "source": "/document/language"
                    },
                    {
                    "name": "translatedText",
                    "source": "/document/reviews_text/pages/*/translated_text"
                    },
                    { 
                    "name": "sentiment",
                    "source": "/document/reviews_text/pages/*/sentiment"
                    },
                    {
                    "name": "keyPhrases",
                    "source": "/document/reviews_text/pages/*/keyphrases/*"
                    },
                    {
                    "name": "Page",
                    "source": "/document/reviews_text/pages/*"
                    }
                ]
                }
            ],
            "outputs": [
                { "name": "output" , "targetName": "tableprojection" }
            ]
        }
    ],
    "knowledgeStore": {
        "storageConnectionString": "{{storage-connection-string}}",
        "projections": [
            {
                "tables": [
                    { "tableName": "hotelReviews1Document", "generatedKeyName": "Documentid", "source": "/document/tableprojection" },
                    { "tableName": "hotelReviews2Pages", "generatedKeyName": "Pagesid", "source": "/document/tableprojection/pages/*" },
                    { "tableName": "hotelReviews3KeyPhrases", "generatedKeyName": "KeyPhrasesid", "source": "/document/tableprojection/pages/*/keyPhrases/*" }
                ],
                "objects": []
            },
            {
                "tables": [
                    { 
                        "tableName": "hotelReviews4InlineProjectionDocument", "generatedKeyName": "Documentid", "sourceContext": "/document",
                        "inputs": [
                            { "name": "name", "source": "/document/name"},
                            { "name": "reviews_date", "source": "/document/reviews_date"},
                            { "name": "reviews_rating", "source": "/document/reviews_rating"},
                            { "name": "reviews_username", "source": "/document/reviews_username"},
                            { "name": "reviews_title", "source": "/document/reviews_title"},
                            { "name": "reviews_text", "source": "/document/reviews_text"},
                            { "name": "AzureSearch_DocumentKey", "source": "/document/AzureSearch_DocumentKey" }
                        ]
                    },
                    { 
                        "tableName": "hotelReviews5InlineProjectionPages", "generatedKeyName": "Pagesid", "sourceContext": "/document/reviews_text/pages/*",
                        "inputs": [
                            { "name": "Sentiment", "source": "/document/reviews_text/pages/*/sentiment"},
                            { "name": "LanguageCode", "source": "/document/language"},
                            { "name": "Keyphrases", "source": "/document/reviews_text/pages/*/keyphrases"},
                            { "name": "TranslatedText", "source": "/document/reviews_text/pages/*/translated_text"},
                            { "name": "Page", "source": "/document/reviews_text/pages/*" }
                        ]
                    },
                    { 
                        "tableName": "hotelReviews6InlineProjectionKeyPhrases", "generatedKeyName": "kpidv2", "sourceContext": "/document/reviews_text/pages/*/keyphrases/*",
                        "inputs": [
                            { "name": "Keyphrases", "source": "/document/reviews_text/pages/*/keyphrases/*" }
                        ]
                    }
                ],
                "objects": []
            }
        ]
    }
}
```

## <a name="create-an-indexer"></a>创建索引器

最后一步是[创建索引器](/rest/api/searchservice/create-indexer)请求。 索引器读取数据并激活技能集。 索引器的定义引用创建的其他多个资源：数据源、索引和技能集。 

+ `parameters/configuration` 对象控制索引器引入数据的方式。 在这种情况下，输入数据在一个具有标题行和逗号分隔值的 CSV 文件中。 

+ 字段映射创建的“AzureSearch_DocumentKey”是 blob 索引器生成的（基于元数据存储路径）每个文档的唯一标识符。 

+ 输出字段映射指定如何将已扩充的字段映射到搜索索引中的字段。 输出字段映射不用于知识存储（知识存储使用形状和投影来表达物理数据结构）。

在 Postman 中选择“发送”以创建并运行索引器。 此步骤中将执行数据导入、技能组执行和知识存储创建操作。

```json
{
    "name": "{{indexer-name}}",
    "dataSourceName": "{{datasource-name}}",
    "skillsetName": "{{skillset-name}}",
    "targetIndexName": "{{index-name}}",
    "parameters": {
        "configuration": {
            "dataToExtract": "contentAndMetadata",
            "parsingMode": "delimitedText",
            "firstLineContainsHeaders": true,
            "delimitedTextDelimiter": ","
        }
    },
    "fieldMappings": [
        {
            "sourceFieldName": "AzureSearch_DocumentKey",
            "targetFieldName": "AzureSearch_DocumentKey",
            "mappingFunction": { "name": "base64Encode" }
        }
    ],
    "outputFieldMappings": [
        { "sourceFieldName": "/document/reviews_text/pages/*/Keyphrases/*", "targetFieldName": "Keyphrases" },
        { "sourceFieldName": "/document/Language", "targetFieldName": "Language" },
        { "sourceFieldName": "/document/reviews_text/pages/*/Sentiment", "targetFieldName": "Sentiment" }
    ]
}
```

## <a name="check-status"></a>查看状态

发送每个请求后，搜索服务应以 201 成功消息进行响应。 如果出现错误，请重新检查变量并确保搜索服务有空间容纳新索引、索引器、数据源和技能组（免费层每种类型仅可容纳三个）。

在 Azure 门户中，转到 Azure 认知搜索服务的“概览”页。 依次选择“索引器”选项卡、“hotels-reviews-ixr”。 在一两分钟内，状态应从“正在进行”变为“成功”，且没有任何错误和警告。

## <a name="check-tables-in-storage-explorer"></a>使用存储资源管理器查看表

在 Azure 门户中，切换到 Azure 存储帐户并使用存储资源管理器查看新的表。 应看到六个表，每个表对应于技能组中定义的每个投影。

每个表都是使用在查询中交叉链接表所需的 ID 生成的。 打开表时，滚动浏览这些字段可查看管道添加的内容字段。

   :::image type="content" source="media/knowledge-store-create-rest/knowledge-store-tables.png" alt-text="存储资源管理器中知识存储表的屏幕截图" border="true":::

在本演练中，知识存储由各种表组成，显示了形成和构建表的不同方式。 表 1 到表 3 使用整形程序技能的输出来确定列和行。 表 4 到 6 基于嵌入在投影本身的内联整形指令创建。 可以使用这两种方法获得相同的结果。

| 表 | 说明 |
|-------|-------------|
| hotelReviews1Document | 包含从 CSV 结转的字段，例如 reviews_date 和 reviews_text。 |
| hotelReviews2Pages | 包含由技能集创建的扩充字段，例如情绪评分和已翻译的文本。 |
| hotelReviews3KeyPhrases | 包含仅包含关键短语的长列表。 |
| hotelReviews4InlineProjectionDocument | 第一个表的替代项，使用内联整形而不是整形程序技能来为投影的数据绘制数据。 |
| hotelReviews5InlineProjectionPages | 第二个表的替代项，使用内联整形。 |
| hotelreviews6InlineProjectionKeyPhrases | 第三个表的替代项，使用内联整形。 |

## <a name="clean-up"></a>清理

在自己的订阅中操作时，最好在项目结束时确定是否仍需要已创建的资源。 持续运行资源可能会产生费用。 可以逐个删除资源，也可以删除资源组以删除整个资源集。

可以使用左侧导航窗格中的“所有资源”或“资源组”链接 ，在门户中查找和管理资源。

如果使用的是免费服务，请记住只能设置三个索引、索引器和数据源。 可以在门户中删除单个项目，以不超出此限制。

> [!TIP]
> 若要重复此练习或尝试其他 AI 扩充演练，请删除 hotel-reviews-idxr 索引器和相关对象以重新创建它们。 删除该索引器会将每日的免费事务计数器重置为零。

## <a name="next-steps"></a>后续步骤

使用认知服务扩充数据并将结果投影到知识存储后，接下来可以使用存储资源管理器或其他应用来浏览扩充的数据集。

若要了解如何使用存储资源管理器浏览此知识存储，请参阅以下演练：

> [!div class="nextstepaction"]
> [使用存储资源管理器查看](knowledge-store-view-storage-explorer.md)