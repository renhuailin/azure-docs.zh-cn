---
title: 快速入门：用于 Java（Windows、Linux、macOS）的语音 SDK 平台设置 - 语音服务
titleSuffix: Azure Cognitive Services
description: 使用本指南，设置可将 Java（Windows、Linux、macOS）与语音服务 SDK 配合使用的平台。
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/15/2020
ms.custom: devx-track-java
ms.author: lajanuar
ms.openlocfilehash: 3ae4b6cd241e798305f2e8d1ce3acd61a32d337a
ms.sourcegitcommit: e7d500f8cef40ab3409736acd0893cad02e24fc0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122070000"
---
本指南介绍如何安装用于 64 位 Java 8 JRE 的[语音 SDK](~/articles/cognitive-services/speech-service/speech-sdk.md)。 如果只是需要包名称以便自行开始，那么请知悉在 Maven 中央存储库中未提供 Java SDK。 无论是使用 Gradle 还是 `pom.xml` 依赖项文件，都需要添加指向 `https://csspeechstorage.blob.core.windows.net/maven/` 的自定义存储库（请参阅下文了解包名称）。

> [!NOTE]
> 对于语音设备 SDK 和 Roobo 设备，请参阅[语音设备 SDK](~/articles/cognitive-services/speech-service/speech-devices-sdk.md)。

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="supported-operating-systems"></a>支持的操作系统

- Java 语音 SDK 包适用于以下操作系统：
  - Windows：仅 64 位
  - Mac：macOS X 10.13 或更高版本
  - Linux：请参阅[受支持的 Linux 分发和目标体系结构](~/articles/cognitive-services/speech-service/speech-sdk.md)的列表。

## <a name="prerequisites"></a>先决条件

- [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) 或 [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)

- [Eclipse Java IDE](https://www.eclipse.org/downloads/)（要求已安装 Java）

- 在 Windows 上，需要安装适用于平台的 [Microsoft Visual C++ Redistributable for Visual Studio 2019](https://support.microsoft.com/topic/the-latest-supported-visual-c-downloads-2647da03-1eea-4433-9aff-95f26a218cc0)。 首次安装时，可能需要重启。

- 在 Linux 上，请参阅[系统要求和安装说明](~/articles/cognitive-services/speech-service/speech-sdk.md#get-the-speech-sdk)。

## <a name="gradle-config"></a>Gradle 配置

Gradle 配置既需要自定义存储库，也需要显式引用依赖项扩展 `.jar`。

```groovy
// build.gradle

repositories {
    maven {
        url "https://csspeechstorage.blob.core.windows.net/maven/"
    }
}

dependencies {
    implementation group: 'com.microsoft.cognitiveservices.speech', name: 'client-sdk', version: "1.18.0", ext: "jar"
}
```

## <a name="create-an-eclipse-project-and-install-the-speech-sdk"></a>创建 Eclipse 项目并安装语音 SDK

[!INCLUDE [](~/includes/cognitive-services-speech-service-quickstart-java-create-proj.md)]

## <a name="next-steps"></a>后续步骤

[!INCLUDE [windows](../quickstart-list.md)]
