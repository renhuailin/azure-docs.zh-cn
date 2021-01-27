---
title: 生成趋势和统计信息报告
description: 使用用于 IoT 趋势和统计小组件的 Defender 深入了解网络活动、统计信息和趋势。
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 01/24/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 8bfbacc77843c7978bcb1d364bd93f5e09381144
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/26/2021
ms.locfileid: "98811609"
---
# <a name="sensor-trends-and-statistics-reports"></a>传感器趋势和统计信息报告

## <a name="about-sensor-trends-and-statistics-reports"></a>关于传感器趋势和统计信息报告

你可以创建小组件图和饼图来深入了解网络趋势和统计信息。 小组件可以在用户定义的仪表板下进行分组。

> [!NOTE]
> 管理员和安全分析师可以创建趋势和统计报告。

该仪表板包含小组件，这些小组件以图形方式描述以下类型的信息：

- 按端口排列的流量
- 通道带宽
- 总带宽
- 活动 TCP 连接
- 设备：
  - 新建设备
  - 繁忙设备
  - 按供应商的设备
  - 按 OS 的设备
  - 断开连接的设备
- 连接丢弃时间
- 事件的警报（按类型）
- 数据库表访问
- 协议剖析小组件
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

默认情况下，在过去7天内显示检测结果。 您可以使用筛选器工具更改此范围。 例如，自由文本搜索。

## <a name="see-also"></a>另请参阅

[风险评估报告](how-to-create-risk-assessment-reports.md) 
[传感器数据挖掘查询](how-to-create-data-mining-queries.md) 
[攻击向量报告](how-to-create-attack-vector-reports.md)