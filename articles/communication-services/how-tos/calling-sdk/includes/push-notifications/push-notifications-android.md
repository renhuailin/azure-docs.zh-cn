---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 09/08/2021
ms.author: rifox
ms.openlocfilehash: 9ecf53a84a804cd312f4d3a98661f4e688726577
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124837688"
---
[!INCLUDE [Install SDK](../install-sdk/install-sdk-android.md)]

### <a name="additional-prerequisites-for-push-notifications"></a>推送通知的其他先决条件

一个 Firebase 帐户，它设置为启用 Cloud Messaging (FCM) 并将 Firebase Cloud Messaging 服务连接到 Azure 通知中心实例。 有关更多详细信息，请参阅[通信服务通知](../../../../concepts/notifications.md)。
此外，本教程假定你使用 Android Studio 3.6 或更高版本来构建应用程序。

Android 应用程序需要一组权限才能接收来自 Firebase Cloud Messaging 的通知消息。 在 `AndroidManifest.xml` 文件中，在 `<manifest ...>` 后或在 `</application>` 标记下添加以下权限集。

```xml
<uses-permission android:name="android.permission.INTERNET"/>
<uses-permission android:name="android.permission.GET_ACCOUNTS"/>
<uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
```

## <a name="overview"></a>概述
移动推送通知是移动设备上显示的弹出通知。 对于呼叫，我们将重点介绍 VoIP（Internet 语音协议）推送通知。 我们将注册推送通知、处理推送通知，然后取消注册推送通知。

## <a name="register-for-push-notifications"></a>注册推送通知

若要注册推送通知，应用程序需要使用设备注册令牌在 `CallAgent` 实例上调用 `registerPushNotification()`。

若要获取设备注册令牌，请在 `dependencies` 部分添加以下行（如果尚不存在），将 Firebase SDK 添加到应用程序模块的 `build.gradle` 文件中：

```groovy
// Add the SDK for Firebase Cloud Messaging
implementation 'com.google.firebase:firebase-core:16.0.8'
implementation 'com.google.firebase:firebase-messaging:20.2.4'
```

在项目级别的 build.gradle 文件中，将以下内容添加到 `dependencies` 部分（如果尚不存在）：

```groovy
classpath 'com.google.gms:google-services:4.3.3'
```

在文件的开头添加以下插件（如果尚不存在）：

```groovy
apply plugin: 'com.google.gms.google-services'
```

在工具栏中选择“立即同步”。 添加以下代码片段，获取 Firebase Cloud Messaging SDK 为客户端应用程序实例生成的设备注册令牌。请务必将以下导入内容添加到实例主“活动”的标头中。 代码片段检索令牌时需要这些内容：

```java
import com.google.android.gms.tasks.OnCompleteListener;
import com.google.android.gms.tasks.Task;
import com.google.firebase.iid.FirebaseInstanceId;
import com.google.firebase.iid.InstanceIdResult;
```

添加此代码片段以检索令牌：

```java
FirebaseInstanceId.getInstance().getInstanceId()
    .addOnCompleteListener(new OnCompleteListener<InstanceIdResult>() {
        @Override
        public void onComplete(@NonNull Task<InstanceIdResult> task) {
            if (!task.isSuccessful()) {
                Log.w("PushNotification", "getInstanceId failed", task.getException());
                return;
            }

            // Get new Instance ID token
            String deviceToken = task.getResult().getToken();
            // Log
            Log.d("PushNotification", "Device Registration token retrieved successfully");
        }
    });
```
在通话服务 SDK 中注册设备注册令牌，以接收来电推送通知：

```java
String deviceRegistrationToken = "<Device Token from previous section>";
try {
    callAgent.registerPushNotification(deviceRegistrationToken).get();
}
catch(Exception e) {
    System.out.println("Something went wrong while registering for Incoming Calls Push Notifications.")
}
```

## <a name="push-notification-handling"></a>处理推送通知

若要接收来电推送通知，请使用有效负载在 CallAgent 实例上调用 handlePushNotification() 。

若要从 Firebase Cloud Messaging 获取有效负载，请先创建一个新服务（“文件”>“新建”>“服务”>“服务”），该服务可扩展 FirebaseMessagingService Firebase SDK 类并替代 `onMessageReceived` 方法。 当 Firebase Cloud Messaging 将推送通知传递到应用程序时，此方法被称为事件处理程序。

```java
public class MyFirebaseMessagingService extends FirebaseMessagingService {
    private java.util.Map<String, String> pushNotificationMessageDataFromFCM;

    @Override
    public void onMessageReceived(RemoteMessage remoteMessage) {
        // Check if message contains a notification payload.
        if (remoteMessage.getNotification() != null) {
            Log.d("PushNotification", "Message Notification Body: " + remoteMessage.getNotification().getBody());
        }
        else {
            pushNotificationMessageDataFromFCM = remoteMessage.getData();
        }
    }
}
```
将以下服务定义添加到 `AndroidManifest.xml` 文件的 `<application>` 标记内：

```xml
<service
    android:name=".MyFirebaseMessagingService"
    android:exported="false">
    <intent-filter>
        <action android:name="com.google.firebase.MESSAGING_EVENT" />
    </intent-filter>
</service>
```

- 检索到有效负载后，可将其传递给通信服务 SDK 以解析为内部 IncomingCallInformation 对象，该对象将通过在 CallAgent 实例上调用 handlePushNotification 方法进行处理   。 `CallAgent` 实例是通过在 `CallClient` 类上调用 `createCallAgent(...)` 方法来创建的。

```java
try {
    IncomingCallInformation notification = IncomingCallInformation.fromMap(pushNotificationMessageDataFromFCM);
    Future handlePushNotificationFuture = callAgent.handlePushNotification(notification).get();
}
catch(Exception e) {
    System.out.println("Something went wrong while handling the Incoming Calls Push Notifications.");
}
```

成功处理推送通知消息，且正确注册所有事件处理程序时，应用程序将响铃。

## <a name="unregister-push-notifications"></a>取消注册推送通知

应用程序可以随时取消注册推送通知。 若要取消注册，请在 callAgent 上调用 `unregisterPushNotification()` 方法。

```java
try {
    callAgent.unregisterPushNotification().get();
}
catch(Exception e) {
    System.out.println("Something went wrong while un-registering for all Incoming Calls Push Notifications.")
}
```