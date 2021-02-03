---
title: 管理 Azure Stack Edge Pro 资源的设备事件警报通知 |Microsoft Docs
description: 描述如何使用 Azure 门户来管理 Azure Stack Edge Pro 资源上的设备事件的警报。
services: databox
author: v-dalc
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/02/2021
ms.author: alkohli
ms.openlocfilehash: a18937d84b05159626708d577982405d79161a1a
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/03/2021
ms.locfileid: "99500794"
---
# <a name="manage-device-event-alert-notifications-on-azure-stack-edge-pro-resources"></a>管理 Azure Stack Edge Pro 资源上的设备事件警报通知

本文介绍如何在 Azure 门户中创建操作规则，以便触发或抑制资源组、Azure 订阅或单个 Azure Stack Edge 资源中发生的设备事件的警报通知。 本文适用于 Azure Stack Edge 的所有型号。  

## <a name="about-action-rules"></a>关于操作规则

操作规则可以触发或禁止显示警报通知。 操作规则将添加到 *操作组* -一组通知首选项，用于通知需要对资源或资源集的不同上下文中触发的警报进行操作的用户。

有关操作规则的详细信息，请参阅 [配置操作规则](/azure/azure-monitor/platform/alerts-action-rules?tabs=portal#configuring-an-action-rule)。 有关操作组的详细信息，请参阅 [在 Azure 门户中创建和管理操作组](/blob/master/articles/azure-monitor/platform/action-groups)。

> [!NOTE]
> 操作规则功能处于预览阶段。 当改进进程时，某些屏幕和步骤可能会更改。


## <a name="create-an-action-rule"></a>创建操作规则

在 Azure 门户中执行以下步骤，为 Azure Stack Edge 设备创建操作规则。

> [!NOTE]
> 这些步骤创建将通知发送到操作组的操作规则。 有关创建操作规则以禁止显示通知的详细信息，请参阅 [配置操作规则](/azure/azure-monitor/platform/alerts-action-rules?tabs=portal#configuring-an-action-rule)。

1. 中转到 Azure 门户中的 Azure Stack Edge 设备，然后 **> "监视**" "警报"。 选择 " **管理操作**"。

   ![监视警报，管理操作视图](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/action-rules-open-view-01.png)

2. 选择 " **操作规则" (预览 ")**，然后选择" **+ 新建操作规则**"。

   ![管理操作，操作规则选项](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/action-rules-open-view-02.png)

3. 在 " **创建操作规则** " 屏幕上，使用 " **作用域** " 选择 Azure 订阅、资源组或目标资源。 操作规则将作用于在该范围内生成的所有警报。

   1. 选择 "按 **范围****选择**"。

      ![选择新操作规则的作用域](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-scope-01.png)

   2. 选择操作规则的 **订阅** ，可以选择按 **资源** 类型进行筛选。 若要筛选 Azure Stack Edge 资源，请选择 " **Data Box Edge 设备" (dataBoxEdge)**。

      **资源** 区域根据你的选择列出可用资源。
  
      !["选择作用域" 屏幕上的可用资源](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-scope-02.png)

   3. 选中要对其应用规则的每个资源的复选框。 你可以选择订阅、资源组或单个资源。 然后选择“完成”。

      ![操作规则作用域的示例设置](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-scope-03.png)

      " **创建操作规则** " 屏幕将显示选定的作用域。

      ![Azure Stack 边缘操作规则的已完成范围](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-scope-04.png)

4. 使用 **筛选器** 选项可将规则的应用范围缩小到所选范围内的部分警报。

   1. 选择 " **添加** " 以打开 " **添加筛选器** " 窗格。

      ![用于向操作规则添加筛选器的选项](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-filter-01.png)

   2. 在 " **筛选器**" 下，添加要应用的每个筛选器。 对于每个筛选器，请选择筛选器类型、 **运算符** 和 **值**。
   
      有关筛选器选项的列表，请参阅 [筛选条件](/azure/azure-monitor/platform/alerts-action-rules?tabs=portal#filter-criteria)。

      下面的示例筛选器适用于监视服务为 Azure Stack Edge 资源引发的严重级别为2、3和4的所有警报。

      添加完筛选器后，请选择 " **完成**"。
   
      ![操作规则的示例筛选器](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-filter-02.png)

5. 在 " **创建操作规则** " 屏幕上，选择 " **操作组** " 以创建发送通知的规则。 然后，按 **操作** 选择 " **选择**"。

   ![用于创建发送通知的操作规则的操作组选项](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-action-group-01.png)

   > [!NOTE]
   > 若要创建禁止显示通知的规则，请选择 " **禁止** 显示"。 有关详细信息，请参阅 [配置操作规则](/azure/azure-monitor/platform/alerts-action-rules?tabs=portal#configuring-an-action-rule)。

6. 选择要与此操作规则一起使用的操作组。 然后选取“选择”  。 你的新操作规则将添加到所选操作组的通知首选项。

   如果需要创建新的操作组，请选择 " **+ 创建操作组**"，并按照 [使用 Azure 门户创建操作组](/azure/azure-monitor/platform/action-groups#create-an-action-group-by-using-the-azure-portal)中的步骤操作。

   ![选择要用于规则的操作组，然后选择 "选择"。](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-action-group-02.png)

7. 为新操作规则指定 **名称** 和 **说明**，并为资源组指定规则。

9. 默认情况下，将启用新规则。 如果不想立即开始使用规则，请在 "**启用规则更新创建**" 中选择 "**否**"。

10. 完成设置后，选择 " **创建**"。

    ![已完成将发送警报通知的操作规则的设置](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-completed-settings.png)

    此时会打开 " **操作规则 (预览")** 屏幕，但你可能不会立即看到你的新操作规则。 焦点是 **所有** 资源组。

11. 若要查看新操作规则，请选择该规则的资源组。

    ![显示新规则的 "操作规则" 屏幕](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-displayed.png)


## <a name="view-notifications"></a>查看通知

当新事件触发操作规则范围内的资源的警报时，通知就会发出。

规则集的操作组，该规则设置接收通知的人员，以及发送的通知类型-电子邮件、短消息服务 (SMS) 消息或两者。

触发警报后，可能需要几分钟的时间才能收到通知。

电子邮件通知将与此类似。

![操作规则的示例电子邮件通知](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/sample-action-rule-email-notification.png)


## <a name="next-steps"></a>后续步骤

<!-- - See [Create and manage action groups in the Azure portal](/blob/master/articles/azure-monitor/platform/action-groups) for guidance on creating a new action group.
- See [Configure an action rule](/azure/azure-monitor/platform/alerts-action-rules?tabs=portal#configuring-an-action-rule) for more info about creating action rules that send or suppress alert notifications. -2 bullets referenced above. Making room for local tasks in "Next Steps." --> 
- 有关查看设备事件、硬件状态和指标图表的信息，请参阅 [监视 Azure Stack Edge Pro](azure-stack-edge-monitor.md) 。 
- 请参阅 [使用 Azure Monitor](azure-stack-edge-gpu-enable-azure-monitor.md) 获取有关优化 Azure Stack EDGE Pro GPU 设备的 Azure Monitor 的信息。
- 有关管理单个警报的信息，请参阅 [使用 Azure Monitor 链接目标创建、查看和管理指标警报](/../azure-monitor/platform/alerts-metric.md) 。
