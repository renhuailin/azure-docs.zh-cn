---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 09/08/2021
ms.author: rifox
ms.openlocfilehash: 7d77857152ad381bb12e5bacc9889c10ca633948
ms.sourcegitcommit: 57b7356981803f933cbf75e2d5285db73383947f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2021
ms.locfileid: "129585032"
---
[!INCLUDE [Install SDK](../install-sdk/install-sdk-android.md)]

## <a name="device-management"></a>设备管理
若要开始将视频与通话一起使用，需要知道如何管理设备。 使用设备可以控制将音频和视频传输到通话。

借助 `DeviceManager`，可枚举能够在通话中用于传输音频/视频流的本地设备。 你还可用它来通过本机浏览器 API 向用户请求访问其麦克风和相机的权限。

可调用 `callClient.getDeviceManager()` 方法来访问 `deviceManager`。

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
VideoDeviceInfo videoDevice = <get-video-device>; // See the `Enumerate local devices` topic above
Context appContext = this.getApplicationContext();

LocalVideoStream currentVideoStream = new LocalVideoStream(videoDevice, appContext);

LocalVideoStream[] localVideoStreams = new LocalVideoStream[1];
localVideoStreams[0] = currentVideoStream;

VideoOptions videoOptions = new VideoOptions(localVideoStreams);

RenderingOptions renderingOptions = new RenderingOptions(ScalingMode.Fit);
VideoStreamRenderer previewRenderer = new VideoStreamRenderer(currentVideoStream, appContext);

VideoStreamRendererView uiView = previewRenderer.createView(renderingOptions);

// Attach the uiView to a viewable location on the app at this point
layout.addView(uiView);
```

## <a name="place-a-11-call-with-video-camera"></a>发起启用相机的一对一通话
> [!WARNING]
> 目前仅支持一个传出本地视频流。若要发出带有视频的呼叫，必须使用 `deviceManager` `getCameras` API 枚举本地相机。
选择所需相机后，用它来构造一个 `LocalVideoStream` 实例，并将其作为 `call` 方法的 `localVideoStream` 数组中的项目传递给 `videoOptions`。
呼叫接通后，会自动开始将视频流从所选相机发送给其他的参与者。

> [!NOTE]
> 出于隐私考虑，如果未在本地预览视频，则不在呼叫中共享该视频。
有关更多详细信息，请查看[本地相机预览](#local-camera-preview)。

```java
VideoDeviceInfo desiredCamera = <get-video-device>; // See the `Enumerate local devices` topic above
Context appContext = this.getApplicationContext();

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

## <a name="start-and-stop-sending-local-video"></a>开始和停止发送本地视频

若要开始发送视频，必须在 `deviceManager` 对象上使用 `getCameraList` API 来枚举相机。 然后，创建 `LocalVideoStream` 的新实例传递所需的相机，并将其作为参数传递给 `startVideo` API：

```java
VideoDeviceInfo desiredCamera = <get-video-device>; // See the `Enumerate local devices` topic above
Context appContext = this.getApplicationContext();

LocalVideoStream currentLocalVideoStream = new LocalVideoStream(desiredCamera, appContext);

VideoOptions videoOptions = new VideoOptions(currentLocalVideoStream);

Future startVideoFuture = call.startVideo(appContext, currentLocalVideoStream);
startVideoFuture.get();
```

成功开始发送视频后，`LocalVideoStream` 实例将被添加到呼叫实例上的 `localVideoStreams` 集合中。

```java
List<LocalVideoStream> videoStreams = call.getLocalVideoStreams();
LocalVideoStream currentLocalVideoStream = videoStreams.get(0); // Please make sure there are VideoStreams in the list before calling get(0).
```

若要停止本地视频，请传递 `localVideoStreams` 集合中可用的 `LocalVideoStream` 实例：

```java
call.stopVideo(appContext, currentLocalVideoStream).get();
```

对 `LocalVideoStream` 实例调用 `switchSource` 来发送视频时，可切换到不同的相机设备：
```java
currentLocalVideoStream.switchSource(source).get();
```

## <a name="render-remote-participant-video-streams"></a>呈现远程参与者视频流

若要列出远程参与者的视频流和屏幕共享流，请检查 `videoStreams` 集合：

```java
List<RemoteParticipant> remoteParticipants = call.getRemoteParticipants();
RemoteParticipant remoteParticipant = remoteParticipants.get(0); // Please make sure there are remote participants in the list before calling get(0).

List<RemoteVideoStream> remoteStreams = remoteParticipant.getVideoStreams();
RemoteVideoStream remoteParticipantStream = remoteStreams.get(0); // Please make sure there are video streams in the list before calling get(0).

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
