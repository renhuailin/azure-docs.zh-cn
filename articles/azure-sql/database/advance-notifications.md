---
title: 计划内维护事件的提前通知（预览版）
description: 每次发生 Azure SQL 数据库的计划内维护之前收到通知。
services: sql-database
ms.service: sql-db-mi
ms.subservice: service-overview
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: scott-kim-sql
ms.author: scottkim
ms.reviewer: mathoma, wiassaf
ms.date: 09/14/2021
ms.openlocfilehash: 89a3f86995d054573897202d2269e4f1f38821e2
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128631184"
---
# <a name="advance-notifications-for-planned-maintenance-events-preview"></a>计划内维护事件的提前通知（预览版）
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

提前通知（预览版）可用于配置为使用非默认[维护时段（预览版）](maintenance-window.md)的数据库。 使用提前通知，客户可以将通知配置为最多在发生任何计划事件前的 24 小时内发送。

可以将通知配置为让你在计划内维护开始前 24 小时收到短信、电子邮件、Azure 推送通知和语音邮件。 在维护开始和结束时，将发送其他通知。

无法为“系统默认”维护时段选项配置提前通知。 选择除“系统默认”以外的其他维护时段，以配置和启用“提前通知”。

> [!NOTE]
> 虽然选择维护时段的功能适用于 Azure SQL 托管实例，但提前通知目前不适用于 Azure SQL 托管实例。 

## <a name="create-an-advance-notification"></a>创建提前通知

提前通知适用于已配置维护时段的 Azure SQL 数据库。 

若要启用通知，请完成以下步骤。  

1. 转到[计划内维护](https://portal.azure.com/#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/plannedMaintenance)页，选择“运行状况警报”，然后选择“添加服务运行状况警报”。

    :::image type="content" source="media/advance-notifications/health-alerts.png" alt-text="“创建新的运行状况警报”菜单选项":::

2. 在“操作”部分中，选择“添加操作组”。 

    :::image type="content" source="media/advance-notifications/add-action-group.png" alt-text="“添加操作组”菜单选项":::

3. 填写“创建操作组”表单，然后选择“下一步: 通知”。  

    :::image type="content" source="media/advance-notifications/create-action-group.png" alt-text="“创建操作组”表单":::

1. 在“通知”选项卡上，选择“通知类型”。 “电子邮件/短信/推送/语音”选项的灵活性最高，建议使用此选项。 选择笔以配置通知。  

    :::image type="content" source="media/advance-notifications/notifications.png" alt-text="配置通知":::



   1. 填写打开的“添加或编辑通知”表单，然后选择“确定”： 

   2. 操作和标记是可选的。 可在此处配置要触发的其他操作，或使用标记对 Azure 资源进行分类和组织。 

   4. 查看“查看 + 创建”选项卡上的详细信息，然后选择“创建”。 

7. 选择“创建”后，将打开“警报规则配置”屏幕并选择操作组。 为新的警报规则提供名称，为其选择资源组，然后选择“创建警报规则”。 

8. 再次单击“运行状况警报”菜单项，警报列表现在包含新警报。 


已经完成全部设置。 下次发生计划内 Azure SQL 维护事件时，你将收到提前通知。

## <a name="receiving-notifications"></a>接收通知

下表显示了你可能会收到的一般信息通知： 

|状态|说明|
|:---|:---|
|**计划内部署**| 在发生维护事件前的 24 小时内收到。 数据库 xyz 的维护计划在下午 5 点到上午 8 点（本地时间）之间的日期范围内进行。|
|**正在进行** | 正在启动数据库 xyz **   的维护。| 
|**完成** | 数据库 xyz 的维护已完成。 |

下表显示了在进行维护时可能会发送的其他通知： 

|状态|说明|
|:---|:---|
|**扩展** | 正在进行维护，但数据库 xyz 的维护未完成。 会在下一个维护时段继续进行维护。| 
|**已取消**| 数据库 xyz 的维护已取消，稍后将重新计划。 |
|**已阻止**|数据库 xyz 的维护期间出现问题。 我们会在恢复时通知你。| 
|**Resumed**|此问题已解决，并且会在下一个维护时段继续进行维护。|


## <a name="next-steps"></a>后续步骤

- [维护时段](maintenance-window.md)
- [维护时段常见问题解答](maintenance-window-faq.yml)
- [Microsoft Azure 中的警报概述](../../azure-monitor/alerts/alerts-overview.md)
- [向 Azure 资源管理器角色发送电子邮件](../../azure-monitor/alerts/action-groups.md#email-azure-resource-manager-role)
