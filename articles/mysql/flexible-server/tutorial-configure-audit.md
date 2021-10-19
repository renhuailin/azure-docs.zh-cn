---
title: 教程：审核 Azure Database for MySQL 灵活服务器
description: 教程：审核 Azure Database for MySQL 灵活服务器
author: SudheeshGH
ms.author: sunaray
ms.service: mysql
ms.topic: tutorial
ms.date: 10/01/2021
ms.openlocfilehash: b4c8206071ce71cc29a9de45b46fa05c40f9cd91
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2021
ms.locfileid: "129621192"
---
# <a name="tutorial-auditing-for-azure-database-for-mysql--flexible-server"></a>教程：审核 Azure Database for MySQL 灵活服务器
[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

用户可在 Azure Database for MySQL 灵活服务器中配置审核日志。 还可使用审核日志来跟踪数据库级活动，包括连接、管理、DDL 和 DML 事件。 这些类型的日志通常用于符合性目的。 数据库审核通常用于：
* 为在特定架构、表、行或受影响的特定内容内发生的所有操作负责
* 根据用户（或其他人）的责任，防止其执行不适当的操作
* 调查可疑活动
* 监视和收集有关特定数据库活动的数据
 
在本教程中，你将了解如何使用 MySQL 审核日志、Log Analytics 工具或工作簿模板直观显示 Azure Database for MySQL 灵活服务器的审核信息。 

## <a name="prerequisites"></a>先决条件
- 你需要创建一个 Azure Database for MySQL 灵活服务器实例。 有关分步过程，请参阅[创建 Azure Database for MySQL 灵活服务器实例](./quickstart-create-server-portal.md)
- 需要创建 Log Analytics 工作区。 有关分步过程，请参阅[创建 Log Analytics 工作区](../../azure-monitor/logs/quick-create-workspace.md)

在本教程中，将了解如何：
>[!div class="checklist"]
> * 通过门户或使用 Azure CLI 配置审核
> * 设置诊断
> * 使用 Log Analytics 查看审核日志 
> * 使用工作簿查看审核日志 

## <a name="configure-auditing-from-portal"></a>从门户配置审核 


1. 登录到 [Azure 门户](https://portal.azure.com/)。

1. 选择灵活服务器。

1. 在侧栏的“设置”部分，选择“服务器参数”。 
    :::image type="content" source="./media/tutorial-configure-audit/server-parameters.png" alt-text="服务器参数":::

1. 将 **audit_log_enabled** 参数更新为 ON。
    :::image type="content" source="./media/tutorial-configure-audit/audit-log-enabled.png" alt-text="启用审核日志":::

1. 通过更新 **audit_log_events** 参数，选择要记录的 [事件类型](concepts-audit-logs.md#configure-audit-logging)。
    :::image type="content" source="./media/tutorial-configure-audit/audit-log-events.png" alt-text="审核日志事件":::

1. 通过更新 audit_log_exclude_users 和 audit_log_include_users 参数，添加任何要包括在日志中或从日志中排除的 MySQL 用户 。 通过提供 MySQL 用户名来指定用户。
    :::image type="content" source="./media/tutorial-configure-audit/audit-log-exclude-users.png" alt-text="审核日志排除用户":::

1. 更改参数之后，可以单击“保存”。 也可以放弃所做的更改。
    :::image type="content" source="./media/tutorial-configure-audit/save-parameters.png" alt-text="保存":::



## <a name="configure-auditing--from-azure-cli"></a>从 Azure CLI 配置审核
 
若要使用 Azure CLI 执行上述操作，可使用 CLI 为服务器启用并配置审核 

```azurecli
# Enable audit logs
az mysql flexible-server parameter set \
--name audit_log_enabled \
--resource-group myresourcegroup \
--server-name mydemoserver \
--value ON
```


## <a name="set-up-diagnostics"></a>设置诊断

审核日志与 Azure Monitor 诊断设置集成，你便可以通过管道将日志传输到 Azure Monitor 日志、事件中心或 Azure 存储。

1. 在侧栏的“监视”部分，选择“诊断设置” 。

1. 单击“+ 添加诊断设置”:::image type="content" source="./media/tutorial-configure-audit/add-diagnostic-setting.png" alt-text="添加诊断设置":::

1. 提供诊断设置名称。

1. 指定发送审核日志的目标（Log Analytics 工作区、存储帐户和/或事件中心）。
    >[!Note]
    > 对于本教程的范围，我们需要将慢查询日志发送到 Log Analytics 工作区
    
1. 选择“MySqlAuditLogs”作为日志类型。
    :::image type="content" source="./media/tutorial-configure-audit/configure-diagnostic-setting.png" alt-text="配置诊断设置":::

1. 配置可以通过管道向其传输审核日志的数据接收器以后，即可单击“保存”。
    :::image type="content" source="./media/tutorial-configure-audit/save-diagnostic-setting.png" alt-text="保存诊断设置":::

    >[!Note]
    >在配置诊断设置之前，应创建数据接收器（Log Analytics 工作区、存储帐户或事件中心）。 可在配置的数据接收器（Log Analytics 工作区、存储帐户或事件中心）中访问慢查询日志。最多可能需要 10 分钟才能显示日志。

## <a name="view-audit-logs-using-log-analytics"></a>使用 Log Analytics 查看审核日志 

导航到“监视”部分下的“日志” 。 关闭“查询”窗口。  

:::image type="content" source="./media/tutorial-configure-audit/log-query.png" alt-text="Log Analytics 的屏幕截图":::

在查询窗口中，可写入要执行的查询。  这里我们已使用查询来查找特定服务器上的汇总审核事件

```kusto
AzureDiagnostics
    |where Category =='MySqlAuditLogs' 
    |project TimeGenerated, Resource, event_class_s, event_subclass_s, event_time_t, user_s ,ip_s , sql_text_s 
    |summarize count() by event_class_s,event_subclass_s 
    |order by event_class_s 
```
  
:::image type="content" source="./media/tutorial-configure-audit/audit-query.png" alt-text="Log Analytics 查询的屏幕截图":::

## <a name="view-audit-logs-using-workbooks"></a>使用工作簿查看审核日志 
 
用于审核的工作簿模板需要我们创建诊断设置以发送平台日志。 

1.  若要发送平台日志，请在“Azure Monitor”菜单中单击“活动日志”，然后单击“诊断设置”。 

    :::image type="content" source="./media/tutorial-configure-audit/activity-diagnostics.png" alt-text="“诊断设置”的屏幕截图":::

2.  使用“添加新设置”或“编辑设置”，以编辑现有设置。 每个设置最多只能包含一个目标类型。

    :::image type="content" source="./media/tutorial-configure-audit/activity-settings-diagnostic.png" alt-text="在“诊断设置”中进行选择的屏幕截图":::

    >[!Note]
    >可在配置的数据接收器（Log Analytics 工作区、存储帐户和事件中心）中访问慢查询日志。最多可能需要 10 分钟才能显示日志。

3.  在 Azure 门户上，导航到 Azure Database for MySQL 灵活服务器的“监视”边栏选项卡，然后选择“工作簿” 。
4.  你应该能够看到模板。 选择“审核” 

    :::image type="content" source="./media/tutorial-configure-audit/monitor-workbooks.png" alt-text="工作簿模板的屏幕截图":::
    
你将能够看到以下可视化效果 
>[!div class="checklist"]
> * 对服务执行的管理操作
> * 审核摘要
> * 审核连接事件摘要
> * 审核连接事件
> * 表访问摘要
> * 已识别的错误


:::image type="content" source="./media/tutorial-configure-audit/admin-events.png" alt-text="工作簿模板管理事件的屏幕截图":::

:::image type="content" source="./media/tutorial-configure-audit/audit-summary.png" alt-text="工作簿模板审核摘要事件的屏幕截图":::

>[!Note]
> * 还可以编辑这些模板，并根据要求对其进行自定义。 有关详细信息，请参阅 [Azure Monitor 工作簿概述 - Azure Monitor](../../azure-monitor/visualize/workbooks-overview.md#editing-mode)
> * 若想快速查看，可将工作簿或 Log Analytics 查询固定到仪表板。 有关详细信息，请参阅[在 Azure 门户中创建仪表板 - Azure 门户](../../azure-portal/azure-portal-dashboards.md) 

“对服务执行的管理操作”可提供针对服务执行的活动的详细信息。 它有助于确定对订阅中的资源执行任何写入操作（PUT、POST、DELETE）的内容、人员和时间。 

其他可视化效果可帮助你获取数据库活动的详细信息。 数据库安全性由四部分组成。 它们是服务器安全性、数据库连接、表访问控制和限制数据库访问。 服务器安全性负责防止未经授权的人员访问数据库。 对于数据库连接，管理员还应检查对数据库进行的更新是否由已授权的人员完成。 限制数据库访问对于在 Internet 中上传了其数据库的用户尤其重要。 这将有助于防止任何外部源进入数据库或获取对数据库的访问权限。 

## <a name="next-steps"></a>后续步骤
- 参阅 [Azure Monitor 工作簿入门](../../azure-monitor/visualize/workbooks-overview.md#visualizations)，详细了解工作簿丰富的可视化效果选项。
- 详细了解[审核日志](concepts-audit-logs.md)