---
title: 什么是 Azure 通信服务？
description: 了解 Azure 通信服务如何通过实时通信帮助你开发丰富的用户体验。
author: probableprime
manager: chpalm
services: azure-communication-services
ms.author: rifox
ms.date: 06/30/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 5e6716980ef8f813be49a7cd6d269d432114aeae
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128671937"
---
# <a name="what-is-azure-communication-services"></a>什么是 Azure 通信服务？

Azure 通信服务是具有 REST API 和客户端库 SDK 的基于云的服务，可用于帮助你将通信集成到应用程序中。 你可以在自己的应用程序中添加通信功能（如媒体编码和实时网络），而不需要是通信技术的专家。 Azure 政府版也支持此功能。

Azure 通信服务支持各种通信格式：

1. 语音和视频通话
1. 格式文本聊天
1. SMS

你可以将自定义客户端终结点、自定义服务和公共交换电话网络 (PSTN) 连接到通信应用程序。 可以直接通过 Azure 通信服务 REST API、SDK 或 Azure 门户获取电话号码；并将这些号码用于短信或通话应用程序。 借助 Azure 通信服务直接路由，可以使用 SIP 和会话边界控制器连接你自己的 PSTN 运营商并自带电话号码。

除了 REST API，[Azure 通信服务客户端库](./concepts/sdk-options.md)还可用于各种平台和语言，包括 Web 浏览器 (JavaScript)、iOS (Swift)、Java (Android)、Windows (.NET)。 [适用于 Web 浏览器的 UI 库](https://aka.ms/acsstorybook)可以加速移动设备和桌面浏览器的开发。 Azure 通信服务与标识无关，你可以控制最终用户的标识和身份验证方式。

Azure 通信服务的场景包括：

- 企业对消费者 (B2C)。 企业的员工和服务可以在自定义浏览器或移动应用程序中使用语音、视频和格式文本聊天方式与使用者交互。 组织可以发送和接收短信，也可以使用通过 Azure 获取的电话号码[操作交互式语音响应系统 (IVR)](https://github.com/microsoft/botframework-telephony/blob/main/EnableTelephony.md)。 通过[与 Microsoft Teams 集成](./quickstarts/voice-video-calling/get-started-teams-interop.md)，可以将使用者连接到员工召开的 Teams 会议；在员工已熟悉 Teams 的情况下，这是远程医疗保健、银行业和产品支持应用场景的理想选择。
- 使用者对使用者 (C2C)。 为使用语音、视频和富文本聊天进行的消费者对消费者交互构建具有吸引力的社交空间。 可以在 Azure 通信服务 SDK 上构建任意类型的用户界面，或使用完整的应用程序示例和开放源代码 UI 工具包来帮助快速开始使用。

若要了解详细信息，请查看我们的 [Microsoft Mechanics 视频](https://www.youtube.com/watch?v=apBX7ASurgM)或下面链接的资源。

## <a name="common-scenarios"></a>常见方案

<br>

| 资源                               |说明                           |
|---                                    |---                                   |
|[创建通信服务资源](./quickstarts/create-communication-resource.md)|使用 Azure 门户或通信服务 SDK 预配第一个通信服务资源，从而开始使用 Azure 通信服务。 具有通信服务资源连接字符串后，便可以预配第一个用户访问令牌。|
|[获取电话号码](./quickstarts/telephony-sms/get-phone-number.md)|使用 Azure 通信服务预配和发布电话号码。 这些电话号码可以用于启动或接收电话呼叫和构建短信解决方案。|
|[从应用发送短信](./quickstarts/telephony-sms/send.md)| Azure 通信服务短信 REST API 和 SDK 可用于从服务应用程序发送和接收短信。|

创建通信服务资源后，可以开始构建客户端方案，例如语音和视频呼叫或文本聊天：

| 资源                               |说明                           |
|---                                    |---                                   |
|[创建第一个用户访问令牌](./quickstarts/access-tokens.md)|用户访问令牌用于对 Azure 通信服务资源进行客户端身份验证。 这些令牌是使用通信服务标识 API 和 SDK 预配和重新颁发的。|
|[语音和视频呼叫入门](./quickstarts/voice-video-calling/getting-started-with-calling.md)| 借助 Azure 通信服务，可以使用呼叫 SDK 将语音和视频呼叫添加到浏览器或本机应用。 |
|**[向应用添加通话功能](./quickstarts/voice-video-calling/pstn-call.md)**|借助 Azure 通信服务，可向应用程序添加电话通话功能。|
|**[在 Teams 会议中加入通话应用](./quickstarts/voice-video-calling/get-started-teams-interop.md)**|Azure 通信服务可用于构建与 Microsoft Teams 交互的自定义会议体验。 通信服务解决方案用户可以通过语音、视频、聊天和屏幕共享与 Teams 参与者进行交互。|
|[聊天入门](./quickstarts/chat/get-started.md)|Azure 通信服务聊天 SDK 用于向应用程序添加实时富文本聊天。|
|[连接 Microsoft 机器人呼叫电话号码](https://github.com/microsoft/botframework-telephony)|电话通道是一种 Microsoft Bot Framework 通道，可让机器人通过手机与用户交互。 它结合使用了 Microsoft Bot Framework 与 Azure 通信服务以及 Azure 语音服务的功能。  |


## <a name="samples"></a>示例

以下示例演示 Azure 通信服务的端到端使用。 使用这些示例来启动你自己的通信服务解决方案。
<br>

| 示例名称                               | 描述                           |
|---                                    |---                                   |
|[群呼英雄示例](./samples/calling-hero-sample.md)| 下载为浏览器、iOS 和 Android 设备进行群组通话设计的应用程序示例。 |
|[群聊英雄示例](./samples/chat-hero-sample.md)| 下载为浏览器的组文本聊天设计的应用程序示例。 |
|**[Web 呼叫示例](./samples/web-calling-sample.md)**| 下载专为音频、视频和 PSTN 呼叫设计的 Web 应用程序示例。 |


## <a name="platforms-and-sdk-libraries"></a>平台和 SDK 库

通过以下资源详细了解 Azure 通信服务 SDK。 如果要构建自己的客户端或通过 Internet 访问该服务，可在大多数功能中使用 REST API。

| 资源                               | 说明                           |
|---                                    |---                                   |
|**[SDK 库和 REST API](./concepts/sdk-options.md)**|Azure 通信服务功能按概念组织成六个领域，每个领域由一个 SDK 表示。 可以基于实时通信需求决定要使用的 SDK 库。|
|**[呼叫 SDK 概述](./concepts/voice-video-calling/calling-sdk-features.md)**|查看通信服务呼叫 SDK 概述。|
|**[聊天 SDK 概述](./concepts/chat/sdk-features.md)**|查看通信服务聊天 SDK 概述。|
|**[短信 SDK 概述](./concepts/telephony-sms/sdk-features.md)**|查看通信服务短信 SDK 概述。|

## <a name="other-microsoft-communication-services"></a>其他 Microsoft 通信服务

可以考虑使用两个其他 Microsoft 通信产品，它们目前不能直接与通信服务交互：

 - [Microsoft Graph 云通信 API](/graph/cloud-communications-concept-overview) 使组织可以构建与具有 Microsoft 365 许可证的 Azure Active Directory 用户关联的通信体验。 这非常适合与 Azure Active Directory 关联的应用程序，或是要在 Microsoft 团队中扩展工作效率体验的应用程序。 还有一些用于在[团队体验](/microsoftteams/platform/?preserve-view=true&view=msteams-client-js-latest)中构建应用程序和自定义的 API。

 - [Azure PlayFab Party](/gaming/playfab/features/multiplayer/networking/) 简化了将低延迟聊天和数据通信添加到游戏的过程。 虽然可以使用通信服务来支持游戏聊天和网络系统，不过 PlayFab 是一种定制选项，可在 Xbox 上免费使用。


## <a name="next-steps"></a>后续步骤

 - [创建通信服务资源](./quickstarts/create-communication-resource.md)
