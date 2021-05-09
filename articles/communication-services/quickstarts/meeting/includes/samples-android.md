---
title: 使用适用于 Android 的 Azure 通信服务 Teams Embed
description: 本概述将介绍如何使用适用于 Android 的 Azure 通信服务 Teams Embed 库。
author: palatter
ms.author: palatter
ms.date: 24/02/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 49ab38b0fe9f8b61e42b60ba4637a58214c72520
ms.sourcegitcommit: b4032c9266effb0bf7eb87379f011c36d7340c2d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/22/2021
ms.locfileid: "107925302"
---
## <a name="prerequisites"></a>先决条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 
- 已部署的通信服务资源。 [创建通信服务资源](../../create-communication-resource.md)。
- 用于启用呼叫客户端的`User Access Token`。 详细了解[如何获取`User Access Token`](../../access-tokens.md)
- 完成[开始向应用程序添加 Teams Embed](../getting-started-with-teams-embed.md) 快速入门

## <a name="teams-embed-events"></a>Teams Embed 事件

将 `MeetingUIClientEventListener` 添加到类中。

```java
public class MainActivity extends AppCompatActivity implements MeetingUIClientEventListener {

    private MeetingUIClient meetingUIClient;
```

将 `MeetingUIClientEventListener` 设置为 `this`。

```java
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_main);

    createMeetingClient();
}

private void createMeetingClient() {
    try {
        CommunicationTokenCredential credential = new CommunicationTokenCredential(tokenRefresher, true, ACS_TOKEN);
        meetingUIClient = new MeetingUIClient(credential);
        meetingUIClient.setMeetingUIClientEventListener(this);
    } catch (Exception ex) {
        Toast.makeText(getApplicationContext(), "Failed to create meeting client: " + ex.getMessage(), Toast.LENGTH_SHORT).show();
    }
}
```

实现 `onCallStateChanged` 和 `onRemoteParticipantCountChanged` 方法。

```java
@Override
public void onCallStateChanged(MeetingUIClientCallState callState) {
    switch(callState) {
        case CONNECTING:
            System.out.println("Call state changed to 'Connecting'");
            break;
        case CONNECTED:
            System.out.println("Call state changed to 'Connected'");
            break;
        case WAITING_IN_LOBBY:
            System.out.println("Call state changed to 'Waiting in Lobby'");
            break;
        case ENDED:
            System.out.println("Call state changed to 'Ended'");
            break;
    }
}

@Override
public void onRemoteParticipantCountChanged(int newCount) {
    System.out.println("Remote participant count changed: " + newCount);
}
```

## <a name="assigning-avatars-for-users"></a>为用户分配头像

将 `MeetingUIClientIdentityProvider` 添加到类中。

```java
public class MainActivity extends AppCompatActivity implements MeetingUIClientIdentityProvider {

    private MeetingUIClient meetingUIClient;
```

将 `MeetingUIClientIdentityProvider` 设置为 `this`。

```java
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_main);

    createMeetingClient();
}

private void createMeetingClient() {
    try {
        CommunicationTokenCredential credential = new CommunicationTokenCredential(tokenRefresher, true, ACS_TOKEN);
        meetingUIClient = new MeetingUIClient(credential);
        meetingUIClient.setMeetingUIClientIdentityProvider(this);
    } catch (Exception ex) {
        Toast.makeText(getApplicationContext(), "Failed to create meeting client: " + ex.getMessage(), Toast.LENGTH_SHORT).show();
    }
}
```

将每个 `userMri` 映射到相应的头像。

```java
@Override
public void provideAvatarFor(String userIdentifier, MeetingUIClientIdentityProviderCallback meetingIdentityProviderCallback) {
    try {
        System.out.println("MeetingUIClientIdentityProvider.provideAvatarFor called for userIdentifier: " + userIdentifier);
        if (userIdentifier.startsWith("8:teamsvisitor:")) {
            meetingIdentityProviderCallback.onAvatarAvailable(ContextCompat.getDrawable(this, R.drawable.nodpi_avatar_placeholder_large_pink));
        } else if (userIdentifier.startsWith("8:orgid:")) {
            meetingIdentityProviderCallback.onAvatarAvailable(ContextCompat.getDrawable(this, R.drawable.nodpi_doctor_avatar));
        } else if (userIdentifier.startsWith("8:acs:")) {
            meetingIdentityProviderCallback.onAvatarAvailable(ContextCompat.getDrawable(this, R.drawable.nodpi_avatar_placeholder_large_green));
        }
    } catch (Exception e) {
        System.out.println("MeetingUIClientIdentityProvider: Exception while provideAvatarFor for userIdentifier: " + userIdentifier + e.getMessage());
    }
}
```


将其他必需的 MeetingUIClientIdentityProvider 接口方法添加到该类，但其可能会保留空实现。

```java
@Override
public void provideDisplayNameFor(String userIdentifier, MeetingUIClientIdentityProviderCallback meetingUIClientIdentityProviderCallback) {
}

@Override
public void provideSubTitleFor(String userIdentifier, MeetingUIClientIdentityProviderCallback meetingUIClientIdentityProviderCallback) {
}
```
