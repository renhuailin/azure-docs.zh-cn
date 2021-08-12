---
title: 文本分析 API 中的新增功能
titleSuffix: Text Analytics - Azure Cognitive Services
description: 本文介绍了 Azure 认知服务文本分析的新版本和新功能。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 07/12/2021
ms.author: aahi
ms.custom: references_regions
ms.openlocfilehash: f79c9cb7381f2325de2efca5e20b37c60ab72013
ms.sourcegitcommit: d2738669a74cda866fd8647cb9c0735602642939
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/13/2021
ms.locfileid: "113652469"
---
# <a name="whats-new-in-the-text-analytics-api"></a>文本分析 API 中有哪些新功能？

文本分析 API 会持续更新。 为了让大家随时了解最新的开发成果，本文介绍了新版本和新功能。

## <a name="july-2021"></a>2021 年 7 月

### <a name="ga-release-updates"></a>正式发布版本更新

* 正式发布针对容器和托管 API 的[运行状况文本分析](how-tos/text-analytics-for-health.md?tabs=ner) (/health)。
* 正式发布[观点挖掘](how-tos/text-analytics-how-to-sentiment-analysis.md?tabs=version-3-1#opinion-mining)。
* 正式发布 [PII 提取和编修](how-tos/text-analytics-how-to-entity-linking.md?tabs=version-3-1#personally-identifiable-information-pii)。
* 正式发布[异步 (`/analyze`) 终结点](how-tos/text-analytics-how-to-call-api.md?tabs=synchronous#using-the-api-asynchronously)。
* 使用新的 SDK 更新了[快速入门](quickstarts/client-libraries-rest-api.md)示例。 

## <a name="june-2021"></a>2021 年 6 月

### <a name="general-api-updates"></a>常规 API 更新

* 基于转换器的关键短语提取的新模型版本 `2021-06-01`。 提供以下功能：
  * 支持 10 种语言（拉丁语和 CJK）。 
  * 改进了关键短语提取。
* [命名实体识别](how-tos/text-analytics-how-to-entity-linking.md) v3.x 的 `2021-06-01` 模型版本，其中提供 
  * 改进了 AI 质量并扩展了对技能实体类别的语言支持。 
  * 为技能实体类别添加了西班牙语、法语、德语、意大利语和葡萄牙语语言支持
* 异步 (/analyze) 操作和运行状况文本分析（非封闭预览版）在所有区域可用。 

### <a name="text-analytics-for-health-updates"></a>健康状况文本分析更新

* 预览健康状况文本分析时不再需要申请访问权限。
* 为 `/health` 终结点和本地容器提供了新模型版本 `2021-05-15`，其中提供
    * 5 种新实体类型：`ALLERGEN`、`CONDITION_SCALE`、`COURSE`、`EXPRESSION` 和 `MUTATION_TYPE`，
    * 14 种新关系类型，
    * 针对新实体类型进行了扩展的断言检测以及
    * 对 ALLERGEN 实体类型的链接支持
* 带有标签 `3.0.016230002-onprem-amd64` 和模型版本 `2021-05-15` 的运行状况文本分析容器的新图像。 此容器可从 Microsoft Container Registry 下载。
 
## <a name="may-2021"></a>2021 年 5 月

* 现在可以使用文本分析资源访问[自定义问答](../qnamaker/custom-question-answering.md)（以前为 QnA Maker）。 

### <a name="general-api-updates"></a>常规 API 更新

* 发布了新 API v3.1-preview.5，其中包括 
  * 异步[分析 API](how-tos/text-analytics-how-to-call-api.md?tabs=asynchronous) 现在支持情绪分析 (SA) 和观点挖掘 (OM)。
  * 新查询参数 `LoggingOptOut` 现在可供希望不记录事件报告输入文本的客户使用。  在[数据隐私](/legal/cognitive-services/text-analytics/data-privacy?context=/azure/cognitive-services/text-analytics/context/context)一文中详细了解此参数。
* 健康状况文本分析和分析异步操作现在在所有区域都可用

## <a name="march-2021"></a>2021 年 3 月

### <a name="general-api-updates"></a>常规 API 更新
* 发布了新 API v3.1-preview.4，其中包括 
   * 观点挖掘 JSON 响应正文中的更改： 
      * `aspects` 现为 `targets`，`opinions` 现为 `assessments`。 
   * 健康状况文本分析托管 web API 的 JSON 响应正文中的更改： 
      * 已停用针对否定检测到的实体对象的 `isNegated` 布尔名称，替换为断言检测。
      * 现在，提取的属性和实体的关系以及不同实体之间的关系包括一个新属性 `role`。  这让检测到的关系类型更加具体。
   * 现在，实体链接可用作 `/analyze` 终结点中的异步任务。
   * 现在，`/pii` 结点中提供一个新参数 `pii-categories`。
      * 使用该参数，可以指定特定的 PII 实体以及输入语言默认情况下不支持的实体。
* 更新了客户端库，其中包括异步分析和健康状况文本分析操作。 你可以在 GitHub 上找到相关示例：

    * [C#](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics)
    * [Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/)
    * [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics)
    * [JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics/samples/v5/javascript)
    
> [!div class="nextstepaction"]
> [详细了解文本分析 API v3.1-Preview.4](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-4/operations/Languages)

### <a name="text-analytics-for-health-updates"></a>健康状况文本分析更新

* 为 `/health` 终结点和本地容器提供了新模型版本 `2021-03-01`，其中提供
    * `Gene` 实体类型已重命名为 `GeneOrProtein`。
    * 新的 `Date` 实体类型。
    * 使用断言检测替代否定检测（仅适用于 API 3.1-preview. 4）。
    * 为通过各种 ontology 和编码系统规范化的链接实体提供一个新的首选属性 `name`（仅适用于 API 3.1-preview.4）。 
* 向容器预览存储库发布了带有 `3.0.015490002-onprem-amd64` 标记的新容器映像和新模型版本 `2021-03-01`。 
    * 2021 年 4 月 26 日之后，此容器映像将再也不可以从 `containerpreview.azurecr.io` 下载。
* 此同一模型版本的新运行状况文本分析容器映像现在可以在 `mcr.microsoft.com/azure-cognitive-services/textanalytics/healthcare` 上获得。 自 4 月 26 日起，你将只能从此存储库下载该容器。

> [!div class="nextstepaction"]
> [详细了解健康状况文本分析](how-tos/text-analytics-for-health.md)

### <a name="text-analytics-resource-portal-update"></a>文本分析资源门户更新
* **处理的文本记录数** 现可用作 Azure 门户中文本分析资源“监视”部分的指标。  

## <a name="february-2021"></a>2021 年 2 月

* [命名实体识别](how-tos/text-analytics-how-to-entity-linking.md) v3.1-preview.x 中 PII 终结点的 `2021-01-15` 模型版本，其中包括 
  * 扩展了对 9 种新语言的支持
  * 针对支持的语言提升了命名实体类别的 AI 质量。
* S0 到 S4 定价层将于 2021 年 3 月 8 日停用。 如果现有文本分析资源使用 S0 到 S4 定价层，则应将其更新为使用标准[定价层](how-tos/text-analytics-how-to-call-api.md#change-your-pricing-tier)。
* [语言检测容器](how-tos/text-analytics-how-to-install-containers.md?tabs=sentiment)现已正式发布。
* API v2.1 即将停用。 

## <a name="january-2021"></a>2021 年 1 月

* [命名实体识别](how-tos/text-analytics-how-to-entity-linking.md) v3.x 的 `2021-01-15` 模型版本，其中提供 
  * 扩展了[几种常规实体类别](named-entity-types.md)的语言支持。 
  * 针对所有支持的 v3 语言提升了常规实体类别的 AI 质量。 

* [语言检测](how-tos/text-analytics-how-to-language-detection.md)的 `2021-01-05` 模型版本，提供了更多[语言支持](language-support.md?tabs=language-detection)。

这些模型版本目前在美国东部地区不可用。 

> [!div class="nextstepaction"]
> [详细了解新 NER 模型](https://azure.microsoft.com/updates/text-analytics-ner-improved-ai-quality)

## <a name="december-2020"></a>2020 年 12 月

* [更新](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/)了文本分析 API 的定价详细信息。

## <a name="november-2020"></a>2020 年 11 月

* 一个适用于新异步[分析 API](how-tos/text-analytics-how-to-call-api.md?tabs=analyze) 的[新终结点](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/Analyze)，使用文本分析 API v3.1-preview.3，支持批量处理 NER、PII 和关键短语提取操作。
* 一个适用于新异步[健康状况文本分析](how-tos/text-analytics-for-health.md)托管 API 的[新终结点](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/Health)，使用文本分析 API v3.1-preview.3，支持批量处理。
* 上面列出的两项新功能仅可用于以下区域：`West US 2`、`East US 2`、`Central US`、`North Europe` 和 `West Europe` 区域。
* 自模型版本 `2020-04-01` 开始，[情绪分析](how-tos/text-analytics-how-to-sentiment-analysis.md) v3.x 中现在支持葡萄牙语（巴西）`pt-BR`。 向现有的 `pt-PT` 中添加了葡萄牙语支持。
* 更新了客户端库，其中包括异步分析和健康状况文本分析操作。 你可以在 GitHub 上找到相关示例：

    * [C#](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics)
    * [Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/)
    * [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics)
    * 
> [!div class="nextstepaction"]
> [详细了解文本分析 API v3.1-Preview.3](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/Languages)

## <a name="october-2020"></a>2020 年 10 月

* 自模型版本 `2020-04-01` 开始，情绪分析 v3.x 支持印地语。 
* v3 /languages 终结点的模型版本 `2020-09-01`，其中增强了语言检测并提升了准确度。
* v3 在印度中部和阿拉伯联合酋长国北部可用。

## <a name="september-2020"></a>2020 年 9 月

### <a name="general-api-updates"></a>常规 API 更新

* 针对文本分析 v3.1 公共预览版本发布了新 URL，以支持对以下命名实体识别 v3 终结点的更新： 
    * 现在，`/pii` 终结点在响应 JSON 中包括新的 `redactedText` 属性，在输入文本中检测到的 PII 实体的每个字符均被替换为 `*`。
    * 现在，`/linking` 终结点在链接实体的响应 JSON 中包括 `bingID` 属性。
* 下列文本分析预览 API 终结点已于 2020 年 9 月 4 日停用：
    * v2.1-preview
    * v3.0-preview
    * v3.0-preview.1
    
> [!div class="nextstepaction"]
> [详细了解文本分析 API v3.1-Preview.2](quickstarts/client-libraries-rest-api.md)

### <a name="text-analytics-for-health-container-updates"></a>健康状况文本分析容器更新

以下是专门针对 9 月版健康状况文本分析容器的更新。
* 向容器预览版本存储库发布了使用新模型版本 `2020-09-03` 的带有 `1.1.013530001-amd64-preview` 标记的新容器映像。 
* 此模型版本提升了实体识别、缩写检测和延迟改进效果。

> [!div class="nextstepaction"]
> [详细了解健康状况文本分析](how-tos/text-analytics-for-health.md)

## <a name="august-2020"></a>2020 年 8 月

### <a name="general-api-updates"></a>常规 API 更新

* V3 `/keyphrases`、`/pii` 和 `/languages` 终结点的模型版本 `2020-07-01`，其中添加了：
    * 用于命名实体识别的其他政府和国家特定的[实体类别](named-entity-types.md?tabs=personal)。
    * 情绪分析 v3 中支持挪威语和土耳其语。
* 对于超过发布的[数据限制](concepts/data-limits.md)的 v3 API 请求，现在将返回 HTTP 400 错误。 
* 返回偏移量的终结点现在支持可选 `stringIndexType` 参数，该参数可调整返回的 `offset` 和 `length` 的值，以匹配支持的[字符串索引架构](concepts/text-offsets.md)。

### <a name="text-analytics-for-health-container-updates"></a>健康状况文本分析容器更新

以下是专门针对 8 月版健康状况文本分析容器的更新。

* 健康状况文本分析的新模型版本：`2020-07-24`
* 发送健康状况文本分析请求的新 URL：`http://<serverURL>:5000/text/analytics/v3.2-preview.1/entities/health`（请注意，要使用此容器映像中包括的 demo Web 应用，需要清理浏览器缓存）

JSON 响应中的以下属性发生了变化：

* `type` 已重名为 `category` 
* `score` 已重名为 `confidenceScore`
* JSON 输出 `category` 字段中的实体现在采用 pascal 大小写格式。 已重命名以下实体：
    * `EXAMINATION_RELATION` 已重命名为 `RelationalOperator`。
    * `EXAMINATION_UNIT` 已重命名为 `MeasurementUnit`。
    * `EXAMINATION_VALUE` 已重命名为 `MeasurementValue`。
    * `ROUTE_OR_MODE` 已重命名为 `MedicationRoute`。
    * 关系实体 `ROUTE_OR_MODE_OF_MEDICATION` 已重命名为 `RouteOfMedication`。

添加了以下实体：

* NER
    * `AdministrativeEvent`
    * `CareEnvironment`
    * `HealthcareProfession`
    * `MedicationForm` 

* 关系提取
    * `DirectionOfCondition`
    * `DirectionOfExamination`
    * `DirectionOfTreatment`

> [!div class="nextstepaction"]
> [详细了解健康状况文本分析容器](how-tos/text-analytics-for-health.md)

## <a name="july-2020"></a>2020 年 7 月 

### <a name="text-analytics-for-health-container---public-gated-preview"></a>健康状况文本分析容器 - 受限制的公共预览版本

健康状况文本分析容器现位于受限制的公共预览版本中，该版本支持从临床文档非结构化英语文本中提取信息，这类文本例如病患登记表、医嘱、研究论文和出院小结。 目前，使用健康状况文本分析容器不收取费用。

该容器提供以下功能：

* 命名实体识别
* 关系提取
* 实体链接
* 否定

## <a name="may-2020"></a>2020 年 5 月

### <a name="text-analytics-api-v3-general-availability"></a>文本分析 API v3 正式发布

文本分析 API v3 现已正式发布，具有以下更新：

* 模型版本 `2020-04-01`
* 每个功能的新[数据限制](concepts/data-limits.md)
* 更新了[情绪分析 (SA) v3](how-tos/text-analytics-how-to-sentiment-analysis.md) 的[语言支持](language-support.md)
* 用于实体链接的单独终结点 
* [命名实体识别 (NER) v3](how-tos/text-analytics-how-to-entity-linking.md) 中的新“Address”实体类别。
* NER v3 中的新子类别：
   * 位置 - 地理
   * 位置 - 结构
   * 组织 - 证券交易所
   * 组织 - 医疗
   * 组织 - 体育
   * 事件 - 文化
   * 事件 - 自然
   * 事件 - 体育

已在 JSON 响应中添加了以下属性：
   * 情绪分析中的 `SentenceText`
   * 每个文档的 `Warnings` 

JSON 响应中以下属性的名称已更改（如果适用）：

* `score` 已重名为 `confidenceScore`
    * `confidenceScore` 的精度为小数点后两位。 
* `type` 已重名为 `category`
* `subtype` 已重名为 `subcategory`

> [!div class="nextstepaction"]
> [详细了解文本分析 API v3](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Languages)

### <a name="text-analytics-api-v31-public-preview"></a>文本分析 API v3.1 公共预览版本
   * 新情绪分析功能 - [观点挖掘](how-tos/text-analytics-how-to-sentiment-analysis.md#opinion-mining)
   * 新增了用于受保护的健康状况信息 (`PHI`) 的个人 (`PII`) 域。

> [!div class="nextstepaction"]
> [详细了解文本分析 API v3.1 预览版](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-1/operations/Languages)

## <a name="february-2020"></a>2020 年 2 月

### <a name="sdk-support-for-text-analytics-api-v3-public-preview"></a>SDK 对文本分析 API v3 公共预览版的支持

作为[统一 Azure SDK 版本](https://techcommunity.microsoft.com/t5/azure-sdk/january-2020-unified-azure-sdk-release/ba-p/1097290)的一部分，文本分析 API v3 SDK 现已作为以下编程语言的公共预览版提供：
   * [C#](./quickstarts/client-libraries-rest-api.md?pivots=programming-language-csharp&tabs=version-3)
   * [Python](./quickstarts/client-libraries-rest-api.md?pivots=programming-language-python&tabs=version-3)
   * [JavaScript (Node.js)](./quickstarts/client-libraries-rest-api.md?pivots=programming-language-javascript&tabs=version-3)
   * [Java](./quickstarts/client-libraries-rest-api.md?pivots=programming-language-java&tabs=version-3)
   
> [!div class="nextstepaction"]
> [详细了解文本分析 API v3 SDK](./quickstarts/client-libraries-rest-api.md?tabs=version-3)

### <a name="named-entity-recognition-v3-public-preview"></a>命名实体识别 v3 公共预览版本

随着我们对文本中常规信息和个人信息实体的检测扩展，命名实体识别 (NER) v3 公共预览服务中现在可以使用更多实体类型。 此更新引入了[模型版本](concepts/model-versioning.md) `2020-02-01`，其中包括：

* 对以下常规实体类型的识别（仅限英语）：
    * PersonType
    * 产品
    * 事件
    * 地缘政治实体 (GPE)，作为“位置”下的子类型
    * 技能

* 对以下个人信息实体类型的识别（仅限英语）：
    * 人员
    * 组织
    * 年龄（作为“数量”下的子类型）
    * 日期（作为 DateTime 下的子类型）
    * 电子邮件 
    * 电话号码（仅限美国）
    * URL
    * IP 地址

### <a name="october-2019"></a>2019 年 10 月

#### <a name="named-entity-recognition-ner"></a>命名实体识别 (NER)

* 一个用于识别个人信息实体类型的[新终结点](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-Preview-2/operations/EntitiesRecognitionPii)（仅限英语）

* 分开用于[实体识别](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-Preview-2/operations/EntitiesRecognitionGeneral)和[实体链接](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-Preview-2/operations/EntitiesLinking)的终结点。

* [模型版本](concepts/model-versioning.md) `2019-10-01`，其中包括：
    * 扩展了对文本中实体的检测和分类。 
    * 对以下新实体类型的识别：
        * 电话号码
        * IP 地址

实体链接支持英语和西班牙语。 NER 语言支持因实体类型而异。

#### <a name="sentiment-analysis-v3-public-preview"></a>情绪分析 v3 公共预览版

* 用于分析情绪的[新终结点](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-Preview-2/operations/Sentiment)。
* [模型版本](concepts/model-versioning.md) `2019-10-01`，其中包括：

    * API 的文本分类和评分准确度以及细节方面显著改进。
    * 自动标记文本中不同的情绪。
    * 文档和语句级别的情绪分析和输出。 

支持英语 (`en`)、日语 (`ja`)、简体中文 (`zh-Hans`)、繁体中文 (`zh-Hant`)、法语 (`fr`)、意大利语 (`it`)、西班牙语 (`es`)、荷兰语 (`nl`)、葡萄牙语 (`pt`) 和德语 (`de`)，在以下区域提供：`Australia East`、`Central Canada`、`Central US`、`East Asia`、`East US`、`East US 2`、`North Europe`、`Southeast Asia`、`South Central US`、`UK South`、`West Europe` 和 `West US 2`。 

> [!div class="nextstepaction"]
> [详细了解情绪分析 v3](how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features)

## <a name="next-steps"></a>后续步骤

* [什么是文本分析 API？](overview.md)  
* [示例用户方案](text-analytics-user-scenarios.md)
* [情绪分析](how-tos/text-analytics-how-to-sentiment-analysis.md)
* [语言检测](how-tos/text-analytics-how-to-language-detection.md)
* [实体识别](how-tos/text-analytics-how-to-entity-linking.md)
* [关键短语提取](how-tos/text-analytics-how-to-keyword-extraction.md)
