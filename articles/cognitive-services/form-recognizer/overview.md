---
title: 什么是表单识别器？
titleSuffix: Azure Cognitive Services
description: Azure 表单识别器服务允许你从表单文档中识别和提取键/值对和表数据，以及从销售收据和名片中提取主要信息。
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: overview
ms.date: 11/23/2020
ms.author: pafarley
ms.custom: cog-serv-seo-aug-2020
keywords: 自动化数据处理, 文档处理, 自动化数据输入, 表单处理
ms.openlocfilehash: 95bbc33035ca99a64242274570be5c9263029aef
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/18/2021
ms.locfileid: "101094372"
---
# <a name="what-is-form-recognizer"></a>什么是表单识别器？

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Azure 表单识别器是一种认知服务，可让你使用机器学习技术构建自动化数据处理软件。 从文档中识别并提取文本、键/值对、选择标记、表和结构 &mdash; 该服务将输出结构化数据（其中包括原始文件中的关系、边界框、置信度等）。 可以快速获取根据特定内容定制的准确结果，无需进行繁琐的手动干预，也不需要具备丰富的数据科学专业知识。 使用表单识别器自动在应用程序中输入数据，并丰富文档搜索功能。

表单识别器由自定义文档处理模型、预生成的发票、收据和名片模型以及布局模型组成。 可以使用 REST API 或客户端库 SDK 调用表单识别器模型，以降低复杂性，并将该模型集成到工作流或应用程序中。

表单识别器包括下列服务：

* **[布局 API](#layout-api)** - 从文档中提取文本、选择标记和表结构及其边界框坐标。
* **[自定义模型](#custom-models)** - 从表单中提取文本、键/值对、选择标记和表数据。 这些模型都是用你自己的数据训练的，因此是针对你的表单量身定制的。
* [预生成模型](#prebuilt-models) - 使用预生成模型从独特的表单类型中提取数据。 以下预生成模型当前可用
  * [发票](./concept-invoices.md)
  * [销售收据](./concept-receipts.md)
  * [名片](./concept-business-cards.md)

## <a name="try-it-out"></a>试试看

若要试用表单识别器服务，请转到联机 UI 工具示例：
<!-- markdownlint-disable MD025 -->
# <a name="v21-preview"></a>[v2.1 预览版](#tab/v2-1)

> [!div class="nextstepaction"]
> [尝试使用表单识别器](https://fott-preview.azurewebsites.net/)

# <a name="v20"></a>[v2.0](#tab/v2-0)

> [!div class="nextstepaction"]
> [尝试使用表单识别器](https://fott.azurewebsites.net/)

---

若要试用表单识别器服务，你将需要一个 Azure 订阅（[免费创建一个](https://azure.microsoft.com/free/cognitive-services)）和一个[表单识别器资源](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer)终结点和密钥。

## <a name="layout-api"></a>布局 API

表单识别器可以使用高清光学字符识别 (OCR) 和增强型深度学习模型来从文档中提取文本、选择标记和表结构（与文本关联的行号和列号）。 有关详细信息，请参阅[布局](./concept-layout.md)概念指南。

:::image type="content" source="./media/tables-example.jpg" alt-text="表示例" lightbox="./media/tables-example.jpg":::

## <a name="custom-models"></a>自定义模式

表单识别器自定义模型会使用你自己的数据进行训练，并且你只需 5 个示例输入表单即可开始。 已训练的文档处理模型可以输出结构化数据（包括原始表单文档中的关系）。 训练模型后，可对其进行测试和重新训练，并最终使用它来根据需求提取其他表单中的数据。

训练自定义模型时，可以使用以下选项：在使用标记数据的情况下进行训练，以及在不使用标记数据的情况下进行训练。

### <a name="train-without-labels"></a>在没有标签的情况下训练

默认情况下，表单识别器使用非监督式学习来了解表单中字段与项之间的布局和关系。 当你提交了输入表单后，算法会按类型聚集表单，发现现有的键和表，以及将值关联到键，并将条目关联到表。 这不要求手动标记数据，也不要求进行繁琐的编码和维护，因此建议你先尝试此方法。

有关如何收集训练文档的提示，请参见[生成训练数据集](./build-training-data-set.md)。

### <a name="train-with-labels"></a>使用标签进行训练

当你使用标记的数据进行训练时，该模型会使用你提供的标记表单进行监督式学习，以便提取感兴趣的值。 这会生成表现更好的模型，并且可以生成处理复杂表单或所含值没有键的表单的模型。

表单识别器使用[布局 API](#layout-api) 来了解印刷体和手写体文本元素的预期大小和位置。 然后，它使用用户指定的标签来了解文档中的键/值关联。 建议使用五个手动标记的相同类型（相同结构）的表单，这样就可以在训练新模型时入门，并根据需要添加更多标记的数据以提高模型准确性。

[开始使用标签进行训练](./quickstarts/label-tool.md)


> [!VIDEO https://channel9.msdn.com/Shows/Docs-Azure/Azure-Form-Recognizer/player]


## <a name="prebuilt-models"></a>预生成的模型

表单识别器还包括用于对独特表单类型进行自动化数据处理的预生成模型。

### <a name="prebuilt-invoice-model"></a>预生成的发票模型
预生成的发票模型从各种格式的发票中提取数据，并返回结构化数据。 此模型提取的关键信息包括发票 ID、客户详细信息、供应商详细信息、收货地址、帐单邮寄地址、总额、税款、小计等。 此外，预生成的发票模型经过训练，可分析和返回发票上的所有文本和表格。 有关详细信息，请参阅[发票](./concept-invoices.md)概念指南。

:::image type="content" source="./media/overview-invoices.jpg" alt-text="发票示例" lightbox="./media/overview-invoices.jpg":::

### <a name="prebuilt-receipt-model"></a>预生成的收据模型

预生成的收据模型用于读取澳大利亚、加拿大、英国、印度和美国的英语销售收据 &mdash; 由餐馆、油站、零售店等使用的类型。 此模型可提取关键信息，如交易的时间和日期、商家信息、税额、明细项目和总计等。 此外，预生成的收据模型经过训练，可分析和返回收据中的所有文本。 有关详细信息，请参阅[收据](./concept-receipts.md)概念指南。

:::image type="content" source="./media/overview-receipt.jpg" alt-text="收据示例" lightbox="./media/overview-receipt.jpg":::

### <a name="prebuilt-business-cards-model"></a>预生成的名片模型

通过名片模型，你可以从英语名片中提取人员姓名、职务、地址、电子邮件、公司和电话号码等信息。 有关详细信息，请参阅[名片](./concept-business-cards.md)概念指南。

:::image type="content" source="./media/overview-business-card.jpg" alt-text="名片示例" lightbox="./media/overview-business-card.jpg":::


## <a name="get-started"></a>入门

使用[示例表单识别器工具](https://fott.azurewebsites.net/)或按照快速入门开始从表单中提取数据。 我们建议你在学习该技术时使用免费服务。 请记住，每月的免费页数限于 500。

* [客户端库/REST API 快速入门](./quickstarts/client-library.md)（所有语言，多种场景）
* Web UI 快速入门
  * [在有标签的情况下进行训练 - 示例标签工具](quickstarts/label-tool.md)
* REST 示例 (GitHub)
 * 从文档中提取文本、选择标记和表结构
    * [提取布局数据 - Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-layout.md)
  * 训练自定义模型并提取表单数据
    * [在没有标签的情况下进行训练 - Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-train-extract.md)
    * [在有标签的情况下进行训练 - Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-labeled-data.md)
  * 从发票提取数据
    * [提取发票数据 - Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-invoices.md)
  * 从销售收据提取数据
    * [提取收据数据 - Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-receipts.md)
  * 从名片提取数据
    * [提取名片数据 - Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-business-cards.md)

### <a name="review-the-rest-apis"></a>查看 REST API

使用以下 API 训练模型并提取表单中的结构化数据。

|名称 |说明 |
|---|---|
| **分析布局** | 分析作为流传入的文档，以从该文档中提取文本、选择标记、表和结构 |
| **训练自定义模型**| 使用相同类型的 5 个表单对新模型进行训练，以便分析你的表单。 将 _useLabelFile_ 参数设置为 `true`，以便使用手动标记的数据进行训练。 |
| **分析表单** |使用自定义模型分析作为流传入的表单，以从该表单中提取文本、键/值对和表。  |
| **分析发票** | 分析发票，以提取关键信息、表和其他发票文本。|
| **分析收据** | 分析回执文档，以提取关键信息和其他回执文本。|
| **分析名片** | 分析名片以提取关键信息和文本。|

# <a name="v21-preview"></a>[v2.1 预览版](#tab/v2-1)
请浏览 [REST API 参考文档](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeWithCustomForm)以了解详细信息。 如果熟悉旧版 API，请参阅[新增功能](./whats-new.md)一文，了解最近的变更。

# <a name="v20"></a>[v2.0](#tab/v2-0)
请浏览 [REST API 参考文档](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm)以了解详细信息。 如果熟悉旧版 API，请参阅[新增功能](./whats-new.md)一文，了解最近的变更。

---

## <a name="input-requirements"></a>输入要求

[!INCLUDE [input requirements](./includes/input-requirements.md)]

## <a name="deploy-on-premises-using-docker-containers"></a>使用 Docker 容器进行本地部署

[使用表单识别器容器（预览版）](form-recognizer-container-howto.md)在本地部署 API 功能。 借助此 Docker 容器，你能够将服务进一步引入数据，以满足合规性、安全性或其他操作目的。 

## <a name="service-availability-and-redundancy"></a>服务可用性和冗余性

### <a name="is-form-recognizer-service-zone-resilient"></a>表单识别器服务是否可在区域内复原？

是。 默认情况下，表单识别器服务可在区域内复原。

### <a name="how-do-i-configure-the-form-recognizer-service-to-be-zone-resilient"></a>如何将表单识别器服务配置为可在区域内复原？

客户无需配置即可启用区域复原能力。 表单识别器资源默认提供区域内复原能力，由服务自身进行管理。


## <a name="data-privacy-and-security"></a>数据隐私和安全性

与所有认知服务一样，使用表单识别器服务的开发人员应该了解针对客户数据的 Microsoft 政策。 请参阅 Microsoft 信任中心上的[“认知服务”页面](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices)来了解详细信息。

## <a name="next-steps"></a>后续步骤

完成[快速入门](quickstarts/client-library.md)，开始使用表单识别器以你选择的开发语言编写表单处理应用。
