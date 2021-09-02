---
title: Azure 通信服务 - 已知问题
description: 详细了解 Azure 通信服务
author: rinarish
manager: chpalm
services: azure-communication-services
ms.author: rifox
ms.date: 06/30/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 73a30e7052faaaa644511fec65796a4575e5ab4a
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2021
ms.locfileid: "123259141"
---
# <a name="known-issues"></a>已知问题
本文介绍了与 Azure 通信服务通话 SDK 和 Azure 通信服务通话自动化 API 相关的限制和已知问题

> [!IMPORTANT]
> 有多个因素可能会影响通话体验的质量。 若要详细了解通信服务网络配置和测试最佳做法，请查看[网络要求](./voice-video-calling/network-requirements.md)文档。

## <a name="azure-communication-services-calling-sdks"></a>Azure 通信服务通话 SDK

### <a name="javascript-sdk"></a>JavaScript SDK

本部分介绍了与 Azure 通信服务 JavaScript 语音和视频通话 SDK 相关的已知问题。

#### <a name="refreshing-a-page-doesnt-immediately-remove-the-user-from-their-call"></a>刷新页面不会立即将用户从其通话中删除

如果用户处于通话中并决定刷新页面，则通信服务媒体服务不会立即从通话中删除此用户。 它将等待用户重新加入。 媒体服务超时后，将从通话中删除该用户。

最好是构建在通话时不要求最终用户刷新应用程序页面的用户体验。 如果用户刷新页面，请在返回应用程序后重复使用相同的通信服务用户 ID。

从通话中的其他参与者的角度来看，该用户保留在通话中的时间将为 1 分钟或 2 分钟。

如果用户使用同一通信服务用户 ID 重新加入，则该用户将在 `remoteParticipants` 集合中表示为同一现有对象。

如果用户在刷新前正在发送视频，则 `videoStreams` 集合将保留之前的流信息，直到服务超时再将其删除。 在这种情况下，应用程序可能会决定观察添加到集合中的任何新流，并呈现 `id` 最高的流。 


#### <a name="its-not-possible-to-render-multiple-previews-from-multiple-devices-on-web"></a>无法在 Web 中从多个设备呈现多个预览
这是一个已知限制。 有关详细信息，请参阅[通话 SDK 概述](./voice-video-calling/calling-sdk-features.md)。

#### <a name="enumerating-devices-isnt-possible-in-safari-when-the-application-runs-on-ios-or-ipados"></a>应用程序在 iOS 或 iPadOS 上运行时，无法在 Safari 中枚举设备

应用程序无法在 Safari iOS/iPad 上枚举/选择麦克风/扬声器设备（如蓝牙）。 这是已知的操作系统限制。

如果你使用的是 macOS 上的 Safari，应用将无法通过通信服务设备管理器枚举/选择扬声器。 在这种情况下，必须通过 OS 选择设备。 如果在 MacOS 上使用 Chrome，应用可通过通信服务设备管理器枚举/选择设备。

#### <a name="audio-connectivity-is-lost-when-receiving-sms-messages-or-calls-during-an-ongoing-voip-call"></a>在进行 VoIP 通话期间接收短信或电话时，音频连接将丢失
有多种原因可能造成此问题：

- 某些移动浏览器在后台状态时不会维持连接。 如果某一事件将应用程序推送到后台，使 VoIP 通话中断，可能会降低通话体验。 
- 有时，短信或 PSTN 通话会捕获到音频声音，但不会将声音发送回 VoIP 通话。 Apple 在 iOS 版本 14.4.1+ 中修复了此问题。 

<br/>客户端库：通话 (JavaScript)
<br/>浏览器：Safari、Chrome
<br/>操作系统：iOS、Android

#### <a name="repeatedly-switching-video-devices-may-cause-video-streaming-to-temporarily-stop"></a>反复切换视频设备可能会导致视频流暂时停止

从选定设备获取视频流时，在视频设备之间切换可能会导致视频流暂停。

##### <a name="possible-causes"></a>可能的原因
在设备间频繁切换可能会导致性能降低。 建议开发人员先停止一个设备流，然后再开始另一个。

#### <a name="bluetooth-headset-microphone-is-not-detected-therefore-is-not-audible-during-the-call-on-safari-on-ios"></a>未检测到蓝牙耳机麦克风，因此在 iOS 的 Safari 上通话时无法听到声音
iOS 上的 Safari 不支持蓝牙耳机。 你的蓝牙设备不会在可用麦克风选项中列出；如果你尝试在 Safari 上使用蓝牙，其他参与者将无法听到你的声音。

##### <a name="possible-causes"></a>可能的原因
这是已知的 macOS/iOS/iPadOS 操作系统限制。 

在 macOS 和 iOS/IPadOS 上使用 Safari 时，无法通过通信服务设备管理器枚举/选择扬声器设备，因为 Safari 不支持枚举/选择扬声器 。 在这种情况下，应通过操作系统更新设备选项。

#### <a name="rotation-of-a-device-can-create-poor-video-quality"></a>旋转设备可能会降低视频质量
旋转设备时，用户可能会遇到视频质量降低的情况。

<br/>受影响的设备：Google Pixel 5、Google Pixel 3a、Apple iPad 8 和 Apple iPad X
<br/>客户端库：通话 (JavaScript)
<br/>浏览器：Safari、Chrome
<br/>操作系统：iOS、Android


#### <a name="camera-switching-makes-the-screen-freeze"></a>切换摄像头会使屏幕冻结 
当通信服务用户使用 JavaScript 通话 SDK 加入通话，然后点击摄像头开关按钮时，UI 可能会停止响应，直到用户刷新应用程序或将浏览器推送到后台才会恢复响应。

<br/>受影响的设备：Google Pixel 4a
<br/>客户端库：通话 (JavaScript)
<br/>浏览器：Chrome
<br/>操作系统：iOS、Android


##### <a name="possible-causes"></a>可能的原因
正在调查中。

#### <a name="if-the-video-signal-was-stopped-while-the-call-is-in-connecting-state-the-video-will-not-be-sent-after-the-call-started"></a>如果视频信号在通话处于“Connecting”状态时中断，则在通话开始后不会发送视频 
如果用户决定在通话处于 `Connecting` 状态时快速打开/关闭视频，可能会导致通话获取的流出现问题。 建议开发人员采用在通话处于 `Connecting` 状态时不要求打开/关闭视频的方式来构建其应用。 在以下情况下，此问题可能会导致视频性能降低：

 - 用户开始音频，然后在通话处于 `Connecting` 状态时打开和关闭视频。
 - 用户开始音频，然后在通话处于 `Lobby` 状态时打开和关闭视频。

##### <a name="possible-causes"></a>可能的原因
正在调查中。

#### <a name="enumeratingaccessing-devices-for-safari-on-macos-and-ios"></a>在 MacOS 和 iOS 的 Safari 上枚举/访问设备 
如果已授予设备访问权限，则在一段时间后设备权限将被重置。 MacOS 和 iOS 上的 Safari 不会将权限保留很长时间，除非已获取流。 要解决此问题，最简单的方法是在调用设备管理器的设备枚举 API（DeviceManager.getCameras()、DeviceManager.getSpeakers() 和 DeviceManager.getMicrophones()）之前，先调用 DeviceManager.askDevicePermission() API。 如果有权限，用户将不会看到任何提示；如果没有权限，则系统会重新提示。

<br/>受影响的设备：iPhone
<br/>客户端库：通话 (JavaScript)
<br/>浏览器：Safari
<br/>操作系统：iOS

####  <a name="sometimes-it-takes-a-long-time-to-render-remote-participant-videos"></a>有时需要很长时间才能呈现远程参与者的视频
在进行群组通话期间，用户 A 发送视频，然后用户 B 加入通话 。 有时，用户 B 看不到用户 A 的视频，或者用户 A 的视频延迟很长时间才开始呈现。 造成此问题的原因可能是网络环境需要进一步配置。 有关网络配置指导，请查看[网络要求](./voice-video-calling/network-requirements.md)文档。

#### <a name="using-3rd-party-libraries-to-access-gum-during-the-call-may-result-in-audio-loss"></a>在通话期间使用第三方库来访问 GUM 可能会导致音频丢失
在应用程序内部单独使用 getUserMedia 将导致音频流丢失，因为第三方库会接管从 ACS 库进行的设备访问。
建议开发人员执行以下操作：
1. 通话期间，不要使用在内部采用 GetUserMedia API 的第三方库。
2. 如果仍然需要使用第三方库，只能更改所选设备（如果用户有多台设备）或重启通话来进行恢复。

<br/>浏览器：Safari
<br/>操作系统：iOS

##### <a name="possible-causes"></a>可能的原因
在某些浏览器（如 Safari）中，从同一设备获取自己的流会产生争用条件这一负面影响。 从其他设备获取流可能会导致用户 USB/IO 带宽不足，并且 sourceUnavailableError 率将激增。  


## <a name="azure-communication-services-call-automation-apis"></a>Azure 通信服务通话自动化 API

以下是 Azure 通信服务通话自动化 API 中的已知问题

- 目前，服务器应用程序唯一支持的身份验证是使用连接字符串。

- 通话应仅在同一 Azure 通信服务资源的实体之间进行。 跨资源通信已被阻止。

- 不允许在 Teams 租户用户与 Azure 通信服务用户或服务器应用程序实体之间进行通话。

- 如果应用程序通过拨出方式与两个或更多个 PSTN 标识通话，然后退出通话，则其他 PSTN 实体之间的通话会被删除。

