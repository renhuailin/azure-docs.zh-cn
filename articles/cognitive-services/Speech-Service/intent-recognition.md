---
title: 意向识别概述 - 语音服务
titleSuffix: Azure Cognitive Services
description: 使用意向识别，可以识别预先定义的用户目标。 本文概述了意向识别服务的优势和功能。
services: cognitive-services
author: v-demjoh
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/13/2020
ms.author: v-demjoh
keywords: 意向识别
ms.openlocfilehash: c4a81c5f666b9cb42b642390ba79ca2a1bf7232b
ms.sourcegitcommit: e6de87b42dc320a3a2939bf1249020e5508cba94
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/27/2021
ms.locfileid: "114710087"
---
# <a name="what-is-intent-recognition"></a>什么是意向识别？

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

本概述介绍了意向识别的优势和功能。 认知服务语音 SDK 提供了两种识别意向的方法，如下所述。 意向是用户想要做的某件事：预订航班、查看天气预报或拨打电话。 使用意向识别，应用程序、工具和设备可以根据在意向识别器或 LUIS 中定义的选项来确定用户希望启动或执行的操作。

## <a name="pattern-matching"></a>模式匹配
SDK 提供了一个嵌入式模式匹配程序，可用于以非常严格的方式识别意向。 这适用于在需要快速脱机解决方案时非常有用。 当用户要以某种方式进行训练或可以预期使用特定短语来触发意向时，这尤其有效。 例如：“到第 7 层”或“开灯”等。建议从此处开始，如果不再满足需求，可切换到使用 LUIS 或两者的组合。 

## <a name="luis-language-understanding-intent-service"></a>LUIS（语言理解意向服务）
Microsoft LUIS 服务作为完整的 AI 意向服务可用，当可能意向的域很大，并且不真正确定用户将说什么时，该服务适用。 它支持许多复杂的方案、意向和实体。

### <a name="luis-key-required"></a>需要 LUIS 密钥

* LUIS 与语音服务集成，可从语音中识别意向。 不需要语音服务订阅，只需要 LUIS。
* 语音意向识别与语音 SDK 集成在一起。 可以将 LUIS 密钥用于语音服务。
* 通过语音 SDK 的意向识别[在 LUIS 支持的区域的子集中提供](./regions.md#intent-recognition)。

## <a name="get-started"></a>入门
请参阅[操作指南](how-to-use-simple-language-pattern-matching.md)，了解如何开始模式匹配。

请参阅[快速入门](get-started-intent-recognition.md)，了解如何开始 LUIS 意向识别。

## <a name="sample-code"></a>代码示例

意向识别的示例代码：

* [快速入门：使用预构建的家庭自动化应用](../luis/luis-get-started-create-app.md)
* [使用适用于 C# 的语音 SDK 从语音中识别意向](./how-to-recognize-intents-from-speech-csharp.md)
* [使用 Unity 和 C# 实现意向识别和其他语音服务](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/unity/speechrecognizer)
* [使用用于 Python 的语音 SDK 识别意向](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/python/console)
* [在 Windows 上使用适用于 C++ 的语音 SDK 实现意向识别和其他语音服务](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/cpp/windows/console)
* [在 Windows 或 Linux 上使用适用于 Java 的语音 SDK 实现意向识别和其他语音服务](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/java/jre/console)
* [在 Web 浏览器上使用适用于 JavaScript 的语音 SDK 实现意向识别和其他语音服务](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/js/browser)

## <a name="reference-docs"></a>参考文档

* [语音 SDK](./speech-sdk.md)

## <a name="next-steps"></a>后续步骤

* 完成意向识别[快速入门](get-started-intent-recognition.md)
* [免费获取语音服务订阅密钥](overview.md#try-the-speech-service-for-free)
* [获取语音 SDK](speech-sdk.md)
