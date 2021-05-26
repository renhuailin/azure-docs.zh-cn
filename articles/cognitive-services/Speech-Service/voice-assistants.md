---
title: 语音助理 - 语音服务
titleSuffix: Azure Cognitive Services
description: 概述使用语音软件开发工具包 (SDK) 的语音助理的特性、功能和限制。
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: travisw
ms.openlocfilehash: 1a3d99a0befd336d37b02b448a65df406d3a8e6e
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2021
ms.locfileid: "110059007"
---
# <a name="what-is-a-voice-assistant"></a>什么是语音助手？

使用语音服务的语音助理能够支持开发人员为其应用程序和体验创建自然的、类似于人类的对话界面。

语音助手服务可在设备和助手实现之间提供快速可靠的交互，它们主要通过两种方式实现：(1) 使用 [Direct Line Speech](direct-line-speech.md)（通过 Azure 机器人服务）为机器人添加语音功能，或 (2) 针对语音命令方案使用自定义命令。

## <a name="choosing-an-assistant-solution"></a>选择助手解决方案

创建语音助手的第一步是确定其用途。 语音服务为构建助手交互提供多种补充性解决方案。 你可以通过 Azure 机器人服务使用 [Direct Line Speech](direct-line-speech.md) 通道为构建的灵活而多功能的机器人添加语音输入和语音输出功能，也可以轻松创建一个[自定义命令](custom-commands.md)应用直接用于语音命令方案。

| 如果你想要... | 那么请考虑... | 例如… |
|-------------------|------------------|----------------|
|集成了强大技能并可完全控制部署的开放式对话 | 带有 [Direct Line Speech](direct-line-speech.md) 通道的 Azure 机器人服务机器人 | <ul><li>“我需要去西雅图”</li><li>“我可以订购哪种披萨？”</li></ul>
|通过简化的创作和托管实现语音操控或简单的任务导向型对话 | [自定义命令](custom-commands.md) | <ul><li>“打开吸顶灯”</li><li>“让温度再暖 5 度”</li><li>[此处](https://speech.microsoft.com/customcommands)提供了其他示例</li></ul>

如果还不确定希望助手处理哪种情况，我们建议将 [Direct Line Speech](direct-line-speech.md) 设为最佳默认选择。 它可与丰富的工具和创作辅助工具（例如[虚拟助理解决方案和企业模板](/azure/bot-service/bot-builder-enterprise-template-overview)和 [QnA Maker 服务](../qnamaker/overview/overview.md)）相集成，构建通用模式并可使用现有的知识源。

使用“自定义命令”，可以轻松构建针对语音优先的交互体验进行了优化的丰富语音命令应用。 它提供统一的创作体验、自动托管模型和相对较低的复杂性，帮助你集中精力为“语音命令”场景构建最佳解决方案。

   ![助手解决方案比较](media/voice-assistants/assistant-solution-comparison.png "助手解决方案比较")


## <a name="reference-architecture-for-building-a-voice-assistant-using-the-speech-sdk"></a>使用语音 SDK 生成语音助理的参考体系结构

   ![语音助理业务流程服务流的概念图](media/voice-assistants/overview.png "语音助理流")

## <a name="core-features"></a>核心功能

无论选择 [Direct Line Speech](direct-line-speech.md) 还是[自定义命令](custom-commands.md)来创建助手交互，均可使用一套丰富的自定义功能来自定义符合自己品牌、产品和个性的助手。

| 类别 | 功能 |
|----------|----------|
|[自定义关键字](./custom-keyword-basics.md) | 用户可以使用自定义关键字（例如“你好 Contoso”）开始与助手对话。 应用将使用 Speech SDK 中的自定义关键字引擎来完成此工作，可以为其配置[可在此处生成](./custom-keyword-basics.md)的自定义关键字。 语音助手可以使用服务端关键字验证来提升关键字激活的准确度（相比设备自身）。
|[语音转文本](speech-to-text.md) | 语音助理使用语音服务中的[语音转文本](speech-to-text.md)将实时音频转换为识别的文本。 此文本是听录而成的，因此可供助理实现和客户端应用程序使用。
|[文本转语音](text-to-speech.md) | 使用语音服务中的[文本转语音](text-to-speech.md)来合成助理做出的文本响应。 然后，这些合成内容可作为音频流提供给客户端应用程序使用。 Microsoft 提供生成你自己的自定义优质神经 TTS 语音（根据品牌发出语音）的功能。 有关详细信息，请[联系我们](mailto:mstts@microsoft.com)。

## <a name="getting-started-with-voice-assistants"></a>语音助理入门

我们专门提供了快速入门来帮助你在 10 分钟内运行代码。 下表提供了按语言组织的语音助理快速入门列表。

* [快速入门：使用 Direct Line Speech 创建自定义语音助手](quickstarts/voice-assistants.md)
* [快速入门：使用自定义命令生成语音操控应用](quickstart-custom-commands-application.md)

## <a name="sample-code-and-tutorials"></a>示例代码和教程

GitHub 上提供了用于创建语音助理的示例代码。 这些示例涵盖了以多种流行编程语言编写的、用于连接到助理的客户端应用程序。

* [GitHub 上的语音助理示例](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant)
* [教程：通过 Azure 机器人服务使用 C# 语音 SDK 使助手支持语音服务](tutorial-voice-enable-your-bot-speech-sdk.md)
* [教程：创建使用简单语音命令的自定义命令应用程序](./how-to-develop-custom-commands-application.md)

## <a name="customization"></a>自定义

通过 Azure 语音服务生成的语音助理可以使用各种自定义选项。

* [自定义语音识别](./custom-speech-overview.md)
* [自定义语音](how-to-custom-voice.md)
* [自定义关键字](keyword-recognition-overview.md)

> [!NOTE]
> 自定义选项因语言/区域设置而异（请参阅[支持的语言](language-support.md)）。

## <a name="next-steps"></a>后续步骤

* [免费获取语音服务订阅密钥](overview.md#try-the-speech-service-for-free)
* [详细了解自定义命令](custom-commands.md)
* [详细了解 Direct Line Speech](direct-line-speech.md)
* [获取语音 SDK](speech-sdk.md)