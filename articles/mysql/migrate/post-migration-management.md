---
title: 从本地 MySQL 到 Azure Database for MySQL 的迁移指南 - 迁移后管理
description: 成功完成迁移后，下一阶段是管理基于云的新数据工作负载资源。
ms.service: mysql
ms.subservice: migration-guide
ms.topic: how-to
author: arunkumarthiags
ms.author: arthiaga
ms.reviewer: maghan
ms.custom: ''
ms.date: 05/26/2021
ms.openlocfilehash: 69fd0da13bdfda32089f4cabac1fb99cdaeaee53
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/10/2021
ms.locfileid: "111970961"
---
# <a name="mysql-on-premises-to-azure-database-for-mysql-migration-guide-post-migration-management"></a>从本地 MySQL 到 Azure Database for MySQL 的迁移指南 - 迁移后管理

### <a name="monitoring-and-alerts"></a>监视和警报

成功完成迁移后，下一阶段是管理基于云的新数据工作负载资源。 管理操作包括控制平面活动和数据平面活动。 控制平面活动与 Azure 资源相关，而数据平面活动发生在 Azure 资源（在本文中为 MySQL）内部。

Azure Database for MySQL 可让用户使用基于 Azure 的工具（例如 [Azure Monitor](../../azure-monitor/overview.md)、[Log Analytics](../../azure-monitor/logs/design-logs-deployment.md) 和 [Azure Sentinel](../../sentinel/overview.md)）监视这两种类型的操作活动。 除了基于 Azure 的工具以外，还可将安全信息和事件管理 (SIEM) 系统配置为使用这些日志。

无论使用哪种工具来监视基于云的新工作负载，都需要创建警报，以便在出现任何可疑活动时向 Azure 和数据库管理员发出警告。 如果特定的警报事件具有妥善定义的修正路径，则警报可以触发自动化 [Azure Runbook](../../automation/automation-quickstart-create-runbook.md) 来解决该事件。

创建完全受监视环境的第一步是使 MySQL 日志数据能够流入到 Azure Monitor 中。 有关详细信息，请参阅[在 Azure 门户中配置和访问 Azure Database for MySQL 的审核日志](../howto-configure-audit-logs-portal.md)。

日志数据流动后，使用 [Kusto 查询语言 (KQL)](/azure/data-explorer/kusto/query/) 查询各种日志信息。 不熟悉 KQL 的管理员可在[此处](/azure/data-explorer/kusto/query/sqlcheatsheet)或 [Azure Monitor 中的日志查询入门](../../azure-monitor/logs/get-started-queries.md)页中找到 SQL 转 KQL 速查表。

例如，若要获取 Azure Database for MySQL 的内存使用率，请运行以下代码：

```
AzureMetrics
| where TimeGenerated \> ago(15m)
| limit 10
| where ResourceProvider == "MICROSOFT.DBFORMYSQL"
| where MetricName == "memory\_percent"
| project TimeGenerated, Total, Maximum, Minimum, TimeGrain, UnitName 
| top 1 by TimeGenerated
```
若要获取 CPU 使用率，请运行以下代码：

```
AzureMetrics
| where TimeGenerated \> ago(15m)
| limit 10
| where ResourceProvider == "MICROSOFT.DBFORMYSQL"
| where MetricName == "cpu\_percent"
| project TimeGenerated, Total, Maximum, Minimum, TimeGrain, UnitName 
| top 1 by TimeGenerated
```
创建 KQL 查询后，基于这些查询创建[日志警报](../../azure-monitor/alerts/alerts-unified-log.md)。

### <a name="server-parameters"></a>服务器参数

在迁移过程中，可能已修改本地[服务器参数](../concepts-server-parameters.md)以支持快速流出。 此外，还已修改 Azure Database for MySQL 参数以支持快速流入。 迁移后，应将 Azure 服务器参数设置回到其原始的本地工作负载优化值。

但是，请务必检查服务器参数并根据工作负载和环境适当地对其做出更改。 某些非常适合本地环境的值不一定最适合基于云的环境。 此外，在规划将当前本地参数迁移到 Azure 时，请验证实际上是否可以设置这些参数。

不允许在 Azure Database for MySQL 中修改某些参数。

### <a name="powershell-module"></a>PowerShell 模块

可以使用 Azure 门户和 Windows PowerShell 管理 Azure Database for MySQL。 若要开始使用 PowerShell，请使用以下 PowerShell 命令安装适用于 MySQL 的 Azure PowerShell cmdlet：

`Install-Module -Name Az.MySql`

安装模块后，请参考如下教程，了解可以通过哪些方式编写管理活动的脚本：

  - [教程：使用 PowerShell 设计 Azure Database for MySQL](../tutorial-design-database-using-powershell.md)

  - [如何使用 PowerShell 来备份和还原 Azure Database for MySQL 服务器](../howto-restore-server-powershell.md)

  - [使用 PowerShell 在 Azure Database for MySQL 中配置服务器参数](../howto-configure-server-parameters-using-powershell.md)

  - [使用 PowerShell 实现 Azure Database for MySQL 服务器存储的自动增长](../howto-auto-grow-storage-powershell.md)

  - [如何使用 PowerShell 在 Azure Database for MySQL 中创建和管理只读副本](../howto-read-replicas-powershell.md)

  - [使用 PowerShell 重启 Azure Database for MySQL 服务器](../howto-restart-server-powershell.md)

### <a name="azure-database-for-mysql-upgrade-process"></a>Azure Database for MySQL 升级过程

由于 Azure Database for MySQL 是一个 PaaS 产品/服务，因此操作系统或 MySQL 软件的更新管理不由管理员负责。 但必须注意，升级过程可能是随机的，并且在部署时会停止 MySQL 服务器工作负载。 请针对这些停机时间做好规划，在特定实例进入维护模式时将工作负载重新路由到只读副本。

> [!NOTE]
> 这种样式的故障转移体系结构可能需要应用程序数据层进行了更改才能支持此类故障转移方案。 如果将只读副本作为只读副本进行维护而不将其提升，则应用程序只能读取数据，当任何操作尝试将信息写入数据库时，应用程序可能会失败。

在安装更新或关键安全补丁之前的 72 小时，[计划内维护通知](../concepts-monitoring.md#planned-maintenance-notification)功能就会通知资源所有者。 数据库管理员可能需要将计划内和计划外维护通知给应用程序用户。

> [!NOTE]
> Azure Database for MySQL 维护通知极其重要。 数据库维护可能会将数据库和连接的应用程序关闭一段时间。

### <a name="wwi-scenario"></a>WWI 方案

WWI 决定利用 Azure 活动日志，并使 MySQL 日志能够流入 [Log Analytics 工作区](../../azure-monitor/logs/design-logs-deployment.md)。 此工作区已配置为 [Azure Sentinel](../../sentinel/index.yml) 的一部分，因此会显示任何[威胁分析](/azure/mysql/concepts-data-access-and-security-threat-protection)事件，并且会创建事件。

MySQL DBA 安装了 [Azure Database for MySQL Azure PowerShell cmdlet](../quickstart-create-mysql-server-database-using-azure-powershell.md) 以自动管理 MySQL Server，而无需每次都要登录到 Azure 门户。

### <a name="management-checklist"></a>管理查检表

  - 针对 CPU 和内存等常用指标创建资源警报。

  - 确保在迁移后为目标数据工作负载配置服务器参数。

  - 编写常见管理任务的脚本。

  - 针对升级和修补等维护事件设置通知。 必要时通知用户。  


> [!div class="nextstepaction"]  
> [优化](./optimization.md)