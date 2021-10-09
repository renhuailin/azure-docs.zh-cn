---
title: 创建任务来准备和完成计算节点上的作业
description: 使用作业级准备任务最大程度地减少 Azure Batch 计算节点的数据传输，在完成作业时执行释放任务来清理节点。
ms.topic: how-to
ms.date: 09/10/2021
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 66be694cc05655973afac088066e95c6d7033bb2
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128659733"
---
# <a name="run-job-preparation-and-job-release-tasks-on-batch-compute-nodes"></a>在 Batch 计算节点上运行作业准备和作业发布任务

 Azure Batch 作业在执行其任务之前通常需要某种形式的设置。 当其任务完成时，它还可能需要作业后维护。 例如，可能需要将常见的任务输入数据下载到计算节点，或者在作业完成之后，将任务输出数据上传到 Azure 存储。 可以使用“作业准备”和“作业释放”任务来执行这些操作。

## <a name="what-are-job-preparation-and-release-tasks"></a>什么是作业准备和作业释放任务？

在运行作业的任务之前，作业准备任务在计划要运行至少一个任务的所有计算节点上运行。 作业完成后，作业释放任务会在池中至少运行了一个任务的每个节点上运行。 与普通的 Batch 任务一样，可以指定在运行作业准备或释放任务时要调用的命令行。

作业准备和释放任务提供了许多熟悉的 Batch 任务功能，例如文件下载（[资源文件](/dotnet/api/microsoft.azure.batch.jobpreparationtask.resourcefiles)）、提升权限的执行、自定义环境变量、最大执行持续时间、重试计数和文件保留时间。

以下部分介绍如何使用 [Batch .NET](/dotnet/api/microsoft.azure.batch) 库中的 [JobPreparationTask](/dotnet/api/microsoft.azure.batch.jobpreparationtask) 和 [JobReleaseTask](/dotnet/api/microsoft.azure.batch.jobreleasetask) 类。

> [!TIP]
> 作业准备和释放任务在“共享池”环境中特别有用。在这些环境中，计算节点池在任务运行之间保留，并由许多作业使用。

## <a name="when-to-use-job-preparation-and-release-tasks"></a>何时使用作业准备和释放任务

作业准备和作业释放任务适用于以下情况：

- 下载常见任务数据：Batch 作业通常需要一组通用的数据作为作业任务的输入。 例如，在每日风险分析计算中，市场数据特定于作业，同时也是作业中所有任务通用的数据。 这些市场数据（大小通常为若干 GB）应该只下载到每个计算节点一次，以供节点上运行的任意任务使用。 在执行作业的其他任务之前，使用“作业准备任务”将此数据下载到每个节点。

- 作业和任务输出删除：在“共享池”环境中，作业之间的池计算节点不会解除，因此可能需要删除运行之间的作业数据。 可能需要保留节点上的磁盘空间，或符合组织的安全策略。 使用作业释放任务删除作业准备任务下载的数据或者在任务执行期间生成的数据。

- 日志保留：可能想要保留任务生成的日志文件的副本，或失败应用程序可能生成的崩溃转储文件。 在此类情况下，使用作业释放任务将此数据压缩并上传到 [Azure 存储帐户](accounts.md#azure-storage-accounts)。

## <a name="job-preparation-task"></a>作业准备任务

在执行作业中的任务之前，Batch 在计划运行任务的每个计算节点上执行作业准备任务。 默认情况下，Batch 会等到作业准备任务完成后才在节点上运行计划的任务。 但可以将该服务配置为不要等待。 如果节点重启，作业准备任务将重新运行。 你也可禁用此行为。 如果某项作业已配置作业准备任务和作业管理器任务，则如同对其他所有任务之前，作业准备任务将先于作业管理器任务运行。 先运行始终是作业准备任务。

作业准备任务只会在计划运行任务的节点上运行。 这可以防止在没有为节点分配任何任务的情况下不必要地执行准备任务。 当作业的任务数小于池中的节点数时，可能会出现这种情况。 此外，这也适用于在任务计数小于可能的并行任务总数的情况下启用[并行任务执行](batch-parallel-node-tasks.md)，从而留出一些空闲节点的情况。

> [!NOTE]
> [JobPreparationTask](/dotnet/api/microsoft.azure.batch.cloudjob.jobpreparationtask) 与 [CloudPool.StartTask](/dotnet/api/microsoft.azure.batch.cloudpool.starttask) 的不同之处在于，JobPreparationTask 在每个作业启动时执行，而 StartTask 只在计算节点首次加入池或重启时执行。

## <a name="job-release-task"></a>作业释放任务

将作业标记为完成后，作业释放任务会在池中至少运行了一个任务的每个节点上执行。 可以通过发出终止请求将作业标记为已完成。 此请求会将作业状态设置为“正在终止”，从而终止与作业关联的任何活动任务或正在运行的任务，并运行作业释放任务。 然后，该作业将进入已完成状态。

> [!NOTE]
> 删除作业操作也会执行作业释放任务。 但是，如果已经终止了某个作业，则以后删除该作业时，释放任务不会再次运行。

作业释放任务在 Batch 服务终止之前最多可运行 15 分钟。 有关详细信息，请查看 [REST API 参考文档](/rest/api/batchservice/job/add#jobreleasetask)。

## <a name="job-prep-and-release-tasks-with-batch-net"></a>使用 Batch .NET 执行作业准备和释放任务

要使用作业准备任务，可将 [JobPreparationTask](/dotnet/api/microsoft.azure.batch.jobpreparationtask) 对象分配到作业的 [CloudJob.JobPreparationTask](/dotnet/api/microsoft.azure.batch.cloudjob.jobpreparationtask) 属性。 同样，若要使用作业释放任务，请初始化 [JobReleaseTask](/dotnet/api/microsoft.azure.batch.jobreleasetask) 并将它分配到作业的 [CloudJob.JobReleaseTask](/dotnet/api/microsoft.azure.batch.cloudjob.jobreleasetask)。

在此代码片段中，`myBatchClient` 是 [BatchClient](/dotnet/api/microsoft.azure.batch.batchclient) 的实例，`myPool` 是 Batch 帐户中的现有池。

```csharp
// Create the CloudJob for CloudPool "myPool"
CloudJob myJob =
    myBatchClient.JobOperations.CreateJob(
        "JobPrepReleaseSampleJob",
        new PoolInformation() { PoolId = "myPool" });

// Specify the command lines for the job preparation and release tasks
string jobPrepCmdLine =
    "cmd /c echo %AZ_BATCH_NODE_ID% > %AZ_BATCH_NODE_SHARED_DIR%\\shared_file.txt";
string jobReleaseCmdLine =
    "cmd /c del %AZ_BATCH_NODE_SHARED_DIR%\\shared_file.txt";

// Assign the job preparation task to the job
myJob.JobPreparationTask =
    new JobPreparationTask { CommandLine = jobPrepCmdLine };

// Assign the job release task to the job
myJob.JobReleaseTask =
    new JobReleaseTask { CommandLine = jobReleaseCmdLine };

await myJob.CommitAsync();
```

如前所述，终止或删除作业时会执行释放任务。 使用 [JobOperations.TerminateJobAsync](/dotnet/api/microsoft.azure.batch.joboperations.terminatejobasync) 终止作业。 使用 [JobOperations.DeleteJobAsync](/dotnet/api/microsoft.azure.batch.joboperations.deletejobasync) 删除作业。 通常在作业的任务完成时或者达到定义的超时时终止或删除操作。

```csharp
// Terminate the job to mark it as completed; this will initiate the
// job release task on any node that executed job tasks. Note that the
// job release task is also executed when a job is deleted, so you don't
// have to call Terminate if you delete jobs after task completion.

await myBatchClient.JobOperations.TerminateJobAsync("JobPrepReleaseSampleJob");
```

## <a name="code-sample-on-github"></a>GitHub 上的代码示例

要了解作业准备和释放任务的实际使用情况，请查看 GitHub 上的 [JobPrepRelease](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp/ArticleProjects/JobPrepRelease) 示例项目。 此控制台应用程序将执行以下操作：

1. 创建包含两个节点的池。
1. 创建具有作业准备、释放和标准任务的作业。
1. 运行作业准备任务，该任务首先会将节点 ID 写入节点的“共享”目录中的文本文件。
1. 在每个节点上运行一个任务，该任务将其任务 ID 写入同一文本文件。
1. 完成所有任务（或达到超时）后，将每个节点的文本文件内容输出到控制台。
1. 完成作业后，运行作业释放任务以从节点中删除该文件。
1. 输出执行作业准备和释放任务的每个节点上的这些任务的退出代码。
1. 暂停执行，以便确认删除作业和/或池。

示例应用程序的输出类似于：

```
Attempting to create pool: JobPrepReleaseSamplePool
Created pool JobPrepReleaseSamplePool with 2 nodes
Checking for existing job JobPrepReleaseSampleJob...
Job JobPrepReleaseSampleJob not found, creating...
Submitting tasks and awaiting completion...
All tasks completed.

Contents of shared\job_prep_and_release.txt on tvm-2434664350_1-20160623t173951z:
-------------------------------------------
tvm-2434664350_1-20160623t173951z tasks:
  task001
  task004
  task005
  task006

Contents of shared\job_prep_and_release.txt on tvm-2434664350_2-20160623t173951z:
-------------------------------------------
tvm-2434664350_2-20160623t173951z tasks:
  task008
  task002
  task003
  task007

Waiting for job JobPrepReleaseSampleJob to reach state Completed
...

tvm-2434664350_1-20160623t173951z:
  Prep task exit code:    0
  Release task exit code: 0

tvm-2434664350_2-20160623t173951z:
  Prep task exit code:    0
  Release task exit code: 0

Delete job? [yes] no
yes
Delete pool? [yes] no
yes

Sample complete, hit ENTER to exit...
```

> [!NOTE]
> 由于新池中各个节点的创建和启动时间并不一样（某些节点比其他节点更早做好任务准备），可能看到不同的输出。 具体而言，因为任务快速完成，池的某个节点可能执行作业的所有任务。 如果发生这种情况，会发现未执行任何任务的节点没有作业准备和作业释放任务存在。

### <a name="inspect-job-preparation-and-release-tasks-in-the-azure-portal"></a>在 Azure 门户中检查作业准备和释放任务

可以使用 [Azure 门户](https://portal.azure.com)查看作业及其任务的属性。 运行示例应用程序后，还可以下载由作业的任务修改的共享文本文件。

以下屏幕截图显示了 Azure 门户中出现的“准备任务”边栏选项卡。 在任务完成之后（但在删除作业与池之前），导航到 *JobPrepReleaseSampleJob* 属性，并单击“准备任务”或“释放任务”以查看其属性。

:::image type="content" source="media/batch-job-prep-release/portal-jobprep-01.png" alt-text="显示 Azure 门户中的作业准备任务属性的屏幕截图。":::

## <a name="next-steps"></a>后续步骤

- 了解[作业和任务的错误检查](batch-job-task-error-checking.md)。
- 了解如何使用[应用程序包](batch-application-packages.md)来准备 Batch 计算节点，以便执行任务。
- 探索[将数据和应用程序复制到 Batch 计算节点](batch-applications-to-pool-nodes.md)的不同方式。
- 了解如何使用 [Azure Batch 文件约定库](batch-task-output.md#use-the-batch-file-conventions-library-for-net)来保留日志以及其他作业和任务输出数据。
