---
title: 表单识别器工作室 | 预览版
titleSuffix: Azure Applied AI Services
description: 概念：使用表单识别器工作室（预览版）进行表单和文档处理、数据提取和分析
author: sanjeev3
manager: netahw
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 10/11/2021
ms.author: sajagtap
ms.openlocfilehash: e476f6da88688cb055c741f0888001d864908bae
ms.sourcegitcommit: 54e7b2e036f4732276adcace73e6261b02f96343
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/12/2021
ms.locfileid: "129812377"
---
# <a name="form-recognizer-studio--preview"></a>表单识别器工作室 | 预览版

>[!NOTE]
> 表单识别器工作室目前为公共预览版。 某些功能可能不受支持或者受限。

[表单识别器工作室预览版](https://formrecognizer.appliedai.azure.com/)是一个在线工具，用于在应用程序中从表单识别器服务直观地浏览、了解和集成功能。 请参考[表单识别器工作室快速入门](quickstarts/try-v3-form-recognizer-studio.md)使用示例文档或你自己的文档开始探索预生成模型。 使用 [Python SDK 预览版](quickstarts/try-v3-python-sdk.md)和其他快速入门，创建项目以生成自定义表单模型，并在应用程序中引用模型。

下图显示了“发票”预生成模型功能的工作方式。

:::image border="true" type="content" source="media/quickstarts/prebuilt-get-started-v2.gif" alt-text="表单识别器预生成示例":::

## <a name="form-recognizer-studio-features"></a>表单识别器工作室功能

工作室中提供以下表单识别器服务功能。

* 布局：尝试使用表单识别器的“布局”功能从文档（PDF 和 TIFF）和图像（JPG、PNG 和 BMP）提取文本、表、选择标记和结构信息。 从[工作室布局快速入门](quickstarts/try-v3-form-recognizer-studio.md#layout)开始。 使用示例文档和你自己的文档进行探索。 使用交互式可视化效果和 JSON 输出来了解该功能的工作原理。 参阅[布局概述](concept-layout.md)了解详细信息，并通过[布局 Python SDK 快速入门](quickstarts/try-v3-python-sdk.md#try-it-layout-model)获取入门知识。

* 预生成模型：使用 Azure 表单识别器预生成模型，可以在应用和流中添加智能表单处理，而无需训练和生成自己的模型。 从[工作室预生成模型快速入门](quickstarts/try-v3-form-recognizer-studio.md#prebuilt-models)开始。 使用示例文档和你自己的文档进行探索。 使用交互式可视化效果、提取的字段列表和 JSON 输出来了解该功能的工作原理。 参阅[模型概述](concept-model-overview.md)了解详细信息，并通过[预生成发票 Python SDK 快速入门](quickstarts/try-v3-python-sdk.md#try-it-prebuilt-invoice-model)获取入门知识。

* 自定义模型：借助表单识别器的自定义模型，可以从使用你的数据训练的并根据你的表单和文档定制的模型中提取字段和值。 创建独立的自定义模型，或者组合两个或更多个自定义模型来创建组合模型，以从多种表单类型中提取数据。 从[工作室自定义模型快速入门](quickstarts/try-v3-form-recognizer-studio.md#custom-model-basics)开始。  使用联机向导、标记界面、训练步骤和可视化效果来了解该功能的工作原理。 使用示例文档测试自定义模型，并通过迭代改进模型。 参阅[自定义模型概述](concept-custom.md)了解详细信息，并使用[表单识别器 v3.0 预览版迁移指南](v3-migration-guide.md)开始将新模型与应用程序集成。

* 自定义模型：标记功能：创建表单识别器自定义模型需要标识所要提取的字段，并在训练自定义模型之前使用这些字段来标记文档中的内容。 通常可以借助用户界面来标记文本、选择标记、表格数据和其他内容类型，以简化训练工作流。 例如，可以使用[标记为表](quickstarts/try-v3-form-recognizer-studio.md#labeling-as-tables)和[标记为进行签名检测](quickstarts/try-v3-form-recognizer-studio.md#labeling-for-signature-detection)快速入门来了解表单识别器工作室中的标记体验。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [表单识别器工作室（预览版）入门](https://formrecognizer.appliedai.azure.com)

