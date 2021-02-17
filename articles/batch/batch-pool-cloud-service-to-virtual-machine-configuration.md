---
title: 将 Batch 池配置从云服务迁移到虚拟机
description: 了解如何将池配置更新为最新的建议配置
ms.topic: how-to
ms.date: 2/16/2021
ms.openlocfilehash: 9cbcf3864526bd8f8132f3b0f729e2d728e07bb8
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/16/2021
ms.locfileid: "100546034"
---
# <a name="migrate-batch-pool-configuration-from-cloud-services-to-virtual-machines"></a>将 Batch 池配置从云服务迁移到虚拟机

可以使用 [cloudServiceConfiguration](/rest/api/batchservice/pool/add#cloudserviceconfiguration) 或 [virtualMachineConfiguration](/rest/api/batchservice/pool/add#virtualmachineconfiguration) 创建 Batch 池。 “virtualMachineConfiguration”是建议的配置，因为它支持所有 Batch 功能。 “cloudServiceConfiguration”池不能支持所有功能，也没有规划任何新功能。

如果你使用“cloudServiceConfiguration”池，则强烈建议你改用“virtualMachineConfiguration”池。 这样，你将能够利用所有 Batch 功能，例如扩展的 [VM 系列选择](batch-pool-vm-sizes.md)、Linux VM、[容器](batch-docker-container-workloads.md)、[Azure 资源管理器虚拟网络](batch-virtual-network.md)和[节点磁盘加密](disk-encryption.md)。

本文介绍了如何迁移到“virtualMachineConfiguration”。

## <a name="new-pools-are-required"></a>需要新池

无法将现有活动池从“cloudServiceConfiguration”更新为“virtualMachineConfiguration”，必须创建新池。 所有 Batch API、命令行工具、Azure 门户和 Batch Explorer UI 都支持使用“virtualMachineConfiguration”创建池。

[.NET](tutorial-parallel-dotnet.md) 和 [Python](tutorial-parallel-python.md) 教程提供了使用“virtualMachineConfiguration”创建池的示例。

## <a name="pool-configuration-differences"></a>池配置差异

更新池配置时，应考虑以下事项：

- “cloudServiceConfiguration”池节点始终为 Windows OS，“virtualMachineConfiguration”池可以是 Linux OS 或 Windows OS。
- 与“cloudServiceConfiguration”池相比，“virtualMachineConfiguration”池具有更丰富的一组功能，例如容器支持、数据磁盘和磁盘加密。
- “virtualMachineConfiguration”池节点利用托管 OS 磁盘。 用于每个节点的[托管磁盘类型](../virtual-machines/disks-types.md)取决于为该池选择的 VM 大小。 如果为池指定了一个“s”VM 大小（例如“Standard_D2s_v3”），则会使用高级 SSD。 如果指定了一个“非 s”VM 大小（例如“Standard_D2_v3”），则会使用标准 HDD。

   > [!IMPORTANT]
   > 与虚拟机和虚拟机规模集一样，用于每个节点的 OS 托管磁盘都会产生成本，这增加了 VM 的成本。 “cloudServiceConfiguration”节点没有 OS 磁盘成本，因为 OS 磁盘是在节点本地的 SSD 上创建的。

- 池和节点的启动和删除时间在“cloudServiceConfiguration”池和“cloudServiceConfiguration”池之间可能略有不同。

## <a name="azure-data-factory-custom-activity-pools"></a>Azure 数据工厂自定义活动池

Azure Batch 池可用于运行数据工厂自定义活动。 用于运行自定义活动的任何 "cloudServiceConfiguration" 池都需要删除并创建新的 "virtualMachineConfiguration" 池。

- 删除/重新创建之前应暂停管道，以确保不会中断执行。
- 相同的池 id 可用于避免更改链接的服务配置。
- 创建新池后恢复管道。

有关使用 Azure Batch 运行数据工厂自定义活动的详细信息，请执行以下操作：

- [Azure Batch 链接服务](../data-factory/compute-linked-services.md#azure-batch-linked-service)
- [数据工厂管道中的自定义活动](../data-factory/transform-data-using-dotnet-custom-activity.md)

## <a name="next-steps"></a>后续步骤

- 详细了解[池配置](nodes-and-pools.md#configurations)。
- 详细了解[池最佳做法](best-practices.md#pools)。
- 有关[添加池](/rest/api/batchservice/pool/add)和 [virtualMachineConfiguration](/rest/api/batchservice/pool/add#virtualmachineconfiguration) 的 REST API 参考。