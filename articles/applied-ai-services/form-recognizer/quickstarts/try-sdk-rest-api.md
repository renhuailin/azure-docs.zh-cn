---
title: 快速入门：表单识别器客户端库或 REST API
titleSuffix: Azure Applied AI Services
description: 使用表单识别器客户端库或 REST API 创建一个表单处理应用，该应用从自定义文档中提取键值对和表数据。
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 10/07/2021
ms.author: lajanuar
zone_pivot_groups: programming-languages-set-formre
ms.custom: devx-track-js, devx-track-csharp, cog-serv-seo-aug-2020
keywords: 表单处理, 自动化数据处理
ms.openlocfilehash: a50fb3a47d8ec4e76673654107a1c391eafdf4a7
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2021
ms.locfileid: "129716678"
---
# <a name="quickstart-get-started-with-the-client-library-sdks-or-rest-api"></a>快速入门：开始使用客户端库 SDK 或 REST API

开始通过你选择的编程语言来使用 Azure 表单识别器。 Azure 表单识别器是一种 [Azure 应用 AI 服务](../../../applied-ai-services/index.yml)，可让你使用机器学习技术构建自动化数据处理软件。 从表单文档中识别并提取文本、键/值对、选中标记、表数据等内容 &mdash; 该服务将输出结构化数据（其中包括原始文件中的关系）。 可通过 REST API 或 SDK 使用表单识别器。 我们建议你在学习该技术时使用免费服务。 请记住，每月的免费页数限于 500。

你可使用以下 API 提取表单和文档中的结构化数据：

* [对客户端进行身份验证](#authenticate-the-client)
* [分析布局](#analyze-layout)
* [分析回执](#analyze-receipts)
* [分析名片](#analyze-business-cards)
* [分析发票](#analyze-invoices)
* [分析 ID 文档](#analyze-id-documents)
* [训练自定义模型](#train-a-custom-model)
* [使用自定义模型分析表单](#analyze-forms-with-a-custom-model)
* [管理自定义模型](#manage-custom-models)

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