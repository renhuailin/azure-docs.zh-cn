---
title: 使用 Azure Monitor 监视 SSIS 操作
description: 了解如何使用 Azure Monitor 监视 Azure 数据工厂中的 SSIS 操作。
author: minhe-msft
ms.author: hemin
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: monitoring
ms.topic: conceptual
ms.date: 09/02/2021
ms.openlocfilehash: 5c03304933bb9c89b276f4950aea355436393ba2
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124837684"
---
# <a name="monitor-ssis-operations-with-azure-monitor"></a>使用 Azure Monitor 监视 SSIS 操作

要直接迁移 SSIS 工作负载，可以[在 ADF 中预配 SSIS IR](./tutorial-deploy-ssis-packages-azure.md)，以支持：

- 运行部署在由 Azure SQL 数据库服务器/托管实例托管的 SSIS 目录 (SSISDB) 中的包（项目部署模型）
- 运行部署在由 Azure SQL 托管实例托管的文件系统、Azure 文件存储或 SQL Server 数据库 (MSDB) 中的包（包部署模型）

预配后，可以[通过 Azure PowerShell 或 ADF 门户的“监视器”中心检查 SSIS IR 操作状态](./monitor-integration-runtime.md#azure-ssis-integration-runtime)。 使用项目部署模型时，SSIS 包执行日志存储在 SSISDB 内部表或视图中，因此可以使用 SSMS 之类的指定工具对其进行查询、分析和直观显示。 使用包部署模型时，可以将 SSIS 包执行日志作为 CSV 文件存储在文件系统或 Azure 文件存储中，仍需要使用其他指定工具对这些文件进行分析和处理，然后才能对其进行查询、分析和直观显示。

现在，通过 [Azure Monitor](../azure-monitor/data-platform.md) 集成，可在 Azure 门户上查询、分析和直观显示从 SSIS IR 操作和 SSIS 包执行生成的所有指标和日志。 此外，还可发出相关警报。

## <a name="configure-diagnostic-settings-and-workspace-for-ssis-operations"></a>为 SSIS 操作配置诊断设置和工作区

要将从 SSIS IR 操作和 SSIS 包执行生成的所有指标和日志发送到 Azure Monitor，需要[为 ADF 配置诊断设置和工作区](monitor-configure-diagnostics.md)。

## <a name="ssis-operational-metrics"></a>SSIS 操作指标

SSIS 操作[指标](../azure-monitor/essentials/data-platform-metrics.md)是性能计数器或数字值，用于描述特定时间点的 SSIS IR 启动和停止操作以及 SSIS 包执行的状态。 它们是 [Azure Monitor 中 ADF 指标](monitor-metrics-alerts.md)的一部分。

在 Azure Monitor 上为 ADF 配置诊断设置和工作区时，选中“AllMetrics”复选框将使 SSIS 操作指标可用于[使用 Azure 指标资源管理器进行的交互分析](../azure-monitor/essentials/metrics-getting-started.md)、[在 Azure 仪表板上呈现](../azure-monitor/app/tutorial-app-dashboards.md)以及[近实时警报](../azure-monitor/alerts/alerts-metric.md)。

:::image type="content" source="media/data-factory-monitor-oms/monitor-oms-image2.png" alt-text="命名设置并选择 log-analytics 工作区":::

## <a name="ssis-operational-alerts"></a>SSIS 操作警报

若要从 ADF 门户基于 SSIS 操作指标引发警报，请[选择 ADF“监视器”中心的“警报和指标”页面，并按照提供的分步说明进行操作](./monitor-visually.md#alerts)。

:::image type="content" source="media/data-factory-monitor-oms/data-factory-monitor-alerts-ssis.png" alt-text="从 ADF 门户引发 SSIS 操作警报":::

若要从 Azure 门户基于 SSIS 操作指标引发警报，请[选择 Azure“监视器”中心的“警报”页面，并按照提供的分步说明进行操作](monitor-metrics-alerts.md)。

:::image type="content" source="media/data-factory-monitor-oms/azure-monitor-alerts-ssis.png" alt-text="从 Azure 门户引发 SSIS 操作警报":::

## <a name="ssis-operational-logs"></a>SSIS 操作日志

SSIS 操作[日志](../azure-monitor/logs/data-platform-logs.md)是由 SSIS IR 操作和 SSIS 包执行生成的事件，这些事件提供了有关已识别问题的充足上下文，对根本原因分析很有用。 

在 Azure Monitor 上为 ADF 配置诊断设置和工作区时，可以选择相关 SSIS 操作日志，并将其发送到基于 Azure 数据资源管理器的 Log Analytics。 在这里，它们可用于[使用丰富的查询语言进行的分析](../azure-monitor/logs/log-query-overview.md)、[在 Azure 仪表板上呈现](../azure-monitor/app/tutorial-app-dashboards.md)以及[近实时警报](../azure-monitor/alerts/alerts-log.md)。

:::image type="content" source="media/data-factory-monitor-oms/monitor-oms-image2.png" alt-text="命名设置并选择 log-analytics 工作区":::

Azure Monitor 和 Log Analytics 中的 SSIS 包执行日志的架构和内容类似于 SSISDB 内部表或视图中的架构。

| Azure Monitor 日志类别          | Log Analytics 表                     | SSISDB 内部表/视图              |
| ------------------------------------- | ---------------------------------------- | ----------------------------------------- |
| `SSISIntegrationRuntimeLogs`          | `ADFSSISIntegrationRuntimeLogs`          |                                           |
| `SSISPackageEventMessageContext`      | `ADFSSISPackageEventMessageContext`      | `[internal].[event_message_context]`      |
| `SSISPackageEventMessages`            | `ADFSSISPackageEventMessages`            | `[internal].[event_messages]`             |
| `SSISPackageExecutableStatistics`     | `ADFSSISPackageExecutableStatistics`     | `[internal].[executable_statistics]`      |
| `SSISPackageExecutionComponentPhases` | `ADFSSISPackageExecutionComponentPhases` | `[internal].[execution_component_phases]` |
| `SSISPackageExecutionDataStatistics`  | `ADFSSISPackageExecutionDataStatistics`  | `[internal].[execution_data_statistics]`  |

有关 SSIS 操作日志特性/属性的详细信息，请参阅 [Azure Monitor 和 Log Analytics 为 ADF 使用的架构](monitor-schema-logs-events.md)。

无论使用哪种调用方法，所选 SSIS 包执行日志始终会发送到 Log Analytics。 例如，可以在支持 Azure 的 SSDT 中、在 SSMS、SQL Server 代理或其他指定工具中通过 T-SQL，或者作为 ADF 管道中“执行 SSIS 包”活动的已触发运行或调试运行来调用包执行。

在 Logs Analytics 上查询 SSIS IR 操作日志时，可以使用 OperationName 和 ResultType 属性，这两个属性分别设置为 `Start/Stop/Maintenance/Heartbeat` 和 `Started/InProgress/Succeeded/Failed/Healthy/Unhealthy` 。

:::image type="content" source="media/data-factory-monitor-oms/log-analytics-query.png" alt-text="在 Log Analytics 上查询 SSIS IR 操作日志":::

若要查询 SSIS IR 节点状态，可以将 OperationName 属性设置为 `Heartbeat`。 通常，每个节点每分钟都会向日志分析发送一个 `Heartbeat` 记录，通过 ResultType 属性反映其状态，当节点可用于包执行时其属性为 `Healthy`，反之则为 `Unhealthy`。 例如，如果 SSIS IR 有两个可用节点，将在任意一个一分钟时间段内始终可以看到两个 ResultType 属性设置为 `Healthy` 的 `Heartbeat` 记录。

:::image type="content" source="media/data-factory-monitor-oms/log-analytics-query-3.png" alt-text="在日志分析上查询 SSIS IR 检测信号":::

你可以查询以下模式以检测 SSIS IR 节点的不可用性：

* SSIS IR 仍在运行时，在许多一分钟时间段内都会缺失 `Heartbeat` 记录。
* SSIS IR 仍在运行时，在许多一分钟时间段内会有 ResultType 属性设置为 `Unhealthy` 的 `Heartbeat` 记录。

你可以将以上查询转换成[警报](../azure-monitor/alerts/alerts-unified-log.md)，然后转到 [SSIS IR 监视页面](monitor-integration-runtime.md#monitor-the-azure-ssis-integration-runtime-in-azure-portal)以确认收到那些警报。

在 Logs Analytics 上查询 SSIS 包执行日志时，可以使用 OperationId/ExecutionId/CorrelationId 属性联接它们  。 对于与未存储在 SSISDB 中的包/通过 T-SQL 调用的包相关的所有操作/执行，OperationId/ExecutionId 始终设置为 `1`  。

:::image type="content" source="media/data-factory-monitor-oms/log-analytics-query2.png" alt-text="在 Log Analytics 上查询 SSIS 包执行日志":::

## <a name="next-steps"></a>后续步骤

[日志和事件的架构](monitor-schema-logs-events.md)
