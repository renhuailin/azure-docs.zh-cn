---
title: 缩放 HPC 应用程序 - Azure 虚拟机 | Microsoft Docs
description: 了解如何在 Azure VM 上缩放 HPC 应用程序。
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: article
ms.date: 04/16/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: c6396eab9cb7a2224d660e0e034f8592a879510d
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122688456"
---
# <a name="scaling-hpc-applications"></a>缩放 HPC 应用程序

适用于：:heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 灵活规模集 :heavy_check_mark: 统一规模集

若要优化 Azure 上 HPC 应用程序的纵向扩展和横向扩展性能，需要针对特定工作负载进行性能优化和优化试验。 本节以及特定于 VM 系列的页面为缩放应用程序提供了一般指导。

## <a name="application-setup"></a>应用程序设置
[azurehpc 存储库](https://github.com/Azure/azurehpc)包含许多示例，涉及：
- 以最佳方式设置和运行[应用程序](https://github.com/Azure/azurehpc/tree/master/apps)。
- [文件系统和群集](https://github.com/Azure/azurehpc/tree/master/examples)的配置。
- [教程](https://github.com/Azure/azurehpc/tree/master/tutorials)，介绍如何轻松地从一些常见的应用程序工作流着手。

## <a name="optimally-scaling-mpi"></a>最佳缩放 MPI 

以下建议适用于最佳的应用程序缩放效率、性能和一致性：

- 对于较小规模的作业（即小于 256K 个连接），请使用以下选项：
   ```bash
   UCX_TLS=rc,sm
   ```

- 对于较大规模的作业（即大于 256K 个连接），请使用以下选项：
   ```bash
   UCX_TLS=dc,sm
   ```

- 在上面的示例中，要计算 MPI 作业的连接数，请使用：
   ```bash
   Max Connections = (processes per node) x (number of nodes per job) x (number of nodes per job) 
   ```

## <a name="adaptive-routing"></a>自适应路由
自适应路由 (AR) 允许运行 EDR 和 HDR InfiniBand 的 Azure 虚拟机 (VM) 通过动态选择更优化的网络路径来自动检测和避免网络拥塞。 因此，AR 可在 InfiniBand 网络上提供更小的延迟和更高的带宽，从而提高了性能和缩放效率。 有关更多详细信息，请参阅[技术社区文章](https://techcommunity.microsoft.com/t5/azure-compute/adaptive-routing-on-azure-hpc/ba-p/1205217)。

## <a name="process-pinning"></a>进程固定

- 使用顺序固定方法（与自动平衡方法相反）将进程固定到核心。 
- 通过 Numa/Core/HwThread 进行绑定比默认绑定更好。
- 对于混合并行应用程序 (OpenMP+MPI)，在 HB 和 HBv2 VM 大小上，每个 CCX 使用 4 个线程和 1 个 MPI 排名。
- 对于纯 MPI 应用程序，请在 HB 和 HBv2 VM 大小下试验每个CCX 的 1-4 MPI 排名，以获得最佳性能。
- 每个 CCX 使用的内核数减少，可能有利于对内存带宽极度敏感的某些应用程序。 对于这些应用程序，每个 CCX 使用 3 或 2 个内核可以减少内存带宽争用并获得更高的实际性能或更一致的可伸缩性。 特别是，MPI Allreduce 可从此方法受益。
- 对于更大规模的运行，建议使用 UD 或混合 RC+UD 传输。 许多 MPI 库/运行时库在内部执行此操作（例如 UCX 或 MVAPICH2）。 检查传输配置以进行大规模运行。

## <a name="compiling-applications"></a>编译应用程序
<br>
<details>
<summary>单击以展开</summary>

尽管不是必需的，但是使用适当的优化标志编译应用程序可以在 HB 和 HC 系列 VM 上提供最佳的纵向扩展性能。

### <a name="amd-optimizing-cc-compiler"></a>AMD 优化 C/C++ 编译器

AMD 优化 C/C++ 编译器 (AOCC) 编译器系统提供了高级别的高级优化、多线程和处理器支持，包括全局优化、矢量化、过程间分析、循环转换和代码生成。 AOCC 编译器二进制文件适用于具有 GNUC 库 (glibc) 版本 2.17 及更高版本的 Linux 系统。 编译器套件包括 C/C++ 编译器 (clang)、Fortran 编译器 (FLANG) 和 Fortran front end to Clang (Dragon Egg)。

### <a name="clang"></a>Clang

Clang 是 C、C++ 和 Objective-C 编译器，负责处理预处理、解析、优化、代码生成、组装和链接。 Clang 支持 `-march=znver1` 标志，为 AMD 基于 Zen 的 x86 架构实现最佳代码生成和优化。

### <a name="flang"></a>FLANG

FLANG 编译器是 AOCC 套件最新添加的编译器（于 2018 年 4 月添加），目前处于预发布阶段，供开发人员下载和测试。 基于 Fortran 2008，AMD 扩展了 GitHub 版本的 FLANG (https://github.com/flang-compiler/flang) )。 FLANG 编译器支持所有的 Clang 编译器选项和一些特定于 FLANG 的额外编译器选项。

### <a name="dragonegg"></a>DragonEgg

DragonEgg 是一个 gcc 插件，它将 GCC 的优化器和代码生成器替换为 LLVM 项目中的优化器和代码生成器。 AOCC 随附的 DragonEgg 适用于 gcc-4.8.x，已经针对 x86-32/x86-64 目标进行了测试，并已在各种 Linux 平台上成功使用。

GFortran 是 Fortran 程序的实际前端，负责生成 GCC GIMPLE 中间表示 (IR) 的预处理、解析和语义分析。 DragonEgg 是一个 GNU 插件，插入 GFortran 编译流中。 它实现 GNU 插件 API。 借助插件体系结构，DragonEgg 成为了编译器驱动程序，用于驱动编译的不同阶段。  按照下载和安装说明操作后，可以使用以下工具调用 Dragon Egg： 

```bash
$ gfortran [gFortran flags] 
   -fplugin=/path/AOCC-1.2-Compiler/AOCC-1.2-     
   FortranPlugin/dragonegg.so [plugin optimization flags]     
   -c xyz.f90 $ clang -O3 -lgfortran -o xyz xyz.o $./xyz
```
   
### <a name="pgi-compiler"></a>PGI 编译器
已确认 PGI Community Edition 第 17 版可与 AMD EPYC 一起使用。 PGI 编译的 STREAM 版本提供了平台的全部内存带宽。 较新的 Community Edition 18.10（2018 年 11 月）应同样适用。 下面是通过 Intel Compiler 优化编译器的示例 CLI：

```bash
pgcc $(OPTIMIZATIONS_PGI) $(STACK) -DSTREAM_ARRAY_SIZE=800000000 stream.c -o stream.pgi
```

### <a name="intel-compiler"></a>Intel Compiler
已确认 Intel Compiler 第 18 版可与 AMD EPYC 一起使用。 下面是通过 Intel Compiler 优化编译器的示例 CLI。

```bash
icc -o stream.intel stream.c -DSTATIC -DSTREAM_ARRAY_SIZE=800000000 -mcmodel=large -shared-intel -Ofast –qopenmp
```

### <a name="gcc-compiler"></a>GCC 编译器 
对于 HPC，AMD 推荐 GCC 编译器 7.3 或更高版本。 不建议使用早期版本，如 RHEL/CentOS 7.4 随附的 4.8.5。 GCC 7.3 和更高版本将在 HPL、HPCG 和 DGEMM 测试中提供更高的性能。

```bash
gcc $(OPTIMIZATIONS) $(OMP) $(STACK) $(STREAM_PARAMETERS) stream.c -o stream.gcc
```
</details>

## <a name="next-steps"></a>后续步骤

- 使用[有关优化 Azure 上的 HPC 应用程序的学习模块](/learn/modules/optimize-tightly-coupled-hpc-apps/)测试知识。
- 查看 [HBv3 系列概述](hbv3-series-overview.md)和 [HC 系列概述](hc-series-overview.md)。
- 在 [Azure 计算技术社区博客](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)上阅读最新公告、HPC 工作负载示例和性能结果。
- 详细了解 Azure 上的 [HPC](/azure/architecture/topics/high-performance-computing/)。