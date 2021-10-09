---
title: 在客户端上显示通话听录状态
titleSuffix: An Azure Communication Services how-to guide
description: 使用 Azure 通信服务 SDK 显示通话听录状态
author: probableprime
ms.author: rifox
ms.service: azure-communication-services
ms.subservice: calling
ms.topic: how-to
ms.date: 08/10/2021
ms.custom: template-how-to
zone_pivot_groups: acs-plat-ios-android
ms.openlocfilehash: 9304f7905b12362f8026e59af8cf294ed76fc42e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128635734"
---
# <a name="display-call-transcription-state-on-the-client"></a>在客户端上显示通话听录状态

[!INCLUDE [Public Preview Disclaimer](../../includes/public-preview-include-document.md)]

使用通话听录时，可能需要让用户知道正在听录通话。 操作方法如下。

## <a name="prerequisites"></a>先决条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 
- 已部署的通信服务资源。 [创建通信服务资源](../../quickstarts/create-communication-resource.md)。
- 用于启用通话客户端的用户访问令牌。 有关详细信息，请参阅[创建和管理访问令牌](../../quickstarts/access-tokens.md)。
- 可选：完成快速入门[向应用程序添加语音呼叫](../../quickstarts/voice-video-calling/getting-started-with-calling.md)

::: zone pivot="platform-android"
[!INCLUDE [Call transcription client-side Android](./includes/call-transcription/call-transcription-android.md)]
::: zone-end

::: zone pivot="platform-ios"
[!INCLUDE [Call transcription client-side iOS](./includes/call-transcription/call-transcription-ios.md)]
::: zone-end

## <a name="next-steps"></a>后续步骤
- [了解如何管理视频](./manage-video.md)
- [了解如何管理通话](./manage-calls.md)
- [了解如何记录通话](./record-calls.md)