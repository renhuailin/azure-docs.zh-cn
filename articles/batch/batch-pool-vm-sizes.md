---
title: 为池选择 VM 大小和映像
description: 如何选择 Azure Batch 池中计算节点的可用 VM 大小和 OS 版本
ms.topic: conceptual
ms.date: 08/10/2021
ms.custom: seodec18
ms.openlocfilehash: 8a923c2982a632209e659ad663147bff5ff5c20a
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121735567"
---
# <a name="choose-a-vm-size-and-image-for-compute-nodes-in-an-azure-batch-pool"></a>选择 Azure Batch 池中计算节点的 VM 大小和映像

为 Azure Batch 池选择节点大小时，可以在 Azure 中提供的几乎所有 VM 大小中进行选择。 Azure 针对不同工作负荷，为 Linux 和 Windows VM 提供一系列大小。

## <a name="supported-vm-series-and-sizes"></a>支持的 VM 系列和大小

### <a name="pools-in-virtual-machine-configuration"></a>虚拟机配置中的池

虚拟机配置中的 Batch 池支持几乎所有 [VM 大小](../virtual-machines/sizes.md)。 请参阅下表以了解有关支持的大小和限制的详细信息。

| VM 系列  | 支持的大小 |
|------------|---------|
| 基本 A | 除 Basic_A0 (A0) 之外的所有大小 |
| A | 除 Standard_A0、Standard_A8、Standard_A9、Standard_A10、Standard_A11 以外的所有大小 |
| Av2 | 所有大小 |
| B | 不支持 |
| DCsv2 | 所有大小 |
| Dv2, DSv2 | 所有大小 |
| Dv3, Dsv3 | 所有大小 |
| Dav4、Dasv4 | 所有大小 |
| Ddv4、Ddsv4 |  所有大小 |
| Dv4、Dsv4 | 不支持 |
| Ev3, Esv3 | 除 E64is_v3 之外的所有大小 |
| Eav4、Easv4 | 所有大小 |
| Edv4, Edsv4 | 所有大小 |
| Ev4、Esv4 | 不支持 |
| F, Fs | 所有大小 |
| Fsv2 | 所有大小 |
| FX<sup>1</sup> | 所有大小 |
| G, Gs | 所有大小 |
| H | 所有大小 |
| HB | 所有大小 |
| HBv2 | 所有大小 |
| HBv3 | 所有大小 |
| HC | 所有大小 |
| Ls | 所有大小 |
| Lsv2 | 所有大小 |
| M | 所有大小 |
| Mv2<sup>1</sup> | 所有大小 |
| NC | 所有大小 |
| NCv2 | 所有大小 |
| NCv3 | 所有大小 |
| NCasT4_v3 | 所有大小 |
| ND | 所有大小 |
| NDv4 | 所有大小 |
| NDv2 | 无（尚不可用） |
| NP | 所有大小 |
| NV | 所有大小 |
| NVv3 | 所有大小 |
| NVv4 | 所有大小 |
| SAP HANA | 不支持 |

<sup>1</sup> 这些 VM 系列只能与第 2 代 VM 映像一起使用。

### <a name="using-generation-2-vm-images"></a>使用第 2 代 VM 映像

一些 VM 系列（如 [Mv2](../virtual-machines/mv2-series.md)）只能与[第 2 代 VM 映像](../virtual-machines/generation-2.md)一起使用。 第 2 代 VM 映像的指定方式与任何 VM 映像的指定方式一样，都是使用[“imageReference”](/rest/api/batchservice/pool/add#imagereference)配置的“sku”属性；“sku”字符串具有后缀，如“-g2”或”-gen2”。 若要获得 Batch 支持的 VM 映像列表（包括第 2 代映像），请使用[“列表支持的映像”](/rest/api/batchservice/account/listsupportedimages)API、[PowerShell](/powershell/module/az.batch/get-azbatchsupportedimage) 或 [Azure CLI](/cli/azure/batch/pool/supported-images)。

### <a name="pools-in-cloud-services-configuration"></a>云服务配置中的池

> [!WARNING]
> 云服务配置池[已被弃用](https://azure.microsoft.com/updates/azure-batch-cloudserviceconfiguration-pools-will-be-retired-on-29-february-2024/)。 请改用虚拟机配置池。

云服务配置中的 Batch 池支持所有[云服务的 VM 大小](../cloud-services/cloud-services-sizes-specs.md)，但以下项除外：

| VM 系列  | 不支持的大小 |
|------------|-------------------|
| A 系列   | 超小       |
| Av2 系列 | Standard_A1_v2、Standard_A2_v2、Standard_A2m_v2 |

## <a name="size-considerations"></a>大小注意事项

- **应用程序要求** - 请考虑要在节点上运行的应用程序的特征和要求。 考虑应用程序是否是多线程的以及其消耗的内存量等因素有助于确定最合适且经济高效的节点大小。 对于多实例 [MPI 工作负荷](batch-mpi.md)或 CUDA 应用程序，请分别考虑使用专用 [HPC](../virtual-machines/sizes-hpc.md) VM 大小或[启用 GPU](../virtual-machines/sizes-gpu.md) 的 VM 大小。 有关详细信息，请参阅[在 Batch 池中使用支持 RDMA 或启用了 GPU 的实例](batch-pool-compute-intensive-sizes.md)。

- **每个节点的任务数** - 通常，选择节点大小时会假设一个任务要在节点上运行一次。 但是，在作业执行期间，让多个任务（因此有多个应用程序实例）在计算节点上[并行运行](batch-parallel-node-tasks.md)可能是很有利的。 在此情况下，往往会选择多核节点大小，以满足更高的并行任务执行需求。

- **不同任务的负载级别** - 池中的所有节点都是相同大小。 如果打算运行具有不同系统要求和/或负载级别的应用程序，建议使用不同的池。

- **区域可用性** - 某个 VM 系列或大小在创建 Batch 帐户的区域中可能无法使用。 若要检查大小是否可用，请参阅[可用产品（按区域）](https://azure.microsoft.com/regions/services/)。

- **配额** - Batch 帐户中的 [核心配额](batch-quota-limit.md#resource-quotas)会限制可添加到 Batch 池的给定大小的节点数。 根据需要[请求增加配额](batch-quota-limit.md#increase-a-quota)。

- **池配置** - 通常，与云服务配置相比，在虚拟机配置中创建池时有更多 VM 大小选项。

## <a name="supported-vm-images"></a>支持的 VM 映像

使用以下 API 之一返回 Batch 当前支持的 Windows 和 Linux VM 映像列表，包括每个映像的节点代理 SKU ID：

- Batch 服务 REST API：[列出支持的映像](/rest/api/batchservice/account/listsupportedimages)
- PowerShell：[Get-AzBatchSupportedImage](/powershell/module/az.batch/get-azbatchsupportedimage)
- Azure CLI：[az batch pool supported-images](/cli/azure/batch/pool/supported-images)

强烈建议避免使用 Batch 支持终止 (EOL) 日期临近的映像。 可通过 [`ListSupportedImages` API](/rest/api/batchservice/account/listsupportedimages)、[PowerShell](/powershell/module/az.batch/get-azbatchsupportedimage) 或 [Azure CLI](/cli/azure/batch/pool/supported-images) 发现这些日期。 有关 Batch 池 VM 映像选择的详细信息，请参阅 [Batch 最佳做法指南](best-practices.md)。

## <a name="next-steps"></a>后续步骤

- 了解 [Batch 服务工作流和主要资源](batch-service-workflow-features.md)，例如池、节点、作业和任务。
- 有关使用计算密集型 VM 大小的信息，请参阅[在 Batch 池中使用支持 RDMA 或启用 GPU 的实例](batch-pool-compute-intensive-sizes.md)。
