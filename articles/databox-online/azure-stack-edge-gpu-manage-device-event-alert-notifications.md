---
title: 管理 Azure Stack Edge Pro 资源的设备事件警报通知 | Microsoft Docs
description: 介绍如何使用 Azure 门户来管理 Azure Stack Edge Pro 资源上的设备事件警报。
services: databox
author: v-dalc
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 585343137a4a8fd8a1fb591c640e1183d71c0fd3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "102443091"
---
# <a name="manage-device-event-alert-notifications-on-azure-stack-edge-pro-resources"></a>管理 Azure Stack Edge Pro 资源上的设备事件警报通知

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

本文介绍如何在 Azure 门户中创建操作规则，以触发或抑制资源组、Azure 订阅或单个 Azure Stack Edge 资源中发生的设备事件的警报通知。 本文适用于 Azure Stack Edge 的所有模型。  

## <a name="about-action-rules"></a>操作规则

操作规则可以触发或抑制警报通知。 操作规则添加到操作组中，操作组是一组通知首选项，用于向需要根据警报执行操作的用户发出通知，警报是在资源或资源集的不同上下文中触发的。

有关操作规则的详细信息，请参阅[配置操作规则](../azure-monitor/alerts/alerts-action-rules.md?tabs=portal#configuring-an-action-rule)。 有关操作组的详细信息，请参阅[在 Azure 门户中创建和管理操作组](../azure-monitor/alerts/action-groups.md)。

> [!NOTE]
> 操作规则功能现已提供预览版。 某些屏幕和步骤可能会随着过程的优化而改变。


## <a name="create-an-action-rule"></a>创建操作规则

在 Azure 门户中采取以下步骤，为 Azure Stack Edge 设备创建操作规则。

> [!NOTE]
> 这些步骤创建向操作组发送通知的操作规则。 有关创建操作规则以抑制通知的详细信息，请参阅[配置操作规则](../azure-monitor/alerts/alerts-action-rules.md?tabs=portal#configuring-an-action-rule)。

1. 转到 Azure 门户中的 Azure Stack Edge 设备，然后转到“监视”>“警报”。 选择“管理操作”。

   ![“监视”>“警报”>“管理操作”视图](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/action-rules-open-view-01.png)

2. 选择“操作规则(预览)”，然后选择“+ 新建操作规则” 。

   ![“管理操作”>“操作规则”选项](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/action-rules-open-view-02.png)

3. 在“创建操作规则”屏幕上，使用“范围”选择 Azure 订阅、资源组或目标资源 。 操作规则将作用于在该范围内生成的所有警报。

   1. 选择“范围”旁的“选择” 。

      ![选择新操作规则的范围](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-scope-01.png)

   2. 选择操作规则的“订阅”，可以选择按“资源”类型进行筛选 。 若要筛选 Azure Stack Edge 资源，请选择“Data Box Edge 设备(dataBoxEdge)”。

      “资源”区域根据你的选择列出可用资源。
  
      ![“选择范围”屏幕上的可用资源](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-scope-02.png)

   3. 选中要对其应用规则的每个资源旁的复选框。 你可以选择订阅、资源组或单个资源。 然后选择“完成”。

      ![操作规则范围的设置示例](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-scope-03.png)

      “创建操作规则”屏幕显示已选择的范围。

      ![Azure Stack Edge 操作规则的已完成范围](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-scope-04.png)

4. 使用“筛选器”选项将规则的应用程序缩小到所选范围内的警报子集。

   1. 选择“添加”以打开“添加筛选器”窗格 。

      ![用于向操作规则添加筛选器的选项](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-filter-01.png)

   2. 在“筛选器”下添加要应用的每个筛选器。 对于每个筛选器，选择筛选器类型、运算符和值 。
   
      有关筛选器选项的列表，请参阅[筛选条件](../azure-monitor/alerts/alerts-action-rules.md?tabs=portal#filter-criteria)。

      下面的示例筛选器适用于监视器服务为 Azure Stack Edge 资源发出的处于严重级别 2、3 和 4 的所有警报。

      完成添加筛选器后，请选择“完成”。
   
      ![操作规则的筛选器示例](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-filter-02.png)

5. 在“创建操作规则”屏幕上，选择“操作组”以创建发送通知的规则 。 然后，选择“操作”旁的“选择” 。

   ![用于创建发送通知的操作规则的操作组选项](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-action-group-01.png)

   > [!NOTE]
   > 若要创建抑制消息的规则，请选择“抑制”。 有关详细信息，请参阅[配置操作规则](../azure-monitor/alerts/alerts-action-rules.md?tabs=portal#configuring-an-action-rule)。

6. 选择要与此操作规则一起使用的操作组。 然后选取“选择”  。 你的新操作规则将添加到所选操作组的通知首选项中。

   如果需要创建新的操作组，请选择“+ 创建操作组”，并按照[使用 Azure 门户创建操作组](../azure-monitor/alerts/action-groups.md#create-an-action-group-by-using-the-azure-portal)中的步骤进行操作。

   ![选择要用于规则的操作组，然后选择“选择”。](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-action-group-02.png)

7. 为新操作规则提供“名称”和“说明”，并为资源组分配规则 。

9. 默认情况下，新规则处于启用状态。 如果不想立即开始使用规则，请在“启用规则更新创建”中选择“否” 。

10. 设置完成后，请选择“创建”。

    ![将发送警报通知的操作规则的已完成设置](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-completed-settings.png)

    随即将打开“操作规则(预览)”屏幕，但你可能不会立即看到新的操作规则。 焦点是“所有”资源组。

11. 若要查看新操作规则，请选择该规则的资源组。

    ![显示新规则的“操作规则”屏幕](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-displayed.png)


## <a name="view-notifications"></a>查看通知

当新事件触发操作规则范围内的资源警报时，就会发出通知。

规则的操作组设置接收通知的人员和发送的通知的类型 - 电子邮件，短信 (SMS)，或这两者。

触发警报后，可能需要几分钟时间才能收到通知。

电子邮件通知将与此类似。

![操作规则的电子邮件通知示例](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/sample-action-rule-email-notification.png)


## <a name="next-steps"></a>后续步骤

<!-- - See [Create and manage action groups in the Azure portal](../azure-monitor/alerts/action-groups.md) for guidance on creating a new action group.
- See [Configure an action rule](../azure-monitor/alerts/alerts-action-rules.md?tabs=portal#configuring-an-action-rule) for more info about creating action rules that send or suppress alert notifications. -2 bullets referenced above. Making room for local tasks in "Next Steps." --> 
- 有关查看设备事件、硬件状态和指标图表的信息，请参阅[监视 Azure Stack Edge Pro](azure-stack-edge-monitor.md)。 
- 有关优化适用于 Azure Stack Edge Pro GPU 设备的 Azure Monitor 的信息，请参阅[使用 Azure Monitor](azure-stack-edge-gpu-enable-azure-monitor.md)。
- 有关管理单个警报的信息，请参阅[使用 Azure Monitor 链接目标创建、查看和管理指标警报](../azure-monitor/alerts/alerts-metric.md)。