---
title: 什么是 Azure 表单识别器？ （已更新）
titleSuffix: Azure Applied AI Services
description: Azure 表单识别器服务允许你从表单文档中识别和提取键/值对和表数据，以及从销售收据和名片中提取主要信息。
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: overview
ms.date: 10/07/2021
ms.author: lajanuar
recommendations: false
keywords: 自动化数据处理, 文档处理, 自动化数据输入, 表单处理
ms.openlocfilehash: 272104444ca3389f69c639fba0984552b93c5893
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2021
ms.locfileid: "129712101"
---
<!-- markdownlint-disable MD033 -->
<!-- markdownlint-disable MD024 -->
# <a name="what-is-azure-form-recognizer"></a>什么是 Azure 表单识别器？

Azure 表单识别器是一种 [Azure 应用的 AI 服务](../../applied-ai-services/index.yml)，使你能够使用机器学习技术构建自动化文档处理软件。 表单识别器分析表单和文档，提取文本和数据，将字段关系映射为键值对，并返回结构化 JSON 输出。 可以快速获取根据特定内容定制的准确结果，无需进行繁琐的手动干预，也不需要具备丰富的数据科学专业知识。 使用表单识别器自动处理应用程序和工作流中的数据，增强数据驱动的策略，并丰富文档搜索功能。

表单识别器可以轻松识别、提取和分析以下文档数据：

* 表结构和内容。
* 表单元素和字段值。
* 类型化的和手写的字母数字文本。
* 元素之间的关系。
* 键/值对
* 带边界框坐标的元素位置。

本文档包含以下文章类型：

* [**概念**](concept-layout.md)对服务的功能和特性进行了深入说明。
* [**快速入门**](quickstarts/try-sdk-rest-api.md)介绍了入门说明，指导您完成向服务发出请求。
* [**操作指南**](build-training-data-set.md)包含以更具体的方式或自定义方式使用服务的说明。
* [**教程**](tutorial-ai-builder.md)是较长的指南，向您演示了如何在更广泛的业务解决方案中使用该服务作为组件。

## <a name="form-recognizer-features-and-development-options"></a>表单识别器功能和开发选项

### <a name="form-recognizer-ga-v21"></a>[表单识别器 GA (v2.1)](#tab/v2-1)

表单识别器服务 v2.1 支持以下功能。 请使用下表中的链接详细了解每项功能并浏览 API 参考信息。

| 功能 | 说明 | 开发选项 |
|----------|--------------|-------------------------|
|[**布局 API**](concept-layout.md) | 从表单和文档中提取和分析文本、选择标记和表结构及其边界框的坐标。 | <ul><li>[**表单识别器标签工具**](https://fott-2-1.azurewebsites.net/layout-analyze)</li><li>[**REST API**](quickstarts/try-sdk-rest-api.md?pivots=programming-language-rest-api#analyze-layout)</li><li>[Client-library SDK](quickstarts/try-sdk-rest-api.md)</li><li>[表单识别器 Docker 容器](containers/form-recognizer-container-install-run.md?branch=main&tabs=layout#run-the-container-with-the-docker-compose-up-command)</li></ul>|
|[**名片模型**](concept-business-card.md) | 自动提取和处理名片中的重要信息。| <ul><li>[**表单识别器标签工具**](https://fott-2-1.azurewebsites.net/prebuilts-analyze)</li><li>[**REST API**](quickstarts/try-sdk-rest-api.md?pivots=programming-language-rest-api#analyze-business-cards)</li><li>[Client-library SDK](quickstarts/try-sdk-rest-api.md)</li><li>[表单识别器 Docker 容器](containers/form-recognizer-container-install-run.md?tabs=business-card#run-the-container-with-the-docker-compose-up-command)</li></ul>|
|[**ID 文档模型**](concept-id-document.md) | 自动提取和处理美国驾驶证和国际护照中的重要信息。| <ul><li>[**表单识别器标签工具**](https://fott-2-1.azurewebsites.net/prebuilts-analyze)</li><li>[**REST API**](quickstarts/try-sdk-rest-api.md?pivots=programming-language-rest-api#analyze-identity-id-documents)</li><li>[Client-library SDK](quickstarts/try-sdk-rest-api.md)</li><li>[表单识别器 Docker 容器](containers/form-recognizer-container-install-run.md?tabs=id-document#run-the-container-with-the-docker-compose-up-command)</li></ul>|
|[**发票模型**](concept-invoice.md) | 自动提取和处理销售发票中的重要信息。 | <ul><li>[**表单识别器标签工具**](https://fott-2-1.azurewebsites.net/prebuilts-analyze)</li><li>[**REST API**](quickstarts/try-sdk-rest-api.md?pivots=programming-language-rest-api#analyze-invoices)</li><li>[Client-library SDK](quickstarts/try-sdk-rest-api.md)</li><li>[表单识别器 Docker 容器](containers/form-recognizer-container-install-run.md?tabs=invoice#run-the-container-with-the-docker-compose-up-command)</li></ul>|
|[**收据模型**](concept-receipt.md) | 自动提取和处理销售收据中的重要信息。| <ul><li>[**表单识别器标签工具**](https://fott-2-1.azurewebsites.net/prebuilts-analyze)</li><li>[**REST API**](quickstarts/try-sdk-rest-api.md?pivots=programming-language-rest-api#analyze-receipts)</li><li>[Client-library SDK](quickstarts/try-sdk-rest-api.md)</li><li>[表单识别器 Docker 容器](containers/form-recognizer-container-install-run.md?tabs=receipt#run-the-container-with-the-docker-compose-up-command)</li></ul>|
|[**自定义模型**](concept-business-card.md) | 提取和分析与特定业务数据和用例相关的表单和文档中的数据。| <ul><li>[**表单识别器标签工具**](https://fott-2-1.azurewebsites.net)</li><li>[**REST API**](quickstarts/try-sdk-rest-api.md?pivots=programming-language-rest-api#analyze-forms-with-a-custom-model)</li><li>[Client-library SDK](quickstarts/try-sdk-rest-api.md)</li><li>[表单识别器 Docker 容器](containers/form-recognizer-container-install-run.md?tabs=custom#run-the-container-with-the-docker-compose-up-command)</li></ul>|

### <a name="form-recognizer-preview-v30"></a>[表单识别器 (v3.0)](#tab/v3-0)

>[!NOTE]
> 表单识别器工作室当前为公共预览版。 某些功能可能不受支持或者受限。

表单识别器服务 v3.0 支持以下功能和开发选项。 请使用下表中的链接详细了解每项功能并浏览 API 参考信息。

| 功能 | 说明 | 开发选项 |
|----------|--------------|-------------------------|
|[🆕 常规文档模型](concept-general-document.md)|提取文本、表、结构、键值对和命名实体。|<ul ><li>[表单识别器工作室](https://formrecognizer.appliedai.azure.com)</li><li>[**REST API**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument)</li><li>[**C# SDK**](quickstarts/try-v3-csharp-sdk.md)</li><li>[**Python SDK**](quickstarts/try-v3-python-sdk.md)</li></ul> |
|[**布局模型**](concept-layout.md) | 从表单和文档中提取文本、选择标记和表结构及其边界框坐标。</br></br> 布局 API 已更新为预生成的模型。 | <ul><li>[表单识别器工作室](https://formrecognizer.appliedai.azure.com)</li><li>[**REST API**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument)</li><li>[**C# SDK**](quickstarts/try-v3-csharp-sdk.md)</li><li>[**Python SDK**](quickstarts/try-v3-python-sdk.md)</li></ul>|
|[自定义模型（已更新）](concept-custom.md) | 提取和分析与特定业务数据和用例相关的表单和文档中的数据。</br></br>自定义模型 API v3.0 支持自定义表单的签名检测。</li></ul>| <ul><li>[表单识别器工作室](https://fott-2-1.azurewebsites.net)</li><li>[**REST API**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument)</li><li>[**C# SDK**](quickstarts/try-v3-csharp-sdk.md)</li><li>[**Python SDK**](quickstarts/try-v3-python-sdk.md)</li></ul>|
|[收据模型（已更新）](concept-receipt.md) | 自动提取和处理销售收据中的重要信息。</br></br>收据模型 v3.0 支持处理单页酒店收据。| <ul><li>[表单识别器工作室](https://formrecognizer.appliedai.azure.com)</li><li>[**REST API**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument)</li><li>[**C# SDK**](quickstarts/try-v3-csharp-sdk.md)</li><li>[**Python SDK**](quickstarts/try-v3-python-sdk.md)</li></ul>|
|[ID 文档模型（已更新）](concept-id-document.md) |自动提取和处理美国驾驶证和国际护照中的重要信息。</br></br>预生成的 ID 文档 API 支持从美国驾驶证提取有关签名、限制和车辆分类的信息。 |<ul><li> [表单识别器工作室](https://formrecognizer.appliedai.azure.com)</li><li>[**REST API**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument)</li><li>[**C# SDK**](quickstarts/try-v3-csharp-sdk.md)</li><li>[**Python SDK**](quickstarts/try-v3-python-sdk.md)</li></ul>|
|[**发票模型**](concept-invoice.md) | 自动提取和处理销售发票中的重要信息。 | <ul><li>[表单识别器工作室](https://formrecognizer.appliedai.azure.com)</li><li>[**REST API**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument)</li><li>[**C# SDK**](quickstarts/try-v3-csharp-sdk.md)</li><li>[**Python SDK**](quickstarts/try-v3-python-sdk.md)</li></ul>|
|[**名片模型**](concept-business-card.md) |自动提取和处理名片中的重要信息。| <ul><li>[表单识别器工作室](https://formrecognizer.appliedai.azure.com)</li><li>[**REST API**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument)</li><li>[**C# SDK**](quickstarts/try-v3-csharp-sdk.md)</li><li>[**Python SDK**](quickstarts/try-v3-python-sdk.md)</li></ul>|

---

## <a name="prerequisites"></a>先决条件

* 你将需要 Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/cognitive-services)。

* 拥有订阅后，请在 Azure 门户中创建[表单识别器资源](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer)。 可以使用免费定价层 (F0) 试用该服务，然后再升级到付费层进行生产。

* 最后，需要从 Azure 门户检索资源终结点 URL 和 API 密钥，以试用表单识别器服务 ：

  :::image type="content" source="media/containers/keys-and-endpoint.png" alt-text="屏幕截图：Azure 门户中的密钥和终结点窗口。":::

 ## <a name="form-recognizer-workflows"></a>表单识别器工作流

| 文档类型 | 注意事项 | 解决方案 |
| -----------------|-------------------| ----------|
|<ul><li>**发票**</li><li>**回执**</li><li>名片</li></ul>| 你的发票、收据或名片文档是否由英语文本组成？ | <ul><li>是 → [发票](concept-invoice.md)、[收据](concept-receipt.md)或[名片](concept-business-card.md)  </li><li>否 → [布局](concept-layout.md)或[通用文档（预览）](concept-general-document.md)模型 </li></ul>|
|<ul><li>ID 文档</li></ul>| 你的 ID 文档是美国驾照还是国际护照？| <ul><li>是 → [ID 文档](concept-id-document.md)模型</li><li>否 → [布局](concept-layout.md)或[通用文档（预览）](concept-general-document.md)模型 </li></ul>|
|<ul><li>表单或文档 </li></ul>| 你的表单或文档采用的是通常在你的业务或行业中使用的行业标准格式吗？| <ul><li>是 → [布局](concept-id-document.md)或[通用文档（预览）](concept-general-document.md)模型 </li><li>否 → [训练或生成自定义模型](concept-layout.md) 

## <a name="data-privacy-and-security"></a>数据隐私和安全性

 与所有认知服务一样，使用表单识别器服务的开发人员应该了解针对客户数据的 Microsoft 政策。 请参阅我们的[表单识别器的数据、隐私和安全](/legal/cognitive-services/form-recognizer/fr-data-privacy-security)页。 

## <a name="next-steps"></a>后续步骤

### <a name="form-recognizer-v21"></a>[表单识别器 v2.1](#tab/v2-1)

> [!div class="checklist"]
>
> * 试用我们的[示例标记联机工具](https://aka.ms/fott-2.1-ga/)
> * 遵循[客户端库/REST API 快速入门](./quickstarts/try-sdk-rest-api.md)开始从文档中提取数据。 我们建议你在学习该技术时使用免费服务。 请记住，每月的免费页数限于 500。
> * 请浏览 [REST API 参考文档](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeWithCustomForm)以了解详细信息。 
> * 如果熟悉旧版 API，请参阅[新增功能](./whats-new.md)一文，了解最近的变更。

### <a name="form-recognizer-v30"></a>[表单识别器 v3.0](#tab/v3-0)

> [!div class="checklist"]
>
> * 试用我们的[表单识别器工作室](https://formrecognizer.appliedai.azure.com)
> * 请浏览 [REST API 参考文档](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument)以了解详细信息。 
> * 如果熟悉旧版 API，请参阅[新增功能](./whats-new.md)一文，了解最近的变更。

---
