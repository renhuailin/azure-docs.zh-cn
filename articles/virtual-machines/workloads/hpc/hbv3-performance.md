---
title: HBv3 系列 VM 大小的性能和可伸缩性
description: 了解 Azure 中 HBv3 系列 VM 大小的性能和可伸缩性。
services: virtual-machines
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.workload: infrastructure-services
ms.topic: article
ms.date: 03/25/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: bb32a407813ba7e85f55fc39790fba81ca452c50
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122693396"
---
# <a name="hbv3-series-virtual-machine-performance"></a>HBv3 系列虚拟机性能

适用于：:heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 灵活规模集 :heavy_check_mark: 统一规模集

使用常见 HPC 微基准测试程序的性能预期如下：

| 工作负荷                                        | HBv3                                                              |
|-------------------------------------------------|-------------------------------------------------------------------|
| STREAM Triad                                    | 330-350 GB/秒（每个 NUMA 约 82-86 GB/秒）                                     |
| 高性能 Linpack (HPL)                  | 用于 120 核心 VM 大小的 4 TF（Rpeak，FP64），8 TF（Rpeak，FP32）               |
| RDMA 延迟和带宽                        | 1.2 微秒（1 字节），192 Gb/秒（单向）                                        |
| 本地 NVMe SSD 上的 FIO (RAID0)                  | 7 GB/秒的读取速度，3 GB/秒的写入速度；186,000 的 IOPS 读取速度，201,000 的 IOPS 写入速度 |

## <a name="process-pinning"></a>进程固定

[进程固定](compiling-scaling-applications.md#process-pinning)适用于 HBv3 系列 VM，因为我们会将底层硅按原样公开给来宾 VM。 强烈建议使用进程固定来实现最佳性能和一致性。

## <a name="mpi-latency"></a>MPI 延迟

可按如下所示执行 OSU 微基准测试程序套件中的 MPI 延迟测试。 示例脚本位于 [GitHub](https://github.com/Azure/azhpc-images/blob/04ddb645314a6b2b02e9edb1ea52f079241f1297/tests/run-tests.sh) 上。

```bash 
./bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=[INSERT CORE #] ./osu_latency
``` 
## <a name="mpi-bandwidth"></a>MPI 带宽
可按如下所示执行 OSU 微基准测试程序套件中的 MPI 带宽测试。 示例脚本位于 [GitHub](https://github.com/Azure/azhpc-images/blob/04ddb645314a6b2b02e9edb1ea52f079241f1297/tests/run-tests.sh) 上。
```bash
./mvapich2-2.3.install/bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=[INSERT CORE #] ./mvapich2-2.3/osu_benchmarks/mpi/pt2pt/osu_bw
```
## <a name="mellanox-perftest"></a>Mellanox Perftest
[Mellanox Perftest 包](https://community.mellanox.com/s/article/perftest-package)具有许多 InfiniBand 测试，如延迟 (ib_send_lat) 和带宽 (ib_send_bw)。 下面是一个示例命令。
```console
numactl --physcpubind=[INSERT CORE #]  ib_send_lat -a
```
## <a name="next-steps"></a>后续步骤
- 了解有关[缩放 MPI 应用程序](compiling-scaling-applications.md)的信息。
- 有关 HBv3 VM 上 HPC 应用程序的性能和可伸缩性结果，请参阅[技术社区文章](https://techcommunity.microsoft.com/t5/azure-compute/hpc-performance-and-scalability-results-with-azure-hbv3-vms/bc-p/2235843)。
- 在 [Azure 计算技术社区博客](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)上阅读最新公告、HPC 工作负载示例和性能结果。
- 若要从体系结构角度更概略性地看待如何运行 HPC 工作负载，请参阅 [Azure 上的高性能计算 (HPC)](/azure/architecture/topics/high-performance-computing/)。
