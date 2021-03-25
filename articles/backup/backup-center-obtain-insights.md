---
title: 使用备份中心获取见解
description: 了解如何使用备份中心分析历史趋势和更深入地了解备份。
ms.topic: conceptual
ms.date: 09/01/2020
ms.openlocfilehash: c48173749a9b47be7eeb906e9f8eec716e0cb200
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "102505999"
---
# <a name="obtain-insights-using-backup-center"></a>使用备份中心获取见解

为了分析历史趋势和更深入地了解备份，备份中心为[备份报告](configure-reports.md)提供了一个接口，该接口使用 [Azure Monitor 日志](../azure-monitor/logs/data-platform-logs.md)和 [Azure Workbook](../azure-monitor/visualize/workbooks-overview.md)。 备份报告提供以下功能：

- 分配和预测需使用的云存储空间。

- 审核备份和还原。

- 确定不同粒度级别的关键趋势。

- 深入了解备份的成本优化机会。

## <a name="supported-scenarios"></a>支持的方案

- 备份报告当前不可用于 Azure Database for PostgreSQL 服务器备份。

- 有关支持和不支持的方案的详细列表，请参阅[支持矩阵](backup-center-support-matrix.md)。

## <a name="get-started"></a>入门

### <a name="configure-your-vaults-to-send-data-to-a-log-analytics-workspace"></a>将保管库配置为将数据发送到 Log Analytics 工作区

[了解如何为保管库配置大规模诊断设置](./configure-reports.md#get-started)

### <a name="view-backup-reports-in-the-backup-center-portal"></a>在备份中心门户中查看备份报告

在备份中心中选择“备份报告”菜单项将打开报告。 选择一个或多个 Log Analytics 工作区以查看和分析有关备份的关键信息。

![备份中心中的备份报告](./media/backup-center-obtain-insights/backup-center-backup-reports.png)

下面是可用的视图：

1. **摘要** - 此选项卡用于大致了解备份资产。 [了解详细信息](./configure-reports.md#summary)

2. **备份项** - 此选项卡用于查看有关在备份项级别使用的云存储空间的信息和趋势。 [了解详细信息](./configure-reports.md#backup-items)

3. **使用情况** - 此选项卡用于查看备份的关键计费参数。 [了解详细信息](./configure-reports.md#usage)

4. **作业** - 此选项卡用于查看作业的长时间运行趋势，例如每天失败的作业数和导致作业失败的主要原因。 [了解详细信息](./configure-reports.md#jobs)

5. **策略** - 此选项卡用于查看有关所有活动策略的信息，例如关联项的数量，以及按照给定策略备份的项所使用的云存储空间总量。 [了解详细信息](./configure-reports.md#policies)

6. **优化** - 此选项卡用于查看备份的潜在成本优化机会。 [了解详细信息](./configure-reports.md#optimize)

7. **策略遵守** - 此选项卡用于了解每个备份实例每天是否至少有一次成功备份。 [了解详细信息](./configure-reports.md#policy-adherence)

还可以使用[电子邮件报告](backup-reports-email.md)功能为这些报告配置电子邮件。

## <a name="next-steps"></a>后续步骤

- [监视和操作备份](backup-center-monitor-operate.md)
- [备份资产](backup-center-govern-environment.md)
- [使用备份中心执行操作](backup-center-actions.md)