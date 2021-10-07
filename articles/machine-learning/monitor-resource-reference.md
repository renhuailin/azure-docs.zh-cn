---
title: 监视 Azure 机器学习数据引用 | Microsoft Docs
description: 用于监视 Azure 机器学习的参考文档。 了解 Azure Monitor 中收集的和提供的数据和资源。
services: machine-learning
ms.service: machine-learning
ms.subservice: mlops
ms.topic: reference
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.custom: subject-monitoring
ms.date: 04/07/2021
ms.openlocfilehash: 099da6c4e3906b118bdfb5df80cf44ad3814660d
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/04/2021
ms.locfileid: "129427146"
---
# <a name="monitoring-azure-machine-learning-data-reference"></a>监视 Azure 机器学习数据引用

了解 Azure Monitor 从 Azure 机器学习工作区收集的数据和资源。 有关收集和分析监视数据的详细信息，请参阅[监视 Azure 机器学习](monitor-azure-machine-learning.md)。

## <a name="metrics"></a>指标

本部分列出了为 Azure 机器学习自动收集的所有平台指标。 这些指标的资源提供程序是 [Microsoft.MachineLearningServices/workspaces](../azure-monitor/essentials/metrics-supported.md#microsoftmachinelearningservicesworkspaces)。

**Model**

| 指标 | 计价单位 | 说明 |
|--|--|--|
| 成功的模型注册数 | 计数 | 此工作区中成功的模型注册数 |
| 失败的模型注册数 | 计数 | 此工作区中失败的模型注册数 |
| 已启动的模型部署数 | 计数 | 此工作区中已启动的模型部署数 |
| 成功的模型部署数 | 计数 | 此工作区中成功的模型部署数 |
| 失败的模型部署数 | 计数 | 此工作区中失败的模型部署数 |

**配额**

配额信息仅用于 Azure 机器学习计算。

| 指标 | 计价单位 | 说明 |
|--|--|--|
| 节点总数 | 计数 | 节点总数。 此总数包括一些活动节点、空闲节点、不可用节点、已占用节点和正在退出的节点 |
| 活动节点数 | 计数 | 活动节点数。 正在主动运行作业的节点。 |
| 空闲节点数 | 计数 | 空闲节点数。 空闲节点是指未运行任何作业，但可以接受新作业（如果可用）的节点。 |
| 不可用的节点数 | 计数 | 不可用的节点数。 不可用的节点是由于存在某种不可解决的问题而无法正常运行的节点。 Azure 将回收这些节点。 |
| 占用的节点数 | 计数 | 占用的节点数。 这些节点是已从可用节点池中取出的低优先级节点。 |
| 正在退出的节点数 | 计数 | 正在退出的节点数。 正在退出的节点是指刚刚完成了作业处理，将进入空闲状态的节点。 |
| 核心总数 | 计数 | 核心总数 |
| 活动核心数 | 计数 | 活动核心数 |
| 空闲核心数 | 计数 | 空闲核心数 |
| 不可用的核心数 | 计数 | 不可用的核心数 |
| 占用的核心数 | 计数 | 占用的核心数 |
| 正在退出的核心数 | 计数 | 正在退出的核心数 |
| 配额利用率百分比 | 计数 | 已利用的配额百分比 |

**资源**

| 指标| 计价单位 | 说明 |
|--|--|--|
| CpuUtilization | 计数 | CPU 节点上的使用率百分比。 利用率每分钟报告一次。 |
| GpuUtilization | 计数 | GPU 节点上的使用率百分比。 利用率每分钟报告一次。 |
| GpuMemoryUtilization | 计数 | GPU 节点上内存利用率的百分比。 利用率每分钟报告一次。 |
| GpuEnergyJoules | 计数 | GPU 节点上的间隔功耗（焦耳）。 功耗每分钟报告一次。 |

**运行**

有关工作区训练运行的信息。

| 指标 | 计价单位 | 说明 |
|--|--|--|
| 已取消运行数 | 计数 | 此工作区取消的运行数。 成功取消运行时，将更新计数。 |
| 请求取消运行数 | 计数 | 此工作区请求取消的运行数。 当收到用于运行的取消请求时，将更新计数。 |
| 已完成的运行数 | 计数 | 已成功为此工作区完成的运行数。 当运行完成并收集输出时，将更新计数。 |
| 失败的运行次数 | 计数 | 此工作区失败的运行数。 运行失败时，将更新计数。 |
| 完成状态运行数 | 计数 | 此工作区的进入完成状态的运行数。 运行已完成但输出收集仍在进行时，将更新计数。 | 
| 无响应运行数 | 计数 | 此工作区无响应的运行数。 当运行进入无响应状态时，将更新计数。 |
| 未启动运行 | 计数 | 此工作区处于“未启动”状态的运行数。 当收到创建运行的请求但尚未填充运行信息时，将更新计数。 |
| 准备的运行数 | 计数 | 为此工作区准备的运行数。 准备运行环境时，当运行进入准备状态时，将更新计数。 |
| 预配运行数 | 计数 | 为此工作区预配的运行数。 当运行正在等待创建或预配计算目标时，将更新计数。 |
| 排队运行数 | 计数 | 此工作区已排队的运行数。 当运行在计算目标中排队时，将更新计数。 等待所需计算节点准备就绪时，可能会发生。 |
| 已启动的运行数 | 计数 | 为此工作区运行的运行数。 运行在所需资源上开始运行时，将更新计数。 |
| 启动运行数 | 计数 | 为此工作区启动的运行数。 在请求创建运行和运行信息（如运行 ID）已填充后，将更新计数 |
| 错误 | 计数 | 此工作区中的运行错误数。 运行遇到错误时，将更新计数。 |
| 警告 | 计数 | 此工作区中的运行警告数。 运行遇到警告时，将更新计数。 |

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
| PipelineStepType | 运行时使用的 [PipelineStep](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinestep) 类型。 仅适用于已完成的运行数、失败运行数和已启动的运行数。 |
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
| ModelsChangeEvent | ModelsChangeEvent |
| ModelsReadEvent | ModelsReadEvent |
| ModelsActionEvent | ModelsActionEvent |
| DeploymentReadEvent | DeploymentReadEvent |
| DeploymentEventACI | DeploymentEventACI |
| DeploymentEventAKS | DeploymentEventAKS |
| InferencingOperationAKS | InferencingOperationAKS |
| InferencingOperationACI | InferencingOperationACI |
| EnvironmentChangeEvent | EnvironmentChangeEvent |
| EnvironmentReadEvent | EnvironmentReadEvent |
| DataLabelChangeEvent | DataLabelChangeEvent |
| DataLabelReadEvent | DataLabelReadEvent |
| ComputeInstanceEvent | ComputeInstanceEvent |
| DataStoreChangeEvent | DataStoreChangeEvent |
| DataStoreReadEvent | DataStoreReadEvent |
| DataSetChangeEvent | DataSetChangeEvent |
| DataSetReadEvent | DataSetReadEvent |
| PipelineChangeEvent | PipelineChangeEvent |
| PipelineReadEvent | PipelineReadEvent |
| RunEvent | RunEvent |
| RunReadEvent | RunReadEvent |


## <a name="schemas"></a>架构

Azure 机器学习正在使用以下架构

### <a name="amlcomputejobevent-table"></a>AmlComputeJobEvent 表

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

### <a name="amlcomputeclusterevent-table"></a>AmlComputeClusterEvent 表

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

### <a name="amlcomputeclusternodeevent-table"></a>AmlComputeClusterNodeEvent 表

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

### <a name="amlcomputeinstanceevent-table"></a>AmlComputeInstanceEvent 表

| 属性 | 说明 |
|:--- |:--- |
| 类型 | 日志事件的名称，AmlComputeInstanceEvent |
| TimeGenerated | 生成日志项的时间 (UTC) |
| Level | 事件的严重级别。 必须是信息性、警告、错误或严重。 |
| ResultType | 事件的状态。 典型值包括“Started”、“In Progress”、“Succeeded”、“Failed”、“Active”和“Resolved”。 |
| CorrelationId | 用于将一组相关事件组合在一起的 GUID（如果适用）。 |
| OperationName | 与日志项关联的操作的名称 |
| 标识 | 执行操作的用户或应用程序的标识。 |
| AadTenantId | 为其提交了该操作的 AAD 租户 ID。 |
| AmlComputeInstanceName | 与日志项关联的计算实例的名称。 |

### <a name="amldatalabelevent-table"></a>AmlDataLabelEvent 表

| 属性 | 说明 |
|:--- |:--- |
| 类型 | 日志事件的名称，AmlDataLabelEvent |
| TimeGenerated | 生成日志项的时间 (UTC) |
| Level | 事件的严重级别。 必须是信息性、警告、错误或严重。 |
| ResultType | 事件的状态。 典型值包括“Started”、“In Progress”、“Succeeded”、“Failed”、“Active”和“Resolved”。 |
| CorrelationId | 用于将一组相关事件组合在一起的 GUID（如果适用）。 |
| OperationName | 与日志项关联的操作的名称 |
| 标识 | 执行操作的用户或应用程序的标识。 |
| AadTenantId | 为其提交了该操作的 AAD 租户 ID。 |
| AmlProjectId | AML 项目的唯一标识符。 |
| AmlProjectName | AML 项目的名称。 |
| AmlLabelNames | 为项目创建的标签类名。 |
| AmlDataStoreName | 用于存储项目数据的数据存储的名称。 |

### <a name="amldatasetevent-table"></a>AmlDataSetEvent 表

| 属性 | 说明 |
|:--- |:--- |
| 类型 | 日志事件的名称，AmlDataSetEvent |
| TimeGenerated | 生成日志项的时间 (UTC) |
| Level | 事件的严重级别。 必须是信息性、警告、错误或严重。 |
| ResultType | 事件的状态。 典型值包括“Started”、“In Progress”、“Succeeded”、“Failed”、“Active”和“Resolved”。 |
| AmlWorkspaceId | AML 工作区的 GUID 和唯一 ID。 |
| OperationName | 与日志项关联的操作的名称 |
| 标识 | 执行操作的用户或应用程序的标识。 |
| AadTenantId | 为其提交了该操作的 AAD 租户 ID。 |
| AmlDatasetId | AML 数据集的 ID。 |
| AmlDatasetName | AML 数据集的名称。 |

### <a name="amldatastoreevent-table"></a>AmlDataStoreEvent 表

| 属性 | 说明 |
|:--- |:--- |
| 类型 | 日志事件的名称，AmlDataStoreEvent |
| TimeGenerated | 生成日志项的时间 (UTC) |
| Level | 事件的严重级别。 必须是信息性、警告、错误或严重。 |
| ResultType | 事件的状态。 典型值包括“Started”、“In Progress”、“Succeeded”、“Failed”、“Active”和“Resolved”。 |
| AmlWorkspaceId | AML 工作区的 GUID 和唯一 ID。 |
| OperationName | 与日志项关联的操作的名称 |
| 标识 | 执行操作的用户或应用程序的标识。 |
| AadTenantId | 为其提交了该操作的 AAD 租户 ID。 |
| AmlDatastoreName | AML 数据存储的名称。 |

### <a name="amldeploymentevent-table"></a>AmlDeploymentEvent 表

| 属性 | 说明 |
|:--- |:--- |
| 类型 | 日志事件的名称，AmlDeploymentEvent |
| TimeGenerated | 生成日志项的时间 (UTC) |
| Level | 事件的严重级别。 必须是信息性、警告、错误或严重。 |
| ResultType | 事件的状态。 典型值包括“Started”、“In Progress”、“Succeeded”、“Failed”、“Active”和“Resolved”。 |
| OperationName | 与日志项关联的操作的名称 |
| 标识 | 执行操作的用户或应用程序的标识。 |
| AadTenantId | 为其提交了该操作的 AAD 租户 ID。 |
| AmlServiceName | AML 服务的名称。 |

### <a name="amlinferencingevent-table"></a>AmlInferencingEvent 表

| 属性 | 说明 |
|:--- |:--- |
| 类型 | 日志事件的名称，AmlInferencingEvent |
| TimeGenerated | 生成日志项的时间 (UTC) |
| Level | 事件的严重级别。 必须是信息性、警告、错误或严重。 |
| ResultType | 事件的状态。 典型值包括“Started”、“In Progress”、“Succeeded”、“Failed”、“Active”和“Resolved”。 |
| OperationName | 与日志项关联的操作的名称 |
| 标识 | 执行操作的用户或应用程序的标识。 |
| AadTenantId | 为其提交了该操作的 AAD 租户 ID。 |
| AmlServiceName | AML 服务的名称。 |

### <a name="amlmodelsevent-table"></a>AmlModelsEvent 表

| 属性 | 说明 |
|:--- |:--- |
| 类型 | 日志事件的名称，AmlModelsEvent |
| TimeGenerated | 生成日志项的时间 (UTC) |
| Level | 事件的严重级别。 必须是信息性、警告、错误或严重。 |
| ResultType | 事件的状态。 典型值包括“Started”、“In Progress”、“Succeeded”、“Failed”、“Active”和“Resolved”。 |
| OperationName | 与日志项关联的操作的名称 |
| 标识 | 执行操作的用户或应用程序的标识。 |
| AadTenantId | 为其提交了该操作的 AAD 租户 ID。 |
| ResultSignature | 事件的 HTTP 状态代码。 典型值包括 200、201、202 等。 |
| AmlModelName | AML 模型的名称。 |

### <a name="amlpipelineevent-table"></a>AmlPipelineEvent 表

| 属性 | 说明 |
|:--- |:--- |
| 类型 | 日志事件的名称，AmlPipelineEvent |
| TimeGenerated | 生成日志项的时间 (UTC) |
| Level | 事件的严重级别。 必须是信息性、警告、错误或严重。 |
| ResultType | 事件的状态。 典型值包括“Started”、“In Progress”、“Succeeded”、“Failed”、“Active”和“Resolved”。 |
| AmlWorkspaceId | AML 工作区的 GUID 和唯一 ID。 |
| AmlWorkspaceId | AML 工作区的名称。 |
| OperationName | 与日志项关联的操作的名称 |
| 标识 | 执行操作的用户或应用程序的标识。 |
| AadTenantId | 为其提交了该操作的 AAD 租户 ID。 |
| AmlModuleId | 模块的 GUID 和唯一 ID。|
| AmlModelName | AML 模型的名称。 |
| AmlPipelineId | AML 管道的 ID。 |
| AmlParentPipelineId | 父 AML 管道的 ID（克隆时）。 |
| AmlPipelineDraftId | AML 管道草稿的 ID。 |
| AmlPipelineDraftName | AML 管道草稿的名称。 |
| AmlPipelineEndpointId | AML 管道终结点的 ID。 |
| AmlPipelineEndpointName | AML 管道终结点的名称。 |


### <a name="amlrunevent-table"></a>AmlRunEvent 表

| 属性 | 说明 |
|:--- |:--- |
| 类型 | 日志事件的名称，AmlRunEvent |
| TimeGenerated | 生成日志项的时间 (UTC) |
| Level | 事件的严重级别。 必须是信息性、警告、错误或严重。 |
| ResultType | 事件的状态。 典型值包括“Started”、“In Progress”、“Succeeded”、“Failed”、“Active”和“Resolved”。 |
| OperationName | 与日志项关联的操作的名称 |
| AmlWorkspaceId | AML 工作区的 GUID 和唯一 ID。 |
| 标识 | 执行操作的用户或应用程序的标识。 |
| AadTenantId | 为其提交了该操作的 AAD 租户 ID。 |
| RunId | 运行的唯一 ID。 |

### <a name="amlenvironmentevent--table"></a>AmlEnvironmentEvent 表

| 属性 | 说明 |
|:--- |:--- |
| 类型 | 日志事件的名称，AmlEnvironmentEvent |
| TimeGenerated | 生成日志项的时间 (UTC) |
| Level | 事件的严重级别。 必须是信息性、警告、错误或严重。 |
| OperationName | 与日志项关联的操作的名称 |
| 标识 | 执行操作的用户或应用程序的标识。 |
| AadTenantId | 为其提交了该操作的 AAD 租户 ID。 |
| AmlEnvironmentName | AML 环境配置的名称。 |
| AmlEnvironmentVersion | AML 环境配置版本的名称。 |


## <a name="see-also"></a>请参阅

- 有关监视 Azure 机器学习的说明，请参阅[监视 Azure 机器学习](monitor-azure-machine-learning.md)。
- 有关监视 Azure 资源的详细信息，请参阅[通过 Azure Monitor 监视 Azure 资源](../azure-monitor/essentials/monitor-azure-resource.md)。
