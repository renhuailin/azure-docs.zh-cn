---
title: 客户端和服务器体系结构
titleSuffix: An Azure Communication Services concept document
description: 了解通信服务的体系结构。
author: mikben
manager: mikben
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 74a48b05e7e3b00d331da4fcf66092490ae70374
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "103490664"
---
# <a name="client-and-server-architecture"></a>客户端和服务器体系结构

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]


<!--
> [!WARNING]
> This document is under construction and needs the following items to be addressed:
> - Need to add security best practices for token management here
> - Reference docs:
> - https://docs.microsoft.com/windows/security/threat-protection/security-policy-settings/create-a-token-object
> - https://docs.microsoft.com/azure/aks/operator-best-practices-identity
> - https://docs.microsoft.com/cloud-app-security/api-tokens?view=gestures-1.0-->

每个 Azure 通信服务应用程序都将具有客户端应用程序，后者使用服务来促进人们之间的联系 。 本页面介绍各种方案中的常见体系结构元素。

## <a name="user-access-management"></a>用户访问管理

Azure 通信服务客户端库需要 `user access tokens` 来安全地访问通信服务资源。 由于令牌的敏感特性以及生成令牌所需的连接字符串，`User access tokens` 应该由受信任的服务生成和管理。 如果无法适当管理访问令牌，则可能会因滥用资源而导致额外的费用。 强烈建议使用受信任的服务进行用户管理。 受信任的服务将生成令牌，并使用适当的加密将它们传递回客户端。 下面是一个示例体系结构流：

:::image type="content" source="../media/scenarios/archdiagram-access.png" alt-text="显示用户访问令牌体系结构的关系图。":::

有关其他信息，请查看[标识管理最佳做法](../../security/fundamentals/identity-management-best-practices.md)

## <a name="browser-communication"></a>浏览器通信

Azure 通信 JavaScript 客户端库可启用具有丰富文本、语音和视频交互的 Web 应用程序。 该应用程序通过客户端库直接与 Azure 通信服务交互，从而访问数据平面，并传递实时文本、语音和视频通信。 下面是一个示例体系结构流：

:::image type="content" source="../media/scenarios/archdiagram-browser.png" alt-text="显示用于通信服务的浏览器到浏览器体系结构的关系图。":::

## <a name="native-app-communication"></a>原生应用通信

很多方案都最适用于原生应用程序。 Azure 通信服务既支持浏览器到应用通信，也支持应用到应用通信。  构建原生应用程序体验时，即使应用程序未在运行，使用推送通知也能使用户接听电话。 利用 Azure 通信服务，可通过与 Google Firebase、Apple Push Notification 服务和 Windows 推送通知集成的推送通知来简化这一操作。 下面是一个示例体系结构流：

:::image type="content" source="../media/scenarios/archdiagram-app.png" alt-text="显示原生应用通信的通信服务体系结构的关系图。":::

## <a name="voice-and-sms-over-the-public-switched-telephony-network-pstn"></a>公用电话交换网 (PSTN) 上的语音和短信

通过电话系统进行通信可显著扩大应用程序的覆盖范围。 为了支持 PSTN 语音和短信方案，Azure 通信服务帮助你直接从 Azure 门户或者使用 REST API 和客户端库来[获取电话号码](../quickstarts/telephony-sms/get-phone-number.md)。 获取电话号码后，就可在入站和出站方案中使用 PSTN 呼叫和短信来联系客户。 下面是一个示例体系结构流：

> [!Note]
> 在公共预览版期间，帐单邮寄地址位于美国和加拿大的客户可预配美国电话号码。

:::image type="content" source="../media/scenarios/archdiagram-pstn.png" alt-text="显示通信服务 PSTN 体系结构的关系图。":::

有关 PSTN 电话号码的详细信息，请查看[电话号码类型](../concepts/telephony-sms/plan-solution.md)

## <a name="humans-communicating-with-bots-and-other-services"></a>人与机器人和其他服务之间的通信

Azure 通信服务支持使用直接访问 Azure 通信服务数据平面的服务通过文本和语音通道进行人与系统的通信。 例如，你可让机器人接听来电或参与网络聊天。 Azure 通信服务提供了客户端库，它们可对通话和聊天实现这些方案。 下面是一个示例体系结构流：

:::image type="content" source="../media/scenarios/archdiagram-bot.png" alt-text="显示通信服务机器人体系结构的关系图。":::

## <a name="networking"></a>网络

你可能希望在用户之间交换任意数据，例如同步共享的混合现实或游戏体验。 可通过两种方式直接使用用于文本、语音和视频通信的实时数据平面：

- **呼叫客户端库** - 通话中的设备可访问 API 来通过通话通道发送和接收数据。 这是将数据通信添加到现有交互的最简单的方法。
- **STUN/TURN** - Azure 通信服务提供符合标准的 STUN 和 TURN 服务。 这使你能够在这些标准化基元之上构建高度自定义的传输层。 你可自行创建符合标准的客户端，也可使用开源库，例如 [WinRTC](https://github.com/microsoft/winrtc)。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [创建用户访问令牌](../quickstarts/access-tokens.md)

有关详细信息，请参阅以下文章：

- 了解[身份验证](../concepts/authentication.md)
- 了解[电话号码类型](../concepts/telephony-sms/plan-solution.md)

- [向应用中添加聊天](../quickstarts/chat/get-started.md)
- [添加对应用的音频呼叫](../quickstarts/voice-video-calling/getting-started-with-calling.md)
