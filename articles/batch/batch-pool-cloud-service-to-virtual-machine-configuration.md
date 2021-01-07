---
title: 将 Batch 池配置从云服务迁移到虚拟机
description: 了解如何将池配置更新为最新的和建议的配置
ms.topic: how-to
ms.date: 1/6/2021
ms.openlocfilehash: b6f4184f7c4f133f74cb3157638b1621dad25fda
ms.sourcegitcommit: 9514d24118135b6f753d8fc312f4b702a2957780
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/07/2021
ms.locfileid: "97969021"
---
# <a name="migrate-batch-pool-configuration-from-cloud-services-to-virtual-machines"></a>将 Batch 池配置从云服务迁移到虚拟机

可以使用 [cloudServiceConfiguration](https://docs.microsoft.com/rest/api/batchservice/pool/add#cloudserviceconfiguration) 或 [virtualMachineConfiguration](https://docs.microsoft.com/rest/api/batchservice/pool/add#virtualmachineconfiguration)创建批处理池。 "virtualMachineConfiguration" 是推荐的配置，因为它支持所有批处理功能。 "cloudServiceConfiguration" 池不支持所有功能，并且未计划任何新功能。

如果使用 "cloudServiceConfiguration" 池，强烈建议移动到使用 "virtualMachineConfiguration" 池。 这使你可以受益于所有批处理功能，如 [VM 系列](batch-pool-vm-sizes.md)、Linux vm、 [容器](batch-docker-container-workloads.md)、 [Azure 资源管理器虚拟网络](batch-virtual-network.md)和 [节点磁盘加密](disk-encryption.md)的扩展选择。

本文介绍如何迁移到 "virtualMachineConfiguration"。

## <a name="new-pools-are-required"></a>需要新池

无法将现有活动池从 "cloudServiceConfiguration" 更新为 "virtualMachineConfiguration"，必须创建新池。 所有 Batch Api、命令行工具、Azure 门户和 Batch Explorer UI 都支持使用 "virtualMachineConfiguration" 创建池。

[.Net](tutorial-parallel-dotnet.md)和[Python](tutorial-parallel-python.md)教程提供了使用 "virtualMachineConfiguration" 创建池的示例。

## <a name="pool-configuration-differences"></a>池配置差异

更新池配置时，应考虑以下事项：

- "cloudServiceConfiguration" 池节点始终为 Windows OS，"virtualMachineConfiguration" 池可以是 Linux 或 Windows 操作系统。
- 与 "cloudServiceConfiguration" 池相比，"virtualMachineConfiguration" 池具有更丰富的功能，例如容器支持、数据磁盘和磁盘加密。
- "virtualMachineConfiguration" 池节点利用托管 OS 磁盘。 用于每个节点的 [托管磁盘类型](../virtual-machines/disks-types.md) 取决于为该池选择的 VM 大小。 如果为池指定了 "VM 大小" （例如 "Standard_D2s_v3"），则使用高级 SSD。 如果指定了 "非 s" VM 大小，例如 "Standard_D2_v3"，则使用标准 HDD。

   > [!IMPORTANT]
   > 与虚拟机和虚拟机规模集一样，用于每个节点的 OS 托管磁盘会产生成本，这增加了 Vm 的成本。 在本地 SSD 节点上创建 OS 磁盘时，"cloudServiceConfiguration" 节点没有操作系统磁盘开销。

- 池和节点的启动和删除时间可能在 "cloudServiceConfiguration" 池和 "virtualMachineConfiguration" 池之间略有不同。

## <a name="next-steps"></a>后续步骤

- 了解有关 [池配置](nodes-and-pools.md#configurations)的详细信息。
- 了解有关 [池最佳实践](best-practices.md#pools)的详细信息。
- REST API 的 [池添加](https://docs.microsoft.com/rest/api/batchservice/pool/add) 和 [virtualMachineConfiguration](https://docs.microsoft.com/rest/api/batchservice/pool/add#virtualmachineconfiguration)参考。
