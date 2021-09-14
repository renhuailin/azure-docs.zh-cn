---
title: 将临时 OS 磁盘节点用于 Azure Batch 池
description: 了解如何以及为何创建使用临时 OS 磁盘节点的 Batch 池。
ms.topic: how-to
ms.date: 09/03/2021
ms.openlocfilehash: 760e2848917c6b1c502ac6ba9aae00d5ddd89c8c
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/07/2021
ms.locfileid: "123544373"
---
# <a name="use-ephemeral-os-disk-nodes-for-azure-batch-pools"></a>将临时 OS 磁盘节点用于 Azure Batch 池

某些 Azure 虚拟机 (VM) 系列支持使用[临时 OS 磁盘](../virtual-machines/ephemeral-os-disks.md)，这些磁盘会在节点虚拟机本地存储上创建 OS 磁盘。 默认的 Batch 池配置对节点 OS 磁盘使用 [Azure 托管磁盘](../virtual-machines/managed-disks-overview.md)，其中托管磁盘类似于物理磁盘，但已经过虚拟化并保留在远程 Azure 存储中。

对于 Batch 工作负载，使用临时 OS 磁盘的主要好处是降低了与池相关的成本、使得缩短节点启动时间成为可能，而且由于 OS 磁盘性能更好而提高了应用程序的性能。 在选择是否应对工作负载使用临时 OS 磁盘时，请考虑以下几点：

- 到临时 OS 磁盘的读/写延迟更低，这可能会提高应用程序性能。
- 临时 OS 磁盘没有存储费用，而每个托管的 OS 磁盘都会产生费用。
- 在 Batch 支持的情况下，与托管磁盘相比，使用临时磁盘时能更快地重置节点映像。
- 使用临时 OS 磁盘时，节点启动时间可能略微缩短。
- 临时 OS 磁盘的耐用性和可用性不高；当 VM 因任何原因被删除时，OS 磁盘会丢失。 由于 Batch 工作负载本质上是无状态的，并且通常不依赖于对持久化 OS 磁盘的更改，因此临时 OS 磁盘适用于大多数 Batch 工作负载。
- 目前，并非所有 Azure VM 系列都支持使用临时 OS 磁盘。 如果 VM 大小不支持临时 OS 磁盘，则必须使用托管 OS 磁盘。

> [!NOTE]
> 临时 OS 磁盘配置仅适用于“virtualMachineConfiguration”池，不可用于“cloudServiceConfiguration”池。 建议为 Batch 池使用“virtualMachineConfiguration”，因为“cloudServiceConfiguration”池不支持某些功能，而且没有计划任何新功能。 [2024 年 2 月 29 日之后](https://azure.microsoft.com/updates/azure-batch-cloudserviceconfiguration-pools-will-be-retired-on-29-february-2024/)，无法创建新的“cloudServiceConfiguration”池，也无法向现有池添加新节点。 有关详细信息，请参阅[将 Batch 池配置从云服务迁移到虚拟机](batch-pool-cloud-service-to-virtual-machine-configuration.md)。

## <a name="vm-series-support"></a>VM 系列支持

若要确定 VM 系列是否支持临时 OS 磁盘，请查看每个 VM 实例的相关文档。 例如，[Ddv4 和 Ddsv4 系列](../virtual-machines/ddv4-ddsv4-series.md)支持临时 OS 磁盘。

或者，可通过编程方式查询以检查“EphemeralOSDiskSupported”功能。 [临时 OS 磁盘常见问题解答](../virtual-machines/ephemeral-os-disks.md#frequently-asked-questions)中提供了一个示例 PowerShell cmdlet，它可用于查询此功能。

## <a name="create-a-pool-that-uses-ephemeral-os-disks"></a>创建一个使用临时 OS 磁盘的池

默认情况下，不设置 `EphemeralOSDiskSettings` 属性。 必须设置此属性才能在池节点上配置对临时 OS 磁盘的使用。

以下示例显示如何创建一个 Batch 池，其中节点使用临时 OS 磁盘而非托管磁盘。

### <a name="batch-net-api"></a>Batch .NET API

```csharp
VirtualMachineConfiguration virtualMachineConfiguration = new VirtualMachineConfiguration(
        imageReference: imageReference,
        nodeAgentSkuId: nodeAgentSku
        );
virtualMachineConfiguration.OSDisk = new OSDisk();
virtualMachineConfiguration.OSDisk.EphemeralOSDiskSettings = new DiffDiskSettings();
virtualMachineConfiguration.OSDisk.EphemeralOSDiskSettings.Placement = DiffDiskPlacement.CacheDisk;
```

## <a name="next-steps"></a>后续步骤

- 了解 [Batch 服务工作流和主要资源](batch-service-workflow-features.md)，例如池、节点、作业和任务。
- 了解[可能与 Azure Batch 工作负载相关的成本](budget.md)。
