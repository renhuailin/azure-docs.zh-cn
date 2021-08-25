---
title: 什么是 Azure 表单识别器？
titleSuffix: Azure Applied AI Services
description: Azure 表单识别器服务允许你从表单文档中识别和提取键/值对和表数据，以及从销售收据和名片中提取主要信息。
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: overview
ms.date: 03/15/2021
ms.author: lajanuar
ms.custom: cog-serv-seo-aug-2020
keywords: 自动化数据处理, 文档处理, 自动化数据输入, 表单处理
ms.openlocfilehash: 2231832f87b2caf085f5d5278a2b291cda25b606
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122326775"
---
# <a name="what-is-azure-form-recognizer"></a>什么是 Azure 表单识别器？

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Azure 表单识别器是 [Azure 应用 AI 服务](../../applied-ai-services/index.yml)的一部分，可让你使用机器学习技术构建自动化数据处理软件。 从文档中识别并提取文本、键/值对、选择标记、表和结构 &mdash; 该服务将输出结构化数据（其中包括原始文件中的关系、边界框、置信度等）。 可以快速获取根据特定内容定制的准确结果，无需进行繁琐的手动干预，也不需要具备丰富的数据科学专业知识。 使用表单识别器自动在应用程序中输入数据，并丰富文档搜索功能。

表单识别器由自定义文档处理模型、预生成的发票、收据、身份证和名片模型以及布局模型组成。 可以使用 REST API 或客户端库 SDK 调用表单识别器模型，以降低复杂性，并将该模型集成到工作流或应用程序中。

本文档包含以下文章类型：

* [**概念**](concept-layout.md)对服务的功能和特性进行了深入说明。
* [**快速入门**](quickstarts/client-library.md)介绍了入门说明，指导您完成向服务发出请求。
* [**操作指南**](build-training-data-set.md)包含以更具体的方式或自定义方式使用服务的说明。
* [**教程**](tutorial-ai-builder.md)是较长的指南，向您演示了如何在更广泛的业务解决方案中使用该服务作为组件。

## <a name="form-recognizer-features"></a>表单识别器功能

利用表单识别器，可通过以下功能轻松提取和分析文档数据：

### <a name="layout"></a>[布局](concept-layout.md)

从文档中提取文本、选择标记和表结构及其边界框坐标。

表单识别器可以使用高清光学字符识别 (OCR) 和增强型深度学习模型来从文档中提取文本、选择标记和表结构（与文本关联的行号和列号）。

:::image type="content" source="./media/tables-example.jpg" alt-text="表示例" lightbox="./media/tables-example.jpg":::

### <a name="custom-models"></a>[自定义模式](concept-custom.md)

从表单中提取文本、键/值对、选择标记和表数据。 这些模型都是用你自己的数据训练的，因此是针对你的表单量身定制的。

表单识别器自定义模型会使用你自己的数据进行训练，并且你只需 5 个示例输入表单即可开始。 已训练的文档处理模型可以输出结构化数据（包括原始表单文档中的关系）。 训练模型后，可对其进行测试和重新训练，并最终使用它来根据需求提取其他表单中的数据。

训练自定义模型时，可以使用以下选项：在使用标记数据的情况下进行训练，以及在不使用标记数据的情况下进行训练。

#### <a name="train-without-labels"></a>在没有标签的情况下训练

表单识别器使用非监督式学习来理解表单中字段与项之间的布局和关系。 当你提交了输入表单后，算法会按类型聚集表单，发现现有的键和表，以及将值关联到键，并将条目关联到表。 无标签训练并不要求手动标记数据，也不要求进行繁琐的编码和维护，因此建议你先尝试此方法。

有关如何收集训练文档的提示，请参见[生成训练数据集](./build-training-data-set.md)。

#### <a name="train-with-labels"></a>使用标签进行训练

当你使用带标签数据进行训练时，该模型会通过监督式学习使用提供的带标签表单提取关注的值。 带标签的数据会生成表现更好的模型，并且可以生成处理复杂表单或所含值没有键的表单的模型。

表单识别器使用[布局 API](#layout) 来习得印刷体和手写体文本元素的预期大小和位置并提取表。 然后，它使用用户指定的标签来习得文档中的键/值关联和表。 建议使用五个手动标记的相同类型（相同结构）的表单，这样就可以在训练新模型时入门，并根据需要添加更多标记的数据以提高模型准确性。 借助表单识别器可以训练一个模型，以使用监督学习功能提取键值对和表。

[开始使用标签进行训练](label-tool.md)

> [!VIDEO https://channel9.msdn.com/Shows/Docs-Azure/Azure-Form-Recognizer/player]


### <a name="prebuilt-models"></a>预生成的模型

 表单识别器还包括预生成的模型，可用于收据、名片、发票和身份证件的自动化数据处理。

### <a name="receipts"></a>[Receipts](concept-receipts.md)

预生成的收据模型用于读取澳大利亚、加拿大、英国、印度和美国的英语销售收据 &mdash; 由餐馆、油站、零售店等使用的类型。 此模型可提取关键信息，如交易的时间和日期、商家信息、税额、明细项目和总计等。 此外，预生成的收据模型经过训练，可分析和返回收据中的所有文本。

:::image type="content" source="./media/overview-receipt.jpg" alt-text="收据示例" lightbox="./media/overview-receipt.jpg":::

### <a name="business-cards"></a>[名片](concept-business-cards.md)

通过名片模型，你可以从英语名片中提取人员姓名、职务、地址、电子邮件、公司和电话号码等信息。

:::image type="content" source="./media/overview-business-card.jpg" alt-text="名片示例" lightbox="./media/overview-business-card.jpg":::

### <a name="invoices"></a>[发票](concept-invoices.md)

预生成的发票模型从各种格式的发票中提取数据，并返回结构化数据。 此模型提取的关键信息包括发票 ID、客户详细信息、供应商详细信息、收货地址、帐单邮寄地址、合计、税款、小计、细目，等等。 此外，预生成的发票模型经过训练，可分析和返回发票上的所有文本和表格。

:::image type="content" source="./media/overview-invoices.jpg" alt-text="发票示例" lightbox="./media/overview-invoices.jpg":::

### <a name="identity-documents"></a>[标识文档](concept-identification-cards.md)

使用身份证件 (ID) 模型可以从全球护照和美国驾照中提取关键信息。 它可以提取文档 ID、出生日期、过期日期、姓名、国家、地区、计算机可读区域等数据。

:::image type="content" source="./media/id-example-drivers-license.jpg" alt-text="示例身份卡" lightbox="./media/overview-id.jpg":::

## <a name="get-started"></a>入门

使用示例表单识别器工具可以试用布局模型、预生成模型，并训练文档的自定义模型。 若要试用表单识别器服务，你将需要一个 Azure 订阅（[免费创建一个](https://azure.microsoft.com/free/cognitive-services)）和一个[表单识别器资源](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer)终结点和密钥 。

>
> [!div class="nextstepaction"]
> [尝试使用表单识别器](https://aka.ms/fott-2.1-ga/)
>

遵循[客户端库/REST API 快速入门](./quickstarts/client-library.md)开始从文档中提取数据。 我们建议你在学习该技术时使用免费服务。 请记住，每月的免费页数限于 500。

请浏览 [REST API 参考文档](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeWithCustomForm)以了解详细信息。 如果熟悉旧版 API，请参阅[新增功能](./whats-new.md)一文，了解最近的变更。

## <a name="input-requirements"></a>输入要求

[!INCLUDE [input requirements](./includes/input-requirements.md)]

## <a name="service-availability-and-redundancy"></a>服务可用性和冗余性

### <a name="is-form-recognizer-service-zone-resilient"></a>表单识别器服务是否可在区域内复原？

是。 默认情况下，表单识别器服务可在区域内复原。

### <a name="how-do-i-configure-the-form-recognizer-service-to-be-zone-resilient"></a>如何将表单识别器服务配置为可在区域内复原？

客户无需配置即可启用区域复原能力。 表单识别器资源默认提供区域内复原能力，由服务自身进行管理。

## <a name="data-privacy-and-security"></a>数据隐私和安全性

* 与所有认知服务一样，使用表单识别器服务的开发人员应该了解针对客户数据的 Microsoft 政策。 请参阅 Microsoft 信任中心上的[“认知服务”页面](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices)来了解详细信息。

* 表单识别器不会在客户部署服务实例的区域之外存储或处理客户数据。

## <a name="next-steps"></a>后续步骤

试用我们的在线工具并阅读快速入门，以详细了解表单识别器服务。

* [**表单识别器工具**](https://aka.ms/fott-2.1-ga)
* [**客户端库和 REST API 快速入门**](quickstarts/client-library.md)