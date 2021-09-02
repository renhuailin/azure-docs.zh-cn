---
title: Azure 通信服务 - 最佳做法
description: 详细了解 Azure 通信服务最佳做法
author: srahaman
manager: akania
services: azure-communication-services
ms.author: srahaman
ms.date: 06/30/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: a5181a5a95c3e6eb33eb084d41674746096dd8c2
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2021
ms.locfileid: "123259120"
---
# <a name="best-practices-azure-communication-services-calling-sdks"></a>最佳做法：Azure 通信服务呼叫 SDK
本文介绍了与 Azure 通信服务 (ACS) 呼叫 SDK 相关的最佳做法。

## <a name="acs-web-javascript-sdk-best-practices"></a>ACS web JavaScript SDK 最佳做法
本部分介绍了与 Azure 通信服务 JavaScript 语音和视频呼叫 SDK 相关的最佳做法。

## <a name="javascript-voice-and-video-calling-sdk"></a>JavaScript 语音和视频呼叫 SDK

### <a name="plug-in-microphone-or-enable-microphone-from-device-manager-when-acs-call-in-progress"></a>当 ACS 呼叫正在进行时，插入麦克风或从设备管理器启用麦克风
当呼叫开始时没有可用的麦克风，然后一个麦克风变为可用时，将引发“noMicrophoneDevicesEnumerated”呼叫诊断事件。
发生这种情况时，应用程序应调用 `askDevicePermission` 以获得用户同意来枚举设备。 然后，用户将能够将麦克风静音/取消静音。

### <a name="stop-video-on-page-hide"></a>隐藏页面时停止视频
当用户导航离开“呼叫”选项卡时，视频流式处理将停止。 某些设备会继续对最后一帧进行流式处理。 为了避免这个问题，在用户导航离开启用视频的活动呼叫时，建议开发人员停止视频流式处理。 可以通过呼叫 `call.stopVideo` API 来停止视频。
```JavaScript
document.addEventListener("visibilitychange", function() {
    if (document.visibilityState === 'visible') {
        // Start Video if it was stopped on visibility change (flag true)
    } else {
        // Stop Video if it's on and set flag = true to keep track
    }
});
```

### <a name="dispose-video-stream-renderer-view"></a>释放视频流呈现器视图
当不再需要时，通信服务应用程序应释 `VideoStreamRendererView` 或其父级 `VideoStreamRenderer` 实例。

### <a name="hang-up-the-call-on-onbeforeunload-event"></a>挂断对 onbeforeunload 事件的呼叫
`onbeforeunload` 事件发出时，应用程序应调用 `call.hangup`。

### <a name="hang-up-the-call-on-microphonemuteunexpectedly-ufd"></a>挂断对 microphoneMuteUnexpectedly UFD 的呼叫
当 iOS/Safari 用户收到 PSTN 呼叫时，Azure 通信服务失去麦克风访问权限。 Azure 通信服务将引发 `microphoneMuteUnexpectedly` 呼叫诊断事件，此时，通信服务将无法重新获得对麦克风的访问权限。
建议在出现这种情况时挂断呼叫 (`call.hangUp`)。

### <a name="device-management"></a>设备管理
可以使用 Azure 通信服务 SDK 来管理设备和媒体操作。
- 应用程序不应使用本机浏览器 API（如 `getUserMedia` 或 `getDisplayMedia`）来获取 SDK 之外的流。 如果这样做，则必须在使用 `DeviceManager` 或其他设备管理 API 之前，通过通信服务 SDK 手动释放媒体流。

### <a name="request-device-permissions"></a>请求设备权限
可以使用 SDK 请求设备权限：
- 应用程序应使用 `DeviceManager.askDevicePermission` 来请求对音频和/或视频设备的访问权限。
- 如果用户拒绝访问，`DeviceManager.askDevicePermission` 将在后续呼叫时对给定的设备类型（音频或视频）返回“false”，即使在页面刷新后也是如此。 在这种情况下，应用程序必须检测到用户之前曾拒绝访问，并指示用户手动重置或明确授予对给定设备类型的访问权限。

## <a name="next-steps"></a>后续步骤
有关详细信息，请参阅以下文章：

- [向应用中添加聊天](../quickstarts/chat/get-started.md)
- [添加对应用的音频呼叫](../quickstarts/voice-video-calling/getting-started-with-calling.md)
- [参考文档](reference.md)
