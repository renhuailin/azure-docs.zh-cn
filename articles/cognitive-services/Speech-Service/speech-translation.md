---
title: 语音翻译概述 - 语音服务
titleSuffix: Azure Cognitive Services
description: 使用语音翻译，可以将语音的端到端实时多语言翻译添加到应用程序、工具和设备。 相同 API 可以用于语音到语音和语音到文本的转换。 本文概述了语音翻译服务的优势和功能。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/01/2020
ms.author: erhopf
ms.custom: devx-track-csharp, cog-serv-seo-aug-2020
keywords: 语音翻译
ms.openlocfilehash: 94ddd06068513261b5b73b313877e273c7251d62
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/24/2021
ms.locfileid: "104954953"
---
# <a name="what-is-speech-translation"></a>什么是语音翻译？

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

本概述介绍语音翻译服务的优势和功能，它可以对音频流进行实时[多语言语音转语音](language-support.md#speech-translation)和语音转文本翻译。 使用语音 SDK，应用程序、工具和设备可以访问所提供的音频的源听录和翻译输出。 检测到语音时，会返回过渡性的听录和翻译结果，最终结果可以转换为合成语音。

## <a name="core-features"></a>核心功能

* 包含识别结果的语音转文本翻译。
* 语音转语音翻译。
* 支持翻译为多种目标语言。
* 过渡性识别和翻译结果。

## <a name="get-started"></a>入门 

请参阅[快速入门](get-started-speech-translation.md)以开始使用语音翻译。 语音翻译服务通过[语音 SDK](speech-sdk.md) 和[语音 CLI](spx-overview.md) 提供。

## <a name="sample-code"></a>代码示例

GitHub 上提供了语音 SDK 的示例代码。 这些示例涵盖了常见方案，例如，从文件或流中读取音频、连续和单次识别/翻译，以及使用自定义模型。

* [语音转文本和翻译示例 (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)

## <a name="migration-guides"></a>迁移指南

如果你的应用程序、工具或产品使用[语音翻译 API](./how-to-migrate-from-translator-speech-api.md)，则可按照我们创建的指南迁移到语音服务。

* [从语音翻译 API 迁移到语音服务](how-to-migrate-from-translator-speech-api.md)

## <a name="reference-docs"></a>参考文档

* [语音 SDK](./speech-sdk.md)
* [语音设备 SDK](speech-devices-sdk.md)
* [REST API：语音转文本](rest-speech-to-text.md)
* [REST API：文本转语音](rest-text-to-speech.md)
* [REST API：批量听录和自定义](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0)

## <a name="next-steps"></a>后续步骤

* 完成语音翻译[快速入门](get-started-speech-translation.md)
* [免费获取语音服务订阅密钥](overview.md#try-the-speech-service-for-free)
* [获取语音 SDK](speech-sdk.md)