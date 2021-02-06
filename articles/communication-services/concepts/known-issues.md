---
title: Azure 通信服务-已知问题
description: 了解 Azure 通信服务的已知问题
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 10/03/2020
ms.topic: troubleshooting
ms.service: azure-communication-services
ms.openlocfilehash: e9e4b747d9d0ab39a1d0ecef6cf45e4cc0f9e2c5
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/06/2021
ms.locfileid: "99628131"
---
# <a name="known-issues-azure-communication-services"></a>已知问题： Azure 通信服务

本文介绍与 Azure 通信服务相关的已知问题。

## <a name="video-streaming-quality-on-chromeandroid"></a>Chrome/Android 上的视频流质量 

在 Chrome Android 上，视频流性能可能会下降。

### <a name="possible-causes"></a>可能的原因
远程流的质量取决于用于启动该流的客户端呈现器的分辨率。 订阅远程流时，接收方将基于发送方的客户端呈现器维度确定自己的解析。

## <a name="bluetooth-headset-microphones-are-not-detected"></a>未检测到蓝牙耳机麦克风

将蓝牙耳机连接到通信服务呼叫时可能会遇到问题。

### <a name="possible-causes"></a>可能的原因
没有选择在 iOS 上选择蓝牙麦克风的选项。


## <a name="repeatedly-switching-video-devices-may-cause-video-streaming-to-temporarily-stop"></a>反复切换视频设备可能会导致视频流暂时停止

在视频设备之间切换可能会导致视频流在从所选设备获取流时暂停。

### <a name="possible-causes"></a>可能的原因
在媒体设备之间进行流式传输和切换时，需要进行大量的计算。 频繁切换可能会导致性能下降。 鼓励开发人员先停止一个设备流，然后再开始另一个。
