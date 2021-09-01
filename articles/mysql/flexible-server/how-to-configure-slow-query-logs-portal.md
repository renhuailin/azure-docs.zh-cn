---
title: 配置慢查询日志 - Azure 门户 - Azure Database for MySQL（灵活服务器）
description: 本文介绍如何从 Azure 门户配置和访问 Azure Database for MySQL 灵活服务器中的慢查询日志。
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 9/21/2020
ms.openlocfilehash: e28e11c8addc83cfe913a28cb91d3bc0d4580a8e
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/30/2021
ms.locfileid: "122652771"
---
# <a name="configure-and-access-slow-query-logs-for-azure-database-for-mysql---flexible-server-using-the-azure-portal"></a>使用 Azure 门户配置和访问 Azure Database for MySQL（灵活服务器）的慢查询日志

[[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

> [!IMPORTANT]
> Azure Database for MySQL 灵活服务器当前以公共预览版提供。

可以从 Azure 门户配置、列出并下载 Azure Database for MySQL 灵活服务器[慢查询日志](concepts-slow-query-logs.md)。

## <a name="prerequisites"></a>先决条件

本文中的步骤要求具备[灵活服务器](quickstart-create-server-portal.md)。

## <a name="configure-logging"></a>配置日志记录

配置 MySQL 慢查询日志的访问权限。 

1. 登录到 [Azure 门户](https://portal.azure.com/)。

1. 选择灵活服务器。

1. 在侧栏的“设置”部分，选择“服务器参数”。 
   :::image type="content" source="./media/how-to-configure-slow-query-logs-portal/server-parameters.png" alt-text="服务器参数页。":::

1. 将 slow_query_log 参数更新为“启用” 。
   :::image type="content" source="./media/how-to-configure-slow-query-logs-portal/slow-query-log-enable.png" alt-text="启用慢查询日志。":::

1. 更改所需的任何其他参数（例如 `long_query_time`, `log_slow_admin_statements`). 请参阅[慢查询日志](./concepts-slow-query-logs.md#configure-slow-query-logging)文档获取更多参数。  
   :::image type="content" source="./media/how-to-configure-slow-query-logs-portal/long-query-time.png" alt-text="更新慢查询日志相关参数。":::

1. 选择“保存” 。 
   :::image type="content" source="./media/how-to-configure-slow-query-logs-portal/save-parameters.png" alt-text="保存慢查询日志参数。":::

在“服务器参数”  页上，可以通过关闭该页来返回到日志列表。

## <a name="set-up-diagnostics"></a>设置诊断

慢查询日志与 Azure Monitor 诊断设置集成，你便可以通过管道将日志传输到 Azure Monitor 日志、事件中心或 Azure 存储。

1. 在边栏中的“监视”  部分下，选择“诊断设置”   > “添加诊断设置”  。

   :::image type="content" source="./media/how-to-configure-slow-query-logs-portal/add-diagnostic-setting.png" alt-text="“诊断设置”选项的屏幕截图":::

1. 提供诊断设置名称。

1. 指定向哪些目标（存储帐户、事件中心或 Log Analytics 工作区）发送慢查询日志。

1. 选择 **MySqlSlowLogs** 作为日志类型。
    :::image type="content" source="./media/how-to-configure-slow-query-logs-portal/configure-diagnostic-setting.png" alt-text="“诊断设置配置”选项的屏幕截图":::

1. 配置可以通过管道向其传送慢查询日志的数据接收器后，选择“保存”。 
    :::image type="content" source="./media/how-to-configure-slow-query-logs-portal/save-diagnostic-setting.png" alt-text="“诊断设置配置”选项的屏幕截图，其中突出显示了“保存”":::

1. 可以通过在配置的数据接收器中浏览慢查询日志来对其进行访问。 最多需要等待 10 分钟的时间，这些日志就会出现。

如果通过管道将日志传输到 Azure Monitor 日志（Log Analytics），请参阅一些可用于分析的[示例查询](concepts-slow-query-logs.md#analyze-logs-in-azure-monitor-logs)。 

## <a name="next-steps"></a>后续步骤
<!-- - See [Access slow query Logs in CLI](howto-configure-server-logs-in-cli.md) to learn how to download slow query logs programmatically.-->
- 详细了解[慢查询日志](concepts-slow-query-logs.md)
- 有关参数定义和 MySQL 日志记录的详细信息，请参阅[日志](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html)上的 MySQL 文档。
- 了解[审核日志](concepts-audit-logs.md)
