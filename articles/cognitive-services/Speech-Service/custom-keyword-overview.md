---
title: 自定义关键字 - 语音服务
titleSuffix: Azure Cognitive Services
description: 概述使用语音软件开发工具包 (SDK) 开发的自定义关键字的特性、功能和限制。
services: cognitive-services
author: hasyashah
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: hasshah
ms.custom: devx-track-csharp
ms.openlocfilehash: b2763372cc63637f73610e1936e3ea54a4524e7d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "91356598"
---
# <a name="what-is-a-keyword"></a>什么是关键字？

关键字是使产品激活语音的单词或短语。 例如，“Hey Cortana”是 Cortana 助理的关键字。 借助语音激活，用户只需说出关键字，即可开始在完全无需动手的情况下与产品交互。 当产品持续侦听关键字时，所有音频都在用户设备上进行本地处理，直到检测到关键字，以确保数据尽可能保持私有状态。

## <a name="core-features-of-custom-keyword"></a>自定义关键字的核心功能

通过自定义关键字的自定义、性能和集成功能，可以定制语音激活，以最大程度地满足产品的视觉和用户需求。

| 功能 | 说明 |
|----------|----------|
| 关键字自定义 | 作为品牌的延伸，关键字可强化你与客户建立的资产。 借助 Speech Studio 上的自定义关键字门户，可以指定最能代表品牌的任何单词或短语。 可以通过选择适当的发音来进一步个性化关键字，这些发音会由生成的关键字模型进行采用。
| 关键字验证 | 如果在本地检测到的关键字有很高的置信度，则音频会发送到云，以便进一步验证用户是否说出了关键字。 关键字验证可降低不正确本地检测的影响并保护用户隐私，从而提供额外的安全层。
| 语音助理和语音 SDK 集成 | 可以通过语音 SDK 轻松地在设备或应用程序中集成从 Speech Studio 上的自定义关键字生成的关键字。 只需将 SDK 指向由 Speech Studio 提供的关键字模型，产品便会激活语音（由关键字验证提供支持）。 可以通过生成自己的[语音助理](voice-assistants.md)来完成产品的语音体验。

## <a name="get-started-with-custom-keywords"></a>自定义关键字入门

* 有关基本用法和设计模式，请参阅[自定义关键字基础知识](custom-keyword-basics.md)。
* 如何[使用 C# 通过语音 SDK 对产品进行语音激活](tutorial-voice-enable-your-bot-speech-sdk.md)

## <a name="choose-an-effective-keyword"></a>选择有效的关键字

创建有效的关键字对于确保设备可一致准确地进行响应至关重要。 自定义关键字是区分设备和加强品牌效应的有效方式。 选择关键字时请考虑以下准则：

> [!div class="checklist"]
> * 关键字应该是英文单词或短语。
> * 说该词应该不超过两秒钟。
> * 4 到 7 个音节的单词效果最好。 例如，“Hey, Computer”是一个很好的关键字。 而只是“Hey”则是一个糟糕的唤醒词。
> * 关键字应遵循常见的英语发音规则。
> * 遵循常见英语发音规则的独特或甚至虚构的单词可以减少误报。 例如，“computerama”可能是一个很好的关键字。
> * 不要选择常用词。 例如，“eat”和“go”是人们在日常对话中经常说的词。 它们可能是设备的错误触发器。
> * 避免使用可能有其他发音的关键字。 用户必须知道“正确”的发音才能使他们的设备做出响应。 例如，“509”的发音可以是“five zero nine”、“five oh nine”或“five hundred and nine”。 “R.E.I.” 发音可以是“r-e-i”或“ray”。 “Live”的发音可以是“/līv/”或“/liv/”。
> * 不要使用特殊字符、符号或数字。 例如，“Go#”和“20 + cats”可能是问题关键字。 但是，“go sharp”或“twenty plus cats”可行。 你仍然可以在品牌中使用这些符号，并通过营销和文档来强化正确的发音。

> [!NOTE]
> 如果选择商标词作为关键字，请确保你拥有该商标，或者获得商标所有者的许可才能使用该词。 Microsoft 对你选择的关键字可能引起的任何法律问题不承担任何责任。

## <a name="see-samples-on-github"></a>查看 GitHub 上的示例

* [使用 C# 在通用 Windows 平台上，通过语音 SDK 识别关键字](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/uwp/keyword-recognizer)
* [使用 Android 在 Android 上，通过语音 SDK 识别关键字](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/android/keyword-recognizer)

## <a name="next-steps"></a>后续步骤

* [免费获取语音服务订阅密钥](overview.md#try-the-speech-service-for-free)
* [获取语音 SDK](speech-sdk.md)
* [详细了解语音助理](voice-assistants.md)
