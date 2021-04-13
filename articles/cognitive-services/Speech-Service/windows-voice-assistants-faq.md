---
title: Windows 上的语音助理 - 常见问题解答
titleSuffix: Azure Cognitive Services
description: Windows 语音代理开发期间经常遇到的常见问题。
services: cognitive-services
author: cfogg6
manager: trrwilson
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: travisw
ms.openlocfilehash: 3cb4dd16592f3af962b32fe3005d2ad4b241d831
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "84456993"
---
# <a name="samples-and-faqs"></a>示例和常见问题解答

## <a name="the-uwp-voice-assistant-sample"></a>UWP 语音助理示例

UWP 语音助理示例是 Windows 上的语音助理，用于

- 演示如何使用 Windows ConversationalAgent API
- 显示 Windows 上语音代理的最佳做法
- 为你的 MVP 代理应用程序提供适应性强的应用和可重用组件
- 显示如何将 Direct Line Speech 以及 Bot Framework 或自定义命令与 Windows ConversationalAgent API 结合使用，以实现端到端的语音代理体验

随示例应用一起提供的文档将演练语音激活和代理管理的代码路径，包括从启动的先决条件到正确清理的完整内容。

> [!div class="nextstepaction"]
> [访问 UWP 示例的 GitHub 存储库](https://aka.ms/MVA/sample)

## <a name="frequently-asked-questions"></a>常见问题

### <a name="how-do-i-contact-microsoft-for-resources-like-limited-access-feature-tokens-and-keyword-model-files"></a>如何与 Microsoft 联系以获取有限的访问功能令牌和关键字模型文件等资源？

联系 winvoiceassistants@microsoft.com 以请求这些资源。

### <a name="my-app-is-showing-in-a-small-window-when-i-activate-it-by-voice-how-can-i-transition-from-the-compact-view-to-a-full-application-window"></a>在通过语音激活我的应用时，它会显示在一个小窗口中。 如何从精简视图转换为完整的应用程序窗口？

首次通过语音激活应用程序时，应用程序会在精简视图中启动。 请阅读[语音激活预览版的设计指南](windows-voice-assistants-best-practices.md#design-guidance-for-voice-activation-preview)，获取有关 Windows 上的语音助理的不同视图以及在这些视图之间转换的指导。

若要从精简视图转换到完整应用视图，请使用 appView API `TryEnterViewModeAsync`：

`var appView = ApplicationView.GetForCurrentView();
 await appView.TryEnterViewModeAsync(ApplicationViewMode.Default);`

### <a name="why-are-voice-assistant-features-on-windows-only-enabled-for-uwp-applications"></a>为什么只为 UWP 应用程序启用 Windows 上的语音助理功能？

考虑到语音激活等功能相关的隐私风险，UWP 平台的功能是必需的，这些功能可确保 Windows 上的语音助理功能足够安全。

### <a name="the-uwp-voice-assistant-sample-uses-direct-line-speech-do-i-have-to-use-direct-line-speech-for-my-voice-assistant-on-windows"></a>UWP 语音助理示例使用 Direct Line Speech。 我必须为 Windows 上的语音助理使用 Direct Line Speech 吗？

UWP 示例应用程序是使用 Direct Line Speech 和语音服务 SDK 开发的，用于演示如何使用带有 Windows 对话代理功能的对话服务。 但是，你可使用任何服务进行本地和云关键字验证、语音转文本、机器人对话和文本转语音。