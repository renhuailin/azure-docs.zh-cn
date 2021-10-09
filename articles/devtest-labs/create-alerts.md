---
title: 为实验室创建活动日志警报
description: 本文介绍在 Azure 开发测试实验室中创建实验室的活动日志警报的步骤。
ms.topic: how-to
ms.date: 07/10/2020
ms.openlocfilehash: 0ebb5ae118d6485afd697ff9b5db7113a101b358
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128628159"
---
# <a name="create-activity-log-alerts-for-labs-in-azure-devtest-labs"></a>在 Azure 开发测试实验室中创建实验室的活动日志警报
本文介绍如何在 Azure 开发测试实验室中创建实验室的活动日志警报（例如：创建 VM 或删除 VM 时）。

## <a name="create-alerts"></a>创建警报
在此示例中，将通过向订阅所有者发送电子邮件的操作为实验室中的所有管理操作创建警报。 

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 在 Azure 门户的搜索栏中，键入 Monitor，然后从结果列表中选择“监视器”。 

    :::image type="content" source="./media/activity-logs/search-monitor.png" alt-text="搜索监视器":::        
1. 在左侧菜单上选择“警报”，然后在工具栏上选择“新建警报规则”。 

    :::image type="content" source="./media/activity-logs/alerts-page.png" alt-text="“警报”页":::    
1. 在“创建警报规则”页上，单击“选择资源” 。 

    :::image type="content" source="./media/activity-logs/select-resource-link.png" alt-text="选择警报的资源":::        
1. 针对“按资源类型筛选”选择“开发测试实验室”，在列表中选择实验室，然后选择“完成”。

    :::image type="content" source="./media/activity-logs/select-lab-resource.png" alt-text="选择实验室作为资源":::
1. 返回“创建警报规则”页，单击“选择条件” 。 

    :::image type="content" source="./media/activity-logs/select-condition-link.png" alt-text="选择条件链接":::    
1. 在“配置信号逻辑”页上，选择开发测试实验室支持的信号。 

    :::image type="content" source="./media/activity-logs/select-signal.png" alt-text="选择信号":::
1. 按“事件级别”（详细的、信息性、警告、错误、严重、所有）、“状态”（失败、已启动、已成功）以及“启动事件的人”筛选。 
1. 选择“完成”，完成配置条件。 

    :::image type="content" source="./media/activity-logs/configure-signal-logic-done.png" alt-text="配置信号逻辑 - 完成":::
1. 你已为警报指定范围（实验室）和条件。 现在需要指定一个操作组，其中包含满足条件时要运行的操作。 返回“创建警报规则”页，选择“选择操作组”。 

    :::image type="content" source="./media/activity-logs/select-action-group-link.png" alt-text="选择操作组链接":::
1. 在工具栏上选择“创建操作组”链接。 

    :::image type="content" source="./media/activity-logs/create-action-group-link.png" alt-text="创建操作组链接":::
1. 在“添加操作组”页上，执行以下步骤：
    1. 为操作组输入“名称”。
    1. 为操作组输入“短名称”。 
    1. 选择要在其中创建警报的“资源组”。 
    1. 输入操作的名称。 
    1. 选择“操作类型”（在此示例中，操作类型为“通过电子邮件发送 Azure 资源管理器角色”）。 

        :::image type="content" source="./media/activity-logs/add-action-group.png" alt-text="“添加操作组”页":::
    1. 在“通过电子邮件发送 Azure 资源管理器角色”页上，选择角色。 在此示例中，它是“所有者”。 选择“确定”。  

        :::image type="content" source="./media/activity-logs/select-role.png" alt-text="选择角色":::            
    1. 在“添加操作组”页面上选择“确定” 。 
1. 现在，在“创建警报规则”页上输入警报规则的名称，然后选择“确定” 。 

    :::image type="content" source="./media/activity-logs/create-alert-rule-done.png" alt-text="创建警报规则 - 完成":::

## <a name="view-alerts"></a>查看警报 
1. 在此示例中，将看到所有管理操作的“警报”上的警报。 可能需要一段时间才能显示警报。 

    :::image type="content" source="./media/activity-logs/alerts.png" alt-text="屏幕捕获将在仪表板中显示警报。":::
1. 如果选择列中的数字（例如：“警报总数”），将看到已引发的警报。 

    :::image type="content" source="./media/activity-logs/all-alerts.png" alt-text="所有警报":::
1. 如果选择警报，将看到相关的详细信息。 

    :::image type="content" source="./media/activity-logs/alert-details.png" alt-text="警报详细信息":::
1. 在此示例中，还会收到一封包含内容的电子邮件，如以下示例所示： 

    :::image type="content" source="./media/activity-logs/alert-email.png" alt-text="警报电子邮件":::

## <a name="next-steps"></a>后续步骤
- 要了解有关使用不同操作类型创建操作组的详细信息，请参阅[在 Azure 门户中创建和管理操作组](../azure-monitor/alerts/action-groups.md)。
- 要了解有关活动日志的详细信息，请参阅 [Azure 活动日志](../azure-monitor/essentials/activity-log.md)。
- 要了解有关如何根据活动日志设置警报的信息，请参阅[根据活动日志发出警报](../azure-monitor/alerts/activity-log-alerts.md)。
