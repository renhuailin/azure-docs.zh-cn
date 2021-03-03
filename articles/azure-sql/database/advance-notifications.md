---
title: 计划内维护事件 (预览) 前进通知
description: 在 Azure SQL 数据库或 Azure SQL 托管实例的计划内维护之前获取通知。
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
ms.openlocfilehash: 3f63a8d2f75a58a771d837128c2b2498c28948ef
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101662181"
---
# <a name="advance-notifications-for-planned-maintenance-events-preview"></a> (预览版的计划内维护事件的提前通知) 
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

 (预览) 的高级通知可用于为维护时段配置的数据库 [ (预览版) ](maintenance-window.md)。 使用提前通知，客户可以将通知配置为在任何计划事件提前发送到24小时。

可以配置通知，以便你可以在计划的维护在接下来的24小时内开始时获取文本、电子邮件、Azure 推送通知和 voicemails。 在开始维护和维护结束时，将发送其他通知。


## <a name="create-an-advance-notification"></a>创建提前通知

提前通知适用于已配置维护时段的 Azure SQL 数据库和托管实例。 

完成以下步骤以启用通知。  

1. 请在 [计划的维护](https://portal.azure.com/#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/plannedMaintenance) 页上，选择 " **运行状况警报**"，然后选择 " **添加服务运行状况警报**"。

    :::image type="content" source="media/advance-notifications/health-alerts.png" alt-text="&quot;创建新的运行状况警报&quot; 菜单选项":::

2. 在 " **操作** " 部分中，选择 " **添加操作组**"。 

    :::image type="content" source="media/advance-notifications/add-action-group.png" alt-text="添加操作组菜单选项":::

3. 完成 " **创建操作组** " 窗体，然后选择 " **下一步：通知**"。  

    :::image type="content" source="media/advance-notifications/create-action-group.png" alt-text="创建操作组窗体":::

1. 在 " **通知** " 选项卡上，选择 **通知类型**。 **电子邮件/短信/推送/语音** 选项提供最大的灵活性，建议使用此选项。 选择笔以配置通知。  

    :::image type="content" source="media/advance-notifications/notifications.png" alt-text="配置通知":::



   1. 完成打开的 " *添加或编辑" 通知* 窗体，然后选择 **"确定"**： 

   2. 操作和标记是可选的。 可在此处配置要触发的其他操作，或使用标记对 Azure 资源进行分类和组织。 

   4. 查看 " **查看 + 创建** " 选项卡上的详细信息，然后选择 " **创建**"。 

7. 选择 "创建" 后，将打开 "警报规则配置" 屏幕并选择 "操作组"。 为新的警报规则提供一个名称，然后为其选择资源组，然后选择 " **创建警报规则**"。 

8. 再次单击 " **运行状况警报** " 菜单项，警报列表现在包含新警报。 


已经完成全部设置。 下一次出现计划的 Azure SQL 维护事件时，你将收到提前通知。

## <a name="receiving-notifications"></a>接收通知

下表显示了你可能会收到的一般信息通知： 

|状态|说明|
|:---|:---|
|**计划的部署**| 在维护事件之前的24小时内收到。 维护计划于早晨8点 (本地时间) 对于 DB xyz 之间的时间间隔。|
|**正在进行** | 数据库 *xyz* 的维护   正在启动。| 
|**完成** | 数据库 *xyz* 的维护已完成。 |

下表显示了在进行维护时可能会发送的其他通知： 

|状态|说明|
|:---|:---|
|**扩展** | 正在进行维护，但没有为数据库 *xyz* 完成维护。 维护将在下一个维护时段继续。| 
|**已取消**| 数据库 *xyz* 的维护已取消，稍后将重新计划。 |
|**已阻止**|维护数据库 *xyz* 时出现问题。 我们会在恢复时通知你。| 
|**Resumed**|此问题已解决，维护将在下一个维护时段继续。|


## <a name="next-steps"></a>后续步骤

- [维护时段](maintenance-window.md)
- [维护时段常见问题解答](maintenance-window-faq.yml)
- [Microsoft Azure 中的警报概述](../../azure-monitor/platform/alerts-overview.md)
- [向 Azure 资源管理器角色发送电子邮件](../../azure-monitor/platform/action-groups.md#email-azure-resource-manager-role)