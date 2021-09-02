---
title: Azure 通信服务中的通知
titleSuffix: An Azure Communication Services concept document
description: 向在 Azure 通信服务上构建的应用的用户发送通知。
author: probableprime
manager: chpalm
services: azure-communication-services
ms.author: rifox
ms.date: 06/30/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 831aa049d6426eeb23e4f227321a79359ed852af
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2021
ms.locfileid: "123255363"
---
# <a name="communication-services-notifications"></a>通信服务通知

Azure 通信服务聊天和通话 SDK 会创建一个实时消息通道，允许以高效可靠的方式将信号消息推送到连接的客户端。 这使你可以在应用程序中构建丰富的实时通信功能，而无需实现复杂的 HTTP 轮询逻辑。 但是，在移动应用程序中，此信号通道仅当应用程序在前台处于活动状态时才保持连接状态。 如果希望用户在应用程序处于后台时接收传入呼叫或聊天消息，则应使用推送通知。

推送通知使你可以将信息从你的应用程序发送到用户的移动设备。 可以使用推送通知显示对话、播放声音或显示传入呼叫 UI。 Azure 通信服务提供与 [Azure 事件网格](../../event-grid/overview.md)和 [Azure 通知中心](../../notification-hubs/notification-hubs-push-notification-overview.md)的集成，使你可以向应用添加推送通知。

## <a name="trigger-push-notifications-via-azure-event-grid"></a>通过 Azure 事件网格触发推送通知

Azure 通信服务与 [Azure 事件网格](https://azure.microsoft.com/services/event-grid/)集成，从而以可靠、可缩放且安全的方式提供实时事件通知。 你可以利用此集成来创建将移动推送通知传递给用户的通知服务，具体方法是创建触发 [Azure 函数](../../azure-functions/functions-overview.md)或 Webhook 的事件网格订阅。

:::image type="content" source="./media/notifications/acs-events-int.png" alt-text="显示通信服务如何与事件网格集成的关系图。":::

详细了解 [Azure 通信服务中的事件处理](../../event-grid/event-schema-communication-services.md)。

## <a name="deliver-push-notifications-via-azure-notification-hubs"></a>通过 Azure 通知中心传递推送通知

可以将 Azure 通知中心连接到通信服务资源，以便在收到传入呼叫时自动向用户的移动设备发送推送通知。 你应使用这些推送通知从后台唤醒你的应用程序，并显示让用户接受或拒绝呼叫的 UI。

:::image type="content" source="./media/notifications/acs-anh-int.png" alt-text="显示通信服务如何与 Azure 通知中心集成的关系图。":::

通信服务使用 Azure 通知中心作为直通服务，以便使用[直接发送](/rest/api/notificationhubs/direct-send) API 与各种特定于平台的推送通知服务进行通信。 这使你可以重复使用现有 Azure 通知中心资源和配置，向你的应用程序传递低延迟、可靠的呼叫通知。

> [!NOTE]
> 目前仅支持调用推送通知。

### <a name="notification-hub-provisioning"></a>通知中心预配

若要使用通知中心将推送通知传递到客户端设备，请在与通信服务资源相同的订阅中[创建通知中心](../../notification-hubs/create-notification-hub-portal.md)。 必须为想要使用的平台通知系统配置 Azure 通知中心。 若要了解如何从通知中心获取客户端应用程序中的推送通知，请参阅[通知中心入门](../../notification-hubs/notification-hubs-android-push-notification-google-fcm-get-started.md)，并从页面顶部附近的下拉列表中选择目标客户端平台。

> [!NOTE]
> 目前支持 APN 和 FCM 平台。
APN 平台需要使用令牌身份验证模式进行配置。 目前不支持证书身份验证模式。

通知中心配置完成后，便可以使用 Azure 资源管理器客户端或通过 Azure 门户为中心提供连接字符串，从而将它关联到通信服务资源。 连接字符串应包含 `Send` 权限。 建议专门为中心另外创建一个仅具有 `Send` 权限的访问策略。 详细了解[通知中心安全和访问策略](../../notification-hubs/notification-hubs-push-notification-security.md)

#### <a name="using-the-azure-resource-manager-client-to-link-your-notification-hub"></a>使用 Azure 资源管理器客户端链接通知中心

若要登录 Azure 资源管理器，请执行以下操作并使用你的凭据登录。

```console
armclient login
```

 成功登录后，请执行以下操作来预配通知中心：

```console
armclient POST /subscriptions/<sub_id>/resourceGroups/<resource_group>/providers/Microsoft.Communication/CommunicationServices/<resource_id>/linkNotificationHub?api-version=2020-08-20-preview "{'connectionString': '<connection_string>','resourceId': '<resource_id>'}"
```

#### <a name="using-the-azure-portal-to-link-your-notification-hub"></a>使用 Azure 门户链接通知中心

1. 在门户中，转到 Azure 通信服务资源。

1. 在通信服务资源中，从“通信服务”页左侧菜单中选择“推送通知”，并连接之前预配的通知中心。

1. 选择“连接通知中心”。 你将看到可供连接的通知中心列表。
 
1. 选择要用于此资源的通知中心。
 
   - 如需创建新的中心，请选择“新建通知中心”以获取为此资源预配的新中心。

   :::image type="content" source="./media/notifications/acs-anh-portal-int.png" alt-text="显示 Azure 门户中的推送通知设置的屏幕截图。":::

现在，你将看到已与连接状态关联的通知中心。

如要为资源使用不同的中心，请选择“断开连接”，然后重复上述步骤以链接不同的通知中心。

> [!NOTE]
> 有关中心链接方式的任何更改最多只需 10 分钟即会反映在数据平面（即发送通知时）中。 如果在更改之前发送了通知，则在第一次连接中心时，系统会应用此相同行为。

### <a name="device-registration"></a>设备注册

请参阅[语音呼叫快速入门](../quickstarts/voice-video-calling/getting-started-with-calling.md)，以了解如何向通信服务注册设备句柄。

### <a name="troubleshooting-guide-for-push-notifications"></a>推送通知疑难解答指南

如果在设备上看不到推送通知，则可能是已在下面三个位置删除了通知：

- Azure 通知中心未接受来自 Azure 通信服务的通知
- 平台通知系统（例如 APNs 和 FCM）未接受来自 Azure 通知中心的通知
- 平台通知系统未将通知传递到设备。

下面介绍了可能删除通知的第一个位置（Azure 通知中心未接受来自 Azure 通信服务的通知）。 对于其他两个位置，请参阅[在 Azure 通知中心诊断已删除的通知](../../notification-hubs/notification-hubs-push-notification-fixer.md)。

若要查看通信服务资源是否将通知发送到 Azure 通知中心，一种方法是查看链接的 [Azure 通知中心指标](../../azure-monitor/essentials/metrics-supported.md#microsoftnotificationhubsnamespacesnotificationhubs)中的 `incoming messages` 指标。

下面是一些常见的错误配置，它们可能是 Azure 通知中心不接受来自通信服务资源的通知的原因。

#### <a name="azure-notification-hub-not-linked-to-the-communication-services-resource"></a>Azure 通知中心未链接到通信服务资源

可能存在你没有将 Azure 通知中心链接到通信服务资源的情况。 可查看[通知中心预配部分](#notification-hub-provisioning)，了解如何链接它们。

#### <a name="the-linked-azure-notification-hub-isnt-configured"></a>已链接的 Azure 通知中心未配置

必须通过要使用的平台（例如 iOS 或 Android）的平台通知系统凭据来配置已链接的通知中心。 若要更详细地了解如何执行此操作，请参阅[在通知中心设置推送通知](../../notification-hubs/configure-notification-hub-portal-pns-settings.md)。

#### <a name="the-linked-azure-notification-hub-doesnt-exist"></a>已链接的 Azure 通知中心不存在

链接到通信服务资源的 Azure 通知中心不复存在。 请检查已链接的通知中心是否仍然存在。

#### <a name="the-azure-notification-hub-apns-platform-is-configured-with-certificate-authentication-mode"></a>Azure 通知中心 APNs 平台已配置有证书身份验证模式

如果要使用带有证书身份验证模式的 APNs 平台，请注意当前不支持此操作。 应为 APNs 平台配置[在通知中心设置推送通知](../../notification-hubs/configure-notification-hub-portal-pns-settings.md)中指定的令牌身份验证模式。

#### <a name="the-linked-connection-string-doesnt-have-send-permission"></a>已链接的连接字符串没有 `Send` 权限

用于将通知中心链接到通信服务资源的连接字符串需要具有 `Send` 权限。 若要更详细地了解如何创建新的连接字符串，或者查看 Azure 通知中心当前提供的连接字符串，可查看[通知中心安全性和访问策略](../../notification-hubs/notification-hubs-push-notification-security.md)

#### <a name="the-linked-connection-string-or-azure-notification-hub-resourceid-arent-valid"></a>已链接的连接字符串或 Azure 通知中心 resourceId 无效

请确保为通信服务资源配置了正确的连接字符串和 Azure 通知中心 resourceId

#### <a name="the-linked-connection-string-is-regenerated"></a>已重新生成链接的连接字符串

如果重新生成了链接的 Azure 通知中心的连接字符串，则必须通过[重新链接通知中心](#notification-hub-provisioning)，在通信服务资源中更新连接字符串。

## <a name="next-steps"></a>后续步骤

* 有关 Azure 事件网格的简介，请参阅[什么是事件网格？](../../event-grid/overview.md)
* 若要详细了解 Azure 通知中心概念，请参阅 [Azure 通知中心文档](../../notification-hubs/index.yml)