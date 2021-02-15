---
title: 跟踪传感器活动
description: 事件时间线显示网络上检测到的活动的时间线，包括警报和警报管理操作、网络事件和用户操作（如用户登录和用户删除）。
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/10/2020
ms.topic: article
ms.service: azure
ms.openlocfilehash: b8a7679521096cf3d98e8f55748eb0398fdc4ab8
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/15/2021
ms.locfileid: "100523594"
---
# <a name="track-sensor-activity"></a>跟踪传感器活动

## <a name="event-timeline"></a>事件时间线

事件时间线显示传感器检测到的活动的时间线。 例如：

  - 警报和警报管理操作

  - 网络事件

  - 用户操作，如用户登录和用户删除

事件时间线提供网络中发生的事件的按时间顺序视图。 事件时间线允许了解和分析之前和之后的事件链，这有助于调查和取证。

> [!NOTE]
> *管理员* 和 *安全分析师* 可以执行本部分中所述的过程。

查看事件日志：

- 从侧边菜单中，选择 " **事件时间线**"。

   :::image type="content" source="media/how-to-track-sensor-activity/event-timeline.png" alt-text="查看事件时间线上的事件。":::

除了查看传感器检测到的事件之外，还可以手动将事件添加到时间线。 如果事件发生在外部系统中，但影响网络，则此过程很有用，记录事件并将其显示为时间线的一部分非常重要。

手动添加事件：

- 选择 " **创建事件**"。

将事件日志信息导出到 CSV 文件中：

- 选择“导出”  。

## <a name="filter-the-event-timeline"></a>筛选事件时间线

筛选时间线以显示你感兴趣的设备和事件。

筛选时间线：

1. 选择 " **高级筛选器**"。

   :::image type="content" source="media/how-to-track-sensor-activity/advance-filters.png" alt-text="使用 &quot;事件&quot; &quot;高级筛选器&quot; 窗口筛选事件。":::

2. 设置事件筛选器，如下所示：

   - **包含地址**：显示特定设备的事件。

   - **排除地址**：隐藏特定设备的事件。

   - **Include 事件类型**：显示特定事件类型。

   - **排除事件类型**：隐藏特定事件类型。

   - **设备组**：选择设备组，因为它是在设备映射中定义的。 仅显示此组中的事件。

3. 选择 " **全部清除** " 以清除所有选定的筛选器。

4. 仅按 **警报**、 **警报和通知** 或 **所有事件** 进行搜索。

5. 选择 " **选择日期** " 选择特定日期。 选择 "天"、"小时" 和 "分钟"。 将显示所选时间范围内的事件。

6.  选择要包含或排除用户操作事件的 **用户操作** 。

7.  选择箭头 (**V**) 以查看有关事件的详细信息：

    - 如果有任何) 显示警报的详细说明，请选择相关警报 (。

    - 选择要在地图上显示设备的设备。

    - 如果要按相关设备进行筛选，请选择 " **按相关设备筛选事件** "。

    - 选择 " **PCAP 文件** " 以下载 PCAP 文件 (如果它存在，则在特定时间) 包含整个网络的数据包捕获。 
    
      PCAP 文件包含有助于网络工程师确定事件确切参数的技术信息。 可以使用网络协议分析器（如 Wireshark，即开源应用程序）分析 PCAP 文件。

## <a name="next-steps"></a>后续步骤

[查看警报](how-to-view-alerts.md)
