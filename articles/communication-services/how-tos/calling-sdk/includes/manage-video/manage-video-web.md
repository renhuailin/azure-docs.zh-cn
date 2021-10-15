---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 09/08/2021
ms.author: rifox
ms.openlocfilehash: 97e28db07e950fde42033ef57c76b2734b73f7cc
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "128698908"
---
[!INCLUDE [Install SDK](../install-sdk/install-sdk-web.md)]

## <a name="device-management"></a>设备管理
若要开始将视频与通话一起使用，需要知道如何管理设备。 使用设备可以控制将音频和视频传输到通话。

在 `deviceManager` 中，你可以枚举可在通话中传输音频和视频流的本地设备。 还可使用它来请求访问本地设备的麦克风和相机的权限。

可以调用 `callClient.getDeviceManager()` 方法来访问 `deviceManager`：

```js
const deviceManager = await callClient.getDeviceManager();
```

### <a name="get-local-devices"></a>获取本地设备

若要访问本地设备，可在 `deviceManager` 上使用枚举方法。 枚举是异步操作

```js
//  Get a list of available video devices for use.
const localCameras = await deviceManager.getCameras(); // [VideoDeviceInfo, VideoDeviceInfo...]

// Get a list of available microphone devices for use.
const localMicrophones = await deviceManager.getMicrophones(); // [AudioDeviceInfo, AudioDeviceInfo...]

// Get a list of available speaker devices for use.
const localSpeakers = await deviceManager.getSpeakers(); // [AudioDeviceInfo, AudioDeviceInfo...]
```

### <a name="set-the-default-microphone-and-speaker"></a>设置默认麦克风和扬声器

在 `deviceManager` 中，你可以设置将用来启动通话的默认设备。 如果未设置客户端默认值，则通信服务会使用操作系统默认值。

```js
// Get the microphone device that is being used.
const defaultMicrophone = deviceManager.selectedMicrophone;

// Set the microphone device to use.
await deviceManager.selectMicrophone(localMicrophones[0]);

// Get the speaker device that is being used.
const defaultSpeaker = deviceManager.selectedSpeaker;

// Set the speaker device to use.
await deviceManager.selectSpeaker(localSpeakers[0]);
```

### <a name="local-camera-preview"></a>本地相机预览

可使用 `deviceManager` 和 `VideoStreamRenderer` 开始呈现来自本地相机的流。 此流不会发送给其他参与者；这是一项本地预览源。

```js
const cameras = await deviceManager.getCameras();
const camera = cameras[0];
const localCameraStream = new LocalVideoStream(camera);
const videoStreamRenderer = new VideoStreamRenderer(localCameraStream);
const view = await videoStreamRenderer.createView();
htmlElement.appendChild(view.target);
```

### <a name="request-permission-to-camera-and-microphone"></a>请求对相机和麦克风的权限

提示用户授予相机和/或麦克风权限：

```js
const result = await deviceManager.askDevicePermission({audio: true, video: true});
```

这将使用一个对象进行解析，该对象指示是否授予了 `audio` 和 `video` 权限：

```js
console.log(result.audio);
console.log(result.video);
```
#### <a name="notes"></a>说明
- 插入/拔出视频设备时会触发“videoDevicesUpdated”事件。
- 插入音频设备时会触发“audioDevicesUpdated”事件
- 创建 DeviceManager 时，如果尚未授予权限，则开始时它无法识别出任何设备，因此最初其设备列表为空。 如果随后调用 DeviceManager.askPermission() API，则系统会提示用户进行设备访问，如果用户单击“允许”以授权访问，则设备管理器将识别出系统上的设备，更新其设备列表并发出“audioDevicesUpdated”和“videoDevicesUpdated”事件。 假设随后刷新页面并创建设备管理器，则因为用户之前已授权访问，所以设备管理器将能够识别出设备，因此开始时设备列表会填满，并且不会发出“audioDevicesUpdated”和“videoDevicesUpdated”事件。
- Android Chrome、iOS Safari 和 macOS Safari 都不支持扬声器枚举/选择项。

## <a name="start-and-stop-sending-local-video"></a>开始和停止发送本地视频

若要开始发送视频，必须在 `deviceManager` 对象上使用 `getCameras` 方法来枚举相机。 接下来使用所需相机创建一个新的 `LocalVideoStream` 实例，然后将 `LocalVideoStream` 对象传递给现有通话对象的 `startVideo` 方法：

```js
const deviceManager = await callClient.getDeviceManager();
const cameras = await deviceManager.getCameras();
const camera = cameras[0]
const localVideoStream = new LocalVideoStream(camera);
await call.startVideo(localVideoStream);
```

当你成功开始发送视频后，系统会将一个 `LocalVideoStream` 实例添加到通话实例上的 `localVideoStreams` 集合。

```js
call.localVideoStreams[0] === localVideoStream;
```

若要停止本地视频，请传递 `localVideoStreams` 集合中可用的 `localVideoStream` 实例：

```js
await call.stopVideo(localVideoStream);
// or
await call.stopVideo(call.localVideoStreams[0]);
```

可通过 4 种方法传递 `localVideoStream` 实例，以在通话中启动视频：`callAgent.startCall()`、`callAgent.join()`、`call.accept()` 和 `call.startVideo()`。 若要使用 `call.stopVideo()`，必须传递同一个传递给用于启动视频的原始方法的 `localVideoStream` 实例。

对 `localVideoStream` 实例调用 `switchSource` 来发送视频时，可切换到不同的相机设备：

```js
const cameras = await callClient.getDeviceManager().getCameras();
const camera = cameras[1];
localVideoStream.switchSource(camera);
```

如果指定的视频设备被另一个进程占用或在系统中被禁用：
- 在通话中，如果视频已关闭并且使用 `call.startVideo()` 启动视频，则此方法将引发 `SourceUnavailableError`，并且 `cameraStartFiled` 将设置为 true。
- 调用 `localVideoStream.switchSource()` 方法将导致 `cameraStartFailed` 设置为 true。
我们的通话诊断指南提供有关如何诊断通话相关问题的其他信息。

## <a name="place-a-11-call-with-video-camera"></a>发起启用相机的一对一通话

> [!IMPORTANT]
> 目前仅支持一个传出本地视频流。

若要发起视频通话，必须使用 `deviceManager` 中的 `getCameras()` 方法枚举本地相机。

选择相机后，请使用它来构造 `LocalVideoStream` 实例。 在 `videoOptions` 中将该实例作为 `localVideoStream` 数组中的项传递到 `startCall` 方法。

```js
const deviceManager = await callClient.getDeviceManager();
const cameras = await deviceManager.getCameras();
const camera = cameras[0]
localVideoStream = new LocalVideoStream(camera);
const placeCallOptions = {videoOptions: {localVideoStreams:[localVideoStream]}};
const userCallee = { communicationUserId: '<ACS_USER_ID>' }
const call = callAgent.startCall([userCallee], placeCallOptions);
```

- 在通话接通后，它就会自动将来自所选相机的视频流发送给其他参与者。 这也适用于 `Call.Accept()` 视频选项和 `CallAgent.join()` 视频选项。

## <a name="render-remote-participant-video-streams"></a>呈现远程参与者视频流

若要列出远程参与者的视频流和屏幕共享流，请检查 `videoStreams` 集合：

```js
const remoteVideoStream: RemoteVideoStream = call.remoteParticipants[0].videoStreams[0];
const streamType: MediaStreamType = remoteVideoStream.mediaStreamType;
```

若要呈现 `RemoteVideoStream`，你必须订阅它的 `isAvailableChanged` 事件。 如果该 `isAvailable` 属性更改为 `true`，则远程参与者正在发送流。 发生该情况后，请创建一个新的 `VideoStreamRenderer` 实例，然后使用异步 `createView` 方法创建一个新的 `VideoStreamRendererView` 实例。  然后，可以将 `view.target` 附加到任何 UI 元素。

每当远程流的可用性发生变化时，可以选择销毁整个 `VideoStreamRenderer`、特定的 `VideoStreamRendererView`，或将其保留，但这将导致显示空白的视频帧。

```js
subscribeToRemoteVideoStream = async (remoteVideoStream) => {
    // Create a video stream renderer for the remote video stream.
    let videoStreamRenderer = new VideoStreamRenderer(remoteVideoStream);
    let view;
    const remoteVideoContainer = document.getElementById('remoteVideoContainer');
    const renderVideo = async () => {
        try {
            // Create a renderer view for the remote video stream.
            view = await videoStreamRenderer.createView();
            // Attach the renderer view to the UI.
            remoteVideoContainer.appendChild(view.target);
        } catch (e) {
            console.warn(`Failed to createView, reason=${e.message}, code=${e.code}`);
        }
    }
    remoteVideoStream.on('isAvailableChanged', async () => {
        // Participant has switched video on.
        if (remoteVideoStream.isAvailable) {
            await renderVideo();

        // Participant has switched video off.
        } else {
            if (view) {
                view.dispose();
                view = undefined;
            }
        }
    });

    // Participant has video on initially.
    if (remoteVideoStream.isAvailable) {
        await renderVideo();
    }
}
```

### <a name="remote-video-stream-properties"></a>远程视频流属性

远程视频流具有以下属性：

- `id`：远程视频流的 ID。

```js
const id: number = remoteVideoStream.id;
```

- `mediaStreamType`：可以是 `Video` 或 `ScreenSharing`。

```js
const type: MediaStreamType = remoteVideoStream.mediaStreamType;
```

- `isAvailable`：指示远程参与者终结点是否正在主动发送流。

```js
const type: boolean = remoteVideoStream.isAvailable;
```

## <a name="videostreamrenderer-methods-and-properties"></a>VideoStreamRenderer 方法和属性
创建一个 `VideoStreamRendererView` 实例，该实例可以附加到应用程序 UI 中来呈现远程视频流；使用异步 `createView()` 方法，该方法在流准备好呈现时进行解析，并返回一个具有 `target` 属性（表示 `video` 元素）的对象，该对象可以附加到 DOM 树中的任何位置

```js
await videoStreamRenderer.createView();
```

处置 `videoStreamRenderer` 和所有关联的 `VideoStreamRendererView` 实例：
```js
videoStreamRenderer.dispose();
```

## <a name="videostreamrendererview-methods-and-properties"></a>VideoStreamRendererView 方法和属性

创建 `VideoStreamRendererView` 时，你可以指定 `scalingMode` 和 `isMirrored` 属性。 `scalingMode` 可以是 `Stretch`、`Crop` 或 `Fit`。 如果指定了 `isMirrored`，则呈现的流会垂直翻转。

```js
const videoStreamRendererView: VideoStreamRendererView = await videoStreamRenderer.createView({ scalingMode, isMirrored });
```
每个 `VideoStreamRendererView` 实例都有一个表示呈现图面的 `target` 属性。 在应用程序 UI 中附加此属性：

```js
htmlElement.appendChild(view.target);
```

可以调用 `updateScalingMode` 方法来更新 `scalingMode`：

```js
view.updateScalingMode('Crop');
```