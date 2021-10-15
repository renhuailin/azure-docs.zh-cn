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
ms.openlocfilehash: 7c982c0cb94ed5cf4a473265b1b414edbe0295a0
ms.sourcegitcommit: 03e84c3112b03bf7a2bc14525ddbc4f5adc99b85
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/03/2021
ms.locfileid: "129400527"
---
# <a name="configure-diagnostic-settings-and-a-workspace"></a>配置诊断设置和工作区

为数据工厂创建或添加诊断设置。

1. 在 Azure 门户中，转到“监视”。 选择“设置” > “诊断设置” 。

1. 选择要为其设置诊断设置的数据工厂。

1. 如果所选数据工厂不存在任何设置，则系统会提示你创建设置。 选择“启用诊断”。

   :::image type="content" source="media/data-factory-monitor-oms/monitor-oms-image1.png" alt-text="显示创建诊断设置（如果不存在设置）的屏幕截图。":::

   如果数据工厂中存在现有设置，你将看到数据工厂中已配置的设置列表。 选择“添加诊断设置”。

   :::image type="content" source="media/data-factory-monitor-oms/add-diagnostic-setting.png" alt-text="显示添加诊断设置（如果存在设置）的屏幕截图。":::

1. 为设置指定名称，选择“发送到 Log Analytics”，然后从“Log Analytics 工作区”中选择一个工作区。

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

      你可以选择与工作负荷相关的各种日志，以发送到 Log Analytics 表。 例如： 
        - 如果你根本不使用 SQL Server Integration Services (SSIS)，则无需选择任何 SSIS 日志。 
        - 如果要记录 SSIS 集成运行时 (IR) 启动、停止或维护操作，请选择 SSIS IR 日志。 
        - 如果通过 SQL Server Management Studio、SQL Server 代理或其他指定工具上的 T-SQL 调用 SSIS 包执行，请选择 SSIS 包日志。 
        - 如果通过数据工厂管道中的“执行 SSIS 包”活动调用 SSIS 包执行，请选择所有日志。

    * 如果选择“所有指标”，则可以使用各种数据工厂指标来监视或发出警报。 这些指标包含数据工厂活动、管道和触发器运行的指标，以及 SSIS IR 操作和 SSIS 包执行的指标。

   :::image type="content" source="media/data-factory-monitor-oms/monitor-oms-image2.png" alt-text="显示为设置命名并选择 Log Analytics 工作区的屏幕截图。":::

    > [!NOTE]
    > 由于 Azure 日志表的列数不能超过 500，因此我们强烈建议选择“资源特定”模式。 有关详细信息，请参阅 [Azure 诊断日志引用](/azure/azure-monitor/reference/tables/azurediagnostics)。

1. 选择“保存”。

几分钟后，新设置将出现在此数据工厂的设置列表中。 生成新的事件数据后，诊断日志将立即流式传输到该工作区。 发出事件后可能需要最多 15 分钟的时间该事件才会出现在 Log Analytics 中。

## <a name="next-steps"></a>后续步骤

[通过 Azure Monitor REST API 设置诊断日志](monitor-logs-rest.md)
