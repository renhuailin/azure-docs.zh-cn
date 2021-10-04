---
title: 配置诊断设置和工作区
description: 了解如何配置诊断设置和 Log Analytics 工作区以监视 Azure 数据工厂。
author: minhe-msft
ms.author: hemin
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: monitoring
ms.topic: conceptual
ms.date: 09/02/2021
ms.openlocfilehash: 5673dd881d96dc14922e8a9f9bedc0501d2f5d18
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124837726"
---
# <a name="configure-diagnostic-settings-and-workspace"></a>配置诊断设置和工作区

为数据工厂创建或添加诊断设置。

1. 在门户中，转到“监视”。 选择“设置” > “诊断设置”。

1. 选择要为其设置诊断设置的数据工厂。

1. 如果所选数据工厂不存在任何设置，则系统会提示你创建设置。 选择“启用诊断”。

   :::image type="content" source="media/data-factory-monitor-oms/monitor-oms-image1.png" alt-text="如果不存在任何设置，请创建一个诊断设置":::

   如果数据工厂中存在现有设置，你将看到数据工厂中已配置的设置列表。 选择“添加诊断设置”。

   :::image type="content" source="media/data-factory-monitor-oms/add-diagnostic-setting.png" alt-text="如果存在设置，则添加诊断设置":::

1. 为设置指定名称，选择“发送到 Log Analytics”，然后从 **Log Analytics 工作区** 中选择一个工作区。

    * 在“Azure 诊断”模式下，诊断日志流入 AzureDiagnostics 表中。

    * 在“特定于资源”模式下，来自 Azure 数据工厂的诊断日志流入下表中：
      - _ADFActivityRun_
      - _ADFPipelineRun_
      - _ADFTriggerRun_
      - _ADFSSISIntegrationRuntimeLogs_
      - _ADFSSISPackageEventMessageContext_
      - _ADFSSISPackageEventMessages_
      - _ADFSSISPackageExecutableStatistics_
      - _ADFSSISPackageExecutionComponentPhases_
      - _ADFSSISPackageExecutionDataStatistics_

      你可以选择与工作负荷相关的各种日志，以发送到 Log Analytics 表。 例如，如果你根本不使用 SQL Server Integration Services (SSIS)，就不需要选择任何 SSIS 日志。 如果要记录 SSIS Integration Runtime (IR) 的启动/停止/维护操作，则可以选择 SSIS IR 日志。 如果在 SQL Server Management Studio (SSMS)、SQL Server 代理或其他指定工具上通过 T-SQL 调用 SSIS 包执行，则可以选择 SSIS 包日志。 如果通过 ADF 管道中的“执行 SSIS 包”活动调用 SSIS 包执行，则可以选择所有日志。

    * 如果选择 AllMetrics，则可使用多种 ADF 指标来进行监视或发出警报，包括针对 ADF 活动、管道和触发器运行的指标以及针对 SSIS IR 操作和 SSIS 包执行的指标。

   :::image type="content" source="media/data-factory-monitor-oms/monitor-oms-image2.png" alt-text="命名设置并选择 log-analytics 工作区":::

    > [!NOTE]
    > 由于 Azure 日志表的列数不能超过 500，因此强烈建议选择“特定于资源”模式。 有关详细信息，请参阅 [Azure 诊断日志参考](/azure/azure-monitor/reference/tables/azurediagnostics)。

1. 选择“保存”。

几分钟后，新设置将出现在此数据工厂的设置列表中。 生成新的事件数据后，诊断日志将立即流式传输到该工作区。 发出事件后可能需要最多 15 分钟的时间该事件才会出现在 Log Analytics 中。

## <a name="next-steps"></a>后续步骤

[通过 Azure Monitor REST API 设置诊断日志](monitor-logs-rest.md)
