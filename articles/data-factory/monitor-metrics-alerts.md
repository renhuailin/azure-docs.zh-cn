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
ms.openlocfilehash: 3b8351c94fb7c9ace6dbf0e31a50f2ed3d8789a3
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128666621"
---
# <a name="data-factory-metrics-and-alerts"></a>数据工厂指标和警报

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Azure 数据工厂提供以下指标和警报来启用对服务的监视。

## <a name="data-factory-metrics"></a>数据工厂指标

使用 Monitor 可以洞察 Azure 工作负荷的性能与运行状况。 最重要的 Monitor 数据类型是指标（也称为性能计数器）。 大多数 Azure 资源都会发出指标。 Monitor 提供多种方式来配置和使用这些指标，以便进行监视与故障排除。

下面是 Azure 数据工厂版本 2 发出的一些指标：

| **指标**                           | **指标显示名称**                  | **单位** | **聚合类型** | **说明**                |
|--------------------------------------|------------------------------------------|----------|----------------------|--------------------------------|
| ActivityCancelledRuns                 | 已取消的活动运行指标数           | 计数    | 总计                | 在一分钟时段内取消的活动运行总数。 |
| ActivityFailedRuns                   | 失败的活动运行数指标             | 计数    | 总计                | 在一分钟时段内失败的活动运行总数。 |
| ActivitySucceededRuns                | 成功的活动运行数指标          | 计数    | 总计                | 在一分钟时段内成功的活动运行总数。 |
| PipelineCancelledRuns                 | 已取消的管道运行指标数           | 计数    | 总计                | 在一分钟时段内取消的管道运行总数。 |
| PipelineFailedRuns                   | 失败的管道运行数指标             | 计数    | 总计                | 在一分钟时段内失败的管道运行总数。 |
| PipelineSucceededRuns                | 成功的管道运行数指标          | 计数    | 总计                | 在一分钟时段内成功的管道运行总数。 |
| TriggerCancelledRuns                  | 已取消的触发器运行指标数            | 计数    | 总计                | 在一分钟时段内取消的触发器运行总数。 |
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
| PipelineElapsedTimeRuns | 运行时间管道运行指标 | 计数 | 总计 | 在一分钟时段内，管道运行的时间长于用户定义的预期时间的次数。 [（查看更多）](tutorial-operationalize-pipelines.md) |

若要访问指标，请参阅 [Azure Monitor 数据平台](../azure-monitor/data-platform.md)中的说明。

> [!NOTE]
> 仅发出已完成的、已触发活动的和管道运行中的事件，PipelineElapsedTimeRuns 除外。 不会发出正在进行的运行和调试运行。 另一方面，将发出所有 SSIS 包执行事件，包括已完成和正在进行的事件，而无论使用何种调用方法。 例如，可以在支持 Azure 的 SQL Server Data Tools (SSDT) 中、在 SSMS、SQL Server 代理或其他指定工具中通过 T-SQL，或者作为 ADF 管道中“执行 SSIS 包”活动的已触发运行或调试运行来调用包执行。

## <a name="data-factory-alerts"></a>数据工厂警报

登录到 Azure 门户，选择“Monitor” > “警报”以创建警报。

:::image type="content" source="media/monitor-using-azure-monitor/alerts_image3.png" alt-text="门户菜单中的警报":::

### <a name="create-alerts"></a>创建警报

1. 选择“+ 创建新的预警规则”，创建新的警报。

    :::image type="content" source="media/monitor-using-azure-monitor/alerts_image4.png" alt-text="新建警报规则":::

1. 定义警报条件。

    > [!NOTE]
    > 请务必在“按资源类型筛选”下拉列表中选择“所有”。

    :::image type="content" source="media/monitor-using-azure-monitor/alerts_image5.png" alt-text="“定义警报条件” > “选择目标”，将会打开“选择资源”窗格":::

    :::image type="content" source="media/monitor-using-azure-monitor/alerts_image6.png" alt-text="“定义警报条件” > “添加条件”，将会打开“配置信号逻辑”窗格":::

    :::image type="content" source="media/monitor-using-azure-monitor/alerts_image7.png" alt-text="“配置信号类型”窗格":::

1. 定义警报详细信息。

    :::image type="content" source="media/monitor-using-azure-monitor/alerts_image8.png" alt-text="警报详细信息":::

1. 定义操作组。

    :::image type="content" source="media/monitor-using-azure-monitor/alerts_image9.png" alt-text="创建一项规则，突出显示了“新建操作组”":::

    :::image type="content" source="media/monitor-using-azure-monitor/alerts_image10.png" alt-text="创建新的操作组":::

    :::image type="content" source="media/monitor-using-azure-monitor/alerts_image11.png" alt-text="配置电子邮件、短信、推送和语音":::

    :::image type="content" source="media/monitor-using-azure-monitor/alerts_image12.png" alt-text="删除操作组":::

## <a name="next-steps"></a>后续步骤

[配置诊断设置和工作区](monitor-configure-diagnostics.md)
