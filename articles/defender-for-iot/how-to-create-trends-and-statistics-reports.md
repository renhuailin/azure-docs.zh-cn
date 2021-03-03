---
title: 生成趋势和统计信息报告
description: 使用用于 IoT 趋势和统计小组件的 Defender 深入了解网络活动、统计信息和趋势。
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 2/21/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 7cb3848f3711b6bf10c316ba4f5321286e2260fb
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101706335"
---
# <a name="sensor-trends-and-statistics-reports"></a>传感器趋势和统计信息报告

你可以创建小组件图和饼图来深入了解网络趋势和统计信息。 小组件可以在用户定义的仪表板下进行分组。

> [!NOTE]
> 管理员和安全分析师可以创建趋势和统计报告。

该仪表板包含小组件，这些小组件以图形方式描述以下类型的信息：

- 按端口排列的流量
- 按端口排列的排名靠前的流量
- 通道带宽
- 总带宽
- 活动 TCP 连接
- 通过 VLAN 的热门带宽
- 设备：
  - 新建设备
  - 繁忙设备
  - 按供应商的设备
  - 按 OS 的设备
  - 每个 VLAN 的设备数
  - 断开连接的设备
- 连接丢弃时间（小时）
- 事件的警报（按类型）
- 数据库表访问
- 协议剖析小组件
- DELTAV
  - DeltaV roc 操作分发
  - 按名称 DeltaV roc 事件
  - 按时间 DeltaV 事件
- AMS
  - 按服务器端口的 AMS 流量
  - 通过命令进行 AMS 流量
- 以太网和 IP 地址：
  - CIP 服务的以太网和 IP 地址流量
  - 通过 CIP 类的以太网和 IP 地址流量
  - 以太网和 IP 地址流量（按命令）
- OPC
  - OPC 顶层管理操作
  - OPC top i/o 操作
- Siemens S7：
  - 按 control 函数的 S7 流量
  - 按 subfunction S7 流量
- VLAN
  - 每个 VLAN 的设备数
  - 通过 VLAN 的热门带宽
- 60870-5-104
  - IEC-60870 流量 by ASDU
- BACNET
  - BACnet 服务
- DNP3
  - 按功能的 DNP3 流量
- SRTP:
  - 按服务代码 SRTP 流量
  - 按天列出的 SRTP 错误
- SuiteLink:
  - SuiteLink 查询排在最前面的标记
  - SuiteLink 数值标记行为
- IEC-60870 流量 by ASDU
- 按功能的 DNP3 流量
- 按服务的彩信流量
- 按功能 Modbus 流量
- OPC-按服务的 UA 流量

> [!NOTE]
>  根据传感器时间设置小组件中的时间。

## <a name="create-reports"></a>创建报表

若要查看仪表板和小组件：

选择侧栏菜单上的 " **趋势 & 统计信息** "。

:::image type="content" source="media/how-to-generate-reports/investigation-screenshot.png" alt-text="调查的屏幕截图。":::

默认情况下，在过去七天内显示检测结果。 您可以使用筛选器工具更改此范围。 例如，一个自由文本搜索。

## <a name="create-a-dashboard"></a>创建仪表板

通过选择 " **仪表板** " 下拉菜单创建新的仪表板。 你可以创建任意数量的小组件并将其添加到仪表板。

可以使用以下选项创建自定义仪表板：

- 将小组件添加到仪表板

- 从仪表板删除小组件

- 修改小组件的筛选器

- 调整小组件的大小

- 更改小组件的位置

  :::image type="content" source="media/how-to-create-trends-and-statistics-reports/pin-a-dashboard.png" alt-text="更改小组件的位置。":::

若要创建自定义仪表板：

1. 从左侧面板中选择 " **趋势和统计信息** "。

1. 选择 " **选择仪表板** " 下拉菜单，然后选择 " **创建仪表板** " 按钮。

1. 为新仪表板输入有意义的名称，并选择 " **创建**"。

1. 选择页面左上角的 " **添加小组件** "。

  :::image type="content" source="media/how-to-create-trends-and-statistics-reports/widget-store.png" alt-text="从小组件存储区中选择小组件。":::

1. 在窗口的右上角提供了 **安全性** 和 **操作** 小组件。 从各种类别和协议中进行选择。 每个小组件都将显示一个具有微型图形的简短说明。 使用滚动条可以查看所有可用的小组件。

1. 使用 " **单击以添加** " 按钮选择一个小组件。 小组件会立即显示在仪表板上。

删除仪表板：

1. 从下拉菜单中选择仪表板的名称。

1. 选择 " **删除** " 图标，然后选择 **"确定"**。

  :::image type="content" source="media/how-to-create-trends-and-statistics-reports/garbage-icon.png" alt-text="选择 &quot;删除&quot; 图标以删除仪表板。":::

编辑仪表板名称：

1. 从下拉菜单中选择仪表板的名称。

1. 选择 " **编辑** " 图标。
  
  :::image type="content" source="media/how-to-create-trends-and-statistics-reports/edit-name.png" alt-text="选择 &quot;编辑&quot; 图标以编辑仪表板的名称。":::

1. 输入仪表板的新名称，然后选择 " **保存**"。
 
设置默认仪表板：

1. 从下拉菜单中选择仪表板的名称。

1. 选择 **星形** 图标以选择要设置为默认仪表板的仪表板。

   :::image type="content" source="media/how-to-create-trends-and-statistics-reports/default-dashboard.png" alt-text="选择星形图标以选择你的默认仪表板。"::: 

修改小组件中的筛选数据：

1. 选择 " **筛选器** " 图标。

  :::image type="content" source="media/how-to-create-trends-and-statistics-reports/filter-widget.png" alt-text="选择 &quot;筛选器&quot; 图标以设置小组件的参数。":::

1. 编辑所需的参数。

1. 选择“确定”。

删除小组件：

- 选择该 :::image type="icon" source="media/how-to-create-trends-and-statistics-reports/x-icon.png" border="false"::: 图标。

  :::image type="content" source="media/how-to-create-trends-and-statistics-reports/delete-widget.png" alt-text="选择 X 以删除小组件。":::

## <a name="creating-widgets"></a>创建小组件 

小组件存储允许你按类别和协议选择小组件。 可以通过选择 " **安全**" 或 " **操作** " 小组件来显示它们。

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/widget-store.png" alt-text="从小组件商店中选择你的小组件。":::

每个小组件都包含有关系统流量、网络统计信息、协议统计信息、设备和警报信息的具体信息。 小组件没有数据时，会显示一条消息。

您可以在饼图中从饼上删除一个部分，以更清楚地查看剩余切片的相对重要性。 在屏幕底部的图例中选择切片的名称以执行此操作。

以下各部分提供了几个小组件的用例示例。

### <a name="busy-devices-widget"></a>繁忙设备小组件

此小组件列出最繁忙的5个设备。 在 **编辑** 模式下，可以按已知协议进行筛选。

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/busy-device.png" alt-text="繁忙设备小组件的视图。":::

### <a name="total-bandwidth-widget"></a>总带宽小组件

此小组件按 Mbps (每秒兆位) 的带宽进行跟踪。 带宽在 y 轴上指示，日期显示在 x 轴上。 **编辑** 模式允许根据客户端、服务器、服务器端口或子网来筛选结果。 将光标悬停在图表上时，将显示工具提示。

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/total-bandwidth.png" alt-text="总带宽小组件的视图。":::

### <a name="channels-bandwidth-widget"></a>通道带宽小组件

此小组件显示前5个流量通道。 可以按地址进行筛选，并设置显示的结果数。 选择向下箭头以显示更多通道。

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/channels-bandwidth.png" alt-text="通道带宽小组件的视图。":::

### <a name="traffic-by-port-widget"></a>按端口小组件排列的流量

此小组件按端口显示流量，其中的每个端口由不同的颜色指定。 每个端口的流量与饼图部分的大小成正比。

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/traffic-by-port.png" alt-text="按端口小组件显示的流量视图。":::

### <a name="new-devices-widget"></a>新设备小组件

此小组件显示新的设备条形图，指示在特定日期发现了多少个新设备。

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/new-devices.png" alt-text="新设备小组件的视图。":::

### <a name="protocol-dissection-widgets"></a>协议剖析小组件

此小组件显示一个饼图，其中提供了每个协议的流量、细化 by 函数代码和服务。 饼图每个扇区的大小与相对于其他切片的流量成正比。

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/protocol-dissection.png" alt-text="协议剖析小组件的视图。":::

### <a name="active-tcp-connections-widget"></a>活动 TCP 连接小组件

此小组件显示一个图表，该图表显示系统中活动的 TCP 连接数。

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/active-tcp.png" alt-text="活动 TCP 连接小组件的视图。":::

### <a name="incident-by-type-widget"></a>事件（按类型小组件）

此小组件显示按类型列出事件数量的饼图。 这是在预定义的时间段内每个引擎生成的警报数。

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/incident-by-type.png" alt-text="事件的视图（按类型小组件）。":::

## <a name="devices-by-vendor-widget"></a>设备（按供应商小组件）

此小组件显示按供应商显示的设备数的饼图。 特定供应商的设备数量与该设备的供应商部件在磁盘中相对于其他设备供应商的大小成正比。

## <a name="number-of-devices-per-vlan-widget"></a>每个 VLAN 小组件的设备数

此小组件显示一个饼图，其中显示每个 VLAN 发现的设备数。 饼图每个扇区的大小与发现的设备相对于其他切片的数量成正比。

将显示每个 VLAN，其中包含由您手动添加的传感器或名称分配的 VLAN 标记。

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/number-of-devices.png" alt-text="设备小组件数量的视图。":::

### <a name="top-bandwidth-by-vlan-widget"></a>按 VLAN 小组件列出的热门带宽

此小组件显示 VLAN 消耗的带宽。 默认情况下，该小组件显示5个具有最高带宽的 Vlan。

你可以按小组件中显示的时间段来筛选数据。 选择向下箭头以显示更多结果。

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/top-bandwidth.png" alt-text="按 VLAN 小组件列出的最大带宽的视图。":::

## <a name="system-report"></a>系统报告

下载系统报告： 

1. 选择侧栏菜单上的 " **趋势 & 统计信息** "。

1. 在右上角选择 " **系统报表** "。 报表将自动下载。

  :::image type="content" source="media/how-to-create-trends-and-statistics-reports/system-report.png" alt-text="选择 &quot;系统报告&quot; 按钮以下载系统报告的副本。":::

系统报告是包含系统中所有数据的 PDF 文件：

  - 设备

  - 警报

  - 网络策略信息

## <a name="devices-in-a-system-report"></a>系统报表中的设备 

系统报表显示所有设备及其信息的列表。 例如，使用类型、名称和协议。 系统报告还显示每个供应商的设备列表。

## <a name="alerts-in-system-report"></a>系统报表中的警报 

系统报表显示所有警报的列表，其中包含其信息，例如日期和严重性。

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/alerts-report.png" alt-text="系统报表上警报的视图。":::

## <a name="network-information-in-system-report"></a>系统报告中的网络信息 

系统报告详细显示网络基线。 例如，DNP3 函数代码和每个连接的开放端口。

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/dnp3.png" alt-text="系统报表中的 DNP3 函数视图。":::

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/open-port.png" alt-text="每个连接的打开端口报表的视图。":::

## <a name="see-also"></a>另请参阅

[风险评估报告](how-to-create-risk-assessment-reports.md) 
[传感器数据挖掘查询](how-to-create-data-mining-queries.md) 
[攻击向量报告](how-to-create-attack-vector-reports.md)
