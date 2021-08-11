---
title: 将所有 Azure SQL 数据库诊断和审核日志连接到 Azure Sentinel
description: 了解如何使用 Azure Policy 强制将 Azure SQL 数据库诊断日志和安全审核日志连接到 Azure Sentinel。
author: yelevin
manager: rkarlin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 04/21/2021
ms.author: yelevin
ms.openlocfilehash: ba4cefaca7225f25076efa5cdcb81de46aa5cd60
ms.sourcegitcommit: 5ce88326f2b02fda54dad05df94cf0b440da284b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/22/2021
ms.locfileid: "107891313"
---
# <a name="connect-azure-sql-database-diagnostics-and-auditing-logs"></a>连接 Azure SQL 数据库诊断和审核日志

Azure SQL 是一种完全托管的平台即服务 (PaaS) 数据库引擎，可在没有用户参与的情况下处理大部分数据库管理功能，例如升级、修补、备份和监视。 

通过 Azure SQL 数据库连接器，可将数据库的审核日志和诊断日志流式传输到 Azure Sentinel，从而能够持续监视所有实例中的活动。

- 通过连接诊断日志，可将不同数据类型的数据库诊断日志发送到 Azure Sentinel 工作区。

- 通过连接审核日志，可以从服务器级别的所有 Azure SQL 数据库流式传输安全审核日志。

详细了解 [Azure SQL 数据库诊断遥测](../azure-sql/database/metrics-diagnostic-telemetry-logging-streaming-export-configure.md)和 [Azure SQL 服务器审核](../azure-sql/database/auditing-overview.md)。

## <a name="prerequisites"></a>先决条件

- 你必须具有对 Azure Sentinel 工作区的读取和写入权限。

- 要连接审核日志，必须对 Azure SQL Server 审核设置具有读取和写入权限。

- 若要使用 Azure Policy 将日志流式处理策略应用到 Azure SQL 数据库和服务器资源，必须具有策略分配范围的“所有者”角色。

## <a name="connect-to-azure-sql-database"></a>连接到 Azure SQL 数据库

此连接器使用 Azure Policy 将单个 Azure SQL 日志流式处理配置应用到定义为某个范围的实例集合。 Azure SQL 数据库连接器向 Azure Sentinel 发送两种类型的日志：诊断日志（从 SQL 数据库发送）和审核日志（在 SQL 服务器级发送）。 可以在连接器页面左侧的“数据类型”下查看从 Azure SQL 数据库和服务器引入的日志类型。

1. 在 Azure Sentinel 导航菜单中，选择“数据连接器”。

1. 从数据连接器库中选择“Azure SQL 数据库”，然后在预览窗格中选择“打开连接器页面” 。

1. 在连接器页的“配置”部分，记下可以连接的两类日志。

### <a name="connect-diagnostics-logs"></a>选择诊断日志

1. 展开“从 Azure SQL 数据库大规模流式传输诊断日志”。

1. 选择“启动 Azure Policy 分配向导”按钮。

    策略分配向导随即打开，可在其中创建名为“部署 - 为 SQL 数据库配置诊断设置以将日志传送到 Log Analytics 工作区”的新策略。

    1. 在“基本信息”选项卡中，单击“作用域”下带三个点的按钮，选择你的订阅（或资源组）。 还可以添加说明。

    1. 在“参数”选项卡中，保留前两个设置。 从“Log Analytics 工作区”下拉列表中选择你的 Azure Sentinel 工作区。 其余的下拉字段表示可用的诊断日志类型。 将要引入的所有日志类型标记为“True”。

    1. 此策略将应用于将来添加的资源。 若也要对现有资源应用该策略，请选择“修正”选项卡，然后勾选“创建修正任务”复选框 。

    1. 在“查看 + 创建”选项卡中，单击“创建”   。 你的策略现已分配给所选的作用域。

### <a name="connect-audit-logs"></a>连接审核日志

1. 返回连接器页面，展开“在服务器级别从 Azure SQL 数据库大规模流式传输审核日志”。

1. 选择“启动 Azure Policy 分配向导”按钮。

    策略分配向导随即打开，可在其中创建名为“部署 - 为 SQL 数据库配置审核设置以将日志传送到 Log Analytics 工作区”的新策略。

    1. 在“基本信息”选项卡中，单击“作用域”下带三个点的按钮，选择你的订阅（或资源组）。 还可以添加说明。

    1. 在“参数”选项卡中，从“Log Analytics 工作区”下拉列表中选择 Azure Sentinel 工作区。 保留“效果”设置。

    1. 此策略将应用于将来添加的资源。 若也要对现有资源应用该策略，请选择“修正”选项卡，然后勾选“创建修正任务”复选框 。

    1. 在“查看 + 创建”选项卡中，单击“创建”   。 你的策略现已分配给所选的作用域。

> [!NOTE]
>
> 对于此特定数据连接器，仅当在过去 14 天内的某个时间点引入数据时，连接状态指示器（数据连接器库中的色带以及数据类型名称旁边的连接图标）才显示为“已连接”（绿色）。 一旦 14 天过去但未引入数据，连接器将显示为已断开连接。 当有更多数据引入的那一刻，连接器将重新变为“已连接”状态。

## <a name="next-steps"></a>后续步骤

在本文档中，你已学习如何使用 Azure Policy 将 Azure SQL 数据库诊断和审核日志连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：

- 了解如何[洞悉数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats-built-in.md)。
