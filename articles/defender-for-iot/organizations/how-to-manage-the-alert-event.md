---
title: 管理警报事件
description: 管理网络中检测到的警报事件。
ms.date: 05/26/2021
ms.topic: how-to
ms.openlocfilehash: 57d6a2225cc5380d8c1bf4be0955c1b728501cc1
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2021
ms.locfileid: "113015649"
---
# <a name="manage-alert-events"></a>管理警报事件

下列选项可用于管理警报事件：

 | 操作 | 说明 |
 |--|--|
 | **Learn** | 对检测到的事件授权。 有关详细信息，请查看[关于学习和取消学习事件](#about-learning-and-unlearning-events)。 |
 | **确认** | 对检测到的事件隐藏一次警报。 如果再次检测到该事件，将再次触发此警报。 有关详细信息，请查看[关于确认和取消确认事件](#about-acknowledging-and-unacknowledging-events)。 |
 | **静音** | 持续忽略具有相同设备和可比流量的活动。 有关详细信息，请查看[关于屏蔽和取消屏蔽事件](#about-muting-and-unmuting-events)。 |
 
你还可导出警报信息。
## <a name="about-learning-and-unlearning-events"></a>关于学习和取消学习事件

指示与已学习的网络存在偏差的事件可能反映了有效的网络更改。 如果要批准这些更改，可以指示 Defender for IoT 来学习行为。 示例可能包括：

- 在现有设备上检测到新活动。 例如，授权设备尝试访问另一台设备上的新资源。

- 新设备被添加到网络中。  

- 固件版本更改遵循标准维护过程。

- 新设备在目标控制器上执行了读/写操作。

- 新设备在目标控制器上执行读/写操作，并且应被定义为编程设备。

- 执行新的合法扫描，设备应被定义为扫描设备。

若要了解某个事件，请执行以下操作： 

1. 导航到“警报”选项卡。

    :::image type="content" source="media/how-to-work-with-alerts-sensor/alerts-tab.png" alt-text="从屏幕左侧的导航栏中选择“警报”选项卡。":::

1. 从警报列表中选择警报。

1. 选择“了解”。

    :::image type="content" source="media/how-to-work-with-alerts-sensor/learn.png" alt-text="检测到的地址扫描窗口。":::

在你生成数据挖掘、风险评估和攻击途径报告时计算警报中反映的活动。 当你管理这些事件时，传感器会相应地更新报告。

选择“学习”时，传感器会将流量、配置或活动视作有效。 该事件将不再触发警报。 这也意味着在传感器生成风险评估、攻击途径和其他报告时不会计算事件。

已学习的事件可以被忘记。 传感器取消学习事件时，会重新触发与此事件相关的警报。

取消了解某个事件的步骤

1. 导航到“警报”选项卡。

    :::image type="content" source="media/how-to-work-with-alerts-sensor/alerts-tab.png" alt-text="从屏幕左侧的导航栏中选择“警报”选项卡。":::

1. 从“视图”下拉菜单中，选择“已确认”。

    :::image type="content" source="media/how-to-work-with-alerts-sensor/view-acknowledged.png" alt-text="从视图部分的下拉菜单中选择“已确认”。" lightbox="media/how-to-work-with-alerts-sensor/view-acknowledged-expanded.png":::

1. 从警报列表中选择警报。

1. 选择“取消了解”。

    :::image type="content" source="media/how-to-work-with-alerts-sensor/unlearn.png" alt-text="选择“取消了解”以取消了解某个事件。":::

事件被取消了解后，它会移回到“主视图”页。

若要查看某个警报是否已了解或已确认，请执行以下操作：

1. 导航到“事件时间线”选项卡 :::image type="icon" source="media/how-to-work-with-alerts-sensor/event-timeline.png" border="false":::

1. 在时间线中查找警报。

    :::image type="content" source="media/how-to-work-with-alerts-sensor/event-timeline-acknowledged.png" alt-text="可以在事件时间线中查找已确认的事件。":::

    你会在警报的窗口中看到警报是否已了解或已确认。
 
## <a name="about-acknowledging-and-unacknowledging-events"></a>关于确认和取消确认事件

在某些情况下，你可能不希望传感器学习检测到的事件，或者该选项可能不可用。 而事件可能需要缓解措施。 例如：

- **缓解网络配置或设备**：你收到一个警报，它指示在网络上检测到一台新设备。 进行调查时，你发现该设备是未经授权的网络设备。 你可断开设备与网络的连接来处理该事件。
- **更新传感器配置**：你收到一个警报，它指示服务器启动了过多的远程连接。 触发此警报的原因是根据定义，传感器异常阈值会在 1 分钟内触发超过特定会话数量的警报。 你可通过更新阈值来处理该事件。

执行缓解或调查后，可选择“确认”来指示传感器隐藏警报。 如果再次检测到该事件，将再次触发此警报。

若要清除警报：

  - 选择“确认”。

若要再次查看警报：

  - 访问警报并选择“取消确认”。

如果需要进一步调查，请取消确认警报。

## <a name="about-muting-and-unmuting-events"></a>关于屏蔽和取消屏蔽事件

在某些情况下，你可能希望指示传感器忽略网络上的特定方案。 例如：

  - “异常”引擎对两台设备之间的带宽峰值触发警报，但峰值对这些设备有效。

  - “协议冲突”引擎对两台设备之间检测到的协议偏差触发警告，但设备之间的偏差是有效的。

在这些情况下，不可进行学习。 如果无法进行学习，而且你想要在计算风险和攻击途径时取消显示警报并删除设备，那么你可将警报事件屏蔽。

> [!NOTE] 
> 无法将 Internet 设备定义为源或目标的事件屏蔽。

### <a name="what-alert-activity-is-muted"></a>已屏蔽哪种警报活动？

已屏蔽的场景包括就事件检测到的网络设备和流量。 警报的标题描述了被屏蔽的流量。

被屏蔽的设备将在警报中显示为图像。 如果显示两台设备，则它们之间的特定警报流量将被屏蔽。

**示例 1**

如果事件被屏蔽，则每当主设备（源）向辅助设备（目标）发送由供应商定义的非法函数代码时，事件都会被忽略。

:::image type="content" source="media/how-to-work-with-alerts-sensor/secondary-device-connected.png" alt-text="收到的辅助设备。":::

**示例 2**

如果事件被屏蔽，则每当源设备发送包含非法内容的 HTTP 标头时，无论目标设备是什么，事件都会被忽略。

:::image type="content" source="media/how-to-work-with-alerts-sensor/illegal-http-header-content.png" alt-text="非法 HTTP 标头内容的屏幕截图。":::

**事件被屏蔽后：**

- 警报在被取消屏蔽之前，可在“已确认”警报视图中访问警报。

- 屏蔽操作将显示在“事件时间线”中。

  :::image type="content" source="media/how-to-work-with-alerts-sensor/muted-event-notification-screenshot.png" alt-text="检测到但已屏蔽的事件。":::

- 传感器将在生成风险评估、攻击途径和其他报告时重新计算设备。 例如，如果屏蔽在设备上检测到恶意流量的警报，则不会在风险评估报告中计算该设备。

**若要屏蔽和取消屏蔽某警报：**

- 选择警报上的“屏蔽”图标。

**若要查看已屏蔽的警报：**

1. 在“警报”主屏幕中选择“已确认”选项 。

2. 将鼠标悬停在警报上方，查看警报是否被屏蔽。  

## <a name="export-alert-information"></a>导出警报信息

将警报信息导出到 .csv 文件。 可以导出检测到的所有警报的信息或导出基于筛选视图的信息。 导出的信息如下：

- 源地址
- 目标地址
- 警报标题
- 警报严重性
- 警报消息
- 其他信息
- “已确认”状态
- PCAP 可用性

若要导出：

1. 在边侧菜单中选择“警报”。

1. 选择“导出”  。

1. 选择“导出扩展警报”，将涉及多个设备的每条警报的警报信息导出到单独的行中。 选择“导出扩展警报”后，.csv 文件将创建警报事件的重复行，但每行包含非重复项。 使用此选项可以更方便地调查导出的警报事件。

## <a name="see-also"></a>另请参阅

[警报类型和说明](alert-engine-messages.md)

[控制要监视的流量](how-to-control-what-traffic-is-monitored.md)
