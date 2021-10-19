---
title: Azure 通信服务通话诊断
titleSuffix: An Azure Communication Services concept document
description: 提供通话诊断功能的概述。
author: probableprime
ms.author: rifox
manager: chpalm
services: azure-communication-services
ms.date: 08/17/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.subservice: calling
ms.openlocfilehash: 23b75ff29e32bafdce320d334d9236902cbe4b2c
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2021
ms.locfileid: "129667604"
---
# <a name="call-diagnostics"></a>通话诊断

在 Azure 通信服务中处理通话时，可能会出现问题，从而给客户带来问题。 为此，我们提供了一项名为“通话诊断”的功能，可让你检查通话的各种属性，以确定可能是什么问题。

**目前，仅我们的 JS/Web SDK 支持“通话诊断”。**

## <a name="accessing-diagnostics"></a>访问诊断

通话诊断是核心 `Call` API 的扩展功能，可用于诊断活跃通话。

```js
const callDiagnostics = call.api(Features.Diagnostics);
```

## <a name="diagnostic-values"></a>诊断值

提供了以下面向用户的诊断：

### <a name="network-values"></a>网络值

| 名称                      | 说明                                                     | 可能值                                                                                                                                                                                                                                  | 用例                                           |
| ------------------------- | --------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | --------------------------------------------------- |
| noNetwork                 | 无可用网络。                                  | - 当通话因无可用网络而无法启动时，设为 `True`。 <br/> - 当存在 ICE 候选人时，设为 `False`。                                                                                                  | 设备未连接到网络。               |
| networkRelaysNotReachable | 网络问题。                                        | - 当网络存在一些不允许访问 ACS 中继的限制时，设为 `True`。 <br/> - 进行新通话时，设为 `False`。                                                                                                | 在 WiFi 信号打开和关闭时的通话期间。 |
| networkReconnect          | 连接断开，我们正在重新连接到网络。 | - 网络断开时，设为 `Bad` <br/> - 当媒体传输连接断开时，设为 `Poor` <br/> - 连接新会话时，设为 `Good`。                                                                       | 低带宽、没有 Internet                          |
| networkReceiveQuality     | 关于传入流质量的指标。                 | - 当接收流存在严重问题时，设为 `Bad`。 质量 <br/> - 当接收流存在轻微问题时，设为 `Poor`。 质量 <br/> - 当接收流不存在问题时，设为 `Good`。 | 低带宽                                       |

### <a name="audio-values"></a>音频值

| 名称                           | 说明                                                     | 可能值                                                                                                                                                                                                                                                                                                   | 用例                                                        |
| ------------------------------ | --------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------- |
| noSpeakerDevicesEnumerated     | 用户系统上没有音频输出设备（扬声器）。 | - 当系统上没有扬声器设备并且支持扬声器选择项时，设为 `True`。 <br/> - 当系统上至少有 1 个扬声器设备并且支持扬声器选择项时，设为 `False`。                                                                                             | 拔下所有扬声器                                       |
| speakingWhileMicrophoneIsMuted | 静音时说话。                                   | - 当本地麦克风静音且本地用户正在说话时，设为 `True`。 <br/> - 当本地用户停止说话或取消麦克风静音时，设为 `False`。 <br/> \* 请注意：到目前为止，Safari 尚不支持此功能，因为音频电平示例取自 webrtc。 统计信息。                 | 在通话过程中，将麦克风静音并对其讲话。           |
| noMicrophoneDevicesEnumerated  | 用户系统上没有音频捕获设备（麦克风）      | - 当系统上没有麦克风设备时，设为 `True`。 <br/> - 当系统上至少有 1 个麦克风设备时，设为 `False`。                                                                                                                                                              | 在通话期间，拔掉了所有麦克风。                   |
| microphoneNotFunctioning       | 麦克风无法正常工作。                                  | - 当因系统禁用麦克风设备或因其他进程占用麦克风设备而无法开始发送本地音频流时，设为 `True`。 引发此 UFD 需要大约 10 秒。 <br/> - 当麦克风再次开始成功发送音频流时，设为 `False`。 | 无可用麦克风，系统中已禁用麦克风访问 |
| microphoneMuteUnexpectedly     | 麦克风静音                                             | - 当麦克风意外进入静音状态时，设为 `True`。 <br/> - 当麦克风开始成功发送音频流时，设为 `False`                                                                                                                                                                  | 从系统将麦克风静音。                             |
| microphonePermissionDenied     | 设备音量低或 macOS 上几乎无声。 | - 当系统设置（音频）拒绝音频权限时，设为 `True`。 <br/> - 成功获取流时，设为 `False`。 <br/> 请注意：此诊断仅适用于 macOS。                                                                                                                             | “设置”中禁用麦克风权限。             |

### <a name="camera-values"></a>相机值

| 名称                   | 说明                                            | 可能值                                                                                                                                                                                                                                                                                              | 用例                                                                       |
| ---------------------- | ------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------- |
| cameraFreeze           | 相机停止生成帧，且时长超过 5 秒。 | - 当本地视频流处于冻结状态时，设为 `True`。 这意味着远程端会发现视频在其屏幕上被冻结，或意味着远程参与者没有在其屏幕上呈现视频。 <br/> - 当冻结结束并且用户可以照常观看视频时，设为 `False`。 | 通话过程中相机断开连接或网络故障导致相机冻结。 |
| cameraStartFailed      | 一般相机故障。                                | - 当因系统禁用相机设备或因其他进程占用相机设备而无法开始发送本地视频时，设为 `True`。 <br/> - 当选定的相机设备成功发送本地视频时，设为 `False`。 。                                                  | 相机故障                                                                 |
| cameraStartTimedOut    | 相机处于故障状态的常见场景。          | - 当相机设备超时并开始发送视频流时，设为 `True`。 <br/> - 当选定的相机设备再次成功发送本地视频时，设为 `False`。                                                                                                                                         | 相机故障                                                                 |
| cameraPermissionDenied | “设置”中拒绝相机权限。            | - 当系统设置（视频）拒绝相机权限时，设为 `True`。 <br/> - 成功获取流时，设为 `False`。 <br> 请注意：此诊断仅适用于 macOS Chrome                                                                                                                  | “设置”中禁用相机权限。                                |

### <a name="misc-values"></a>杂项值

| 名称                         | 说明                                                  | 可能值                                                                                                                                                                                         | 用例                                 |
| ---------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------- |
| screenshareRecordingDisabled | “设置”中的首选项拒绝系统屏幕共享。 | - 当系统设置（共享）拒绝屏幕共享权限时，设为 `True`。 <br/> - 成功获取流时，设为 `False`。 <br/> 请注意：此诊断仅适用于 macOS.Chrome。 | “设置”中禁用屏幕录制。 |

## <a name="diagnostic-events"></a>诊断事件

- 订阅 `diagnosticChanged` 事件，监视任何通话诊断何时发生更改。

```js
/**
 *  Each diagnostic has the following data:
 * - diagnostic is the type of diagnostic, e.g. NetworkSendQuality, DeviceSpeakWhileMuted, etc...
 * - value is DiagnosticQuality or DiagnosticFlag:
 *     - DiagnosticQuality = enum { Good = 1, Poor = 2, Bad = 3 }.
 *     - DiagnosticFlag = true | false.
 * - valueType = 'DiagnosticQuality' | 'DiagnosticFlag'
 * - mediaType is the media type associated with the event, e.g. Audio, Video, ScreenShare. These are defined in `CallDiagnosticEventMediaType`.
 */
const diagnosticChangedListener = (diagnosticInfo: NetworkDiagnosticChangedEventArgs | MediaDiagnosticChangedEventArgs) => {
    console.log(`Diagnostic changed: ` +
        `Diagnostic: ${diagnosticInfo.diagnostic}` +
        `Value: ${diagnosticInfo.value}` +
        `Value type: ${diagnosticInfo.valueType}` +
        `Media type: ${diagnosticInfo.mediaType}` +

    if (diagnosticInfo.valueType === 'DiagnosticQuality') {
        if (diagnosticInfo.value === DiagnosticQuality.Bad) {
            console.error(`${diagnosticInfo.diagnostic} is bad quality`);

        } else if (diagnosticInfo.value === DiagnosticQuality.Poor) {
            console.error(`${diagnosticInfo.diagnostic} is poor quality`);
        }

    } else if (diagnosticInfo.valueType === 'DiagnosticFlag') {
        if (diagnosticInfo.value === true) {
            console.error(`${diagnosticInfo.diagnostic}`);
        }
    }
};

callDiagnostics.network.on('diagnosticChanged', diagnosticChangedListener);
callDiagnostics.media.on('diagnosticChanged', diagnosticChangedListener);
```

## <a name="get-the-latest-diagnostics"></a>获取最新诊断信息

- 获取引发的最新通话诊断值。 如果诊断未定义，则是因为从未引发该诊断。

```js
const latestNetworkDiagnostics = callDiagnostics.network.getLatest();

console.log(
  `noNetwork: ${latestNetworkDiagnostics.noNetwork.value}, ` +
    `value type = ${latestNetworkDiagnostics.noNetwork.valueType}`
);

console.log(
  `networkReconnect: ${latestNetworkDiagnostics.networkReconnect.value}, ` +
    `value type = ${latestNetworkDiagnostics.networkReconnect.valueType}`
);

console.log(
  `networkReceiveQuality: ${latestNetworkDiagnostics.networkReceiveQuality.value}, ` +
    `value type = ${latestNetworkDiagnostics.networkReceiveQuality.valueType}`
);

const latestMediaDiagnostics = callDiagnostics.media.getLatest();

console.log(
  `speakingWhileMicrophoneIsMuted: ${latestMediaDiagnostics.speakingWhileMicrophoneIsMuted.value}, ` +
    `value type = ${latestMediaDiagnostics.speakingWhileMicrophoneIsMuted.valueType}`
);

console.log(
  `cameraStartFailed: ${latestMediaDiagnostics.cameraStartFailed.value}, ` +
    `value type = ${latestMediaDiagnostics.cameraStartFailed.valueType}`
);

console.log(
  `microphoneNotFunctioning: ${latestMediaDiagnostics.microphoneNotFunctioning.value}, ` +
    `value type = ${latestMediaDiagnostics.microphoneNotFunctioning.valueType}`
);
```
