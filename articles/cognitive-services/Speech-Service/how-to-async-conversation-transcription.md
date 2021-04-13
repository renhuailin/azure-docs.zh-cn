---
title: 异步对话听录 - 语音服务
titleSuffix: Azure Cognitive Services
description: 了解如何通过语音服务使用异步对话听录。 仅适用于 Java 和 C#。
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: amishu
ms.custom: devx-track-csharp
zone_pivot_groups: programming-languages-set-twenty-one
ms.openlocfilehash: 4b2dfa8d474f10d6b4ca1c46ac2b575e8d8407ff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "88934629"
---
# <a name="asynchronous-conversation-transcription"></a>异步对话听录

在本文中，使用 RemoteConversationTranscriptionClient API 演示异步对话听录。 如果已经配置对话听录来进行异步听录且具有 `conversationId`，则可以使用 RemoteConversationTranscriptionClient API 来获取与 `conversationId` 关联的听录。

## <a name="asynchronous-vs-real-time--asynchronous"></a>异步与实时 + 异步

通过异步听录，将对话音频进行流式传输，但是不需要实时返回的听录。 相反，发送音频后，使用 `Conversation` 的 `conversationId` 来查询异步听录的状态。 异步听录准备就绪后，将获得 `RemoteConversationTranscriptionResult`。

通过实时增强异步，你可以实时地获取听录，也可以通过使用 `conversationId`（类似于异步场景）查询来获得听录。

完成异步听录需要执行两个步骤。 第一步是上传音频：选择仅异步或实时增强异步。 第二步是获取听录结果。

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/how-to/remote-conversation/csharp/examples.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [prerequisites](includes/how-to/remote-conversation/java/examples.md)]
::: zone-end


## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [浏览 GitHub 上的示例](https://aka.ms/csspeech/samples)
