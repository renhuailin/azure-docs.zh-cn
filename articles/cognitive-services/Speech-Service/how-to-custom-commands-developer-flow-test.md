---
title: 测试自定义命令应用
titleSuffix: Azure Cognitive Services
description: 本文介绍测试自定义命令应用程序的不同方法。
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.custom: devx-track-csharp
ms.openlocfilehash: fa9f9ec8d7a8f60d6c72cb6c4f669ef511cc0068
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/22/2021
ms.locfileid: "112456862"
---
# <a name="test-your-custom-commands-application"></a>测试自定义命令应用程序

本文介绍测试自定义命令应用程序的不同方法。

## <a name="test-in-the-portal"></a>在门户中测试

在门户中测试是检查自定义命令应用程序是否按预期运行的最简单且最快捷的方法。 成功训练应用后，单击 `Test` 按钮即可开始测试。

> [!div class="mx-imgBorder"]
> ![在门户中测试](media/custom-commands/create-basic-test-chat-no-mic.png)

## <a name="test-with-windows-voice-assistant-client"></a>使用 Windows 语音助理客户端进行测试

Windows 语音助理客户端是 C# 中的一种 Windows Presentation Foundation (WPF) 应用程序，可以在创建自定义客户端应用程序之前轻松测试与机器人的交互。

该工具可以接受自定义命令应用程序 ID。 它使你能够测试自定义命令服务上托管的任务完成或命令和控制方案。

若要设置客户端，请查看 [Windows 语音助理客户端](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/tree/master/clients/csharp-wpf)。

> [!div class="mx-imgBorder"]
> ![WVAC - 创建配置文件](media/custom-commands/conversation.png)

## <a name="test-programatically-with-the-cognitive-services-voice-assistant-test-tool"></a>通过认知服务语音助理测试工具以编程方式进行测试

语音助理测试工具是一种可配置的 .NET Core C# 控制台应用程序，用于 Microsoft 语音助理的端到端功能回归测试。 

该工具可作为控制台命令手动运行，也可作为 Azure DevOps CI/CD 管道的一部分自动运行，来防止机器人出现回归。

若要了解如何设置该工具，请参阅[语音助理测试工具](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/tree/main/clients/csharp-dotnet-core/voice-assistant-test)。

## <a name="test-with-speech-sdk-enabled-client-applications"></a>使用支持语音 SDK 的客户端应用程序进行测试

语音软件开发工具包 (SDK) 公开了许多语音服务功能，这使得你能够开发支持语音的应用程序。 它可在大多数平台和多种编程语言中使用。

使用语音 SDK 设置通用 Windows 平台 (UWP) 客户端应用程序，并将其与自定义命令应用程序集成：  
- [操作说明：使用语音 SDK 与客户端应用程序集成](./how-to-custom-commands-setup-speech-sdk.md)
- [操作说明：向客户端应用程序发送活动](./how-to-custom-commands-send-activity-to-client.md)
- [操作说明：设置 Web 终结点](./how-to-custom-commands-setup-web-endpoints.md)

对于其他编程语言和平台：
- [语音 SDK 编程语言、平台、方案性能](./speech-sdk.md)
- [语音助理示例代码](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [查看 GitHub 上的示例](https://aka.ms/speech/cc-samples)
