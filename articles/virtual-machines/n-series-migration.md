---
title: Azure 中 GPU 计算工作负载的迁移指南
description: NC、ND、NCv2 系列迁移指南。
author: iafinder
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
ms.topic: conceptual
ms.date: 08/15/2020
ms.author: iafinder
ms.openlocfilehash: 574118d9bd8c400eccb48ed551d6059c0dfc7bf2
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128551674"
---
# <a name="migration-guide-for-gpu-compute-workloads-in-azure"></a>Azure 中 GPU 计算工作负载的迁移指南

随着市场和 Microsoft Azure 数据中心提供更强大的 GPU，我们建议重新评估工作负载的性能并考虑迁移到更新的 GPU。

出于相同的原因，以及为了维持优质可靠的服务产品，Azure 会定期停用支持较旧 VM 大小的硬件。 Azure 中第一批停用的 GPU 产品为原始的 NC、NC v2 和 ND 系列 VM，它们分别由 NVIDIA Tesla K80、P100 和 P40 数据中心 GPU 加速器提供支持。 这些产品将在 2022 年 8 月 31 日停用，此系列中最旧的 VM 是在 2016 年推出的。

从那时起，GPU 与整个深度学习和 HPC 行业一起取得了令人难以置信的进步，每两代的性能通常相差两倍以上。 自从 NVIDIA K80、P40 和 P100 GPU 推出以来，Azure 已经交付了多款更新一代和类别的 VM 产品，这些产品面向 GPU 加速计算和 AI，基于 NVIDIA 的 T4、V100 和 A100 GPU，通过基于 InfiniBand 的互连结构等可选功能加以区分。 我们鼓励客户在其迁移路径中考虑这些选项。

在大多数情况下，新一代 GPU 带来的性能显著提升都能降低总体 TCO（尽管每 GPU 小时的成本可能不同），因为它们可以减少作业持续时间（支持作业突发），或减少满足固定大小的计算资源需求所需的、支持 GPU 的 VM 总体数量。 除这些优势外，客户还可以通过性能更高的 VM 来缩短得出解决方案的时间，并通过采用更新的软件、CUDA 运行时和驱动程序版本来改善其解决方案的运行状况和可支持性。

## <a name="migration-vs-optimization"></a>迁移与优化

Azure 已认识到，客户会出于多种不同的要求（包括 GPU 体系结构考虑因素、互连、TCO、得出解决方案的时间，以及基于法规要求所在地或延迟要求的区域可用性）而决定选择特定的 GPU VM 产品 - 而其中一些要求甚至会随着时间而改变。

同时，GPU 加速是一个快速发展的新领域。

因此，此产品领域不存在以一应百的指导原则，迁移是重新评估工作负载可能发生的巨大变化的最佳时机 - 例如，从群集部署模型迁移到单个大型 8-GPU VM 或反之、利用精度更低的数据类型、采用多实例 GPU 之类的功能，等等。

假设本已十分强大的每一代 GPU 性能还能通过添加 TensorCore 之类的功能得到数量级的提升，这种考虑因素与特定的工作负载极其相关。

将迁移与应用程序重新架构相结合可以产生巨大的价值，并减少成本和得出解决方案的时间。

但是，此类改进措施超出了本文档的范畴。本文档的目的是重点介绍适用于当今客户可运行的通用工作负载的直接等价类别，以确定每 GPU 成本和性能与即将停用的现有 VM 系列最相似的 VM 选项。

因此，本文档假设用户可能无法洞察或控制特定于工作负载的属性，例如所需的 VM 实例数、GPU 数、互连数，等等。

## <a name="recommended-upgrade-paths"></a>建议的升级路径

### <a name="nc-series-vms-featuring-nvidia-k80-gpus"></a>搭载 NVIDIA K80 GPU 的 NC 系列 VM

[NC (v1) 系列](./nc-series.md) VM 是 Azure 中最老的 GPU 加速计算 VM 类型，由 1 到 4 个与 Intel Xeon E5-2690 v3 (Haswell) 处理器搭配使用的 NVIDIA Tesla K80 数据中心 GPU 加速器提供支持。 它们是曾经用于要求苛刻的 AI、ML 和 HPC 应用程序的旗舰 VM 类型，即使到了产品生命周期的晚期，它们依然是用户的热门选择（特别是可以享受 NC 系列促销价），某些用户看重它们极低的每 GPU 小时绝对成本，因此放弃了每美元吞吐量更高的 GPU。

相比搭载较新 GPU 的 VM 系列，老化的 NVIDIA K80 GPU 平台的计算性能相对较低，当今 NC 系列的一个流行用例是实时推理和分析工作负载，其中的加速 VM 必须处于稳定状态，以便在应用程序发出的请求抵达时为其提供服务。 在这种情况下，请求的数量或批大小可能不足，无法从更高性能的 GPU 中受益。 NC VM 也很受开发人员和学生的欢迎，他们想要了解、体验 GPU 加速或从事相关开发，并需要一个用于迭代的、无需生产级性能表现的经济型基于云的 CUDA 部署目标。

一般而言，NC 系列客户应考虑直接从 NC 大小迁移到 [NC T4 v3](./nct4-v3-series.md) 大小，这是 Azure 的新型 GPU 加速平台，适合由 NVIDIA Tesla T4 GPU 提供支持的轻型工作负载，不过，对于在支持 InfiniBand 的 NC 系列大小上运行的工作负载，应考虑其他 VM SKU。

| 当前 VM 大小 | 目标 VM 大小 | 规格差异 | 
|---|---|---|
Standard_NC6 <br> Standard_NC6_Promo | Standard_NC4as_T4_v3 <br>或<br>Standard_NC8as_T4 | CPU：Intel Haswell，以前为 AMD Rome<br>GPU 数量：1（相同）<br>GPU 代系：NVIDIA Keppler，以前为 Turing（+2 代，FP32 FLOPs 约提升 2 倍）<br>GPU 内存（每个 GPU 的 GiB）：16 (+4)<br>vCPU 数量：4 (-2) 或 8 (+2)<br>内存 GiB：16 (-40) 或 56（相同）<br>临时存储 (SSD) GiB：180 (-160) 或 360 (+20)<br>最大数据磁盘数：8 (-4) 或 16 (+4)<br>加速网络：是 (+)<br>高级存储：是 (+)| 
| Standard_NC24<br>Standard_NC24_Promo | Standard_NC64as_T4_v3* | CPU：Intel Haswell，以前为 AMD Rome<br>GPU 数量：4（相同）<br>GPU 代系：NVIDIA Keppler，以前为 Turing（+2 代，FP32 FLOPs 约提升 2 倍）<br>GPU 内存（每个 GPU 的 GiB）：16 (+4)<br>vCPU 数量：64 (+40)<br>内存 GiB：440 (+216)<br>临时存储 (SSD) GiB：2880 (+1440)<br>最大数据磁盘数：32 (-32)<br>加速网络：是 (+)<br>高级存储：是 (+) | 
|Standard_NC24r<br>Standard_NC24r_Promo<br><br>（支持 InfiniBand 群集的大小） | Standard_NC24rs_v3* | CPU：Intel Haswell，以前为 Intel Broadwell<br>GPU 数量：4（相同）<br>GPU 代系：NVIDIA Keppler，以前为 Volta（+2 代）<br>GPU 内存（每个 GPU 的 GiB）：16 (+4)<br>vCPU 数量：24 (+0)<br>内存 GiB：448 (+224)<br>临时存储 (SSD) GiB：2948 (+1440)<br>最大数据磁盘数：32（相同）<br>加速网络：否（相同）<br>高级存储：是 (+)<br>InfiniBand 互连：是 | 


### <a name="nd-series-vms-featuring-nvidia-tesla-p40-gpus"></a>搭载 NVIDIA Tesla P40 GPU 的 ND 系列 VM

ND 系列虚拟机原本是针对 AI 和深度学习工作负载设计的中端平台。 与其前任产品相比，它们的单精度浮点运算能力已得到改进，可提供卓越的性能来实现批量推理，并由 NVIDIA Tesla P40 GPU 和 Intel Xeon E5-2690 v4 (Broadwell) CPU 提供支持。 与 NC 和 NC v2 系列一样，ND 系列可通过 RDMA 和 InfiniBand 连接提供辅助型低延迟、高吞吐量网络的配置，以便可运行跨多个 GPU 的大规模训练作业。

| 当前 VM 大小 | 目标 VM 大小 | 规格差异 | 
|---|---|---|
|Standard_ND6 | Standard_NC4as_T4_v3<br>或<br>Standard_NC8as_T4 | CPU：Intel Broadwell，以前为 AMD Rome<br>GPU 数量：1（相同）<br>GPU 代系：NVIDIA Pascal，以前为 Turing（+1 代）<br>GPU 内存（每个 GPU 的 GiB）：16 (-8)<br>vCPU 数量：4 (-2) 或 8 (+2)<br>内存 GiB：16 (-40) 或 56 (-56)<br>临时存储 (SSD) GiB：180 (-552) 或 360 (-372)<br>最大数据磁盘数：8 (-4) 或 16 (+4)<br>加速网络：是 (+)<br>高级存储：是 (+) | 
| Standard_ND12 | Standard_NC16as_T4_v3 | CPU：Intel Broadwell，以前为 AMD Rome<br>GPU 数量：1 (-1)<br>GPU 代系：NVIDIA Pascal，以前为 Turing（+1 代）<br>GPU 内存（每个 GPU 的 GiB）：16 (-8)<br>vCPU 数量：16 (+4)<br>内存 GiB：110 (-114)<br>临时存储 (SSD) GiB：360 (-1,114)<br>最大数据磁盘数：48 (+16)<br>加速网络：是 (+)<br>高级存储：是 (+) | 
| Standard_ND24 | Standard_NC64as_T4_v3* | CPU：Intel Broadwell，以前为 AMD Rome<br>GPU 数量：4（相同）<br>GPU 代系：NVIDIA Pascal，以前为 Turing（+1 代）<br>GPU 内存（每个 GPU 的 GiB）：16 (-8)<br>vCPU 数量：64 (+40)<br>内存 GiB：440（相同）<br>临时存储 (SSD) GiB：2880（相同）<br>最大数据磁盘数：32（相同）<br>加速网络：是 (+)<br>高级存储：是 (+) | 
| Standard_ND24r | Standard_NC24rs_v3* | CPU：Intel Broadwell（相同）<br>GPU 数量：4（相同）<br>GPU 代系：NVIDIA Pascal，以前为 Volta（+1 代）<br>GPU 内存（每个 GPU 的 GiB）：16 (-8)<br>vCPU 数量：24 (+0)<br>内存 GiB：448（相同）<br>临时存储 (SSD) GiB：2948（相同）<br>最大数据磁盘数：32（相同）<br>加速网络：否（相同）<br>高级存储：是 (+)<br>InfiniBand 互连：是（相同） | 

### <a name="nc-v2-series-vms-featuring-nvidia-tesla-p100-gpus"></a>搭载 NVIDIA Tesla P100 GPU 的 NC v2 系列 VM

NC v2 系列虚拟机原本是针对 AI 和深度学习工作负载设计的旗舰平台。 它们为深度学习训练提供卓越的性能，每个 GPU 的性能大约为原始 NC 系列的 2 倍，由 NVIDIA Tesla P100 GPU 和 Intel Xeon E5-2690 v4 (Broadwell) CPU 提供支持。 与 NC 和 ND 系列一样，NC v2 系列可通过 RDMA 和 InfiniBand 连接提供辅助型低延迟、高吞吐量网络的配置，以便可运行跨多个 GPU 的大规模训练作业。

| 当前 VM 大小 | 目标 VM 大小 | 规格差异 | 
|---|---|---|
| Standard_NC6s_v2 | Standard_NC6s_v3 | CPU：Intel Broadwell（相同）<br>GPU 数量：1（相同）<br>GPU 代系：NVIDIA Pascal，以前为 Volta（+1 代）<br>GPU 内存（每个 GPU 的 GiB）：16（相同）<br>vCPU 数量：6（相同）<br>内存 GiB：112（相同）<br>临时存储 (SSD) GiB：736（相同）<br>最大数据磁盘数：12（相同）<br>加速网络：否（相同）<br>高级存储：是 (+) | 
| Standard_NC12s_v2 | Standard_NC12s_v3 | CPU：Intel Broadwell（相同）<br>GPU 数量：2（相同）<br>GPU 代系：NVIDIA Pascal，以前为 Volta（+1 代）<br>GPU 内存（每个 GPU 的 GiB）：16（相同）<br>vCPU 数量：12（相同）<br>内存 GiB：112（相同）<br>临时存储 (SSD) GiB：1474（相同）<br>最大数据磁盘数：24（相同）<br>加速网络：否（相同）<br>高级存储：是 (+) | 
| Standard_NC24s_v2 | Standard_NC24s_v3 | CPU：Intel Broadwell（相同）<br>GPU 数量：4（相同）<br>GPU 代系：NVIDIA Pascal，以前为 Volta（+1 代）<br>GPU 内存（每个 GPU 的 GiB）：16（相同）<br>vCPU 数量：24（相同）<br>内存 GiB：448（相同）<br>临时存储 (SSD) GiB：2948（相同）<br>最大数据磁盘数：32（相同）<br>加速网络：否（相同）<br>高级存储：是 (+) | 
| Standard_NC24rs_v2 | Standard_NC24rs_v3* | CPU：Intel Broadwell（相同）<br>GPU 数量：4（相同）<br>GPU 代系：NVIDIA Pascal，以前为 Volta（+1 代）<br>GPU 内存（每个 GPU 的 GiB）：16（相同）<br>vCPU 数量：24（相同）<br>内存 GiB：448（相同）<br>临时存储 (SSD) GiB：2948（相同）<br>最大数据磁盘数：32（相同）<br>加速网络：否（相同）<br>高级存储：是 (+)<br>InfiniBand 互连：是（相同）| 


## <a name="migration-steps"></a>迁移步骤

### <a name="general-changes"></a>常规更改

1.  选择要迁移的系列和大小。 利用[定价计算器](https://azure.microsoft.com/pricing/calculator/)获取更多见解。

2.  获取目标 VM 系列的配额

3.  将当前 N\* 系列 VM 大小调整为目标大小。 还可以借此机会更新虚拟机映像使用的操作系统，或者采用预装了驱动程序的 HPC 映像之一作为起点。

    > [!IMPORTANT]
    > 你的 VM 映像可能是使用不符合新 GPU VM 系列需求的旧版 CUDA 运行时、NVIDIA 驱动程序和（如果适用，仅限支持 RDMA 的大小）Mellanox OFED 驱动程序生成的，可以[按照 Azure 文档中的说明](./sizes-gpu.md)更新该映像。

### <a name="breaking-changes"></a>重大更改

#### <a name="select-target-size-for-migration"></a>选择迁移的目标大小

评估当前使用情况后，确定需要哪种类型的 GPU VM。 根据工作负载要求，可以使用几个不同的选项。

> [!NOTE]
> 最佳做法是根据成本和性能选择 VM 大小。 本指南中的建议基于对性能指标的通用一对一比较，以及另一个 VM 系列中最匹配的大小。 在确定适当的大小之前，请使用 Azure 定价计算器进行成本比较。

> [!IMPORTANT]
> 所有旧式 NC、NC v2 和 ND 系列大小均提供多 GPU 大小，包括 4-GPU 大小，其中为算力需求超过了单个 4-GPU VM 或单个 K80、P40 或 P100 GPU 能力的紧密耦合的横向扩展工作负载提供和不提供 InfiniBand 互连。 尽管上述建议提供了简单直接的正向迁移路径，但这些 VM 大小的用户应考虑使用更强大的基于 NVIDIA V100 GPU 的 VM 系列（例如 [NC v3 系列](./ncv3-series.md)和 [ND v2 系列](./ndv2-series.md)）来实现其性能目标，这些系列通常能够以更低的成本实现相同级别的工作负载性能并提高其易管理性，因为在需要多 GPU 和多节点配置之前，它们分别能够提供高得多的每 GPU 和每 VM 性能。
<br>

#### <a name="get-quota-for-the-target-vm-family"></a>获取目标 VM 系列的配额

按照指南[请求提高 VM 系列的 vCPU 配额](../azure-portal/supportability/per-vm-quota-requests.md)。 选择你所选的要迁移到的目标 VM 大小。

#### <a name="resize-the-current-virtual-machine"></a>调整当前虚拟机的大小

可以[调整虚拟机大小](resize-vm.md)。 

## <a name="next-steps"></a>后续步骤

有关支持 GPU 的虚拟机大小的完整列表，请参阅 [GPU - 加速计算概述](sizes-gpu.md)