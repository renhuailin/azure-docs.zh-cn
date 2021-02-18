---
title: 监视 Azure 机器学习数据引用 | Microsoft Docs
description: 用于监视 Azure 机器学习的参考文档。 了解 Azure Monitor 中收集的和提供的数据和资源。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.custom: subject-monitoring
ms.date: 10/02/2020
ms.openlocfilehash: f9f25ec2dd8fb8b859f7863c1e695e22cb9a9cec
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/17/2021
ms.locfileid: "100575078"
---
# <a name="monitoring-azure-machine-learning-data-reference"></a>监视 Azure 机器学习数据引用

了解 Azure Monitor 从 Azure 机器学习工作区收集的数据和资源。 有关收集和分析监视数据的详细信息，请参阅[监视 Azure 机器学习](monitor-azure-machine-learning.md)。

## <a name="metrics"></a>指标

本部分列出了为 Azure 机器学习自动收集的所有平台指标。 这些指标的资源提供程序是 [Microsoft.MachineLearningServices/workspaces](../azure-monitor/essentials/metrics-supported.md#microsoftmachinelearningservicesworkspaces)。

**Model**

| 指标 | 计价单位 | 说明 |
| ----- | ----- | ----- |
| 模型部署失败 | 计数 | 失败的模型部署数。 |
| 模型部署开始 | 计数 | 开始的模型部署数。 |
| 模型部署成功 | 计数 | 成功的模型部署数。 |
| 模型注册失败 | 计数 | 失败的模型注册数。 |
| 模型注册成功 | 计数 | 成功的模型注册数。 |

**配额**

配额信息仅用于 Azure 机器学习计算。

| 指标 | 计价单位 | 说明 |
| ----- | ----- | ----- |
| 活动核心数 | 计数 | 活动计算核心的数量。 |
| 活动节点数 | 计数 | 活动节点的数量。 |
| 空闲核心数 | 计数 | 空闲计算核心的数量。 |
| 空闲节点数 | 计数 | 空闲计算节点的数量。 |
| 正在离开核心数 | 计数 | 正在离开核心的数量。 |
| 正在离开节点数 | 计数 | 正在离开节点的数量。 |
| 已占用核心数 | 计数 | 已占用核心的数量。 |
| 已占用节点 | 计数 | 已占用节点的数量。 |
| 配额使用率百分比 | 百分比 | 已使用配额的百分比。 |
| 核心总数 | 计数 | 核心总数。 |
| 节点总数 | 计数 | 节点总数。 |
| 不可用核心数 | 计数 | 不可用核心的数量。 |
| 不可用节点数 | 计数 | 不可用节点的数量。 |

**资源**

| 指标 | 计价单位 | 说明 |
| ----- | ----- | ----- |
| CpuUtilization | 百分比 | 在运行/作业过程中，给定节点使用的 CPU 百分比。 仅当作业在节点上运行时，才会发布此指标。 一个作业可以使用一个或多个节点。 此指标按节点发布。 |
| GpuUtilization | 百分比 | 在运行/作业过程中，给定节点使用的 GPU 百分比。 一个节点可以有一个或多个 GPU。 此指标按每个节点的 GPU 发布。 |

**Run**

有关训练运行的信息。

| 指标 | 计价单位 | 说明 |
| ----- | ----- | ----- |
| 已完成的运行数 | 计数 | 已完成运行的数量。 |
| 失败运行数 | 计数 | 失败运行的数量。 |
| 已开始运行 | 计数 | 已开始运行的数量。 |

## <a name="metric-dimensions"></a>指标维度

有关指标维度定义的详细信息，请参阅[多维指标](../azure-monitor/essentials/data-platform-metrics.md#multi-dimensional-metrics)。

Azure 机器学习具有以下与指标关联的维度。

| 维度 | 说明 |
| ---- | ---- |
| 群集名称 | 计算群集资源的名称。 可用于所有配额指标。 |
| VM 系列名称 | 群集使用的 VM 系列的名称。 可用于配额使用率百分比。 |
| VM 优先级 | VM 的优先级。 可用于配额使用率百分比。
| CreatedTime | 仅适用于 CpuUtilization 和 GpuUtilization。 |
| DeviceId | 设备 (GPU) 的 ID。 仅适用于 GpuUtilization。 |
| NodeId | 作业运行时所在的已创建节点的 ID。 仅适用于 CpuUtilization 和 GpuUtilization。 |
| RunId | 运行/作业的 ID。 仅适用于 CpuUtilization 和 GpuUtilization。 |
| ComputeType | 运行时使用的计算类型。 仅适用于已完成的运行数、失败运行数和已启动的运行数。 |
| PipelineStepType | 运行时使用的 [PipelineStep](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinestep?preserve-view=true&view=azure-ml-py) 类型。 仅适用于已完成的运行数、失败运行数和已启动的运行数。 |
| PublishedPipelineId | 运行时使用的已发布管道的 ID。 仅适用于已完成的运行数、失败运行数和已启动的运行数。 |
| RunType | 运行的类型。 仅适用于已完成的运行数、失败运行数和已启动的运行数。 |

RunType 维度的有效值为：

| Value | 说明 |
| ----- | ----- |
| 试验 | 非管道运行。 |
| PipelineRun | 管道运行，它是 StepRun 的父级。 |
| StepRun | 管道步骤的运行。 |
| ReusedStepRun | 重用上次运行的管道步骤的运行。 |

## <a name="activity-log"></a>活动日志

下表列出了可能在活动日志中创建的与 Azure 机器学习相关的操作。

| Operation | 说明 |
|:---|:---|
| 创建或更新机器学习工作区 | 已创建或更新工作区 |
| 检查计算名称可用性 | 检查计算名称是否已在使用中 |
| 创建或更新计算资源 | 已创建或更新计算资源 |
| 删除计算资源 | 已删除计算资源 |
| 列出机密 | 执行操作时列出的机器学习工作区的机密 |

## <a name="resource-logs"></a>资源日志

本部分列出了可为 Azure 机器学习工作区收集的资源日志类型。

资源提供程序和类型：[Microsoft.MachineLearningServices/workspace](../azure-monitor/essentials/resource-logs-categories.md#microsoftmachinelearningservicesworkspaces).

| 类别 | 显示名称 |
| ----- | ----- |
| AmlComputeClusterEvent | AmlComputeClusterEvent |
| AmlComputeClusterNodeEvent | AmlComputeClusterNodeEvent |
| AmlComputeCpuGpuUtilization | AmlComputeCpuGpuUtilization |
| AmlComputeJobEvent | AmlComputeJobEvent |
| AmlRunStatusChangedEvent | AmlRunStatusChangedEvent |

## <a name="schemas"></a>架构

Azure 机器学习正在使用以下架构

### <a name="amlcomputejobevents-table"></a>AmlComputeJobEvents 表

| 属性 | 说明 |
|:--- |:---|
| TimeGenerated | 生成日志项目的时间 |
| OperationName | 与日志事件关联的操作的名称 |
| Category | 日志事件的名称，AmlComputeClusterNodeEvent |
| JobId | 已提交作业的 ID |
| ExperimentId | 试验的 ID |
| ExperimentName | 试验的名称 |
| CustomerSubscriptionId | 已提交的试验和作业的 SubscriptionId |
| WorkspaceName | 机器学习工作区的名称 |
| ClusterName | 群集的名称 |
| ProvisioningState | 作业提交的状态 |
| ResourceGroupName | 资源组的名称 |
| JobName | 作业的名称 |
| ClusterId | 群集的 ID |
| EventType | 作业事件的类型。 例如 JobSubmitted、JobRunning、JobFailed、JobSucceeded。 |
| ExecutionState | 作业（运行）的状态。 例如已排队、正在运行、成功、失败 |
| ErrorDetails | 作业错误的详细信息 |
| CreationApiVersion | 用于创建作业的 Api 版本 |
| ClusterResourceGroupName | 群集的资源组名称 |
| TFWorkerCount | TF 辅助角色的计数 |
| TFParameterServerCount | TF 参数服务器的计数 |
| ToolType | 使用的工具类型 |
| RunInContainer | 描述作业是否应在容器中运行的标志 |
| JobErrorMessage | 作业错误的详细消息 |
| NodeId | 作业运行时所创建节点的 ID |

### <a name="amlcomputeclusterevents-table"></a>AmlComputeClusterEvents 表

| 属性 | 说明 |
|:--- |:--- |
| TimeGenerated | 生成日志项目的时间 |
| OperationName | 与日志事件关联的操作的名称 |
| Category | 日志事件的名称，AmlComputeClusterNodeEvent |
| ProvisioningState | 群集的预配状态 |
| ClusterName | 群集的名称 |
| ClusterType | 群集的类型 |
| CreatedBy | 创建群集的用户 |
| CoreCount | 群集中的核心计数 |
| VmSize | 群集的 VM 大小 |
| VmPriority | 在群集内所创建节点的优先级 Dedicated/LowPriority |
| ScalingType | 群集缩放的类型手动/自动 |
| InitialNodeCount | 群集的初始节点计数 |
| MinimumNodeCount | 群集的最小节点计数 |
| MaximumNodeCount | 群集的最大节点计数 |
| NodeDeallocationOption | 解除分配节点的方法 |
| 发布者 | 群集类型的发布服务器 |
| 产品/服务 | 用于创建群集的产品/服务 |
| SKU | 群集内所创建节点/VM 的 Sku |
| 版本 | 创建节点/VM 时使用的映像版本 |
| SubnetId | 群集的 SubnetId |
| AllocationState | 群集分配状态 |
| CurrentNodeCount | 群集的当前节点计数 |
| TargetNodeCount | 群集纵向扩展/减少时的目标节点计数 |
| EventType | 群集创建期间的事件类型。 |
| NodeIdleTimeSecondsBeforeScaleDown | 群集纵向缩减之前的空闲时间（以秒为单位） |
| PreemptedNodeCount | 群集的已占用节点计数 |
| IsResizeGrow | 指示群集正在纵向扩展的标志 |
| VmFamilyName | 可在群集内创建的节点的 VM 系列名称 |
| LeavingNodeCount | 群集的正在离开节点计数 |
| UnusableNodeCount | 群集的不可用节点计数 |
| IdleNodeCount | 群集的空闲节点计数 |
| RunningNodeCount | 群集的正在运行节点计数 |
| PreparingNodeCount | 群集的正在准备节点计数 |
| QuotaAllocated | 群集的已分配配额 |
| QuotaUtilized | 群集的已利用配额 |
| AllocationStateTransitionTime | 将时间从一种状态转换为另一种状态 |
| ClusterErrorCodes | 群集创建或缩放期间收到的错误代码 |
| CreationApiVersion | 创建群集时使用的 API 版本 |

### <a name="amlcomputeclusternodeevents-table"></a>AmlComputeClusterNodeEvents 表

| 属性 | 说明 |
|:--- |:--- |
| TimeGenerated | 生成日志项目的时间 |
| OperationName | 与日志事件关联的操作的名称 |
| Category | 日志事件的名称，AmlComputeClusterNodeEvent |
| ClusterName | 群集的名称 |
| NodeId | 创建的群集节点的 ID |
| VmSize | 节点的 VM 大小 |
| VmFamilyName | 节点所属的 VM 系列 |
| VmPriority | 已创建的节点的优先级 Dedicated/LowPriority |
| 发布者 | VM 映像的发布者。 例如 microsoft-dsvm |
| 产品/服务 | 与 VM 创建相关联的产品/服务 |
| SKU | 已创建的节点/VM 的 SKU |
| 版本 | 创建节点/VM 时使用的映像版本 |
| ClusterCreationTime | 创建群集的时间 |
| ResizeStartTime | 群集开始纵向扩展/缩减的时间 |
| ResizeEndTime | 群集结束纵向扩展/缩减的时间 |
| NodeAllocationTime | 分配节点的时间 |
| NodeBootTime | 节点启动的时间 |
| StartTaskStartTime | 向节点分配任务并启动任务的时间 |
| StartTaskEndTime | 向节点分配任务并结束任务的时间 |
| TotalE2ETimeInSeconds | 总时间节点处于活动状态 |


## <a name="see-also"></a>请参阅

- 有关监视 Azure 机器学习的说明，请参阅[监视 Azure 机器学习](monitor-azure-machine-learning.md)。
- 有关监视 Azure 资源的详细信息，请参阅[通过 Azure Monitor 监视 Azure 资源](../azure-monitor/essentials/monitor-azure-resource.md)。
