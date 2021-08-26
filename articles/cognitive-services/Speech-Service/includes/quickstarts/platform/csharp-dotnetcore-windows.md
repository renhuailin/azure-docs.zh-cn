---
title: 快速入门：用于 C# .NET Core 的语音 SDK 平台设置 - 语音服务
titleSuffix: Azure Cognitive Services
description: 使用本指南，通过语音服务 SDK 在 Windows 或 macOS 的 .NET Core 下设置 C# 平台。
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/15/2020
ms.author: lajanuar
ms.openlocfilehash: 7c05c58d1994b32703e2900070405aea30e39479
ms.sourcegitcommit: e7d500f8cef40ab3409736acd0893cad02e24fc0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122069996"
---
本指南介绍如何安装用于 C# .NET Core 的[语音 SDK](~/articles/cognitive-services/speech-service/speech-sdk.md)。 如果只是需要包名称以便自行开始，请在 NuGet 控制台中运行 `Install-Package Microsoft.CognitiveServices.Speech`。

> [!NOTE]
> .NET Core 是一个实现了 [.NET Standard](/dotnet/standard/net-standard) 规范的开源跨平台 .NET 平台。

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="prerequisites"></a>先决条件

本快速入门需要：

* 在 Windows 上，需要安装适用于平台的 [Microsoft Visual C++ Redistributable for Visual Studio 2019](https://support.microsoft.com/en-us/topic/the-latest-supported-visual-c-downloads-2647da03-1eea-4433-9aff-95f26a218cc0)。 首次安装时，可能需要重启。
* [.NET Core SDK](https://dotnet.microsoft.com/download)
* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) 或更高版本

## <a name="create-a-visual-studio-project-and-install-the-speech-sdk"></a>创建 Visual Studio 项目并安装语音 SDK

[!INCLUDE [](~/includes/cognitive-services-speech-service-quickstart-dotnetcore-create-proj.md)]

## <a name="next-steps"></a>后续步骤

[!INCLUDE [windows](../quickstart-list.md)]