---
title: 处理传感器的警报
description: 使用警报来帮助你增强网络的安全性和操作。
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 11/30/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 00207ffb8480ae99c2f1aad74183fca9ea45ee17
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/31/2020
ms.locfileid: "97838351"
---
# <a name="work-with-alerts-on-your-sensor"></a>处理传感器的警报

使用警报来帮助你增强网络的安全性和操作。 警报提供以下相关信息：

- 与授权网络活动的偏差

- 协议和操作异常

- 可疑的恶意软件流量

:::image type="content" source="media/how-to-work-with-alerts-sensor/address-scan-detected-screen.png" alt-text="检测地址扫描。":::

警报管理选项允许用户：

- 指示传感器了解作为授权流量检测到的活动。

- 确认检查警报。

- 指示传感器使检测到的事件与相同的设备和可比较的流量进行静音。

还提供了其他工具，可帮助你增强和加速警报调查。 例如：

  - 添加警报审阅者的说明注释。

  - 创建要在 SOC 解决方案中显示的警报组。 

  - 搜索特定警报;查看相关的 PCAP 文件;在设备映射中查看检测到的设备和其他连接的设备，或将警报详细信息发送到合作伙伴系统。

  - 将警报转发给合作伙伴供应商： SIEM 系统、MSSP 系统等。

## <a name="alerts-and-engines"></a>警报和引擎

当传感器引擎检测到需要关注的网络流量和行为变化时，会触发警报。 本文介绍每个引擎触发的警报类型。

| 警报类型 | 说明 |
|-|-|
| 策略冲突警报 | 当策略冲突引擎检测到以前获知的流量的偏差时触发。 例如： <br /> -检测到新设备。  <br /> -在设备上检测到新的配置。 <br /> -未定义为编程设备的设备将执行编程更改。 <br /> -固件版本已更改。 |
| 协议冲突警报 | 在协议冲突引擎检测到不符合协议规范的数据包结构或字段值时触发。 | 
| 操作警报 | 当操作引擎检测到网络操作事件或设备出现故障时触发。 例如，通过 Stop PLC 命令停止网络设备，或者传感器上的接口停止监视流量。 |
| 恶意软件警报 | 当恶意软件引擎检测到恶意网络活动时触发。 例如，引擎检测到已知的攻击，如 Conficker。 |
| 异常警报 | 异常引擎检测到偏差时触发。 例如，设备正在执行网络扫描，但未定义为扫描设备。 |

工具可用于启用和禁用传感器引擎。 不会从已禁用的引擎触发警报。 请参阅 [控制要监视的流量](how-to-control-what-traffic-is-monitored.md)。

## <a name="alerts-and-sensor-reporting"></a>警报和传感器报告

生成数据挖掘、风险评估和攻击向量报告时，可以计算警报中反映的活动。 当你管理这些事件时，传感器会相应地更新报表。

例如：

  - 在定义的子网中的设备与位于子网外部的设备之间的未经授权的连接 (公共) 将显示在数据挖掘 *Internet 活动* 报告和风险评估 *internet 连接* 部分。 在对这些设备进行授权后 () 了解后，它们会在生成这些报告时进行计算。

  - 在风险评估报表中报告网络设备上检测到的恶意软件事件。 如果 *静音* 了有关恶意软件事件的警报，则不会在风险评估报表中计算受影响的设备。

## <a name="see-also"></a>另请参阅

- [学习和智能 IT 学习模式](how-to-control-what-traffic-is-monitored.md#learning-and-smart-it-learning-modes)
- [查看警报中提供的信息](how-to-view-information-provided-in-alerts.md)
- [管理警报事件](how-to-manage-the-alert-event.md)
- [加速警报工作流](how-to-accelerate-alert-incident-response.md)
