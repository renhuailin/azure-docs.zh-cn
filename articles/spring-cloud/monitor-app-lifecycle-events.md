---
title: 使用 Azure 活动日志和 Azure 服务运行状况监视应用生命周期事件
description: 使用 Azure 活动日志和 Azure 服务运行状况监视应用生命周期事件并设置警报。
author: karlerickson
ms.author: shiqiu
ms.service: spring-cloud
ms.topic: how-to
ms.date: 08/19/2021
ms.custom: devx-track-java
ms.openlocfilehash: 77e6704808cf54f84f0261c07236bfa27d17a1f6
ms.sourcegitcommit: d858083348844b7cf854b1a0f01e3a2583809649
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2021
ms.locfileid: "122868482"
---
# <a name="monitor-app-lifecycle-events-using-azure-activity-log-and-azure-service-health"></a>使用 Azure 活动日志和 Azure 服务运行状况监视应用生命周期事件

本文介绍如何使用 Azure 活动日志和 Azure 服务运行状况监视应用生命周期事件并设置警报。

Azure Spring Cloud 提供用于监视应用程序状态和运行状况的内置工具。 应用生命周期事件可帮助你了解对应用程序所做的任何更改，以便可以根据需要执行操作。 

## <a name="prerequisites"></a>先决条件

- Azure 订阅。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 一个已部署的 Azure Spring Cloud 服务实例，并且至少已在该服务实例中创建一个应用程序。 有关详细信息，请参阅[快速入门：部署第一个 Azure Spring Cloud 应用程序](quickstart.md)。 

## <a name="monitor-app-lifecycle-events-triggered-by-users-in-azure-activity-logs"></a>在 Azure 活动日志中监视由用户触发的应用生命周期事件

[Azure 活动日志](../azure-monitor/essentials/activity-log.md)包含对订阅中的资源执行的操作所发出的资源事件。 应用程序生命周期事件（启动、停止、重启）的以下详细信息已添加到 Azure 活动日志中：

- 操作何时发生
- 操作的状态
- 启动应用时创建了哪些实例
- 停止应用时删除了哪些实例
- 重启应用时删除和创建了哪些实例

例如，当重启应用时，可以从 Azure 门户中的“活动日志”详细信息页中找到受影响的实例。

:::image type="content" source="media/monitor-app-lifecycle-events/activity-log-restart-detail.png" lightbox="media/monitor-app-lifecycle-events/activity-log-restart-detail.png" alt-text="活动日志中重启详细信息的 Azure 门户屏幕截图":::

## <a name="monitor-app-lifecycle-events-in-azure-service-health"></a>在 Azure 服务运行状况中监视应用生命周期事件

[Azure 资源运行状况](../service-health/resource-health-overview.md)可帮助你诊断可能影响服务可用性的问题并获得相关支持。 这些问题包括服务事件、计划内维护期和区域中断。 应用程序重启事件会添加到 Azure 服务运行状况。 它们包括意外的事件（例如计划外的应用故障）和计划的操作（例如计划内维护）。

### <a name="monitor-unplanned-app-lifecycle-events"></a>监视计划外的应用生命周期事件

应用因计划外事件而重启时，Azure Spring Cloud 实例会在 Azure 门户的“资源运行状况”部分中显示“已降级”状态 。 “已降级”表示资源已检测到潜在的性能降低，但仍可供使用。 计划外事件的示例包括应用故障、运行状况检查失败和系统中断。

:::image type="content" source="media/monitor-app-lifecycle-events/resource-health-detail.png" alt-text="“资源运行状况”窗格的屏幕截图":::

可在运行状况历史记录页面中查找最新状态、根本原因和受影响的实例。

:::image type="content" source="media/monitor-app-lifecycle-events/unplanned-app-lifecycle-event-details.png" lightbox="media/monitor-app-lifecycle-events/unplanned-app-lifecycle-event-details.png" alt-text="计划外应用生命周期事件的示例日志屏幕截图":::


### <a name="monitor-planned-app-lifecycle-events"></a>监视计划内的应用生命周期事件

在平台维护期间，应用可能会重启。 可以从 Azure 服务运行状况的“计划内维护”页面提前收到维护通知。

:::image type="content" source="media/monitor-app-lifecycle-events/planned-maintenance-notification.png" lightbox="media/monitor-app-lifecycle-events/planned-maintenance-notification.png" alt-text="计划内维护通知示例的屏幕截图":::

发生平台维护时，Azure Spring Cloud 实例也会显示“已降级”状态。 如果在平台维护期间需要重启，Azure Spring Cloud 将执行滚动更新以增量更新应用程序。 滚动更新用于在不停机的情况下更新工作负载。 可以在运行状况历史记录页面中查找最新状态。

:::image type="content" source="media/monitor-app-lifecycle-events/planned-maintenance-in-progress.png" lightbox="media/monitor-app-lifecycle-events/planned-maintenance-in-progress.png" alt-text="正在进行的计划内维护的示例日志屏幕截图":::

## <a name="set-up-alerts"></a>设置警报

可以为应用生命周期事件设置警报。 服务运行状况通知也存储在 Azure 活动日志中。 由于活动日志存储了大量信息，因此我们提供了单独的用户界面，以便更轻松地查看和设置有关服务运行状况通知的警报。

以下列表描述设置警报所需的关键步骤： 

1. 设置操作组，其中包含触发警报时要执行的操作。 示例操作类型包括发送语音呼叫、短信、电子邮件或触发各种类型的自动化操作。 各种警报可以使用相同的操作组或不同的操作组，具体取决于用户的要求。
2. 设置预警规则。 警报使用操作组来通知用户某个特定应用生命周期事件的警报已被触发。

### <a name="set-up-alerts-on-activity-log"></a>在活动日志上设置警报

以下步骤显示如何在 Azure 门户中创建活动日志预警规则：

1. 导航到“活动日志”，打开任何活动日志的详细信息页面，然后选择“新建预警规则” 。

   :::image type="content" source="media/monitor-app-lifecycle-events/activity-log-alert.png" lightbox="media/monitor-app-lifecycle-events/activity-log-alert.png" alt-text="活动日志警报的屏幕截图":::

2. 选择警报范围。

3. 指定警报条件。

   :::image type="content" source="media/monitor-app-lifecycle-events/activity-log-alert-condition.png" lightbox="media/monitor-app-lifecycle-events/activity-log-alert-condition.png" alt-text="活动日志警报条件的屏幕截图":::

4. 选择“操作”并添加“预警规则详细信息” 。

5. 选择“创建警报规则”。

### <a name="set-up-alerts-to-monitor-app-lifecycle-events-in-azure-service-health"></a>在 Azure 服务运行状况中设置用于监视应用生命周期事件的警报

以下步骤显示如何在 Azure 门户中为服务运行状况通知创建预警规则：

1. 导航到“服务运行状况”下的“资源运行状况”，然后选择“添加资源运行状况警报”  。

   :::image type="content" source="media/monitor-app-lifecycle-events/add-resource-health-alert.png" alt-text="“资源运行状况”窗格的屏幕截图，其中突出显示了“添加资源运行状况警报”按钮":::

2. 选择警报针对的 **资源**。

   :::image type="content" source="media/monitor-app-lifecycle-events/resource-health-alert-target.png" alt-text="资源运行状况警报目标的屏幕截图":::

3. 指定警报条件。

   :::image type="content" source="media/monitor-app-lifecycle-events/resource-health-alert-condition.png" alt-text="资源运行状况警报条件的屏幕截图":::

4. 选择“操作”并添加“预警规则详细信息” 。

5. 选择“创建警报规则”。

### <a name="set-up-alerts-to-monitor-the-planned-maintenance-notification"></a>设置用于监视计划内维护通知的警报

以下步骤显示如何在 Azure 门户中为计划内维护通知创建预警规则：

1. 导航到“服务运行状况”下的“运行状况警报”，然后选择“添加服务运行状况警报”  。

   :::image type="content" source="media/monitor-app-lifecycle-events/add-service-health-alert.png" alt-text="“运行状况警报”窗格的屏幕截图，其中突出显示了“添加服务运行状况警报”按钮":::

2. 提供“订阅”、“服务”、“区域”、“事件类型”、“操作”和“预警规则详细信息”的值     。

   :::image type="content" source="media/monitor-app-lifecycle-events/add-service-health-alert-details.png" lightbox="media/monitor-app-lifecycle-events/add-service-health-alert-details.png" alt-text="服务运行状况的“创建预警警报”窗格的屏幕截图":::

3. 选择“创建警报规则”。

## <a name="next-steps"></a>后续步骤

[自行诊断并解决 Azure Spring Cloud 中的问题](how-to-self-diagnose-solve.md)
