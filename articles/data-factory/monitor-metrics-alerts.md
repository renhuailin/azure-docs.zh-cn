---
title: 数据工厂指标和警报
description: 了解适用于监视数据 Azure 数据工厂的指标。
author: minhe-msft
ms.author: hemin
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: monitoring
ms.topic: conceptual
ms.date: 09/02/2021
ms.openlocfilehash: 53f12eed1e39eb035f5ec69c825ab5658b207b03
ms.sourcegitcommit: 03e84c3112b03bf7a2bc14525ddbc4f5adc99b85
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/03/2021
ms.locfileid: "129400508"
---
# <a name="data-factory-metrics-and-alerts"></a>数据工厂指标和警报

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Azure 数据工厂提供以下指标和警报来启用对服务的监视。

## <a name="data-factory-metrics"></a>数据工厂指标

使用 Azure Monitor 可以查看 Azure 工作负载的性能和运行状况。 最重要的 Monitor 数据类型是指标（也称为性能计数器）。 大多数 Azure 资源都会发出指标。 Monitor 提供多种方式来配置和使用这些指标，以便进行监视与故障排除。

以下是 Azure 数据工厂版本 2 发出的一些指标。

| **指标**                           | **指标显示名称**                  | **单位** | **聚合类型** | **说明**                |
|--------------------------------------|------------------------------------------|----------|----------------------|--------------------------------|
| ActivityCancelledRuns                 | 已取消的活动运行数指标           | 计数    | 总计                | 在一分钟时段内取消的活动运行总数。 |
| ActivityFailedRuns                   | 失败的活动运行数指标             | 计数    | 总计                | 在一分钟时段内失败的活动运行总数。 |
| ActivitySucceededRuns                | 成功的活动运行数指标          | 计数    | 总计                | 在一分钟时段内成功的活动运行总数。 |
| PipelineCancelledRuns                 | 已取消的管道运行数指标           | 计数    | 总计                | 在一分钟时段内取消的管道运行总数。 |
| PipelineFailedRuns                   | 失败的管道运行数指标             | 计数    | 总计                | 在一分钟时段内失败的管道运行总数。 |
| PipelineSucceededRuns                | 成功的管道运行数指标          | 计数    | 总计                | 在一分钟时段内成功的管道运行总数。 |
| TriggerCancelledRuns                  | 已取消的触发器运行数指标            | 计数    | 总计                | 在一分钟时段内取消的触发器运行总数。 |
| TriggerFailedRuns                    | 失败的触发器运行数指标              | 计数    | 总计                | 在一分钟时段内失败的触发器运行总数。 |
| TriggerSucceededRuns                 | 成功的触发器运行数指标           | 计数    | 总计                | 在一分钟时段内成功的触发器运行总数。 |
| SSISIntegrationRuntimeStartCancelled  | 已取消的 SSIS 集成运行时启动指标           | 计数    | 总计                | 在一分钟时段内取消的 SSIS 集成运行时启动总数。 |
| SSISIntegrationRuntimeStartFailed    | 失败的 SSIS 集成运行时启动指标             | 计数    | 总计                | 在一分钟时段内失败的 SSIS 集成运行时启动总数。 |
| SSISIntegrationRuntimeStartSucceeded | 成功的 SSIS 集成运行时启动指标          | 计数    | 总计                | 在一分钟时段内成功的 SSIS 集成运行时启动总数。 |
| SSISIntegrationRuntimeStopStuck      | 停滞的 SSIS 集成运行时停止指标               | 计数    | 总计                | 在一分钟时段内停滞的 SSIS 集成运行时停止总数。 |
| SSISIntegrationRuntimeStopSucceeded  | 成功的 SSIS 集成运行时停止指标           | 计数    | 总计                | 在一分钟时段内成功的 SSIS 集成运行时停止总数。 |
| SSISPackageExecutionCancelled         | 已取消的 SSIS 包执行指标  | 计数    | 总计                | 在一分钟时段内取消的 SSIS 包执行总数。 |
| SSISPackageExecutionFailed           | 失败的 SSIS 包执行指标    | 计数    | 总计                | 在一分钟时段内失败的 SSIS 包执行总数。 |
| SSISPackageExecutionSucceeded        | 已成功的 SSIS 包执行指标 | 计数    | 总计                | 在一分钟时段内成功的 SSIS 包执行总数。 |
| PipelineElapsedTimeRuns | 运行时间管道运行指标 | 计数 | 总计 | 在一分钟时段内管道运行的时间长于用户定义的预期持续时间的次数。 [（查看更多。）](tutorial-operationalize-pipelines.md) |

若要访问指标，请参阅 [Azure Monitor 数据平台](../azure-monitor/data-platform.md)中的说明。

> [!NOTE]
> 仅发出已完成的、已触发活动的和管道运行中的事件，PipelineElapsedTimeRuns 除外。 不会发出正在进行的运行和调试运行。 另一方面，将发出所有 SSIS 包执行事件，包括已完成和正在进行的事件，而无论使用何种调用方法。 例如，可通过 SQL Server Management Studio、SQL Server 代理或其他指定工具上的 T-SQL，或作为数据工厂管道中“执行 SSIS 包”活动的已触发运行或调试运行，在已启用 Azure 的 SQL Server Data Tools 上调用包执行。

## <a name="data-factory-alerts"></a>数据工厂警报

登录到 Azure 门户，然后选择“监视器” > “警报”以创建警报 。

:::image type="content" source="media/monitor-using-azure-monitor/alerts_image3.png" alt-text="显示门户菜单中的警报的屏幕截图。":::

### <a name="create-alerts"></a>创建警报

1. 选择“+ 新建警报规则”，以创建新警报。

    :::image type="content" source="media/monitor-using-azure-monitor/alerts_image4.png" alt-text="显示创建新警报规则的屏幕截图。":::

1. 定义警报条件。

    > [!NOTE]
    > 请务必在“按资源类型筛选”下拉列表中选择“所有” 。

    :::image type="content" source="media/monitor-using-azure-monitor/alerts_image5.png" alt-text="显示用于打开用于选择资源的窗格的选项的屏幕截图。":::

    :::image type="content" source="media/monitor-using-azure-monitor/alerts_image6.png" alt-text="显示用于打开用于配置信号逻辑的窗格的选项的屏幕截图。":::

    :::image type="content" source="media/monitor-using-azure-monitor/alerts_image7.png" alt-text="显示配置信号逻辑的屏幕截图。":::

1. 定义警报详细信息。

    :::image type="content" source="media/monitor-using-azure-monitor/alerts_image8.png" alt-text="显示警报详细信息的屏幕截图。":::

1. 定义操作组。

    :::image type="content" source="media/monitor-using-azure-monitor/alerts_image9.png" alt-text="显示创建规则并突出显示“新建操作组”的屏幕截图。":::

    :::image type="content" source="media/monitor-using-azure-monitor/alerts_image10.png" alt-text="显示创建新操作组的屏幕截图。":::

    :::image type="content" source="media/monitor-using-azure-monitor/alerts_image11.png" alt-text="显示配置电子邮件、短信、推送和语音的屏幕截图。":::

    :::image type="content" source="media/monitor-using-azure-monitor/alerts_image12.png" alt-text="显示定义操作组的屏幕截图。":::

## <a name="next-steps"></a>后续步骤

[配置诊断设置和工作区](monitor-configure-diagnostics.md)
