---
title: Azure 通信服务-常见问题/已知问题
description: 了解有关 Azure 通信服务的详细信息
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 10/03/2020
ms.topic: troubleshooting
ms.service: azure-communication-services
ms.openlocfilehash: e58335f1e266af651eb5867ca98e9ec979803b94
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101655881"
---
# <a name="faq--known-issues"></a>常见问题/已知问题
本文介绍与 Azure 通信服务相关的已知问题和常见问题。

## <a name="faq"></a>常见问题解答

### <a name="why-is-the-quality-of-my-video-degraded"></a>为什么视频质量下降？

视频流的质量取决于用于启动该流的客户端呈现器的大小。 订阅远程流时，接收方将基于发送方的客户端呈现器维度确定自己的解析。

### <a name="why-is-it-not-possible-to-enumerateselect-micspeaker-devices-on-safari"></a>为什么无法在 Safari 上枚举/选择麦克风/发言人设备？

应用程序无法枚举/选择 mic/发言人设备 (如 Safari iOS/iPad 上的蓝牙) 。 这是操作系统的限制-始终只有一个设备。

对于 MacOS 上的 Safari-应用无法通过通信服务 Device Manager 枚举/选择扬声器-必须通过 OS 选择这些项。 如果在 MacOS 上使用 Chrome，应用可以通过通信服务 Device Manager 来枚举/选择设备。

## <a name="known-issues"></a>已知问题

本部分提供有关与 Azure 通信服务相关的已知问题的信息。

### <a name="repeatedly-switching-video-devices-may-cause-video-streaming-to-temporarily-stop"></a>反复切换视频设备可能会导致视频流暂时停止

在视频设备之间切换可能会导致视频流在从所选设备获取流时暂停。

#### <a name="possible-causes"></a>可能的原因
在媒体设备之间进行流式传输和切换时，需要进行大量的计算。 频繁切换可能会导致性能下降。 鼓励开发人员先停止一个设备流，然后再开始另一个。
