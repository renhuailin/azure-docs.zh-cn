---
title: 了解传感器警报
description: 使用警报来帮助增强网络的安全性和操作。
ms.date: 3/29/2021
ms.topic: how-to
ms.openlocfilehash: 353f7fb0da85a0ffa737dc5c4afa70763b2fb185
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2021
ms.locfileid: "113015400"
---
# <a name="about-sensor-alerts"></a>关于传感器警报

警报可帮助你增强网络的安全性和操作。 警报提供以下方面的信息：

- 与已授权网络活动之间的偏差

- 协议和操作异常

- 可疑的恶意软件流量

:::image type="content" source="media/how-to-work-with-alerts-sensor/address-scan-detected-screen.png" alt-text="检测地址扫描。":::

警报管理选项可让用户执行以下操作：

- 指示传感器获知检测为已授权流量的活动。

- 确认审查警报。

- 指示传感器屏蔽在相同设备和类似流量上检测到的事件。

还有其他工具可帮助你增强和加速警报调查。 例如：

  - 为警报审查者添加说明注释。

  - 创建要在 SOC 解决方案中显示的警报组。 

  - 搜索特定的警报；审查相关的 PCAP 文件；在设备地图中查看检测到的设备和其他已连接设备，或者将警报详细信息发送到合作伙伴系统。

  - 将警报转发到合作伙伴供应商：SIEM 系统、MSSP 系统等。

## <a name="alerts-and-engines"></a>警报和引擎

当传感器引擎检测到网络流量和行为出现了需要引以关注的变化时，会触发警报。 本文将介绍每个引擎触发的警报类型。

| 警报类型 | 描述 |
|-|-|
| 策略违反警报 | 当策略违反引擎检测到与以前获知的流量有偏差时触发。 例如： <br /> - 检测到新设备。  <br /> - 在设备上检测到新的配置。 <br /> - 未定义为编程设备的设备执行编程更改。 <br /> - 固件版本已更改。 |
| 协议违反警报 | 当协议冲突引擎检测到不符合协议规范的数据包结构或字段值时触发。 | 
| 操作警报 | 当操作引擎检测到网络操作事件或设备故障时触发。 例如，通过 Stop PLC 命令停止网络设备，或者传感器上的接口停止监视流量。 |
| 恶意软件警报 | 当恶意软件引擎检测到恶意网络活动时触发。 例如，引擎检测到 Conficker 等已知攻击。 |
| 异常警报 | 当异常引擎检测到偏差时触发。 例如，设备正在执行网络扫描，但未定义为扫描设备。 |

可以使用工具来启用和禁用传感器引擎。 不会从已禁用的引擎触发警报。 请参阅[控制要监视的流量](how-to-control-what-traffic-is-monitored.md)。

## <a name="alerts-and-sensor-reporting"></a>警报和传感器报告

可以在生成数据挖掘、风险评估和攻击途径报告时计算警报中反映的活动。 当你管理这些事件时，传感器会相应地更新报告。

例如：

  - 定义的子网中的设备与位于子网外部（公共）的设备之间的未授权连接将显示在数据挖掘的“Internet 活动”报告和风险评估的“Internet 连接”部分 。 在授权（获知）这些设备后，会在生成这些报告时计算这些设备。

  - 在网络设备上检测到的恶意软件事件将在“风险评估”报告中报告。 如果屏蔽了有关恶意软件事件的警报，则不会在“风险评估”报告中计算受影响的设备。

## <a name="next-steps"></a>后续步骤

[学习和智能 IT 学习模式](how-to-control-what-traffic-is-monitored.md#learning-and-smart-it-learning-modes)
[查看警报中提供的信息](how-to-view-information-provided-in-alerts.md)
[管理警报事件](how-to-manage-the-alert-event.md)
[加速警报工作流](how-to-accelerate-alert-incident-response.md)
[警报类型和描述](alert-engine-messages.md)
