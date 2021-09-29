---
title: Azure 通信服务中的语音和视频概念
titleSuffix: An Azure Communication Services concept document
description: 了解通信服务呼叫类型。
author: probableprime
manager: chpalm
services: azure-communication-services
ms.author: rifox
ms.date: 06/30/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.subservice: calling
ms.openlocfilehash: 9fd2de1bdcd89f027db9a3fd74ef40111f540ced
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128635957"
---
# <a name="voice-and-video-concepts"></a>语音和视频概念

[!INCLUDE [SDP Plan B Deprecation Notice](../../includes/plan-b-sdp-deprecation.md)]

可以使用 Azure 通信服务进行和接收一对一或群组语音和视频通话。 你可以对其他连接到 Internet 的设备和旧式电话进行呼叫。 你可以使用通信服务 JavaScript、Android 或 iOS SDK 来构建允许用户在私人对话或小组讨论中彼此交谈的应用程序。 Azure 通信服务支持与服务或机器人之间的呼叫。

## <a name="call-types-in-azure-communication-services"></a>Azure 通信服务中的呼叫类型

在 Azure 通信服务中可以启动多种类型的呼叫。 你的呼叫类型决定信令架构、媒体流量和定价模型。

### <a name="voice-over-ip-voip"></a>IP 语音 (VoIP)

当应用程序的用户通过 Internet 或数据连接呼叫应用程序的另一个用户时，该呼叫是通过 IP 语音 (VoIP) 进行的。 在这种情况下，信令和媒体流通过 Internet 发出。

### <a name="public-switched-telephone-network-pstn"></a>公用电话交换网 (PSTN)

每当你的用户与传统电话号码交互时，将通过 PSTN（公用电话交换网）语音呼叫来辅助完成呼叫。 若要拨打和接收 PSTN 呼叫，需要向 Azure 通信服务资源添加电话功能。 在这种情况下，信令和媒体结合使用基于 IP 和基于 PSTN 的技术来连接用户。

### <a name="one-to-one-call"></a>一对一呼叫

当你的一个用户使用我们的一个 SDK 连接到另一个用户时，会发生 Azure 通信服务的一对一呼叫。 呼叫可以是 VoIP 或 PSTN。

### <a name="group-call"></a>组呼叫

如果有三个或更多个参与者相互连接，会发生 Azure 通信服务的组呼叫。 连接 VoIP 和 PSTN 的用户的任意组合都可以出现在组呼叫中。 一对一呼叫可以通过向呼叫中添加更多参与者来转换为组呼叫。 其中一个参与者可以是机器人。

### <a name="supported-video-standards"></a>支持的视频标准
我们支持 H.264 (MPEG-4)。

### <a name="video-quality"></a>视频质量
我们在本机 (iOS, Android) SDK 上最多可支持全高清 1080p。 对于 Web (JS) SDK，我们支持标准 HD 720p。 质量取决于可用带宽。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [呼叫入门](../../quickstarts/voice-video-calling/getting-started-with-calling.md)

有关详细信息，请参阅以下文章：
- 熟悉常规[呼叫流](../call-flows.md)
- [电话号码类型](../telephony-sms/plan-solution.md)
- 了解如何[调用 SDK 功能](../voice-video-calling/calling-sdk-features.md)
