---
title: 快速入门：用于 C# 通用 Windows 平台 (UWP) 平台设置的语音 SDK - 语音服务
titleSuffix: Azure Cognitive Services
description: 使用本指南，通过语音服务 SDK 在通用 Windows 平台 (UWP) 下设置 C# 平台。
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/15/2020
ms.author: lajanuar
ms.openlocfilehash: a6f76423a0dfc88bc588946014e19c5f6db84d86
ms.sourcegitcommit: e7d500f8cef40ab3409736acd0893cad02e24fc0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122069992"
---
本指南介绍如何安装用于 C# 通用 Windows 平台 (UWP) 的[语音 SDK](~/articles/cognitive-services/speech-service/speech-sdk.md)。 如果只是需要包名称以便自行开始，请在 NuGet 控制台中运行 `Install-Package Microsoft.CognitiveServices.Speech`。

> [!NOTE]
> 通用 Windows 平台允许开发在支持 Windows 10 的任何设备上运行的应用，包括电脑、Xbox、Surface Hub 和其他设备。

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="prerequisites"></a>先决条件

本快速入门需要：

* 在 Windows 上，需要安装适用于平台的 [Microsoft Visual C++ Redistributable for Visual Studio 2019](https://support.microsoft.com/en-us/topic/the-latest-supported-visual-c-downloads-2647da03-1eea-4433-9aff-95f26a218cc0)。 首次安装时，可能需要重启。
* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)（版本不限）

## <a name="create-a-visual-studio-project-and-install-the-speech-sdk"></a>创建 Visual Studio 项目并安装语音 SDK

[!INCLUDE [](~/includes/cognitive-services-speech-service-quickstart-uwp-create-proj.md)]

## <a name="next-steps"></a>后续步骤

[!INCLUDE [windows](../quickstart-list.md)]