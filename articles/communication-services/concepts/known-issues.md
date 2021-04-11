---
title: Azure 通信服务 - 常见问题解答/已知问题
description: 详细了解 Azure 通信服务
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: troubleshooting
ms.service: azure-communication-services
ms.openlocfilehash: 2c6ac34d8daf00578cb1d03833a28eb8535708b7
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/16/2021
ms.locfileid: "103493636"
---
# <a name="faq--known-issues"></a>常见问题解答/已知问题
本文提供了与 Azure 通信服务相关的已知问题和常见问题解答的信息。

## <a name="faq"></a>常见问题解答

### <a name="why-is-the-quality-of-my-video-degraded"></a>为什么视频质量下降？

视频流的质量取决于用于启动该流的客户端呈现器的大小。 订阅远程流时，接收方将基于发送方的客户端呈现器维度确定其自己的分辨率。

### <a name="why-is-it-not-possible-to-enumerateselect-micspeaker-devices-on-safari"></a>为什么无法在 Safari 上枚举/选择麦克风/扬声器设备？

应用程序无法在 Safari iOS/iPad 上枚举/选择麦克风/扬声器设备（如蓝牙）。 这是操作系统的限制，始终只有一台设备。

对于 MacOS 上的 Safari，应用无法通过通信服务设备管理器枚举/选择扬声器，必须通过操作系统选择这些设备。 如果在 MacOS 上使用 Chrome，应用可以通过通信服务设备管理器枚举/选择设备。

## <a name="known-issues"></a>已知问题

本部分提供有关与 Azure 通信服务相关的已知问题的信息。

### <a name="repeatedly-switching-video-devices-may-cause-video-streaming-to-temporarily-stop"></a>反复切换视频设备可能会导致视频流暂时停止

从选定设备获取视频流时，在视频设备之间切换可能会导致视频流暂停。

#### <a name="possible-causes"></a>可能的原因
在媒体设备之间进行流式传输和切换时，需要进行大量的计算。 频繁切换可能会导致性能下降。 建议开发人员先停止一个设备流，然后再开始另一个。
