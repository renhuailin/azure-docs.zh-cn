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
ms.openlocfilehash: df132c35ebb04596d91720431f5b08cb88e2abd9
ms.sourcegitcommit: 3af12dc5b0b3833acb5d591d0d5a398c926919c8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/11/2021
ms.locfileid: "98104177"
---
# <a name="connect-azure-sql-database-diagnostics-and-auditing-logs"></a>连接 Azure SQL 数据库诊断和审核日志

Azure SQL 是一种完全托管的平台即服务 (PaaS) 数据库引擎，可处理大多数数据库管理功能，例如升级、修补、备份和监视，无需用户参与。 

利用 Azure SQL 数据库连接器，你可以将数据库审核和诊断日志流式传输到 Sentinel，使你能够在所有实例中持续监视活动。

- 连接诊断日志可将不同数据类型的数据库诊断日志发送到 "Sentinel" 工作区。

- 连接审核日志使你可以从服务器级别的所有 Azure SQL 数据库流式传输安全审核日志。

了解有关 [监视 AZURE SQL 数据库](../azure-sql/database/metrics-diagnostic-telemetry-logging-streaming-export-configure.md)的详细信息。

## <a name="prerequisites"></a>必备知识

- 你必须具有 Azure Sentinel 工作区的读取和写入权限。

- 若要连接审核日志，您必须对 Azure SQL Server 审核设置具有读取和写入权限。

## <a name="connect-to-azure-sql-database"></a>连接到 Azure SQL 数据库
    
1. 在 Azure Sentinel 导航菜单中，选择 " **数据连接器**"。

1. 从数据连接器库中选择 " **AZURE SQL 数据库** "，然后选择预览窗格中的 " **打开连接器" 页面**  。

1. 在 "连接器" 页的 " **配置** " 部分中，记下可以连接的两类日志。

### <a name="connect-diagnostics-logs"></a>连接诊断日志

1. 在 " **诊断日志**" 下， **手动启用每个 Azure SQL 数据库上的诊断日志**。

1. 选择 " **打开 AZURE sql >** " 链接，打开 " **azure sql** 资源" 边栏选项卡。

1. **(可选)** 若要轻松查找数据库资源，请选择顶部 "筛选器" 栏上的 " **添加筛选器** "。
    1. 在 " **筛选器** " 下拉列表中，选择 " **资源类型**"。
    1. 在 " **值** " 下拉列表中，取消选择 " **全选**"，然后选择 " **SQL 数据库**"。
    1. 单击“应用”。
    
1. 选择要将其诊断日志发送到 Azure Sentinel 的数据库资源。

    > [!NOTE]
    > 对于需要收集其日志的每个数据库资源，必须从此步骤开始重复此过程。

1. 从所选数据库的 "资源" 页上，在 "导航" 菜单上的 " **监视** " 下，选择 " **诊断设置**"。

    1. 选择表底部的 " **+ 添加诊断设置** " 链接。

    1. 在 " **诊断设置** " 屏幕的 "  **诊断设置名称** " 字段中输入一个名称。
    
    1. 在 " **目标详细信息** " 列中，将 " **发送到 Log Analytics 工作区** " 复选框标记。 其中将显示两个新字段。 选择相关 **订阅** ，并 **Log Analytics 工作区** (Azure Sentinel 所在的位置) 。

    1. 在 " **类别详细信息** " 列中，标记要引入的日志和指标类型的复选框。 建议选择 " **日志** 和 **指标**" 下的所有可用类型。

    1. 选择屏幕顶部的 " **保存** "。

- 或者，你可以使用提供的 **PowerShell 脚本** 来连接诊断日志。
    1. 在 **诊断日志** 下，展开 " **由 PowerShell 脚本启用**"。

    1. 复制代码块并粘贴 PowerShell。

### <a name="connect-audit-logs"></a>连接审核日志

1. 在 " **审核日志 (预览")** 上，在 **服务器级别) 上，展开 "在所有 Azure SQL (数据库上启用审核日志**"。

1. 选择 " **打开 AZURE sql >** " 链接以打开 " **SQL server** 资源" 边栏选项卡。

1. 选择要将其审核日志发送到 Azure Sentinel 的 SQL server。

    > [!NOTE]
    > 对于每个要收集其日志的服务器资源，必须从此步骤开始重复此过程。

1. 从所选服务器的 "资源" 页上，在 "导航" 菜单上的 " **安全性** " 下，选择 " **审核**"。

    1. 将 " **启用 AZURE SQL 审核** " 切换到 **"打开**"。

    1. 在 " **审核日志目标**" 下，选择 **Log Analytics (预览)**"。
    
    1. 从显示的工作区列表中，选择 Azure Sentinel 所在的工作区 () 。

    1. 选择屏幕顶部的 " **保存** "。

- 或者，你可以使用提供的 **PowerShell 脚本** 来连接诊断日志。
    1. 在 " **审核日志**" 下，展开 " **由 PowerShell 脚本启用**"。

    1. 复制代码块并粘贴 PowerShell。


> [!NOTE]
>
> 使用这个特定的数据连接器，在过去两周内引入数据时，数据连接器库中的连接状态 (指示器和数据类型) 名称旁的连接图标将显示为 " *已连接* (绿色) 。 两周后，如果没有数据引入，连接器将显示为 "已断开连接"。 数据经历的时间越多， *连接* 状态就返回。

## <a name="next-steps"></a>后续步骤
本文档介绍了如何将 Azure SQL 数据库诊断和审核日志连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats-built-in.md)。