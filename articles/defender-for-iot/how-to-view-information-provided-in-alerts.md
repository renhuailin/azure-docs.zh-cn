---
title: 查看警报中的信息
description: 从 "警报" 窗口中选择警报以查看详细信息。
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/03/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 5877db6b3bc7366f28e679882a2c784e6828b1c1
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/15/2021
ms.locfileid: "100523577"
---
# <a name="view-information-in-alerts"></a>查看警报中的信息

从 " **警报** " 窗口中选择警报以查看警报详细信息。 详细信息包括以下信息：

- 警报元数据

- 有关流量、设备和事件的信息

- 设备映射中连接的设备的链接

- 安全分析师和管理员定义的注释

- 调查事件的建议

## <a name="alert-metadata"></a>警报元数据

警报详细信息包括以下警报元数据：

  - 警报 ID

  - 触发警报的策略引擎

  - 触发警报的日期和时间

:::image type="content" source="media/how-to-work-with-alerts-sensor/internet-connectivity-detection-unauthorized.png" alt-text="检测到未经授权的 internet 连接。":::

## <a name="information-about-devices-traffic-and-the-event"></a>有关设备、流量和事件的信息

警报消息提供以下信息：

  - 检测到的设备。

  - 检测到设备之间的流量，如协议和函数代码。

  - 深入了解事件的含义。

在决定如何管理警报事件时，可以使用此信息。

## <a name="links-to-connected-devices-in-the-device-map"></a>设备映射中连接的设备的链接

若要详细了解连接到检测到的设备的设备，可以在警报中选择设备映像，并在地图中查看连接的设备。

:::image type="content" source="media/how-to-work-with-alerts-sensor/rcp-operation-failed.png" alt-text="RCP 操作失败。":::

:::image type="content" source="media/how-to-work-with-alerts-sensor/devices-screen-populated.png" alt-text="设备屏幕截图。":::

地图将筛选到所选的设备，以及连接到它的其他设备。 此地图还会在警报中显示检测到的设备的 " **快速属性** " 对话框。

## <a name="comments-defined-by-security-analysts-and-administrators"></a>安全分析师和管理员定义的注释 

警报可能包含预定义注释的列表。 例如，注释可以是要执行的缓解措施的说明，或者是要与事件相关的个人的姓名。

管理警报事件时，可以选择最能反映事件状态的注释或备注，也可以选择调查警报时所采取的步骤。

选择的注释保存在警报消息中。 使用注释增强了调查警报事件时个人和团队之间的通信。 因此，注释可以加快事件响应时间。

管理员或安全分析师预定义注释。 所选注释不会转发到转发规则中定义的合作伙伴系统。

查看警报中的信息后，可以执行各种取证步骤来指导您管理警报事件。 例如：

- 分析最近的设备活动 (数据挖掘报表) 。 

- 分析 (事件时间线) 同时发生的其他事件。 

- 分析)  (PCAP 文件的综合事件流量。

## <a name="pcap-files"></a>PCAP 文件

在某些情况下，可以从警报消息访问 PCAP 文件。 如果需要更多有关关联网络流量的详细信息，这可能很有用。

若要下载 PCAP 文件，请选择 " :::image type="content" source="media/how-to-work-with-alerts-sensor/download-pcap.png" alt-text="下载&quot; 图标。"::: 位于 " **警报详细信息** " 对话框的右上角。

## <a name="recommendations-for-investigating-an-event"></a>调查事件的建议 

警报的 **建议** 区域显示可帮助你更好地了解事件的信息。 在管理警报事件或对设备或网络采取操作之前，请查看此信息。

## <a name="next-steps"></a>后续步骤

[加速警报工作流](how-to-accelerate-alert-incident-response.md)

[管理警报事件](how-to-manage-the-alert-event.md)
