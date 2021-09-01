---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 06/30/2021
ms.author: rifox
ms.openlocfilehash: 86ab1bc1b9612c663076e5ed55ae5531317906f2
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/26/2021
ms.locfileid: "123078105"
---
## <a name="prerequisites"></a>先决条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 
- 已部署的通信服务资源。 [创建通信服务资源](../../../create-communication-resource.md)。
- 用于启用呼叫客户端的`User Access Token`。 详细了解[如何获取`User Access Token`](../../../access-tokens.md)
- 可选：完成[开始向应用程序添加通话功能](../../getting-started-with-calling.md)快速入门

## <a name="setting-up"></a>设置

### <a name="install-the-package"></a>安装包

> [!NOTE]
> 本文档使用版本 1.0.0 的通话 SDK。

找到项目级别 build.gradle，确保将 `mavenCentral()` 添加到 `buildscript` 和 `allprojects` 下的存储库列表中
```groovy
buildscript {
    repositories {
    ...
        mavenCentral()
    ...
    }
}
```

```groovy
allprojects {
    repositories {
    ...
        mavenCentral()
    ...
    }
}
```
然后，在模块级别的 build.gradle 中，将以下行添加到 dependencies 部分

```groovy
dependencies {
    ...
    implementation 'com.azure.android:azure-communication-calling:1.0.0'
    ...
}

```

## <a name="object-model"></a>对象模型

以下类和接口用于处理 Azure 通信服务通话 SDK 的某些主要功能：

| 名称                                  | 说明                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| CallClient| CallClient 是通话 SDK 的主入口点。|
| CallAgent | CallAgent 用于启动和管理呼叫。 |
| CommunicationTokenCredential | CommunicationTokenCredential 用作实例化 CallAgent 的令牌凭据。|
| CommunicationIdentifier | CommunicationIdentifier 用作可参与通话的不同类型的参与者。|

## <a name="initialize-the-callclient-create-a-callagent-and-access-the-devicemanager"></a>初始化 CallClient、创建 CallAgent 和访问 DeviceManager

若要创建 `CallAgent` 实例，必须对 `CallClient` 实例调用 `createCallAgent` 方法。 这将异步返回 `CallAgent` 实例对象。
`createCallAgent` 方法采用 `CommunicationUserCredential` 作为参数来封装[访问令牌](../../../access-tokens.md)。
若要访问 `DeviceManager`，必须先创建一个 callAgent 实例，然后可使用 `CallClient.getDeviceManager` 方法获取 DeviceManager。

```java
String userToken = '<user token>';
CallClient callClient = new CallClient();
CommunicationTokenCredential tokenCredential = new CommunicationTokenCredential(userToken);
android.content.Context appContext = this.getApplicationContext(); // From within an Activity for instance
CallAgent callAgent = callClient.createCallAgent(appContext, tokenCredential).get();
DeviceManager deviceManager = callClient.getDeviceManager(appContext).get();
```
若要为主叫方设置显示名称，请使用以下替代方法：

```java
String userToken = '<user token>';
CallClient callClient = new CallClient();
CommunicationTokenCredential tokenCredential = new CommunicationTokenCredential(userToken);
android.content.Context appContext = this.getApplicationContext(); // From within an Activity for instance
CallAgentOptions callAgentOptions = new CallAgentOptions();
callAgentOptions.setDisplayName("Alice Bob");
DeviceManager deviceManager = callClient.getDeviceManager(appContext).get();
CallAgent callAgent = callClient.createCallAgent(appContext, tokenCredential, callAgentOptions).get();
```


## <a name="place-an-outgoing-call-and-join-a-group-call"></a>拨出呼叫和加入群组通话

若要创建和发起呼叫，需要调用 `CallAgent.startCall()` 方法并为被叫方提供 `Identifier`。
若要加入群组通话，需要调用 `CallAgent.join()` 方法并提供 groupId。 组 ID 必须采用 GUID 或 UUID 格式。

创建和发起呼叫的操作是同步的。 通过呼叫实例，可订阅呼叫中的所有事件。

### <a name="place-a-11-call-to-a-user"></a>向用户发出一对一呼叫
若要向另一位通信服务用户发出呼叫，请对 `callAgent` 调用 `call` 方法，并传递带有 `communicationUserId` 键的对象。
```java
StartCallOptions startCallOptions = new StartCallOptions();
Context appContext = this.getApplicationContext();
CommunicationUserIdentifier acsUserId = new CommunicationUserIdentifier(<USER_ID>);
CommunicationUserIdentifier participants[] = new CommunicationUserIdentifier[]{ acsUserId };
call oneToOneCall = callAgent.startCall(appContext, participants, startCallOptions);
```

### <a name="place-a-1n-call-with-users-and-pstn"></a>向用户和 PSTN 发起一对多通话
> [!WARNING]
> PSTN 呼叫当前不可用

若要对用户和 PSTN 号码发出一对多呼叫，必须指定被叫方的电话号码。
必须将通信服务资源配置为允许 PSTN 呼叫：
```java
CommunicationUserIdentifier acsUser1 = new CommunicationUserIdentifier(<USER_ID>);
PhoneNumberIdentifier acsUser2 = new PhoneNumberIdentifier("<PHONE_NUMBER>");
CommunicationIdentifier participants[] = new CommunicationIdentifier[]{ acsUser1, acsUser2 };
StartCallOptions startCallOptions = new StartCallOptions();
Context appContext = this.getApplicationContext();
Call groupCall = callAgent.startCall(participants, startCallOptions);
```

### <a name="place-a-11-call-with-video-camera"></a>发起启用相机的一对一通话
> [!WARNING]
> 目前仅支持一个传出本地视频流。若要发出带有视频的呼叫，必须使用 `deviceManager` `getCameras` API 枚举本地相机。
选择所需相机后，用它来构造一个 `LocalVideoStream` 实例，并将其作为 `call` 方法的 `localVideoStream` 数组中的项目传递给 `videoOptions`。
呼叫接通后，会自动开始将视频流从所选相机发送给其他的参与者。

> [!NOTE]
> 出于隐私考虑，如果未在本地预览视频，则不在呼叫中共享该视频。
有关更多详细信息，请查看[本地相机预览](#local-camera-preview)。
```java
Context appContext = this.getApplicationContext();
VideoDeviceInfo desiredCamera = callClient.getDeviceManager(appContext).get().getCameras().get(0);
LocalVideoStream currentVideoStream = new LocalVideoStream(desiredCamera, appContext);
LocalVideoStream[] localVideoStreams = new LocalVideoStream[1];
localVideoStreams[0] = currentVideoStream;
VideoOptions videoOptions = new VideoOptions(localVideoStreams);

// Render a local preview of video so the user knows that their video is being shared
Renderer previewRenderer = new VideoStreamRenderer(currentVideoStream, appContext);
View uiView = previewRenderer.createView(new CreateViewOptions(ScalingMode.FIT));
// Attach the uiView to a viewable location on the app at this point
layout.addView(uiView);

CommunicationUserIdentifier[] participants = new CommunicationUserIdentifier[]{ new CommunicationUserIdentifier("<acs user id>") };
StartCallOptions startCallOptions = new StartCallOptions();
startCallOptions.setVideoOptions(videoOptions);
Call call = callAgent.startCall(context, participants, startCallOptions);
```

### <a name="join-a-group-call"></a>加入群组通话
若要发起新的群组通话或加入正在进行的群组通话，必须调用“join”方法并传递带有 `groupId` 属性的对象。 该值必须为 GUID。
```java
Context appContext = this.getApplicationContext();
GroupCallLocator groupCallLocator = new GroupCallLocator("<GUID>");
JoinCallOptions joinCallOptions = new JoinCallOptions();

call = callAgent.join(context, groupCallLocator, joinCallOptions);
```

### <a name="accept-a-call"></a>接受呼叫
若要接听来电，请对呼叫对象调用“accept”方法。

```java
Context appContext = this.getApplicationContext();
IncomingCall incomingCall = retrieveIncomingCall();
Call call = incomingCall.accept(context).get();
```

若要接受启用了摄像机的呼叫：

```java
Context appContext = this.getApplicationContext();
IncomingCall incomingCall = retrieveIncomingCall();
AcceptCallOptions acceptCallOptions = new AcceptCallOptions();
VideoDeviceInfo desiredCamera = callClient.getDeviceManager().get().getCameraList().get(0);
acceptCallOptions.setVideoOptions(new VideoOptions(new LocalVideoStream(desiredCamera, appContext)));
Call call = incomingCall.accept(context, acceptCallOptions).get();
```

可订阅 `callAgent` 对象上的 `onIncomingCall` 事件来获取来电：

```java
// Assuming "callAgent" is an instance property obtained by calling the 'createCallAgent' method on CallClient instance 
public Call retrieveIncomingCall() {
    IncomingCall incomingCall;
    callAgent.addOnIncomingCallListener(new IncomingCallListener() {
        void onIncomingCall(IncomingCall inboundCall) {
            // Look for incoming call
            incomingCall = inboundCall;
        }
    });
    return incomingCall;
}
```


## <a name="push-notifications"></a>推送通知

### <a name="overview"></a>概述
移动推送通知是移动设备上显示的弹出通知。 对于呼叫，我们将重点介绍 VoIP（Internet 语音协议）推送通知。 我们将注册推送通知、处理推送通知，然后取消注册推送通知。

### <a name="prerequisites"></a>先决条件

一个 Firebase 帐户，它设置为启用 Cloud Messaging (FCM) 并将 Firebase Cloud Messaging 服务连接到 Azure 通知中心实例。 有关更多详细信息，请参阅[通信服务通知](../../../../concepts/notifications.md)。
此外，本教程假定你使用 Android Studio 3.6 或更高版本来构建应用程序。

Android 应用程序需要一组权限才能接收来自 Firebase Cloud Messaging 的通知消息。 在 `AndroidManifest.xml` 文件中，在 <manifest ...> 或 </application> 标记下添加以下权限集 

```XML
    <uses-permission android:name="android.permission.INTERNET"/>
    <uses-permission android:name="android.permission.GET_ACCOUNTS"/>
    <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
```

### <a name="register-for-push-notifications"></a>注册推送通知

若要注册推送通知，应用程序需要使用设备注册令牌在 CallAgent 实例上调用 `registerPushNotification()`。

若要获取设备注册令牌，请在 `dependencies` 部分添加以下行（如果尚不存在），将 Firebase SDK 添加到应用程序模块的 build.gradle 文件中：

```
    // Add the SDK for Firebase Cloud Messaging
    implementation 'com.google.firebase:firebase-core:16.0.8'
    implementation 'com.google.firebase:firebase-messaging:20.2.4'
```

在项目级别的 build.gradle 文件中，将以下内容添加到 `dependencies` 部分（如果尚不存在）：

```
    classpath 'com.google.gms:google-services:4.3.3'
```

在文件的开头添加以下插件（如果尚不存在）：

```
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

### <a name="push-notification-handling"></a>处理推送通知

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
将以下服务定义添加到 `AndroidManifest.xml` 文件的 <application> 标记内：

```XML
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

### <a name="unregister-push-notifications"></a>取消注册推送通知

应用程序可以随时取消注册推送通知。 若要取消注册，请在 callAgent 上调用 `unregisterPushNotification()` 方法。

```java
try {
    callAgent.unregisterPushNotification().get();
}
catch(Exception e) {
    System.out.println("Something went wrong while un-registering for all Incoming Calls Push Notifications.")
}
```

## <a name="call-management"></a>通话管理
可在通话过程中访问通话属性并执行各种操作来管理与视频和音频相关的设置。

### <a name="call-properties"></a>通话属性

获取此呼叫的唯一 ID：

```java
String callId = call.getId();
```

若要了解呼叫中的其他参与者，请检查 `call` 实例上的 `remoteParticipant` 集合：

```java
List<RemoteParticipant> remoteParticipants = call.getRemoteParticipants();
```

主叫方的标识（如果是来电）：

```java
CommunicationIdentifier callerId = call.getCallerInfo().getIdentifier();
```

获取呼叫的状态： 

```java
CallState callState = call.getState();
```

它会返回一个表示当前呼叫状态的字符串：
* “NONE”- 初始通话状态
* “EARLY_MEDIA”- 表示在接通电话前播放通知的状态
* “CONNECTING”- 拨打或接听电话后的初始过渡状态
* “RINGING”- 对于去电，表示远程参与者的电话正在响铃
* “CONNECTED”- 已接通电话
* “LOCAL_HOLD”- 本地参与者暂停通话，本地终结点与远程参与者之间没有媒体传输
* “REMOTE_HOLD”- 远程参与者暂停通话，本地终结点与远程参与者之间没有媒体传输
* “DISCONNECTING”- 通话进入“Disconnected”状态前的过渡状态
* “DISCONNECTED”- 最终通话状态
* “IN_LOBBY”- 在大厅中，为了实现 Teams 会议互操作性

若要了解呼叫结束的原因，请检查 `callEndReason` 属性。 它包含代码/子代码： 

```java
CallEndReason callEndReason = call.getCallEndReason();
int code = callEndReason.getCode();
int subCode = callEndReason.getSubCode();
```

若要查看当前呼叫是来电还是去电，请检查 `callDirection` 属性：

```java
CallDirection callDirection = call.getCallDirection(); 
// callDirection == CallDirection.INCOMING for incoming call
// callDirection == CallDirection.OUTGOING for outgoing call
```

若要查看当前麦克风是否静音，请检查 `muted` 属性：

```java
boolean muted = call.isMuted();
```

若要检查活动视频流，请查看 `localVideoStreams` 集合：

```java
List<LocalVideoStream> localVideoStreams = call.getLocalVideoStreams();
```

### <a name="mute-and-unmute"></a>静音和取消静音

若要使本地终结点静音或取消静音，可使用 `mute` 和 `unmute` 异步 API：

```java
Context appContext = this.getApplicationContext();
call.mute(appContext).get();
call.unmute(appContext).get();
```
### <a name="change-the-volume-of-the-call"></a>更改通话音量
    
在进行通话时，手机上的硬件音量键应允许用户更改通话音量。
为此，通过在通话所在的活动上使用具有流类型 `AudioManager.STREAM_VOICE_CALL` 的方法 `setVolumeControlStream` 来完成。
这允许硬件音量键更改通话音量（由电话图标或音量滑块上的类似图标表示），并防止更改其他声音配置文件的音量，如警报、媒体或系统范围的音量。 有关详细信息，可以查看[处理音频输出中的更改 | Android 开发人员](https://developer.android.com/guide/topics/media-apps/volume-and-earphones)。
    
```java
@Override
protected void onCreate(Bundle savedInstanceState) {
    ...
    setVolumeControlStream(AudioManager.STREAM_VOICE_CALL);
}
```

    
### <a name="start-and-stop-sending-local-video"></a>开始和停止发送本地视频

若要开始发送视频，必须在 `deviceManager` 对象上使用 `getCameraList` API 来枚举相机。 然后，创建 `LocalVideoStream` 的新实例传递所需的相机，并将其作为参数传递给 `startVideo` API：

```java
VideoDeviceInfo desiredCamera = <get-video-device>;
Context appContext = this.getApplicationContext();
LocalVideoStream currentLocalVideoStream = new LocalVideoStream(desiredCamera, appContext);
VideoOptions videoOptions = new VideoOptions(currentLocalVideoStream);
Future startVideoFuture = call.startVideo(appContext, currentLocalVideoStream);
startVideoFuture.get();
```

成功开始发送视频后，`LocalVideoStream` 实例将被添加到呼叫实例上的 `localVideoStreams` 集合中。

```java
currentLocalVideoStream == call.getLocalVideoStreams().get(0);
```

若要停止本地视频，请传递 `localVideoStreams` 集合中可用的 `LocalVideoStream` 实例：

```java
call.stopVideo(appContext, currentLocalVideoStream).get();
```

对 `LocalVideoStream` 实例调用 `switchSource` 来发送视频时，可切换到不同的相机设备：
```java
currentLocalVideoStream.switchSource(source).get();
```

## <a name="remote-participants-management"></a>远程参与者管理

所有远程参与者均以 `RemoteParticipant` 类型表示，可通过呼叫实例上的 `remoteParticipants` 集合获得。

### <a name="list-participants-in-a-call"></a>列出通话参与者
`remoteParticipants` 集合会返回给定通话中远程参与者的列表：
```java
List<RemoteParticipant> remoteParticipants = call.getRemoteParticipants(); // [remoteParticipant, remoteParticipant....]
```

### <a name="remote-participant-properties"></a>远程参与者属性
任何给定的远程参与者都具有与之关联的一组属性和集合：

* 获取此远程参与者的标识符。
标识其中一种“标识符”类型
```java
CommunicationIdentifier participantIdentifier = remoteParticipant.getIdentifier();
```

* 获取此远程参与者的状态。
```java
ParticipantState state = remoteParticipant.getState();
```
状态可以是下列其中一项
* “IDLE”- 初始状态
* “EARLY_MEDIA”- 在参与者接通电话前播放通知
* “RINGING”- 参与者电话正在响铃
* “CONNECTING”- 参与者正在连接到通话时的过渡状态
* “CONNECTED”- 参与者已接通电话
* “HOLD”- 参与者已暂停通话
* “IN_LOBBY”- 参与者正在大厅中等待许可。 当前仅在 Teams 互操作方案中使用
* “DISCONNECTED”- 最终状态，参与者已与通话断开连接


* 若要了解参与者退出通话的原因，请检查 `callEndReason` 属性：
```java
CallEndReason callEndReason = remoteParticipant.getCallEndReason();
```

* 若要检查此远程参与者是否已静音，请检查 `isMuted` 属性：
```java
boolean isParticipantMuted = remoteParticipant.isMuted();
```

* 若要检查此远程参与者是否正在讲话，请检查 `isSpeaking` 属性：
```java
boolean isParticipantSpeaking = remoteParticipant.isSpeaking();
```

* 若要检查给定参与者在此呼叫中发送的所有视频流，请检查 `videoStreams` 集合：
```java
List<RemoteVideoStream> videoStreams = remoteParticipant.getVideoStreams(); // [RemoteVideoStream, RemoteVideoStream, ...]
```

### <a name="add-a-participant-to-a-call"></a>向通话添加参与者

若要向通话添加参与者（用户或电话号码），可调用 `addParticipant`。 这会以同步方式返回远程参与者实例。

```java
const acsUser = new CommunicationUserIdentifier("<acs user id>");
const acsPhone = new PhoneNumberIdentifier("<phone number>");
RemoteParticipant remoteParticipant1 = call.addParticipant(acsUser);
AddPhoneNumberOptions addPhoneNumberOptions = new AddPhoneNumberOptions(new PhoneNumberIdentifier("<alternate phone number>"));
RemoteParticipant remoteParticipant2 = call.addParticipant(acsPhone, addPhoneNumberOptions);
```

### <a name="remove-participant-from-a-call"></a>删除通话参与者
若要删除通话参与者（用户或电话号码），可调用 `removeParticipant`。
一旦从呼叫中删除参与者，就会异步解决此问题。
这还将从 `remoteParticipants` 集合中删除该参与者。
```java
RemoteParticipant acsUserRemoteParticipant = call.getParticipants().get(0);
RemoteParticipant acsPhoneRemoteParticipant = call.getParticipants().get(1);
call.removeParticipant(acsUserRemoteParticipant).get();
call.removeParticipant(acsPhoneRemoteParticipant).get();
```

## <a name="render-remote-participant-video-streams"></a>呈现远程参与者视频流
若要列出远程参与者的视频流和屏幕共享流，请检查 `videoStreams` 集合：
```java
RemoteParticipant remoteParticipant = call.getRemoteParticipants().get(0);
RemoteVideoStream remoteParticipantStream = remoteParticipant.getVideoStreams().get(0);
MediaStreamType streamType = remoteParticipantStream.getType(); // of type MediaStreamType.Video or MediaStreamType.ScreenSharing
```
 
若要呈现来自远程参与者的 `RemoteVideoStream`，必须订阅 `OnVideoStreamsUpdated` 事件。

在此事件中，将 `isAvailable` 属性更改为 true 表示远程参与者当前正在发送流。 发生此情况后，请创建 `Renderer` 的新实例，然后使用异步 `createView` API 创建新的 `RendererView`，并在应用程序 UI 中的任意位置附加 `view.target`。

当远程流的可用性发生变化时，可选择销毁整个呈现器、销毁特定的 `RendererView`，也可保留它们，但这将导致显示空白的视频帧。

```java
VideoStreamRenderer remoteVideoRenderer = new VideoStreamRenderer(remoteParticipantStream, appContext);
VideoStreamRendererView uiView = remoteVideoRenderer.createView(new RenderingOptions(ScalingMode.FIT));
layout.addView(uiView);

remoteParticipant.addOnVideoStreamsUpdatedListener(e -> onRemoteParticipantVideoStreamsUpdated(p, e));

void onRemoteParticipantVideoStreamsUpdated(RemoteParticipant participant, RemoteVideoStreamsEvent args) {
    for(RemoteVideoStream stream : args.getAddedRemoteVideoStreams()) {
        if(stream.getIsAvailable()) {
            startRenderingVideo();
        } else {
            renderer.dispose();
        }
    }
}
```

### <a name="remote-video-stream-properties"></a>远程视频流属性
远程视频流具有几个属性

* `Id` - 远程视频流的 ID
```java
int id = remoteVideoStream.getId();
```

* `MediaStreamType` - 可以是“Video”或“ScreenSharing”
```java
MediaStreamType type = remoteVideoStream.getMediaStreamType();
```

* `isAvailable` - 指示远程参与者终结点是否正在主动发送流
```java
boolean availability = remoteVideoStream.isAvailable();
```

### <a name="renderer-methods-and-properties"></a>呈现器方法和属性
采用 API 的呈现器对象

* 创建一个 `VideoStreamRendererView` 实例，随后可将其附加到应用程序 UI 中来呈现远程视频流。
```java
// Create a view for a video stream
VideoStreamRendererView.createView()
```
* 处置呈现器及其所有相关 `VideoStreamRendererView`。 从 UI 中删除所有关联视图后，系统会调用它。
```java
VideoStreamRenderer.dispose()
```

* `StreamSize` - 远程视频流的大小（宽度/高度）
```java
StreamSize renderStreamSize = VideoStreamRenderer.getSize();
int width = renderStreamSize.getWidth();
int height = renderStreamSize.getHeight();
```

### <a name="rendererview-methods-and-properties"></a>RendererView 方法和属性
创建 `VideoStreamRendererView` 时，可指定将应用于此视图的 `ScalingMode` 和 `mirrored` 属性：缩放模式可以是“裁剪”或“拟合”

```java
VideoStreamRenderer remoteVideoRenderer = new VideoStreamRenderer(remoteVideoStream, appContext);
VideoStreamRendererView rendererView = remoteVideoRenderer.createView(new CreateViewOptions(ScalingMode.Fit));
```

然后，可使用以下代码片段将创建的 RendererView 附加到应用程序 UI：
```java
layout.addView(rendererView);
```

稍后可在 RendererView 对象上调用 `updateScalingMode` API，并将 ScalingMode.CROP 或 ScalingMode.FIT 作为参数来更新缩放模式。
```java
// Update the scale mode for this view.
rendererView.updateScalingMode(ScalingMode.CROP)
```


## <a name="device-management"></a>设备管理

借助 `DeviceManager`，可枚举能够在通话中用于传输音频/视频流的本地设备。 你还可用它来通过本机浏览器 API 向用户请求访问其麦克风和相机的权限。

可调用 `callClient.getDeviceManager()` 方法来访问 `deviceManager`。
> [!WARNING]
> 当前必须先实例化 `callAgent` 对象才能获得 DeviceManager 的访问权限

```java
Context appContext = this.getApplicationContext();
DeviceManager deviceManager = callClient.getDeviceManager(appContext).get();
```

### <a name="enumerate-local-devices"></a>枚举本地设备

若要访问本地设备，可在设备管理器上使用枚举方法。 枚举是同步操作。

```java
//  Get a list of available video devices for use.
List<VideoDeviceInfo> localCameras = deviceManager.getCameras(); // [VideoDeviceInfo, VideoDeviceInfo...]
```

### <a name="local-camera-preview"></a>本地相机预览

可使用 `DeviceManager` 和 `Renderer` 开始呈现来自本地相机的流。 此流不会发送给其他参与者；这是一项本地预览源。 这是异步操作。

```java
VideoDeviceInfo videoDevice = <get-video-device>;
Context appContext = this.getApplicationContext();
currentVideoStream = new LocalVideoStream(videoDevice, appContext);
LocalVideoStream[] localVideoStreams = new LocalVideoStream[1];
localVideoStreams[0] = currentVideoStream;
videoOptions = new VideoOptions(localVideoStreams);

VideoStreamRenderer previewRenderer = new VideoStreamRenderer(currentVideoStream, appContext);
VideoStreamRendererView uiView = previewRenderer.createView(new RenderingOptions(ScalingMode.Fit));

// Attach the uiView to a viewable location on the app at this point
layout.addView(uiView);
```

## <a name="record-calls"></a>录制通话
> [!WARNING]
> 在 ACS 呼叫 Android SDK 的 1.1.0 版和 beta 版 1.1.0-beta.1 之前，`isRecordingActive` 和 `addOnIsRecordingActiveChangedListener` 是 `Call` 对象的一部分。 对于新的 Beta 版本，这些 API 已作为 `Call` 的扩展功能移动，如下所述。

> [!NOTE]
> 此 API 以预览状态提供给开发者，可能根据我们收到的反馈更改。 请勿在生产环境中使用此 API。 要使用此 API，请使用“beta”版 ACS 呼叫 Android SDK

通话录制是核心 `Call` API 的扩展功能。 首先需要获取录制功能 API 对象：

```java
RecordingFeature callRecordingFeature = call.api(RecordingFeature.class);
```

然后，若要检查是否正在录制通话，请检查 `callRecordingFeature` 的 `isRecordingActive` 属性。 它将返回 `boolean`。

```java
boolean isRecordingActive = callRecordingFeature.isRecordingActive();
```

还可以订阅录制更改：

```java
private void handleCallOnIsRecordingChanged(PropertyChangedEvent args) {
    boolean isRecordingActive = callRecordingFeature.isRecordingActive();
}

callRecordingFeature.addOnIsRecordingActiveChangedListener(handleCallOnIsRecordingChanged);

```

如果要从应用程序开始录制，请先按照[通话记录概述](../../../../concepts/voice-video-calling/call-recording.md)的步骤设置通话记录。

在服务器上设置通话记录后，需要从 Android 应用程序中获取来自通话的 `ServerCallId` 值，然后将其发送到服务器以开始录制过程。 可以使用 `CallInfo` 类（可使用 `getInfo()` 在类对象中找到）中的 `getServerCallId()` 查找 `ServerCallId` 值。

```java
try {
    String serverCallId = call.getInfo().getServerCallId().get();
    // Send serverCallId to your recording server to start the call recording.
} catch (ExecutionException | InterruptedException e) {
} catch (UnsupportedOperationException unsupportedOperationException) {
}
```

当从服务器开始录制时，将触发事件 `handleCallOnIsRecordingChanged`，并且 `callRecordingFeature.isRecordingActive()` 的值为 `true`。

与启动通话记录一样，如果想要停止通话记录，需要获取 `ServerCallId` 并将其发送到录制服务器，这样服务器就能停止通话记录了。

```java
try {
    String serverCallId = call.getInfo().getServerCallId().get();
    // Send serverCallId to your recording server to stop the call recording.
} catch (ExecutionException | InterruptedException e) {
} catch (UnsupportedOperationException unsupportedOperationException) {
}
```

当服务器停止录制时，将触发事件 `handleCallOnIsRecordingChanged`，并且 `callRecordingFeature.isRecordingActive()` 的值为 `false`。


## <a name="call-transcription"></a>通话听录
> [!WARNING]
> 在 ACS 呼叫 Android SDK 的 1.1.0 版和 beta 版 1.1.0-beta.1 之前，`isTranscriptionActive` 和 `addOnIsTranscriptionActiveChangedListener` 是 `Call` 对象的一部分。 对于新的 Beta 版本，这些 API 已作为 `Call` 的扩展功能移动，如下所述。
    
> [!NOTE]
> 此 API 以预览状态提供给开发者，可能根据我们收到的反馈更改。 请勿在生产环境中使用此 API。 要使用此 API，请使用“beta”版 ACS 呼叫 Android SDK

通话听录是核心 `Call` API 的扩展功能。 首先需要获取听录功能 API 对象：

```java
TranscriptionFeature callTranscriptionFeature = call.api(TranscriptionFeature.class);
```

然后，若要检查是否正在停录通话，请检查 `callTranscriptionFeature` 的 `isTranscriptionActive` 属性。 它将返回 `boolean`。

```java
boolean isTranscriptionActive = callTranscriptionFeature.isTranscriptionActive();
```

还可以订阅听录中的更改：

```java
private void handleCallOnIsTranscriptionChanged(PropertyChangedEvent args) {
    boolean isTranscriptionActive = callTranscriptionFeature.isTranscriptionActive();
}

callTranscriptionFeature.addOnIsTranscriptionActiveChangedListener(handleCallOnIsTranscriptionChanged);

```    

## <a name="eventing-model"></a>事件模型
可订阅大多数属性和集合，以便在值发生更改时收到通知。

### <a name="properties"></a>属性
若要订阅 `property changed` 事件，请采取以下操作：

```java
// subscribe
PropertyChangedListener callStateChangeListener = new PropertyChangedListener()
{
    @Override
    public void onPropertyChanged(PropertyChangedEvent args)
    {
        Log.d("The call state has changed.");
    }
}
call.addOnStateChangedListener(callStateChangeListener);

//unsubscribe
call.removeOnStateChangedListener(callStateChangeListener);
```

### <a name="collections"></a>集合
若要订阅 `collection updated` 事件，请采取以下操作：

```java
LocalVideoStreamsChangedListener localVideoStreamsChangedListener = new LocalVideoStreamsChangedListener()
{
    @Override
    public void onLocalVideoStreamsUpdated(LocalVideoStreamsEvent localVideoStreamsEventArgs) {
        Log.d(localVideoStreamsEventArgs.getAddedStreams().size());
        Log.d(localVideoStreamsEventArgs.getRemovedStreams().size());
    }
}
call.addOnLocalVideoStreamsChangedListener(localVideoStreamsChangedListener);
// To unsubscribe
call.removeOnLocalVideoStreamsChangedListener(localVideoStreamsChangedListener);
```
