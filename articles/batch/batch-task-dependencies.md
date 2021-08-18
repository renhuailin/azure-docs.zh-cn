---
title: 创建任务依赖项以运行任务
description: 在 Azure Batch 中创建依赖于其他任务的完成的任务，以处理 MapReduce 样式和类似的大数据工作负荷。
ms.topic: how-to
ms.date: 06/29/2021
ms.custom: H1Hack27Feb2017, devx-track-csharp
ms.openlocfilehash: 0cd5e0bc97d37e4daf76bee66c8b4de4698b7aca
ms.sourcegitcommit: 695a33a2123429289ac316028265711a79542b1c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/01/2021
ms.locfileid: "113126511"
---
# <a name="create-task-dependencies-to-run-tasks-that-depend-on-other-tasks"></a>创建任务依赖关系，以运行依赖于其他任务的任务

使用 Batch 任务依赖关系，可以创建在完成一个或多个父任务后在计算节点上按计划执行的任务。 例如，可以创建一个作业，使用单独的并行任务渲染 3D 影片的每个帧。 最后一个任务仅在所有帧已成功渲染后，才将渲染的帧合并为完整影片。 换句话说，最后一个任务依赖于前面的父任务。

任务依赖关系可发挥作用的部分方案包括：

- 云中的 MapReduce 样式工作负荷。
- 数据处理任务可以表示为有向无环图 (DAG) 的作业。
- 渲染前和渲染后过程，其中只有在完成每个任务后，其后续任务才能开始。
- 下游任务依赖于上游任务输出的任何其他作业。

默认情况下，依赖任务计划为仅在成功完成父任务后执行。 你可以选择指定一个[依赖关系操作](#dependency-actions)来替代默认行为，并在父任务失败时运行依赖任务。

本文讨论如何使用 [Batch .NET](/dotnet/api/microsoft.azure.batch) 库配置任务依赖关系。 本文首先说明如何为作业[启用任务依赖关系](#enable-task-dependencies)，然后演示如何[为任务配置依赖关系](#create-dependent-tasks)。 本文还介绍如何指定一个依赖关系操作，以便在父任务失败时运行依赖任务。 最后介绍 Batch 支持的[依赖关系方案](#dependency-scenarios)。

## <a name="enable-task-dependencies"></a>启用任务依赖关系

要在批处理应用程序中使用任务依赖关系，必须先将作业配置为使用任务依赖关系。 在 Batch .NET 中，为 [CloudJob](/dotnet/api/microsoft.azure.batch.cloudjob) 启用任务依赖关系的方法是将其 [UsesTaskDependencies](/dotnet/api/microsoft.azure.batch.cloudjob.usestaskdependencies) 属性设置为 `true`：

```csharp
CloudJob unboundJob = batchClient.JobOperations.CreateJob( "job001",
    new PoolInformation { PoolId = "pool001" });

// IMPORTANT: This is REQUIRED for using task dependencies.
unboundJob.UsesTaskDependencies = true;
```

在以上代码片段中，“batchClient”是 [BatchClient](/dotnet/api/microsoft.azure.batch.batchclient) 类的一个实例。

## <a name="create-dependent-tasks"></a>创建依赖任务

若要创建一个依赖于一个或多个父任务的完成的任务，可以指定该任务必须“依赖于”其他任务。 在 Batch .NET 中，为 [CloudTask.DependsOn](/dotnet/api/microsoft.azure.batch.cloudtask.dependson) 属性配置 [TaskDependencies](/dotnet/api/microsoft.azure.batch.taskdependencies) 类的一个实例：

```csharp
// Task 'Flowers' depends on completion of both 'Rain' and 'Sun'
// before it is run.
new CloudTask("Flowers", "cmd.exe /c echo Flowers")
{
    DependsOn = TaskDependencies.OnIds("Rain", "Sun")
},
```

此代码片段创建任务 ID 为“Flowers”的依赖任务。 “Flowers”任务依赖于“Rain”和“Sun”任务。 “Flowers”任务将计划为仅在“Rain”和“Sun”任务已成功完成后才在计算节点上运行。

> [!NOTE]
> 默认情况下，当任务处于已完成状态并且其退出代码为 `0` 时，该任务被视为已成功完成。 在 Batch .NET 中，这意味着 [CloudTask.State](/dotnet/api/microsoft.azure.batch.cloudtask.state) 属性值为 `Completed`，并且 CloudTask 的 [TaskExecutionInformation.ExitCode](/dotnet/api/microsoft.azure.batch.taskexecutioninformation.exitcode) 属性值为 `0`。 若要了解如何更改此设置，请参阅[依赖关系操作](#dependency-actions)部分。

## <a name="dependency-scenarios"></a>依赖关系方案

可以在 Azure Batch 中使用三种基本任务依赖关系方案：一对一、一对多和任务 ID 范围依赖关系。 可以组合这三种方案来提供第四种方案：多对多。

| 方案&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | 示例 | 图示 |
|:---:| --- | --- |
|  [一对一](#one-to-one) |*taskB* 取决于 *taskA* <p/> *taskA* 成功完成后，*taskB* 才会按计划执行 |:::image type="content" source="media/batch-task-dependency/01_one_to_one.png" alt-text="示意图中显示了一对一任务依赖关系方案。"::: |
|  [一对多](#one-to-many) |*taskC* 同时取决于 *taskA* 和 *taskB* <p/> *taskA* 和 *taskB* 成功完成后，*taskC* 才会按计划执行 |:::image type="content" source="media/batch-task-dependency/02_one_to_many.png" alt-text="示意图中显示了一对多任务依赖关系方案。"::: |
|  [任务 ID 范围](#task-id-range) |*taskD* 取决于一系列任务 <p/> ID 为 *1* 到 *10* 的任务成功完成后，*taskD* 才会按计划执行 |:::image type="content" source="media/batch-task-dependency/03_task_id_range.png" alt-text="示意图中显示了任务 ID 范围任务依赖关系方案。"::: |

> [!TIP]
> 可以创建 **多对多** 关系，例如，在此关系中任务 C、D、E 和 F 都依赖于任务 A 和 B。这很有用，例如，在下游任务依赖于多个上游任务的输出的并行化预处理方案中，即可以这样操作。
> 
> 在本部分的示例中，仅在父任务成功完成时才运行依赖任务。 此行为是依赖任务的默认行为。 你可以通过指定一个[依赖关系操作](#dependency-actions)来替代默认行为，在父任务失败后运行依赖项任务。

### <a name="one-to-one"></a>一对一

在一对一关系中，任务依赖于一个父任务的成功完成。 若要创建该依赖关系，请在填充 [CloudTask.DependsOn](/dotnet/api/microsoft.azure.batch.cloudtask.dependson) 属性时，为 [TaskDependencies.OnId](/dotnet/api/microsoft.azure.batch.taskdependencies.onid) 静态方法提供单个任务 ID。

```csharp
// Task 'taskA' doesn't depend on any other tasks
new CloudTask("taskA", "cmd.exe /c echo taskA"),

// Task 'taskB' depends on completion of task 'taskA'
new CloudTask("taskB", "cmd.exe /c echo taskB")
{
    DependsOn = TaskDependencies.OnId("taskA")
},
```

### <a name="one-to-many"></a>一对多

在一对多关系中，任务依赖于多个父任务的完成。 若要创建该依赖关系，请在填充 [CloudTask.DependsOn](/dotnet/api/microsoft.azure.batch.cloudtask.dependson) 属性时，为 [TaskDependencies.OnIds](/dotnet/api/microsoft.azure.batch.taskdependencies.onids) 静态方法提供特定任务 ID 的集合。

```csharp
// 'Rain' and 'Sun' don't depend on any other tasks
new CloudTask("Rain", "cmd.exe /c echo Rain"),
new CloudTask("Sun", "cmd.exe /c echo Sun"),

// Task 'Flowers' depends on completion of both 'Rain' and 'Sun'
// before it is run.
new CloudTask("Flowers", "cmd.exe /c echo Flowers")
{
    DependsOn = TaskDependencies.OnIds("Rain", "Sun")
},
```

> [!IMPORTANT]
> 如果父任务 ID 的组合长度大于 64000 个字符，则创建依赖任务将失败。 若要指定大量的父任务，请考虑改为使用任务 ID 范围。

### <a name="task-id-range"></a>任务 ID 范围

在依赖于一系列父任务的关系中，任务依赖于其 ID 位于指定的范围内的任务的完成情况。

若要创建该依赖关系，请在填充 [CloudTask.DependsOn](/dotnet/api/microsoft.azure.batch.cloudtask.dependson) 属性时，为 [TaskDependencies.OnIdRange](/dotnet/api/microsoft.azure.batch.taskdependencies.onidrange) 静态方法提供该范围内的第一个和最后一个任务 ID。

> [!IMPORTANT]
> 将任务 ID 范围用于依赖项时，只有 ID 表示整数值的任务将由范围选定。 例如，范围 `1..10` 会选择任务 `3` 和 `7`，但不选择 `5flamingoes`。
>
> 在评估范围依赖项时，前导零不重要，因此，带字符串标识符 `4`、`04` 和 `004` 的任务都将处于范围内，因为它们将全部视为任务 `4`，并且要完成的第一个任务将满足依赖项。
>
> 对于要运行的依赖任务，范围内的每个任务都必须满足该依赖关系，这分为两种情况：一种情况是成功完成，另一种情况是已完成，但出现了失败，该失败映射到设置为“Satisfy”的某个[依赖关系操作](#dependency-actions)。

```csharp
// Tasks 1, 2, and 3 don't depend on any other tasks. Because
// we will be using them for a task range dependency, we must
// specify string representations of integers as their ids.
new CloudTask("1", "cmd.exe /c echo 1"),
new CloudTask("2", "cmd.exe /c echo 2"),
new CloudTask("3", "cmd.exe /c echo 3"),

// Task 4 depends on a range of tasks, 1 through 3
new CloudTask("4", "cmd.exe /c echo 4")
{
    // To use a range of tasks, their ids must be integer values.
    // Note that we pass integers as parameters to TaskIdRange,
    // but their ids (above) are string representations of the ids.
    DependsOn = TaskDependencies.OnIdRange(1, 3)
},
```

## <a name="dependency-actions"></a>依赖关系操作

默认情况下，只有在父任务成功完成后，才能运行某个依赖任务或任务集。 在某些情况下，建议运行依赖任务，即使父任务失败。 你可以通过指定一个依赖关系操作来替代默认行为，该操作会指示依赖任务是否有资格运行。

例如，假设某个依赖任务正在等待完成上游任务后提供的数据。 如果上游任务失败，依赖任务仍可使用旧数据运行。 在这种情况下，依赖关系操作可以指定即使父任务失败，依赖任务也符合运行的条件。

依赖关系操作基于父任务的退出条件。 可为以下任一退出条件指定依赖关系操作：

- 发生预处理错误时。
- 发生文件上传错误时。 如果任务退出，并返回通过“exitCodes”或“exitCodeRanges”指定的退出代码，然后遇到文件上传错误，则优先执行退出代码指定的操作 。
- 任务退出并返回“ExitCodes”属性定义的退出代码时。
- 任务退出并返回处于“ExitCodeRanges”属性指定的范围内的退出代码时。
- 如果任务退出，并返回非由“ExitCodes”或“ExitCodeRanges”定义的退出代码，或者如果任务退出，并返回预处理错误，而“PreProcessingError”属性未设置，或者如果任务失败，并返回文件上传错误，而“FileUploadError”属性未设置，则为默认情况   。 

对于 .NET，这些条件定义为 [ExitConditions ](/dotnet/api/microsoft.azure.batch.exitconditions) 类的属性。

若要指定依赖关系操作，请将退出条件的 [ExitOptions.DependencyAction](/dotnet/api/microsoft.azure.batch.exitoptions.dependencyaction) 属性设置为下列项之一：

- Satisfy：指示如果父任务退出并返回了指定的错误，则依赖项任务有资格运行。
- **阻止**：指示依赖项任务没有资格运行。

对于退出代码 0，**DependencyAction** 属性的默认设置为 **Satisfy**；对于其他退出条件，其默认设置为 **Block**。

以下代码片段设置父任务的 **DependencyAction** 属性。 如果父任务退出并返回预处理错误或指定的错误代码，则依赖任务将被阻止。 如果父任务退出并返回其他任何非零错误，依赖任务将符合运行的条件。

```csharp
// Task A is the parent task.
new CloudTask("A", "cmd.exe /c echo A")
{
    // Specify exit conditions for task A and their dependency actions.
    ExitConditions = new ExitConditions
    {
        // If task A exits with a pre-processing error, block any downstream tasks (in this example, task B).
        PreProcessingError = new ExitOptions
        {
            DependencyAction = DependencyAction.Block
        },
        // If task A exits with the specified error codes, block any downstream tasks (in this example, task B).
        ExitCodes = new List<ExitCodeMapping>
        {
            new ExitCodeMapping(10, new ExitOptions() { DependencyAction = DependencyAction.Block }),
            new ExitCodeMapping(20, new ExitOptions() { DependencyAction = DependencyAction.Block })
        },
        // If task A succeeds or fails with any other error, any downstream tasks become eligible to run 
        // (in this example, task B).
        Default = new ExitOptions
        {
            DependencyAction = DependencyAction.Satisfy
        }
    }
},
// Task B depends on task A. Whether it becomes eligible to run depends on how task A exits.
new CloudTask("B", "cmd.exe /c echo B")
{
    DependsOn = TaskDependencies.OnId("A")
},
```

## <a name="code-sample"></a>代码示例

GitHub 上的 [TaskDependencies](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/TaskDependencies) 示例项目演示了：

- 如何在作业上启用任务依赖关系。
- 如何创建依赖于其他任务的任务。
- 如何在计算节点池中执行这些任务。

## <a name="next-steps"></a>后续步骤

- 了解 Batch 的[应用程序包](batch-application-packages.md)功能。使用该功能，可以轻松地部署任务在计算节点上执行的应用程序并对其进行版本控制。
- 了解[作业和任务的错误检查](batch-job-task-error-checking.md)。
