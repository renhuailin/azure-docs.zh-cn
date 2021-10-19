---
title: Teams 会议互操作性
titleSuffix: An Azure Communication Services concept document
description: 加入 Teams 会议
author: tomkau
ms.author: tomkau
ms.date: 10/15/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.subservice: teams-interop
ms.openlocfilehash: 6be59e810f504e6909818a8e7ceb57b23174238b
ms.sourcegitcommit: d2875bdbcf1bbd7c06834f0e71d9b98cea7c6652
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/12/2021
ms.locfileid: "129855871"
---
# <a name="join-a-teams-meeting"></a>加入 Teams 会议

> [!IMPORTANT]
> BYOI 互操作性处于公共预览阶段，可供所有通信服务应用程序和 Teams 组织使用。
>
> 预览 API 和 SDK 在提供时没有附带服务级别协议，不建议用于生产工作负载。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

Azure 通信服务可用于构建使用户能够加入并参与 Teams 会议的应用程序。 [标准 ACS 定价](https://azure.microsoft.com/pricing/details/communication-services/)适用于这些用户，但互操作性功能本身没有额外费用。 利用“自带身份 (BYOI)”模型，你可以控制用户身份验证，并且应用程序的用户无需 Teams 许可证即可加入 Teams 会议。 这非常适用于企业对消费者解决方案，这些解决方案让获得许可的 Teams 用户（例如，医疗保健提供者或财务顾问）和外部用户（例如，患者或客户）能够使用自定义应用程序加入虚拟咨询体验。

还可以将 Microsoft 365 Teams 标识与 Azure 通信服务 SDK 结合使用。 有关详细信息，请参阅[此处](./teams-interop.md)。

目前，Teams 用户无法加入使用 Azure 通信服务呼叫 SDK 发起的呼叫。

## <a name="enabling-anonymous-meeting-join-in-your-teams-tenant"></a>在团队租户中启用匿名会议加入

当 BYOI 用户加入 Teams 会议时，这些用户被视为匿名外部用户，类似于使用 Teams Web 应用程序匿名加入 Teams 会议的用户。 BYOI 用户以匿名用户身份加入 Teams 会议的功能由现有的“允许匿名会议加入”配置控制，该配置还控制现有 Teams 匿名会议加入。 此设置可在 [Teams 管理中心](https://admin.teams.microsoft.com/meetings/settings)或通过 Teams PowerShell cmdlet [Set-CsTeamsMeetingConfiguration](/powershell/module/skype/set-csteamsmeetingconfiguration) 进行更新。 自定义应用程序应考虑使用用户身份验证和其他安全措施来保护 Teams 会议。 请注意允许匿名用户加入会议的安全影响，并使用 [Teams 安全指南](/microsoftteams/teams-security-guide#addressing-threats-to-teams-meetings)配置匿名用户可用的功能。

## <a name="meeting-experience"></a>会议体验

与 Teams 匿名会议加入一样，应用程序必须具有要加入的会议链接，该链接可通过图形 API 或 Microsoft Teams 中的日历来检索。 Teams 中显示的 BYOI 用户的名称可通过通信服务呼叫 SDK 进行配置，并将它们标记为“外部”，以让 Teams 用户知道他们尚未使用 Azure Active Directory 进行身份验证。

在会议期间，通信服务用户将能够通过 Azure 通信服务 SDK 使用核心音频、视频、屏幕共享和聊天功能。 一旦通信服务用户离开会议或会议结束，用户便无法再发送或接收新的聊天消息，但他们将有权访问在会议期间发送和接收的消息。 匿名通信服务用户无法向会议添加其他参与者，也无法开始录制或停录会议。

有关加入 Teams 会议所需数据流的更多信息，请访问[客户端和服务器体系结构页面](client-and-server-architecture.md)。 [群组通话特大示例](../samples/calling-hero-sample.md)提供了用于从 Web 应用程序加入 Teams 会议的示例代码。

## <a name="privacy"></a>隐私
通过 Azure 通信服务与 Microsoft Teams 之间的互操作性，应用程序和用户可以参与 Teams 调用、会议和聊天。 你需要负责确保，在 Teams 调用或会议中启用录制或听录时，要通知应用程序的用户。

Microsoft 会通过 Azure 通信服务 API 向你指示录制或听录已开始，你需要在应用程序的用户界面中向用户实时传达这一信息。 你同意赔偿 Microsoft 因自己未能遵守此义务而产生的所有费用和损失。

## <a name="limitations-and-known-issues"></a>限制和已知问题

- BYOI 用户可以加入为 Teams 频道安排的 Teams 会议并可使用音频和视频，但他们将无法发送或接收任何聊天消息，因为他们不是该频道的成员。
- 使用 Microsoft Graph [列出 Teams 会议的参与者](https://docs.microsoft.com/graph/api/call-list-participants)时，当前不包括通信服务用户的详细信息。
- Teams 会议最多支持 1000 名参与者，但 Azure 通信服务呼叫 SDK 目前仅支持 350 名参与者。
- 对于 [Microsoft Teams 的云视频互操作性](https://docs.microsoft.com/microsoftteams/cloud-video-interop)，某些设备在通信服务用户共享其屏幕时会出现问题。
- 举手、一起模式和分组讨论室等功能仅适用于 Teams 用户。
- 呼叫 SDK 当前不支持 Teams 会议的隐藏字幕。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [将 BYOI 通话应用加入 Teams 会议中](../quickstarts/voice-video-calling/get-started-teams-interop.md)
> [将 BYOI 聊天应用加入 Teams 会议](../quickstarts/chat/meeting-interop.md)
