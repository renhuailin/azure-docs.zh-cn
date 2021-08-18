---
title: 深入了解全球、区域和本地威胁
description: 通过使用本地管理控制台中的站点地图，深入了解全球、区域和本地威胁。
ms.date: 12/07/2020
ms.topic: how-to
ms.openlocfilehash: db3b9fbca9acd6c4ce1cfe137a4024f66d8a6292
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2021
ms.locfileid: "113015788"
---
# <a name="gain-insight-into-global-regional-and-local-threats"></a>深入了解全球、区域和本地威胁

本地管理控制台中的站点地图可将网络划分为反映业务拓扑的地理和逻辑段，从而帮助你实现完整的安全覆盖范围：

- **地理设施级别**：站点反映了根据地图上显示的地理位置进行分组的若干设备。 默认情况下，Azure Defender for IoT 提供世界地图。 你可以更新地图以反映你的组织或业务结构。 例如，使用反映特定国家、城市或工业园区内站点的地图。 当地图上的站点颜色更改时，它为 SOC 团队提供了设施中关键系统状态的指示。

  地图是交互式的，可以打开每个站点并深入了解该站点的信息。

- **全球逻辑层**：业务部门是一种根据特定行业将企业划分为逻辑段的方法。 执行此操作时，业务拓扑将反映在地图上。

  例如，包含玻璃厂、塑料厂和汽车厂的全球性公司可以作为三个不同的业务部门进行管理。 多伦多的一个物理站点包括三个不同的玻璃生产线、一个塑料生产线和一个卡车引擎生产线。 因此，此站点拥有所有三个业务部门的代表。

- **地理区域级别**：创建区域，以将全球企业划分为多个地理区域。 例如，我们描述的那家公司可能会使用北美、西欧和东欧区域。 这三个业务部门在北美都有工厂。 西欧有汽车厂和玻璃厂，东欧只有塑料厂。

- **本地逻辑段级别**：区域是站点内的逻辑段，它定义了功能区域或生产线等内容。 使用区域可以强制实现与区域定义相关的安全策略。 例如，包含五个生产线的站点可以分为五个区域段。

- **本地视图级别**：单个传感器安装的本地视图可提供对已连接设备的操作状态和安全状态的见解。

## <a name="work-with-site-map-views"></a>使用站点地图视图

本地管理控制台通过与上下文相关的地图提供工业网络的整体视图。 常规地图视图显示组织的全球地图以及每个站点的地理位置。

:::image type="content" source="media/how-to-work-with-maps/enterprise.png" alt-text="“企业地图”视图的屏幕截图。":::

### <a name="color-coded-map-views"></a>颜色编码的地图视图

**绿色**：安全事件的数量低于 Defender for IoT 为你的系统定义的阈值。 无需采取任何措施。

**黄色**：安全事件的数量等于 Defender for IoT 为你的系统定义的阈值。 考虑调查这些事件。  

**红色**：安全事件的数量高于 Defender for IoT 为你的系统定义的阈值。 立即采取措施。

### <a name="risk-level-map-views"></a>风险级别地图视图

**风险评估**：“风险评估”视图显示有关站点风险的信息。 风险信息有助于确定缓解措施的优先级，并构建路线图以计划进行安全性改进。

**事件响应**：获得整个企业中每个站点上所有未确认的警报的集中视图。 你可以向下钻取和管理在特定站点中检测到的警报。

:::image type="content" source="media/how-to-work-with-maps/incident-responses.png" alt-text="包含“事件响应”的“企业地图”视图的屏幕截图。":::

**恶意活动**：如果检测到恶意软件，该站点会显示为红色。 这表示应立即采取措施。

:::image type="content" source="media/how-to-work-with-maps/malicious-activity.png" alt-text="包含“恶意活动”的“企业地图”视图的屏幕截图。":::

**操作警报**：借助此针对 OT 系统的地图视图，可更好地了解哪个 OT 系统可能会遇到操作事件，例如 PLC 停止、固件上传和程序上传。

:::image type="content" source="media/how-to-work-with-maps/operational-alerts.png" alt-text="包含“操作警报”的“企业地图”视图的屏幕截图。":::

若要选择地图视图：

1. 从地图中选择“默认视图”。
2. 选择一个视图。

:::image type="content" source="media/how-to-work-with-maps/map-views.png" alt-text="站点地图默认视图的屏幕截图。":::

## <a name="update-the-site-map-image"></a>更新站点地图图像

Defender for IoT 提供默认的世界地图。 你可以更改它以反映你的组织：例如，国家/地区地图或城市地图。 

若要替换地图：

1. 在左侧窗格中选择“系统设置”。

2. 选择“更改站点地图”，然后上传图形文件以替换默认地图。

## <a name="next-step"></a>后续步骤

[查看警报](how-to-view-alerts.md)
