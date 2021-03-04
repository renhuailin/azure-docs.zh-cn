---
title: Azure 通信服务呼叫客户端概述
titleSuffix: An Azure Communication Services concept document
description: 概述呼叫客户端库。
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: ed56d0cf69710173d41c5262e7a3121a0cad1304
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101660134"
---
# <a name="calling-client-library-overview"></a>呼叫客户端库概述

呼叫客户端库有两个不同的系列，分别面向客户端和服务 。 目前可用的客户端库适用于最终用户体验：网站和本机应用。

服务客户端库尚不可用，该库提供对原始语音和视频数据平面的访问权限，适用于与机器人和其他服务的集成。

## <a name="calling-client-library-capabilities"></a>呼叫客户端库功能

下表列出了 Azure 通信服务呼叫客户端库中当前可用的一组功能。

| 功能组 | 功能                                                                                                          | JS  | Java (Android) | Objective-C (iOS)
| ----------------- | ------------------------------------------------------------------------------------------------------------------- | ---  | -------------- | -------------
| 核心功能 | 在两个用户之间启动一对一呼叫                                                                           | ✔️   | ✔️            | ✔️
|                   | 为两个以上的用户（最多 350 个用户）启动组呼叫                                                       | ✔️   | ✔️            | ✔️
|                   | 将两个用户的一对一呼叫升级为两个以上用户的组呼叫                                 | ✔️   | ✔️            | ✔️
|                   | 在一个组呼叫开始后加入它                                                                              | ✔️   | ✔️            | ✔️
|                   | 邀请其他 VoIP 参与者加入正在进行的组呼叫                                                       | ✔️   | ✔️            | ✔️
|                   | 打开/关闭视频                                                         | ✔️   | ✔️            | ✔️
|                   | 将麦克风静音/取消静音                                                                                                     | ✔️   | ✔️            | ✔️
|                   | 切换照相机                                                                                              | ✔️   | ✔️            | ✔️
|                   | 本地保持/取消保持                                                                                                  | ✔️   | ✔️            | ✔️
|                   | 活动说话人                                                                                                      | ✔️   | ✔️            | ✔️
|                   | 选择通话扬声器                                                                                            | ✔️   | ✔️            | ✔️
|                   | 选择呼叫麦克风                                                                                         | ✔️   | ✔️            | ✔️
|                   | 显示参与者的状态<br/>空闲、早期媒体、正在连接、已连接、等候接听、接听中、已断开连接         | ✔️   | ✔️            | ✔️
|                   | 显示呼叫的状态<br/>早期媒体、正在传入、正在连接、正在响铃、已连接、通话保持、正在断开连接、已断开连接 | ✔️   | ✔️            | ✔️
|                   | 显示参与者是否静音                                                                                      | ✔️   | ✔️            | ✔️
|                   | 显示参与者退出通话的原因                                                                       | ✔️   | ✔️            | ✔️
| 屏幕共享    | 在应用程序中共享整个屏幕                                                                 | ✔️   | ❌            | ❌
|                   | 共享特定的应用程序（从正在运行的应用程序列表中）                                                | ✔️   | ❌            | ❌
|                   | 从打开的选项卡列表中共享 web 浏览器选项卡                                                                  | ✔️   | ❌            | ❌
|                   | 参与者可以查看远程屏幕共享                                                                            | ✔️   | ✔️            | ✔️
| 名单            | 列出参与者                                                                                                   | ✔️   | ✔️            | ✔️
|                   | 删除参与者                                                                                                | ✔️   | ✔️            | ✔️
| PSTN              | 启动与 PSTN 参与者的一对一呼叫                                                                     | ✔️   | ✔️            | ✔️
|                   | 与 PSTN 参与者进行组呼叫                                                                           | ✔️   | ✔️            | ✔️
|                   | 将 PSTN 参与者的一对一呼叫升级为组呼叫                                                 | ✔️   | ✔️            | ✔️
|                   | 以 PSTN 参与者身份在组呼叫中拨出                                                                    | ✔️   | ✔️            | ✔️
| 常规           | 使用音频测试服务测试麦克风、扬声器和照相机（可通过呼叫 8:echo123 使用）                   |  ✔️  | ✔️            | ✔️

## <a name="javascript-calling-client-library-support-by-os-and-browser"></a>OS 和浏览器对 JavaScript 调用客户端库的支持

下表显示了当前可用的一组受支持的浏览器和版本。

|                                  | Windows          | macOS          | Ubuntu | Linux  | Android | iOS    | iPad OS|
| -------------------------------- | ---------------- | -------------- | ------- | ------ | ------ | ------ | -------|
| **呼叫客户端库** | Chrome*、新版 Microsoft Edge | Chrome、Safari* | Chrome*  | Chrome* | Chrome* | Safari** | Safari** |


*请注意，除前两个版本外，还支持最新版本的 Chrome。<br/>

**请注意，支持 Safari 版本 13.1+。 尚不支持 Safari macOS 的传出视频，但它在 iOS 上受支持。 仅桌面 iOS 支持传出屏幕共享。 Safari 目前不支持 1:1 通话和群组通话。

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

## <a name="calling-client-library-streaming-support"></a>呼叫客户端库流式传输支持
通信服务呼叫客户端库支持以下流式传输配置：

|           |Web | Android/iOS|
|-----------|----|------------|
|**# 个可同时发送的传出数据流** |1 个音频/视频或 1 个音频/屏幕共享 | 1 音频/视频 |
|**# 个可同时呈现的传入数据流** |1 个音频/视频或 1 个音频/屏幕共享| 6 个音频/视频或 1 屏幕共享 |

请注意，在组方案中，一个混合音频流用于支持所有音频参与者。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [呼叫入门](../../quickstarts/voice-video-calling/getting-started-with-calling.md)

有关详细信息，请参阅以下文章：
- 熟悉常规[呼叫流](../call-flows.md)
- 了解[呼叫类型](../voice-video-calling/about-call-types.md)
- 了解[电话号码类型](../telephony-sms/plan-solution.md)
