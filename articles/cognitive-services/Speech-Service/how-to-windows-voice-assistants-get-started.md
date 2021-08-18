---
title: Windows 上的语音助理 - 入门
titleSuffix: Azure Cognitive Services
description: 开始开发 Windows 语音代理的步骤，包括示例代码快速入门参考。
services: cognitive-services
author: cfogg6
manager: trrwilson
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: travisw
ms.openlocfilehash: 1d9b5e861b35ade3cf391e2a0ace52722821a158
ms.sourcegitcommit: d2738669a74cda866fd8647cb9c0735602642939
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/13/2021
ms.locfileid: "113650631"
---
# <a name="getting-started-with-voice-assistants-on-windows"></a>Windows 上的语音助理入门

本指南将指导你完成在 Windows 上开发语音助理的步骤。

## <a name="set-up-your-development-environment"></a>设置开发环境

若要开始开发适用于 Windows 的语音助理，需要确保具有适当的开发环境。

- Visual Studio：需要安装 [Microsoft Visual Studio 2017](https://visualstudio.microsoft.com/) Community Edition 或更高版本
- Windows 版本：具有 Windows 的 Windows 预览体验成员快速循环内部版本和 Windows SDK 的 Windows 预览体验成员版本的 PC。 此示例代码经验证可使用 Windows SDK 19018 在 Windows 预览体验成员发布版本 19025.vb_release_analog.191112-1600 上工作。 高于指定版本的任何内部版本或 SDK 都应兼容。
- UWP 开发工具：Visual Studio 中的通用 Windows 平台开发工作负载。 请参阅 UWP [设置](/windows/uwp/get-started/get-set-up)页面，使计算机准备好开发 UWP 应用程序。
- 可正常工作的麦克风和音频输出

## <a name="obtain-resources-from-microsoft"></a>从 Microsoft 获取资源

Windows 上完全自定义语音代理所需的某些资源需要 Microsoft 提供的资源。 [UWP 语音助理示例](windows-voice-assistants-faq.yml#the-uwp-voice-assistant-sample)为初始开发和测试提供了这些资源的示例版本，因此初始开发不需要此部分。

- 关键字模型：语音激活需要 Microsoft 提供的关键字模型（形式为 .bin 文件）。 UWP 语音助理示例中提供的 .bin 文件针对关键字 Contoso 进行训练。
- 受限访问功能令牌：由于 ConversationalAgent API 提供对麦克风音频的访问，因此它们由受限访问功能限制进行保护。 若要使用受限访问功能，需要从 Microsoft 获取连接到应用程序的包标识的受限访问功能令牌。

## <a name="establish-a-dialog-service"></a>建立对话服务

若要获得完整的语音助理体验，应用程序需要具有以下功能的对话服务

- 在给定音频文件中检测关键字
- 侦听用户输入并将其转换为文本
- 向机器人提供文本
- 将机器人的文本响应转换为音频输出

下面是使用 Direct Line Speech 创建基本对话服务的要求。

- 语音服务订阅：用于语音转文本和文本转语音转换的认知语音服务订阅。 在[此处](./overview.md#try-the-speech-service-for-free).
- Bot Framework 机器人：使用 Bot Framework 4.2 或更高版本创建的机器人，它订阅了 [Direct Line Speech](./direct-line-speech.md) 以启用语音输入和输出。 [本指南](./tutorial-voice-enable-your-bot-speech-sdk.md)包含创建“回显机器人”并使它订阅 Direct Line Speech 的分步说明。 还可以转到[此处](https://blog.botframework.com/2018/05/07/build-a-microsoft-bot-framework-bot-with-the-bot-builder-sdk-v4/)获取有关如何创建自定义机器人的步骤，然后按照[此处](./tutorial-voice-enable-your-bot-speech-sdk.md)的相同步骤订阅 Direct Line Speech（不过是使用新机器人，而不是“回显机器人”）。

## <a name="try-out-the-sample-app"></a>尝试示例应用

通过语音服务订阅密钥和回显机器人的机器人 ID，你可以尝试 [UWP 语音助理示例](windows-voice-assistants-faq.yml#the-uwp-voice-assistant-sample)。 按照自述文件中的说明运行应用并输入凭据。

## <a name="create-your-own-voice-assistant-for-windows"></a>为 Windows 创建自己的语音助理

从 Microsoft 收到受限访问功能令牌和 bin 文件后，便可以在 Windows 上开始运行自己的语音助理。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [阅读语音助理实现指南](windows-voice-assistants-implementation-guide.md)