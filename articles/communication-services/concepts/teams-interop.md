---
title: Teams 互操作性
titleSuffix: An Azure Communication Services concept document
description: Teams 互操作性
author: chpalm
ms.author: chpalm
ms.date: 06/30/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.subservice: teams-interop
ms.openlocfilehash: 32067409bb6289b283d8dd3b4de18e1a83f8e374
ms.sourcegitcommit: d2875bdbcf1bbd7c06834f0e71d9b98cea7c6652
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/12/2021
ms.locfileid: "129856042"
---
# <a name="teams-interoperability"></a>Teams 互操作性

> [!IMPORTANT]
> BYOI 互操作性处于公共预览阶段，可供所有通信服务应用程序和 Teams 组织使用。
>
> Microsoft 365 已验证的互操作性提供个人预览版，并且仅限 Azure 通信服务早期采用者使用服务控制。 若要加入早期访问计划，请完成[此表单](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR8MfnD7fOYZEompFbYDoD4JUMkdYT0xKUUJLR001ODdQRk1ITTdOMlRZNSQlQCN0PWcu)。
>
> 预览 API 和 SDK 在提供时没有附带服务级别协议，不建议用于生产工作负载。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

Azure 通信服务可用于构建与 Microsoft Teams 交互的自定义应用程序。 通信服务应用程序的最终用户可以通过语音、视频、聊天和屏幕共享与 Teams 参与者进行交互。 以下视频演示了此功能：


<br>
<br>


> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RWGTqQ]


Azure 通信服务支持两种类型的 Teams 互操作性，具体取决于最终用户的标识：

- **自带标识。** 你可以控制用户身份验证，并且自定义应用程序的用户无需拥有 Azure Active Directory 标识或 Teams 许可证即可加入 Teams 会议。 Teams 将你的应用程序视为匿名外部用户。
- **Microsoft 365 Teams 标识。** 你的应用程序代表最终用户的 Microsoft 365 标识及其 Teams 配置的资源行动。 这些经过身份验证的应用程序可以代表 Microsoft 365 用户无缝地拨打电话和加入会议。

应用程序可以同时实现这两种身份验证方案，并让最终用户选择身份验证。

## <a name="overview"></a>概述

用户可以通过两种方式访问 Teams 通话体验：

- 作为 Teams 用户通过 Teams 客户端访问。 这包括桌面、移动设备和 Web Teams 客户端。 
- 作为 Teams 匿名用户通过应用程序的 Web 体验访问。 

Teams 匿名用户无需是 Teams 用户。 使用 Azure 通信服务，可以为 Teams 用户和 Teams 匿名用户生成和自定义新的 Teams 通话终结点。 可以使用通信服务调用 SDK 和用户界面库进行自定义并集成到任何现有的应用程序或产品。 下图演示了如何通过多个终结点加入 Teams 会议：![Azure 通信服务中的多个互操作性方案概述](./media/teams-identities/teams_interop_overview.png)

当终结点通过 Azure 通信服务客户端库使用 Teams 标识连接到 Teams 会议时，终结点将被视为具有 Teams 客户端的 Teams 用户。 与 Team 匿名用户相比，Team 用户可以访问更多的功能。 Teams 用户可以加入 Teams 会议、向其他 Teams 用户发出呼叫、接收来自电话号码的呼叫，还可以将正在进行的呼叫转移到 Teams 呼叫队列。 下图显示了具有 Teams 标识的通信服务终结点的连接。

![Azure 通信服务中的互操作性方案概述](./media/teams-identities/teams_interop_m365_identity_interop_overview.png)

## <a name="bring-your-own-identity"></a>自带标识

自带标识 (BYOI) 是使用 Azure 通信服务和 Teams 互操作性的常见模型。 它支持任何标识提供者和身份验证方案。 启用的第一个方案允许你的应用程序加入 Microsoft Teams 会议，并且 Teams 会将这些用户视为匿名外部帐户，与使用 Teams 匿名 Web 应用程序加入的用户相同。 这非常适用于将员工（熟悉 Teams）和外部用户（使用自定义应用程序体验）聚集到会议体验中的企业对使用者应用程序。 今后，我们将启用其他方案，包括直接呼叫和聊天，让你的应用程序可以在 Teams 会议的上下文之外发起与 Teams 用户的呼叫和聊天。

有关详细信息，请参阅[加入 Teams 会议](join-teams-meeting.md)。

目前，Teams 用户无法加入使用 Azure 通信服务呼叫 SDK 发起的通话。

## <a name="microsoft-365-teams-identity"></a>Microsoft 365 Teams 标识
Azure 通信服务通话 SDK 可与 Microsoft 365 Teams 标识一起使用，以支持类似 Teams 的 Teams 互操作性体验。 Microsoft 365 Teams 标识由 Azure Active Directory 提供和验证。 你的应用可以使用常规 Microsoft 365 标识拨打或接听电话。 有关用户的所有属性和详细信息都绑定到 Azure Active Directory 用户。

此标识模型非常适用于需要自定义用户界面、Teams 客户端不适用于你的平台或 Teams 客户端不支持足够级别的自定义的用例。 例如，应用程序可用于代表最终用户的 Teams 预配 PSTN 号码接听电话，并具有针对接待员或呼叫中心业务流程优化的用户界面。  

通话和屏幕共享功能可通过通信服务通话 SDK 获得。 通话管理可通过图形 API、Teams 客户端或 Teams 管理门户中的配置获得。 聊天功能可通过图形 API 获得。

Teams 用户通过 MSAL 库针对客户端应用程序中的 Azure Active Directory 进行身份验证。 身份验证令牌通过通信服务标识 SDK 交换为 Microsoft 365 Teams 令牌。 建议在后端服务中实现令牌交换，因为交换请求由 Azure 通信服务的凭据签名。 在后端服务中，可以要求任何其他身份验证。

若要详细了解该功能，请通过完成[此表单](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR8MfnD7fOYZEompFbYDoD4JUMkdYT0xKUUJLR001ODdQRk1ITTdOMlRZNSQlQCN0PWcu)加入 TAP 计划以提前访问。

## <a name="comparison"></a>比较

|条件|自带标识| Microsoft 365 Teams 标识|
|---|---|---|
|适用| 在使用者应用程序的企业对使用者方案中 | 在企业应用程序上的企业对企业或企业对使用者方案中 |
|标识提供者|任意|Azure Active Directory|
|身份验证和授权|自定义*| Azure Active Directory 和自定义*|
|可通过以下方式通话 | 通信服务通话 SDK | 通信服务通话 SDK |
|可通过以下方式聊天 | 通信服务聊天 SDK | 图形 API |
|PSTN 支持| Teams 会议中不支持通信服务用户 | 分配给 Teams 标识的入站呼叫、使用呼叫计划的出站呼叫|

\* 发布访问令牌的服务器逻辑可以对请求执行任何自定义身份验证和授权。

## <a name="privacy"></a>隐私
通过 Azure 通信服务与 Microsoft Teams 之间的互操作性，应用程序和用户可以参与 Teams 调用、会议和聊天。 你需要负责确保，在 Teams 调用或会议中启用录制或听录时，要通知应用程序的用户。

Microsoft 会通过 Azure 通信服务 API 向你指示录制或听录已开始，你需要在应用程序的用户界面中向用户实时传达这一信息。 你同意赔偿 Microsoft 因自己未能遵守此义务而产生的所有费用和损失。

## <a name="pricing"></a>定价
Azure 通信服务 API 和 SDK 的所有使用情况都会递增 [Azure 通信服务计费表](https://azure.microsoft.com/pricing/details/communication-services/)。 与 Microsoft Teams 的交互（例如使用 Teams 分配的号码加入会议或发起电话呼叫）将递增这些计费表，但 Teams 互操作性功能本身没有额外费用，并且 BYOI 和 Microsoft 365 身份验证选项之间没有定价差异。

如果 Azure 应用程序有一个最终用户与 Microsoft Teams 用户在会议中用时 10 分钟，那么这两个用户总共消耗了 20 分钟的通话时间。 通过自定义应用程序以及使用 Azure API 和 SDK 进行的 10 分钟将计入你的资源。 但是，最终用户在本机 Teams 应用程序中使用的 10 分钟由适用的 Teams 许可证涵盖，并且不由 Azure 计费。

## <a name="teams-in-government-clouds-gcc"></a>政府云中的 Teams (GCC)
Azure 通信服务互操作性与目前使用 [Microsoft 365 政府云 (GCC)](/MicrosoftTeams/plan-for-government-gcc) 的 Teams 部署不兼容。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [对 Microsoft 365 用户进行身份验证](../quickstarts/manage-teams-identity.md)
