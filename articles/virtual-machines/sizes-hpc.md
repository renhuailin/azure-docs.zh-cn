---
title: Azure VM 大小 - HPC | Microsoft Docs
description: 列出 Azure 中适用于高性能计算虚拟机的各种大小。 针对此系列中的大小列出了 vCPU、数据磁盘和 NIC 的数量，以及存储吞吐量和网络带宽。
author: vermagit
ms.service: virtual-machines
ms.subservice: vm-sizes-hpc
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 03/19/2021
ms.author: amverma
ms.reviewer: jushiman
ms.openlocfilehash: 11c3fadd7f95f07bbcbf095e8d6a126022bbea0b
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122697919"
---
# <a name="high-performance-computing-vm-sizes"></a>高性能计算 VM 大小

**适用于：** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 灵活规模集 :heavy_check_mark: 统一规模集

> [!TIP]
> 请尝试使用[虚拟机选择器工具](https://aka.ms/vm-selector)查找最适合你的工作负载的其他尺寸。

Azure H 系列虚拟机 (VM) 旨在为各种实际 HPC 工作负载提供一流的性能、可伸缩性与成本效益。

[HBv3 系列](hbv3-series.md) VM 针对 HPC 应用程序进行了优化，例如流体动力学、显式和隐式有限元素分析、天气建模、地震处理、容器模拟和 RTL 模拟。 HBv3 VM 最多具有 120 个 AMD EPYC™ 7003 系列（米兰）CPU 内核、448 GB RAM，无超线程。 HBv3 系列 VM 还提供 350 GB/秒的内存带宽，每个内核最多可达 32 MB 的 L3 缓存，最高可达 7 GB/秒的块设备 SSD 性能和最高为 3.675 GHz 的时钟频率。 

所有 HBv3 系列 VM 均具有 NVIDIA 网络提供的 200 Gb/秒 HDR InfiniBand，可启用超级计算机规模的 MPI 工作负载。 这些 VM 以非阻塞胖树结构进行连接，以实现优化且一致的 RDMA 性能。 HDR InfiniBand 结构支持自适应路由和动态互联传输（DCT，对标准 RC 和 UD 传输的补充）。 这些功能可增强应用程序性能、可伸缩性和一致性，强烈建议使用。

[HBv2 系列](hbv2-series.md) VM 针对内存带宽驱动的应用（例如流体动力学、有限元分析和储层模拟）进行了优化。 HBv2 VM 搭载 120 个 AMD EPYC 7742 处理器核心，为每个 CPU 核心提供 4 GB RAM，但不支持同步多线程。 每个 HBv2 VM 提供高达 340 GB/秒的内存带宽，以及高达 4 兆次的 FP64 浮点运算能力。

HBv2 VM 搭载 200 Gb/秒的 Mellanox HDR InfiniBand，而 HB 和 HC 系列 VM 搭载的是 100 Gb/秒的 Mellanox EDR InfiniBand。 其中每种 VM 在非阻塞胖树中连接，实现优化且一致的 RDMA 性能。 HBv2 VM 支持自适应路由和动态互联传输 (DCT)（标准 RC 和 UD 传输除外）。 这些功能可增强应用程序性能、可伸缩性和一致性，强烈建议使用。

[HB 系列](hb-series.md) VM 针对内存带宽驱动的应用（例如流体动力学、显式有限元分析和天气建模）进行了优化。 HB VM 搭载 60 个 AMD EPYC 7551 处理器核心，为每个 CPU 核心提供 4 GB RAM，但不支持超线程。 AMD EPYC 平台提供 260 GB/秒以上的内存带宽。

[HC 系列](hc-series.md) VM 针对密集计算驱动的应用（例如隐式有限元分析、分子动力学和计算化学）进行了优化。 HC VM 搭载 44 个 Intel Xeon Platinum 8168 处理器核心，为每个 CPU 核心提供 8 GB RAM，但不支持超线程。 Intel Xeon Platinum 平台支持 Intel 的丰富软件工具生态系统，例如 Intel 数学内核库。

[H 系列](h-series.md) VM 针对由高 CPU 频率驱动的，或者要求为每个核心提供较大内存的应用进行了优化。 H 系列 VM 搭载 8 或 16 个 Intel Xeon E5 2667 v3 处理器核心，为每个 CPU 核心提供 7 GB 或 14 GB RAM，但不支持超线程。 H 系列搭载 56 Gb/秒的 Mellanox FDR InfiniBand，采用无阻塞胖树配置，可实现一致的 RDMA 性能。 H 系列 VM 支持 Intel MPI 5.x 和 MS-MPI。

> [!NOTE]
> 所有 HBv3、HBv2、HB 和 HC 系列 VM 对物理服务器拥有独占访问权限。 每个物理服务器只有 1 个 VM，对于这些 VM 大小，不会与任何其他 VM 进行多租户共享。

> [!NOTE]
> [A8 – A11 VM](./sizes-previous-gen.md#a-series---compute-intensive-instances) 从 2021 年 3 月开始停用。 目前不能部署这些大小的新 VM。 如果你有现有 VM，请参阅电子邮件通知了解后续步骤，其中包括 [HPC 迁移指南](https://azure.microsoft.com/resources/hpc-migration-guide/)中的迁移到其他 VM 大小。

## <a name="rdma-capable-instances"></a>支持 RDMA 的实例

大多数 HPC VM 大小都具有用于建立远程直接内存访问 (RDMA) 连接的网络接口。 使用“r”指定的所选 [N 系列](./nc-series.md)大小也支持 RDMA 功能。 此接口是对其他 VM 大小提供的标准 Azure 以太网接口的补充。

借助此辅助接口，支持 RDMA 的实例可以通过 InfiniBand (IB) 网络进行通信，在 HBv3、HBv2 系列上以 HDR 速率运行，在 HB、HC、NDv2 系列上以 EDR 速率运行，在 H16r、H16mr 和支持 RDMA 的其他 N 系列虚拟机上以 FDR 速率运行。 这些 RDMA 功能可以提高基于消息传递接口 (MPI) 的应用程序的可伸缩性和性能。

> [!NOTE]
> SR-IOV 支持：Azure HPC 中目前有两类 VM，具体取决于 VM 中是否为 InfiniBand 启用了 SR-IOV。 目前，Azure 上几乎所有较新一代的支持 RDMA 或者已启用 InfiniBand 的 VM 都已启用 SR-IOV，但 H16r、H16mr 和 NC24r 除外。
> 只能通过 InfiniBand (IB) 网络启用 RDMA，具有 RDMA 功能的所有 VM 都支持 RDMA。
> 只有已启用 SR-IOV 的 VM 才支持基于 IB 的 IP。
> 无法通过以太网启用 RDMA。

- 操作系统 - 通常使用 CentOS、RHEL、Ubuntu、SUSE 等 Linux 分发版。 所有 HPC 系列 VM 都支持 Windows Server 2016 和更高版本。 未启用 SR-IOV 的 VM 也支持 Windows Server 2012 R2 和 Windows Server 2012。 请注意，[由于 VM 大小包含 64 个以上的（虚拟或物理）核心，HBv2 及更高版本不支持 Windows Server 2012 R2](/windows-server/virtualization/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows)。 有关 Azure 市场上受支持 VM 映像的列表以及如何适当配置这些映像的信息，请参阅 [VM 映像](./workloads/hpc/configure.md)。 相应的 VM 大小页还列出了软件堆栈支持。

- **InfiniBand 和驱动程序** - 在已启用 InfiniBand 的 VM 上，需要安装适当的驱动程序才能启用 RDMA。 有关 Azure 市场上受支持 VM 映像的列表以及如何适当配置这些映像的信息，请参阅 [VM 映像](./workloads/hpc/configure.md)。 另请参阅[启用 InfiniBand](./workloads/hpc/enable-infiniband.md)，了解 VM 扩展或 InfiniBand 驱动程序的手动安装。

- **MPI** - Azure 上已启用 SR-IOV 的 VM 大小允许将几乎任何形式的 MPI 与 Mellanox OFED 配合使用。 在未启用 SR-IOV 的 VM 上，支持的 MPI 实现使用 Microsoft Network Direct (ND) 接口在 VM 之间通信。 因此，仅支持 Intel MPI 5.x 和 Microsoft MPI (MS-MPI) 2012 R2 或更高版本。 更高版本的 Intel MPI 运行时库不一定与 Azure RDMA 驱动程序兼容。 有关在 Azure 中的 HPC VM 上设置 MPI 的更多详细信息，请参阅[设置用于 HPC 的 MPI](./workloads/hpc/setup-mpi.md)。

  > [!NOTE]
  > RDMA 网络地址空间：Azure 中的 RDMA 网络保留地址空间 172.16.0.0/16。 若要在 Azure 虚拟网络中部署的实例上运行 MPI 应用程序，请确保虚拟网络地址空间不与 RDMA 网络重叠。

## <a name="cluster-configuration-options"></a>群集配置选项

Azure 提供了多个选项，用于创建可使用 RDMA 网络通信的 HPC VM 的群集，包括： 

- **虚拟机** - 在同一规模集或可用性集内部署支持 RDMA 的 HPC VM（使用 Azure 资源管理器部署模型时）。 如果使用经典部署模型，请在同一云服务中部署 VM。

- **虚拟机规模集** - 在虚拟机规模集内，确保将部署限制为单个放置组，以便在规模集内部进行 InfiniBand 通信。 例如，在资源管理器模板中，将 `singlePlacementGroup` 属性设置为 `true`。 请注意，可以使用 `singlePlacementGroup=true` 运转的最大比例集大小上限默认为 100 个 VM。 如果你的 HPC 作业规模需求高于在单个租户中包含 100 个 VM，可以免费[建立在线客户支持请求](../azure-portal/supportability/how-to-create-azure-support-request.md)来请求提高限制。 在单个规模集内包含的 VM 数限制可提高到 300 个。 请注意，使用可用性集部署 VM 时，最大限制为每个可用性集 200 个 VM。

  > [!NOTE]
  > 虚拟机之间的 MPI：如果需要在虚拟机 (VM) 之间使用 RDMA（例如使用 MPI 通信），请确保 VM 位于同一虚拟机规模集或可用性集内。

- Azure CycleCloud - 使用 [Azure CycleCloud](/azure/cyclecloud/) 创建一个 HPC 群集用于运行 MPI 作业。

- **Azure Batch** - 创建一个 [Azure Batch](../batch/index.yml) 池用于运行 MPI 工作负载。 若要在 Azure Batch 中运行 MPI 应用程序时使用计算密集型实例，请参阅[在 Azure Batch 中使用多实例任务来运行消息传递接口 (MPI) 应用程序](../batch/batch-mpi.md)。

- **Microsoft HPC Pack**  -  [HPC Pack](/powershell/high-performance-computing/overview) 包含用于 MS-MPI 的、在支持 RDMA 的 Linux VM 上部署时使用 Azure RDMA 网络的运行时环境。 有关示例部署，请参阅[使用 HPC Pack 设置一个用于运行 MPI 应用程序的 Linux RDMA 群集](/powershell/high-performance-computing/hpcpack-linux-openfoam)。

## <a name="deployment-considerations"></a>部署注意事项

- **Azure 订阅** - 若要部署不止一些计算密集型实例，请考虑使用即用即付订阅或其他购买选项。 如果使用的是 [Azure 免费帐户](https://azure.microsoft.com/free/)，则仅可以使用有限数量的 Azure 计算核心。

- 定价和可用性 - 按 Azure 区域查看 [VM 定价](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)和[可用性](https://azure.microsoft.com/global-infrastructure/services/)。

- **核心配额** - 可能需要在 Azure 订阅中在默认值的基础上增加核心配额。 订阅可能也会限制可在特定 VM 大小系列（包括 H 系列）中部署的核心数目。 若要请求增加配额，可免费 [建立联机客户支持请求](../azure-portal/supportability/how-to-create-azure-support-request.md) 。 （默认限制可能会因订阅类别而异。）

  > [!NOTE]
  > 如果有大规模容量需求，请联系 Azure 支持。 Azure 配额为信用额度，而不是容量保障。 不管配额是什么，都只根据所用的核心数计费。
  
- **虚拟网络** – Azure [虚拟网络](https://azure.microsoft.com/documentation/services/virtual-network/)不需要使用计算密集型实例。 但是，对于许多部署来说，如果需要访问本地资源，则可能至少需要一个基于云的 Azure 虚拟网络或站点到站点连接。 需要时，请创建一个新的虚拟网络来部署实例。 不支持将计算密集型 VM 添加到地缘组中的虚拟网络。

- **大小调整** - 可以只对同一大小系列（H 系列或 N 系列）内的计算密集型实例进行大小调整，因为它们使用专用硬件。 例如，可仅将 H 系列 VM 的大小从一个 H 系列大小调整为另一个。 对于某些 VM，可能需要考虑到 InfiniBand 驱动程序支持和 NVMe 磁盘方面的其他事项。


## <a name="other-sizes"></a>其他大小

- [常规用途](sizes-general.md)
- [计算优化](sizes-compute.md)
- [内存优化](sizes-memory.md)
- [存储优化](sizes-storage.md)
- [GPU 优化](sizes-gpu.md)
- [前几代](sizes-previous-gen.md)

## <a name="next-steps"></a>后续步骤

- 在 [HPC 工作负载](./workloads/hpc/overview.md)中详细了解如何[配置 VM](./workloads/hpc/configure.md)、[启用 InfiniBand](./workloads/hpc/enable-infiniband.md)、[设置 MPI](./workloads/hpc/setup-mpi.md) 以及如何为 Azure 优化 HPC 应用程序。
- 查看 [HBv3 系列概述](./workloads/hpc/hbv3-series-overview.md)和 [HC 系列概述](./workloads/hpc/hc-series-overview.md)。
- 在 [Azure 计算技术社区博客](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)上阅读最新公告、HPC 工作负载示例和性能结果。
- 若要从体系结构角度更概略性地看待如何运行 HPC 工作负荷，请参阅 [Azure 上的高性能计算 (HPC)](/azure/architecture/topics/high-performance-computing/)。
