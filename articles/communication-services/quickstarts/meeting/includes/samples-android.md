---
title: 使用适用于 Android 的 Azure 通信服务 Teams Embed
description: 本概述将介绍如何使用适用于 Android 的 Azure 通信服务 Teams Embed 库。
author: palatter
ms.author: palatter
ms.date: 24/02/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 711c738adaaad8aff1e1f56b537b41a846e528db
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "104802991"
---
## <a name="prerequisites"></a>先决条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 
- 已部署的通信服务资源。 [创建通信服务资源](../../create-communication-resource.md)。
- 用于启用呼叫客户端的`User Access Token`。 详细了解[如何获取`User Access Token`](../../access-tokens.md)
- 完成[开始向应用程序添加 Teams Embed](../getting-started-with-teams-embed.md) 快速入门

## <a name="teams-embed-events"></a>Teams Embed 事件

将 `MeetingEventListener` 添加到类中。

```java
public class MainActivity extends AppCompatActivity implements MeetingEventListener {

    private MeetingUIClient meetingUIClient;
```

在 `this` 中设置 `MeetingEventListener`。

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
        meetingUIClient.setMeetingEventListener(this);
    } catch (Exception ex) {
        Toast.makeText(getApplicationContext(), "Failed to create meeting client: " + ex.getMessage(), Toast.LENGTH_SHORT).show();
    }
}
```

实现 `onCallStateChanged` 和 `onRemoteParticipantCountChanged` 方法。

```java
@Override
public void onCallStateChanged(CallState callState) {
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

将 `MeetingIdentityProvider` 添加到类中。

```java
public class MainActivity extends AppCompatActivity implements MeetingIdentityProvider {

    private MeetingUIClient meetingUIClient;
```

在 `this` 中设置 `MeetingIdentityProvider`。

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
        meetingUIClient.setMeetingIdentityProvider(this);
    } catch (Exception ex) {
        Toast.makeText(getApplicationContext(), "Failed to create meeting client: " + ex.getMessage(), Toast.LENGTH_SHORT).show();
    }
}
```

将每个 `userMri` 映射到相应的头像。

```java
@Override
public void provideAvatarFor(String userIdentifier, MeetingIdentityProviderCallback meetingIdentityProviderCallback) {
    Drawable myAvatar = null;
    try {
        System.out.println("MicrosoftTeamsSDKIdentityProvider.requestForAvatar called for userIdentifier: " + userIdentifier);
        if (userIdentifier.startsWith("8:teamsvisitor:")) {
            // get and provide avatar picture asynchronously with long fetching/decoding delay.
            System.out.println("update avatar for Anonymous user");
            String imageUrl = "https://mlccdn.blob.core.windows.net/dev/LWA/qingy/doughboy_36x36.png";
            updateAvatarFromUrl(imageUrl, meetingIdentityProviderCallback);
        } else if (userIdentifier.startsWith("8:orgid:")) {
            System.out.println("update avatar for OrgID user");
            String imageUrl = "https://mlccdn.blob.core.windows.net/dev/LWA/qingy/qingy_120.jpg";
            updateAvatarFromUrl(imageUrl, meetingIdentityProviderCallback);
        } else if (userIdentifier.startsWith("8:acs:")) {
            System.out.println("update avatar for ACS user");
            String imageUrl = "https://mlccdn.blob.core.windows.net/dev/LWA/qingy/msudan.png";
            updateAvatarFromUrl(imageUrl, meetingIdentityProviderCallback);
        }
    } catch (Exception e) {
        System.out.println("MicrosoftTeamsSDKIdentityProvider: Exception while requestForAvatar for userIdentifier: " + userIdentifier + e.getMessage());
    }
}

/**
    * download and callback to set the Avatar image from web URL.
    * We have a few sample images on CDN
    *                 String imageUrl = "https://mlccdn.blob.core.windows.net/dev/LWA/qingy/doughboy_36x36.png";
    *                 String imageUrl = "https://mlccdn.blob.core.windows.net/dev/LWA/qingy/qingy_120.jpg";
    *                 String imageUrl = "https://mlccdn.blob.core.windows.net/dev/LWA/qingy/msudan.png";
    */
private void updateAvatarFromUrl(String url, MeetingIdentityProviderCallback meetingIdentityProviderCallback) {
    String urlImage = url;
    WeakReference<MeetingIdentityProviderCallback> weakReferenceObserver = new WeakReference<>(meetingIdentityProviderCallback);
    new AsyncTask<String, Integer, Drawable>(){
        @Override
        protected Drawable doInBackground(String... strings) {
            Bitmap bitmap = null;
            try {
                HttpURLConnection connection = (HttpURLConnection) new URL(urlImage).openConnection();
                connection.connect();
                InputStream input = connection.getInputStream();
                bitmap = BitmapFactory.decodeStream(input);
            } catch (IOException e) {
                e.printStackTrace();
            }
            Drawable d = new BitmapDrawable(getResources(), bitmap);
            return d;
        }
        protected void onPostExecute(Drawable myAvatar) {
            // provide avatar when it is available.
            MeetingIdentityProviderCallback callback = weakReferenceObserver.get();
            if (callback != null) {
                System.out.println("invoke the callback method with fetched avatar");
                callback.onAvatarAvailable(myAvatar);
            } else {
                System.out.println("callback observer are reclaimed, there is no need to update avatar in UI anymore");
            }
        }
    }.execute();
}
```
