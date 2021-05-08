---
title: 计划内维护事件的提前通知（预览版）
description: 在 Azure SQL 数据库的计划内维护之前获得通知。
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/02/2021
ms.openlocfilehash: 07f6267a14a4604e1a43dd1a1a9930d63a419336
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "101690883"
---
# <a name="advance-notifications-for-planned-maintenance-events-preview"></a>计划内维护事件的提前通知（预览版）
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

提前通知（预览版）可用于为[维护时段（预览版）](maintenance-window.md)配置的数据库。 使用提前通知，客户可以将通知配置为在任何计划事件之前最多 24 小时发送。

可以将通知配置为让你在计划内维护开始前 24 小时收到短信、电子邮件、Azure 推送通知和语音邮件。 在维护开始时以及在维护结束时，还会另外发送通知。

> [!Note]
> 虽然选择维护时段的功能可用于 Azure SQL 托管实例，但提前通知目前不可用于 Azure SQL 托管实例。

## <a name="create-an-advance-notification"></a>创建提前通知

提前通知可用于配置了维护时段的 Azure SQL 数据库。 

请完成以下步骤以启用通知。  

1. 转到[计划内维护](https://portal.azure.com/#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/plannedMaintenance)页，选择“运行状况警报”，然后选择“添加服务运行状况警报” 。

    :::image type="content" source="media/advance-notifications/health-alerts.png" alt-text="创建新的运行状况警报菜单选项":::

2. 在“操作”部分，选择“添加操作组” 。 

    :::image type="content" source="media/advance-notifications/add-action-group.png" alt-text="添加操作组菜单选项":::

3. 完成“创建操作组”表单，然后选择“下一步: 通知” 。  

    :::image type="content" source="media/advance-notifications/create-action-group.png" alt-text="创建操作组表单":::

1. 在“通知”选项卡上，选择“通知类型” 。 “电子邮件/短信/推送/语音”选项的灵活性最高，建议使用此选项。 选择笔形图标以配置通知。  

    :::image type="content" source="media/advance-notifications/notifications.png" alt-text="配置通知":::



   1. 完成打开的“添加或编辑通知”表单，然后选择“确定”： 

   2. “操作”和“标记”是可选的。 可在此处配置其他要触发的操作，或者使用标记对 Azure 资源进行分类和组织。 

   4. 查看“查看 + 创建”选项卡上的详细信息，然后选择“创建” 。 

7. 在选择“创建”后，会打开警报规则配置屏幕，并且操作组将会被选中。 为新警报规则提供名称，再为其选择资源组，然后选择“创建警报规则”。 

8. 再次单击“运行状况警报”菜单项，警报列表现在会包含新警报。 


已经完成全部设置。 在下次有计划内的 Azure SQL 维护事件时，你会收到提前通知。

## <a name="receiving-notifications"></a>接收通知

下表显示了你可能会收到的常规信息通知： 

|状态|说明|
|:---|:---|
|计划内部署| 在维护事件之前 24 小时收到。 计划在 DATE 的下午 5 点 - 上午 8 点（当地时间）维护 DB xyz。|
|正在进行 | 已开始对数据库 xyz 进行维护 ** 。| 
|**完成** | 数据库 xyz 的维护已完成。 |

下表显示了在进行维护时可能会发送的其他通知： 

|状态|说明|
|:---|:---|
|**扩展** | 数据库 xyz 的维护正在进行，但尚未完成。 维护会在下一个维护时段继续。| 
|**已取消**| 数据库 xyz 的维护已取消，稍后会重新进行计划。 |
|**已阻止**|在维护数据库 xyz 的过程中出现了问题。 我们会在恢复时通知你。| 
|**Resumed**|问题已解决，维护将在下一个维护时段继续。|


## <a name="next-steps"></a>后续步骤

- [维护时段](maintenance-window.md)
- [维护时段常见问题解答](maintenance-window-faq.yml)
- [Microsoft Azure 中的警报概述](../../azure-monitor/platform/alerts-overview.md)
- [向 Azure 资源管理器角色发送电子邮件](../../azure-monitor/platform/action-groups.md#email-azure-resource-manager-role)