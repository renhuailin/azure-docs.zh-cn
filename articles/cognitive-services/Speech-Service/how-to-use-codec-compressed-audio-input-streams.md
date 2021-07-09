---
title: 使用语音 SDK 流式传输编解码器压缩的音频 - 语音服务
titleSuffix: Azure Cognitive Services
description: 了解如何使用语音 SDK 将压缩音频流式传输到语音服务。 适用于用于 Linux 的 C++、C# 和 Java，Android 中的 Java 和 iOS 中的 Objective-C。
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: amishu
ms.custom: devx-track-csharp
zone_pivot_groups: programming-languages-set-twenty-two
ms.openlocfilehash: 183679eb9c4eb2480736dfb4cec8a343c67d0ed7
ms.sourcegitcommit: 67cdbe905eb67e969d7d0e211d87bc174b9b8dc0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/09/2021
ms.locfileid: "111854464"
---
# <a name="use-codec-compressed-audio-input"></a>使用通过编解码器压缩的音频输入

语音 SDK 和语音 CLI 可以使用 GStreamer 接收压缩的音频格式。 GStreamer 会先解压缩音频，然后再将音频作为原始 PCM 通过网络发送到语音服务。

平台 | 语言 | 支持的 GStreamer 版本
| :--- | ---: | :---:
Windows（不包括 UWP）  | C++、C#、Java、Python | [1.18.3](https://gstreamer.freedesktop.org/data/pkg/windows/1.18.3/)
Linux  | C++、C#、Java、Python | [受支持的 Linux 发行版和目标体系结构](~/articles/cognitive-services/speech-service/speech-sdk.md)
Android  | Java | [1.18.3](https://gstreamer.freedesktop.org/data/pkg/android/1.18.3/)

## <a name="speech-sdk-version-required-for-compressed-audio-input"></a>压缩的音频输入所需的语音 SDK 版本
* RHEL 8 和 CentOS 8 需要语音 SDK 1.10.0 或更高版本
* Windows 需要语音 SDK 版本 1.11.0 或更高版本。
* 适用于 Windows 和 Android 上的最新 GStreamer 的语音 SDK 版本 1.16.0 或更高版本。

[!INCLUDE [supported-audio-formats](includes/supported-audio-formats.md)]

## <a name="gstreamer-required-to-handle-compressed-audio"></a>处理压缩音频所需的 GStreamer

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/csharp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-cpp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/cpp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/java/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/python/prerequisites.md)]
::: zone-end

## <a name="example-code-using-codec-compressed-audio-input"></a>使用编解码器压缩的音频输入的示例代码

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/csharp/examples.md)]
::: zone-end

::: zone pivot="programming-language-cpp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/cpp/examples.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/java/examples.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/python/examples.md)]
::: zone-end

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解如何识别语音](./get-started-speech-to-text.md)
