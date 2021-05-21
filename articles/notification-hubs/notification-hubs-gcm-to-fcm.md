---
title: Azure 通知中心和 Google Firebase Cloud Messaging (FCM) 迁移
description: 介绍 Azure 通知中心如何解决从 Google GCM 到 FCM 的迁移。
services: notification-hubs
author: sethmanheim
manager: femila
editor: jwargo
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 04/10/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 04/10/2019
ms.openlocfilehash: 2f2ca4b56445b3f399477e396de579d8a8c539e3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "80127025"
---
# <a name="azure-notification-hubs-and-google-firebase-cloud-messaging-migration"></a>Azure 通知中心和 Google Firebase Cloud Messaging 迁移

## <a name="current-state"></a>当前状态

Google 宣布从 Google Cloud Messaging (GCM) 迁移到 Firebase Cloud Messaging (FCM) 时，像我们这样的推送服务必须调整向 Android 设备发送通知的方式，以适应这种变化。

我们更新了服务后端，然后根据需要发布了对 API 和 SDK 的更新。 在我们的实现中，我们决定保持与现有 GCM 通知架构的兼容性，以最大程度地减少对客户的影响。 这意味着我们当前在 FCM 旧模式下使用 FCM 向 Android 设备发送通知。 最终，我们想要添加对 FCM 的真正支持，包括新功能和有效负载格式。 这是一项长期的更改，当前的迁移重点是保持与现有应用程序和 SDK 的兼容性。 你可以在你的应用中使用 GCM 或 FCM SDK（连同我们的 SDK），我们会确保正确发送通知。

一些客户最近收到了来自 Google 的电子邮件，包含对使用 GCM 终结点进行通知的应用的警告。 这只是一条警告，没有实际问题；应用的 Android 通知仍会发送给 Google 进行处理，而 Google 也依旧会处理。 一些在其服务配置中显式指定 GCM 终结点的客户仍在使用已弃用的终结点。 当 Google 发送电子邮件时，我们已经发现了这种缺口，并正在努力解决此问题。

我们替换了弃用的终结点，并部署了修补程序。

## <a name="going-forward"></a>今后

Google 的 FCM FAQ 表示你无需执行任何操作。 Google 在 [FCM FAQ](https://developers.google.com/cloud-messaging/faq) 中表示：“客户端 SDK 和 GCM 令牌将继续无限期地工作。 但是，除非迁移到 FCM，否则无法在 Android 应用中定位最新版本的 Google Play Services。”

如果应用使用 GCM 库，请继续按照 Google 的说明升级到应用中的 FCM 库。 我们的 SDK 与其中任何一个都兼容，因此你不需要在我们这边更新应用中的任何内容（只要你是最新的 SDK 版本）。

## <a name="questions-and-answers"></a>问题与解答

下面是对客户反馈的一些常见问题的解答：

**问：** 在截止日期（Google 当前的截止日期是 5 月 29 日，可能会更改）前，需执行什么操作才能具备兼容性？

**答：** 无需执行任何操作。 我们将保持与现有 GCM 通知架构的兼容性。 GCM 密钥将继续像应用程序使用的任何 GCM SDK 和库一样正常工作。

如果/当你决定升级到 FCM SDK 和库以利用新功能时，GCM 密钥仍然有效。 如果需要，可以切换为使用 FCM 密钥，但是请确保在创建新的 Firebase 项目时将 Firebase 添加到现有的 GCM 项目中。 这将保证与运行仍使用 GCM SDK 和库的旧版应用的客户向后兼容。

如果你正在创建新的 FCM 项目并且未附加到现有 GCM 项目，则一旦使用新 FCM 机密更新了通知中心，便无法再将通知推送到当前应用安装，因为新的 FCM 密钥没有执行旧 GCM 项目的链接。

**问：** 为什么我收到关于旧 GCM 终结点被使用的电子邮件？ 我该怎么做？

**答：** 无需执行任何操作。 我们已经迁移到新的终结点，很快就会完成，所以不需要进行任何更改。 没有任何问题，只是一个遗漏的终结点导致 Google 发送警告消息。

**问：** 如何在不中断现有用户的情况下转换到新的 FCM SDK 和库？

答：随时升级。 Google 尚未宣布弃用现有的 GCM SDK 和库。 为确保不会中断向现有用户推送通知，请确保在创建新的 Firebase 项目时将其与现有 GCM 项目相关联。 这将确保新的 Firebase 机密适用于使用 GCM SDK 和库运行旧版应用的用户，以及使用 FCM SDK 和库运行应用的新用户。

**问：** 何时可以使用新的 FCM 功能和架构进行通知？

**答：** 敬请关注有关 API 和 SDK 的更新，我们预计会在未来的几个月内提供一些内容。
