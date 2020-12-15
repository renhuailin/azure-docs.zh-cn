---
title: 调用文本分析 API
titleSuffix: Azure Cognitive Services
description: 本文说明如何调用 Azure 认知服务文本分析 REST API 和 Postman。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 12/02/2020
ms.author: aahi
ms.openlocfilehash: 7b035af85e250d97fb05625bf386bec8dc94a74c
ms.sourcegitcommit: 2ba6303e1ac24287762caea9cd1603848331dd7a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/15/2020
ms.locfileid: "97505250"
---
# <a name="how-to-call-the-text-analytics-rest-api"></a>如何调用文本分析 REST API

本文介绍如何使用文本分析 REST API 和 [Postman](https://www.postman.com/downloads/) 来演示关键概念。 API 提供若干同步和异步终结点，以便使用服务的功能。 

## <a name="using-the-api-asynchronously"></a>以异步方式使用 API

从第3.1 版-预览版3开始，文本分析 API 提供了两个异步终结点： 

* `/analyze`通过文本分析的终结点，你可以在一个 API 调用中分析具有多个文本分析功能的一组相同的文本文档。 以前，若要使用多个功能，你需要为每个操作执行单独的 API 调用。 需要分析具有多个文本分析功能的大型文档集时，请考虑此功能。

* 用于 `/health` 运行状况的终结文本分析点，它可从临床文档中提取和标记相关的医疗信息。  

请参阅下表，了解可以异步使用哪些功能。 请注意，只能从终结点调用几个功能 `/analyze` 。 

| Feature | Synchronous | 异步 |
|--|--|--|
| 语言检测 | ✔ |  |
| 情绪分析 | ✔ |  |
| 观点挖掘 | ✔ |  |
| 关键短语提取 | ✔ | ✔* |
| 命名实体识别 (包括 PII 和 PHI)  | ✔ | ✔* |
| 运行状况 (容器文本分析)  | ✔ |  |
| 运行状况 (API 的文本分析)  |  | ✔  |

`*` -通过终结点以异步方式调用 `/analyze` 。


[!INCLUDE [text-analytics-api-references](../includes/text-analytics-api-references.md)]

[!INCLUDE [v3 region availability](../includes/v3-region-availability.md)]

## <a name="prerequisites"></a>必备条件


> [!NOTE]
> * 如果要使用或终结点，你将需要使用标准 (S) [定价层](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/) 的文本分析资源 `/analyze` `/health` 。

1.  首先，请前往 [Azure 门户](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) 并新建一个文本分析资源（如果没有）。 如果要使用或终结点，请选择 " **标准 (") 定价层** `/analyze` `/health` 。 此 `/analyze` 终结点包含在 [定价层](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/)中。

2.  选择要用于终结点的区域。  请注意 `/analyze` ，和 `/health` 终结点仅在以下区域提供：美国西部2、美国东部2、美国中部、北欧和西欧。

3.  创建文本分析资源，并前往页面左侧的 "密钥和终结点" 边栏选项卡。 复制稍后在调用 Api 时使用的密钥。 稍后会将此添加为标头的值 `Ocp-Apim-Subscription-Key` 。


<a name="json-schema"></a>

## <a name="api-request-format"></a>API 请求格式

#### <a name="synchronous"></a>[Synchronous](#tab/synchronous)

对于所有同步操作，API 请求的格式都是相同的。 文档作为原始非结构化文本提交到 JSON 对象。 不支持 XML。 JSON 架构由以下描述的元素组成。

| 元素 | 有效值 | 必需？ | 使用情况 |
|---------|--------------|-----------|-------|
|`id` |数据类型为字符串，但实际上文档 ID 往往是整数。 | 必须 | 系统使用你提供的 ID 来构建输出。 为请求中的每个 ID 生成语言代码、关键短语和情绪分数。|
|`text` | 非结构化原始文本，最多 5,120 个字符。 | 必须 | 对于语言检测，可以使用任何语言来表示文本。 对于情绪分析、关键短语提取和实体标识，此文本必须使用[支持的语言](../language-support.md)。 |
|`language` | [支持语言](../language-support.md)的 2 字符 [ISO 639-1](https://en.wikipedia.org/wiki/List_of_ISO_639-1_codes) 代码 | 多种多样 | 需要情绪分析、关键短语提取、实体链接；语言检测为可选。 排除语言检测不会有任何错误，但没有它会削弱分析。 语言代码应对应你提供的 `text`。 |

下面是同步文本分析终结点的 API 请求的示例。 

```json
{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "Sample text to be sent to the text analytics api."
    }
  ]
}
```

#### <a name="analyze"></a>[分析](#tab/analyze)

> [!NOTE]
> 文本分析客户端库的最新预发行版使你能够使用客户端对象来调用异步分析操作。 可在 GitHub 上找到示例：
* [C#](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics)
* [Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/)
* [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics)

`/analyze`终结点使你可以选择要在单个 API 调用中使用的受支持文本分析功能。 此终结点当前支持：

* 关键短语提取 
* 命名实体识别 (包括 PII 和 PHI) 

| 元素 | 有效值 | 必需？ | 使用情况 |
|---------|--------------|-----------|-------|
|`displayName` | 字符串 | 可选 | 用作作业的唯一标识符的显示名称。|
|`analysisInput` | 包括 `documents` 以下字段 | 必须 | 包含要发送的文档的信息。 |
|`documents` | 包括 `id` 以下和 `text` 字段 | 必须 | 包含要发送的每个文档的信息以及文档的原始文本。 |
|`id` | String | 必须 | 提供的 Id 用于构建输出的结构。 |
|`text` | 非结构化原始文本，最多125000个字符。 | 必须 | 必须采用英语语言，这是当前唯一支持的语言。 |
|`tasks` | 包括以下文本分析功能： `entityRecognitionTasks` 、 `keyPhraseExtractionTasks` 或 `entityRecognitionPiiTasks` 。 | 必须 | 要使用的一个或多个文本分析功能。 请注意， `entityRecognitionPiiTasks` 有一个可选 `domain` 参数，可将其设置为 `pii` 或 `phi` 。 如果未指定，则系统默认为 `pii` 。 |
|`parameters` | 包括 `model-version` 以下和 `stringIndexType` 字段 | 必须 | 此字段包含在所选的上述功能任务中。 它们包含有关要使用的模型版本的信息和索引类型。 |
|`model-version` | String | 必须 | 指定要使用的模型的版本。  |
|`stringIndexType` | String | 必须 | 指定与编程环境匹配的文本解码器。  支持的类型 `textElement_v8` (默认值) 、 `unicodeCodePoint` 、 `utf16CodeUnit` 。 有关详细信息，请参阅 [文本偏移文章](../concepts/text-offsets.md#offsets-in-api-version-31-preview) 。  |
|`domain` | 字符串 | 可选 | 仅适用于任务的参数 `entityRecognitionPiiTasks` ，可以设置为 `pii` 或 `phi` 。 如果未指定，则默认为 `pii` 。  |

```json
{
    "displayName": "My Job",
    "analysisInput": {
        "documents": [
            {
                "id": "doc1",
                "text": "It's incredibly sunny outside! I'm so happy"
            },
            {
                "id": "doc2",
                "text": "Pike place market is my favorite Seattle attraction."
            }
        ]
    },
    "tasks": {
        "entityRecognitionTasks": [
            {
                "parameters": {
                    "model-version": "latest",
                    "stringIndexType": "TextElements_v8"
                }
            }
        ],
        "keyPhraseExtractionTasks": [{
            "parameters": {
                "model-version": "latest"
            }
        }],
        "entityRecognitionPiiTasks": [{
            "parameters": {
                "model-version": "latest"
            }
        }]
    }
}

```

#### <a name="text-analytics-for-health"></a>[运行状况文本分析](#tab/health)

针对运行状况托管 API 的文本分析的 API 请求的格式与它的容器的格式相同。 文档作为原始非结构化文本提交到 JSON 对象。 不支持 XML。 JSON 架构由以下描述的元素组成。  请填写并提交 [认知服务请求表单](https://aka.ms/csgate) ，请求访问运行状况公共预览版文本分析。 不会向你收取文本分析的健康状况。 

| 元素 | 有效值 | 必需？ | 使用情况 |
|---------|--------------|-----------|-------|
|`id` |数据类型为字符串，但实际上文档 ID 往往是整数。 | 必须 | 系统使用你提供的 ID 来构建输出。 |
|`text` | 非结构化原始文本，最多 5,120 个字符。 | 必须 | 请注意，目前仅支持英文文本。 |
|`language` | [支持语言](../language-support.md)的 2 字符 [ISO 639-1](https://en.wikipedia.org/wiki/List_of_ISO_639-1_codes) 代码 | 必须 | `en`目前仅支持。 |

下面是运行状况终结点的文本分析 API 请求的示例。 

```json
example.json

{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "Subject was administered 100mg remdesivir intravenously over a period of 120 min"
    }
  ]
}
```

---

>[!TIP]
> 有关将数据发送到文本分析 API 的速率和大小限制的信息，请参阅 [数据和速率限制](../concepts/data-limits.md) 一文。


## <a name="set-up-a-request"></a>设置请求 

在 Postman (或其他 web API 测试工具) 中，为要使用的功能添加终结点。 使用下表查找适当的终结点格式，并将替换 `<your-text-analytics-resource>` 为资源终结点。 例如：

`https://my-resource.cognitiveservices.azure.com/text/analytics/v3.0/languages`

#### <a name="synchronous"></a>[Synchronous](#tab/synchronous)

| Feature | 请求类型 | 资源终结点 |
|--|--|--|
| 语言检测 | POST | `<your-text-analytics-resource>/text/analytics/v3.0/languages` |
| 情绪分析 | POST | `<your-text-analytics-resource>/text/analytics/v3.0/sentiment` |
| 观点挖掘 | POST | `<your-text-analytics-resource>/text/analytics/v3.0/sentiment?opinionMining=true` |
| 关键短语提取 | POST | `<your-text-analytics-resource>/text/analytics/v3.0/keyPhrases` |
| 命名实体识别-常规 | POST | `<your-text-analytics-resource>/text/analytics/v3.0/entities/recognition/general` |
| 命名实体识别-PII | POST | `<your-text-analytics-resource>/text/analytics/v3.0/entities/recognition/pii` |
| 命名实体识别-PHI | POST |  `<your-text-analytics-resource>/text/analytics/v3.0/entities/recognition/pii?domain=phi` |

#### <a name="analyze"></a>[分析](#tab/analyze)

| Feature | 请求类型 | 资源终结点 |
|--|--|--|
| 提交分析作业 | POST | `https://<your-text-analytics-resource>/text/analytics/v3.1-preview.3/analyze` |
| 获取分析状态和结果 | GET | `https://<your-text-analytics-resource>/text/analytics/v3.1-preview.3/analyze/jobs/<Operation-Location>` |

#### <a name="text-analytics-for-health"></a>[运行状况文本分析](#tab/health)

| Feature | 请求类型 | 资源终结点 |
|--|--|--|
| 提交运行状况作业文本分析  | POST | `https://<your-text-analytics-resource>/text/analytics/v3.1-preview.3/entities/health/jobs` |
| 获取作业状态和结果 | GET | `https://<your-text-analytics-resource>/text/analytics/v3.1-preview.3/entities/health/jobs/<Operation-Location>` |
| 取消作业 | DELETE | `https://<your-text-analytics-resource>/text/analytics/v3.1-preview.3/entities/health/jobs/<Operation-Location>` |

--- 

获得终结点后，在 Postman (或其他 web API 测试工具) ：

1. 选择要使用的功能的请求类型。
2. 从上表中粘贴所需的正确操作的终结点。
3. 设置三个请求标头：

   + `Ocp-Apim-Subscription-Key`：你的访问密钥，从 Azure 门户获取
   + `Content-Type`：application/json
   + `Accept`：application/json

    如果使用的是 Postman，则请求应类似于以下屏幕截图，假设有一个 `/keyPhrases` 终结点。
    
    ![请求带终结点和标头的屏幕截图](../media/postman-request-keyphrase-1.png)
    
4. 为 **正文** 格式选择 **raw**
    
    ![请求具有正文设置的屏幕截图](../media/postman-request-body-raw.png)

5. 以有效格式粘贴某些 JSON 文档。 使用上面的 " **API 请求格式** " 部分中的示例，有关详细信息和示例，请参阅以下主题：

      + [语言检测](text-analytics-how-to-language-detection.md)
      + [关键短语提取](text-analytics-how-to-keyword-extraction.md)
      + [情绪分析](text-analytics-how-to-sentiment-analysis.md)
      + [实体识别](text-analytics-how-to-entity-linking.md)

## <a name="send-the-request"></a>发送请求

提交 API 请求。 如果调用了同步终结点，则响应将立即显示为一个 JSON 文档，其中包含请求中提供的每个文档 ID 的项。

如果调用了异步 `/analyze` 或 `/health` 终结点，请检查是否收到了202响应代码。 你将需要获取响应来查看结果：

1. 在 API 响应中，查找 `Operation-Location` 标头中的，它标识发送到 API 的作业。 
2. 为所使用的终结点创建 GET 请求。 请参阅 [上表](#set-up-a-request) 中的终结点格式，并查看 [API 参考文档](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/AnalyzeStatus)。 例如：

    `https://my-resource.cognitiveservices.azure.com/text/analytics/v3.1-preview.3/analyze/jobs/<Operation-Location>`

3. 将添加 `Operation-Location` 到请求。

4. 响应将是一个 JSON 文档，其中包含请求中提供的每个文档 ID 的项。

请注意，对于异步 `/analyze` 或 `/health` 操作，上述步骤2中的 GET 请求的结果可用于从创建作业的时间24小时。  此时间由 `expirationDateTime` GET 响应中的值指示。  在此时间段后，结果将被清除，并且不再可用于检索。    

## <a name="example-api-responses"></a>示例 API 响应
 
# <a name="synchronous"></a>[Synchronous](#tab/synchronous)

同步终结点响应将根据你使用的终结点而有所不同。 有关示例响应，请参阅以下文章。

+ [语言检测](text-analytics-how-to-language-detection.md#step-3-view-the-results)
+ [关键短语提取](text-analytics-how-to-keyword-extraction.md#step-3-view-results)
+ [情绪分析](text-analytics-how-to-sentiment-analysis.md#view-the-results)
+ [实体识别](text-analytics-how-to-entity-linking.md#view-results)

# <a name="analyze"></a>[分析](#tab/analyze)

如果成功，对终结点的 GET 请求 `/analyze` 将返回一个对象，其中包含所分配的任务。 例如，`keyPhraseExtractionTasks`。 这些任务包含来自适当文本分析功能的响应对象。 有关详细信息，请参阅以下文章。

+ [关键短语提取](text-analytics-how-to-keyword-extraction.md#step-3-view-results)
+ [实体识别](text-analytics-how-to-entity-linking.md#view-results)


```json
{
  "displayName": "My Analyze Job",
  "jobId": "dbec96a8-ea22-4ad1-8c99-280b211eb59e_637408224000000000",
  "lastUpdateDateTime": "2020-11-13T04:01:14Z",
  "createdDateTime": "2020-11-13T04:01:13Z",
  "expirationDateTime": "2020-11-14T04:01:13Z",
  "status": "running",
  "errors": [],
  "tasks": {
      "details": {
          "name": "My Analyze Job",
          "lastUpdateDateTime": "2020-11-13T04:01:14Z"
      },
      "completed": 1,
      "failed": 0,
      "inProgress": 2,
      "total": 3,
      "keyPhraseExtractionTasks": [
          {
              "name": "My Analyze Job",
              "lastUpdateDateTime": "2020-11-13T04:01:14.3763516Z",
              "results": {
                  "inTerminalState": true,
                  "documents": [
                      {
                          "id": "doc1",
                          "keyPhrases": [
                              "sunny outside"
                          ],
                          "warnings": []
                      },
                      {
                          "id": "doc2",
                          "keyPhrases": [
                              "favorite Seattle attraction",
                              "Pike place market"
                          ],
                          "warnings": []
                      }
                  ],
                  "errors": [],
                  "modelVersion": "2020-07-01"
              }
          }
      ]
  }
}
```

# <a name="text-analytics-for-health"></a>[运行状况文本分析](#tab/health)

请参阅以下文章，详细了解文本分析的运行状况异步 API 响应：

+ [运行状况文本分析](text-analytics-for-health.md#hosted-asynchronous-web-api-response)


--- 

## <a name="see-also"></a>另请参阅

* [文本分析概述](../overview.md)
* [常见问题解答 (FAQ)](../text-analytics-resource-faq.md)</br>
* [文本分析产品页](//go.microsoft.com/fwlink/?LinkID=759712)
* [使用文本分析客户端库](../quickstarts/client-libraries-rest-api.md)
* [新增功能](../whats-new.md)
