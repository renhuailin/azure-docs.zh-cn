---
title: IoT Edge 上的实时视频分析配额和限制 - Azure
description: 本文介绍 IoT Edge 上的实时视频分析配额和限制。
ms.topic: conceptual
ms.date: 05/22/2020
ms.openlocfilehash: 68c7b91bb1051348b5a8e52f841d443894f0a632
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "97400518"
---
# <a name="quotas-and-limitations"></a>配额和限制

本文枚举了 IoT Edge 上的实时视频分析模块的配额和限制。

## <a name="maximum-period-of-disconnected-use"></a>在已断开连接的情况下使用的最长期限

边缘模块可承受 Internet 连接暂时断开的情况。 如果模块断开连接的时间超过 36 小时，它将停用所有正在运行的图形实例， 并将阻止所有进一步的直接方法调用。

若要将边缘模块恢复到操作状态，必须还原 Internet 连接，使此模块能够与 Azure 媒体服务帐户成功通信。

## <a name="maximum-number-of-graph-instances"></a>图形实例的最大数量

每个模块最多可有 1000 个图形实例（通过 GraphInstanceSet 创建）。

## <a name="maximum-number-of-graph-topologies"></a>图形拓扑的最大数量

每个模块最多可有 50 个图形拓扑（通过 GraphTopologySet 创建）。

## <a name="limitations-on-graph-topologies-at-preview"></a>预览版图形拓扑的限制

在预览版本中，对于可以在一个媒体图拓扑中连接在一起的不同节点，存在一些限制。

* RTSP 源
   * 每个图形拓扑仅允许有一个 RTSP 源。
* 运动检测处理器
   * 必须立即成为 RTSP 源中的下游。
   * 不能是 HTTP 或 gRPC 扩展处理器使用的下游。
* 信号门处理器
   * 必须立即成为 RTSP 源中的下游。
* 资产接收器 
   * 必须立即成为 RTSP 源或信号门处理器中的下游。
* 文件接收器
   * 必须立即成为信号门处理器中的下游。
   * 不能立即成为 HTTP 或 gRPC 扩展处理器或运动检测处理器的下游
* IoT 中心接收器
   * 不能立即成为 IoT 中心源的下游。

## <a name="limitations-on-media-service-operations-at-preview"></a>预览版中媒体服务操作的限制

预览版发布时，IoT Edge 上的实时视频分析不支持以下功能：

* 能够在不中断的情况下将媒体服务帐户从一个订阅迁移到另一个订阅。
* 能够将多个存储帐户用于媒体服务帐户。
* 能够在不重启的情况下动态更改模块所需属性中的服务主体信息。

只能使用支持 RTSP 协议的 IP 相机。 可以在 [ONVIF 一致性](https://www.onvif.org/conformant-products)产品页上找到支持 RTSP 的 IP 相机。 查找符合配置文件 G、S 或 T 的设备。

此外，还应将这些相机配置为使用 H.264 视频和 AAC 音频。 当前不支持其他编解码器。 

## <a name="next-steps"></a>后续步骤

[概述](overview.md)
