---
title: HBv3 系列 VM 概述、体系结构、拓扑 - Azure 虚拟机 |Microsoft Docs
description: 了解 Azure 中的 HBv3 系列 VM 大小。
services: virtual-machines
author: vermagit
tags: azure-resource-manager
ms.service: virtual-machines
ms.subservice: workloads
ms.workload: infrastructure-services
ms.topic: article
ms.date: 03/25/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 7e9cdb80f1423076fb222e1d1521117e65bca9bc
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114460565"
---
# <a name="hbv3-series-virtual-machine-overview"></a>HBv3 系列虚拟机概述 

[HBv3 系列](../../hbv3-series.md)服务器具有 2 个 64 核 EPYC 7V13 CPU，总共 128 个物理“Zen3”内核。 HBv3 上禁用了同时多线程处理技术 (SMT)。 这 128 个内核分为 16 个部分（每个套接字 8 个），每个部分包含 8 个处理器内核，可对 32 MB L3 缓存进行统一访问。 Azure HBv3 服务器还运行以下 AMD BIOS 设置：

```bash
Nodes per Socket (NPS) = 2
L3 as NUMA = Disabled
NUMA domains within VM OS = 4
C-states = Enabled
```

因此，该服务器使用 4 个 NUMA 域（每个套接字 2 个）引导，每个域的大小为 32 核。 每个 NUMA 都可以直接访问以 3200 MT/秒的速度运行的 4 个物理 DRAM 通道。

为了给 Azure 虚拟机监控程序提供运行空间而又不干扰 VM，我们为每台服务器预留 8 个物理内核。

## <a name="vm-topology"></a>VM 拓扑

下图显示了服务器的拓扑。 我们以对称方式在两个 CPU 插槽上保留这 8 个虚拟机监控程序主机核心（黄色），前 2 个核心来自每个 NUMA 域上特定的 Core Complex Dies (CCD)，其余核心用于 HBv3 系列 VM（绿色）。

![HBv3 系列服务器的拓扑](./media/architecture/hbv3/hbv3-topology-server.png)

请注意，CCD 边界不等效于 NUMA 边界。 在 HBv3 上，一组四个连续的 (4) CCD 配置为一个 NUMA 域，无论是在主机服务器级别上还是在来宾 VM 中。 因此，所有大小的 HBv3 VM 都公开 4 个 NUMA 域，这些域将按如下所示对 OS 和应用程序显示，4 个统一 NUMA 域，每个域具有不同的核心数，具体取决于特定的 [HBV3 VM 大小](../../hbv3-series.md)。

![HBv3 系列 VM 的拓扑](./media/architecture/hbv3/hbv3-topology-vm.png)

每种大小的 HBv3 VM 与 AMD EPYC 7003 系列中其他 CPU 的物理布局、功能和性能相似，如下所示：

| HBv3 系列 VM 大小             | NUMA 域 | 每个 NUMA 域的核心  | 与 AMD EPYC 相似         |
|---------------------------------|--------------|------------------------|----------------------------------|
Standard_HB120rs_v3               | 4            | 30                     | 双插槽 EPYC 7713            |
Standard_HB120r-96s_v3            | 4            | 24                     | 双插槽 EPYC 7643            |
Standard_HB120r-64s_v3            | 4            | 16                     | 双插槽 EPYC 7543            |
Standard_HB120r-32s_v3            | 4            | 8                      | 双插槽 EPYC 7313            |
Standard_HB120r-16s_v3            | 4            | 4                      | 双插槽 EPYC 72F3            |

> [!NOTE]
> 受约束的核心 VM 大小仅减少向 VM 公开的物理核心数。 所有全局共享资产（RAM、内存带宽、L3 缓存、GMI 和 xGMI 连接、InfiniBand、Azure 以太网、本地 SSD）保持不变。 这允许客户选择最适合给定的一组工作负荷或软件授权需求的 VM 大小。

每种大小的 HBv3 VM 的虚拟 NUMA 映射会映射到基础物理 NUMA 拓扑。 硬件拓扑没有潜在的误导性抽象。 

使用 [lstopo](https://linux.die.net/man/1/lstopo) 输出的各种 [HBV3 VM 大小](../../hbv3-series.md)的确切拓扑如下所示：
```bash
lstopo-no-graphics --no-io --no-legend --of txt
```
<br>
<details>
<summary>单击以查看 Standard_HB120rs_v3 的 lstopo 输出</summary>

![HBv3-120 VM 的 lstopo 输出](./media/architecture/hbv3/hbv3-120-lstopo.png)
</details>

<details>
<summary>单击以查看 Standard_HB120rs 96_v3 的 lstopo 输出</summary>

![HBv3-96 VM 的 lstopo 输出](./media/architecture/hbv3/hbv3-96-lstopo.png)
</details>

<details>
<summary>单击以查看 Standard_HB120rs 64_v3 的 lstopo 输出</summary>

![HBv3-64 VM 的 lstopo 输出](./media/architecture/hbv3/hbv3-64-lstopo.png)
</details>

<details>
<summary>单击以查看 Standard_HB120rs 32_v3 的 lstopo 输出</summary>

![HBv3-32 VM 的 lstopo 输出](./media/architecture/hbv3/hbv3-32-lstopo.png)
</details>

<details>
<summary>单击以查看 Standard_HB120rs 16_v3 的 lstopo 输出</summary>

![HBv3-16 VM 的 lstopo 输出](./media/architecture/hbv3/hbv3-16-lstopo.png)
</details>

## <a name="infiniband-networking"></a>InfiniBand 网络
HBv3 VM 还具有 Nvidia Mellanox HDR InfiniBand 网络适配器 (ConnectX-6)，其运行速度高达 200 Gb/秒。NIC 通过 SRIOV 传递到 VM，从而使网络流量可以绕过虚拟机监控程序。 因此，客户会在 HBv3 VM 上加载标准的 Mellanox OFED 驱动程序，因为它们会成为裸机环境。

HBv3 VM 支持自适应路由、动态连接的传输（DCT，还有标准 RC 和 UD 传输），以及基于硬件的 MPI 集合到 ConnectX-6 适配器的机载处理器的卸载。 这些功能增强了应用性能、可伸缩性和一致性，我们强烈建议使用。

## <a name="temporary-storage"></a>临时存储
HBv3 VM 具有 3 台物理本地 SSD 设备。 一台设备已预先格式化以用作页面文件，并将在你的 VM 中显示为通用的“SSD”设备。

另外两台较大的 SSD 通过 NVMeDirect 作为未格式化的块 NVMe 设备提供。 由于块 NVMe 设备会绕过虚拟机监控程序，因此它将具有更高的带宽、更高的 IOPS 和更低的每 IOP 延迟。

当在条带阵列中配对时，NVMe SSD 可提供高达 7 GB/秒的读取速度和 3 GB/秒的写入速度，以及高达 186,000 IOPS（读取）和 201,000 IOPS（写入）的深度队列深度。

## <a name="hardware-specifications"></a>硬件规格 

| 硬件规格          | HBv3 系列 VM              |
|----------------------------------|----------------------------------|
| 核心数                            | 120、96、64、32 或 16（已禁用 SMT）               | 
| CPU                              | AMD EPYC 7V13                   | 
| CPU 频率（非 AVX）          | 3.1 GHz（所有内核）、3.675 GHz（多达 10 个内核）    | 
| 内存                           | 448 GB（每个内核的 RAM 依赖于 VM 大小）         | 
| 本地磁盘                       | 2 个 960 GB NVMe（块）、480 GB SSD（页面文件） | 
| Infiniband                       | 200 Gb/秒 Mellanox ConnectX-6 HDR InfiniBand | 
| 网络                          | 50 Gb/秒以太网（40 Gb/秒可用）Azure 第二代 SmartNIC | 

## <a name="software-specifications"></a>硬件规格 

| 硬件规格        | HBv3 系列 VM                                            | 
|--------------------------------|-----------------------------------------------------------|
| 最大 MPI 作业大小               | 36,000 个内核（单个虚拟机规模中的 300 个虚拟机，且 singlePlacementGroup = true） |
| MPI 支持                    | HPC-X、Intel MPI、OpenMPI、MVAPICH2、MPICH  |
| 其他框架          | UCX、libfabric、PGAS                  |
| Azure 存储支持          | 标准磁盘和高级磁盘（最多 32 个磁盘）              |
| SRIOV RDMA 的操作系统支持      | CentOS/RHEL 7.6+, Ubuntu 18.04+, SLES 12 SP4+, WinServer 2016+           |
| 推荐的性能操作系统 | CentOS 8.1、Windows Server 2019+
| Orchestrator 支持           | Azure CycleCloud、Azure Batch、AKS；[群集配置选项](../../sizes-hpc.md#cluster-configuration-options)                      | 

> [!NOTE] 
> 在 HBv3 和大于 64（虚拟或物理）核的其他 VM 上，不支持 Windows Server 2012 R2。 有关详细信息，请参阅 [Windows Server 上 Hyper-V 支持的 Windows 来宾操作系统](/windows-server/virtualization/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows)。

## <a name="next-steps"></a>后续步骤

- 在 [Azure 计算技术社区博客](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)上阅读最新公告、HPC 工作负载示例和性能结果。
- 若要从体系结构角度更概略性地看待如何运行 HPC 工作负荷，请参阅 [Azure 上的高性能计算 (HPC)](/azure/architecture/topics/high-performance-computing/)。
