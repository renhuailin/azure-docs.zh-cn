---
title: 快速入门：用于 C# (Xamarin) 的语音 SDK 平台设置 - 语音服务
titleSuffix: Azure Cognitive Services
description: 使用本指南，通过语音服务 SDK 设置用于 C# Xamarin 的平台。
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/15/2020
ms.author: pafarley
ms.custom: devx-track-csharp
ms.openlocfilehash: f3156439d4f1ece7f2e63509328d4b17f0d91ad7
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/07/2021
ms.locfileid: "123544083"
---
本指南介绍如何安装用于 [Xamarin](/xamarin/get-started/what-is-xamarin) 的[语音 SDK](~/articles/cognitive-services/speech-service/speech-sdk.md)，这是一种开源平台，用于为装有 .NET 的 iOS、Android 和 Windows 构建现代的高性能应用程序。 如果只是需要包名称以便自行开始，请在 NuGet 控制台中运行 `Install-Package Microsoft.CognitiveServices.Speech`。

> [!NOTE]
> 适用于 Xamarin 的语音 SDK 支持 Windows 桌面版（x86 和 x64）或通用 Windows 平台（x86、x64、ARM/ARM64）、Android（x86、ARM32/64）和 iOS（x64 模拟器和 ARM64）。

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="prerequisites"></a>先决条件

本快速入门需要：

* 在 Windows 上，需要安装适用于平台的 [Microsoft Visual C++ Redistributable for Visual Studio 2019](https://support.microsoft.com/en-us/topic/the-latest-supported-visual-c-downloads-2647da03-1eea-4433-9aff-95f26a218cc0)。 首次安装时，可能需要重启。
* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)

## <a name="create-a-visual-studio-project-and-install-the-speech-sdk"></a>创建 Visual Studio 项目并安装语音 SDK

[!INCLUDE [](~/includes/cognitive-services-speech-service-quickstart-xamarin-create-proj.md)]

语音 SDK 现已安装。 现在可以删除或重复使用在前面的步骤中创建的“helloworld”项目。

## <a name="next-steps"></a>后续步骤

[!INCLUDE [windows](../quickstart-list.md)]
