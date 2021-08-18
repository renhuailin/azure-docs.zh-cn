---
title: Durable Functions 中的性能和缩放 - Azure
description: 了解 Azure Functions 的 Durable Functions 扩展的独特缩放特征。
author: cgillum
ms.topic: conceptual
ms.date: 05/13/2021
ms.author: azfuncdf
ms.openlocfilehash: 179eb4c5c380a65374143cf24b2f960458fe62b5
ms.sourcegitcommit: f3b930eeacdaebe5a5f25471bc10014a36e52e5e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/16/2021
ms.locfileid: "112240449"
---
# <a name="performance-and-scale-in-durable-functions-azure-functions"></a>Durable Functions 中的性能和缩放 (Azure Functions)

若要优化性能和可伸缩性，必须了解 [Durable Functions](durable-functions-overview.md) 的独特缩放特征。

## <a name="azure-storage-provider"></a>Azure 存储提供程序

Durable Functions 的默认配置将此运行时状态存储在 Azure 存储（经典）帐户中。 所有函数执行都由 Azure 存储队列驱动。 业务流程和实体状态以及历史记录存储在 Azure 表中。 Azure Blob 和 Blob 租用用于将业务流程实例和实体分布到多个应用实例（也称为辅助角色或简称 VM）中 。 本部分详细介绍了各种 Azure 存储项目及其对性能和可伸缩性的影响。

> [!NOTE]
> 本文档主要关注使用默认 Azure 存储提供程序时 Durable Functions 的性能和可伸缩性特征。 不过，也适用于其他存储提供程序。 要详细了解 Durable Functions 支持的存储提供程序以及它们之间的比较，请参阅 [Durable Functions 存储提供程序](durable-functions-storage-providers.md)文档。

### <a name="history-table"></a>历史记录表

“历史记录”表是一个 Azure 存储表，包含任务中心内所有业务流程实例的历史记录事件。 此表的名称采用 *TaskHubName* History 格式。 当实例运行时，会在此表中添加新行。 此表的分区键派生自业务流程的实例 ID。 默认情况下，实例 ID 是随机的，这是为了确保在 Azure 存储中以最佳方式分配内部分区。 此表的行键是用于对历史记录事件进行排序的序列号。

需要运行业务流程实例时，将使用单个表分区内的范围查询将“历史记录”表中的相应行载入内存。 然后，这些历史记录事件将重播到业务流程协调程序函数代码中，使其恢复到以前的检查点状态。 以这种方式使用执行历史记录来重新生成状态不受[事件溯源模式](/azure/architecture/patterns/event-sourcing)的影响。

> [!TIP]
> “历史记录”表中存储的业务流程数据包括来自活动函数和子业务流程函数的输出有效负载。 来自外部事件的有效负载也存储在“历史记录”表中。 由于每次需要执行业务流程时都会将完整的历史记录载入内存，因此，如果历史记录足够大，可能会对指定 VM 带来巨大的内存压力。 可将大型业务流程拆分为多个子业务流程，或减小其调用的活动函数和子业务流程函数返回的输出大小，从而减小业务流程历史记录的长度和大小。 或者，也可降低每个 VM 的[并发限制](#concurrency-throttles)（用于限制同时加载到内存中的业务流程数），从而减少内存使用量。

### <a name="instances-table"></a>实例表

“实例”表包含任务中心内所有业务流程和实体实例的状态。 创建实例时，会在此表中添加新行。 此表的分区键是业务流程实例 ID 或实体键，行键是空字符串。 每个业务流程或实体实例对应一行。

使用此表可满足来自[代码](durable-functions-instance-management.md#query-instances)以及[状态查询 HTTP API](durable-functions-http-api.md#get-instance-status) 调用的实例查询请求。 它与前面所述的“历史记录”表内容保持最终一致。 以这种方式使用单独的 Azure 存储表有效满足实例查询操作不受[命令和查询责任分离 (CQRS) 模式](/azure/architecture/patterns/cqrs)的影响。

> [!TIP]
> “实例”表进行分区后，可存储数百万个业务流程实例，而不会对运行时性能或缩放产生任何显著影响。 但实例数可能会对[多实例查询](durable-functions-instance-management.md#query-all-instances)性能产生重大影响。 若要控制这些表中存储的数据量，请考虑定期[清除旧实例数据](durable-functions-instance-management.md#purge-instance-history)。

### <a name="internal-queue-triggers"></a>内部队列触发器

业务流程协调程序、实体和活动函数均由函数应用任务中心内的内部队列触发。 以这种方式使用队列可以提供可靠的“至少一次”消息传送保证。 Durable Functions 中有两种类型的队列：“控制队列”和“工作项队列” 。

#### <a name="the-work-item-queue"></a>工作项队列

Durable Functions 中的每个任务中心都有一个工作项队列。 它是一个基本队列，其行为类似于 Azure Functions 中的其他任何 `queueTrigger` 队列。 此队列每次将一条消息取消排队，可用于触发无状态活动函数。 其中的每个消息包含活动函数输入和其他元数据，例如要执行的函数。 当 Durable Functions 应用程序横向扩展到多个 VM 时，这些 VM 将会竞争，以从工作项队列中获取任务。

#### <a name="control-queues"></a>控制队列

Durable Functions 中的每个任务中心有多个控制队列。 与较为简单的工作项队列相比，控制队列更加复杂。 控制队列用于触发有状态的业务流程协调程序和实体函数。 由于业务流程协调程序和实体函数实例均为有状态的单一实例，因此，每个业务流程协调程序或实体一次仅由一个辅助角色处理非常重要。 为实现此约束，每个业务流程实例或实体均分配给一个控制队列。 这些控制队列会在辅助角色之间进行负载均衡，以确保每个队列一次仅由一个辅助角色处理。 后续部分将会更详细地介绍此行为。

控制队列包含各种业务流程生命周期消息类型。 示例包括[业务流程协调程序控制消息](durable-functions-instance-management.md)、活动函数响应消息和计时器消息。 在单次轮询中，最多会从一个控制队列中取消 32 条消息的排队。 这些消息包含有效负载数据以及元数据，包括适用的业务流程实例。 如果将多个取消排队的消息用于同一业务流程实例，将会批处理这些消息。

系统将使用后台线程不断轮询控制队列消息。 每个队列轮询的批次大小由 host.json 中的 `controlQueueBatchSize` 设置控制，默认值为 32（Azure 队列支持的最大值）。 内存中缓冲的预提取控制队列消息的最大数目由 host.json 中的 `controlQueueBufferThreshold` 设置控制。 `controlQueueBufferThreshold` 的默认值取决于各种因素，包括托管计划的类型。 要详细了解这些设置，请参阅 [host.json 架构](../functions-host-json.md#durabletask)文档。

> [!TIP]
> 增加 `controlQueueBufferThreshold` 的值可让单个业务流程或实体更快地处理事件。 但增大此值也可能导致内存使用量增加。 内存使用率之所以增高，一部分原因是从队列中提取了更多的消息，还有一部分原因是将更多的业务流程历史记录提取到了内存中。 因此，减小 `controlQueueBufferThreshold` 的值可能是减少内存使用量的有效方法。

#### <a name="queue-polling"></a>队列轮询

Durable Task 扩展实现了随机指数退让算法，以降低空闲队列轮询对存储事务成本造成的影响。 找到消息时，运行时会立即检查另一条消息。 如果未找到消息，它将等待一段时间，然后重试。 如果后续尝试获取队列消息失败，则等待时间会继续增加，直到达到最长等待时间（默认为 30 秒）。

可以通过 [host.json 文件](../functions-host-json.md#durabletask)中的 `maxQueuePollingInterval` 属性配置最大轮询延迟。 将此属性设置为较高的值时，可能导致的消息处理延迟也越高。 只有在不活动的时间段过后，才会出现较高的延迟。 将此属性设置为较低的值时，可能导致的[存储成本会较高](durable-functions-billing.md#azure-storage-transactions)，因为存储事务数增多。

> [!NOTE]
> 在 Azure Functions 消耗计划和高级计划中运行时，[Azure Functions 缩放控制器](../event-driven-scaling.md)每隔 10 秒就会轮询每个控件和工作项队列一次。 若要确定何时激活函数应用实例并进行缩放决策，这种额外的轮询是必需的。 在撰写本文时，这种 10 秒的时间间隔为常量，不能进行配置。

#### <a name="orchestration-start-delays"></a>业务流程启动延迟
通过在某个任务中心的控制队列中放置 `ExecutionStarted` 消息来启动业务流程实例。 在某些情况下，你可能会观察到计划的业务流程运行时间与它实际开始运行的时间之间存在数秒延迟。 在此时间间隔内，业务流程实例仍处于 `Pending` 状态。 造成这种延迟的潜在原因有两个：

1. 积压的控制队列：如果此实例的控制队列包含大量消息，则运行时可能需要一段时间才能接收并处理 `ExecutionStarted` 消息。 当业务流程同时处理大量事件时，可能会出现消息积压。 进入控制队列的事件包括业务流程启动事件、活动完成、持久计时器、终止和外部事件。 如果在正常情况下发生此延迟，请考虑创建具有大量分区的新任务中心。 配置更多分区会使运行时创建更多用于分配负载的控制队列。 每个分区对应 1:1，其中包含一个控制队列，分区数量最多为 16 个。

2. 回退轮询延迟：业务流程延迟的另一个常见原因是之[前所说的控制队列的回退轮询行为](#queue-polling)。 但是，仅当应用横向扩展到两个或更多实例时，才会出现这种延迟。 如果只有一个应用实例，或启动业务流程的应用实例与轮询目标控制队列的是同一个实例，则不会出现队列轮询延迟。 如前所述，可以通过更新 host.json 设置来减少回退轮询延迟。

### <a name="storage-account-selection"></a>存储帐户的选择

在配置的 Azure 存储帐户中创建 Durable Functions 使用的队列、表和 Blob。 可以使用 **host.json** 文件中的 `durableTask/storageProvider/connectionStringName` 设置（或 Durable Functions 1.x 中的 `durableTask/azureStorageConnectionStringName` 设置指定要使用的帐户。

#### <a name="durable-functions-2x"></a>Durable Functions 2.x

```json
{
  "extensions": {
    "durableTask": {
      "storageProvider": {
        "connectionStringName": "MyStorageAccountAppSetting"
      }
    }
  }
}
```

#### <a name="durable-functions-1x"></a>Durable Functions 1.x

```json
{
  "extensions": {
    "durableTask": {
      "azureStorageConnectionStringName": "MyStorageAccountAppSetting"
    }
  }
}
```

如果未指定，则使用默认的 `AzureWebJobsStorage` 存储帐户。 但是，对于性能敏感型工作负荷，我们建议配置非默认的存储帐户。 Durable Functions 重度使用 Azure 存储，它通过专用的存储帐户将 Durable Functions 存储的使用与 Azure Functions 主机的内部使用隔离开来。

> [!NOTE]
> 使用 Azure 存储提供程序时，需要标准常规用途 Azure 存储帐户。 所有其他存储帐户类型均不受支持。 强烈建议将旧版 v1 常规用途存储帐户用于 Durable Functions。 对于 Durable Functions 工作负载，使用较新的 v2 存储帐户可能要贵得多。 若要详细了解 Azure 存储帐户类型，请参阅[存储帐户概述](../../storage/common/storage-account-overview.md)文档。

### <a name="orchestrator-scale-out"></a>业务流程协调程序横向扩展

尽管可以通过灵活添加更多的 VM 横向扩展活动功能，但单个协调程序实例和实体仅可拥有单个分区，最大分区数受到`partitionCount`设置限制（`host.json`中）。 

> [!NOTE]
> 一般而言，业务流程协调程序函数是轻量型的，应该不需要大量的计算能力。 因此，无需创建大量的控制队列分区即可让业务流程获得极佳的吞吐量。 大部分繁重工作应在可无限横向扩展的无状态活动函数中完成。

控制队列的数目在 **host.json** 文件中定义。 以下示例 host.json 片段将 `durableTask/storageProvider/partitionCount` 属性（或 Durable Functions 1.x 中的 `durableTask/partitionCount`）设置为 `3`。 请注意，控制队列与分区数量相同。

#### <a name="durable-functions-2x"></a>Durable Functions 2.x

```json
{
  "extensions": {
    "durableTask": {
      "storageProvider": {
        "partitionCount": 3
      }
    }
  }
}
```

#### <a name="durable-functions-1x"></a>Durable Functions 1.x

```json
{
  "extensions": {
    "durableTask": {
      "partitionCount": 3
    }
  }
}
```

可将任务中心配置为包含 1 到 16 个分区。 如果未指定分区数，则会使用默认分区数 **4**。

通信量较少时，应用程序将缩放，因此分区将由少量的辅助角色管理。 以下图为例。

![缩放业务流程图](./media/durable-functions-perf-and-scale/scale-progression-1.png)

上图表明协调程序 1 到 6 在分区之间进行负载均衡。 同样，分区（如活动）在辅助角色之间进行负载均衡。 无论启动多少业务流程协调程序，分区均可在辅助角色之间进行负载平衡。

如果在 Azure Functions 消耗或弹性高级计划中运行，或者如果配置了基于负载的自动缩放，则在通信量增加时将分配更多的辅助角色，而分区最终会在所有辅助角色之间进行负载均衡。 如果继续横向扩展，则最终每个分区由单个辅助角色管理。 另一方面，活动将继续在所有辅助角色之间进行负载均衡。 下图显示了这种方法。

![首次横向扩展业务流程图](./media/durable-functions-perf-and-scale/scale-progression-2.png)

在“任意给定时间”，最大并发活动业务流程数的上限等于分配给应用程序的辅助角色数量乘以 `maxConcurrentOrchestratorFunctions` 的值。 当分区在辅助角色之间充分横向扩展时，此上限会更精确。 完全向外扩展时，由于每个辅助角色只有单个函数主机实例，因此活动并发业务流程协调程序实例的最大数目等于分区数乘以 `maxConcurrentOrchestratorFunctions` 的值 。

> [!NOTE]
> 在这种情况下，“活动”意味着业务流程或实体加载到了内存中并在处理新事件 。 如果业务流程或实体正在等待更多事件（例如活动函数的返回值），它将从内存中卸载，且不再被视为“活动”。 仅当存在要处理的新事件时，业务流程和实体才会重新加载到内存中。 在单个 VM 上运行的业务流程或实体没有实际的数量上限，即使它们都处于“正在运行”状态也是如此。 唯一的限制是并发活动业务流程或实体实例的数量。

下图展示了充分横向扩展的情况，其中添加了更多的业务流程协调程序，但有些处于非活动状态，以灰色显示。

![第二次横向扩展业务流程图](./media/durable-functions-perf-and-scale/scale-progression-3.png)

横向扩展期间可跨 Functions 主机实例重新分配控制队列租用，确保分区均匀分布。 这些租用在内部以 Azure Blob 存储租用的形式实现，确保一个业务流程实例或实体每次仅在一个主机实例上运行。 如果为任务中心配置了三个分区（三个控制队列），则可以在所有三个包含租约的主机实例上对业务流程实例和实体进行负载均衡。 可以添加更多的 VM，以提高活动函数执行容量。

下图演示了 Azure Functions 主机如何与横向扩展环境中的存储实体交互。

![缩放示意图](./media/durable-functions-perf-and-scale/scale-interactions-diagram.png)

如上图所示，所有 VM 都会争用工作项队列中的消息。 但是，只有三个 VM 可从控制队列获取消息，每个 VM 锁定了单个控制队列。

业务流程实例和实体分布在所有控制队列实例之间。 可以通过哈希处理业务流程的实例 ID 或实体的名称和键对来执行分布。 业务流程实例实例 ID 默认是随机的 GUID，确保将实例均匀分布在所有控制队列之间。

一般而言，业务流程协调程序函数是轻量型的，应该不需要大量的计算能力。 因此，无需创建大量的控制队列分区即可获得业务流程的极佳吞吐量。 大部分繁重工作应在可无限横向扩展的无状态活动函数中完成。

### <a name="auto-scale"></a>自动缩放

与消耗计划和弹性高级计划中运行的所有 Azure Functions 一样，Durable Functions 支持通过 [Azure Functions 缩放控制器](../event-driven-scaling.md#runtime-scaling)自动缩放。 缩放控制器通过定期发出 _peek_ 命令来监视所有队列的延迟。 根据扫视消息的延迟，缩放控制器将决定是要添加还是删除 VM。

如果缩放控制器确定控制队列消息延迟过高，则会添加 VM 实例，直到消息延迟下降到可接受的级别，或者达到控制队列分区计数。 同样，如果工作项队列延迟偏高，缩放控制器会不断地添加 VM 实例，而不管分区计数如何。

> [!NOTE]
> 从 Durable Functions 2.0 开始，可以将函数应用配置为在 Elastic Premium 计划中受 VNET 保护的服务终结点中运行。 在此配置中，Durable Functions 触发器启动缩放请求而不是缩放控制器。 有关详细信息，请参阅[运行时缩放监视](../functions-networking-options.md#premium-plan-with-virtual-network-triggers)。

## <a name="thread-usage"></a>线程用量

在单个线程上执行业务流程协调程序函数，确保多次重播后执行可以是确定性的。 由于采用这种单线程执行，业务流程协调程序函数线程不得执行 CPU 密集型任务、执行 I/O 或出于任何原因而阻塞。 应将需要 I/O、阻塞或多个线程的任何工作移到活动函数中。

活动函数的行为与队列触发的正则函数完全相同。 它们可以安全地执行 I/O、执行 CPU 密集型操作和使用多个线程。 由于活动触发器是无状态的，因此可以任意横向扩展到不限数量的 VM。

实体函数也在单个线程上执行，操作是逐个处理的。 但是，实体函数对于可执行的代码类型没有任何限制。

## <a name="concurrency-throttles"></a>并发限制

Azure Functions 支持在单个应用实例中并发执行多个函数。 这种并发执行有助于提高并行度，将典型的应用在一段时间内遇到的“冷启动”次数减到最少。 但是，高并发性可能会耗尽每个 VM 的系统资源，例如网络连接或可用内存。 根据函数应用的需求，可能有必要限制每个实例的并发性，以免在负载较高的情况下出现内存不足的问题。

可以在 **host.json** 文件中配置活动、业务流程协调程序和实体函数的并发限制。 相关的设置为活动函数的 `durableTask/maxConcurrentActivityFunctions`，以及业务流程协调程序和实体函数的 `durableTask/maxConcurrentOrchestratorFunctions`。 这些设置控制可同时加载到内存中的业务流程协调程序、实体或活动函数的最大数目。

### <a name="functions-20"></a>Functions 2.0

```json
{
  "extensions": {
    "durableTask": {
      "maxConcurrentActivityFunctions": 10,
      "maxConcurrentOrchestratorFunctions": 10
    }
  }
}
```

### <a name="functions-1x"></a>Functions 1.x

```json
{
  "durableTask": {
    "maxConcurrentActivityFunctions": 10,
    "maxConcurrentOrchestratorFunctions": 10
  }
}
```

在前面的示例中，最多 10 个业务流程协调程序或实体函数和 10 个活动函数可在单个 VM 上并发运行。 如果未指定数量，并发活动函数和业务流程协调程序或实体函数的执行数限制为 VM 上核心数的 10 倍。

如果达到了辅助 VM 上的活动或业务流程/实体数量上限，Durable 触发器将等待所有正在执行的函数完成或卸载后才会开始执行新函数。

> [!NOTE]
> 这些设置有助于管理单个 VM 上的内存和 CPU 用量。 但是，在跨多个 VM 横向扩展后，每个 VM 有自身的一组限制。 无法使用这些设置来全局控制并发性。

> [!NOTE]
> 业务流程和实体仅在主动处理事件或操作时才加载到内存中。 执行其逻辑并等待在业务流程协调程序函数代码中命中 `await` (C#) 或 `yield`（JavaScript、Python）语句后，它们将从内存中卸载。 从内存中卸载的业务流程和实体不计入 `maxConcurrentOrchestratorFunctions` 限制。 即使数百万个业务流程或实体处于“正在运行”状态，它们也仅在加载到活动内存时才会计入限制。 如果业务流程正在等待活动完成执行，那么计划活动函数的业务流程同样不计入限制。

### <a name="language-runtime-considerations"></a>语言运行时注意事项

你选择的语言运行时可能会对函数施加严格的并发限制。 例如，采用 Python 或 PowerShell 编写的 Durable Functions 应用可能只支持在单个 VM 上一次运行一个函数。 如果没有仔细考虑，可能会导致严重的性能问题。 例如，如果业务流程协调程序扇出到 10 个活动，但语言运行时将并发数量限制为一个函数，那么 10 个活动函数中有 9 个将会停滞，以等待运行机会。 此外，这 9 个停滞的活动将无法对任何其他辅助角色进行负载均衡，因为 Durable Functions 运行时已将它们加载到内存中。 如果活动函数长时间运行，就特别容易出现问题。

如果使用的语言运行时施加了并发限制，则应该更新 Durable Functions 并发设置，以匹配语言运行时的并发设置。 这样可确保 Durable Functions 运行时不会尝试同时运行比语言运行时允许的并发数量更多的函数，从而将任何挂起的活动均衡到其他 VM。 例如，如果 Python 应用将并发数量限制为 4 个函数（可能只在一个语言工作进程上配置了 4 个线程，或在 4 个语言工作进程上配置了 1 个线程），则应将 `maxConcurrentOrchestratorFunctions` 和 `maxConcurrentActivityFunctions` 配置为 4。

有关 Python 的详细信息和性能建议，请参阅[在 Azure Functions 中提高 Python 应用的吞吐量性能](../python-scale-performance-reference.md)。 此 Python 开发人员参考文档中提到的技术可能会对 Durable Functions 性能和可伸缩性产生重大影响。

## <a name="extended-sessions"></a>扩展会话

扩展会话是一种设置，可将业务流程和实体保留在内存中，即使它们已处理完消息。 启用扩展会话的典型影响是会减少针对基础持久性存储区的 I/O，并提高整体吞吐量。

可以通过在 **host.json** 文件中将 `durableTask/extendedSessionsEnabled` 设置为 `true` 来启用扩展会话。 `durableTask/extendedSessionIdleTimeoutInSeconds` 设置可用于控制空闲会话在内存中的保存时间长短：

**Functions 2.0**
```json
{
  "extensions": {
    "durableTask": {
      "extendedSessionsEnabled": true,
      "extendedSessionIdleTimeoutInSeconds": 30
    }
  }
}
```

**Functions 1.0**
```json
{
  "durableTask": {
    "extendedSessionsEnabled": true,
    "extendedSessionIdleTimeoutInSeconds": 30
  }
}
```

需要注意此设置的两个潜在缺点：

1. 函数应用的内存使用量总体上有所增加，因为空闲实例不会很快从内存中卸载。
2. 如果存在许多并发的且生存期较短的不同业务流程协调程序或实体函数执行，吞吐量可能会总体下降。

例如，如果 `durableTask/extendedSessionIdleTimeoutInSeconds` 设置为 30 秒，则执行时间不超过 1 秒的、生存期较短的业务流程协调程序或实体函数周期仍会占用内存 30 秒。 这种消耗还会计入前面所述的 `durableTask/maxConcurrentOrchestratorFunctions` 配额，从而可能导致其他业务流程协调程序或实体函数无法运行。

后续部分将描述扩展会话对业务流程协调程序和实体函数的具体影响。

> [!NOTE]
> 当前仅在 .NET 语言（如 C# 或 F#）中支持扩展会话。 为其他平台将 `extendedSessionsEnabled` 设置为 `true` 可能会导致运行时问题，例如在执行活动和业务流程触发的功能失败时无提示。

> [!NOTE]
> 对扩展会话的支持可能因[使用的 Durable Functions 存储提供程序](durable-functions-storage-providers.md)而异。 请参阅存储提供程序文档，了解它是否支持扩展会话。

### <a name="orchestrator-function-replay"></a>业务流程协调程序函数重播

如前所述，业务流程协调程序函数是使用“历史记录”表的内容重播的。 默认情况下，每当从控制队列中取消一批消息的排队时，都会重播业务流程协调程序函数代码。 即使你使用扇出、扇入模式并等待所有任务完成（例如，使用 `Task.WhenAll()` ( .NET)、`context.df.Task.all()` (JavaScript) 或 `context.task_all()` (Python)），在一段时间内处理批次任务响应时也会发生重播。 启用扩展会话后，业务流程协调程序函数实例将在内存中保存更长时间，同时，无需重播完整历史记录即可处理新消息。

对于以下情况，往往可以观测到扩展会话对性能的改进：

* 当并发运行的业务流程实例数量有限时。
* 当业务流程包含大量可快速完成的连续操作（例如数百个活动函数调用）时。
* 当业务流程扇出和扇入可在大致相同的时间完成的大量操作时。
* 当业务流程协调程序函数需要处理大型消息或执行任何 CPU 密集型数据处理时。

在所有其他情况下，通常观测不到业务流程协调程序函数有明显的性能改进。

> [!NOTE]
> 只能在全面开发并测试业务流程协调程序函数之后才使用这些设置。 在开发时，默认的激进重播行为可用于检测[业务流程协调程序函数代码约束](durable-functions-code-constraints.md)违规，因此默认已禁用此行为。

### <a name="entity-function-unloading"></a>实体函数卸载

实体函数最多可以处理单个批中的 20 个操作。 实体处理完一批操作后，会保持自身的状态并从内存中卸载。 可以使用扩展会话设置来延迟从内存中卸载实体。 实体继续像以前一样保持其状态更改，但会在配置的时间段内保留在内存中，以减少存储中的负载数。 存储中的负载减少了，经常访问的实体的整体吞吐量也就提高了。

## <a name="performance-targets"></a>性能目标

规划对生产应用程序使用 Durable Functions 时，必须在规划过程中提前考虑性能要求。 本部分介绍一些基本的使用方案和预期的最大吞吐量数字。

* **顺序活动执行**：此方案描述的业务流程协调程序函数逐个运行一系列活动函数。 它与[函数链接](durable-functions-sequence.md)示例非常类似。
* **并行活动执行**：此方案描述的业务流程协调程序函数使用 [扇出扇入](durable-functions-cloud-backup.md)模式并行执行多个活动函数。
* **并行响应处理**：此方案是 [扇出扇入](durable-functions-cloud-backup.md)模式的后半部分。 它侧重于扇入性能。 必须注意，与扇出不同，扇入是由单个业务流程协调程序函数实例执行的，因此只能在单个 VM 上运行。
* **外部事件处理**：此方案表示一次等待一个 [外部事件](durable-functions-external-events.md)的单个业务流程协调程序函数实例。
* **实体操作处理**：此方案测试单个 [计数器实体](durable-functions-entities.md)处理恒定操作流的速度。

> [!TIP]
> 与扇出不同，扇入操作限制为单个 VM。 如果应用程序使用扇出扇入模式，并且你关注扇入性能，请考虑在多个[子业务流程](durable-functions-sub-orchestrations.md)之间分割活动函数扇出。

### <a name="azure-storage-performance-targets"></a>Azure 存储性能目标

下表显示了使用默认的[适用于 Durable Functions 的 Azure Storage 提供程序](durable-functions-storage-providers.md#azure-storage)时，针对上述场景的预期最大吞吐量。 “实例”是指在 Azure 应用服务中单个小型 ([A1](../../virtual-machines/sizes-previous-gen.md)) VM 上运行的业务流程协调程序函数的单个实例。 在各种情况下，都假设已启用[扩展会话](#orchestrator-function-replay)。 实际结果可能根据函数代码执行的 CPU 或 I/O 工作而异。

| 方案 | 最大吞吐量 |
|-|-|
| 顺序活动执行 | 每个实例每秒 5 个活动 |
| 并行活动执行（扇出） | 每个实例每秒 100 个活动 |
| 并行响应处理（扇入） | 每个实例每秒 150 个响应 |
| 外部事件处理 | 每个实例每秒 50 个事件 |
| 实体操作处理 | 每秒 64 个操作 |

如果发现吞吐量数字不符合预期，但 CPU 和内存用量似乎正常，请检查原因是否与[存储帐户的运行状况](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#troubleshooting-guidance)相关。 Durable Functions 扩展可能会在 Azure 存储帐户中施加大量的负载，负载高到一定程度会导致存储帐户受限制。

> [!TIP]
> 在某些情况下，可通过增加 host.json 中 `controlQueueBufferThreshold` 设置的值来大幅提升外部事件、活动扇入和实体操作的吞吐量。 将此值增大到其默认值后，持久任务框架存储提供程序可使用更多内存来更主动地预提取这些事件，从而减少从 Azure 存储控制队列中取消消息排队时的相关延迟。 有关详细信息，请参阅 [host.json](durable-functions-bindings.md#host-json) 参考文档。

### <a name="high-throughput-processing"></a>高吞吐量处理

Azure 存储后端的体系结构对 Durable Functions 理论上的最大性能和可伸缩性施加了一些限制。 如果测试显示 Azure 存储上的 Durable Functions 不满足吞吐量要求，你应考虑改用[适用于 Durable Functions 的 Netherite 存储提供程序](durable-functions-storage-providers.md#netherite)。

Netherite 存储后端由 [Microsoft Research](https://www.microsoft.com/research) 设计和开发。 它在 [Azure 页 Blob](../../storage/blobs/storage-blob-pageblob-overview.md) 之上使用 [Azure 事件中心](../../event-hubs/event-hubs-about.md)和 [FASTER](https://www.microsoft.com/research/project/faster/) 数据库技术。 与其他提供程序相比，Netherite 的设计支持以更高的吞吐量处理业务流程和实体。 在某些基准方案中，与默认的 Azure 存储提供程序相比，其吞吐量提高了一数量级以上。

要详细了解 Durable Functions 支持的存储提供程序以及它们之间的比较，请参阅 [Durable Functions 存储提供程序](durable-functions-storage-providers.md)文档。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解灾难恢复和异地分发](durable-functions-disaster-recovery-geo-distribution.md)
