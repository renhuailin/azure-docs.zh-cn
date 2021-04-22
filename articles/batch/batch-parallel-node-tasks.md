---
title: 以并发方式运行任务以最大程度地利用 Batch 计算节点
description: 通过减少所用的计算节点数并在 Azure Batch 池的每个节点上并行运行任务，来提高效率并降低成本
ms.topic: how-to
ms.date: 04/13/2021
ms.custom: H1Hack27Feb2017, devx-track-csharp
ms.openlocfilehash: 81648f30a7a02f702dcb189aa7df27e5a82e2b07
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389292"
---
# <a name="run-tasks-concurrently-to-maximize-usage-of-batch-compute-nodes"></a>以并发方式运行任务以最大程度地利用 Batch 计算节点

可在池中数量较少的计算节点上最大程度地利用资源，方法是在每个节点上同时运行多个任务。

尽管某些方案在某个节点的所有资源专用于单个任务时效果最好，但当多个任务共享这些资源时，某些工作负载可能会发现作业时间缩短且成本降低。 请考虑下列情形：

- 对于能够共享数据的任务，请 **尽量减少数据传输**。 将共享数据复制到较小数目的节点并在每个节点上并行执行任务可以大大减少数据传输费用， 尤其是在复制到每个节点的数据必须跨地理区域传输的情况下。
- 如果任务需要大量的内存，但这种需要仅在执行过程中短时出现且时间不固定，则请 **尽量增加内存使用**。 可以减少计算节点的数量但增加其大小，同时提供更多的内存，以便有效地应对此类高峰负载。 这些节点将在每个节点上并行运行多个任务，而每个任务都可以充分利用节点在不同时间的大量内存。
- 对于需要在池中进行节点间通信的情况，请 **减少节点数目限制**。 目前，经过配置可以进行节点间通信的池仅限 50 个计算节点。 如果此类池中的每个节点都可以并行执行任务，则可同时执行较大数量的任务。
- **复制本地计算群集**：适用于首次将计算环境移至 Azure 等情况。 如果当前本地解决方案在单个计算节点上执行多个任务，则可以通过增大节点任务的最大数量来更彻底地对配置进行镜像操作。

## <a name="example-scenario"></a>示例方案

例如，假设有一个具有 CPU 和内存要求的任务应用程序，而[标准\_D1](../cloud-services/cloud-services-sizes-specs.md#d-series) 节点足以满足该要求。 但是，若要在所需时间内完成作业，需要使用 1,000 个这样的节点。

如果不使用具有 1 个 CPU 内核的 Standard\_D1 节点，则可使用每个具有 16 个内核的 [Standard\_D14](../cloud-services/cloud-services-sizes-specs.md#d-series) 节点，同时允许并行执行任务。 这意味着可以使用 1/16 的节点，即只需使用 63 个节点，而无需使用 1,000 个节点。 如果需要对每个节点使用大型应用程序文件或引用数据，则可缩短作业持续时间并提高效率，因为只需将数据复制到 63 个节点。

## <a name="enable-parallel-task-execution"></a>允许并行执行任务

可在池级别配置计算节点，以便并行执行任务。 在创建池时，使用 Batch .NET 库设置 [CloudPool.TaskSlotsPerNode](/dotnet/api/microsoft.azure.batch.cloudpool.taskslotspernode) 属性。 如果使用的是 Batch REST API，则可在创建池时在请求正文中设置 [taskSlotsPerNode](/rest/api/batchservice/pool/add) 元素。

> [!NOTE]
> 只能在创建池时设置 `taskSlotsPerNode` 元素和 [TaskSlotsPerNode](/dotnet/api/microsoft.azure.batch.cloudpool) 属性。 创建完池以后，不能对上述元素和属性进行修改。

Azure Batch 允许你将每节点的任务槽数最多设置为节点核心数的 4 倍。 例如，如果将池的节点大小配置为“大型”（四核），则可将 `taskSlotsPerNode` 设置为 16。 但是，无论节点有多少个核心，每个节点的任务槽数都不能超过 256 个。 有关每个节点大小的核心数的详细信息，请参阅[云服务的大小](../cloud-services/cloud-services-sizes-specs.md)。 有关服务限制的详细信息，请参阅 [Azure Batch 服务的配额和限制](batch-quota-limit.md)。

> [!TIP]
> 为池构造[自动缩放公式](/rest/api/batchservice/pool/enableautoscale)时，请务必考虑 `taskSlotsPerNode` 值。 例如，如果增加每个节点的任务数，则可能会极大地影响对 `$RunningTasks` 求值的公式。 有关详细信息，请参阅[自动缩放 Azure Batch 池中的计算节点](batch-automatic-scaling.md)。

## <a name="specify-task-distribution"></a>指定任务分布

当启用并发任务时，请务必指定任务在池中各节点之间的分布方式。

可以通过 [CloudPool.TaskSchedulingPolicy](/dotnet/api/microsoft.azure.batch.cloudpool.taskschedulingpolicy) 属性指定任务，即让任务在池中所有节点之间平均分配（“散布式”）。 或者，先给池中的每个节点分配尽量多的任务，再将任务分配给池中的其他节点（“装箱式”）。

例如，可参阅上面示例中 [Standard\_D14](../cloud-services/cloud-services-sizes-specs.md#d-series) 节点的池，该池配置后的 [CloudPool.TaskSlotsPerNode](/dotnet/api/microsoft.azure.batch.cloudpool.taskslotspernode) 值为 16。 如果在对 [CloudPool.TaskSchedulingPolicy](/dotnet/api/microsoft.azure.batch.cloudpool.taskschedulingpolicy) 进行配置时，将 [ComputeNodeFillType](/dotnet/api/microsoft.azure.batch.common.computenodefilltype) 设置为 Pack，则会充分使用每个节点的所有 16 个核心，并可通过[自动缩放池](batch-automatic-scaling.md)将不使用的节点（没有分配任何任务的节点）从池中删除。 这可以最大程度地减少资源使用量并节省资金。

## <a name="define-variable-slots-per-task"></a>定义每任务可变槽数

可以通过 [CloudTask.RequiredSlots](/dotnet/api/microsoft.azure.batch.cloudtask.requiredslots) 属性对任务进行定义，指定它在计算节点上运行时所需的槽数。 默认值为 1。 如果你的任务在计算节点上具有不同的资源使用率权重，则可以设置可变任务槽数。 这使每个计算节点都有合理数量的并发运行任务，而不会滥用系统资源，例如 CPU 或内存。

例如，对于具有属性 `taskSlotsPerNode = 8` 的池，你可以使用 `requiredSlots = 8` 提交需要多核的 CPU 密集型任务，而其他任务可以设置为 `requiredSlots = 1`。 计划此混合工作负荷时，CPU 密集型任务将以独占方式在其计算节点上运行，而其他任务可以在其他节点上并发运行（一次最多八个任务）。 这有助于平衡多个计算节点上的工作负荷，提高资源使用效率。

确保没有将任务的 `requiredSlots` 指定为大于池的 `taskSlotsPerNode`。 这将导致任务永远无法运行。 当你提交任务时，Batch 服务当前不会验证此冲突，因为作业在提交时可能未绑定池，也可能已通过禁用/重新启用将其更改到不同的池。

> [!TIP]
> 使用可变任务槽数时，可能会暂时无法计划具有更多必需槽的大型任务，原因如下：任何计算节点上都没有足够的可用槽，即使某些节点上仍有空闲的槽。 你可以提高这些任务的作业优先级，增加对节点上可用槽的竞争机会。
>
> Batch 服务在无法计划要运行的任务时会发出 [TaskScheduleFailEvent](batch-task-schedule-fail-event.md)，并且始终会在必需的槽变得可用之前重试计划。 你可以侦听该事件以检测潜在的任务计划问题，并相应地进行缓解。

## <a name="batch-net-example"></a>Batch .NET 示例

以下 [Batch .NET](/dotnet/api/microsoft.azure.batch) API 代码片段展示了如何创建一个每个节点具有多个任务槽的池，以及如何提交具有必需槽的任务。

### <a name="create-a-pool-with-multiple-task-slots-per-node"></a>创建每个节点上具有多个任务槽的池

此代码片段演示了一个请求，该请求要求创建一个包含四个节点的池，每个节点允许有四个任务槽。 它指定了一个任务计划策略，要求先用任务填充一个节点，然后再将任务分配给池中的其他节点。

有关如何使用 Batch .NET API 添加池的详细信息，请参阅 [BatchClient.PoolOperations.CreatePool](/dotnet/api/microsoft.azure.batch.pooloperations.createpool)。

```csharp
CloudPool pool =
    batchClient.PoolOperations.CreatePool(
        poolId: "mypool",
        targetDedicatedComputeNodes: 4
        virtualMachineSize: "standard_d1_v2",
        VirtualMachineConfiguration: new VirtualMachineConfiguration(
            imageReference: new ImageReference(
                                publisher: "MicrosoftWindowsServer",
                                offer: "WindowsServer",
                                sku: "2019-datacenter-core",
                                version: "latest"),
            nodeAgentSkuId: "batch.node.windows amd64");

pool.TaskSlotsPerNode = 4;
pool.TaskSchedulingPolicy = new TaskSchedulingPolicy(ComputeNodeFillType.Pack);
pool.Commit();
```

### <a name="create-a-task-with-required-slots"></a>创建具有必需槽的任务

此代码片段创建一个具有非默认 `requiredSlots` 的任务。 仅当计算节点上有足够的空闲槽可用时，此任务才会运行。

```csharp
CloudTask task = new CloudTask(taskId, taskCommandLine)
{
    RequiredSlots = 2
};
```

### <a name="list-compute-nodes-with-counts-for-running-tasks-and-slots"></a>列出计算节点以及正在运行的任务和槽的计数

此代码片段列出池中的所有计算节点，并输出每个节点正在运行的任务和任务槽的计数。

```csharp
ODATADetailLevel nodeDetail = new ODATADetailLevel(selectClause: "id,runningTasksCount,runningTaskSlotsCount");
IPagedEnumerable<ComputeNode> nodes = batchClient.PoolOperations.ListComputeNodes(poolId, nodeDetail);

await nodes.ForEachAsync(node =>
{
    Console.WriteLine(node.Id + " :");
    Console.WriteLine($"RunningTasks = {node.RunningTasksCount}, RunningTaskSlots = {node.RunningTaskSlotsCount}");

}).ConfigureAwait(continueOnCapturedContext: false);
```

### <a name="list-task-counts-for-the-job"></a>列出作业的任务计数

此代码片段获取作业的任务计数，其中包含每个任务状态的任务和任务槽计数。

```csharp
TaskCountsResult result = await batchClient.JobOperations.GetJobTaskCountsAsync(jobId);

Console.WriteLine("\t\tActive\tRunning\tCompleted");
Console.WriteLine($"TaskCounts:\t{result.TaskCounts.Active}\t{result.TaskCounts.Running}\t{result.TaskCounts.Completed}");
Console.WriteLine($"TaskSlotCounts:\t{result.TaskSlotCounts.Active}\t{result.TaskSlotCounts.Running}\t{result.TaskSlotCounts.Completed}");
```

## <a name="batch-rest-example"></a>Batch REST 示例

以下 [Batch REST](/rest/api/batchservice/) API 代码片段展示了如何创建一个每个节点具有多个任务槽的池，以及如何提交具有必需槽的任务。

### <a name="create-a-pool-with-multiple-task-slots-per-node"></a>创建每个节点上具有多个任务槽的池

此代码片段演示了一个请求，该请求要求创建一个包含两个大型节点的池，每个节点最多四个任务。

有关如何使用 REST API 添加池的详细信息，请参阅[将池添加到帐户](/rest/api/batchservice/pool/add)。

```json
{
  "odata.metadata":"https://myaccount.myregion.batch.azure.com/$metadata#pools/@Element",
  "id":"mypool",
  "vmSize":"large",
  "virtualMachineConfiguration": {
    "imageReference": {
      "publisher": "canonical",
      "offer": "ubuntuserver",
      "sku": "18.04-lts"
    },
    "nodeAgentSKUId": "batch.node.ubuntu 16.04"
  },
  "targetDedicatedComputeNodes":2,
  "taskSlotsPerNode":4,
  "enableInterNodeCommunication":true,
}
```

### <a name="create-a-task-with-required-slots"></a>创建具有必需槽的任务

此代码片段展示了一个请求，该请求要求添加具有非默认 `requiredSlots` 的任务。 仅当计算节点上有足够的空闲槽可用时，此任务才会运行。

```json
{
  "id": "taskId",
  "commandLine": "bash -c 'echo hello'",
  "userIdentity": {
    "autoUser": {
      "scope": "task",
      "elevationLevel": "nonadmin"
    }
  },
  "requiredSLots": 2
}
```

## <a name="code-sample-on-github"></a>GitHub 上的代码示例

GitHub 上的 [ParallelTasks](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ParallelTasks) 项目说明了如何使用 [CloudPool.TaskSlotsPerNode](/dotnet/api/microsoft.azure.batch.cloudpool.taskslotspernode) 属性。

此 C# 控制台应用程序使用 [Batch .NET](/dotnet/api/microsoft.azure.batch) 库创建包含一个或多个计算节点的池。 它在这些节点上执行其数量可以配置的任务，以便模拟可变负荷。 应用程序的输出显示了哪些节点执行了每个任务。 该应用程序还提供了作业参数和持续时间的摘要。

例如，下面显示了 ParallelTasks 示例应用程序运行两次后的输出摘要部分。 此处显示的作业持续时间不包括创建池的时间，因为每个作业都提交到先前创建的、其计算节点在提交时处于空闲状态的池。

第一次执行示例应用程序时，结果显示，在池中只有一个节点且使用默认的一个节点一个任务设置的情况下，作业持续时间超过 30 分钟。

```
Nodes: 1
Node size: large
Task slots per node: 1
Max slots per task: 1
Tasks: 32
Duration: 00:30:01.4638023
```

第二次运行示例应用程序时，显示作业持续时间显著缩短。 这是因为该池已被配置为每个节点四个任务，因此可以并行执行任务，使得作业可以在大约四分之一的时间内完成。

```
Nodes: 1
Node size: large
Task slots per node: 4
Max slots per task: 1
Tasks: 32
Duration: 00:08:48.2423500
```

## <a name="next-steps"></a>后续步骤

- 尝试 [Batch Explorer](https://azure.github.io/BatchExplorer/) 热度地图。 Batch Explorer 是一个功能丰富的免费独立客户端工具，可帮助创建、调试和监视 Azure Batch 应用程序。 执行 [ParallelTasks](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ParallelTasks) 示例应用程序时，可以使用 Batch Explorer 热度地图功能轻松显示每个节点上并行任务的执行情况。
- 探究 [GitHub 上的 Azure Batch 示例](https://github.com/Azure/azure-batch-samples)。
- 详细了解 [Batch 任务依赖项](batch-task-dependencies.md)。
