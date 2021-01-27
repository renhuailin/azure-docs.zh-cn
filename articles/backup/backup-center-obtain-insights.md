---
title: 使用备份中心获取见解
description: 了解如何分析历史趋势，并通过备份中心获取有关备份的更深入见解。
ms.topic: conceptual
ms.date: 09/01/2020
ms.openlocfilehash: c5f707ce3bc8d1172805ac14958a50bf59d1412d
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/27/2021
ms.locfileid: "98893687"
---
# <a name="obtain-insights-using-backup-center-preview"></a>使用备份中心 (预览获取见解) 

为了分析历史趋势并深入了解你的备份，备份中心提供了一个用于 [备份报表 (预览) ](configure-reports.md)的界面，该界面使用 [Azure Monitor 日志](../azure-monitor/platform/data-platform-logs.md) 和 [Azure 工作簿](../azure-monitor/platform/workbooks-overview.md)。 备份报表提供以下功能：

- 分配和预测需使用的云存储空间。

- 审核备份和还原。

- 确定不同粒度级别的关键趋势。

- 了解备份的成本优化机会。

## <a name="supported-scenarios"></a>支持的方案

- 备份报表 (预览) 当前不可用于 Azure Database for PostgreSQL 服务器备份。

- 有关支持的和不支持的方案的详细列表，请参阅 [支持矩阵](backup-center-support-matrix.md) 。

## <a name="get-started"></a>入门

### <a name="configure-your-vaults-to-send-data-to-a-log-analytics-workspace"></a>将保管库配置为将数据发送到 Log Analytics 工作区

[了解如何为保管库配置 "大规模诊断设置"](./configure-reports.md#get-started)

### <a name="view-backup-reports-in-the-backup-center-portal"></a>在备份中心门户中查看备份报表

选择 "备份中心" 中的 " **备份报表** " 菜单项将打开报表。 选择一个或多个 Log Analytics 工作区来查看和分析备份中的关键信息。

![备份中心的备份报表](./media/backup-center-obtain-insights/backup-center-backup-reports.png)

下面是可用的视图：

1. **摘要** -使用此选项卡可大致了解备份空间。 [了解详细信息](./configure-reports.md#summary)

1. **备份项** -使用此选项卡查看在备份项级别使用的云存储的信息和趋势。 [了解详细信息](./configure-reports.md#backup-items)

1. **使用情况** -使用此选项卡查看备份的按键帐单参数。 [了解详细信息](./configure-reports.md#usage)

1. **作业** -使用此选项卡可以查看作业的长时间运行趋势，例如每天失败的作业数，以及失败的原因的主要原因。 [了解详细信息](./configure-reports.md#jobs)

1. **策略** -使用此选项卡查看所有活动策略的相关信息，例如，关联的项目数和在给定策略中备份的项目所消耗的云存储总量。 [了解详细信息](./configure-reports.md#policies)

1. **优化** -使用此选项卡可查看备份的潜在成本优化机会。 [了解详细信息](./configure-reports.md#optimize)

## <a name="next-steps"></a>后续步骤

- [监视和操作备份](backup-center-monitor-operate.md)
- [控制备份场地](backup-center-govern-environment.md)
- [使用备份中心执行操作](backup-center-actions.md)