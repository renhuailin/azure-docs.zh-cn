---
title: 处理本地管理控制台上的警报
description: 使用本地管理控制台获得网络中最近威胁的企业视图，并更好地了解传感器用户如何处理它们。
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/06/2020
ms.service: azure
ms.topic: how-to
ms.openlocfilehash: ebd5a0c14b6f9e3a57e3a7df888b5f9d24f9a2df
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/15/2021
ms.locfileid: "100523611"
---
# <a name="work-with-alerts-on-the-on-premises-management-console"></a>处理本地管理控制台上的警报 

你可以从管理控制台的 " **警报** " 窗口中执行以下操作：

- 使用警报筛选器

- 使用警报计数器

- 查看警报信息

- 管理警报事件

- 创建警报排除规则

- 触发外部系统发出的警报排除规则

- 利用警报组加速事件工作流

## <a name="view-alerts-in-the-on-premises-management-console"></a>在本地管理控制台中查看警报

本地管理控制台将聚合所有连接的传感器的警报。 这为网络中的最新威胁提供企业视图，有助于更好地了解传感器用户如何处理它们。

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/alerts-with-samples.png" alt-text="&quot;警报&quot; 窗口的屏幕截图。":::

### <a name="work-with-alert-filters"></a>使用警报筛选器

" **警报** " 窗口显示连接到本地管理控制台的传感器生成的警报。 你可以查看已连接传感器的所有警报，或者显示从特定中发送的警报：

- 站点

- 区域

- 设备

- 传感器

选择 " **清除筛选器** " 以查看所有警报。

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/clear-filters.png" alt-text="通过选择 &quot;清除筛选器&quot; 按钮清除筛选器。":::

### <a name="work-with-alert-counters"></a>使用警报计数器

警报计数器按严重性和确认状态提供警报细目。

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/number-of-alerts.png" alt-text="警报计数器显示有多少警报。":::

警报计数器中显示以下严重级别：

- **严重**：指示应该立即处理的恶意攻击。

- **主要**：指示解决重要的安全威胁。

- **小调**：表示可能包含安全威胁的基线行为的一些偏差。

- **警告**：指示不存在安全威胁的基线行为的一些偏差。

严重性级别是预定义的。

可以调整计数器以根据确认和未确认的警报提供数字。 在 IoT 传感器的 Defender 上触发了未确认的警报，但没有在传感器上检查操作员的警报。

如果选择了 " **显示确认警报** " 选项，则所有已确认的警报将显示在 " **警报** " 窗口中。

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/show-acknowledged-alerts.png" alt-text="查看已确认的警报。":::

### <a name="view-alert-information"></a>查看警报信息

该警报提供以下信息：

- 警报事件的摘要。

- 警报严重性。

- 传感器上检测到的警报的链接。

- 警报 UUID。 UUID 包含与传感器上检测到的警报事件关联的警报 ID，由连字符分隔，后跟唯一的系统 ID 号。

**本地管理控制台警报 UUID**

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/unauthorized-internet-connectivity.png" alt-text="设备已连接，但未获得授权。":::

**传感器警报 ID**

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/internet-connectivity-alert.png" alt-text="传感器警报 ID。":::

使用 Uuid 可确保在本地管理控制台中显示的每个警报都可以搜索，并可通过唯一的数字识别。 这是必需的，因为从多个传感器生成的警报可能会生成相同的警报 ID。

> [!NOTE]
> 默认情况下，在定义转发规则时，Uuid 显示在以下合作伙伴系统中： ArcSight、syslog server、QRadar、Sentinel 和 NetWitness。 不需要安装。

查看警报信息：

- 在警报列表中，选择一个警报。

  :::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/alert-information.png" alt-text="警报信息的屏幕截图。":::

在传感器中查看警报：

- 从警报中选择 " **打开传感器** "。

查看区域映射中的设备：

- 若要查看设备映射，并将焦点放在警报设备及其所有设备上，请选择 " **显示设备**"。

## <a name="manage-alert-events"></a>管理警报事件

有多个选项可用于管理本地管理控制台的警报事件。

- 了解或确认警报事件。 选择 " **了解 &** " "了解" 以了解可以获得授权的所有警报事件，并确认当前未确认的所有警报事件。

  :::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/learn-and-acknowledge.png" alt-text="选择 &quot;了解 & 确认&quot; 了解所有。":::

- 静音和取消静音警报事件。

若要了解有关学习、确认和静音警报事件的详细信息，请参阅传感器 [管理警报事件](how-to-manage-the-alert-event.md) 一文。

## <a name="export-alert-information"></a>导出警报信息

将警报信息导出到 .csv 文件。 您可以导出检测到的所有警报的信息，或基于筛选视图导出信息。导出以下信息：

- 源地址
- 目标地址
- 警报标题
- 警报严重性
- 警报消息
- 其他信息
- 确认状态
- PCAP 可用性

导出：

1. 从侧菜单中选择 "警报"。
1. 选择“导出”  。
1. 选择 "导出扩展警报" 可在单独的行中为涵盖多个设备的每个警报导出警报信息。 如果选择了 "导出扩展警报"，则 .csv 文件将创建警报的重复行，其中每行包含唯一项。 使用此选项可以更方便地调查导出的警报事件。  

## <a name="create-alert-exclusion-rules"></a>创建警报排除规则

指示用于 IoT 的 Defender 忽略警报触发器，基于：

- 时区和时间段

- 设备地址 (IP、MAC、子网) 

- 警报名称

- 特定传感器

当你希望将 Defender 用于 IoT 忽略将触发警报的活动时，请创建警报排除规则。

例如，如果你知道由特定传感器监视的所有设备将在两天内完成维护过程，则可以定义一个排除规则，该规则指示在预定义时间段内由此传感器检测到的警报的 Defender。

### <a name="alert-exclusion-logic"></a>警报排除逻辑

警报规则逻辑是 `AND` 基于的。 这意味着，仅当满足所有规则条件时才会触发警报。

如果未定义规则条件，则该条件将包括所有选项。 例如，如果不在规则中包含传感器的名称，则将其应用于所有传感器。

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/create-alert-exclusion-v2.png" alt-text="创建排除规则视图的屏幕截图。":::

规则摘要将出现在 " **排除规则** " 窗口中。

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/exclusion-summary-v2.png" alt-text="排除规则摘要视图的屏幕截图。":::

除了使用排除规则外，还可以通过对警报进行静音来取消警报。

### <a name="create-exclusion-rules"></a>创建排除规则

创建排除规则：

1. 在本地管理控制台的左窗格中，选择 " **警报排除**"。 通过选择窗口右上角的 " **添加** " 图标来定义新的排除规则 :::image type="icon" source="media/how-to-work-with-alerts-on-premises-management-console/add-icon.png" border="false"::: 。 " **创建排除规则** " 对话框随即打开。

   :::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/create-alert-exclusion-view.png" alt-text="通过在此处填写信息来创建警报排除项。":::

2. 在 " **名称** " 字段中输入规则名称。 名称不能包含 (`"`) 的引号。

3. 在 " **按时区/期间** " 部分中，输入特定时区中的时间段。 在一个时区中的特定时间段内创建排除规则时使用此功能，但应在其他时区同时实现。 例如，你可能需要在三个不同时区中的 8:00 AM 和 10:00 AM 之间应用排除规则。 在这种情况下，创建三个使用相同时间段和相关时区的单独排除规则。

4. 选择“添加”。 在排除期间，不会在连接的传感器上创建警报。

   :::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/by-the-time-period.png" alt-text="按时间段视图的屏幕截图。":::

5. 在 " **按设备地址** " 部分中，定义：

  - 要排除的设备 IP 地址、MAC 地址或子网地址。

  - 已排除的设备、源和目标的流量方向。

6. 选择“添加”。

7. 在 " **按警报标题** " 部分中，开始键入警报标题。 从下拉列表中，选择要排除的警报标题或标题。

   :::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/alert-title.png" alt-text="按警报标题视图的屏幕截图。":::

8. 选择“添加”。

9. 在 " **按传感器名称** " 部分中，开始键入传感器名称。 从下拉列表中，选择要排除的一个或一组传感器。

10. 选择“添加”。

11. 选择“保存”  。 新规则显示在规则列表中。

您可以通过将警报静音或创建警报排除规则来抑制它们。 本部分介绍这两种功能的可能用例。

- **排除规则**。 在以下情况时编写排除规则：

  - 您提前知道您需要从数据库中排除该事件。 例如，你知道在某个传感器上检测到的方案将触发不相关的警报。 例如，你将在特定站点上的组织 Plc 上执行维护工作，并希望抑制与此站点的 Plc 相关的警报。

  - 要使 Defender for IoT 忽略特定时间范围内的事件， (系统维护任务) 。

  - 要忽略特定子网中的事件。

  - 需要控制从具有一个规则的多个传感器生成的警报事件。

  - 您不希望将警报排除作为事件记录在事件日志中。

- **静音**。 警报时静音：

  - 不计划需要静音的项。 您不会提前知道哪些事件是不相关的。

  - 您希望从 " **警报** " 窗口中禁止显示警报，但仍要在事件日志中进行跟踪。

  - 要忽略特定通道上的事件。

### <a name="trigger-alert-exclusion-rules-from-external-systems"></a>触发外部系统发出的警报排除规则

触发外部系统发出的警报排除规则。 例如，从管理网络维护过程的企业票证系统或系统管理排除规则。

定义传感器、引擎、开始时间和应用规则的结束时间。 有关详细信息，请参阅 [IOT API 传感器和管理控制台 api 的 Defender](references-work-with-defender-for-iot-apis.md)。

使用 API 创建的规则将在 " **排除规则** " 窗口中显示为 RO。

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/edit-exclusion-rule-screen.png" alt-text="编辑排除规则视图的屏幕截图。":::

## <a name="next-steps"></a>后续步骤

[处理传感器的警报](how-to-work-with-alerts-on-your-sensor.md)
