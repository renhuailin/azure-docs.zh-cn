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
ms.date: 06/10/2021
ms.author: aahi
ms.openlocfilehash: b9e18bb9bf313ce2bbf1441b319b841a3153f38b
ms.sourcegitcommit: ddac53ddc870643585f4a1f6dc24e13db25a6ed6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/18/2021
ms.locfileid: "122396966"
---
# <a name="how-to-call-the-text-analytics-rest-api"></a>如何调用文本分析 REST API

在本文中，我们将使用文本分析 REST API 和 [Postman](https://www.postman.com/downloads/) 来演示这些关键概念。 API 提供多个同步和异步终结点，用于使用服务的功能。 

## <a name="create-a-text-analytics-resource"></a>创建文本分析资源

> [!NOTE]
> * 如果要使用 `/analyze` 或 `/health` 终结点，需要使用标准 (S) [定价层](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/)的文本分析资源。 此 `/analyze` 终结点包含在[定价层](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/)中。

使用文本分析 API 之前，需要创建一个 Azure 资源，其中包含应用程序的密钥和终结点。 

1.  首先，转到 [Azure 门户](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics)并创建一个新的文本分析资源（如果没有）。 选择一个[定价层](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/)。

2.  选择要用于终结点的区域。

3.  创建文本分析资源，并转到页面左侧资源管理下的“密钥和终结点”部分。 复制稍后调用 API 时要使用的密钥。 稍后会将此添加为 `Ocp-Apim-Subscription-Key` 标头的值。

4. 若要检查使用文本分析资源发送的文本记录数，请执行以下操作：

    1. 导航到 Azure 门户中的文本分析资源。 
    2. 单击左侧导航菜单中的“监视”下的“指标” 。 
    3. 在“指标”下拉框中选择“已处理的文本记录”。
    
文本记录是最多包含 1000 个字符的输入文本单位。  例如，作为输入文本提交的 1500 个字符将计为 2 个文本记录。

## <a name="change-your-pricing-tier"></a>更改定价层 

如果现有文本分析资源使用 S0 到 S4 定价层，则应将其更新为使用标准[定价层](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/)。 S0 到 S4 定价层将停用。 更新资源定价：

1. 导航到 [Azure 门户](https://portal.azure.com/)中的文本分析资源。
2. 选择左侧导航菜单中的“定价层”。 它将在“资源管理”下。 
3. 选择标准 (S) 定价层。 然后单击“选择”。

你还可以使用标准 (S) 定价层创建新的文本分析资源，并迁移应用程序以使用新资源凭据。 

## <a name="using-the-api-synchronously"></a>同步使用 API

你可以同步调用文本分析（用于低延迟方案）。 使用同步 API 时，必须单独调用每个 API（功能）。 如果需要调用多个功能，请查看以下部分，了解如何以异步方式调用文本分析。 

## <a name="using-the-api-asynchronously"></a>以异步方式使用 API

文本分析 v3.1 API 提供了两个异步终结点： 

* 用于文本分析的 `/analyze` 终结点，它让你能够在一个 API 调用中使用多个文本分析功能分析同一组文本文档。 以前，若要使用多个功能，需要为每个操作执行单独的 API 调用。 需要使用多个文本分析功能分析大型文档集时，请考虑使用此功能。

* 用于健康状况文本分析的 `/health` 终结点，它可以从临床文档中提取和标记相关的医疗信息。  

请参阅下表，了解可以通过异步方式使用哪些功能。 请注意，只有几个功能可以从 `/analyze` 终结点进行调用。 

| 功能 | 同步 | 异步 |
|--|--|--|
| 语言检测 | ✔ |  |
| 情绪分析 | ✔ | ✔* |
| 观点挖掘 | ✔ | ✔* |
| 关键短语提取 | ✔ | ✔* |
| 命名实体识别（包括 PII 和 PHI） | ✔ | ✔* |
| 实体链接 | ✔ | ✔* |
| 健康状况文本分析（容器） | ✔ |  |
| 健康状况文本分析 (API) |  | ✔  |
| 文本摘要 |  | ✔  |

`*` - 通过 `/analyze` 终结点以异步方式调用。


[!INCLUDE [text-analytics-api-references](../includes/text-analytics-api-references.md)]

[!INCLUDE [text-analytics-character-limits](../includes/character-limits.md)]

<a name="json-schema"></a>

## <a name="api-request-formats"></a>API 请求格式

可以同时将同步调用和异步调用发送到文本分析 API。

#### <a name="synchronous"></a>[同步](#tab/synchronous)

### <a name="synchronous-requests"></a>同步请求

对于所有同步操作，API 请求的格式都是相同的。 文档作为原始非结构化文本提交到 JSON 对象。 不支持 XML。 JSON 架构由下面描述的元素组成。

| 元素 | 有效值 | 必需？ | 使用情况 |
|---------|--------------|-----------|-------|
|`id` |数据类型为字符串，但实际上文档 ID 往往是整数。 | 必选 | 系统使用你提供的 ID 来构建输出。 为请求中的每个 ID 生成语言代码、关键短语和情绪分数。|
|`text` | 非结构化原始文本，最多 5,120 个字符。 | 必选 | 对于语言检测，可以使用任何语言来表示文本。 对于情绪分析、关键短语提取和实体标识，此文本必须使用[支持的语言](../language-support.md)。 |
|`language` | [支持语言](../language-support.md)的 2 字符 [ISO 639-1](https://en.wikipedia.org/wiki/List_of_ISO_639-1_codes) 代码 | 多种多样 | 需要情绪分析、关键短语提取、实体链接；语言检测为可选。 排除语言检测不会有任何错误，但没有它会削弱分析。 语言代码应对应你提供的 `text`。 |

下面是同步文本分析终结点的 API 请求示例。 

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

#### <a name="asynchronous"></a>[异步](#tab/asynchronous)

### <a name="asynchronous-requests-to-the-analyze-endpoint"></a>对 `/analyze` 终结点的异步请求

> [!NOTE]
> 文本分析客户端库的最新预发行版本让你能够使用客户端对象调用异步分析操作。 你可以在 GitHub 上找到相关示例：
* [C#](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics)
* [Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/)
* [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics)

`/analyze` 终结点使你可以选择要在单个 API 调用中使用哪个受支持的文本分析功能。 此终结点目前支持：

* 关键短语提取 
* 命名实体识别（包括 PII 和 PHI）
* 实体链接
* 情绪分析
* 观点挖掘
* 文本摘要

| 元素 | 有效值 | 必需？ | 使用情况 |
|---------|--------------|-----------|-------|
|`displayName` | 字符串 | 可选 | 用作作业的唯一标识符的显示名称。|
|`analysisInput` | 包括下面的 `documents` 字段 | 必需 | 包含要发送的文档的信息。 |
|`documents` | 包括下面的 `id` 和 `text` 字段 | 必需 | 包含发送的每个文档的信息以及文档的原始文本。 |
|`id` | String | 必须 | 提供的 ID 用于构建输出。 |
|`text` | 非结构化原始文本，最多包含 125,000 个字符。 | 必需 | 必须为英语，这是目前支持的唯一语言。 |
|`tasks` | 包括以下文本分析功能：`entityRecognitionTasks`、`entityLinkingTasks`、`keyPhraseExtractionTasks`、`entityRecognitionPiiTasks`、`extractiveSummarizationTasks` 或 `sentimentAnalysisTasks`。 | 必需 | 要使用的一个或多个文本分析功能。 请注意，`entityRecognitionPiiTasks` 有一个可选的 `domain` 参数，该参数可设置为 `pii` 或 `phi`，并且 `piiCategories` 用于检测所选的实体类型。 如果未指定 `domain` 参数，则系统默认使用 `pii`。 类似地，`sentimentAnalysisTasks` 具有 `opinionMining` 布尔参数，用于在情绪分析的输出中包含观点挖掘结果。 |
|`parameters` | 包括下面的 `model-version` 和 `stringIndexType` 字段 | 必需 | 此字段包含在所选的上述功能任务中。 它们包含有关要使用的模型版本和索引类型的信息。 |
|`model-version` | String | 必须 | 指定要使用调用的哪个模型版本。  |
|`stringIndexType` | String | 必须 | 指定与编程环境匹配的文本解码器。  支持的类型有 `textElement_v8`（默认）、`unicodeCodePoint` 和 `utf16CodeUnit`。 有关详细信息，请参阅[文本偏移文章](../concepts/text-offsets.md#offsets-in-api-version-31)。  |
|`domain` | 字符串 | 可选 | 仅作为参数应用于 `entityRecognitionPiiTasks` 任务，并可设置为 `pii` 或 `phi`。 如果未指定，则默认使用 `pii`。  |

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
                "text": "Pike place market is my favorite Seattle attraction. The shops have very good food."
            }
        ]
    },
    "tasks": {
        "entityRecognitionTasks": [
            {
                "parameters": {
                    "model-version": "latest",
                    "loggingOptOut": false
                }
            }
        ],
        "entityRecognitionPiiTasks": [
            {
                "parameters": {
                    "model-version": "latest",
                    "loggingOptOut": true,
                    "domain": "phi",
                    "piiCategories":["default"]
                }
            }
        ],
        "entityLinkingTasks": [
            {
                "parameters": {
                    "model-version": "latest",
                    "loggingOptOut": false
                }
            }
        ],
        "keyPhraseExtractionTasks": [
            {
                "parameters": {
                    "model-version": "latest",
                    "loggingOptOut": false
                }
            }
        ],
        "sentimentAnalysisTasks": [
            {
                "parameters": {
                    "model-version": "latest",
                    "loggingOptOut": false,
                    "opinionMining": true
                }
            }
        ]
    }
}

```

### <a name="asynchronous-requests-to-the-health-endpoint"></a>对 `/health` 终结点的异步请求

针对健康状况文本分析托管 API 的 API 请求的格式与它的容器的格式相同。 文档作为原始非结构化文本提交到 JSON 对象。 不支持 XML。 JSON 架构由下面描述的元素组成。  请填写并提交[认知服务请求表单](https://aka.ms/csgate)，请求访问健康状况文本分析。

| 元素 | 有效值 | 必需？ | 使用情况 |
|---------|--------------|-----------|-------|
|`id` |数据类型为字符串，但实际上文档 ID 往往是整数。 | 必选 | 系统使用你提供的 ID 来构建输出。 |
|`text` | 非结构化原始文本，最多 5,120 个字符。 | 必选 | 请注意，目前仅支持英语文本。 |
|`language` | [支持语言](../language-support.md)的 2 字符 [ISO 639-1](https://en.wikipedia.org/wiki/List_of_ISO_639-1_codes) 代码 | 必需 | 目前仅支持 `en` 群集。 |

以下是用于健康状况文本分析终结点的 API 请求示例。 

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
> 有关将数据发送到文本分析 API 的速率和大小限制的信息，请参阅[数据和速率限制](../concepts/data-limits.md)一文。


## <a name="set-up-a-request"></a>设置请求 

在 Postman（或其他 Web API 测试工具）中，为要使用的功能添加终结点。 使用下表查找适当的终结点格式，并将 `<your-text-analytics-resource>` 替换为资源终结点。 例如：

> [!TIP]
> 可以通过将 `/v3.1` 替换为 `/v3.0/` 来调用 v3.0 的以下同步终结点。

`https://my-resource.cognitiveservices.azure.com/text/analytics/v3.1/languages`

#### <a name="synchronous"></a>[同步](#tab/synchronous)

### <a name="endpoints-for-sending-synchronous-requests"></a>用于发送同步请求的终结点

| 功能 | 请求类型 | 资源终结点 |
|--|--|--|
| 语言检测 | POST | `<your-text-analytics-resource>/text/analytics/v3.1/languages` |
| 情绪分析 | POST | `<your-text-analytics-resource>/text/analytics/v3.1/sentiment` |
| 观点挖掘 | POST | `<your-text-analytics-resource>/text/analytics/v3.1/sentiment?opinionMining=true` |
| 关键短语提取 | POST | `<your-text-analytics-resource>/text/analytics/v3.1/keyPhrases` |
| 命名实体识别 - 常规 | POST | `<your-text-analytics-resource>/text/analytics/v3.1/entities/recognition/general` |
| 命名实体识别 - PII | POST | `<your-text-analytics-resource>/text/analytics/v3.1/entities/recognition/pii` |
| 命名实体识别 - PHI | POST |  `<your-text-analytics-resource>/text/analytics/v3.1/entities/recognition/pii?domain=phi` |
| 实体链接 | POST | `<your-text-analytics-resource>/text/analytics/v3.1/entities/linking` |

#### <a name="asynchronous"></a>[异步](#tab/asynchronous)

### <a name="endpoints-for-sending-asynchronous-requests-to-the-analyze-endpoint"></a>用于将异步请求发送到 `/analyze` 终结点的终结点

| 功能 | 请求类型 | 资源终结点 |
|--|--|--|
| 提交分析作业 | POST | `https://<your-text-analytics-resource>/text/analytics/v3.1/analyze` |
| 获取分析状态和结果 | GET | `https://<your-text-analytics-resource>/text/analytics/v3.1/analyze/jobs/<Operation-Location>` |

### <a name="endpoints-for-sending-asynchronous-requests-to-the-health-endpoint"></a>用于将异步请求发送到 `/health` 终结点的终结点

| 功能 | 请求类型 | 资源终结点 |
|--|--|--|
| 提交健康状况文本分析作业  | POST | `https://<your-text-analytics-resource>/text/analytics/v3.1/entities/health/jobs` |
| 获取作业状态和结果 | GET | `https://<your-text-analytics-resource>/text/analytics/v3.1/entities/health/jobs/<Operation-Location>` |
| 取消作业 | DELETE | `https://<your-text-analytics-resource>/text/analytics/v3.1/entities/health/jobs/<Operation-Location>` |

--- 

获得终结点后，在 Postman（或其他 Web API 测试工具）中执行以下操作：

1. 选择要使用的功能的请求类型。
2. 从上表中粘贴所需的正确操作的终结点。
3. 设置三个请求标头：

   + `Ocp-Apim-Subscription-Key`：从 Azure 门户获得的访问密钥
   + `Content-Type`：application/json
   + `Accept`：application/json

    如果使用的是 Postman，则请求应类似于以下屏幕截图（假设有一个 `/keyPhrases` 终结点）。
    
    ![请求带终结点和标头的屏幕截图](../media/postman-request-keyphrase-1.png)
    
4. 为“正文”选择“原始”格式
    
    ![请求具有正文设置的屏幕截图](../media/postman-request-body-raw.png)

5. 以有效格式粘贴一些 JSON 文档。 使用上面“API 请求格式”部分中的示例，有关详细信息和示例，请参阅以下主题：

      + [语言检测](text-analytics-how-to-language-detection.md)
      + [关键短语提取](text-analytics-how-to-keyword-extraction.md)
      + [情绪分析](text-analytics-how-to-sentiment-analysis.md)
      + [实体识别](text-analytics-how-to-entity-linking.md)

## <a name="send-the-request"></a>发送请求

提交 API 请求。 如果调用了同步终结点，则响应将立即显示为一个 JSON 文档，其中包含请求中提供的每个文档 ID 的项。

如果调用了异步 `/analyze` 或 `/health` 终结点，请检查是否收到 202 响应代码。 需要获取响应才能查看结果：

1. 在 API 响应中，从标头中找到 `Operation-Location`，它用于标识发送到 API 的作业。 
2. 为所使用的终结点创建 GET 请求。 请参阅[上文中的表格](#set-up-a-request)，了解终结点格式，并查看 [API 参考文档](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1/operations/AnalyzeStatus)。 例如：

    `https://my-resource.cognitiveservices.azure.com/text/analytics/v3.1/analyze/jobs/<Operation-Location>`

3. 将 `Operation-Location` 添加到请求。

4. 响应将为单个 JSON 文档，请求中提供的每个文档 ID 都有一个条目。

请注意，对于异步 `/analyze` 或 `/health` 操作，上述步骤 2 中 GET 请求的结果在创建作业后的 24 小时内可用。  此时间由 GET 响应中的 `expirationDateTime` 值指示。  在此时间段后，结果将被清除，并且不再可用于检索。    

## <a name="example-api-responses"></a>示例 API 响应
 
# <a name="synchronous"></a>[同步](#tab/synchronous)

### <a name="example-responses-for-synchronous-operation"></a>同步操作的示例响应

同步终结点响应将根据你使用的终结点而有所不同。 有关示例响应，请参阅以下文章。

+ [语言检测](text-analytics-how-to-language-detection.md#step-3-view-the-results)
+ [关键短语提取](text-analytics-how-to-keyword-extraction.md#step-3-view-results)
+ [情绪分析](text-analytics-how-to-sentiment-analysis.md#view-the-results)
+ [实体识别](text-analytics-how-to-entity-linking.md#view-results)

# <a name="asynchronous"></a>[异步](#tab/asynchronous)

### <a name="example-responses-for-asynchronous-operations"></a>异步操作的示例响应

如果成功，对 `/analyze` 终结点的 GET 请求将返回包含所分配任务的对象。 例如，`keyPhraseExtractionTasks`。 这些任务包含来自适当文本分析功能的响应对象。 有关详细信息，请参阅以下文章。

+ [关键短语提取](text-analytics-how-to-keyword-extraction.md#step-3-view-results)
+ [情绪分析](text-analytics-how-to-sentiment-analysis.md#view-the-results)
+ [实体识别](text-analytics-how-to-entity-linking.md#view-results)
+ [运行状况文本分析](text-analytics-for-health.md#hosted-asynchronous-web-api-response)

--- 

## <a name="see-also"></a>另请参阅

* [文本分析概述](../overview.md)
* [模型版本](../concepts/model-versioning.md)
* [常见问题解答 (FAQ)](../text-analytics-resource-faq.yml)</br>
* [文本分析产品页](//go.microsoft.com/fwlink/?LinkID=759712)
* [使用文本分析客户端库](../quickstarts/client-libraries-rest-api.md)
* [新增功能](../whats-new.md)
