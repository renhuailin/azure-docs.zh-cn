---
title: 使用传感器控制台仪表板
description: 使用仪表板可以快速查看网络的安全状态。 仪表板在时间线上提供整个系统受到的威胁的概要概述，并提供相关设备的信息。
ms.date: 11/03/2020
ms.topic: article
ms.openlocfilehash: e9bc650e9c306d12ef63994852816c96e1bdf40a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "104781528"
---
# <a name="the-dashboard"></a>仪表板

使用仪表板可以快速查看网络的安全状态。 仪表板在时间线上提供整个系统受到的威胁的概要概述，并提供相关设备的信息，包括：

- 不同严重级别的警报：

- 严重

- 主要

- 次要

- 警告

- 位于页面中心的两个指示器显示“每秒数据包数(PPS)”和“未确认的警报数(UA)”。 PPS 是系统每秒确认的数据包数。 UA 是尚未确认的警报数。

- 未确认的警报列表及其说明。

- 包含警报说明的时间线。

:::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/dashboard-alert-screen-v2.png" alt-text="仪表板":::

## <a name="viewing-the-latest-alerts"></a>查看最新警报

位于页面中心的“未确认的警报数(UA)”仪表指示此类警报的数目。 若要查看警报列表，请在仪表板页的底部选择“更多警报”，或者在边侧菜单中选择“警报” 。

:::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/unhandled-alerts-list.png" alt-text="未确认的警报数":::

## <a name="status-boxes"></a>状态框

本部分介绍每个状态框。

| 状态框和仪表 | 说明 |
| -------------- | -------------- |
| 关键警报:::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/critical-alert-status-box-v2.png" alt-text="-"::: | 关键警报 - 位于页面的中上方的框指示关键警报数目。 选中此框可在时间线上以及仪表下方的列表（如果有）中显示警报说明。                              |
| 主要警报:::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/major-alert-status-box-v2.png" alt-text="-"::: | 主要警报 - 位于页面右上方的框指示主要警报数目。 选中此框可在时间线上以及仪表下方的列表（如果有）中显示警报说明。                                     |
| 次要警报:::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/minor-alert-status-box-v2.png" alt-text="-"::: | 次要警报 - 位于页面左下方的框指示次要警报数目。 选中此框可在时间线上以及仪表下方的列表（如果有）中显示警报说明。                                   |
| :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/warnings-alert-status-box-v2.png" alt-text="警告警报"::: | 警告警报 - 位于页面的中下方的框指示警告警报数目。 选中此框可在时间线上以及仪表下方的列表（如果有）中显示警报说明。                             |
| 所有警报:::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/all-alert-status-box-v2.png" alt-text="-"::: | 所有警报 - 位于页面右下方的框指示关键、主要、次要和警告警报总数。 选中此框可在时间线上以及仪表下方的列表（如果有）中显示警报说明。 |
| 每秒数据包数:::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/packets-per-second-gauge-v2.png" alt-text="-"::: | 每秒数据包数(PPS) - PPS 指标指示网络性能。 |
| 未确认的事件数(UA):::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/unacknowledged-events-gauge-v2.png" alt-text="-"::: | 未确认的事件数 - 此指标指示未确认的事件数。

## <a name="using-the-timeline"></a>使用时间线

警报显示在包含日期和时间信息的垂直时间线上。

时间线以图形方式显示以下信息：

- 严重警报

- 主要警报

- 次要警报

- 警告警报

:::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/timeline-of-events.png" alt-text="时间线图":::

## <a name="viewing-alerts"></a>查看警报

选择警报框底部的向下箭头“V”可显示警报条目和设备信息。

:::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/extended-alert-screen.png" alt-text="警报条目和设备信息":::

- 选择设备可显示物理模式图。 相关设备会突出显示。

- 单击警报框中的任意位置可显示有关该警报的更多详细信息。 此时会显示如下所示的弹出窗口

- 选择“Excel”可以导出有关警报的 CSV 文件:::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/excel-icon.png" alt-text="-":::。

- 仅限管理员和安全分析人员 - 选择“全部确认”可以确认所有关联的警报:::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/approve-all-icon.png" alt-text="-":::。

- 选择“PDF”可将警报报告作为 PDF 文件下载:::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/pdf-icon.png" alt-text="-":::。

- 选择“固定”可以固定或取消固定警报:::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/pin-icon.png" alt-text="-":::。 选择固定会将警报添加到“警报”屏幕上的“已固定警报”窗口中 。

- 选择“下载”可以通过下载包含网络协议分析的相关 PCAP 文件来调查警报:::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/download-icon.png" alt-text="-":::。

- 选择“云”可下载相关的已筛选 PCAP 文件，其中仅包含与该警报相关的数据包，从而减少了输出文件大小，使用户能够更专注于分析:::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/cloud-download-icon.png" alt-text="-":::。 可以使用 [Wireshark](https://www.wireshark.org/) 查看该文件。

- 选择“导航”可导航到事件时间线上所请求的警报的发生时间:::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/navigate-icon.png" alt-text="-":::。 这样，你便可以评估可能发生的与特定警报相关的其他事件。

- 仅限管理员和安全分析人员 - 将警报状态从“未确认”更改为“已确认”。 选择“了解”可以批准检测到的活动。

:::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/unauthorized-internet-connectivity-detection-v3.png" alt-text="检测到未经授权的 Internet 连接":::

## <a name="see-also"></a>另请参阅

[处理传感器上的警报](how-to-work-with-alerts-on-your-sensor.md)
