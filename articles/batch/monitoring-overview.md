---
title: 监视 Azure Batch
description: 了解 Azure 监视服务、指标、诊断日志以及 Azure Batch 的其他监视功能。
ms.topic: how-to
ms.date: 08/23/2021
ms.openlocfilehash: b2b73261b07a7e5eb269c1f58a45f97d99133ac4
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/24/2021
ms.locfileid: "122768884"
---
# <a name="monitor-batch-solutions"></a>监视 Batch 解决方案

[Azure Monitor](../azure-monitor/overview.md) 和 Batch 服务提供了一系列服务、工具和 API 来监视 Batch 解决方案。 本概述文章可帮助你选择适合需求的监视方法。

## <a name="subscription-level-monitoring"></a>订阅级监视

在订阅级别（包括 Batch 帐户），[Azure 活动日志](../azure-monitor/essentials/activity-log.md)将操作事件数据收集到几个类别中。

对于 Batch 帐户，具体而言，活动日志收集与帐户创建和删除以及密钥管理相关的事件。

可以在 Azure 门户中查看活动日志，也可以使用 Azure CLI、PowerShell cmdlet 或 Azure Monitor REST API 查询事件。 还可以导出活动日志，或者配置[活动日志警报](../azure-monitor/alerts/alerts-activity-log.md)。

## <a name="batch-account-level-monitoring"></a>Batch 帐户级监视

使用 [Azure Monitor](../azure-monitor/overview.md) 的各项功能监视每个 Batch 帐户。 Azure Monitor 针对 Batch 帐户中的资源（例如池、作业和任务）收集[指标](../azure-monitor/essentials/data-platform-metrics.md)和可选的[资源日志](../azure-monitor/essentials/resource-logs.md)。 可以手动或以编程方式收集并使用此数据来监视 Batch 帐户中的活动以及对问题进行诊断。 有关详细信息，请参阅[用于诊断评估和监视的 Batch 指标、警报和日志](batch-diagnostics.md)。

> [!NOTE]
> 指标默认情况下在 Batch 帐户中可用，不需要进行额外配置，它们具有为期 30 天的滚动历史记录。 必须为 Batch 帐户创建诊断设置才能将其资源日志发送到 Log Analytics 工作区，并且可能会产生额外的成本来存储或处理资源日志数据。

## <a name="batch-resource-monitoring"></a>Batch 资源监视

在 Batch 应用程序中，可以使用 Batch API 来监视或查询资源（包括作业、任务、节点和池）的状态。 例如：

- [按状态对任务和计算节点计数](batch-get-resource-counts.md)
- [创建可高效列出 Batch 资源的查询](batch-efficient-list-queries.md)
- [创建任务依赖项](batch-task-dependencies.md)
- 使用[作业管理器任务](/rest/api/batchservice/job/add#jobmanagertask)
- 监视[任务状态](/rest/api/batchservice/task/list#taskstate)
- 监视[节点状态](/rest/api/batchservice/computenode/list#computenodestate)
- 监视[池状态](/rest/api/batchservice/pool/get#poolstate)
- 监视[帐户中的池使用情况](/rest/api/batchservice/pool/listusagemetrics)
- [按状态对池节点](/rest/api/batchservice/account/listpoolnodecounts)进行计数

## <a name="additional-monitoring-solutions"></a>其他监视解决方案

使用 [Application Insights](../azure-monitor/app/app-insights-overview.md) 以编程方式监视 Batch 作业和任务的可用性、性能和使用情况。 通过 Application Insights，你可以从计算节点 (VM) 监视性能计数器并检索在计算节点上运行的任务的自定义信息。

有关示例，请参阅[使用 Application Insights 监视和调试 Batch .NET 应用程序](monitor-application-insights.md)以及附随的[代码示例](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ApplicationInsights)。

> [!NOTE]
> 使用 Application Insights 可能会产生额外的成本。 请参阅[定价信息](https://azure.microsoft.com/pricing/details/application-insights/)。

[Batch Explorer](https://github.com/Azure/BatchExplorer) 是一个功能丰富的免费独立客户端工具，可帮助创建、调试和监视 Azure Batch 应用程序。 下载适用于 Mac、Linux 或 Windows 的[安装包](https://azure.github.io/BatchExplorer/)。 （可选）使用 [Azure Batch Insights](https://github.com/Azure/batch-insights) 在 Batch Explorer 中获取 Batch 节点的系统统计信息，例如 VM 性能计数器。

## <a name="next-steps"></a>后续步骤

- 了解适用于生成批处理解决方案的[批处理 API 和工具](batch-apis-tools.md)。
- 详细了解使用 Batch [实现诊断日志记录](batch-diagnostics.md)。
