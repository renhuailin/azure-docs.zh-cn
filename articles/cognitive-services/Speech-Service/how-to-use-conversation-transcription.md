---
title: 实时对话对话快速入门-语音服务
titleSuffix: Azure Cognitive Services
description: 了解如何通过语音 SDK 使用实时对话。 利用会话会话，你可以通过将音频流式传输到语音服务来转录会议和其他对话。
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/20/2020
ms.author: trbye
zone_pivot_groups: acs-js-csharp
ms.openlocfilehash: ae3d7b329c55ce0be101cee73e1fc7674a2d75aa
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/21/2020
ms.locfileid: "95026533"
---
# <a name="get-started-with-real-time-conversation-transcription"></a>实时对话脚本入门

通过语音 SDK 的 **ConversationTranscriber** API，你可以通过使用或将音频流式传输到语音服务，使用添加、删除和标识多个参与者的能力转录会议和其他对话 `PullStream` `PushStream` 。 首先使用 REST API 为每个参与者创建语音签名，然后将语音签名与 SDK 结合使用来转录会话。 有关详细信息，请参阅会话脚本 [概述](conversation-transcription.md) 。

## <a name="limitations"></a>限制

* 仅可用于以下订阅区域： `centralus` 、 `eastasia` 、 `eastus` 、 `westeurope`
* 需要一个带有播放引用流的7个 mic 循环多麦克风阵列。 麦克风阵列应满足 [我们的规范](./speech-devices-sdk-microphone.md)。
* [语音设备 SDK](speech-devices-sdk.md)提供了合适的设备和演示会话脚本的示例应用。

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
> [异步会话](how-to-async-conversation-transcription.md) 
>  脚本[ROOBO 设备示例代码](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK/blob/master/Samples/Java/Android/Speech%20Devices%20SDK%20Starter%20App/example/app/src/main/java/com/microsoft/cognitiveservices/speech/samples/sdsdkstarterapp/ConversationTranscription.java) 
> [Azure Kinect 开发工具包示例代码](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK/blob/master/Samples/Java/Windows_Linux/SampleDemo/src/com/microsoft/cognitiveservices/speech/samples/Cts.java)