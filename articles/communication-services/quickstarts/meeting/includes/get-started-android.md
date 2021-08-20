---
title: 快速入门：使用 Azure 通信服务将加入 Teams 会议添加到 Android 应用程序
description: 本快速入门介绍如何使用适用于 Android 的 Azure 通信服务 Teams 嵌入库。
author: palatter
ms.author: palatter
ms.date: 06/30/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 0340135bf17f31ccc1dd00507a1c57426a3c641d
ms.sourcegitcommit: 6bd31ec35ac44d79debfe98a3ef32fb3522e3934
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2021
ms.locfileid: "113218113"
---
本快速入门介绍如何使用适用于 Android 的 Azure 通信服务 Teams 嵌入库加入 Microsoft Teams 会议。

## <a name="sample-code"></a>代码示例

可以从 [GitHub](https://github.com/Azure-Samples/teams-embed-android-getting-started) 下载示例应用。

## <a name="prerequisites"></a>必备条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- [Android Studio](https://developer.android.com/studio)，用于创建 Android 应用程序。
- 已部署的通信服务资源。 [创建通信服务资源](../../create-communication-resource.md)。
- Azure 通信服务的[用户访问令牌](../../access-tokens.md)。

## <a name="setting-up"></a>设置

### <a name="create-an-android-app-with-an-empty-activity"></a>使用空活动创建 Android 应用

在 Android Studio 中，选择“启动新的 Android Studio 项目”。

:::image type="content" source="../media/android/studio-new-project.png" alt-text="显示在 Android Studio 中选择了“启动新的 Android Studio 项目”按钮的屏幕截图。":::

在“手机和平板电脑”下选择“空活动”项目模板。

:::image type="content" source="../media/android/studio-blank-activity.png" alt-text="显示在“项目模板”屏幕中选择了“空活动”选项的屏幕截图。":::

将项目命名为“`TeamsEmbedAndroidGettingStarted`”，将语言设置为 Java，并选择最小 SDK 为“API 21: Android 5.0 (Lollipop)”或更高版本。

:::image type="content" source="../media/android/studio-calling-min-api.png" alt-text="显示在“项目模板”屏幕中选择了“空活动”选项的屏幕截图 2。":::


### <a name="install-the-azure-package"></a>安装 Azure 包

在应用级别（应用文件夹）`build.gradle` 中，将以下行添加到“依赖项”和“Android”部分

```groovy
android {
    ...
    packagingOptions {
        pickFirst  'META-INF/*'
    }
}
```

```groovy
dependencies {
    ...
    implementation 'com.azure.android:azure-communication-common:1.0.0'
    ...
}
```

更新 `build.gradle` 文件中的值

```groovy
 buildTypes {
        release {
        ...
            minifyEnabled true
            shrinkResources true
        ...
    }
}
```


### <a name="install-the-teams-embed-package"></a>安装 Teams 嵌入包

下载 `MicrosoftTeamsSDK` 包。

然后将 `MicrosoftTeamsSDK` 文件夹解压缩到项目应用文件夹。 例如： `TeamsEmbedAndroidGettingStarted/app/MicrosoftTeamsSDK`.

### <a name="add-teams-embed-package-to-your-buildgradle"></a>向 build.gradle 添加 Teams 嵌入包。

在应用级别 `build.gradle` 中，在文件末尾添加以下行：

```groovy
apply from: 'MicrosoftTeamsSDK/MicrosoftTeamsSDK.gradle'
 ```

将项目与 gradle 文件同步。

### <a name="create-application-class"></a>创建应用程序类

创建新的 Java 类文件 `TeamsEmbedAndroidGettingStarted`。 此类将是必须扩展 `TeamsSDKApplication` 的应用程序类。 [Android 文档](https://developer.android.com/reference/android/app/Application)

:::image type="content" source="../media/android/application-class-location.png" alt-text="显示在 Android Studio 中创建应用程序类的位置的屏幕截图":::

```java
package com.microsoft.teamsembedandroidgettingstarted;

import com.microsoft.teamssdk.app.TeamsSDKApplication;

public class TeamsEmbedAndroidGettingStarted extends TeamsSDKApplication {
}
```

### <a name="update-themes"></a>更新主题

将 `theme.xml` 和 `theme.xml (night)` 文件中的样式名称设置为 `AppTheme`。

:::image type="content" source="../media/android/theme-settings.png" alt-text="显示 Android Studio 中 theme.xml 文件的屏幕截图":::

```xml
<style name="AppTheme" parent="Theme.AppCompat.DayNight.DarkActionBar">
```

```xml
<style name="AppTheme" parent="Theme.AppCompat.Light.DarkActionBar">
```

### <a name="add-permissions-to-application-manifest"></a>将权限添加到应用程序清单

将 `RECORD_AUDIO` 权限添加到应用程序清单中 (`app/src/main/AndroidManifest.xml`)：  


```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.yourcompany.TeamsEmbedAndroidGettingStarted">
    <uses-permission android:name="android.permission.RECORD_AUDIO"/>
    <application
```

### <a name="add-app-name-and-theme-to-application-manifest"></a>向应用程序清单添加应用名称和主题

向清单添加 xmlns:tools="http://schemas.android.com/tools"。

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    package="com.yourcompany.TeamsEmbedAndroidGettingStarted">
```

将 `.TeamsEmbedAndroidGettingStarted` 添加至 `android:name`、`android:name`、`tools:replace`，并将 `android:theme` 更改为 `@style/AppTheme`

```xml
<application
    android:name=".TeamsEmbedAndroidGettingStarted"
    tools:replace="android:name"
    android:theme="@style/AppTheme"
    android:allowBackup="true"
    android:icon="@mipmap/ic_launcher"
    android:label="@string/app_name"
    android:roundIcon="@mipmap/ic_launcher_round"
    android:supportsRtl="true">
```

### <a name="set-up-the-layout-for-the-app"></a>为应用设置布局

创建 ID 为 `join_meeting` 的按钮。 导航到布局文件 (`app/src/main/res/layout/activity_main.xml`) 并将文件的内容替换为以下代码：

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <Button
        android:id="@+id/join_meeting"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Join Meeting"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent" />
</androidx.constraintlayout.widget.ConstraintLayout>
```

### <a name="create-the-main-activity-scaffolding-and-bindings"></a>创建主活动基架和绑定

创建布局后，可以添加必要绑定以及活动的基本基架。 活动会处理请求运行时权限、创建会议客户端以及在按下按钮时加入会议。 每个操作都涵盖在自己的部分中。 

`onCreate` 方法会经过重写，以便为 `Join Meeting` 按钮添加绑定。 这仅在创建活动时发生一次。 有关 `onCreate` 的详细信息，请参阅指南[了解活动生命周期](https://developer.android.com/guide/components/activities/activity-lifecycle)。

导航到 MainActivity.java 并将内容替换为以下代码：

```java
package com.yourcompany.teamsembedandroidgettingstarted;

import androidx.appcompat.app.AppCompatActivity;
import androidx.core.app.ActivityCompat;

import android.Manifest;
import android.content.pm.PackageManager;
import android.os.Bundle;
import android.widget.Button;
import android.widget.Toast;

import com.azure.android.communication.common.CommunicationTokenCredential;
import com.azure.android.communication.common.CommunicationTokenRefreshOptions;
import com.azure.android.communication.ui.meetings.MeetingUIClientJoinOptions;
import com.azure.android.communication.ui.meetings.MeetingUIClient;
import com.azure.android.communication.ui.meetings.MeetingUIClientTeamsMeetingLinkLocator;

import java.util.ArrayList;
import java.util.concurrent.Callable;

public class MainActivity extends AppCompatActivity {

    private final String displayName = "John Smith";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        
        Button joinMeetingButton = findViewById(R.id.join_meeting);
        joinMeetingButton.setOnClickListener(l -> joinMeeting());
    }
    
    private void joinMeeting() {
    // See section on joining a meeting
    }

    private MeetingUIClient createMeetingUIClient() {
        // See section on creating meeting ui client
    }

    private void getAllPermissions() {
        // See section on getting permissions
    }
}
```

### <a name="request-permissions-at-runtime"></a>在运行时请求权限

对于 Android 6.0 及更高版本（API 级别 23）和 `targetSdkVersion` 23 或更高版本，在运行时（而不是在安装应用时）授予权限。 为请求权限，可以实现 `getAllPermissions` 以便为每个所需权限调用 `ActivityCompat.checkSelfPermission` 和 `ActivityCompat.requestPermissions`。

```java
/**
 * Request each required permission if the app doesn't already have it.
 */
private void getAllPermissions() {
    String[] requiredPermissions = new String[]{Manifest.permission.RECORD_AUDIO};
    ArrayList<String> permissionsToAskFor = new ArrayList<>();
    for (String permission : requiredPermissions) {
        if (ActivityCompat.checkSelfPermission(this, permission) != PackageManager.PERMISSION_GRANTED) {
            permissionsToAskFor.add(permission);
        }
    }
    if (!permissionsToAskFor.isEmpty()) {
        ActivityCompat.requestPermissions(this, permissionsToAskFor.toArray(new String[0]), 202);
    }
}
```

> [!NOTE]
> 设计应用时，请考虑何时应请求这些权限。 应在需要时请求权限，而不是提前请求。 有关详细信息，请参阅 [Android 权限指南。](https://developer.android.com/training/permissions/requesting)

## <a name="object-model"></a>对象模型

以下类和接口会处理 Azure 通信服务 Teams 嵌入库的部分主要功能：

| 名称                                    | 说明                                                                                                                                                       |
| --------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| MeetingUIClient                         | MeetingUIClient 是 Teams 嵌入库的主要入口点。                                                                                           |
| MeetingUIClientJoinOptions              | MeetingUIClientJoinOptions 用于可配置的选项，例如显示名称。                                                                                |
| MeetingUIClientTeamsMeetingLinkLocator  | MeetingUIClientTeamsMeetingLinkLocator 用于设置用于加入会议的会议 URL。                                                                      |
| MeetingUIClientGroupCallLocator         | MeetingUIClientGroupCallLocator 用于设置要加入的组 ID。                                                                                         |
| MeetingUIClientIconType                 | MeetingUIClientIconType 用于指定哪些图标可以替换为特定于应用的图标。                                                                  |
| MeetingUIClientCall                     | MeetingUIClientCall 描述调用，并提供 API 来控制调用。                                                                                           |
| MeetingUIClientCallState                | MeetingUIClientCallState 用于报告调用状态更改。 选项如下：`CONNECTING`、`WAITING_IN_LOBBY`、`CONNECTED` 和 `ENDED`。 |
| MeetingUIClientAudioRoute               | MeetingUIClientAudioRoute 用于本地音频路由，如 `Earpiece` 或 `SpeakerOn`。                                                                          |
| MeetingUIClientLayoutMode               | MeetingUIClientLayoutMode 用于允许在调用 UI 模式下选择不同的布局模式。                                                                              |
| MeetingUIClientAvatarSize               | MeetingUIClientAvatarSize 是表示由 MeetingUIClientCallIdentityProvider 请求的不同头像大小的枚举                                |
| MeetingUIClientCallEventListener        | MeetingUIClientCallEventListener 用于接收事件，例如调用状态的更改。                                                                    |
| MeetingUIClientCallIdentityProvider     | MeetingUIClientCallIdentityProvider 用于将用户详细信息映射到会议中的用户。                                                                    |
| MeetingUIClientCallUserEventListener    | MeetingUIClientCallUserEventListener 在 UI 中提供有关用户操作的信息。                                                                       |

## <a name="create-a-meetingclient-from-the-user-access-token"></a>从用户访问令牌创建 MeetingClient

经过身份验证的会议客户端可以使用用户访问令牌进行实例化。 此标记由具有特定于应用程序的身份验证的服务生成。 如要详细了解用户访问令牌的信息，请查看[用户访问令牌](../../access-tokens.md)指南。 对于快速入门，请使用为你的 Azure 通信服务资源生成的用户访问令牌替换 `<USER_ACCESS_TOKEN>`。

```java
private MeetingUIClient createMeetingUIClient() { 
    try {
        CommunicationTokenRefreshOptions refreshOptions = new CommunicationTokenRefreshOptions(tokenRefresher, true, "<USER_ACCESS_TOKEN>");
        CommunicationTokenCredential credential = new CommunicationTokenCredential(refreshOptions);
        return new MeetingUIClient(credential);
    } catch (Exception ex) {
        Toast.makeText(getApplicationContext(), "Failed to create meeting ui client: " + ex.getMessage(), Toast.LENGTH_SHORT).show();
    }
}
```

### <a name="setup-token-refreshing"></a>安装程序令牌刷新

创建一个可调用的 `tokenRefresher` 方法。 然后创建一个 `fetchToken` 方法以获取用户令牌。 [请参阅此处，了解操作说明](../../access-tokens.md?pivots=programming-language-java)

```java
Callable<String> tokenRefresher = () -> {
    return fetchToken();
};

public String fetchToken() {
    // Get token
    return USER_ACCESS_TOKEN;
}
```

## <a name="start-a-meeting-using-the-meeting-client"></a>使用会议客户端启动会议

`joinMeeting` 方法设置为在点击“加入会议”按钮时执行的操作。 通过 `MeetingUIClient` 即可加入会议，而且只需 `MeetingUIClientTeamsMeetingLinkLocator` 和 `MeetingUIClientJoinOptions` 即可实现。
注意，将 `<MEETING_URL>` 替换为 Microsoft Teams 会议链接。

```java
/**
 * Join a meeting with a meetingURL.
 */
private void joinMeeting() {
    getAllPermissions();
    MeetingUIClient meetingUIClient = createMeetingUIClient();
    
    MeetingUIClientTeamsMeetingLinkLocator meetingUIClientTeamsMeetingLinkLocator = new MeetingUIClientTeamsMeetingLinkLocator(<MEETING_URL>);
    
    MeetingUIClientJoinOptions meetingJoinOptions = new MeetingUIClientJoinOptions(displayName, false);
    
    try {
        meetingUIClient.join(meetingUIClientTeamsMeetingLinkLocator, meetingJoinOptions);
    } catch (Exception ex) {
        Toast.makeText(getApplicationContext(), "Failed to join meeting: " + ex.getMessage(), Toast.LENGTH_SHORT).show();
    }
}
```

### <a name="get-a-microsoft-teams-meeting-link"></a>获取 Microsoft Teams 会议链接

可以使用图形 API 来检索 Microsoft Teams 会议链接。 [Graph 文档](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta&preserve-view=true)中详细介绍了检索会议链接的步骤。
通信服务呼叫 SDK 接受完整的 Teams 会议链接。 此链接作为 `onlineMeeting` 资源的一部分返回，可在 [`joinWebUrl` 属性](/graph/api/resources/onlinemeeting?view=graph-rest-beta&preserve-view=true)下方获取。你还可以从 Teams 会议邀请信息的“加入会议”URL 中获取所需的会议信息。

## <a name="launch-the-app-and-join-a-meeting"></a>启动应用程序并加入会议

现在可以使用工具栏上的“运行应用”按钮 (Shift+F10) 启动应用。 

:::image type="content" source="../media/android/quickstart-android-join-meeting.png" alt-text="显示已完成应用程序的屏幕截图。":::

:::image type="content" source="../media/android/quickstart-android-joined-meeting.png" alt-text="加入会议后，显示已完成应用程序的屏幕截图。":::

## <a name="add-localization-support-based-on-your-app"></a>根据应用程序加本地化支持

Microsoft Teams SDK 支持 50 多种语言的 100 多个字符串。 默认情况下，仅启用英语。 可以在 gradle 文件中启用其他语言。

#### <a name="add-localizations-to-the-sdk-based-on-what-your-app-supports"></a>根据应用程序用支持的内容将本地化功能添加到 SDK

1. 确定应用支持的语言列表
2. 打开 MicrosoftTeamsSDK.gradle 文件
3. 在 defaultConfig 块中，默认情况下，resConfigs 属性设置为“en”。 添加应用所需的语言。 参考：[Android 文档](https://developer.android.com/studio/build/shrink-code#unused-alt-resources)
