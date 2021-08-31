---
title: 区域 - 语音服务
titleSuffix: Azure Cognitive Services
description: 语音服务的可用区域和终结点列表，包括语音转文本、文本转语音和语音翻译。
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/01/2021
ms.author: panosper
ms.custom: seodec18,references_regions
ms.openlocfilehash: 002f8ce7311d59a101f676908453fb1493b6dd39
ms.sourcegitcommit: e6de87b42dc320a3a2939bf1249020e5508cba94
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/27/2021
ms.locfileid: "114710417"
---
# <a name="speech-service-supported-regions"></a>语音服务支持的区域

通过语音服务，应用程序可将音频转换为文本、执行语音翻译以及将文本转换为语音。 多个区域中均提供该服务，这些区域为语音 SDK 和 REST API 使用唯一终结点。

此处提供了对所有区域的语音体验执行自定义配置的语音门户： https://speech.microsoft.com

考虑区域时，请注意以下几点：

* 如果应用程序使用[语音 SDK](speech-sdk.md)，请在创建语音配置时提供区域标识符，例如 `westus`。
* 如果应用程序使用某个语音服务的 [REST API](./overview.md#reference-docs)，则区域是你在发出请求时使用的终结点 URI 的一部分。
* 为某个区域创建的密钥仅在该区域有效。 尝试在其他区域使用此类密钥会导致身份验证错误。

> [!NOTE]
> 语音服务不会在客户部署服务实例的区域之外存储/处理客户数据。

## <a name="speech-sdk"></a>语音 SDK

在[语音 SDK](speech-sdk.md) 中，区域指定为字符串（例如，在 C# 语音 SDK 中用作 `SpeechConfig.FromSubscription` 的参数）。

### <a name="speech-to-text-text-to-speech-and-translation"></a>语音转文本、文本转语音和翻译

语音自定义门户在此处提供： https://speech.microsoft.com

可以在以下区域使用语音服务，以进行 **语音识别**、**文本转语音** 和 **翻译**：

[!INCLUDE [](../../../includes/cognitive-services-speech-service-region-identifier.md)]

如果使用 [语音 SDK](speech-sdk.md)，则区域由 **区域标识符** 指定（例如，作为 `SpeechConfig.FromSubscription` 的参数）。 请确保该区域与订阅的区域匹配。

如果打算使用音频数据训练自定义模型，请使用[具有专用硬件的区域](custom-speech-overview.md#set-up-your-azure-account)之一进行更快的训练。 稍后可以使用 [REST API](https://centralus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/CopyModelToSubscription) 将完全训练的模型复制到另一个区域。

### <a name="intent-recognition"></a>意向识别

通过语音 SDK 实现 **意向识别** 的可用区域如下：

| 全球区域 | 区域           | 区域标识符 |
| ------------- | ---------------- | -------------------- |
| 亚洲          | 东亚        | `eastasia`           |
| 亚洲          | 东南亚   | `southeastasia`      |
| 澳大利亚     | 澳大利亚东部   | `australiaeast`      |
| 欧洲        | 北欧     | `northeurope`        |
| 欧洲        | 西欧      | `westeurope`         |
| 北美 | 美国东部          | `eastus`             |
| 北美 | 美国东部 2        | `eastus2`            |
| 北美 | 美国中南部 | `southcentralus`     |
| 北美 | 美国中西部  | `westcentralus`      |
| 北美 | 美国西部          | `westus`             |
| 北美 | 美国西部 2        | `westus2`            |
| 南美洲 | Brazil South     | `brazilsouth`        |

这是[语言理解服务 (LUIS)](../luis/luis-reference-regions.md) 支持的发布区域的子集。

### <a name="voice-assistants"></a>语音助手

[语音 SDK](speech-sdk.md) 在以下区域通过 [Direct Line Speech](./direct-line-speech.md) 支持“语音助理”功能：

| 全球区域 | 区域           | 区域标识符    |
| ------------- | ---------------- | -------------------- |
| 北美 | 美国西部          | `westus`             |
| 北美 | 美国西部 2        | `westus2`            |
| 北美 | 美国东部          | `eastus`             |
| 北美 | 美国东部 2        | `eastus2`            |
| 北美 | 美国中西部  | `westcentralus`      |
| 北美 | 美国中南部 | `southcentralus`     |
| 欧洲        | 西欧      | `westeurope`         |
| 欧洲        | 北欧     | `northeurope`        |
| 亚洲          | 东亚        | `eastasia`           |
| 亚洲          | 东南亚   | `southeastasia`      |
| 印度         | 印度中部    | `centralindia`       |

### <a name="speaker-recognition"></a>说话人识别

说话人识别功能目前仅在 `westus` 区域提供。

## <a name="rest-apis"></a>REST API

语音服务还为语音转文本和文本转语音请求公开 REST 终结点。

### <a name="speech-to-text"></a>语音转文本

有关语音转文本的参考文档，请参阅[语音转文本 REST API](rest-speech-to-text.md)。

REST API 的终结点具有以下格式：

```
https://<REGION_IDENTIFIER>.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1
```

将 `<REGION_IDENTIFIER>` 替换为与下表中的订阅区域匹配的标识符：

[!INCLUDE [](../../../includes/cognitive-services-speech-service-region-identifier.md)]

> [!NOTE]
> 必须将语言参数追加到 URL 以避免收到 4xx HTTP 错误。 例如，使用“美国西部”终结点设置为美国英语的语言为：`https://westus.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US`。

### <a name="text-to-speech"></a>文本转语音

有关文本转语音的参考文档，请参阅[文本转语音 REST API](rest-text-to-speech.md)。

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]
