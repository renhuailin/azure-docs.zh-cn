---
title: 监视 Azure 机器学习 | Microsoft Docs
description: 了解如何使用 Azure Monitor 查看、分析和创建有关 Azure 机器学习中指标的警报。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.custom: subject-monitoring
ms.date: 10/01/2020
ms.openlocfilehash: c0f35290aa653d5b9e9be9f1a9a0184854509889
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121862213"
---
# <a name="monitor-azure-machine-learning"></a>监视 Azure 机器学习

当你的关键应用程序和业务流程依赖于 Azure 资源时，你需要监视这些资源的可用性、性能和操作。 本文介绍了 Azure 机器学习生成的监视数据，以及如何使用 Azure Monitor 对此数据进行分析和发出警报。

> [!TIP]
> 本文档中的信息主要面向管理员，因为它介绍针对 Azure 机器学习服务和关联的 Azure 服务的监视。 如果你是一名数据科学家或开发人员，并且想要监视与模型训练运行相关的信息，请参阅以下文档 ：
>
> * [启动、监视和取消训练运行](how-to-track-monitor-analyze-runs.md)
> * [记录训练运行的指标](how-to-log-view-metrics.md)
> * [使用 MLflow 跟踪试验](how-to-use-mlflow.md)
> * [使用 TensorBoard 将运行可视化](how-to-monitor-tensorboard.md)
>
> 如果要监视由部署为 Web 服务的模型生成的信息，请参阅[收集模型数据](how-to-enable-data-collection.md)和[使用 Application Insights 进行监视](how-to-enable-app-insights.md)。

## <a name="what-is-azure-monitor"></a>说明是 Azure Monitor？

Azure 机器学习使用 [Azure Monitor](../azure-monitor/overview.md) 创建监视数据，这是 Azure 中的一个完整堆栈监视服务。 Azure Monitor 提供了一组用于监视 Azure 资源的完整功能。 它还可以监视其他云和本地的资源。

一开始可以阅读[使用 Azure Monitor 监视 Azure 资源](../azure-monitor/essentials/monitor-azure-resource.md)一文，其中介绍了以下概念：

- 说明是 Azure Monitor？
- 与监视相关的成本
- 监视 Azure 中收集的数据
- 配置数据收集
- Azure 中用于分析监视数据并就其发出警报的标准工具

本文中的以下各部分将介绍从 Azure 机器学习收集的特定数据。 这些部分还提供了使用 Azure 工具配置数据收集和分析此数据的示例。

> [!TIP]
> 若想了解与 Azure Monitor 相关的成本，请参阅[使用情况和估计成本](../azure-monitor//usage-estimated-costs.md)。 若要了解数据在 Azure Monitor 中显示需要花多长时间，请参阅 [日志数据引入时间](../azure-monitor/logs/data-ingestion-time.md)。

## <a name="monitoring-data-from-azure-machine-learning"></a>Azure 机器学习的监视数据

Azure 机器学习收集的监视数据的类型与 [Azure 资源的监视数据](../azure-monitor/essentials/monitor-azure-resource.md#monitoring-data)中所述的其他 Azure 资源相同。 

请参阅 [Azure 机器学习监视数据参考](monitor-resource-reference.md)，详细了解 Azure 机器学习创建的日志和指标。

<a id="configuration"></a>

## <a name="collection-and-routing"></a>收集和路由

平台指标和活动日志会自动收集和存储，但你可以使用诊断设置将其路由到其他位置。  

在创建诊断设置并将其路由到一个或多个位置之前，不会收集和存储资源日志。 需要管理多个Azure 机器学习工作区时，可将所有工作区的日志路由到同一个日志记录目标，并从单个位置查询所有日志。

有关使用 Azure 门户、Azure CLI 或 PowerShell 创建诊断设置的详细过程，请参阅[创建诊断设置以收集 Azure 中的平台日志和指标](../azure-monitor/essentials/diagnostic-settings.md)。 创建诊断设置时，请指定要收集的日志类别。 Azure 机器学习的类别在 [Azure 机器学习监视数据参考](monitor-resource-reference.md#resource-logs)中列出。

> [!IMPORTANT]
> 启用这些设置需要额外的 Azure 服务（存储帐户、事件中心或 Log Analytics），这可能会增加成本。 若要估算成本，请访问 [Azure 定价计算器](https://azure.microsoft.com/pricing/calculator)。

可以为 Azure 机器学习配置以下日志：

| 类别 | 说明 |
|:---|:---|
| AmlComputeClusterEvent | Azure 机器学习计算群集的事件。 |
| AmlComputeClusterNodeEvent | Azure 机器学习计算群集内节点的事件。 |
| AmlComputeJobEvent | Azure 机器学习计算上运行的作业的事件。 |


> [!NOTE]
> 启用诊断设置中的指标时，当前发送到存储帐户、事件中心或 log analytics 的信息中并不包含维度信息。

以下部分将讨论可以收集的指标和日志。

## <a name="analyzing-metrics"></a>分析指标

可以从“Azure Monitor”菜单中打开“指标”，以分析 Azure 机器学习指标以及来自其他 Azure 服务的指标 。 有关使用此工具的详细信息，请参阅 [Azure 指标资源管理器入门](../azure-monitor/essentials/metrics-getting-started.md)。

有关收集的平台指标的列表，请参阅[监视 Azure 机器学习数据引用指标](monitor-resource-reference.md#metrics)。

Azure 机器学习的所有指标都位于命名空间 **机器学习服务工作区** 中。

![机器学习服务工作区处于选定状态的指标资源管理器](./media/monitor-azure-machine-learning/metrics.png)

若要参考，可以查看 [Azure Monitor 中所有受支持的资源指标](../azure-monitor/essentials/metrics-supported.md)列表。

> [!TIP]
> Azure Monitor 指标数据有效期为 90 天。 但在创建图表时，只直观显示 30 天的数据。 例如，如果想要直观显示 90 天的数据，必须将它拆分成 90 天内包含 30 天的数据的三个图表。
### <a name="filtering-and-splitting"></a>筛选和拆分

对于支持维度的指标，应用筛选器时可以使用维度值。 例如，筛选“群集名称”为 `cpu-cluster` 的“活动核心”。 

还可以按维度来拆分指标，将指标的不同部分进行直观比较。 例如，拆分 **管道步骤类型**，查看管道中使用的步骤类型的计数。

有关筛选和拆分的详细信息，请参阅 [Azure Monitor 的高级功能](../azure-monitor/essentials/metrics-charts.md)。

<a id="analyzing-log-data"></a>
## <a name="analyzing-logs"></a>分析日志

使用 Azure Monitor Log Analytics 需要创建诊断配置，并启用 __将信息发送到 Log Analytics__。 有关详细信息，请参阅[收集和路由](#collection-and-routing)部分。

Azure Monitor 日志中的数据以表形式存储，每个表包含自己独有的属性集。 Azure 机器学习将数据存储在以下表格中：

| 表 | 说明 |
|:---|:---|
| AmlComputeClusterEvent | Azure 机器学习计算群集的事件。|
| AmlComputeClusterNodeEvent | Azure 机器学习计算群集内节点的事件。 |
| AmlComputeJobEvent | Azure 机器学习计算上运行的作业的事件。 |
| AmlComputeInstanceEvent | 访问（读取/写入）ML 计算实例时生成的事件。 类别包括：ComputeInstanceEvent（非常琐碎）。 |
| AmlDataLabelEvent | 访问（读取、创建或删除）数据标签或其项目时生成的事件。 类别包括：DataLabelReadEvent、DataLabelChangeEvent。  |
| AmlDataSetEvent | 访问（读取、创建或删除）已注册或未注册的 ML 数据集时生成的事件。 类别包括：DataSetReadEvent、DataSetChangeEvent。 |
| AmlDataStoreEvent | 访问（读取、创建或删除）ML 数据存储时生成的事件。 类别包括：DataStoreReadEvent、DataStoreChangeEvent。 |
| AmlDeploymentEvent | 在 ACI 或 AKS 上进行模型部署时生成的事件。 类别包括：DeploymentReadEvent、DeploymentEventACI、DeploymentEventAKS。 |
| AmlInferencingEvent | AKS 或 ACI 计算类型上的推理或相关操作事件。 类别包括：InferencingOperationACI（非常琐碎）、InferencingOperationAKS（非常琐碎）。 |
| AmlModelsEvent | 访问（读取、创建或删除）ML 模型时生成的事件。 包括将模型和资产打包成随时可生成的包时生成的事件。 类别包括：ModelsReadEvent、ModelsActionEvent。|
| AmlPipelineEvent | 访问（读取、创建或删除）ML 管道草稿、终结点或模块时生成的事件。类别包括：PipelineReadEvent、PipelineChangeEvent。 |
| AmlRunEvent | 访问（读取、创建或删除）ML 试验时生成的事件。 类别包括：RunReadEvent、RunEvent。 |
| AmlEnvironmentEvent | 访问（读取、创建或删除）ML 环境配置时生成的事件。 类别包括：EnvironmentReadEvent（非常琐碎）、EnvironmentChangeEvent。 |


> [!IMPORTANT]
> 在 Azure 机器学习菜单中选择“日志”时，Log Analytics 随即打开，其查询范围设置为当前工作区。 这意味着日志查询只包含来自该资源的数据。 如果希望运行的查询包含其他数据库或其他 Azure 服务的数据，请从“Azure Monitor”菜单中选择“日志”。 有关详细信息，请参阅 [Azure Monitor Log Analytics 中的日志查询范围和时间范围](../azure-monitor/logs/scope.md)。

请参阅 [Azure 机器学习监视数据参考](monitor-resource-reference.md)，详细了解相关日志和指标。

### <a name="sample-kusto-queries"></a>示例 Kusto 查询

> [!IMPORTANT]
> 在 [service-name] 菜单中选择“日志”时，Log Analytics 随即打开，其查询范围设置为当前 Azure 机器学习工作区。 这意味着日志查询只包含来自该资源的数据。 如果希望运行包含其他工作区或其他 Azure 服务数据的查询，请从“Azure Monitor”菜单中选择“日志” 。 有关详细信息，请参阅 [Azure Monitor Log Analytics 中的日志查询范围和时间范围](../azure-monitor/logs/scope.md)。

下面是一些可用于帮助监视 Azure 机器学习资源的查询： 

+ 获取过去五天内失败的作业：

    ```Kusto
    AmlComputeJobEvent
    | where TimeGenerated > ago(5d) and EventType == "JobFailed"
    | project  TimeGenerated , ClusterId , EventType , ExecutionState , ToolType
    ```

+ 获取特定名称的作业的记录：

    ```Kusto
    AmlComputeJobEvent
    | where JobName == "automl_a9940991-dedb-4262-9763-2fd08b79d8fb_setup"
    | project  TimeGenerated , ClusterId , EventType , ExecutionState , ToolType
    ```

+ 为 VM 大小为 Standard_D1_V2 的群集获取过去五天内的群集事件：

    ```Kusto
    AmlComputeClusterEvent
    | where TimeGenerated > ago(4d) and VmSize == "STANDARD_D1_V2"
    | project  ClusterName , InitialNodeCount , MaximumNodeCount , QuotaAllocated , QuotaUtilized
    ```

+ 获取最近 8 天内分配的节点：

    ```Kusto
    AmlComputeClusterNodeEvent
    | where TimeGenerated > ago(8d) and NodeAllocationTime  > ago(8d)
    | distinct NodeId
    ```

将多个 Azure 机器学习工作区连接到同一个 Log Analytics 工作区时，可以跨所有资源进行查询。 

+ 获取过去一天内各个工作区和群集中运行的节点数：

    ```Kusto
    AmlComputeClusterEvent
    | where TimeGenerated > ago(1d)
    | summarize avgRunningNodes=avg(TargetNodeCount), maxRunningNodes=max(TargetNodeCount)
             by Workspace=tostring(split(_ResourceId, "/")[8]), ClusterName, ClusterType, VmSize, VmPriority
    ```

## <a name="alerts"></a>警报

可以通过从“Azure Monitor”菜单中打开“警报”，来访问 Azure 机器学习的警报。 请参阅[使用 Azure Monitor 创建、查看和管理指标警报](../azure-monitor/alerts/alerts-metric.md)，详细了解如何创建警报。

下表列出了常见和推荐使用的 Azure 机器学习的指标警报规则：

| 警报类型 | 条件 | 描述 |
|:---|:---|:---|
| 失败的模型部署数 | 聚合类型：总计，运算符：大于，阈值：0 | 当一个或多个模型部署失败时 |
| 配额利用率百分比 | 聚合类型：平均，运算符：大于，阈值：90| 当配额使用率百分比大于 90% 时 |
| 不可用的节点数 | 聚合类型：总计，运算符：大于，阈值：0 | 当存在一个或多个不可用的节点时 |

## <a name="next-steps"></a>后续步骤

- 请参阅[监视 Azure 机器学习数据参考](monitor-resource-reference.md)，了解相关日志和指标。
- 有关如何使用与 Azure 机器学习相关的配额的信息，请参阅[管理和请求 Azure 资源配额](how-to-manage-quotas.md)。
- 有关监视 Azure 资源的详细信息，请参阅[通过 Azure Monitor 监视 Azure 资源](../azure-monitor/essentials/monitor-azure-resource.md)。
