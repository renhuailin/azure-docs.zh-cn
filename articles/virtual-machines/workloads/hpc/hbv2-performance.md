---
title: HBv2 系列 VM 大小性能
description: 了解 Azure 中 HBv2 系列 VM 大小的性能测试结果。
services: virtual-machines
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.workload: infrastructure-services
ms.topic: article
ms.date: 09/28/2020
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 28e7066c166a4c61c2f6436e9bd126f712f89f78
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101670065"
---
# <a name="hbv2-series-virtual-machine-sizes"></a>HBv2 系列虚拟机大小

已对 [HBv2 系列的](../../hbv2-series.md) vm 运行多个性能测试。 下面是此性能测试的部分结果。


| 工作负荷                                        | HBv2                                                              |
|-------------------------------------------------|-------------------------------------------------------------------|
| STREAM Triad                                    | 每 CCX 350 GB/秒 (21-23 GB/秒)                                      |
| High-Performance Linpack (HPL.DAT)                   | 4兆次 (Rpeak，FP64) ，8兆次 (Rmax，FP32)                |
| RDMA 延迟 & 带宽                        | 1.2 微秒，190 Gb/秒                                        |
| 本地 NVMe SSD 上的 FIO                           | 2.7 GB/s 读取，1.1 GB/秒写入;102k IOPS 读取，115 IOPS 写入 |
| IOR on 8 * Azure 高级 SSD (P40 托管磁盘，RAID0) * *  | 1.3 GB/s 读取，2.5 GB/写入;101k IOPS 读取，105k IOPS 写入 |


## <a name="mpi-latency"></a>MPI 延迟

运行 OSU microbenchmark suite 的 MPI 延迟测试。 示例脚本位于 [GitHub](https://github.com/Azure/azhpc-images/blob/04ddb645314a6b2b02e9edb1ea52f079241f1297/tests/run-tests.sh)上。


```bash 
./bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=[INSERT CORE #] ./osu_latency
``` 
 
:::image type="content" source="./media/latency-hbv2.png" alt-text="Azure HB-ACCT-WC 上的 MPI 延迟。":::


## <a name="mpi-bandwidth"></a>MPI 带宽

运行 OSU microbenchmark suite 的 MPI 带宽测试。 示例脚本位于 [GitHub](https://github.com/Azure/azhpc-images/blob/04ddb645314a6b2b02e9edb1ea52f079241f1297/tests/run-tests.sh)上。


```bash
./mvapich2-2.3.install/bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=[INSERT CORE #] ./mvapich2-2.3/osu_benchmarks/mpi/pt2pt/osu_bw
``` 

:::image type="content" source="./media/bandwidth-hbv2.png" alt-text="Azure HB-ACCT-WC 上的 MPI 带宽。":::


## <a name="mellanox-perftest"></a>Mellanox Perftest

[Mellanox Perftest 包](https://community.mellanox.com/s/article/perftest-package)具有许多不受影响的测试，如延迟 (ib_send_lat) 和带宽 (ib_send_bw) 。 下面是一个示例命令。 


```console
numactl --physcpubind=[INSERT CORE #]  ib_send_lat -a
```


## <a name="next-steps"></a>后续步骤

- 阅读有关最新的公告和一些高性能计算 (HPC) 示例和 [Azure 计算技术社区博客](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)中的结果。
- 有关运行 HPC 工作负荷的更高级结构视图，请参阅 [Azure 上的高性能计算 (HPC) ](/azure/architecture/topics/high-performance-computing/)。