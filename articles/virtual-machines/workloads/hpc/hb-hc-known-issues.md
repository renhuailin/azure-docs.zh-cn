---
title: 排查 HPC 和 GPU VM 的已知问题 - Azure 虚拟机 | Microsoft Docs
description: 了解如何排查 Azure 中的 HPC 和 GPU VM 大小的已知问题。
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: article
ms.date: 04/28/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 9b0e33b80cf5bd8963efa036dc76ae9f634fee66
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114460531"
---
# <a name="known-issues-with-h-series-and-n-series-vms"></a>H 系列和 N 系列 VM 的已知问题

本文尝试列出使用 [H 系列](../../sizes-hpc.md)和 [N 系列](../../sizes-gpu.md) HPC 和 GPU VM 时最常见的问题及其解决方案。

## <a name="cache-topology-on-standard_hb120rs_v3"></a>Standard_HB120rs_v3 上的缓存拓扑
`lstopo` 显示 Standard_HB120rs_v3 VM 大小上不正确的缓存拓扑。 它可能显示每个 NUMA 只有 32 MB L3。 但是实际上，每个 NUMA 按照预期确实有 120 MB L3，因为对于整个 VM，与其他受限核心 HBv3 VM 大小一样，可使用 480 MB 的 L3。 这是显示正确值时出现的显示错误，不会影响工作负载。

## <a name="qp0-access-restriction"></a>qp0 访问限制
若要防止可能导致安全漏洞的低级别硬件访问，来宾 VM 将无法访问队列对 0。 这只会影响通常与 ConnectX InfiniBand NIC 的管理相关的操作，以及运行一些 InfiniBand 诊断（如 ibdiagnet），而不会影响最终用户应用程序。

## <a name="mofed-installation-on-ubuntu"></a>Ubuntu 上的 MOFED 安装
在内核版本为 `5.4.0-1039-azure #42` 及更高版本的基于 Ubuntu-18.04 的市场 VM 映像上，某些早期 Mellanox OFED 不兼容，这会导致在某些情况下 VM 启动时间增加长达 30 分钟。 Mellanox OFED 版本 5.2-1.0.4.0 和 5.2-2.2.0.0 均存在此问题。 可通过 Mellanox OFED 5.3-1.0.0.1 解决此问题。
如果需要使用不兼容的 OFED，一种解决方案是使用 Canonical:UbuntuServer:18_04-lts-gen2:18.04.202101290 市场 VM 映像或更低版本，而不是更新内核。

## <a name="mpi-qp-creation-errors"></a>MPI QP 创建错误
如果在运行任何 MPI 工作负载的过程中，引发了如下所示的 InfiniBand QP 创建错误，建议重启 VM 并重试工作负载。 将来会解决此问题。

```bash
ib_mlx5_dv.c:150  UCX  ERROR mlx5dv_devx_obj_create(QP) failed, syndrome 0: Invalid argument
```

当观察到问题时，可以验证最大队列对数的值，如下所示。
```bash
[user@azurehpc-vm ~]$ ibv_devinfo -vv | grep qp
max_qp: 4096
```

## <a name="accelerated-networking-on-hb-hc-hbv2-and-ndv2"></a>HB、HC、HBv2 和 NDv2 上的加速网络

[Azure 加速网络](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/)目前在支持 RDMA 和 InfiniBand 和启用了 SR-IOV 的 VM 大小 [HB](../../hb-series.md)、[HC](../../hc-series.md)、[HBv2](../../hbv2-series.md) 和 [NDv2](../../ndv2-series.md) 上提供。 此功能现在允许在 Azure 以太网上增强吞吐量（最多 30 Gbps）和延迟。 尽管这不同于 InfiniBand 网络上的 RDMA 功能，但在 InfiniBand 上运行作业时，此功能的一些平台更改可能会影响某些 MPI 实现的行为。 特别是某些 VM 上的 InfiniBand 接口的名称可能略有不同（mlx5_1，而不是以前的 mlx5_0），这可能需要调整 MPI 命令行，尤其是在使用 UCX 接口时（通常与 OpenMPI 和 HPC-X 一起使用）。 目前最简单的解决方案是在 CentOS HPC VM 映像上使用最新的 HPC-X，或在不需要加速网络时将其禁用。
有关详细信息，请阅读此 [TechCommunity 文章](https://techcommunity.microsoft.com/t5/azure-compute/accelerated-networking-on-hb-hc-and-hbv2/ba-p/2067965)，其中提供了有关如何解决任何观察到的问题的说明。

## <a name="infiniband-driver-installation-on-non-sr-iov-vms"></a>非 SR-IOV VM 上的 InfiniBand 驱动程序安装

目前 H16r、H16mr 和 NC24r 未启用 SR-IOV。 有关 InfiniBand 堆栈分流的详细信息，请参阅 [Azure VM 大小 - HPC](../../sizes-hpc.md#rdma-capable-instances)。
可在启用了 SR-IOV 的 VM 上通过 OFED 驱动程序配置 InfiniBand，而非 SR-IOV VM 需要 ND 驱动程序。 此 IB 支持在 [CentOS、RHEL 和 Ubuntu](configure.md) 上根据情况提供。

## <a name="duplicate-mac-with-cloud-init-with-ubuntu-on-h-series-and-n-series-vms"></a>在 H 系列和 N 系列 VM 上，Ubuntu 中的 cloud-init 存在重复 MAC

Ubuntu VM 映像上的 cloud-init 在尝试打开 IB 接口时出现已知问题。 在 VM 重新启动或泛化后尝试创建 VM 映像时可能出现此情况。 VM 启动日志可能会显示错误，如下所示：
```console
“Starting Network Service...RuntimeError: duplicate mac found! both 'eth1' and 'ib0' have mac”.
```

这是一个已知问题，即“Ubuntu 上的 cloud-init 存在重复 MAC”。 这将在更新的内核中得到解决。 如果遇到此问题，则解决方法是：
1) 部署 (Ubuntu 18.04) 市场 VM 映像
2) 安装必要的软件包以启用 IB（[说明见此处](https://techcommunity.microsoft.com/t5/azure-compute/configuring-infiniband-for-ubuntu-hpc-and-gpu-vms/ba-p/1221351)）
3) 编辑 waagent.conf 以更改 EnableRDMA=y
4) 禁用云 cloud-init 中的网络
    ```console
    echo network: {config: disabled} | sudo tee /etc/cloud/cloud.cfg.d/99-disable-network-config.cfg
    ```
5) 编辑 netplan 的网络配置文件（由 cloud-init 生成）以删除 MAC
    ```console
    sudo bash -c "cat > /etc/netplan/50-cloud-init.yaml" <<'EOF'
    network:
      ethernets:
        eth0:
          dhcp4: true
      version: 2
    EOF
    ```

## <a name="dram-on-hb-series-vms"></a>HB 系列 VM 上的 DRAM

HB 系列 VM 此时只能向来宾 VM 公开 228 GB 的 RAM。 同样，在 HBv2 上是 458 GB，在 HBv3 虚拟机上是 448 GB。 这是因为 Azure 虚拟机监控程序的已知限制阻止将页面分配给为来宾 VM 保留的 AMD CCX 的本地 DRAM（NUMA 域）。

## <a name="gss-proxy"></a>GSS 代理

GSS 代理在 CentOS/RHEL 7.5 中有一个已知 bug，在与 NFS 一起使用时，会表现为显著的性能和响应性损失。 这可以通过以下方式来缓解：

```console
sed -i 's/GSS_USE_PROXY="yes"/GSS_USE_PROXY="no"/g' /etc/sysconfig/nfs
```

## <a name="cache-cleaning"></a>清理缓存

在 HPC 系统上，在任务完成后，在下一个用户被分配到相同节点之前，清理内存通常很有用。 在 Linux 中运行应用程序后，你可能会发现，尽管没有运行任何应用程序，但可用内存减少了，而缓冲区内存增加了。

![清理前的命令提示符屏幕截图](./media/known-issues/cache-cleaning-1.png)

使用 `numactl -H` 将显示哪些 NUMAnode 的内存进行了缓冲（可能全部）。 在 Linux 中，用户可以通过三种方式清理缓存，以将缓冲或缓存的内存恢复到“可用”。 需要有 root 权限或 sudo 权限。

```console
echo 1 > /proc/sys/vm/drop_caches [frees page-cache]
echo 2 > /proc/sys/vm/drop_caches [frees slab objects e.g. dentries, inodes]
echo 3 > /proc/sys/vm/drop_caches [cleans page-cache and slab objects]
```

![清理后的命令提示符屏幕截图](./media/known-issues/cache-cleaning-2.png)

## <a name="kernel-warnings"></a>内核警告

在 Linux 下启动 HB 系列 VM 时，可能会忽略以下内核警告消息。 这是因为 Azure 虚拟机监控程序的一个已知限制，将在一段时间内解决此问题。

```console
[  0.004000] WARNING: CPU: 4 PID: 0 at arch/x86/kernel/smpboot.c:376 topology_sane.isra.3+0x80/0x90
[  0.004000] sched: CPU #4's llc-sibling CPU #0 is not on the same node! [node: 1 != 0]. Ignoring dependency.
[  0.004000] Modules linked in:
[  0.004000] CPU: 4 PID: 0 Comm: swapper/4 Not tainted 3.10.0-957.el7.x86_64 #1
[  0.004000] Hardware name: Microsoft Corporation Virtual Machine/Virtual Machine, BIOS 090007 05/18/2018
[  0.004000] Call Trace:
[  0.004000] [<ffffffffb8361dc1>] dump_stack+0x19/0x1b
[  0.004000] [<ffffffffb7c97648>] __warn+0xd8/0x100
[  0.004000] [<ffffffffb7c976cf>] warn_slowpath_fmt+0x5f/0x80
[  0.004000] [<ffffffffb7c02b34>] ? calibrate_delay+0x3e4/0x8b0
[  0.004000] [<ffffffffb7c574c0>] topology_sane.isra.3+0x80/0x90
[  0.004000] [<ffffffffb7c57782>] set_cpu_sibling_map+0x172/0x5b0
[  0.004000] [<ffffffffb7c57ce1>] start_secondary+0x121/0x270
[  0.004000] [<ffffffffb7c000d5>] start_cpu+0x5/0x14
[  0.004000] ---[ end trace 73fc0e0825d4ca1f ]---
```


## <a name="next-steps"></a>后续步骤

- 查看 [HB 系列概述](hb-series-overview.md)和 [HC 系列概述](hc-series-overview.md)，以了解如何对工作负载进行优化配置以提高性能和可伸缩性。
- 在 [Azure 计算技术社区博客](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)上阅读最新公告、HPC 工作负载示例和性能结果。
- 若要从体系结构角度更概略性地看待如何运行 HPC 工作负载，请参阅 [Azure 上的高性能计算 (HPC)](/azure/architecture/topics/high-performance-computing/)。
