---
title: 快速入门：表单识别器客户端库或 REST API
titleSuffix: Azure Applied AI Services
description: 使用表单识别器客户端库或 REST API 创建一个表单处理应用，该应用从自定义文档中提取键值对和表数据。
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 04/14/2021
ms.author: lajanuar
zone_pivot_groups: programming-languages-set-formre
ms.custom: devx-track-js, devx-track-csharp, cog-serv-seo-aug-2020
keywords: 表单处理, 自动化数据处理
ms.openlocfilehash: 34b2e591c8d050a168e82bb40c5167823c136e52
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/25/2021
ms.locfileid: "110374139"
---
# <a name="quickstart-use-the-form-recognizer-client-library-or-rest-api"></a>快速入门：使用表单识别器客户端库或 REST API

开始使用以你选择的开发语言编写的表单识别器。 Azure 表单识别器是一种认知服务，可让你使用机器学习技术构建自动化数据处理软件。 从表单文档中识别并提取文本、键/值对、选中标记、表数据等内容 &mdash; 该服务将输出结构化数据（其中包括原始文件中的关系）。 可通过 REST API 或 SDK 使用表单识别器。 我们建议你在学习该技术时使用免费服务。 请记住，每月的免费页数限于 500。

你可使用以下 API 提取表单和文档中的结构化数据：

|名称 |说明 |
|---|---|
| [分析布局](#analyze-layout) | 分析作为流传入的文档，以从该文档中提取文本、选择标记、表和结构 |
| **[分析回执](#analyze-receipts)** | 分析回执文档，以提取关键信息和其他回执文本。|
| **[分析名片](#analyze-business-cards)** | 分析名片以提取关键信息和文本。|
| **[分析发票](#analyze-invoices)** | 分析发票，以提取关键信息、表和其他发票文本。|
| **[分析标识文档](#analyze-identity-documents)** | 分析身份证文档以提取关键信息和其他身份证文本。|
| **[训练自定义模型](#train-a-custom-model)**| 使用相同类型的 5 个表单对新模型进行训练，以便分析你的表单。 将 _useLabelFile_ 参数设置为 `true`，以便使用手动标记的数据进行训练。 |
| **[使用自定义模型分析表单](#analyze-forms-with-a-custom-model)**|使用自定义模型分析作为流传入的表单，以从该表单中提取文本、键/值对和表。  |
|**[管理自定义模型](#manage-custom-models)**| 可以检查表单识别器帐户中的自定义模型数量，使用模型 ID 获取特定模型，以及从帐户中删除模型。|

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
