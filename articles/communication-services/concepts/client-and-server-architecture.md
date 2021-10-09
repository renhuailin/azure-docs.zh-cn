---
title: 客户端和服务器体系结构
titleSuffix: An Azure Communication Services concept document
description: 了解通信服务的体系结构。
author: probableprime
manager: mikben
ms.author: rifox
ms.date: 06/30/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 6d1228ab412ff87f3004e6e1dd5ab1486cee67a0
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129360097"
---
# <a name="client-and-server-architecture"></a>客户端和服务器体系结构

本页说明了各种 Azure 通信服务方案中的典型体系结构组件和数据流。 相关组件包括：

1. **客户端应用程序。** 最终用户使用此网站或本机应用程序进行通信。 Azure 通信服务为多个浏览器和应用程序平台提供 [SDK 客户端库](sdk-options.md)。 除了我们的核心 SDK 外，还提供了一个 [UI 库](https://aka.ms/acsstorybook)用于加速浏览器应用开发。
1. **标识管理服务。**  构建此服务功能是为了将业务逻辑中的用户和其他概念映射到 Azure 通信服务，并在需要时为这些用户创建令牌。
1. **通话管理服务。**  构建此服务功能是为了管理和监视语音和视频通话。  此服务可以通过呼叫自动化 SDK 和 REST API 创建通话、邀请用户、呼叫电话号码、播放音频、收听 DMTF 声音以及利用许多其他通话功能。


## <a name="user-access-management"></a>用户访问管理

Azure 通信服务客户端库必须提供 `user access tokens` 才能安全地访问通信服务资源。 由于令牌的敏感特性以及生成令牌所需的连接字符串或托管标识，`User access tokens` 应该由受信任的服务生成和管理。 如果无法适当管理访问令牌，则可能会因滥用资源而导致额外的费用。

:::image type="content" source="../media/scenarios/architecture_v2_identity.svg" alt-text="显示用户访问令牌体系结构的关系图。":::

### <a name="dataflows"></a>数据流
1. 用户启动客户端应用程序。 此应用程序和用户身份验证方案的设计由你控制。
2. 客户端应用程序与标识管理服务联系。 标识管理服务维护用户和其他可寻址对象（例如服务或机器人）到 Azure 通信服务标识的映射。
3. 标识管理服务为适用的标识创建用户访问令牌。 如果过去未分配任何 Azure 通信服务标识，则会创建一个新的标识。  

### <a name="resources"></a>资源
- **概念：** [用户标识](identity-model.md)
- **快速入门：** [创建和管理访问令牌](../quickstarts/access-tokens.md)
- **教程：** [使用 Azure Functions 构建标识管理服务](../tutorials/trusted-service-tutorial.md)

> [!IMPORTANT]
> 为简单起见，我们不会在后续体系结构流中显示用户访问管理和令牌分发。


## <a name="calling-a-user-without-push-notifications"></a>在没有推送通知的情况下呼叫用户
最简单的语音和视频呼叫场景是用户在没有推送通知的情况下在前台呼叫另一用户。

:::image type="content" source="../media/scenarios/architecture_v2_calling_without_notifications.svg" alt-text="显示没有推送通知的呼叫的通信服务体系结构的关系图。":::

### <a name="dataflows"></a>数据流

1. 接受用户会初始化通话客户端，使其能够接收来电。
2. 发起用户需要他们要呼叫的人员的 Azure 通信服务标识。 典型的体验可能是一个由标识管理服务维护的朋友列表，该服务整理用户的朋友和关联的 Azure 通信服务标识。
3. 发起用户初始化其通话客户端并呼叫远程用户。
4. 通过呼叫 SDK 通知接受用户有来电。
5. 用户在通话中使用语音和视频相互通信。

### <a name="resources"></a>资源
- **概念：** [呼叫概述](voice-video-calling/calling-sdk-features.md)
- **快速入门：** [在应用中添加语音呼叫](../quickstarts/voice-video-calling/getting-started-with-calling.md)
- **快速入门：** [在应用中添加视频呼叫](../quickstarts/voice-video-calling/get-started-with-video-calling.md)
- **特大示例：** [Web、iOS 和 Android 群组呼叫](../samples/calling-hero-sample.md)


## <a name="joining-a-user-created-group-call"></a>加入用户创建的群组通话
你可能希望用户无需明确的邀请即可加入通话。 例如，可能有一个带有关联通话的社交空间，用户可以在闲暇时加入该通话。 在第一个数据流中，展示了最初由客户端创建的通话。

:::image type="content" source="../media/scenarios/architecture_v2_calling_join_client_driven.svg" alt-text="显示呼叫带外信号的通信服务体系结构的关系图。":::

### <a name="dataflows"></a>数据流
1. 发起用户初始化其通话客户端并创建群组通话。
2. 发起用户与通话管理服务共享群组通话 ID。
3. 通话管理服务与其他用户共享通话 ID。 例如，如果应用程序面向计划事件，则群组通话 ID 可能是计划事件数据模型的一个属性。
4. 其他用户使用群组通话 ID 加入通话。
5. 用户在通话中使用语音和视频相互通信。


## <a name="joining-a-scheduled-teams-call"></a>加入计划的 Teams 通话
Azure 通信服务应用程序可以加入 Teams 通话。 这适用于许多企业对消费者方案，其中消费者利用自定义应用程序和自定义标识，而企业方使用 Teams。

:::image type="content" source="../media/scenarios/architecture_v2_calling_join_teams_driven.svg" alt-text="显示加入 Teams 会议的通信服务体系结构的关系图。":::


### <a name="dataflows"></a>数据流
1. 通话管理服务使用 [Graph API](/graph/api/resources/onlinemeeting?view=graph-rest-1.0&preserve-view=true) 创建群组通话。 另一种模式是最终用户使用 [Bookings](https://www.microsoft.com/microsoft-365/business/scheduling-and-booking-app)、Outlook、Teams 或 Microsoft 365 生态系统中的其他计划体验创建群组通话。
2. 通话管理服务与 Azure 通信服务客户端共享 Teams 通话的详细信息。
3. 通常情况下，Teams 用户必须加入通话，并允许外部用户通过大厅加入。 但是，这种体验对 Teams 租户配置和特定会议设置很敏感。
4. Azure 通信服务用户使用步骤 2 中收到的详细信息初始化其通话客户端并加入 Teams 会议。
5. 用户在通话中使用语音和视频相互通信。

### <a name="resources"></a>资源
- **概念：** [Teams 互操作性](teams-interop.md)
- **快速入门：** [加入 Teams 会议](../quickstarts/voice-video-calling/get-started-teams-interop.md)
