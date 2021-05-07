---
title: 在 Azure 通知中心中配置 Google Firebase Cloud Messaging |Microsoft Docs
description: 了解如何使用 Google Firebase Cloud Messaging 设置配置 Azure 通知中心。
services: notification-hubs
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.topic: article
ms.date: 08/04/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 03/25/2019
ms.openlocfilehash: 0da103c11e2412108535ca322917632f5d95559d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "96003559"
---
# <a name="configure-google-firebase-settings-for-a-notification-hub-in-the-azure-portal"></a>在 Azure 门户中为通知中心配置 Google Firebase 设置

本文介绍如何使用 Azure 门户为 Azure 通知中心配置 Google Firebase Cloud Messaging (FCM) 设置。  

## <a name="prerequisites"></a>先决条件

如果你尚未创建通知中心，现在请创建一个。 有关详细信息，请参阅[在 Azure 门户中创建 Azure 通知中心](create-notification-hub-portal.md)。

## <a name="configure-google-firebase-cloud-messaging-fcm"></a>配置 Google Firebase Cloud Messaging (FCM)

以下过程介绍了为通知中心配置 Google Firebase Cloud Messaging (FCM) 设置的步骤：

1. 在 Azure 门户的“通知中心”页上，从左侧菜单中选择“Google (GCM/FCM)”。
2. 为之前保存的 FCM 项目粘贴“API 密钥”。
3. 选择“保存”。

   ![显示如何为 Google FCM 配置通知中心的屏幕截图](./media/notification-hubs-android-push-notification-google-fcm-get-started/fcm-server-key.png)

## <a name="next-steps"></a>后续步骤

有关使用 Azure 通知中心和 Google Firebase Cloud Messaging 将通知发送到 Android 设备的分步说明教程，请参阅[使用通知中心和 Google FCM 将推送通知发送到 Android 设备](notification-hubs-android-push-notification-google-fcm-get-started.md)。
