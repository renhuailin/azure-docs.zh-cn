---
title: 快速入门 - 从 Android 应用加入 Teams 会议
description: 本教程介绍如何使用适用于 Android 的 Azure 通信服务通话 SDK 加入 Teams 会议
author: chpalm
ms.author: rifox
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 7d9c0adfe946dee7f3a634cd2eaf44a92bab83b6
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/26/2021
ms.locfileid: "123078498"
---
本快速入门介绍如何使用适用于 Android 的 Azure 通信服务通话 SDK 加入 Teams 会议。

## <a name="sample-code"></a>代码示例
在 [GitHub](https://github.com/Azure-Samples/communication-services-android-quickstarts/tree/main/join-call-to-teams-meeting) 上查找此快速入门的最终代码。

## <a name="prerequisites"></a>先决条件

- 正常工作的[通信服务通话 Android 应用](../../getting-started-with-calling.md)。
- [Teams 部署](/deployoffice/teams-install)。


## <a name="add-the-teams-ui-controls"></a>添加 Teams UI 控件

将 activity_main.xml 中的代码替换为以下代码片段。 文本框用于输入 Teams 会议上下文，按钮用于加入指定的会议：

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <EditText
        android:id="@+id/teams_meeting_link"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:ems="10"
        android:hint="Teams meeting link"
        android:inputType="textUri"
        android:layout_marginTop="100dp"
        android:layout_marginHorizontal="20dp"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent" />

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginBottom="70dp"
        android:gravity="center"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent">

        <Button
            android:id="@+id/join_meeting_button"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Join Meeting" />

        <Button
            android:id="@+id/hangup_button"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Hangup" />

    </LinearLayout>

    <TextView
        android:id="@+id/call_status_bar"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginBottom="40dp"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent" />

    <TextView
        android:id="@+id/recording_status_bar"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginBottom="20dp"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent" />

</androidx.constraintlayout.widget.ConstraintLayout>
```

## <a name="enable-the-teams-ui-controls"></a>启用 Teams UI 控件

将 `MainActivity.java` 的内容替换为以下代码片段：

```java

package com.contoso.acsquickstart;

import androidx.annotation.NonNull;
import androidx.appcompat.app.AppCompatActivity;
import androidx.core.app.ActivityCompat;

import android.Manifest;
import android.content.pm.PackageManager;
import android.os.Bundle;
import android.widget.Button;
import android.widget.EditText;
import android.widget.TextView;
import android.widget.Toast;

import java.util.ArrayList;
import java.util.concurrent.ExecutionException;

import com.azure.android.communication.calling.Call;
import com.azure.android.communication.calling.CallAgent;
import com.azure.android.communication.calling.CallClient;
import com.azure.android.communication.calling.HangUpOptions;
import com.azure.android.communication.calling.JoinCallOptions;
import com.azure.android.communication.common.CommunicationTokenCredential;
import com.azure.android.communication.calling.TeamsMeetingLinkLocator;

public class MainActivity extends AppCompatActivity {
    private static final String[] allPermissions = new String[] { Manifest.permission.RECORD_AUDIO, Manifest.permission.CAMERA, Manifest.permission.WRITE_EXTERNAL_STORAGE, Manifest.permission.READ_PHONE_STATE };
    private static final String UserToken = "<User_Access_Token>";

    TextView callStatusBar;
    TextView recordingStatusBar;

    private CallAgent agent;
    private Call call;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        getAllPermissions();
        createAgent();

        Button joinMeetingButton = findViewById(R.id.join_meeting_button);
        joinMeetingButton.setOnClickListener(l -> joinTeamsMeeting());

        Button hangupButton = findViewById(R.id.hangup_button);
        hangupButton.setOnClickListener(l -> leaveMeeting());

        callStatusBar = findViewById(R.id.call_status_bar);
        recordingStatusBar = findViewById(R.id.recording_status_bar);
    }

    /**
     * Join Teams meeting
     */
    private void joinTeamsMeeting() {
        if (UserToken.startsWith("<")) {
            Toast.makeText(this, "Please enter token in source code", Toast.LENGTH_SHORT).show();
            return;
        }

        EditText calleeIdView = findViewById(R.id.teams_meeting_link);
        String meetingLink = calleeIdView.getText().toString();
        if (meetingLink.isEmpty()) {
            Toast.makeText(this, "Please enter Teams meeting link", Toast.LENGTH_SHORT).show();
            return;
        }

        JoinCallOptions options = new JoinCallOptions();
        TeamsMeetingLinkLocator teamsMeetingLinkLocator = new TeamsMeetingLinkLocator(meetingLink);
        call = agent.join(
                getApplicationContext(),
                teamsMeetingLinkLocator,
                options);
        call.addOnStateChangedListener(p -> setCallStatus(call.getState().toString()));
        call.addOnIsRecordingActiveChangedListener(p -> setRecordingStatus(call.isRecordingActive()));
    }

    /**
     * Leave from the meeting
     */
    private void leaveMeeting() {
        try {
            call.hangUp(new HangUpOptions()).get();
        } catch (ExecutionException | InterruptedException e) {
            Toast.makeText(this, "Unable to leave meeting", Toast.LENGTH_SHORT).show();
        }
    }

    /**
     * Create the call agent
     */
    private void createAgent() {
        try {
            CommunicationTokenCredential credential = new CommunicationTokenCredential(UserToken);
            agent = new CallClient().createCallAgent(getApplicationContext(), credential).get();
        } catch (Exception ex) {
            Toast.makeText(getApplicationContext(), "Failed to create call agent.", Toast.LENGTH_SHORT).show();
        }
    }

    /**
     * Request each required permission if the app doesn't already have it.
     */
    private void getAllPermissions() {
        ArrayList<String> permissionsToAskFor = new ArrayList<>();
        for (String permission : allPermissions) {
            if (ActivityCompat.checkSelfPermission(this, permission) != PackageManager.PERMISSION_GRANTED) {
                permissionsToAskFor.add(permission);
            }
        }
        if (!permissionsToAskFor.isEmpty()) {
            ActivityCompat.requestPermissions(this, permissionsToAskFor.toArray(new String[0]), 1);
        }
    }

    /**
     * Ensure all permissions were granted, otherwise inform the user permissions are missing.
     */
    @Override
    public void onRequestPermissionsResult(int requestCode, @NonNull String[] permissions, int[] grantResults) {
        boolean allPermissionsGranted = true;
        for (int result : grantResults) {
            allPermissionsGranted &= (result == PackageManager.PERMISSION_GRANTED);
        }
        if (!allPermissionsGranted) {
            Toast.makeText(this, "All permissions are needed to make the call.", Toast.LENGTH_LONG).show();
            finish();
        }
    }

    /**
     * Shows call status in status bar
     */
    private void setCallStatus(String status) {
        runOnUiThread(() -> callStatusBar.setText(status));
    }

    /**
     * Shows recording status status bar
     */
    private void setRecordingStatus(boolean status) {
        if (status == true) {
            runOnUiThread(() -> recordingStatusBar.setText("This call is being recorded"));
        }
        else {
            runOnUiThread(() -> recordingStatusBar.setText(""));
        }
    }
}

```

## <a name="get-the-teams-meeting-link"></a>获取 Teams 会议链接

可以使用图形 API 来检索 Teams 会议链接。 [Graph 文档](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta&preserve-view=true)中对此进行了详细介绍。
通信服务呼叫 SDK 接受完整的 Teams 会议链接。 此链接将作为 `onlineMeeting` 资源的一部分返回，可在 [`joinWebUrl` 属性](/graph/api/resources/onlinemeeting?view=graph-rest-beta&preserve-view=true)下访问。 还可以从 Teams 会议邀请本身内的“加入会议”URL 中获取所需的会议信息。

## <a name="launch-the-app-and-join-teams-meeting"></a>启动应用并加入 Teams 会议

现在可以使用工具栏上的“运行应用”按钮 (Shift+F10) 启动应用。 应该看到以下内容：

:::image type="content" source="../../media/android/acs-join-teams-meeting-quickstart.png" alt-text="显示已完成应用程序的屏幕截图。":::

将 Teams 上下文插入文本框，然后按“加入会议”，从通信服务应用程序中加入 Teams 会议。
