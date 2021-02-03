---
title: 管理警报事件
description: 管理在网络中检测到的警报事件。
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/07/2020
ms.service: azure
ms.topic: how-to
ms.openlocfilehash: c0670f37da0cead5e3bd05a1d69e17191e8c0ccf
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/03/2021
ms.locfileid: "99508737"
---
# <a name="manage-alert-events"></a>管理警报事件

以下选项可用于管理警报事件：

 | 操作 | 说明 |
 |--|--|
 | **Learn** | 授权检测到的事件。 有关详细信息，请参阅 [关于学习和 unlearning 事件](#about-learning-and-unlearning-events)。 |
 | **认可** | 针对检测到的事件隐藏一次警报。 如果再次检测到该事件，将再次触发此警报。 有关详细信息，请参阅 [关于确认和 unacknowledging 事件](#about-acknowledging-and-unacknowledging-events)。 |
 | **静音** | 连续忽略具有相同设备和流量的活动。 有关详细信息，请参阅 [关于静音和将事件](#about-muting-and-unmuting-events)。 |

## <a name="about-learning-and-unlearning-events"></a>关于学习和 unlearning 事件

指示网络的偏差的事件可能反映出有效的网络更改。 示例可能包括一个已加入网络的新授权设备或授权的固件更新。

选择 " **了解**" 时，传感器会将流量、配置或活动视为有效。 这意味着不会再针对事件触发警报。 这也意味着传感器生成风险评估、攻击向量和其他报告时不会计算该事件。

例如，你将收到一个警报，指示网络扫描程序之前未定义的设备上的地址扫描活动。 如果此设备已添加到网络以进行扫描，则可以指示传感器将设备视为扫描设备。

:::image type="content" source="media/how-to-work-with-alerts-sensor/detected.png" alt-text="&quot;检测到的地址&quot; 扫描窗口。":::

了解的事件可以是 unlearned。 当传感器 unlearns 事件时，它将重新触发与此事件相关的警报。

## <a name="about-acknowledging-and-unacknowledging-events"></a>关于确认和 unacknowledging 事件

在某些情况下，你可能不希望传感器了解检测到的事件，或者该选项可能不可用。 相反，事件可能需要缓解。 例如：

- **缓解网络配置或设备**：收到一个警报，指示在网络上检测到新的设备。 调查时，发现设备是未授权的网络设备。 通过断开设备与网络的连接来处理事件。
- **更新传感器配置**：收到一个警报，指示服务器启动了过多的远程连接。 触发此警报的原因是，传感器异常阈值定义为在一分钟内触发特定数量的会话上的警报。 可以通过更新阈值来处理事件。

执行缓解或调查后，可以通过选择 " **确认**" 来指示传感器隐藏警报。 如果再次检测到该事件，将 retriggered 警报。

清除警报：

  - 选择 " **确认**"。

若要再次查看警报：

  - 访问警报并选择 " **Unacknowledge**"。

如果需要进一步调查，Unacknowledge 警报。

## <a name="about-muting-and-unmuting-events"></a>关于静音和将事件

在某些情况下，可能需要指示传感器忽略网络上的特定方案。 例如：

  - **异常** 引擎在两个设备之间的带宽高峰触发警报，但高峰对于这些设备是有效的。

  - **协议冲突** 引擎触发两个设备之间检测到的协议偏差发出的警报，但两个设备之间的偏差是有效的。

在这些情况下，学习功能不可用。 当学习无法执行，并且你想要在计算风险和攻击向量时禁止显示警报并删除设备时，可以改为对警报事件进行静音。

> [!NOTE] 
> 无法将 internet 设备定义为源或目标的事件静音。

### <a name="what-traffic-is-muted"></a>什么是静音的流量？

一种静音的方案包括网络设备和检测到事件的流量。 警报标题描述要静音的流量。

要静音的设备将显示为警报中的图像。 如果显示两个设备，则会将其之间的特定警报流量静音。

**示例 1**

当某个事件处于静音时，只要主 (源) 将辅助 (目标发送) 供应商定义的非法函数代码，就会忽略此事件。

:::image type="content" source="media/how-to-work-with-alerts-sensor/secondary-device-connected.png" alt-text="辅助设备已收到。":::

**示例 2**

当某个事件处于静音时，无论目标如何，只要源发送包含非法内容的 HTTP 标头，就会忽略此事件。

:::image type="content" source="media/how-to-work-with-alerts-sensor/illegal-http-header-content.png" alt-text="非法 HTTP 标头内容的屏幕截图。":::

**在事件静音后：**

- 在 unmuted 之前，可以在 **确认** 的警报视图中访问该警报。

- "静音" 操作将显示在 **事件时间线** 中。

  :::image type="content" source="media/how-to-work-with-alerts-sensor/muted-event-notification-screenshot.png" alt-text="检测到事件并静音。":::

- 传感器将在生成风险评估、攻击向量和其他报告时重新计算设备。 例如，如果您在设备上静音了检测到恶意流量的警报，则不会在风险评估报表中计算此设备。

**若要为警报静音和取消静音：**

- 选择警报上的 **静音** 图标。

**查看静音警报：**

1. 选择 " **确认** " 选项，然后选择 " **警报** " 主屏幕。

2. 将鼠标悬停在警报上方，查看其是否已静音。  

## <a name="see-also"></a>另请参阅

[控制要监视的流量](how-to-control-what-traffic-is-monitored.md)
