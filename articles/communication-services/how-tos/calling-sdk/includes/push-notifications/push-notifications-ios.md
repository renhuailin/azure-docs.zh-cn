---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 09/08/2021
ms.author: rifox
ms.openlocfilehash: c1d03c13ac4d2f4911baf0315358003dd43e0b3b
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124837690"
---
[!INCLUDE [Install SDK](../install-sdk/install-sdk-ios.md)]

## <a name="set-up-push-notifications"></a>设置推送通知

移动推送通知是在移动设备中收到的弹出通知。 对于呼叫，我们将重点介绍 VoIP（Internet 语音协议）推送通知。 

以下部分介绍如何注册、处理和取消注册推送通知。 在开始这些任务之前，请先满足以下先决条件：

1. 在 Xcode 中，转到“签名和功能”。 选择“+功能”来添加一项功能，然后选择“推送通知” 。
2. 选择“+功能”来再添加一项功能，然后选择“背景模式” 。
3. 在“背景模式”下，选中“IP 语音”和“远程通知”复选框  。

:::image type="content" source="../../../../quickstarts/voice-video-calling/media/ios/xcode-push-notification.png" alt-text="显示如何在 Xcode 中添加功能的屏幕截图。" lightbox="../../../../quickstarts/voice-video-calling/media/ios/xcode-push-notification.png":::

### <a name="register-for-push-notifications"></a>注册推送通知

若要注册推送通知，请使用设备注册令牌在 `CallAgent` 实例上调用 `registerPushNotification()`。

成功初始化后需要注册推送通知。 销毁 `callAgent` 对象时，将调用 `logout`，这会自动取消注册推送通知。

```swift
let deviceToken: Data = pushRegistry?.pushToken(for: PKPushType.voIP)
callAgent.registerPushNotifications(deviceToken: deviceToken!) { (error) in
    if(error == nil) {
        print("Successfully registered to push notification.")
    } else {
        print("Failed to register push notification.")
    }
}
```

## <a name="handle-push-notifications"></a>处理推送通知
若要接收来电推送通知，请使用字典有效负载在 `CallAgent` 实例上调用 `handlePushNotification()`。

```swift
let callNotification = PushNotificationInfo.fromDictionary(pushPayload.dictionaryPayload)

callAgent.handlePush(notification: callNotification) { (error) in
    if (error == nil) {
        print("Handling of push notification was successful")
    } else {
        print("Handling of push notification failed")
    }
}
```
## <a name="unregister-push-notifications"></a>取消注册推送通知

应用程序可以随时取消注册推送通知。 在 CallAgent 上调用  方法即可。

> [!NOTE]
> 注销时，应用程序不会自动取消注册推送通知。

```swift
callAgent.unregisterPushNotification { (error) in
    if (error == nil) {
        print("Unregister of push notification was successful")
    } else {
       print("Unregister of push notification failed, please try again")
    }
}
```