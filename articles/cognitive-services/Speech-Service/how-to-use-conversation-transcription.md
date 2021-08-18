---
title: 实时对话听录快速入门 - 语音服务
titleSuffix: Azure Cognitive Services
description: 了解如何通过语音 SDK 使用实时对话听录。 通过对话听录可以听录会议和其他对话（能够将音频流式传输到语音服务来添加、删除和标识多个参与者）。
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/20/2020
ms.author: lajanuar
zone_pivot_groups: acs-js-csharp
ms.openlocfilehash: e42c457f042c43b109055c8445690412d87a7700
ms.sourcegitcommit: e7d500f8cef40ab3409736acd0893cad02e24fc0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122068968"
---
# <a name="get-started-with-real-time-conversation-transcription"></a>开始使用实时对话听录

语音 SDK 的 ConversationTranscriber API 可以听录会议和其他对话（使用 `PullStream` 或 `PushStream` 将音频流式传输到语音服务来添加、删除和标识多个参与者）。 首先使用 REST API 为每个参与者创建声音签名，然后结合使用声音签名和 SDK 来听录对话。 有关详细信息，请参阅对话听录[概述](conversation-transcription.md)。

## <a name="limitations"></a>限制

* 仅在以下订阅区域中可用：`centralus``eastasia`、`eastus`、`westeurope`
* 需要一个具有 7 个麦克风的循环多麦克风阵列。 麦克风阵列应满足[我们的规格](./speech-devices-sdk-microphone.md)。
* [语音设备 SDK](speech-devices-sdk.md) 提供多个设备和一个演示对话听录的示例应用。

## <a name="prerequisites"></a>先决条件

本文假定你有 Azure 帐户和语音服务订阅。 如果你没有帐户和订阅，[可以免费试用语音服务](overview.md#try-the-speech-service-for-free)。

::: zone pivot="programming-language-javascript"
[!INCLUDE [JavaScript Basics include](includes/how-to/conversation-transcription/real-time-javascript.md)]
::: zone-end

::: zone pivot="programming-language-csharp"
[!INCLUDE [C# Basics include](includes/how-to/conversation-transcription/real-time-csharp.md)]
::: zone-end

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [异步对话听录](how-to-async-conversation-transcription.md)
> [ROOBO 设备示例代码](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK/blob/master/Samples/Java/Android/Speech%20Devices%20SDK%20Starter%20App/example/app/src/main/java/com/microsoft/cognitiveservices/speech/samples/sdsdkstarterapp/ConversationTranscription.java)
> [Azure Kinect Dev Kit 示例代码](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK/blob/master/Samples/Java/Windows_Linux/SampleDemo/src/com/microsoft/cognitiveservices/speech/samples/Cts.java)