---
title: Azure 通信服务呼叫 SDK 概述
titleSuffix: An Azure Communication Services concept document
description: 提供呼叫 SDK 的概述。
author: probableprime
manager: chpalm
services: azure-communication-services
ms.author: rifox
ms.date: 06/30/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.subservice: calling
ms.openlocfilehash: 410b0926faf2e5c6b8885c91cdf08343c3000469
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129362318"
---
# <a name="calling-sdk-overview"></a>呼叫 SDK 概述

[!INCLUDE [SDP Plan B Deprecation Notice](../../includes/plan-b-sdp-deprecation.md)]

最终用户设备可以通过呼叫 SDK 来驱动语音和视频通信体验。 本页提供呼叫功能的详细说明，包括平台和浏览器支持信息。 若要立即开始，请查看[呼叫快速入门](../../quickstarts/voice-video-calling/getting-started-with-calling.md)或[呼叫 hero 示例](../../samples/calling-hero-sample.md)。 

开始开发后，请查看[已知问题页](../known-issues.md)，找到我们正在处理的 bug。

呼叫 SDK 的关键功能：

- **寻址** - Azure 通信服务提供通用 [标识](../identity-model.md)用于对通信终结点进行寻址。 客户端使用这些标识对服务进行身份验证并相互通信。 在可让客户端洞察谁已连接到呼叫终结点（名单）的呼叫 API 中会使用这些标识。
- **加密** - 呼叫 SDK 将加密流量并防止在线篡改。 
- **设备管理和媒体** - 呼叫 SDK 提供所需的工具用于绑定到音频和视频设备、对内容进行编码以通过通信数据平面高效传输，并在指定的输出设备和视图中呈现内容。 此外，还提供 API 用于屏幕和应用程序共享。
- **PSTN** - 呼叫 SDK 可以 [使用你在 Azure 门户中获取的电话号码](../../quickstarts/telephony-sms/get-phone-number.md)或者以编程方式，通过传统的公共交换电话系统接收和发起语音呼叫。
- **Teams 会议** - 呼叫 SDK 可以 [加入 Teams 会议](../../quickstarts/voice-video-calling/get-started-teams-interop.md)，并与 Teams 语音和视频数据平面交互。 
- **通知** - 呼叫 SDK 提供可以在接到来电时通知客户端的 API。 如果你的应用不是在前台运行，可以使用[触发弹出式通知](../notifications.md)（“toast”）的模式来通知最终用户接到了来电。 

## <a name="detailed-capabilities"></a>详细功能 

下面列出了 Azure 通信服务呼叫 SDK 中当前提供的功能集。


| 功能组 | 功能                                                                                                          | JS  | Windows | Java (Android) | Objective-C (iOS) |
| ----------------- | ------------------------------------------------------------------------------------------------------------------- | --- | ------- | -------------- | ----------------- |
| 核心功能 | 在两个用户之间启动一对一呼叫                                                                           | ✔️   | ✔️       | ✔️              | ✔️                 |
|                   | 为两个以上的用户（最多 350 个用户）启动组呼叫                                                       | ✔️   | ✔️       | ✔️              | ✔️                 |
|                   | 将两个用户的一对一呼叫升级为两个以上用户的组呼叫                                 | ✔️   | ✔️       | ✔️              | ✔️                 |
|                   | 在一个组呼叫开始后加入它                                                                              | ✔️   | ✔️       | ✔️              | ✔️                 |
|                   | 邀请其他 VoIP 参与者加入正在进行的组呼叫                                                       | ✔️   | ✔️       | ✔️              | ✔️                 |
| Mid 呼叫控制  | 打开/关闭视频                                                                                              | ✔️   | ✔️       | ✔️              | ✔️                 |
|                   | 将麦克风静音/取消静音                                                                                                     | ✔️   | ✔️       | ✔️              | ✔️                 |
|                   | 切换照相机                                                                                              | ✔️   | ✔️       | ✔️              | ✔️                 |
|                   | 本地保持/取消保持                                                                                                  | ✔️   | ✔️       | ✔️              | ✔️                 |
|                   | 活动说话人                                                                                                      | ✔️   | ✔️       | ✔️              | ✔️                 |
|                   | 选择通话扬声器                                                                                            | ✔️   | ✔️       | ✔️              | ✔️                 |
|                   | 选择呼叫麦克风                                                                                         | ✔️   | ✔️       | ✔️              | ✔️                 |
|                   | 显示参与者的状态<br/>空闲、早期媒体、正在连接、已连接、等候接听、接听中、已断开连接         | ✔️   | ✔️       | ✔️              | ✔️                 |
|                   | 显示呼叫的状态<br/>早期媒体、正在传入、正在连接、正在响铃、已连接、通话保持、正在断开连接、已断开连接 | ✔️   | ✔️       | ✔️              | ✔️                 |
|                   | 显示参与者是否静音                                                                                      | ✔️   | ✔️       | ✔️              | ✔️                 |
|                   | 显示参与者退出通话的原因                                                                       | ✔️   | ✔️       | ✔️              | ✔️                 |
| 屏幕共享    | 在应用程序中共享整个屏幕                                                                 | ✔️   | ❌       | ❌              | ❌                 |
|                   | 共享特定的应用程序（从正在运行的应用程序列表中）                                                | ✔️   | ❌       | ❌              | ❌                 |
|                   | 从打开的选项卡列表中共享 web 浏览器选项卡                                                                  | ✔️   | ❌       | ❌              | ❌                 |
|                   | 参与者可以查看远程屏幕共享                                                                            | ✔️   | ✔️       | ✔️              | ✔️                 |
| 名单            | 列出参与者                                                                                                   | ✔️   | ✔️       | ✔️              | ✔️                 |
|                   | 删除参与者                                                                                                | ✔️   | ✔️       | ✔️              | ✔️                 |
| PSTN              | 启动与 PSTN 参与者的一对一呼叫                                                                     | ✔️   | ✔️       | ✔️              | ✔️                 |
|                   | 与 PSTN 参与者进行组呼叫                                                                           | ✔️   | ✔️       | ✔️              | ✔️                 |
|                   | 将 PSTN 参与者的一对一呼叫升级为组呼叫                                                 | ✔️   | ✔️       | ✔️              | ✔️                 |
|                   | 以 PSTN 参与者身份在组呼叫中拨出                                                                    | ✔️   | ✔️       | ✔️              | ✔️                 |
| 常规           | 使用音频测试服务测试麦克风、扬声器和照相机（可通过呼叫 8:echo123 使用）                   | ✔️   | ✔️       | ✔️              | ✔️                 |
| 设备管理 | 请求使用音频和/或视频的权限                                                                       | ✔️   | ✔️       | ✔️              | ✔️                 |
|                   | 获取相机列表                                                                                                     | ✔️   | ✔️       | ✔️              | ✔️                 |
|                   | 设置相机                                                                                                          | ✔️   | ✔️       | ✔️              | ✔️                 |
|                   | 获取所选相机                                                                                                 | ✔️   | ✔️       | ✔️              | ✔️                 |
|                   | 获取麦克风列表                                                                                                 | ✔️   | ✔️       | ❌              | ❌                 |
|                   | 设置麦克风                                                                                                      | ✔️   | ✔️       | ❌              | ❌                 |
|                   | 获取所选麦克风                                                                                             | ✔️   | ✔️       | ❌              | ❌                 |
|                   | 获取扬声器列表                                                                                                   | ✔️   | ✔️       | ❌              | ❌                 |
|                   | 设置扬声器                                                                                                         | ✔️   | ✔️       | ❌              | ❌                 |
|                   | 获取所选扬声器                                                                                                | ✔️   | ✔️       | ❌              | ❌                 |
| 视频渲染   | 在多个位置（本地相机或远程流）渲染单个视频                                                  | ✔️   | ✔️       | ✔️              | ✔️                 |
|                   | 设置/更新缩放模式                                                                                           | ✔️   | ✔️       | ✔️              | ✔️                 |
|                   | 渲染远程视频流                                                                                          | ✔️   | ✔️       | ✔️              | ✔️                 |


## <a name="calling-sdk-streaming-support"></a>呼叫 SDK 流式传输支持
通信服务呼叫 SDK 支持以下流式传输配置：

| 限制                                                         | Web                         | Windows/Android/iOS        |
| ------------------------------------------------------------- | --------------------------- | -------------------------- |
| **最多 # 个可同时发送的传出本地数据流**     | 1 个视频或 1 个屏幕共享 | 1 个视频 + 1 个屏幕共享 |
| **最多 # 个可同时呈现的传入远程数据流** | 4 个视频 + 1 个屏幕共享 | 6 个视频 + 1 个屏幕共享 |

虽然“呼叫 SDK”不会强制执行这些限制，但如果超出这些限制，用户可能会遇到性能降低的情况。

## <a name="calling-sdk-timeouts"></a>呼叫 SDK 超时

以下超时适用于通信服务呼叫 SDK：

| 操作                                                                      | 超时（秒） |
| --------------------------------------------------------------------------- | ------------------ |
| 重新连接/删除参与者                                               | 120                |
| 在调用中添加或删除新的模态（启动/停止视频或屏幕共享） | 40                 |
| 呼叫转移操作超时                                             | 60                 |
| 1:1 呼叫建立超时                                              | 85                 |
| 组呼叫建立超时                                            | 85                 |
| PSTN 呼叫建立超时                                             | 115                |
| 将 1:1 呼叫升级到组呼叫超时                                    | 115                |

## <a name="javascript-calling-sdk-support-by-os-and-browser"></a>OS 和浏览器提供的 JavaScript 呼叫 SDK 支持

下表显示了当前可用的一组受支持的浏览器。 除非另有说明，否则我们支持浏览器最新的三个版本。

| 平台     | Chrome | Safari | Microsoft Microsoft Edge (Chromium)  |
| ------------ | ------ | ------ | --------------   |
| Android      | ✔️      | ❌      | ❌           | 
| iOS          | ❌      | ✔️      | ❌           |
| macOS        | ✔️      | ✔️      | ❌           | 
| Windows      | ✔️      | ❌      | ✔️           |
| Ubuntu/Linux | ✔️      | ❌      | ❌           |    

* iOS 或 Android 不支持传出屏幕共享。
* [Safari 上的 iOS 应用无法枚举/选择麦克风和扬声器设备](../known-issues.md#enumerating-devices-isnt-possible-in-safari-when-the-application-runs-on-ios-or-ipados)（例如，蓝牙）；这是 OS 的限制，且始终只有一个设备，OS 控制默认设备选择。

## <a name="android-calling-sdk-support"></a>Android 调用 SDK 支持

* 支持 Android API 级别 21 或更高版本

* 支持 Java 7 或更高版本

* 支持 Android Studio 2.0

## <a name="ios-calling-sdk-support"></a>iOS 调用 SDK 支持

* 在编译时支持 iOS 10.0+，运行时支持 iOS 12.0+

* Xcode 12.0+

## <a name="calling-client---browser-security-model"></a>调用客户端 - 浏览器安全模型

### <a name="user-webrtc-over-https"></a>通过 HTTPS 的用户 WebRTC

WebRTC API（如 `getUserMedia`）要求通过 HTTPS 处理调用这些 API 的应用。

对于本地开发，可使用 `http://localhost`。

### <a name="embed-the-communication-services-calling-sdk-in-an-iframe"></a>在 iframe 中嵌入通信服务调用 SDK

各种浏览器正在采用新的[权限策略](https://www.w3.org/TR/permissions-policy-1/#iframe-allow-attribute)（也称为功能策略）。 此策略通过控制应用程序如何通过跨源 iframe 元素访问设备的摄像头和麦克风来影响调用方案。

如果要使用 iframe 从其他域托管应用的一部分，则必须将具有正确值的 `allow` 属性添加到 iframe。

例如，此 iframe 允许摄像头和麦克风访问：

```html
<iframe allow="camera *; microphone *">
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [呼叫入门](../../quickstarts/voice-video-calling/getting-started-with-calling.md)

有关详细信息，请参阅以下文章：
- 熟悉常规[呼叫流](../call-flows.md)
- 了解[呼叫类型](../voice-video-calling/about-call-types.md)
- [规划 PSTN 解决方案](../telephony-sms/plan-solution.md)
