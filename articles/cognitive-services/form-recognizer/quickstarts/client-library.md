---
title: 快速入门：表单识别器客户端库或 REST API
titleSuffix: Azure Cognitive Services
description: 使用表单识别器客户端库或 REST API 创建一个表单处理应用，该应用从自定义文档中提取键值对和表数据。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 01/29/2021
ms.author: pafarley
zone_pivot_groups: programming-languages-set-formre
ms.custom: devx-track-js, devx-track-csharp, cog-serv-seo-aug-2020
keywords: 表单处理, 自动化数据处理
ms.openlocfilehash: cc18cc9e54a5a8887ecd3e079285ba5130b88278
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2021
ms.locfileid: "99218037"
---
# <a name="quickstart-use-the-form-recognizer-client-library-or-rest-api"></a>快速入门：使用表单识别器客户端库或 REST API

开始使用以你选择的语言编写的表单识别器。 Azure 表单识别器是一种认知服务，可让你使用机器学习技术构建自动化数据处理软件。 从表单文档中识别并提取文本、键/值对、选中标记、表数据等内容 &mdash; 该服务将输出结构化数据（其中包括原始文件中的关系）。 可通过 REST API 或 SDK 使用表单识别器。 请遵循以下步骤安装 SDK 包并试用基本任务的示例代码。 

使用表单识别器执行以下操作：

* [分析布局](#analyze-layout)
* [分析回执](#analyze-receipts)
* [分析名片](#analyze-business-cards)
* [分析发票](#analyze-invoices)
* [训练自定义模型](#train-a-custom-model)
* [使用自定义模型分析表单](#analyze-forms-with-a-custom-model)
* [管理自定义模型](#manage-your-custom-models)

::: zone pivot="programming-language-csharp"

[!INCLUDE [C# SDK quickstart](../includes/quickstarts/csharp-sdk.md)]

::: zone-end

::: zone pivot="programming-language-java"

[!INCLUDE [Java SDK quickstart](../includes/quickstarts/java-sdk.md)]

::: zone-end

::: zone pivot="programming-language-javascript"

[!INCLUDE [NodeJS SDK quickstart](../includes/quickstarts/javascript-sdk.md)]

::: zone-end

::: zone pivot="programming-language-python"

[!INCLUDE [Python SDK quickstart](../includes/quickstarts/python-sdk.md)]

::: zone-end

::: zone pivot="programming-language-rest-api"

[!INCLUDE [REST API quickstart](../includes/quickstarts/rest-api.md)]

::: zone-end
