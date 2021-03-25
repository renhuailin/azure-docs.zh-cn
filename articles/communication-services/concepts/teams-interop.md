---
title: Teams 会议互操作性
titleSuffix: An Azure Communication Services concept document
description: 加入 Teams 会议
author: chpalm
manager: chpalm
services: azure-communication-services
ms.author: chpalm
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: b7d992961fc165ce643bd0d6aba4d8a9bda1d38e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "103495583"
---
# <a name="teams-interoperability"></a>Teams 互操作性

> [!IMPORTANT]
> 若要启用/禁用 [Teams 租户互操作性](../concepts/teams-interop.md)，请填写[此表单](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR21ouQM6BHtHiripswZoZsdURDQ5SUNQTElKR0VZU0VUU1hMOTBBMVhESS4u)。

Azure 通信服务可用于构建与 Microsoft Teams 交互的自定义会议体验。 通信服务解决方案用户可以通过语音、视频、聊天和屏幕共享与 Teams 参与者进行交互。

借助这种 Teams 互操作性，可创建将用户连接到 Teams 会议的自定义应用程序。 自定义应用程序的用户无需 Azure Active Directory 标识或 Teams 许可证即可体验此功能。 这非常适合用于让员工（可能熟悉 Teams）和外部用户（使用自定义应用程序体验）一起加入流畅的会议体验。 例如：

1. 员工使用 Teams 安排会议 
1. 通过自定义应用程序与外部用户共享会议详细信息。
   * **使用图形 API** 自定义通信服务应用程序使用 Microsoft Graph API 来访问要共享的会议详细信息。 
   * **使用其他选项** 例如，可以在 Microsoft Teams 中从日历复制会议链接。
1. 外部用户使用你的自定义应用程序加入 Teams 会议（通过通信服务通话和聊天客户端库）

此用例的概要结构如下所示： 

![Teams 互操作的体系结构](./media/call-flows/teams-interop.png)

尽管某些 Teams 会议功能（如举手、同聚一堂模式和分组讨论室）只可供 Teams 用户使用，但你的自定义应用程序可以访问会议的核心音频、视频、聊天和屏幕共享功能。 自定义应用程序用户可在通话期间使用会议聊天功能。 他们在加入通话前或退出通话后，将无法发送或接收消息。 

当通信服务用户加入 Teams 会议时，通过呼叫客户端库提供的显示名称将向 Teams 用户显示。 否则，通信服务用户将被视为 Teams 中的匿名用户。  自定义应用程序应考虑使用用户身份验证和其他安全措施来保护 Teams 会议。 请注意允许匿名用户加入会议的安全影响，并使用 [Teams 安全指南](/microsoftteams/teams-security-guide#addressing-threats-to-teams-meetings)配置匿名用户可用的功能。

通信服务团队互操作性当前为个人预览版。 正式发布时，通信服务用户将被视为“外部访问用户”。 要详细了解外部访问，请参阅[在 Microsoft Teams 与组织外部的人员通话、聊天和协作](/microsoftteams/communicate-with-users-from-other-organizations)。

只要在[会议设置](/microsoftteams/meeting-settings-in-teams)中启用了匿名加入，通信服务用户就可以加入计划的 Teams 会议。

## <a name="teams-in-government-clouds-gcc"></a>政府云中的 Teams (GCC)
Azure 通信服务互操作性与目前使用 [Microsoft 365 政府云 (GCC)](/MicrosoftTeams/plan-for-government-gcc) 的 Teams 部署不兼容。 

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [在 Teams 会议中加入通话应用](../quickstarts/voice-video-calling/get-started-teams-interop.md)