---
title: 关于语音 SDK - 语音服务
titleSuffix: Azure Cognitive Services
description: 语音软件开发工具包 (SDK) 公开了许多语音服务功能，这使得开发支持语音的应用程序更为容易。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: pafarley
ms.openlocfilehash: 6078fdd823160b6ea5e76efd5940342d333e84f1
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/07/2021
ms.locfileid: "123539942"
---
# <a name="about-the-speech-sdk"></a>关于语音 SDK

语音软件开发工具包 (SDK) 公开了许多语音服务功能，这使得你能够开发支持语音的应用程序。 语音 SDK 可以在许多编程语言中和所有平台中使用。

[!INCLUDE [Speech SDK Platforms](../../../includes/cognitive-services-speech-service-speech-sdk-platforms.md)]

## <a name="scenario-capabilities"></a>方案功能

语音 SDK 公开了语音服务中的许多功能，但未公开全部功能。 语音 SDK 的功能通常与方案相关联。 语音 SDK 同时适用于实时和非实时方案，使用本地设备、文件、Azure Blob 存储甚至输入和输出流。 如果无法通过语音 SDK 实现某个方案，请寻求使用 REST API 替代方法。

### <a name="speech-to-text"></a>语音转文本

[语音转文本](speech-to-text.md)（也称为“语音识别”）可将音频流听录为应用程序、工具或设备可以使用或显示的文本。 结合[语言理解 (LUIS)](../luis/index.yml) 使用语音转文本可以从听录的语音中派生用户意向，以及处理语音命令。 使用[语音翻译](speech-translation.md)通过单个调用将语音输入翻译为另一种语言。 有关详细信息，请参阅[语音转文本基础知识](./get-started-speech-to-text.md)。

可在以下平台上使用语音识别 (SR)、短语列表、意向、翻译和本地容器：

  - C++/Windows 和 Linux 和 macOS
  - C#（Framework 和 .NET Core）/Windows 和 UWP 和 Unity 和 Xamarin 和 Linux 和 macOS
  - Java（Jre 和 Android）
  - JavaScript（浏览器和 NodeJS）
  - Python
  - Swift
  - Objective-C  
  - Go（仅 SR）

### <a name="text-to-speech"></a>文本转语音

[文本转语音](text-to-speech.md)（也称为“语音合成”）将文本转换为类似人类语言的合成语音。 输入文本是字符串文字或使用[语音合成标记语言 (SSML)](speech-synthesis-markup.md)。 有关标准语音或神经语音的详细信息，请参阅[文本转语音语言和语音支持](language-support.md#text-to-speech)。

可在以下平台上使用文本转语音 (TTS)：

  - C++/Windows 和 Linux 和 macOS
  - C#（Framework 和 .NET Core）/Windows 和 UWP 和 Unity 和 Xamarin 和 Linux 和 macOS
  - Java（Jre 和 Android）
  - JavaScript（浏览器和 NodeJS）
  - Python
  - Swift
  - Objective-C
  - Go
  - TTS REST API 可以在所有其他情况下使用。

### <a name="voice-assistants"></a>语音助手

[语音助理](voice-assistants.md)使用语音 SDK，让你可为你的应用程序和体验创建自然的、类似于人类的对话界面。 语音 SDK 提供快速、可靠的交互，其中包括单一连接上的语音转文本、文本转语音和对话数据。 你的实现可以使用 Bot Framework 的 Direct Line Speech 通道或集成的“自定义命令”服务来完成任务。 此外，语音助理可以使用在[“自定义语音”门户](https://aka.ms/customvoice)中创建的自定义语音来添加独特的语音输出体验。

以下平台上提供了“语音助理”支持：

  - C++/Windows 和 Linux 和 macOS
  - C#/Windows
  - Java/Windows 和 Linux 和 macOS 和 Android（语音设备 SDK）
  - Go

#### <a name="keyword-recognition"></a>关键字识别

语音 SDK 支持[关键字识别](custom-keyword-basics.md)的概念。 “关键字识别”是在语音中识别关键字的操作，后跟一个在听到该关键字时执行的操作。 例如，“你好，Cortana”会激活 Cortana 助理。

“关键字识别”在以下平台上可用：

  - C++/Windows 和 Linux
  - C#/Windows 和 Linux
  - Python/Windows 和 Linux
  - Java/Windows、Linux 和 Android

### <a name="meeting-scenarios"></a>会议场景

无论是在单设备对话中还是在多设备对话中，语音 SDK 都非常适用于“会议听录”场景。

#### <a name="conversation-transcription"></a>对话听录

[对话听录](conversation-transcription.md)实现了实时（和异步）语音识别、说话人识别，并可以将句子归属到每个扬声器（也称为 *分割聚类*）。 它非常适合用于听录能够区分说话人的面对面会谈场景。

**对话听录** 在以下平台上可用：

  - C++/Windows 和 Linux
  - C#（Framework 和 .NET Core）/Windows 和 UWP 和 Linux
  - Java/Windows 和 Linux 和 Android（语音设备 SDK）

#### <a name="multi-device-conversation"></a>多设备对话

借助[多设备对话](multi-device-conversation.md)，可在一个对话中连接多个设备或客户端以发送基于语音或文本的消息，并轻松支持听录和翻译。

**多设备对话** 在以下平台上可用：

  - C++/Windows
  - C#（Framework 和 .NET Core）/Windows

### <a name="custom--agent-scenarios"></a>自定义/代理场景

语音 SDK 可用于“呼叫中心听录”场景，其中会生成电话服务数据。

#### <a name="call-center-transcription"></a>呼叫中心听录

[呼叫中心听录](call-center-transcription.md)是语音转文本的一个常见应用场景，用于听录可能来自各种系统（例如交互式语音应答 (IVR)）的大量电话服务数据。 语音服务的最新语音识别模型非常擅长听录这些电话数据，即使是人类也难以识别的数据。

**呼叫中心听录** 通过批量语音服务（经由其 REST API）提供，可以在任何情况下使用。

### <a name="codec-compressed-audio-input"></a>编解码器压缩的音频输入

一些语音 SDK 编程语言支持编解码器压缩的音频输入流。 有关详细信息，请参阅<a href="/azure/cognitive-services/speech-service/how-to-use-codec-compressed-audio-input-streams" target="_blank">使用压缩的音频输入格式 </a>。

可在以下平台上使用编解码器压缩的音频输入：

  - C++/Linux
  - C#/Linux
  - Java/Linux、Android 和 iOS

## <a name="rest-api"></a>REST API

虽然语音 SDK 涵盖了语音服务的许多功能，但对于某些方案，你可能需要使用 REST API。

### <a name="batch-transcription"></a>批量听录

使用[批量听录](batch-transcription.md)能够以异步方式对大量的数据进行语音转文本听录。 只能通过 REST API 使用批量听录。 除了将语音音频转换为文本，批量语音转文本还允许进行分割聚类和情感分析。

## <a name="customization"></a>自定义

语音服务在语音转文本、文本转语音和语音翻译方面提供了强大的功能和默认模型。 有时，你可能希望提高基线性能，以便更好地处理你的独特用例。 语音服务有各种各样的无代码自定义工具，这些工具使上述事项变得简单，并使你能够使用基于你自己的数据的自定义模型获得竞争优势。 这些模型将仅供你和你的组织使用。

### <a name="custom-speech-to-text"></a>自定义语音转文本

使用语音转文本在独特的环境中进行识别和听录时，可以创建并训练自定义的声学、语言和发音模型，以解决环境干扰或行业特定的词汇的问题。 可通过[自定义语音识别门户](./custom-speech-overview.md)来创建和管理无代码自定义语音识别模型。 自定义语音识别模型在发布后可以由语音 SDK 使用。

### <a name="custom-text-to-speech"></a>自定义文本转语音

自定义文本转语音（也称为“自定义语音”）是一组在线工具，用于为你的品牌创建可识别的独一无二的语音。 可通过[“自定义语音”门户](https://aka.ms/customvoice)来创建和管理无代码“自定义语音”模型。 “自定义语音”模型在发布后可以由语音 SDK 使用。

## <a name="get-the-speech-sdk"></a>获取语音 SDK

# <a name="windows"></a>[Windows](#tab/windows)

[!INCLUDE [Get the Speech SDK](includes/get-speech-sdk-windows.md)]

# <a name="linux"></a>[Linux](#tab/linux)

[!INCLUDE [Get the Speech SDK](includes/get-speech-sdk-linux.md)]

# <a name="ios"></a>[iOS](#tab/ios)

[!INCLUDE [Get the Speech SDK](includes/get-speech-sdk-ios.md)]

# <a name="macos"></a>[macOS](#tab/macos)

[!INCLUDE [Get the Speech SDK](includes/get-speech-sdk-macos.md)]

# <a name="android"></a>[Android](#tab/android)

[!INCLUDE [Get the Speech SDK](includes/get-speech-sdk-android.md)]

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

[!INCLUDE [Get the Node.js Speech SDK](includes/get-speech-sdk-nodejs.md)]

# <a name="browser"></a>[浏览器](#tab/browser)

[!INCLUDE [Get the Browser Speech SDK](includes/get-speech-sdk-browser.md)]

---

[!INCLUDE [License notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

[!INCLUDE [Sample source code](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]

## <a name="next-steps"></a>后续步骤

* [创建免费 Azure 帐户](https://azure.microsoft.com/free/cognitive-services/)
* [了解如何在 C# 中识别语音](./get-started-speech-to-text.md?pivots=programming-language-csharp&tabs=dotnet)
