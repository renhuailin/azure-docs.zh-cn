---
title: 活动日志
description: 本文介绍查看 Azure 开发测试实验室的活动日志的步骤。
ms.topic: how-to
ms.date: 07/10/2020
ms.openlocfilehash: e5c7453a1cc4959f6517050ed4c1896890b2610b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128654262"
---
# <a name="view-activity-logs-for-labs-in-azure-devtest-labs"></a>查看 Azure 开发测试实验室中实验室的活动日志 
在创建一个或多个实验室后，可能需要监视实验室的访问、修改和管理情况、时间以及操作者。 Azure 开发测试实验室使用 Azure Monitor（具体而言是活动日志）提供针对实验室的此类操作信息。 

本文介绍如何查看 Azure 开发测试实验室中实验室的活动日志。

## <a name="view-activity-log-for-a-lab"></a>查看实验室的活动日志

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 选择“所有服务”，然后在“DEVOPS”部分中选择“开发测试实验室”。 如果在“DEVOPS”部分中选择了“开发测试实验室”旁边的 *（星号）， 此操作会将“开发测试实验室”添加到左侧导航菜单中，以便你下次可以轻松访问它。 然后，可以在左侧导航菜单中选择“开发测试实验室”。

    ![所有服务 - 选择“开发测试实验室”](./media/devtest-lab-create-lab/all-services-select.png)
1. 在实验室列表中，选择实验室。
1. 在实验室的主页上，在左侧菜单上选择“配置和策略”。 

    :::image type="content" source="./media/activity-logs/configuration-policies-link.png" alt-text="在左侧菜单中选择“配置和策略”":::
1. 在“配置和策略”页上，选择“管理”下的左侧菜单中的“活动日志”  。 此时应该能看到针对实验室完成的操作的条目。 

    :::image type="content" source="./media/activity-logs/activity-log.png" alt-text="活动日志":::    
1. 选择事件以查看其详细信息。 在“摘要”页上，可以看到操作名称、时间戳以及执行操作的人员等信息。 
    
    :::image type="content" source="./media/activity-logs/stop-vm-event.png" alt-text="停止 VM 事件 - 摘要":::        
1. 切换到“JSON”选项卡以查看更多详细信息。 在下面的示例中，可以看到 VM 的名称以及在 VM 上完成的操作（停止）。

    :::image type="content" source="./media/activity-logs/stop-vm-event-json.png" alt-text="停止 VM 事件 - JSON":::           
1. 切换到“更改历史记录(预览)”选项卡以查看更改的历史记录。 在下面的示例中，可以看到已在 VM 上进行的更改。 

    :::image type="content" source="./media/activity-logs/change-history.png" alt-text="停止 VM 事件 - 更改历史记录":::             
1. 选择更改历史记录列表中的更改，查看有关此更改的更多详细信息。 

    :::image type="content" source="./media/activity-logs/change-details.png" alt-text="停止 VM 事件 - 更改详细信息":::             

有关活动日志的详细信息，请参阅 [Azure 活动日志](../azure-monitor/essentials/activity-log.md)。

## <a name="next-steps"></a>后续步骤

- 若要了解如何针对活动日志设置警报，请参阅[创建警报](create-alerts.md)。
- 若要了解有关活动日志的详细信息，请参阅 [Azure 活动日志](../azure-monitor/essentials/activity-log.md)。
