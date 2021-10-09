---
title: 管理客户端上的通话记录
titleSuffix: An Azure Communication Services how-to guide
description: 使用 Azure 通信服务 SDK 管理客户端上的通话记录。
author: probableprime
ms.author: rifox
ms.service: azure-communication-services
ms.topic: how-to
ms.subservice: calling
ms.date: 08/10/2021
ms.custom: template-how-to
zone_pivot_groups: acs-web-ios-android
ms.openlocfilehash: 5ef33ef46b208f66fa7d31b20bb603cd543880f6
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128699104"
---
# <a name="manage-call-recording-on-the-client"></a>管理客户端上的通话记录

[!INCLUDE [Public Preview Disclaimer](../../includes/public-preview-include-document.md)]

[通话记录](../../concepts/voice-video-calling/call-recording.md)允许用户记录他们使用 Azure 通信服务进行的通话。 本文介绍如何管理客户端上的记录。 在此功能正常工作之前，需要设置[服务器端](../../quickstarts/voice-video-calling/call-recording-sample.md)记录。

## <a name="prerequisites"></a>先决条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 
- 已部署的通信服务资源。 [创建通信服务资源](../../quickstarts/create-communication-resource.md)。
- 用于启用通话客户端的用户访问令牌。 有关详细信息，请参阅[创建和管理访问令牌](../../quickstarts/access-tokens.md)。
- 可选：完成[向应用程序添加语音呼叫](../../quickstarts/voice-video-calling/getting-started-with-calling.md)的快速入门

::: zone pivot="platform-web"
[!INCLUDE [Record Calls Client-side JavaScript](./includes/record-calls/record-calls-web.md)]
::: zone-end

::: zone pivot="platform-android"
[!INCLUDE [Record Calls Client-side Android](./includes/record-calls/record-calls-android.md)]
::: zone-end

::: zone pivot="platform-ios"
[!INCLUDE [Record Calls Client-side iOS](./includes/record-calls/record-calls-ios.md)]
::: zone-end

## <a name="next-steps"></a>后续步骤
- [了解如何管理通话](./manage-calls.md)
- [了解如何管理视频](./manage-video.md)
- [了解如何转录通话](./call-transcription.md)