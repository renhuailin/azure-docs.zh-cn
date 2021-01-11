---
title: 快速入门：表单识别器客户端库
titleSuffix: Azure Cognitive Services
description: 使用表单识别器客户端库创建一个表单处理应用，该应用从自定义文档中提取键/值对和表数据。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 09/21/2020
ms.author: pafarley
zone_pivot_groups: programming-languages-set-formre
ms.custom: devx-track-js, devx-track-csharp, cog-serv-seo-aug-2020
keywords: 表单处理, 自动化数据处理
ms.openlocfilehash: f01adc472f94d679366af50c136ddc020b79b811
ms.sourcegitcommit: 02ed9acd4390b86c8432cad29075e2204f6b1bc3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/29/2020
ms.locfileid: "97808566"
---
# <a name="quickstart-use-the-form-recognizer-client-library"></a>快速入门：使用表单识别器客户端库

开始使用以你选择的语言编写的表单识别器。 Azure 表单识别器是一种认知服务，可让你使用机器学习技术构建自动化数据处理软件。 从表单文档中识别并提取文本、键/值对和表数据 &mdash; 该服务将输出结构化数据（其中包括原始文件中的关系）。 请遵循以下步骤安装 SDK 包并试用基本任务的示例代码。 表单识别器客户端库当前面向表单识别器服务的 v2.0。

使用表单识别器客户端库执行以下操作：

* [识别表单内容](#recognize-form-content)
* [识别回执](#recognize-receipts)
* [识别名片](#recognize-business-cards)
* [识别发票](#recognize-invoices)
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