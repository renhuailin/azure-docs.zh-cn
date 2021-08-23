---
title: 处理本地管理控制台上的警报
description: 使用本地管理控制台获取网络中最近受到的威胁的企业视图，并更好地了解传感器用户如何应对这些威胁。
ms.date: 07/13/2021
ms.topic: how-to
ms.openlocfilehash: 2766776083eaeb96f6a1d577b160d0cdbe9eb3d0
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114466232"
---
# <a name="work-with-alerts-on-the-on-premises-management-console"></a>处理本地管理控制台上的警报 

可以在管理控制台的“警报”窗口中执行以下操作：

- 使用警报筛选器

- 使用警报计数器

- 查看警报信息

- 管理警报事件

- 创建警报排除规则

- 从外部系统触发警报排除规则

- 使用警报组加速事件工作流

## <a name="view-alerts-in-the-on-premises-management-console"></a>在本地管理控制台中查看警报

本地管理控制台聚合了来自所有已连接的传感器的警报。 因此，它提供了网络中最近受到的威胁的企业视图，并可帮助你更好地了解传感器用户如何应对这些威胁。

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/alerts-with-samples.png" alt-text="“警报”窗口的屏幕截图。":::

### <a name="work-with-alert-filters"></a>使用警报筛选器

“警报”窗口显示已连接到本地管理控制台的传感器生成的警报。 可以查看已连接的传感器的所有警报，或者显示从以下特定位置发送的警报：

- 站点

- 区域

- 设备

- 传感器

选择“清除筛选器”可查看所有警报。

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/clear-filters.png" alt-text="通过选择“清除筛选器”按钮来清除筛选器。":::

### <a name="work-with-alert-counters"></a>使用警报计数器

警报计数器按严重性和已确认状态提供警报明细。

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/number-of-alerts.png" alt-text="警报计数器显示收到了多少条警报。":::

警报计数器中显示以下严重级别：

- 严重：指示应立即处理的恶意攻击。

- 重大：指示非常有必要解决的安全威胁。

- 轻微：指示出现了与基线行为有所偏离的行为，它有可能存在安全威胁。

- 警告：指示出现了与基线行为有所偏离的行为，但它没有安全威胁。

严重级别是预定义的。

可以调整计数器，以根据已确认和未确认的警报提供数字。 未确认的警报是在 Defender for IoT 传感器上触发的，但操作员尚未在传感器上检查这些警报。

选择“显示已确认的警报”选项后，所有已确认的警报将显示在“警报”窗口中 。

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/show-acknowledged-alerts.png" alt-text="查看已确认的警报。":::

### <a name="view-alert-information"></a>查看警报信息

该警报显示以下信息：

- 警报事件的摘要。

- 警报严重性。

- 检测到该事件的传感器中警报的链接。

- 警报 UUID。 该 UUID 包含与传感器上检测到的警报事件关联的警报 ID，使用连字符与后面的独一无二的系统 ID 号隔开。

**本地管理控制台警报 UUID**

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/unauthorized-internet-connectivity.png" alt-text="设备已连接，但尚未获得授权。":::

**传感器警报 ID**

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/internet-connectivity-alert.png" alt-text="传感器警报 ID。":::

使用 UUID 能够确保可按唯一的编号搜索和识别本地管理控制台中显示的每个警报。 之所以必须这样做，是因为多个传感器生成的警报有可能生成相同的警报 ID。

> [!NOTE]
> 默认情况下，如果定义了转发规则，UUID 将显示在以下合作伙伴系统中：ArcSight、syslog 服务器、QRadar、Sentinel 和 NetWitness。 无需设置。

若要查看警报信息，请执行以下操作：

- 在警报列表中选择一个警报。

  :::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/alert-information.png" alt-text="警报信息的屏幕截图。":::

若要查看传感器中的警报，请执行以下操作：

- 在警报中选择“打开传感器”。

若要查看区域地图中的设备，请执行以下操作：

- 若要查看焦点置于已发生警报的设备上并且显示了与该设备连接的所有设备的设备地图，请选择“显示设备”。

## <a name="manage-alert-events"></a>管理警报事件

在本地管理控制台中可以使用多个选项来管理警报事件。

- 了解或确认警报事件。 选择“了解和确认”来了解可为其授权的所有警报事件，以及确认当前尚未确认的所有警报事件。

  :::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/learn-and-acknowledge.png" alt-text="选择“了解和确认”来了解所有警报事件。":::

- 屏蔽和取消屏蔽警报事件。

若要详细了解如何学习、确认和屏蔽警报事件，请参阅有关传感器的[管理警报事件](how-to-manage-the-alert-event.md)一文。

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

若要导出警报，请执行以下操作：

1. 在边侧菜单中选择“警报”。

1. 选择“导出”。

1. 选择“导出扩展警报”，将涉及多个设备的每条警报的警报信息导出到单独的行中。 选择“导出扩展警报”后，.csv 文件将创建警报的重复行，但每行包含独特的项。 使用此选项可以更方便地调查导出的警报事件。  

## <a name="create-forwarding-rules"></a>创建转发规则

若要在管理控制台上创建转发规则，请执行以下操作：

1. 登录传感器。

1. 先在侧边菜单上选择“**转发**”。

1. 选择 :::image type="icon" source="media/how-to-work-with-alerts-on-premises-management-console/plus-add-icon.png" border="false"::: 图标。

1. 在“创建转发规则”窗口中，输入规则的名称

    :::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/management-console-create-forwarding-rule.png" alt-text="在“创建转发规则”窗口的字段中输入有意义的名称。":::

   定义触发转发规则所依据的条件。 使用转发规则条件可帮助确定和管理从传感器发送到外部系统的信息量。

1. 从下拉菜单中选择严重性级别。

    这是在严重性级别方面要转发的最小事件。 例如，如果选择“**次要**”、“次要警报”，则将转发此严重级别以上的任何警报。 严重性级别已预先定义。

1. 选择要应用的任何协议。

    如果检测到的流量在特定协议上运行，则仅触发转发规则。 从下拉列表中选择所需的协议，或选择全部协议。

1. 选择应当应用此规则的引擎。

    
   选择所需的引擎，或选择全部引擎。 将发送所选引擎发出的警报。 

1. 选择是否希望转发规则报告系统通知的复选框。
  
1. 选择是否希望转发规则报告警报通知的复选框。

1. 选择“添加”以添加要应用的操作。 填写所选操作所需的任何参数。

    转发规则操作指示传感器将警报信息转发给合作伙伴供应商或服务器。 可以为每个转发规则创建多个操作。

1. 根据需要添加其他操作。

1. 选择“保存”。

有关详细信息，可参阅[关于转发的警报信息](how-to-forward-alert-information-to-partners.md#about-forwarded-alert-information)。 还可以[测试转发规则](how-to-forward-alert-information-to-partners.md#test-forwarding-rules)，或[编辑和删除转发规则](how-to-forward-alert-information-to-partners.md#edit-and-delete-forwarding-rules)。 还可以详细了解[转发规则和警报排除规则](how-to-forward-alert-information-to-partners.md#forwarding-rules-and-alert-exclusion-rules)。

## <a name="create-alert-exclusion-rules"></a>创建警报排除规则

指示 Defender for IoT 基于以下各项忽略警报触发器：

- 时区和时段

- 设备地址（IP、MAC、子网）

- 警报名称

- 特定的传感器

如果你希望 Defender for IoT 忽略将要触发某个警报的活动，请创建警报排除规则。

例如，如果你知道特定传感器监视的所有 OT 设备将要经历为期两天的维护过程，则可以定义一个排除规则，指示 Defender for IoT 在预定义的时段内抑制此传感器检测到的警报。

### <a name="alert-exclusion-logic"></a>警报排除逻辑

警报规则逻辑基于 `AND`。 这意味着，仅当满足所有规则条件时才会触发警报。

如果未定义某个规则条件，该条件将包括所有选项。 例如，如果未在规则中包括某个传感器的名称，该规则将应用于所有传感器。

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/create-alert-exclusion-v2.png" alt-text="“创建排除规则”视图的屏幕截图。":::

规则摘要将显示在“排除规则”窗口中。

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/exclusion-summary-v2.png" alt-text="“排除规则摘要”视图的屏幕截图。":::

除了使用排除规则外，还可以通过屏蔽警报来抑制警报。

### <a name="create-exclusion-rules"></a>创建排除规则

若要创建排除规则，请执行以下操作：

1. 在本地管理控制台的左侧窗格中，选择“警报排除”。 在打开的窗口的右上角，选择“添加”图标 :::image type="icon" source="media/how-to-work-with-alerts-on-premises-management-console/add-icon.png" border="false"::: 定义新的排除规则。 此时会打开“创建排除规则”对话框。

   :::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/create-alert-exclusion-view.png" alt-text="在此对话框中填写信息来创建警报排除规则。":::

1. 在“名称”字段中输入规则名称。 该名称不能包含引号 (`"`)。

1. 在“按时区/时段”部分，输入特定时区中的某个时段。 针对一个时区中的特定时段创建某个排除规则，但同时应在其他时区中实施该规则时，请使用此功能。 例如，你可能需要在三个不同时区中的 8:00 AM 至 10:00 AM 应用某个排除规则。 在这种情况下，请单独创建使用相同时段和相关时区的三个排除规则。

1. 选择“添加”。 在排除期间，不会在连接的传感器上创建任何警报。

   :::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/by-the-time-period.png" alt-text="“按时段”视图的屏幕截图。":::

1. 在“按设备地址”部分，定义：

  - 要排除的设备 IP 地址、MAC 地址或子网地址。

  - 已排除的设备、源和目标的流量方向。

1. 选择“添加”。

1. 在“按警报标题”部分，开始键入警报标题。 从下拉列表中，选择要排除的一个或多个警报标题。

   :::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/alert-title.png" alt-text="“按警报标题”视图的屏幕截图。":::

1. 选择“添加”。

1. 在“按传感器名称”部分，开始键入传感器名称。 从下拉列表中，选择要排除的一个或多个传感器。

1. 选择“添加”。

1. 选择“保存”。 新规则随即显示在规则列表中。

可以通过屏蔽警报或者创建警报排除规则来抑制警报。 本部分介绍这两项功能的可能性用例。

- 排除规则。 对于以下情况，请编写排除规则：

  - 你事先已确认想要从数据库中排除该事件。 例如，你知道在某个传感器上检测到的情况会触发不相关的警报。 例如，你正在特定站点中的组织 PLC 上执行维护工作，并想要抑制与此站点的 PLC 相关的警报。

  - 你希望 Defender for IoT 忽略在特定时间范围（为系统维护任务）生成的事件。

  - 你想要忽略特定子网中的事件。

  - 你想要通过一个规则控制多个传感器生成的警报事件。

  - 你不想要将警报排除作为事件日志中的事件进行跟踪。

- 屏蔽。 对于以下情况，请屏蔽警报：

  - 尚未规划需要屏蔽的项。 你事先并不知道哪些事件是不相关的。

  - 你想要从“警报”窗口抑制警报，但仍希望在事件日志中对其进行跟踪。

  - 你想要忽略特定通道上的事件。

### <a name="trigger-alert-exclusion-rules-from-external-systems"></a>从外部系统触发警报排除规则

从外部系统触发警报排除规则。 例如，从企业票证系统或者用于管理网络维护过程的系统管理排除规则。

定义要应用该规则的传感器、引擎、开始时间和结束时间。 有关详细信息，请参阅 [Defender for IoT API 传感器和管理控制台 API](references-work-with-defender-for-iot-apis.md)。

使用 API 创建的规则将作为 RO 显示在“排除规则”窗口中。

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/edit-exclusion-rule-screen.png" alt-text="“编辑排除规则”视图的屏幕截图。":::

## <a name="next-steps"></a>后续步骤

[处理传感器上的警报](how-to-work-with-alerts-on-your-sensor.md)。
参阅 [Defender for IoT 引擎警报](alert-engine-messages.md)。