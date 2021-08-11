---
title: 快速入门：使用文本分析客户端库进行文本挖掘
titleSuffix: Azure Cognitive Services
description: 使用本快速入门，通过 Azure 认知服务的文本分析 API 执行情绪分析等操作。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 07/08/2021
ms.author: aahi
keywords: 文本挖掘, 情绪分析, 文本分析
ms.custom: devx-track-python, devx-track-js, devx-track-csharp, cog-serv-seo-aug-2020
zone_pivot_groups: programming-languages-text-analytics
ms.openlocfilehash: 69026d9abd8e0f3a978d9c327e9cb9c2e0140d04
ms.sourcegitcommit: e0ef8440877c65e7f92adf7729d25c459f1b7549
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2021
ms.locfileid: "113565771"
---
# <a name="quickstart-use-the-text-analytics-client-library-and-rest-api"></a>快速入门：使用文本分析客户端库和 REST API

参考本文开始使用文本分析客户端库和 REST API。 按照以下步骤使用示例代码挖掘文本：

* 情绪分析
* 观点挖掘
* 语言检测
* 实体识别
* 个人身份信息识别
* 关键短语提取


::: zone pivot="programming-language-csharp"

> [!IMPORTANT]
> * 文本分析 API 的最新稳定版本为 `3.1`。
>    * 确保只按所用版本的说明操作。
> * 为了简单起见，本文中的代码使用了同步方法和不受保护的凭据存储。 对于生产方案，我们建议使用批处理的异步方法来提高性能和可伸缩性。 请参阅下面的参考文档。
> * 如果要使用运行状况文本分析或异步操作，请参阅 Github 上的 [C#](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics)、[Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/) 或 [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics) 示例

[!INCLUDE [C# quickstart](../includes/quickstarts/csharp-sdk.md)]

::: zone-end

::: zone pivot="programming-language-java"

> [!IMPORTANT]
> * 文本分析 API 的最新稳定版本为 `3.1`。
> * 为了简单起见，本文中的代码使用了同步方法和不受保护的凭据存储。 对于生产方案，我们建议使用批处理的异步方法来提高性能和可伸缩性。 请参阅下面的参考文档。
如果要使用运行状况文本分析或异步操作，请参阅 Github 上的 [C#](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics)、[Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/) 或 [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics) 示例

[!INCLUDE [Java quickstart](../includes/quickstarts/java-sdk.md)]

::: zone-end

::: zone pivot="programming-language-javascript"

> [!IMPORTANT]
> * 文本分析 API 的最新稳定版本为 `3.1`。
>    * 确保只按所用版本的说明操作。
> * 为了简单起见，本文中的代码使用了同步方法和不受保护的凭据存储。 对于生产方案，我们建议使用批处理的异步方法来提高性能和可伸缩性。 请参阅下面的参考文档。
> * 还可[在浏览器中](https://github.com/Azure/azure-sdk-for-js/blob/master/documentation/Bundling.md)运行此版本的文本分析客户端库。

[!INCLUDE [NodeJS quickstart](../includes/quickstarts/nodejs-sdk.md)]

::: zone-end

::: zone pivot="programming-language-python"

> [!IMPORTANT]
> * 文本分析 API 的最新稳定版本为 `3.1`。
>    * 确保只按所用版本的说明操作。
> * 为了简单起见，本文中的代码使用了同步方法和不受保护的凭据存储。 对于生产方案，我们建议使用批处理的异步方法来提高性能和可伸缩性。 请参阅下面的参考文档。 如果要使用运行状况文本分析或异步操作，请参阅 Github 上的 [C#](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics)、[Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/) 或 [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics) 示例

[!INCLUDE [Python quickstart](../includes/quickstarts/python-sdk.md)]

::: zone-end

::: zone pivot="rest-api"

> [!IMPORTANT]
> * 文本分析 API 的最新稳定版本为 `3.1`。
>    * 确保只按所用版本的说明操作。

[!INCLUDE [REST API quickstart](../includes/quickstarts/rest-api.md)]

::: zone-end

## <a name="clean-up-resources"></a>清理资源

如果想要清理并删除认知服务订阅，可以删除资源或资源组。 删除资源组同时也会删除与之相关联的任何其他资源。

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [探索解决方案](../text-analytics-user-scenarios.md#analyze-recorded-inbound-customer-calls)

* [文本分析概述](../overview.md)
* [情绪分析](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [实体识别](../how-tos/text-analytics-how-to-entity-linking.md)
* [检测语言](../how-tos/text-analytics-how-to-keyword-extraction.md)
* [语言识别](../how-tos/text-analytics-how-to-language-detection.md)
