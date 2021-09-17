---
title: 使用 Azure 通知中心向用户发送跨平台通知 (ASP.NET)
description: 了解如何使用通知中心模板在单个请求中发送针对所有平台的平台未知通知。
services: notification-hubs
documentationcenter: ''
author: sethmanheim
manager: femila
editor: thsomasu
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: multiple
ms.topic: article
ms.date: 08/23/2021
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 10/02/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 01d6d1cffaa0ae9928c24015570f882036b1e783
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/24/2021
ms.locfileid: "122771434"
---
# <a name="send-cross-platform-notifications-with-azure-notification-hubs"></a>使用 Azure 通知中心发送跨平台通知

本教程基于前一教程：[使用 Azure 通知中心向特定用户发送通知]。 该教程介绍了如何向注册给经过身份验证的特定用户的所有设备推送通知。 该方法需要使用多个请求将通知发送到每个受支持的客户端平台。 Azure 通知中心支持模板，可以通过模板指定特定设备要如何接收通知。 此方法简化了发送跨平台通知的方式。

本文演示了如何利用模板发送针对所有平台的通知。 本文使用单个请求发送不区分平台的通知。 有关模板的更多详细信息，请参阅[通知中心概述][Templates]。

> [!IMPORTANT]
> Visual Studio 2019 不支持使用 Windows Phone 项目 8.1 及更早的版本。 有关详细信息，请参阅 [Visual Studio 2019 平台目标以及兼容性](/visualstudio/releases/2019/compatibility)。

> [!NOTE]
> 通过通知中心，设备可使用同一标记注册多个模板。 在这种情况下，针对该标签的传入的邮件将导致系统向设备发送多个通知（每个通知对应一个模板）。 此过程可以在多个可视通知中显示同一消息，如显示为 Windows 应用商店应用中的徽章和 toast 通知。

## <a name="send-cross-platform-notifications-using-templates"></a>使用模板发送跨平台通知

> [!NOTE]
> Microsoft 推送通知服务 (MPNS) 已弃用，不再受支持。

本部分使用了你在[使用 Azure 通知中心向特定用户发送通知]教程中构建的示例代码。 可以[从 GitHub 下载完整示例](https://github.com/Azure/azure-notificationhubs-dotnet/tree/master/Samples/NotifyUsers)。

若要使用模板发送跨平台通知，请执行以下步骤：

1. 在 Visual Studio 的“解决方案资源管理器”中，展开“Controllers”文件夹，然后打开 RegisterController.cs 文件。   

1. 在 `Put` 方法中找到用于创建新注册的代码块，然后将 `switch` 内容替换为以下代码：

    ```csharp
    switch (deviceUpdate.Platform)
    {
        case "mpns":
            var toastTemplate = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                "<wp:Notification xmlns:wp=\"WPNotification\">" +
                    "<wp:Toast>" +
                        "<wp:Text1>$(message)</wp:Text1>" +
                    "</wp:Toast> " +
                "</wp:Notification>";
            registration = new MpnsTemplateRegistrationDescription(deviceUpdate.Handle, toastTemplate);
            break;
        case "wns":
            toastTemplate = @"<toast><visual><binding template=""ToastText01""><text id=""1"">$(message)</text></binding></visual></toast>";
            registration = new WindowsTemplateRegistrationDescription(deviceUpdate.Handle, toastTemplate);
            break;
        case "apns":
            var alertTemplate = "{\"aps\":{\"alert\":\"$(message)\"}}";
            registration = new AppleTemplateRegistrationDescription(deviceUpdate.Handle, alertTemplate);
            break;
        case "fcm":
            var messageTemplate = "{\"data\":{\"message\":\"$(message)\"}}";
            registration = new FcmTemplateRegistrationDescription(deviceUpdate.Handle, messageTemplate);
            break;
        default:
            throw new HttpResponseException(HttpStatusCode.BadRequest);
    }
    ```

    此代码调用平台特定的方法来创建模板注册而非本机注册。 由于模板注册派生自本机注册，因此无需修改现有注册。

1. 在“解决方案资源管理器”的“Controllers”文件夹中，打开“NotificationsController.cs”文件。    将 `Post` 方法替换为以下代码：

    ```csharp
    public async Task<HttpResponseMessage> Post()
    {
        var user = HttpContext.Current.User.Identity.Name;
        var userTag = "username:" + user;

        var notification = new Dictionary<string, string> { { "message", "Hello, " + user } };
        await Notifications.Instance.Hub.SendTemplateNotificationAsync(notification, userTag);

        return Request.CreateResponse(HttpStatusCode.OK);
    }
    ```

    此代码将通知同时发送到所有平台。 不指定本机有效负载。 通知中心会使用所提供的标记值（在注册的模板中指定）生成正确的负载并将它传递到每个设备。

1. 重新发布 Web API 项目。

1. 再次运行客户端应用来验证注册是否已成功。

1. 可以选择将客户端应用部署到另一个设备，然后运行该应用。 通知会显示在每个设备上。

## <a name="next-steps"></a>后续步骤

现在，你已完成本教程，可以查看以下文章来详细了解通知中心和模板：

* 有关如何使用模板的其他方案，请参阅[向运行通用 Windows 平台应用程序的特定 Windows 设备推送通知][Use Notification Hubs to send breaking news]教程。
* 有关模板的更多详细信息，请参阅[通知中心概述][Templates]。

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[Push to users ASP.NET]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Push to users Mobile Services]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Visual Studio 2012 Express for Windows 8]: https://visualstudio.microsoft.com/downloads/

[Use Notification Hubs to send breaking news]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[Azure Notification Hubs]: https://go.microsoft.com/fwlink/p/?LinkId=314257
[使用 Azure 通知中心向特定用户发送通知]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Templates]: /previous-versions/azure/azure-services/jj927170(v=azure.100)
[Notification Hub How to for Windows Store]: /previous-versions/azure/azure-services/jj927170(v=azure.100)