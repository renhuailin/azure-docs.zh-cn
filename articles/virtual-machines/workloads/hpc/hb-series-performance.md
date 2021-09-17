---
title: HB 系列 VM 大小性能
description: 了解 Azure 中 HB 系列 VM 大小的性能测试结果。
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: article
ms.date: 09/09/2020
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 2545c04074d60d72b0c21877dae3393d6e34fa0f
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122691463"
---
# <a name="hb-series-virtual-machine-sizes"></a>HB 系列虚拟机大小

**适用于：** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 灵活规模集 :heavy_check_mark: 统一规模集

已对 HB 系列大小运行多个性能测试。 下面是此性能测试的部分结果。

| 工作负荷                                        | HB                    |
|-------------------------------------------------|-----------------------|
| STREAM Triad                                    | 260 GB/秒（每个 CCX 32-33 GB/秒）  |
| 高性能 Linpack (HPL)                  | 1,000 GigaFLOPS (Rpeak)，860 GigaFLOPS (Rmax) |
| RDMA 延迟和带宽                        | 1.27 微秒，99.1 Gb/秒   |
| 本地 NVMe SSD 上的 FIO                           | 1.7 GB/秒的读取速度，1.0 GB/秒的写入速度      |  
| 4 个 Azure 高级 SSD 上的 IOR（P30 托管磁盘，RAID0）**  | 725 MB/秒的读取速度，780 MB/秒的写入速度   |


## <a name="mpi-latency"></a>MPI 延迟

运行 OSU 微基准测试程序套件中的 MPI 延迟测试。 示例脚本位于 [GitHub](https://github.com/Azure/azhpc-images/blob/04ddb645314a6b2b02e9edb1ea52f079241f1297/tests/run-tests.sh) 上

```bash
./bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=[INSERT CORE #] ./osu_latency 
```

:::image type="content" source="./media/latency-hb.png" alt-text="Azure HB 上的 MPI 延迟。":::

## <a name="mpi-bandwidth"></a>MPI 带宽

运行 OSU 微基准测试程序套件中的 MPI 带宽测试。 示例脚本位于 [GitHub](https://github.com/Azure/azhpc-images/blob/04ddb645314a6b2b02e9edb1ea52f079241f1297/tests/run-tests.sh) 上

```bash
./mvapich2-2.3.install/bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=[INSERT CORE #] ./mvapich2-2.3/osu_benchmarks/mpi/pt2pt/osu_bw
```

:::image type="content" source="./media/bandwidth-hb.png" alt-text="Azure HB 上的 MPI 带宽。":::


## <a name="mellanox-perftest"></a>Mellanox Perftest

[Mellanox Perftest 包](https://community.mellanox.com/s/article/perftest-package)具有许多 InfiniBand 测试，如延迟 (ib_send_lat) 和带宽 (ib_send_bw)。 下面是一个示例命令。

```console
numactl --physcpubind=[INSERT CORE #]  ib_send_lat -a
```

## <a name="next-steps"></a>后续步骤

- 在 [Azure 计算技术社区博客](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)上阅读最新公告、HPC 工作负载示例和性能结果。
- 若要从体系结构角度更概略性地看待如何运行 HPC 工作负载，请参阅 [Azure 上的高性能计算 (HPC)](/azure/architecture/topics/high-performance-computing/)。
