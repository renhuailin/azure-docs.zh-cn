---
title: 快速入门：用于 C# Unity 的语音 SDK 平台设置 - 语音服务
titleSuffix: Azure Cognitive Services
description: 使用本指南，通过语音服务 SDK 设置用于 C# Unity 的平台。
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/15/2020
ms.author: lajanuar
ms.custom: devx-track-csharp
ms.openlocfilehash: c244009dffab84938c06eee10d7031bc23f10e7a
ms.sourcegitcommit: e7d500f8cef40ab3409736acd0893cad02e24fc0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122069993"
---
本指南介绍如何安装用于 [Unity](https://unity3d.com/) 的[语音 SDK](~/articles/cognitive-services/speech-service/speech-sdk.md)。

> [!NOTE]
> 适用于 Unity 的语音 SDK 支持 Windows 桌面版（x86 和 x64）或通用 Windows 平台（x86、x64、ARM/ARM64）、Android（x86、ARM32/64）、iOS（x64 模拟器和 ARM64）和 Mac (x64)。

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="prerequisites"></a>先决条件

本快速入门需要：

- 在 Windows 上，需要安装适用于平台的 [Microsoft Visual C++ Redistributable for Visual Studio 2019](https://support.microsoft.com/en-us/topic/the-latest-supported-visual-c-downloads-2647da03-1eea-4433-9aff-95f26a218cc0)。 首次安装时，可能需要重启。
- [Unity 2018.3 或更高版本](https://store.unity.com/)，以及[支持 UWP ARM64 的 Unity 2019.1](https://blogs.unity3d.com/2019/04/16/introducing-unity-2019-1/#universal)。
- [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)。 也可以使用 Visual Studio 2017 版本 15.9 或更高版本。
- 为了支持 Windows ARM64，请安装[适用于 ARM64 的可选版本工具，以及适用于 ARM64 的 Windows 10 SDK](https://blogs.windows.com/buildingapps/2018/11/15/official-support-for-windows-10-on-arm-development/)
- 在 Android 上，你需要一台可用于开发的基于 ARM 的 Android 设备（API 23：Android 6.0 Marshmallow 或更高版本），并配备正常工作的麦克风。
- 在 iOS 上，你需要一台可用于开发的 iOS 设备 (ARM64)，并配备正常工作的麦克风。
- 在 macOS 上，你需要 Mac 设备 (x64) 和最新 LTS 版本的 Unity 2019（或更高版本），以便在 Unity Player 设置中实现对麦克风访问的集成支持。

## <a name="install-the-speech-sdk"></a>安装语音 SDK

若要安装适用于 Unity 的语音 SDK，请执行以下步骤：

1. 下载并打开[适用于 Unity 的语音 SDK](https://aka.ms/csspeech/unitypackage)，该程序打包为 Unity 资产包 (.unitypackage)，应该已经与 Unity 关联。 打开资产包后，会显示“导入 Unity 包”对话框。 可能需要创建并打开一个空项目才能使用此步骤。

   [![Unity 编辑器中的“导入 Unity 包”对话框](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-unity-01-import.png)](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-unity-01-import.png#lightbox)

1. 确保选择所有文件，然后选择“导入”。 片刻之后，Unity 资产包即会导入到项目中。

有关将资产包导入 Unity 的详细信息，请参阅 [Unity 文档](https://docs.unity3d.com/Manual/AssetPackages.html)。

## <a name="next-steps"></a>后续步骤

[!INCLUDE [windows](../quickstart-list.md)]
