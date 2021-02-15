---
title: 查看警报
description: 根据各种类别查看警报，并使用搜索功能帮助你查找感兴趣的警报。
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/02/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 6dc2a9683a48f20816adc8ce0ee0c1e8dc57b287
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/15/2021
ms.locfileid: "100523662"
---
# <a name="view-alerts"></a>查看警报

本文介绍如何查看传感器触发的警报并使用警报工具对其进行管理。

你可以根据各种类别查看警报，例如已存档或固定的警报。 还可以搜索感兴趣的警报，例如基于 IP 或 MAC 地址的警报。  

你还可以从传感器仪表板中查看警报。

查看警报：

- 从侧菜单中选择 " **警报** "。 "警报" 窗口显示传感器检测到的警报。

  :::image type="content" source="media/how-to-work-with-alerts-sensor/alerts-screen.png" alt-text="&quot;警报&quot; 屏幕的视图。":::

## <a name="view-alerts-by-category"></a>按类别查看警报

你可以根据 **警报** 主视图中的各种类别来查看警报。 选择警报以查看详细信息并管理事件。

| 按类型排序 | 说明 |
|--|--|
| **重要警报** | 按重要性排序的警报。 |
| **固定警报** | 用户为了进一步调查而固定的警报。 固定的警报不会存档，并在固定文件夹中存储14天。 |
| **最近的警报** | 按时间排序的警报。 |
| **确认的警报** | 已确认和未处理的警报，或者处于静音和 unmuted 的警报。 |
| **存档的警报** | 通知系统自动存档。 只有管理员用户可以访问它们。 |

## <a name="search-for-alerts-of-interest"></a>搜索感兴趣的警报

"警报" 主视图提供各种搜索功能，可帮助你查找感兴趣的警报。

:::image type="content" source="media/how-to-work-with-alerts-sensor/main-alerts-view.png" alt-text="警报学习屏幕截图。":::

### <a name="text-search"></a>文本搜索

使用 "免费搜索" 选项，按文本、数字或字符搜索警报。

搜索：

- 在 "免费搜索" 字段中键入所需的文本，然后在键盘上按 Enter。

清除搜索：

- 删除 "免费搜索" 字段中的文本，然后在键盘上按 Enter。

### <a name="device-group-or-device-ip-address-search"></a>设备组或设备 IP 地址搜索

搜索引用特定 IP 地址或设备组的警报。 设备组是在设备映射中创建的。

使用高级筛选器：

1. 从 "**警报**" 主视图中选择 "**高级筛选器**"。

2. 选择设备组或设备。

3. 选择“确认”。

4. 若要清除搜索，请选择 " **全部清除**"。

### <a name="security-versus-operational-alert-search"></a>安全性与操作警报搜索

切换查看操作和安全警报：

- **安全** 警报表示潜在的恶意软件流量、网络异常、策略冲突和协议冲突。

- **操作** 警报表示网络异常、策略冲突和协议冲突。

如果未选择任何选项，则会显示所有警报。

:::image type="content" source="media/how-to-work-with-alerts-sensor/alerts-security.png" alt-text="&quot;警报&quot; 屏幕上的安全性。":::

## <a name="alert-page-options"></a>警报页选项

警报消息提供以下操作：

- 选择 :::image type="icon" source="media/how-to-work-with-alerts-sensor/acknowledge-an-alert-icon.png" border="false"::: 确认警报。

- 选择此 :::image type="icon" source="media/how-to-work-with-alerts-sensor/unacknowledge-an-alert-icon.png" border="false"::: unacknowledge 将发出警报。

- 选择此 :::image type="icon" source="media/how-to-work-with-alerts-sensor/pin-an-alert-icon.png" border="false"::: 端口可固定警报。

- 选择 :::image type="icon" source="media/how-to-work-with-alerts-sensor/unpin-an-alert-icon.png" border="false"::: 以取消固定警报。

- 选择 :::image type="icon" source="media/how-to-work-with-alerts-sensor/acknowledge-all-alerts-icon.png" border="false"::: 确认所有警报。

- 选择 :::image type="icon" source="media/how-to-work-with-alerts-sensor/learn-and-acknowledge-all-alerts.png" border="false"::: 以了解并确认所有警报。

- 选择此 :::image type="icon" source="media/how-to-work-with-alerts-sensor/export-to-csv.png" border="false"::: 可将警报信息导出为 .csv 文件。 对于涵盖多个设备的每个警报，使用 **扩展警报导出** 选项将警报信息导出到单独的行中。

## <a name="alert-pop-up-window-options"></a>警报弹出窗口选项

- 选择此 :::image type="icon" source="media/how-to-work-with-alerts-sensor/export-to-pdf.png" border="false"::: 图标可将警报报告作为 PDF 文件下载。

- 选择 :::image type="icon" source="media/how-to-work-with-alerts-sensor/download-pcap.png" border="false"::: 图标以下载 PCAP 文件。 该文件可通过 Wireshark （免费的网络协议分析器）来查看。

- 选择 :::image type="icon" source="media/how-to-work-with-alerts-sensor/download-filtered-pcap.png" border="false"::: 此值可下载仅包含相关警报数据包的已筛选 PCAP 文件，从而减少输出文件大小并允许进行更集中的分析。 可以使用 Wireshark 查看文件。

- 选择 :::image type="icon" source="media/how-to-work-with-alerts-sensor/show-alert-in-timeline.png" border="false"::: 图标以在事件时间线中显示警报。

- 选择该 :::image type="icon" source="media/how-to-work-with-alerts-sensor/pin-an-alert-icon.png" border="false"::: 图标来固定警报。

- 选择该 :::image type="icon" source="media/how-to-work-with-alerts-sensor/unpin-an-alert-icon.png" border="false"::: 图标以取消固定警报。

- 选择 :::image type="icon" source="media/how-to-work-with-alerts-sensor/learn-icon.png" border="false"::: 此为仅 (安全分析师和管理员) 的流量。

- 选择要在设备映射中显示设备的设备。

## <a name="next-steps"></a>后续步骤

[管理警报事件](how-to-manage-the-alert-event.md)

[加速警报工作流](how-to-accelerate-alert-incident-response.md)
