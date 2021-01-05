---
title: 深入了解全球、区域和本地威胁
description: 通过在本地管理控制台中使用站点映射，深入了解全球、区域和本地威胁。
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/07/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: cde55f3c2a875c593c07ea05427f8075d8599196
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/31/2020
ms.locfileid: "97838491"
---
# <a name="gain-insight-into-global-regional-and-local-threats"></a>深入了解全球、区域和本地威胁

使用本地管理控制台中的站点映射，可以通过将网络划分为反映业务拓扑的地理和逻辑段来实现完全安全覆盖：

- **地理设施级别**：站点反映多个设备，这些设备根据地图上显示的地理位置进行分组。 默认情况下，Azure Defender for IoT 提供了世界地图。 您可以更新该映射，以反映您的组织或企业结构。 例如，使用反映特定国家/地区、城市或工业校园内站点的地图。 当站点颜色在地图上发生更改时，它会为 SOC 团队提供设备中的严重系统状态指示。

  此地图是交互式的，并允许打开每个站点并深入了解到此站点的信息中。

- **全局逻辑层**：业务部门是指根据特定行业将企业划分为逻辑段的方式。 当你执行此操作时，业务拓扑将反映在地图上。

  例如，包含玻璃工厂、塑料工厂和汽车工厂的全球性公司可以管理为三个不同的业务单位。 位于多伦多的物理站点包括三个不同的玻璃生产线、塑料生产线和卡车引擎生产线。 因此，此站点包含所有三个业务单位的代表。

- **地理区域级别**：创建区域以将全局企业划分为地理区域。 例如，我们介绍的公司可能使用区域北美、西欧和东欧。 北美具有来自所有三个业务单位的工厂。 西欧具有汽车工厂和玻璃工厂，东欧仅具有塑料工厂。

- **本地逻辑段级别**：区域是站点内的一个逻辑段，它定义了一个功能区域或生产线。 使用区域可以强制实施与区域定义相关的安全策略。 例如，包含5个生产行的站点可以分为五个区域。

- **本地视图级别**：单一传感器安装的本地视图可提供连接设备的操作和安全状态的见解。

## <a name="work-with-site-map-views"></a>使用站点地图视图

本地管理控制台在上下文相关的地图中提供了您的工业网络的总体视图。 一般地图视图显示组织的全局地图，其中包含每个站点的地理位置。

:::image type="content" source="media/how-to-work-with-maps/enterprise.png" alt-text="企业地图视图的屏幕截图。":::

### <a name="color-coded-map-views"></a>颜色编码地图视图

**绿色**：安全事件的数量低于为你的系统定义的 "用于 IoT 的 Defender" 阈值。 无需采取任何措施。

**黄色**：安全事件的数目等于为你的系统定义了 IoT 的 Defender 阈值。 请考虑调查事件。  

**红色**：安全事件的数量超出了为你的系统定义了 IoT 的 Defender 的阈值。 立即采取措施。

### <a name="risk-level-map-views"></a>风险级地图视图

**风险评估**：风险评估视图显示有关站点风险的信息。 风险信息可帮助你确定缓解的优先级，并构建道路地图来规划安全改进。

**事件响应**：获取整个企业中每个站点的所有未确认警报的集中视图。 您可以向下钻取并管理在特定站点中检测到的警报。

:::image type="content" source="media/how-to-work-with-maps/incident-responses.png" alt-text="包含事件响应的企业地图视图的屏幕截图。":::

**恶意活动**：如果检测到恶意软件，站点将显示为红色。 这表明您应该立即采取措施。

:::image type="content" source="media/how-to-work-with-maps/malicious-activity.png" alt-text="具有恶意活动的企业地图视图的屏幕截图。":::

**操作警报**：适用于 ot 系统的此地图视图可以更好地了解哪个系统可能会遇到操作事件，例如 PLC 停止、固件上传和程序上载。

:::image type="content" source="media/how-to-work-with-maps/operational-alerts.png" alt-text="包含操作警报的企业地图视图的屏幕截图。":::

选择地图视图：

1. 从映射中选择 **默认视图** 。
2. 选择一个视图。

:::image type="content" source="media/how-to-work-with-maps/map-views.png" alt-text="站点地图默认视图的屏幕截图。":::

## <a name="update-the-site-map-image"></a>更新站点图图像

Defender for IoT 提供默认世界地图。 你可以对其进行更改，以反映你的组织：例如，国家/地区地图或城市地图。 

替换地图：

1. 在左侧窗格中，选择 " **系统设置**"。

2. 选择 **更改网站地图** 并上传图形文件以替换默认映射。

## <a name="next-step"></a>后续步骤

[查看警报](how-to-view-alerts.md)
