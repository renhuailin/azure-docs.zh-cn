---
title: 语音设备 SDK - 语音服务
titleSuffix: Azure Cognitive Services
description: 语音设备 SDK 入门。 “语音服务”适用于多种设备和音频源。 语音设备 SDK 是与特制的麦克风阵列开发工具包配对的一个预优化库。
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: lajanuar
ms.openlocfilehash: 2f86d64785cc0d235e4923bacb26447f7015267c
ms.sourcegitcommit: e7d500f8cef40ab3409736acd0893cad02e24fc0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122070749"
---
# <a name="what-is-the-speech-devices-sdk"></a>什么是语音设备 SDK？

[语音服务](overview.md)适用于多种设备和音频源。 现在，可以通过匹配的硬件和软件进一步利用语音应用程序。 语音设备 SDK 是与特制的麦克风阵列开发工具包配对的一个预优化库。

语音设备 SDK 有助于：

- 快速测试新的语音方案。
- 更轻松地将基于云的语音服务集成到设备中。
- 为客户创建出色的用户体验。

语音设备 SDK 使用[语音 SDK](speech-sdk.md)。 将我们的高级音频处理算法与设备的麦克风阵列配合使用，将音频发送到[语音服务](overview.md)。 它通过噪声抑制、回声消除、波束赋形和混响消减来提供准确的远场[语音识别](speech-to-text.md)。

你还可以使用语音设备 SDK 来构建具有你自己的[自定义关键字](./custom-keyword-basics.md)的环境设备。 自定义关键字提供一个启动用户交互的提示，该提示是品牌特有的。

该语音设备 SDK 支持多种启用语音的方案，例如[语音助理](./voice-assistants.md)、免下车点单系统、[对话听录](./conversation-transcription.md)和智能扬声器。 可以使用文本回复用户、用默认或[自定义语音](./how-to-custom-voice-create-voice.md)回答用户、提供搜索结果以及[翻译](speech-translation.md)为其他语言等。 我们期待看到你的成果！

## <a name="get-the-speech-devices-sdk"></a>获取语音设备 SDK

### <a name="android"></a>Android

适用于 Android 的语音设备 SDK 支持 [Roobo v1](speech-devices-sdk-roobo-v1.md) 和等效设备，请为这些设备下载 [Android 语音设备 SDK](https://aka.ms/sdsdk-download-android) 的最新版本。


如果使用其他 Android 设备（如手机或移动设备），请从 [Android 语音 SDK](speech-sdk.md) 开始


### <a name="windows"></a>Windows

对于 Windows，示例应用程序以跨平台 Java 应用程序的形式提供。 下载最新版本的 [JRE 语音设备 SDK](https://aka.ms/sdsdk-download-JRE)。
该应用程序是使用语音 SDK 程序包和 Eclipse Java IDE (v4) 在 64 位 Windows 上构建的。 它在 64 位 Java 8 运行时环境 (JRE) 中运行。

### <a name="linux"></a>Linux

对于 Linux，示例应用程序以跨平台 Java 应用程序的形式提供。 下载最新版本的 [JRE 语音设备 SDK](https://aka.ms/sdsdk-download-JRE)。
该应用程序是使用语音 SDK 程序包和 Eclipse Java IDE (v4) 在 64 位 Linux（Ubuntu 16.04、Ubuntu 18.04、Debian 9、RHEL 8、CentOS 8）上构建的。 它在 64 位 Java 8 运行时环境 (JRE) 中运行。

提供了其他二进制文件来支持即将推出的设备，例如 [Roobo v2 DDK](https://aka.ms/sdsdk-download-roobov2)、[Urbetter DDK](https://aka.ms/sdsdk-download-urbetter)、[GGEC 扬声器](https://aka.ms/sdsdk-download-speaker)、[Linux ARM32](https://aka.ms/sdsdk-download-linux-arm32) 和 [Linux ARM64](https://aka.ms/sdsdk-download-linux-arm64)。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [选择语音设备](get-speech-devices-sdk.md)
> [!div class="nextstepaction"]
> [免费获取语音服务订阅密钥](overview.md#try-the-speech-service-for-free)