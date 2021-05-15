---
title: 将 Azure SQL 数据库诊断和审核日志连接到 Azure Sentinel
description: 了解如何将 Azure SQL 数据库诊断日志和安全审核日志连接到 Azure Sentinel。
author: yelevin
manager: rkarlin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 01/06/2021
ms.author: yelevin
ms.openlocfilehash: a3a09ceffc75e2d396d7bd7aeedd97b7f2b6ec2b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "99807727"
---
# <a name="connect-azure-sql-database-diagnostics-and-auditing-logs"></a>连接 Azure SQL 数据库诊断和审核日志

Azure SQL 是一个完全托管的平台即服务 (PaaS) 数据库引擎，可在没有用户参与的情况下处理大部分的数据库管理功能，例如升级、修补、备份和监视。 

通过 Azure SQL 数据库连接器，可将数据库的审核日志和诊断日志流式传输到 Azure Sentinel，从而能够持续监视所有实例中的活动。

- 通过连接诊断日志，可将不同数据类型的数据库诊断日志发送到 Azure Sentinel 工作区。

- 通过连接审核日志，可以从服务器级别的所有 Azure SQL 数据库流式传输安全审核日志。

详细了解[监视 Azure SQL 数据库](../azure-sql/database/metrics-diagnostic-telemetry-logging-streaming-export-configure.md)。

## <a name="prerequisites"></a>先决条件

- 你必须具有对 Azure Sentinel 工作区的读取和写入权限。

- 要连接审核日志，必须对 Azure SQL Server 审核设置具有读取和写入权限。

## <a name="connect-to-azure-sql-database"></a>连接到 Azure SQL 数据库
    
1. 在 Azure Sentinel 导航菜单中，选择“数据连接器”。

1. 从数据连接器库中选择“Azure SQL 数据库”，然后在预览窗格中选择“打开连接器页面”。 

1. 在连接器页的“配置”部分，记下可以连接的两类日志。

### <a name="connect-diagnostics-logs"></a>选择诊断日志

1. 在“诊断日志”下，展开“手动启用每个 Azure SQL 数据库上的诊断日志”。

1. 选择“打开 Azure SQL”链接，打开“Azure SQL”资源边栏选项卡。

1. （可选）要轻松查找数据库资源，请选择顶部筛选器栏上的“添加筛选器”。
    1. 在“筛选器”下拉列表中，选择“资源类型”。
    1. 在“值”下拉列表中，取消选择“全选”，然后选择“SQL 数据库”。
    1. 单击“应用”。
    
1. 选择要将其诊断日志发送到 Azure Sentinel 的数据库资源。

    > [!NOTE]
    > 对于需要收集其日志的每个数据库资源，都必须从此步骤开始重复以下过程。

1. 在所选数据库的资源页上，从导航菜单的“监视”下，选择“诊断设置”"。

    1. 选择表底部的“+ 添加诊断设置”链接。

    1. 在“诊断设置”屏幕的“诊断设置名称”字段中输入一个名称。
    
    1. 在“目标详细信息”中，选中“发送到 Log Analytics 工作区”复选框。  其下将显示两个新字段。 选择相关“订阅”和“Log Analytics 工作区”（Azure Sentinel 驻留的位置）。

    1. 在“类别详细信息”列中，选中要引入的日志和指标类型的复选框。 建议选择日志和指标下的所有可用类型。

    1. 选择屏幕顶部的“保存”。

- 或者，可以使用提供的 PowerShell 脚本来连接诊断日志。
    1. 在“诊断日志”下，展开“通过 PowerShell 脚本启用”。

    1. 复制代码块并将其粘贴到 PowerShell 中。

### <a name="connect-audit-logs"></a>连接审核日志

1. 在“审核日志（预览版）”下，展开“启用所有 Azure SQL 数据库上的审核日志（服务器级别）”。

1. 选择“打开 Azure SQL >”链接，打开“SQL Server”资源边栏选项卡。

1. 选择要将其审核日志发送到 Azure Sentinel 的 SQL Server。

    > [!NOTE]
    > 对于需要收集其日志的每个服务器资源，都必须从此步骤开始重复以下过程。

1. 在所选服务器的资源页上，在导航菜单的“安全性”下，选择“审核”。

    1. 将“启用 Azure SQL 审核”切换到“开”。

    1. 在“审核日志目标”下，选择“Log Analytics（预览版）”。
    
    1. 从显示的工作区列表中，选择 Azure Sentinel 所在的工作区。

    1. 选择屏幕顶部的“保存”。

- 或者，可以使用提供的 PowerShell 脚本来连接诊断日志。
    1. 在“审核日志”下，展开“通过 PowerShell 脚本启用”。

    1. 复制代码块并将其粘贴到 PowerShell 中。


> [!NOTE]
>
> 对于这个特定的数据连接器，只有当数据在过去两周内的某个时间点被引入的情况下，连接状态指示器（数据连接器库中的色条和数据类型名称旁边的连接图标）才会显示为“已连接”（绿色）。 一旦两周过去但没有数据引入，连接器将显示为已断开。 当有更多数据引入的那一刻，连接器将重新变为“已连接”状态。

## <a name="next-steps"></a>后续步骤
在本文档中，你已学习如何将 Azure SQL 数据库诊断和审核日志连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats-built-in.md)。