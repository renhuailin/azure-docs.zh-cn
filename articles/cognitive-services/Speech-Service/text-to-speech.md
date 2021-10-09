---
title: 文本转语音概述 - 语音服务
titleSuffix: Azure Cognitive Services
description: 语音服务中的文本转语音功能可让应用程序、工具或设备将文本转换为类似于人类的自然合成语音。 本文概述了文本转语音服务的优势和功能。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/01/2020
ms.author: pafarley
ms.custom: cog-serv-seo-aug-2020
keywords: 文本转语音
ms.openlocfilehash: 7be06937cf9f3117d426fd9c19cbb6059826e6ec
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124736895"
---
# <a name="what-is-neural-text-to-speech"></a>什么是神经文本转语音？

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Microsoft 神经文本转语音使用深度神经网络，使计算机的声音与人类录音几乎无法区分。 神经文本转语音可以生成类人的自然韵律和清晰的字词发音，当你在与 AI 系统交互时，它可以显著减轻听力疲劳。

口语中的重音和语调模式称为韵律。 传统的文本转语音系统将韵律分解为单独的语言分析和声学预测步骤，这些步骤由独立的模型控制。 这可能会导致语音合成杂乱、声调不一。 Microsoft 神经文本转语音功能会同时进行韵律预测和语音合成，利用深度神经网络克服传统文本转语音系统在口语重音和语调模式匹配方面的局限性，并将语音单位合成为计算机的声音。 结果是更流畅和自然的声音。

在本概述中，你将了解文本转语音服务的优势和功能，该服务使你的应用程序、工具或设备可以将文本转换为类似于人的合成语音。 使用类似于人的神经语音，或创建产品或品牌特有的自定义语音。 有关支持的语音、语言和区域设置的完整列表，请参阅[支持的语言](language-support.md#text-to-speech)。

本文档包含以下文章类型：

* **快速入门** 介绍了入门说明，指导您完成向服务发出请求。
* **操作指南** 包含以更具体的方式或自定义方式使用服务的说明。
* **概念** 对服务的功能和特性进行了深入说明。
* **教程** 是较长的指南，向您演示了如何在更广泛的业务解决方案中使用该服务作为组件。

> [!NOTE]
>
> 必应语音已于 2019 年 10 月 15 日停止使用。 如果你的应用程序、工具或产品是使用必应语音 API 或自定义语音，请按照我们制作的指南迁移到语音服务。
>
> * [从必应语音迁移到语音服务](how-to-migrate-from-bing-speech.md)

## <a name="core-features"></a>核心功能

* 语音合成 - 使用[语音 SDK](./get-started-text-to-speech.md) 或 [REST API](rest-text-to-speech.md) 和[平台神经语音](language-support.md#text-to-speech)或[自定义神经语音](custom-neural-voice.md)将文本转换为语音。

* 长音频的异步合成 - 使用[长音频 API](long-audio-api.md) 异步合成 10 分钟以上的文本转语音文件（例如有声书籍或讲座）。 不同于使用语音 SDK 或语音转文本 REST API 执行的合成，响应不会实时返回。 预期会异步发送请求，以轮询的方式获取响应，并会下载合成音频（在服务提供该音频的情况下）。

* 平台神经语音 - 深度神经网络用于克服传统语音合成在口语的重音和语调方面的局限性。 韵律预测和语音合成以同步方式执行，使输出听起来更流畅且自然。 使用神经语音可使得与聊天机器人和语音助手的交流更加自然且富有吸引力、将数字文本（如电子书）转换为有声读物以及增强车载导航系统。 神经语音可以生成类人的自然韵律和清晰的字词发音，当你在与 AI 系统交互时，它可以显著减轻听力疲劳。 有关平台神经语音的完整列表，请参阅[支持的语言](language-support.md#text-to-speech)。

* 使用 SSML 优化 TTS 输出 - 语音合成标记语言 (SSML) 是一种基于 XML 的标记语言，用于对文本转语音输出进行自定义。 使用 SSML，你不仅可以调整音调、添加暂停、改进发音、改变语速、调整音量，以及将多个语音分配到一个文档，还可以定义你自己的词典或者转换为不同的说话风格。 使用[多语言语音](https://techcommunity.microsoft.com/t5/azure-ai/azure-text-to-speech-updates-at-build-2021/ba-p/2382981)，还可通过 SSML 调整说话语言。 请参阅[如何使用 SSML](speech-synthesis-markup.md) 根据场景微调语音输出。

* 视素 - [视素](how-to-speech-synthesis-viseme.md)是观察到的语音中的关键姿态，包括在产生特定音素时嘴唇、下巴和舌头的位置。 视素与语音和音素有很强的关联性。 使用语音 SDK 中的视素事件，可以生成面部动画数据，用于制作唇读交流、教育、娱乐、客户服务等方面的面部动画。 视素目前仅支持 `en-US` 英语（美国）和[神经语音](language-support.md#text-to-speech)。

## <a name="get-started"></a>入门

请参阅[快速入门](get-started-text-to-speech.md)以开始使用文本转语音。 文本转语音服务通过[语音 SDK](speech-sdk.md)、[REST API](rest-text-to-speech.md) 和[语音 CLI](spx-overview.md) 提供

## <a name="sample-code"></a>代码示例

GitHub 上提供了文本转语音的示例代码。 这些示例涵盖了最流行编程语言的文本转语音转换。

* [文本转语音示例 (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
* [文本转语音示例 (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)

## <a name="customization"></a>自定义

除了神经语音外，还可以创建和微调产品或品牌独有的自定义语音。 只需准备好几个音频文件和关联的听录内容即可完全入门。 有关详细信息，请参阅[自定义神经语音入门](how-to-custom-voice.md)

## <a name="pricing-note"></a>定价说明

使用文本转语音服务时，需按照转换为语音的每个字符（包括标点）付费。 尽管 SSML 文档本身不计费，但用于调整文本转语音方式的可选元素（例如音素和音节）将算作计费字符。 下面列出了计费的内容：

* 在请求的 SSML 正文中传递给文本转语音服务的文本
* 请求正文的文本字段中所有 SSML 格式的标记，`<speak>` 和 `<voice>` 标记除外
* 字母、标点、空格、制表符、标记和所有空白字符
* Unicode 中定义的每个码位

有关详细信息，请参阅[定价](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)。

> [!IMPORTANT]
> 每个中文、日语和韩语字符算作两个计费字符。

## <a name="migrate-to-neural-voice"></a>迁移到神经语音

我们将于 2024 年 8 月 31 日停用标准语音，之后将不再支持这些语音。该公告已发送至 2021 年 8 月 31 日之前的所有现有语音订阅。 在停用期间（2021 年 8 月 31 日 - 2024 年 8 月 31 日），现有标准语音用户可以继续使用其标准语音，所有新用户/新语音资源应转换为神经语音。

**必需的操作**

1. 查看[价格](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)结构并试听页面底部的神经语音[示例](https://azure.microsoft.com/services/cognitive-services/text-to-speech/#overview)，以确定适合业务需求的语音。
1. 若要进行更改，请[按照示例代码](speech-synthesis-markup.md#choose-a-voice-for-text-to-speech)中所示，在 2024 年 8 月 31 日之前将你的语音合成请求中的语音名称更新为所选语言支持的神经语音名称。 从 2024 年 9 月 1 日开始，将不再支持标准语音，请在云端或本地为语音合成请求使用神经语音。 对于本地容器，请使用[神经语音容器](../containers/container-image-tags.md)，并按照[说明](speech-container-howto.md)进行操作。

## <a name="reference-docs"></a>参考文档

* [语音 SDK](speech-sdk.md)
* [REST API：文本转语音](rest-text-to-speech.md)

## <a name="next-steps"></a>后续步骤

* [获取免费语音服务订阅](overview.md#try-the-speech-service-for-free)
* [获取语音 SDK](speech-sdk.md)
