---
title: 在 Azure VMware 解决方案中配置警报并使用指标
description: 了解如何使用警报来接收通知。 同时了解如何使用指标来更深入地了解 Azure VMware 解决方案私有云。
ms.topic: how-to
ms.date: 07/23/2021
ms.openlocfilehash: 718838d5335ff10200fc41029a6431a96552894b
ms.sourcegitcommit: 6f21017b63520da0c9d67ca90896b8a84217d3d3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2021
ms.locfileid: "114653569"
---
# <a name="configure-azure-alerts-in-azure-vmware-solution"></a>在 Azure VMware 解决方案中配置 Azure 警报 

在本文中，你将了解如何在 [Microsoft Azure 警报](../azure-monitor/alerts/alerts-overview.md)中配置 [Azure 操作组](../azure-monitor/alerts/action-groups.md)，以便接收你定义的已触发事件的通知。 你还将了解如何使用 [Azure Monitor 指标](../azure-monitor/essentials/data-platform-metrics.md)更深入地了解 Azure VMware 解决方案私有云。

>[!NOTE]
>影响 Azure VMware 解决方案主机及其相应还原可用性的事件会自动发送到包含 Azure VMware 解决方案私有云的订阅的帐户管理员、服务管理员（经典权限）、共同管理员（经典权限）和所有者（RBAC 角色）。

## <a name="supported-metrics-and-activities"></a>支持的指标和活动

可通过 Azure Monitor 指标查看以下指标。

| **信号名称**                                                         | **信号类型** | **监视服务** |
|-------------------------------------------------------------------------|-----------------|---------------------|
| 数据存储磁盘总容量                                           | 指标          | 平台            |
| 已使用的数据存储磁盘百分比                                          | 指标          | 平台            |
| CPU 百分比                                                          | 指标          | 平台            |
| 平均有效内存                                                | 指标          | 平台            |
| 内存平均开销                                                 | 指标          | 平台            |
| 平均内存总量                                                    | 指标          | 平台            |
| 内存使用率平均值                                                    | 指标          | 平台            |
| 使用的数据存储磁盘                                                     | 指标          | 平台            |
| 所有管理操作                                           | 活动日志    | 管理      |
| 注册 Microsoft.AVS 资源提供程序。 (Microsoft.AVS/privateClouds) | 活动日志    | 管理      |
| 创建或更新 PrivateCloud。 (Microsoft.AVS/privateClouds)          | 活动日志    | 管理      |
| 删除 PrivateCloud。 (Microsoft.AVS/privateClouds)                    | 活动日志    | 管理      |

## <a name="configure-an-alert-rule"></a>配置警报规则
1. 在 Azure VMware 解决方案私有云中，选择“监视” > “警报”，然后选择“新建警报规则”  。
 
   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/create-new-alert-rule.png" alt-text="该屏幕截图显示在 Azure VMware 解决方案私有云中配置警报规则的位置。" lightbox="media/configure-alerts-for-azure-vmware-solution/create-new-alert-rule.png":::

   此时将打开一个新的配置屏幕，你将在其中执行以下操作：
   - 定义作用域
   - 配置条件
   - 设置操作组
   - 定义警报规则详细信息
    
   :::image type="content" source="../devtest-labs/media/activity-logs/create-alert-rule-done.png" alt-text="显示“创建警报规则”窗口的屏幕截图。" lightbox="../devtest-labs/media/activity-logs/create-alert-rule-done.png":::

1. 在“作用域”下，选择要监视的目标资源。 默认情况下，定义了从中打开“警报”菜单的 Azure VMware 解决方案私有云。

1. 在“条件”下，选择“添加条件”，然后在打开的窗口中，选择要为警报规则创建的信号 。 

   在本例中，我们选择“已使用的数据存储磁盘百分比”，这从 [Azure VMware 解决方案 SLA](https://aka.ms/avs/sla) 角度来看是相关的。 

   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/configure-signal-logic-options.png" alt-text="显示“配置信号逻辑”窗口的屏幕截图，其中包含用于创建警报规则的信号。"::: 

1. 定义将触发警报的逻辑，然后选择“完成”。 

   在本例中，仅调整了“阈值”和“计算频率” 。 
   
   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/define-alert-logic-threshold.png" alt-text="屏幕截图显示信号警报逻辑的阈值、运算符、聚合类型和粒度、阈值和评估频率。"::: 

1. 在“操作”下，选择“添加操作组”。 操作组定义通知的接收方式和接收者 。   可以通过电子邮件、短信、[Azure 移动应用推送通知](https://azure.microsoft.com/features/azure-portal/mobile-app/)或语音消息接收通知。

1. 选择现有操作组或选择“创建操作组”，以创建新的组。
 
1. 在打开的窗口中，在“基本信息”选项卡上，为操作组指定名称和显示名称。

1. 选择“通知”选项卡，然后选择“通知类型”和“名称”  。 然后选择“确定”。 

   本例基于电子邮件通知。

   :::image type="content" source="../azure-monitor/alerts/media/action-groups/action-group-2-notifications.png" alt-text="屏幕截图显示警报的电子邮件、短信消息、推送和语音设置。" lightbox="../azure-monitor/alerts/media/action-groups/action-group-2-notifications.png":::    

1. （可选）如果要主动采取操作并接收事件通知，请配置“操作”。 选择可用“操作类型”，然后选择“查看 + 创建” 。 

   - 自动化 Runbook - 根据警报自动执行任务

   - Azure Functions - 用于自定义事件驱动的无服务器代码执行

   - ITSM - 与 ServiceNow 等服务提供商集成，以创建票证

   - 逻辑应用 - 用于更复杂的工作流编排

   - Webhook - 触发其他服务中的进程


1. 在“警报规则详细信息”下，提供名称、说明、要存储警报规则的资源组、严重性。 然后选择“创建警报规则”。
   
   可从 Azure 门户查看和管理警报规则。

   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/existing-alert-rule.png" alt-text="屏幕截图显示了“规则”窗口中的新警报规则。" lightbox="media/configure-alerts-for-azure-vmware-solution/existing-alert-rule.png":::      

   当指标达到警报规则中定义的阈值时，系统将更新“警报”菜单并使其可见。

   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/threshold-alert.png" alt-text="屏幕截图显示达到警报规则中定义的阈值后的警报。" lightbox="media/configure-alerts-for-azure-vmware-solution/threshold-alert.png":::     

   根据配置的操作组，你将通过配置的媒体收到通知。 在本例中，我们配置了电子邮件。
    
   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/alert-notification.png" alt-text="包含错误字符串的 Azure Monitor 警报的屏幕截图，其中触发了日期和时间事件。"::: 

## <a name="work-with-metrics"></a>使用指标

1. 在 Azure VMware 解决方案私有云中，选择“监视” > “指标” 。 然后从下拉菜单中选择所需的指标。
    
   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/monitoring-metrics.png" alt-text="屏幕截图显示“指标”窗口，其中焦点位于“指标”下拉菜单。" lightbox="media/configure-alerts-for-azure-vmware-solution/monitoring-metrics.png":::   

1. 可以更改图表的参数，例如“时间范围”或“时间粒度” 。 

   其他选项有：
   - 深入了解日志并查询相关 Log Analytics 工作区中的数据
   - 为方便起见，将此图表固定到 Azure 仪表板。

   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/monitoring-metrics-time-range-granularity.png" alt-text="屏幕截图显示指标的时间范围和时间粒度选项。" lightbox="media/configure-alerts-for-azure-vmware-solution/monitoring-metrics-time-range-granularity.png":::  
 
 
## <a name="next-steps"></a>后续步骤

为 Azure VMware 解决方案私有云配置了警报规则后，现在，你可能想要详细了解以下内容：
- [Azure Monitor 指标](../azure-monitor/essentials/data-platform-metrics.md)
- [Azure Monitor 警报](../azure-monitor/alerts/alerts-overview.md)
- [Azure 操作组](../azure-monitor/alerts/action-groups.md)

你还可以继续学习其他 [Azure VMware 解决方案](index.yml)操作指南。
