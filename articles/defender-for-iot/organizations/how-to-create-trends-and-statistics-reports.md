---
title: 生成趋势和统计信息报告
description: 使用 Defender for IoT 趋势和统计信息小组件深入了解网络活动、统计信息和趋势。
ms.date: 2/21/2021
ms.topic: how-to
ms.openlocfilehash: b4539e20ff485f1b6be69fee8e6849298540adcb
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2021
ms.locfileid: "113015834"
---
# <a name="sensor-trends-and-statistics-reports"></a>传感器趋势和统计信息报告

你可以创建小组件图和饼图来深入了解网络趋势和统计信息。 可以在用户定义的仪表板下对小组件进行分组。

> [!NOTE]
> 管理员和安全分析师可以创建趋势和统计信息报告。

仪表板包含各个小组件，这些小组件以图形方式描述以下类型的信息：

- 按端口显示的流量
- 按端口显示的排名靠前的流量
- 通道带宽
- 总带宽
- 活动 TCP 连接
- 按 VLAN 显示的排名靠前的带宽
- 设备：
  - 新建设备
  - 繁忙的设备
  - 按供应商显示的设备
  - 按 OS 显示的设备
  - 按 VLAN 显示的设备数
  - 断开连接的设备
- 按小时显示的连接断开次数
- 按类型显示的事件警报
- 数据库表访问
- “协议剖析”小组件
- DELTAV
  - DeltaV roc 操作分布
  - 按名称显示的 DeltaV roc 事件
  - 按时间显示的 DeltaV 事件
- AMS
  - 按服务器端口显示的 AMS 流量
  - 按命令显示的 AMS 流量
- 以太网和 IP 地址：
  - 按 CIP 服务显示的以太网和 IP 地址流量
  - 按 CIP 类显示的以太网和 IP 地址流量
  - 按命令显示的以太网和 IP 地址流量
- OPC：
  - OPC 排名靠前的管理操作
  - OPC 排名靠前的 I/O 操作
- Siemens S7：
  - 按控制函数显示的 S7 流量
  - 按子函数显示的 S7 流量
- VLAN
  - 按 VLAN 显示的设备数
  - 按 VLAN 显示的排名靠前的带宽
- 60870-5-104
  - 按 ASDU 显示的 IEC-60870 流量
- BACNET
  - BACnet 服务
- DNP3
  - 按函数显示的 DNP3 流量
- SRTP：
  - 按服务代码显示的 SRTP 流量
  - 按天显示的 SRTP 错误
- SuiteLink：
  - SuiteLink 查询最多的标签
  - SuiteLink 数值标签行为
- 按 ASDU 显示的 IEC-60870 流量
- 按函数显示的 DNP3 流量
- 按服务显示的彩信流量
- 按函数显示的 Modbus 流量
- 按服务显示的 OPC-UA 流量

> [!NOTE]
>  小组件中的时间按传感器时间进行设置。

## <a name="create-reports"></a>创建报表

查看仪表板和小组件：

选择侧边菜单上的“趋势和统计信息”。

:::image type="content" source="media/how-to-generate-reports/investigation-screenshot.png" alt-text="调查的屏幕截图。":::

默认情况下，显示过去七天内的检测结果。 你可以使用筛选工具更改此范围。 例如，自由文本搜索。

## <a name="create-a-dashboard"></a>创建仪表板

选择“仪表板”下拉菜单，创建新的仪表板。 你可以创建任意数量的小组件并将其添加到仪表板。

可使用以下选项创建自定义仪表板：

- 将小组件添加到仪表板

- 从仪表板删除小组件

- 修改小组件的筛选器

- 重设小组件的大小

- 更改小组件的位置

  :::image type="content" source="media/how-to-create-trends-and-statistics-reports/pin-a-dashboard.png" alt-text="更改小组件的位置。":::

创建自定义仪表板：

1. 从左侧面板中选择“趋势和统计信息”。

1. 选择“选择仪表板”下拉菜单，然后选择“创建仪表板”按钮 。

1. 为新仪表板输入一个有意义的名称，然后选择“创建”。

1. 选择页面左上角的“添加小组件”。

  :::image type="content" source="media/how-to-create-trends-and-statistics-reports/widget-store.png" alt-text="从小组件商店中选择小组件。":::

1. 窗口右上角提供了“安全”和“操作”小组件 。 从各种类别和协议中进行选择。 每个小组件都会显示一个简短的说明和一个小图形。 使用滚动条即可查看所有可用的小组件。

1. 使用“单击以添加”按钮选择一个小组件。 该小组件会立即显示在仪表板上。

删除仪表板：

1. 从下拉菜单中选择仪表板的名称。

1. 选择“删除”图标，然后选择”确定” 。

  :::image type="content" source="media/how-to-create-trends-and-statistics-reports/garbage-icon.png" alt-text="选择“删除”图标以删除仪表板。":::

编辑仪表板名称：

1. 从下拉菜单中选择仪表板的名称。

1. 选择“编辑”图标。
  
  :::image type="content" source="media/how-to-create-trends-and-statistics-reports/edit-name.png" alt-text="选择“编辑”图标以编辑仪表板的名称。":::

1. 为仪表板输入新名称，然后选择“保存”。
 
设置默认仪表板：

1. 从下拉菜单中选择仪表板的名称。

1. 选择星形图标，以选择要设置为默认仪表板的仪表板。

   :::image type="content" source="media/how-to-create-trends-and-statistics-reports/default-dashboard.png" alt-text="选择星形图标以选择默认仪表板。"::: 

修改小组件中的筛选数据：

1. 选择“筛选”图标。

  :::image type="content" source="media/how-to-create-trends-and-statistics-reports/filter-widget.png" alt-text="选择筛选图标以设置小组件的参数。":::

1. 编辑所需的参数。

1. 选择“确定”。

删除小组件：

- 选择 :::image type="icon" source="media/how-to-create-trends-and-statistics-reports/x-icon.png" border="false"::: 图标。

  :::image type="content" source="media/how-to-create-trends-and-statistics-reports/delete-widget.png" alt-text="选择 X 以删除小组件。":::

## <a name="creating-widgets"></a>创建小组件 

利用小组件商店，可以按类别和协议选择小组件。 你可以选择可用的“安全”或“操作”小组件来显示它们 。

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/widget-store.png" alt-text="从小组件商店中选择小组件。":::

每个小组件都包含系统流量、网络统计信息、协议统计信息、设备和警报信息的相关详情。 当小组件没有任何数据时，系统会显示一条消息。

你可以在饼图中删除一个部分，以便更清楚地了解其余扇区的相对重要性。 在屏幕底部的图例中选择扇区名称即可实现此目的。

以下各部分提供了几个小组件的用例示例。

### <a name="busy-devices-widget"></a>“繁忙的设备”小组件

此小组件列出最繁忙的 5 个设备。 在“编辑”模式下，可以按已知协议进行筛选。

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/busy-device.png" alt-text="“繁忙的设备”小组件的视图。":::

### <a name="total-bandwidth-widget"></a>“总带宽”小组件

此小组件可跟踪带宽，以每秒兆位 (Mbps) 为单位。 y 轴上显示带宽，x 轴显示日期。 使用“编辑”模式，可以按客户端、服务器、服务器端口或子网来筛选结果。 将光标悬停在图上方时，将显示工具提示。

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/total-bandwidth.png" alt-text="“总带宽”小组件的视图。":::

### <a name="channels-bandwidth-widget"></a>“通道带宽”小组件

此小组件显示流量最多的 5 个通道。 你可以按地址进行筛选，并设置显示的结果数量。 选择向下箭头可显示更多通道。

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/channels-bandwidth.png" alt-text="“通道带宽”小组件的视图。":::

### <a name="traffic-by-port-widget"></a>“按端口显示的流量”小组件

此小组件按端口显示流量，用饼图表示，每个端口用不同的颜色指定。 每个端口的流量与它在饼图中所占的大小成正比。

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/traffic-by-port.png" alt-text="“按端口显示的流量”小组件的视图。":::

### <a name="new-devices-widget"></a>“新设备”小组件

此小组件显示新设备的条形图，指示在特定日期发现了多少个新设备。

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/new-devices.png" alt-text="“新设备”小组件的视图。":::

### <a name="protocol-dissection-widgets"></a>“协议剖析”小组件

此小组件显示一个饼图，可让你查看按功能代码和服务划分的每个协议的流量。 饼图中每个扇区的大小与流量（相对于其他扇区）成正比。

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/protocol-dissection.png" alt-text="“协议剖析”小组件的视图。":::

### <a name="active-tcp-connections-widget"></a>“活动 TCP 连接”小组件

此小组件显示一个图表，其中显示系统中活动的 TCP 连接数。

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/active-tcp.png" alt-text="“活动 TCP 连接”小组件的视图。":::

### <a name="incident-by-type-widget"></a>“按类型显示的事件”小组件

此小组件显示一个饼图，其中按类型显示事件的数量。 这是每个引擎在预定的时间段内生成的警报数。

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/incident-by-type.png" alt-text="“按类型显示的事件”小组件的视图。":::

## <a name="devices-by-vendor-widget"></a>“按供应商显示的设备”小组件

此小组件显示一个饼图，其中按供应商显示设备的数量。 特定供应商的设备数量与该设备的供应商在饼图中所占的大小（相对于其他设备供应商）成正比。

## <a name="number-of-devices-per-vlan-widget"></a>“按 VLAN 显示的设备数”小组件

此小组件显示一个饼图，其中显示发现的设备（按 VLAN 显示）数量。 饼图中每个扇区的大小与发现的设备数（相对于其他扇区）成正比。

每个 VLAN 都将显示由传感器分配的 VLAN 标签或你手动添加的名称。

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/number-of-devices.png" alt-text="设备数量小组件的视图。":::

### <a name="top-bandwidth-by-vlan-widget"></a>“按 VLAN 显示的排名靠前的带宽”小组件

此小组件显示按 VLAN 列出的带宽消耗量。 默认情况下，该小组件显示带宽消耗最高的 5 个 VLAN。

你可以按小组件中显示的时间段来筛选数据。 选择向下箭头可显示更多结果。

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/top-bandwidth.png" alt-text="“按 VLAN 显示的排名靠前的带宽”小组件的视图。":::

## <a name="system-report"></a>系统报告

下载系统报告： 

1. 选择侧边菜单上的“趋势和统计信息”。

1. 选择右上角的“系统报告”。 随即将自动下载报告。

  :::image type="content" source="media/how-to-create-trends-and-statistics-reports/system-report.png" alt-text="选择“系统报告”按钮以下载系统报告的副本。":::

系统报告是包含系统中所有数据的 PDF 文件：

  - 设备

  - 警报

  - 网络策略信息

## <a name="devices-in-a-system-report"></a>系统报告中的设备 

系统报告显示所有设备的列表及其信息。 例如使用的类型、名称和协议。 系统报告还显示按供应商列出的设备列表。

## <a name="alerts-in-system-report"></a>系统报告中的警报 

系统报告显示所有警报的列表及其信息，例如日期和严重性。

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/alerts-report.png" alt-text="系统报告中的警报的视图。":::

## <a name="network-information-in-system-report"></a>系统报告中的网络信息 

系统报告详细显示网络基线。 例如，DNP3 函数代码和每个连接的开放端口。

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/dnp3.png" alt-text="系统报告中的 DNP3 函数视图。":::

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/open-port.png" alt-text="“每个连接的开放端口”报告的视图。":::

## <a name="see-also"></a>另请参阅

[风险评估报告](how-to-create-risk-assessment-reports.md)
[传感器数据挖掘查询](how-to-create-data-mining-queries.md)
[攻击途径报告](how-to-create-attack-vector-reports.md)
