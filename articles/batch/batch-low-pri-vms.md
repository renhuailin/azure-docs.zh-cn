---
title: 在经济高效的低优先级 VM 上运行工作负载
description: 了解如何预配低优先级 VM，以降低 Azure Batch 工作负载的成本。
author: mscurrell
ms.topic: how-to
ms.date: 03/03/2021
ms.custom: seodec18
ms.openlocfilehash: d6e6277292d12de10ad55c5f7d912f41e92b5b18
ms.sourcegitcommit: aba63ab15a1a10f6456c16cd382952df4fd7c3ff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/25/2021
ms.locfileid: "107989299"
---
# <a name="use-low-priority-vms-with-batch"></a>将低优先级 VM 与 Batch 配合使用

Azure Batch 可提供低优先级虚拟机 (VM) 来降低 Batch 工作负载的成本。 低优先级 VM 提供大量的经济型计算资源，使新型 Batch 工作负载成为可能。

低优先级 VM 利用 Azure 中多余的容量。 在池中指定低优先级 VM 时，Azure Batch 可以自动使用此多余容量（如果可用）。

使用低优先级 VM 的弊端是这些 VM 可能并不总是可供分配，也有可能随时被占用，具体取决于可用的容量。 出于此原因，对于作业完成时间很灵活且工作分布在多个 VM 上的批处理和异步处理工作负载，最适合使用低优先级 VM。

与专用 VM 相比，以显著低廉的价格提供低优先级 VM。 有关价格详细信息，请参阅 [Batch 定价](https://azure.microsoft.com/pricing/details/batch/)。

> [!NOTE]
> [现成 VM](https://azure.microsoft.com/pricing/spot/) 现可用于[单实例 VM](../virtual-machines/spot-vms.md) 和 [VM 规模集](../virtual-machine-scale-sets/use-spot.md)。 现成 VM 是低优先级 VM 的进化版，区别在于定价可能不同，而且在分配现成 VM 时可设置价格上限（可选）。
>
>Azure Batch 池未来将在新版本的 [Batch API 和工具](./batch-apis-tools.md)中开始支持现成 VM。 在对现成 VM 的支持推出后，我们将弃用低优先级 VM。为了让用户有足够的时间迁移到现成 VM，我们将使用现有 API 和工具版本继续支持低优先级 VM 至少 12 个月。
>
> 仅虚拟机配置池支持现成 VM。 若要使用现成 VM，所有云服务配置池都需要[迁移到虚拟机配置池](batch-pool-cloud-service-to-virtual-machine-configuration.md)。

## <a name="batch-support-for-low-priority-vms"></a>低优先级 VM 的 Batch 支持

Azure Batch 提供多种功能来方便你使用低优先级 VM 并从中受益：

- Batch 池可以包含专用 VM 和低优先级 VM。 创建池时可以指定每种类型的 VM 的数量，或者随时使用显式的大小调整操作或自动缩放来更改现有池的 VM 数量。 作业和任务提交内容可保持不变，而无需考虑池中的 VM 类型。 此外，还可以配置某个池完全使用低优先级 VM，以尽量最低的成本运行作业，但在容量降至最低阈值之下时，运转专用 VM 来保持作业的运行。
- Batch 池会自动确定低优先级 VM 的目标数量。 如果 VM 被占用或不可用，Batch 将尝试替换丢失的容量并返回到目标。
- 当任务被中断时，Batch 将检测并自动请求任务以再次运行。
- 低优先级 VM 具有不同于专用 VM 的单独 vCPU 配额。 因为低优先级 VM 成本更低，因此，低优先级 VM 的配额高于专用 VM 的配额。 有关详细信息，请参阅 [Batch 服务的配额和限制](batch-quota-limit.md#resource-quotas)。

> [!NOTE]
> [用户订阅模式](accounts.md)下创建的 Batch 帐户目前不支持低优先级 VM。

## <a name="considerations-and-use-cases"></a>注意事项和用例

许多 Batch 工作负载非常适合低优先级 VM。 当作业被分解为多个并行任务时，或当你有许多作业横向扩展并分布在多个 VM 上时，请考虑使用它们。

适合使用低优先级 VM 的批处理用例示例包括：

- **开发和测试**：具体而言，开发大规模解决方案时可以实现极大的节省。 所有类型的测试都可以受益，但大规模负载测试和回归测试可以获得极大的好处。
- **补充按需容量**：低优先级 VM 可用于补充常规的专用 VM。 如果可用，则作业可以扩展，因而能够以更低的成本、更快的速度完成；如果不可用，则仍可遵循专用 VM 的基准。
- **灵活的作业执行时间**：如果作业必须完成的时间比较灵活，则可以容忍潜在的容量下降；但是，增加的低优先级 VM 作业往往能够以更低的成本、更快的速度运行。

可以通过多种方式将 Batch 池配置为使用低优先级 VM：

- 池可以仅使用低优先级 VM。 在这种情况下，Batch 将在可用时恢复任何占用的容量。 此配置是执行作业最节省的方法。
- 低优先级 VM 可与具有固定基准的专用 VM 结合使用。 专用 VM 的固定数量确保始终能够提供一些容量来保持作业的持续运行。
- 池可以动态混用专用 VM 和低优先级 VM，以便在廉价的低优先级 VM 可用时仅使用此类 VM，而全价的专用 VM 可在需要时进行纵向扩展。 此配置将保留最小可用容量以保持作业的进展。

计划使用低优先级 VM 时，请记住以下事项：

- 为了最大程度地利用 Azure 中的多余容量，可以横向扩展适当的作业。
- VM 有时可能不可用或者被占用，导致作业可用的容量减少，或者导致任务中断，需要重新运行。
- 执行时间较短的任务往往最适合在低优先级 VM 上运行。 对于包含长时间运行的任务的作业，如果任务被中断，这些作业受到的影响可能更大。 如果长时间运行的任务实施检查点来保存其执行进度，可以降低此影响。 
- 利用多个 VM 的长时间运行的 MPI 作业不是很适合使用低优先级 VM，因为一个被占用的 VM 可能会导致需要重新运行整个作业。
- 如果未正确配置[网络安全组或 NSG 规则](batch-virtual-network.md#network-security-groups-specifying-subnet-level-rules)，低优先级节点可能会标记为不可用。

## <a name="create-and-manage-pools-with-low-priority-vms"></a>创建和管理具有低优先级 VM 的池

Batch 池可以包含专用 VM 和低优先级 VM（也称为计算节点）。 可为专用 VM 和低优先级 VM 设置计算节点的目标数量。 节点的目标数量指定要在池中包含的 VM 数量。

例如，若要使用 Azure 虚拟机（在本例中为 Linux VM）创建一个目标为 5 个专用 VM 和 20 个低优先级 VM 的池，请使用以下代码：

```csharp
ImageReference imageRef = new ImageReference(
    publisher: "Canonical",
    offer: "UbuntuServer",
    sku: "16.04-LTS",
    version: "latest");

// Create the pool
VirtualMachineConfiguration virtualMachineConfiguration =
    new VirtualMachineConfiguration("batch.node.ubuntu 16.04", imageRef);

pool = batchClient.PoolOperations.CreatePool(
    poolId: "vmpool",
    targetDedicatedComputeNodes: 5,
    targetLowPriorityComputeNodes: 20,
    virtualMachineSize: "Standard_D2_v2",
    virtualMachineConfiguration: virtualMachineConfiguration);
```

可以获取专用 VM 和低优先级 VM 的当前节点数：

```csharp
int? numDedicated = pool1.CurrentDedicatedComputeNodes;
int? numLowPri = pool1.CurrentLowPriorityComputeNodes;
```

池节点提供一个属性用于指示节点是专用 VM 还是低优先级 VM：

```csharp
bool? isNodeDedicated = poolNode.IsDedicated;
```

VM 有时会被占用。 如果发生这种情况，在被占用的节点 VM 上运行的任务将重新排队并重新运行。

对于虚拟机配置池，Batch 还将执行以下操作：

- 将已取代的 VM 的状态更新为“已取代”。 
- VM 被实际删除，导致 VM 本地存储的所有数据丢失。
- 池上的列表节点操作仍将返回被占用的节点。
- 池将不断地尝试用完低优先级节点的可用目标数量。 如果找到替代容量，节点将保留其 ID 但会被重新初始化，依次经历“正在创建”和“正在启动”状态，然后可供任务计划使用。
- Azure 门户以指标形式提供取代计数。

## <a name="scale-pools-containing-low-priority-vms"></a>缩放包含低优先级 VM 的池

与仅包含专用 VM 的池一样，可以通过调用 Resize 方法或使用自动缩放来缩放包含低优先级 VM 的池。

池调整大小操作采用可更新 **targetLowPriorityNodes** 值的另一个可选参数：

```csharp
pool.Resize(targetDedicatedComputeNodes: 0, targetLowPriorityComputeNodes: 25);
```

池自动缩放公式支持低优先级 VM，如下所示：

- 可以获取或设置服务定义的变量 **$TargetLowPriorityNodes** 的值。
- 可以获取服务定义的变量 **$CurrentLowPriorityNodes** 的值。
- 可以获取服务定义的变量 **$PreemptedNodeCount** 的值。 此变量返回处于已取代状态的节点的数量，并可让你根据不可用的已取代节点数增加或减少专用节点的数量。

## <a name="configure-jobs-and-tasks"></a>配置作业和任务

对于低优先级节点，作业和任务几乎不需要额外配置。 请记住以下几点：

- 作业的 JobManagerTask 属性包含 AllowLowPriorityNode 属性。 如果此属性为 true，则可以在专用或低优先级节点上计划作业管理器任务。 如果为 false，则只会在专用节点上计划作业管理器任务。
- 可对任务应用程序使用一个 AZ_BATCH_NODE_IS_DEDICATED [环境变量](batch-compute-node-environment-variables.md)，使该应用程序能够确定它是在低优先级节点还是专用节点上运行。

## <a name="view-metrics-for-low-priority-vms"></a>查看低优先级 VM 的指标

[Azure 门户](https://portal.azure.com)提供了低优先级节点的新指标。 这些指标是：

- 低优先级节点计数
- 低优先级核心计数
- 已占用节点计数

若要在 Azure 门户中查看这些指标，请执行以下操作

1. 导航到 Azure 门户中的批处理帐户。
2. 从“监视”部分选择“指标” 。
3. 从“指标”列表选择所需指标。

## <a name="next-steps"></a>后续步骤

- 了解 [Batch 服务工作流和主要资源](batch-service-workflow-features.md)，例如池、节点、作业和任务。
- 了解适用于生成批处理解决方案的[批处理 API 和工具](batch-apis-tools.md)。
- 开始规划从低优先级 VM 到现成 VM 的迁移。 如果在“云服务配置”池中使用低优先级 VM（[已弃用](https://azure.microsoft.com/updates/azure-batch-cloudserviceconfiguration-pools-will-be-retired-on-29-february-2024/)），请计划改为迁移到 [**虚拟机配置** 池](nodes-and-pools.md#configurations)。
