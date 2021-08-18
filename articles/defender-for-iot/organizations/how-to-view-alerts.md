---
title: 在“警报”页中筛选和管理警报
description: 根据各种类别查看警报，并使用搜索功能帮助查找所需的警报。
ms.date: 05/31/2021
ms.topic: how-to
ms.openlocfilehash: 616072a83df91e047a0105e7a5cac560dcf5e6fd
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2021
ms.locfileid: "113015457"
---
# <a name="filter-and-manage-alerts-from-the-alerts-page"></a>在“警报”页中筛选和管理警报 

本文介绍如何使用警报工具查看传感器触发的警报并对其进行管理。

可以根据各种类别查看警报，例如已存档或固定的警报。 还可以搜索所需的警报，例如基于 IP 或 MAC 地址的警报。  

也可以在传感器仪表板中查看警报。

查看警报：

- 在边侧菜单中选择“警报”。 “警报”窗口将显示传感器检测到的警报。

  :::image type="content" source="media/how-to-work-with-alerts-sensor/alerts-screen.png" alt-text="“警报”屏幕视图。":::

## <a name="view-alerts-by-category"></a>按类别查看警报

可以在“警报”主视图中根据各种类别查看警报。 选择一个警报可以查看详细信息并管理事件。

| 按类型排序 | 说明 |
|--|--|
| 重要警报 | 按重要性排序的警报。 |
| 已固定的警报 | 用户固定的、要进一步调查的警报。 已固定的警报不会存档，将在 pinned 文件夹中存储 14 天。 |
| 最近的警报 | 按时间排序的警报。 |
| 已确认的警报 | 已确认但未处理的警报，或者已屏蔽和取消屏蔽的警报。 |
| 已存档的警报 | 系统已自动存档的警报。 默认情况下，警报会在触发 14 天后存档。 只有管理员用户可以访问这些警报。 |

## <a name="search-for-alerts-of-interest"></a>搜索所需的警报

“警报”主视图提供各种搜索功能用于帮助你查找所需的警报。

:::image type="content" source="media/how-to-work-with-alerts-sensor/main-alerts-view.png" alt-text="警报学习屏幕截图。":::

### <a name="text-search"></a>文本搜索

使用“自由搜索”选项可按文本、数字或字符搜索警报。

若要进行搜索，请执行以下操作：

- 在“自由搜索”字段中键入所需文本并按 Enter 键。

若要清除搜索，请执行以下操作：

- 删除“自由搜索”字段中的文本并按 Enter 键。

### <a name="device-group-or-device-ip-address-search"></a>设备组或设备 IP 地址搜索

搜索引用特定 IP 地址或设备组的警报。 设备组是在设备映射中创建的。

若要使用高级筛选器，请执行以下操作：

1. 在“警报”主视图中选择“高级筛选器” 。

2. 选择设备组或设备。

3. 选择“确认”。

4. 若要清除搜索，请选择“全部清除”。

### <a name="security-versus-operational-alert-search"></a>安全警报与操作警报搜索

在操作警报与安全警报视图之间切换：

- 安全警报表示出现了潜在的恶意软件流量、网络异常、策略冲突和协议冲突。

- 操作警报表示出现了网络异常、策略冲突和协议冲突。

如果未选择任何选项，将显示所有警报。

:::image type="content" source="media/how-to-work-with-alerts-sensor/alerts-security.png" alt-text="“警报”屏幕上的“安全”。":::

## <a name="alert-page-options"></a>警报页选项

警报消息提供以下操作：

- 选择 :::image type="icon" source="media/how-to-work-with-alerts-sensor/acknowledge-an-alert-icon.png" border="false"::: 可确认警报。

- 选择 :::image type="icon" source="media/how-to-work-with-alerts-sensor/unacknowledge-an-alert-icon.png" border="false"::: 可取消确认警报。

- 选择 :::image type="icon" source="media/how-to-work-with-alerts-sensor/pin-an-alert-icon.png" border="false"::: 可固定警报。

- 选择 :::image type="icon" source="media/how-to-work-with-alerts-sensor/unpin-an-alert-icon.png" border="false"::: 可取消固定警报。

- 选择 :::image type="icon" source="media/how-to-work-with-alerts-sensor/acknowledge-all-alerts-icon.png" border="false"::: 可确认所有警报。

- 选择 :::image type="icon" source="media/how-to-work-with-alerts-sensor/learn-and-acknowledge-all-alerts.png" border="false"::: 可学习和确认所有警报。

- 选择 :::image type="icon" source="media/how-to-work-with-alerts-sensor/export-to-csv.png" border="false"::: 可将警报信息导出到 .csv 文件。 选择“扩展警报导出”选项可以在单独的行中导出涉及多个设备的每条警报的警报信息。

## <a name="alert-pop-up-window-options"></a>警报弹出窗口选项

- 选择 :::image type="icon" source="media/how-to-work-with-alerts-sensor/export-to-pdf.png" border="false"::: 图标可将警报报告作为 PDF 文件下载。

- 选择 :::image type="icon" source="media/how-to-work-with-alerts-sensor/download-pcap.png" border="false"::: 图标可下载 PCAP 文件。 可以使用 Wireshark（免费的网络协议分析器）查看该文件。

- 选择 :::image type="icon" source="media/how-to-work-with-alerts-sensor/download-filtered-pcap.png" border="false"::: 可下载已筛选的 PCAP 文件，其中仅包含相关的警报数据包，从而减少了输出文件大小，使用户能够更专注于分析。 可以使用 Wireshark 查看该文件。

- 选择 :::image type="icon" source="media/how-to-work-with-alerts-sensor/show-alert-in-timeline.png" border="false"::: 图标可在事件时间线中显示警报。

- 选择 :::image type="icon" source="media/how-to-work-with-alerts-sensor/pin-an-alert-icon.png" border="false"::: 图标可固定警报。

- 选择 :::image type="icon" source="media/how-to-work-with-alerts-sensor/unpin-an-alert-icon.png" border="false"::: 图标可取消固定警报。

- 选择 :::image type="icon" source="media/how-to-work-with-alerts-sensor/learn-icon.png" border="false"::: 可审批流量（仅限安全分析人员和管理员）。

- 选择一个设备可在设备映射中显示该设备。

## <a name="next-steps"></a>后续步骤

[管理警报事件](how-to-manage-the-alert-event.md)

[加速警报工作流](how-to-accelerate-alert-incident-response.md)
