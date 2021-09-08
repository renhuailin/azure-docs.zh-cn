---
title: Direct Line Speech - 语音服务
titleSuffix: Azure Cognitive Services
description: 概述了使用 Direct Line Speech 和语音软件开发工具包 (SDK) 的语音助理的特性、功能和限制。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: pafarley
ms.openlocfilehash: 016f827a9f83aac6e460fe32ef26df6bbcf726f1
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/07/2021
ms.locfileid: "123544044"
---
# <a name="what-is-direct-line-speech"></a>什么是 Direct Line 语音？

Direct Line Speech 是一种可靠的端到端解决方案，可用于创建灵活、可扩展的语音助理。 它由 Bot Framework 及其 Direct Line Speech 通道提供支持，该通道经过优化，可用于与机器人的语音交互。

[语音助理](voice-assistants.md)之类的应用程序会倾听用户的声音，并执行一个操作以进行响应（通常是回话）。 他们使用[语音转文本](speech-to-text.md)听录用户的语音，然后对文本的自然语言理解执行操作。 此操作通常包含通过[文本转语音](text-to-speech.md)生成的助理语音输出。

Direct Line Speech 为语音助理提供了最高水平的定制化和复杂性。 它是为完成任务或命令和控制使用的开放式、自然或两者混合的会话场景设计的。 这种高度的灵活性带来了更大的复杂性，对于限定为使用自然语言输入的定义明确的任务场景，可能需要考虑[自定义命令](custom-commands.md)来简化解决方案体验。

## <a name="getting-started-with-direct-line-speech"></a>Direct Line Speech 入门

使用 Direct Line Speech 创建语音助理的第一步是[获取语音订阅密钥](overview.md#try-the-speech-service-for-free)，创建与该订阅关联的新机器人，并将机器人连接到 Direct Line Speech 通道。

   ![Direct Line Speech 业务流程服务流概念图](media/voice-assistants/overview-directlinespeech.png "语音通道流")

有关使用 Direct Line Speech 创建简单语音助理的完整分步指南，请参阅[使用语音 SDK 和 Direct Line Speech 通道为机器人启用语音的教程](tutorial-voice-enable-your-bot-speech-sdk.md)。

我们还提供了快速入门，旨在让你快速运行代码和学习 API。 下表列出了按语言和平台组织的语音助理快速入门。

| 快速入门 | 平台 | API 参考 |
|------------|----------|---------------|
| C#、UWP | Windows | [“浏览”](/dotnet/api/microsoft.cognitiveservices.speech) |
| Java | Windows、macOS、Linux | [“浏览”](/java/api/com.microsoft.cognitiveservices.speech) |
| Java | Android | [“浏览”](/java/api/com.microsoft.cognitiveservices.speech) |

## <a name="sample-code"></a>代码示例

GitHub 上提供了用于创建语音助理的示例代码。 这些示例涵盖了以多种流行编程语言编写的、用于连接到助理的客户端应用程序。

* [语音助手示例 (SDK)](https://aka.ms/csspeech/samples/#voice-assistants-quickstarts)
* [教程：使用语音 SDK、C# 为助理启用语音](tutorial-voice-enable-your-bot-speech-sdk.md)

## <a name="customization"></a>自定义

使用语音服务构建的语音助理可以使用各种自定义选项，这些选项可用于[语音转文本](speech-to-text.md)、[文本转语音](text-to-speech.md)和[自定义关键字选择](./custom-keyword-basics.md)。

> [!NOTE]
> 自定义选项因语言/区域设置而异（请参阅[支持的语言](./language-support.md)）。

语音助理的 Direct Line Speech 及其相关功能是对[虚拟助理解决方案和企业模板](/azure/bot-service/bot-builder-enterprise-template-overview)的很好补充。 尽管 Direct Line Speech 可以与任何兼容机器人配合使用，但这些资源为高质量的对话体验提供了可重用的基线，并为快速入门提供了常见的支持技能和模型。

## <a name="reference-docs"></a>参考文档

* [语音 SDK](./speech-sdk.md)
* [Azure 机器人服务](/azure/bot-service/)

## <a name="next-steps"></a>后续步骤

* [免费获取语音服务订阅密钥](overview.md#try-the-speech-service-for-free)
* [获取语音 SDK](speech-sdk.md)
* [创建和部署基本机器人](/azure/bot-service/bot-builder-tutorial-basic-deploy)
* [获取虚拟助理解决方案和企业模板](https://github.com/Microsoft/AI)
