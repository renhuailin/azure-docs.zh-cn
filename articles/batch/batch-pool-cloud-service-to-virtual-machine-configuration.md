---
title: 将 Batch 池配置从云服务迁移到虚拟机
description: 了解如何将池配置更新为最新的建议配置
ms.topic: how-to
ms.date: 09/03/2021
ms.openlocfilehash: 9d4ce46c5291333f161f260c3b4706ea9dcf9a24
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/07/2021
ms.locfileid: "123538878"
---
# <a name="migrate-batch-pool-configuration-from-cloud-services-to-virtual-machine"></a>将 Batch 池配置从云服务迁移到虚拟机

目前可以使用 [virtualMachineConfiguration](/rest/api/batchservice/pool/add#virtualmachineconfiguration) 或 [cloudServiceConfiguration](/rest/api/batchservice/pool/add#cloudserviceconfiguration) 来创建 Batch 池。 建议只使用 virtualMachineConfiguration，因为此配置支持所有 Batch 功能。

cloudServicesConfiguration 池不支持目前的一些 Batch 功能，并且不会支持任何新添加的功能。 [在 2024 年 2 月 29 日之后](https://azure.microsoft.com/updates/azure-batch-cloudserviceconfiguration-pools-will-be-retired-on-29-february-2024/)，你将无法创建新的“cloudServiceConfiguration”池或将新节点添加到现有池。

如果你的 Batch 解决方案目前使用“cloudServiceConfiguration”池，建议尽早更改为“virtualMachineConfiguration”。 这样，你将能够利用所有 Batch 功能，例如扩展的 [VM 系列选择](batch-pool-vm-sizes.md)、Linux VM、[容器](batch-docker-container-workloads.md)、[Azure 资源管理器虚拟网络](batch-virtual-network.md)和[节点磁盘加密](disk-encryption.md)。

## <a name="create-a-pool-using-virtual-machine-configuration"></a>使用 virtualMachineConfiguration 创建池

你无法将使用“cloudServiceConfiguration”的现有活动池切换为使用“virtualMachineConfiguration”， 而只能创建新池。 在创建了新“virtualMachineConfiguration”池并复制了所有作业和任务后，可以删除不再使用的旧“cloudServiceConfiguration”池。

所有的 Batch API、命令行工具、Azure 门户和 Batch Explorer UI 都可以用来创建使用“virtualMachineConfiguration”的池。

如需演练创建使用“virtualMachineConfiguration”的池的过程，请参阅 [.NET 教程](tutorial-parallel-dotnet.md)或 [Python 教程](tutorial-parallel-python.md)。

## <a name="pool-configuration-differences"></a>池配置差异

这两种配置之间的一些主要差异包括：

- “cloudServiceConfiguration”池节点只使用 Windows OS。 “virtualMachineConfiguration”池可以使用 Linux 或 Windows OS。
- 与“cloudServiceConfiguration”池相比，“virtualMachineConfiguration”池具有更丰富的一组功能，例如容器支持、数据磁盘和磁盘加密。
- 池和节点的启动和删除时间在“cloudServiceConfiguration”池和“cloudServiceConfiguration”池之间可能略有不同。
- “virtualMachineConfiguration”池节点利用托管 OS 磁盘。 用于每个节点的[托管磁盘类型](../virtual-machines/disks-types.md)取决于为该池选择的 VM 大小。 如果为池指定了一个“s”VM 大小（例如“Standard_D2s_v3”），则会使用高级 SSD。 如果指定了一个“非 s”VM 大小（例如“Standard_D2_v3”），则会使用标准 HDD。

   > [!IMPORTANT]
   > 与虚拟机和虚拟机规模集一样，用于每个节点的 OS 托管磁盘都会产生成本，这增加了 VM 的成本。 “virtualMachineConfiguration”池可使用[临时 OS 磁盘](create-pool-ephemeral-os-disk.md)来避免产生与托管磁盘相关的额外成本，临时磁盘会在 VM 缓存或临时 SSD 上创建 OS 磁盘。“cloudServiceConfiguration”节点没有 OS 磁盘成本，因为 OS 磁盘是在节点本地 SSD 上创建的。

## <a name="azure-data-factory-custom-activity-pools"></a>Azure 数据工厂自定义活动池

Azure Batch 池可用于运行数据工厂自定义活动。 需要删除任何用于运行自定义活动的“cloudServiceConfiguration”池，并创建新的“virtualMachineConfiguration”池。

在创建用于运行数据工厂自定义活动的新池时，请遵循以下做法：

- 在创建新池并删除旧池之前，先暂停所有管道，以确保执行不会中断。
- 可以使用相同的池 ID 来避免链接服务配置发生变化。
- 在创建了新池之后，恢复管道。

若要详细了解如何使用 Azure Batch 来运行数据工厂自定义活动，请参阅 [Azure Batch 链接服务](../data-factory/compute-linked-services.md#azure-batch-linked-service)和[数据工厂管道中的自定义活动](../data-factory/transform-data-using-dotnet-custom-activity.md)

## <a name="next-steps"></a>后续步骤

- 详细了解[池配置](nodes-and-pools.md#configurations)。
- 详细了解[池最佳做法](best-practices.md#pools)。
- 请查看有关[添加池](/rest/api/batchservice/pool/add)和 [virtualMachineConfiguration](/rest/api/batchservice/pool/add#virtualmachineconfiguration) 的 REST API 参考。