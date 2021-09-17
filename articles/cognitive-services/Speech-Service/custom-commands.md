---
title: 自定义命令 - 语音服务
titleSuffix: Azure Cognitive Services
description: 自定义命令（一种用于创建语音应用程序的解决方案）的特性、功能和限制的概述。
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: travisw
ms.openlocfilehash: db0f78f2f3aef3295837d921d7d70866ed8b8769
ms.sourcegitcommit: d01c2b2719e363178720003b67b968ac2a640204
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2021
ms.locfileid: "122455811"
---
# <a name="what-is-custom-commands"></a>什么是自定义命令？

诸如[语音助理](voice-assistants.md)之类的应用程序会倾听用户的声音，并执行一个操作以进行响应（通常是回话）。 他们使用[语音转文本](speech-to-text.md)听录用户的语音，然后对文本的自然语言理解执行操作。 此操作通常包含通过[文本转语音](text-to-speech.md)生成的助理语音输出。 设备通过语音 SDK 的 `DialogServiceConnector` 对象连接到助理。

使用“自定义命令”，可以轻松构建针对语音优先的交互体验进行了优化的丰富语音命令应用。 它提供统一的创作体验、自动托管模型和相对较低的复杂性，帮助你集中精力为“语音命令”场景构建最佳解决方案。

“自定义命令”最适用于“任务完成”或“命令与控制”场景，尤其适合物联网 (IoT) 设备、环境设备和无外设设备。 示例包括用于酒店、零售和汽车行业的解决方案，其中从事这些行业的用户会希望为来宾、商店库存管理或车内功能提供语音控制体验。

如果你有兴趣构建复杂的对话应用，建议你使用[虚拟助理解决方案](/azure/bot-service/bot-builder-enterprise-template-overview)尝试 Bot Framework。 可以使用 Direct Line Speech 将语音添加到任何 Bot Framework 机器人。

适用于“自定义命令”的良好候选项具有一个固定词汇表，其中包含一组已经过完善定义的变量。 例如，家庭自动化任务（如“控制恒温器”）就是理想之选。

   ![“任务完成”场景的示例](media/voice-assistants/task-completion-examples.png "“任务完成”示例")

## <a name="getting-started-with-custom-commands"></a>“自定义命令”入门

“自定义命令”的目标是降低你用于了解所有不同技术的认知负荷，使你专注于构建语音命令应用。 使用“自定义命令”<a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">创建 Azure 语音资源</a>的第一步。 可以在 Speech Studio 上创作“自定义命令”应用并将其发布，之后设备上的应用程序便可以使用语音 SDK 与它进行通信。

#### <a name="authoring-flow-for-custom-commands"></a>“自定义命令”的创作流
   ![“自定义命令”的创作流](media/voice-assistants/custom-commands-flow.png "“自定义命令”创作流")

按照我们的快速入门操作，在不到 10 分钟的时间内便可让第一个“自定义命令”应用运行代码。

* [使用自定义命令创建语音助手](quickstart-custom-commands-application.md)

完成快速入门后，请浏览我们的操作指南，了解用于设计、开发、调试、部署和集成“自定义命令”应用程序的详细步骤。

## <a name="building-voice-assistants-with-custom-commands"></a>借助“自定义命令”构建语音助理
> [!VIDEO https://www.youtube.com/embed/1zr0umHGFyc]

## <a name="next-steps"></a>后续步骤

* [免费获取语音服务订阅密钥](overview.md#try-the-speech-service-for-free)
* [查看 GitHub 上的“语音助理”存储库以获取示例](https://aka.ms/speech/cc-samples)
* [转到 Speech Studio 以试用“自定义命令”](https://speech.microsoft.com/customcommands)
* [获取语音 SDK](speech-sdk.md)