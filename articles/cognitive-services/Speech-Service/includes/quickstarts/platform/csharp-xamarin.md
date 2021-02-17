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
ms.author: erhopf
ms.custom: devx-track-csharp
ms.openlocfilehash: 8c941f0c96c127a46bb0b888d0500223ebcd9be2
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552835"
---
本指南介绍如何安装用于 [Xamarin](/xamarin/get-started/what-is-xamarin) 的[语音 SDK](~/articles/cognitive-services/speech-service/speech-sdk.md)，这是一种开源平台，用于为装有 .NET 的 iOS、Android 和 Windows 构建现代的高性能应用程序。 如果只是需要包名称以便自行开始，请在 NuGet 控制台中运行 `Install-Package Microsoft.CognitiveServices.Speech`。

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="prerequisites"></a>先决条件

本快速入门需要：

* 在 Windows 上，需要安装适用于平台的 [Microsoft Visual C++ Redistributable for Visual Studio 2019](https://support.microsoft.com/en-us/topic/the-latest-supported-visual-c-downloads-2647da03-1eea-4433-9aff-95f26a218cc0)。 首次安装时，可能需要重启。
* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)

## <a name="create-a-visual-studio-project-and-install-the-speech-sdk"></a>创建 Visual Studio 项目并安装语音 SDK

[!INCLUDE [](~/includes/cognitive-services-speech-service-quickstart-xamarin-create-proj.md)]

语音 SDK 现已安装。 现在可以删除或重复使用在前面的步骤中创建的“helloworld”项目。

现在可以继续完成下面的[后续步骤](#next-steps)。

## <a name="next-steps"></a>后续步骤

[!INCLUDE [windows](../quickstart-list.md)]