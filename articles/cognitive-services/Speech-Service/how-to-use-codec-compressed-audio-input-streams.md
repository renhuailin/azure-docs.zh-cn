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
ms.openlocfilehash: ace584b51f0aa457e30d8e48d97834aa735a1a57
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/21/2020
ms.locfileid: "95026567"
---
# <a name="use-codec-compressed-audio-input-with-the-speech-sdk"></a>在语音 SDK 中使用编解码器压缩的音频输入

语音服务 SDK 压缩音频输入流 API 提供了一种使用 `PullStream` 或 `PushStream` 将压缩音频流式传输到语音服务的方法。

平台 | 语言 | 支持的适用 gstreamer 版本
| :--- | ---: | :---:
Windows (排除 UWP)   | C + +、c #、Java、Python | [busybox-1.15.1-20.el6.x86](https://gstreamer.freedesktop.org/data/pkg/windows/1.15.1/)
Linux  | C + +、c #、Java、Python | [支持的 Linux 分发和目标体系结构](~/articles/cognitive-services/speech-service/speech-sdk.md)
Android  | Java | [1.14.4](https://gstreamer.freedesktop.org/data/pkg/android/1.14.4/)

## <a name="speech-sdk-version-required-for-compressed-audio-input"></a>压缩的音频输入所需的语音 SDK 版本
* RHEL 8 和 CentOS 8 需要语音 SDK 1.10.0 或更高版本
* Windows 需要语音 SDK 版本 1.11.0 或更高版本。

[!INCLUDE [supported-audio-formats](includes/supported-audio-formats.md)]

## <a name="prerequisites"></a>先决条件

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