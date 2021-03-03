---
title: 磁盘指标
description: 磁盘突发指标示例
author: roygara
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 60486c41ad843cf193ee0648dfcfef66f7668e47
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101674978"
---
# <a name="disk-performance-metrics"></a>磁盘性能指标
Azure 在 Azure 门户中提供了度量值，可让你深入了解虚拟机 (VM) 和磁盘的执行情况。 还可以通过 API 调用来检索度量值。 本文分为3个小节：

- **磁盘 IO、吞吐量和队列深度指标** -这些指标使你可以从磁盘和虚拟机的角度查看存储性能。
- **磁盘突发指标** -这些指标可为高级磁盘上的 [突发](disk-bursting.md) 功能提供可观察性。
- **存储 IO 利用率指标** -这些指标有助于诊断磁盘存储性能的瓶颈。 

所有指标都将每分钟发出一次，这种情况下，每隔5分钟发出一次 "分解信用百分比" 指标。

## <a name="disk-io-throughput-and-queue-depth-metrics"></a>磁盘 IO、吞吐量和队列深度指标
以下指标可用于深入了解 VM 和磁盘 IO、吞吐量和队列深度性能：

- OS 磁盘队列深度：当前未完成的等待在 OS 磁盘中读取或写入的 IO 请求数。
- OS 磁盘读取字节数/秒：每秒从 OS 磁盘中读取的字节数。
- OS 磁盘读取操作次数/秒：每秒从 OS 磁盘读取的输入操作的次数。
- OS 磁盘写入字节数/秒：每秒在 OS 磁盘中写入的字节数。
- OS 磁盘写入操作次数/秒：每秒在 OS 磁盘中写入的输出操作的次数。
- 数据磁盘队列深度：当前未完成的等待在数据磁盘中读取或写入的 IO 请求数。
- 数据磁盘读取字节数/秒：每秒从数据磁盘中读取的字节数。
- 数据磁盘读取操作次数/秒：每秒从数据磁盘读取的输入操作的次数。
- 数据磁盘写入字节数/秒：每秒在数据磁盘中写入的字节数。
- 数据磁盘写入操作次数/秒：每秒在数据磁盘中写入的输出操作的次数。
- 磁盘读取字节数/秒：每秒从附加到某个 VM 的所有磁盘中读取的总字节数。
- 磁盘读取操作次数/秒：每秒从附加到某个 VM 的所有磁盘中读取的输入操作的次数。
- 磁盘写入字节数/秒：每秒在附加到某个 VM 的所有磁盘中写入的字节数。
- 磁盘写入操作次数/秒：每秒在附加到某个 VM 的所有磁盘中写入的输出操作的次数。

## <a name="bursting-metrics"></a>突发指标
以下指标有助于在高级磁盘上可观察性到 [突发](disk-bursting.md) 功能：

- **数据磁盘最大突发带宽**：数据磁盘)  (的吞吐量限制。
- **Os 磁盘最大突发带宽**：操作系统磁盘可以突发到的吞吐量限制。
- **数据磁盘最大突发 iops**：数据磁盘)  (的 iops 限制。
- **Os 磁盘最大突发 IOPS**：操作系统磁盘可以突发到的 iops 限制。
- **数据磁盘目标带宽**：数据 () 磁盘的吞吐量限制无需突发即可实现。
- **Os 磁盘目标带宽**：操作系统磁盘无需突发即可实现的吞吐量限制。
- **数据磁盘目标 IOPS**：数据磁盘)  (的 iops 限制，无需突发即可实现。
- **OS 磁盘目标 IOPS**：数据磁盘)  (的 iops 限制，无需突发即可实现。
- **数据磁盘已使用突发 BPS （BPS）百分比**：数据磁盘) 所用的吞吐量突发的累计百分比 (。 在5分钟的时间间隔内发出。
- **Os 磁盘使用的突发 BPS 百分比**：操作系统磁盘使用的吞吐量突发的累计百分比。 在5分钟的时间间隔内发出。
- **数据磁盘使用的突发 IO 富余百分比**：数据磁盘) 所用的 IOPS 突发百分比，以 (。 在5分钟的时间间隔内发出。
- **Os 磁盘已使用突发 IO 富余百分比**： os 磁盘使用的 IOPS 突发百分比。 在5分钟的时间间隔内发出。

## <a name="storage-io-utilization-metrics"></a>存储 IO 利用率指标
以下指标有助于诊断虚拟机和磁盘组合中的瓶颈。 仅当使用启用了高级版的 VM 时，才可以使用这些度量值。 这些指标适用于除 Ultra 之外的所有磁盘类型。 

有助于诊断磁盘 IO 上限的指标：

- 已使用的数据磁盘 IOPS 的百分比：通过将完成的数据磁盘 IOPS 与预配的数据磁盘 IOPS 相比计算得出的百分比。 如果此数为 100%，则表明正在运行的应用程序达到你的数据磁盘 IOPS 限制的 IO 上限。
- 已使用的数据磁盘带宽百分比：通过将完成的数据磁盘吞吐量与预配的数据磁盘吞吐量相比计算得出的百分比。 如果此数为 100%，则表明正在运行的应用程序达到你的数据磁盘带宽限制的 IO 上限。
- 已使用的 OS 磁盘 IOPS 的百分比：通过将完成的 OS 磁盘 IOPS 与预配的 OS 磁盘 IOPS 相比计算得出的百分比。 如果此数为 100%，则表明正在运行的应用程序达到你的 OS 磁盘 IOPS 限制的 IO 上限。
- 已使用的 OS 磁盘带宽百分比：通过将完成的 OS 磁盘吞吐量与预配的 OS 磁盘吞吐量相比计算得出的百分比。 如果此数为 100%，则表明正在运行的应用程序达到你的 OS 磁盘带宽限制的 IO 上限。

有助于诊断 VM IO 上限的指标：

- 已使用的 VM 缓存 IOPS 的百分比：通过将完成的总 IOPS 与最大缓存虚拟机 IOPS 限制相比计算得出的百分比。 如果此数为 100%，则表明正在运行的应用程序达到你的 VM 缓存 IOPS 限制的 IO 上限。
- 已使用的 VM 缓存带宽百分比：通过将完成的总磁盘吞吐量与最大缓存虚拟机吞吐量相比计算得出的百分比。 如果此数为 100%，则表明正在运行的应用程序达到你的 VM 缓存带宽限制的 IO 上限。
- 已使用的 VM 未缓存 IOPS 的百分比：通过将虚拟机上完成的总 IOPS 与最大非缓存虚拟机 IOPS 限制相比计算得出的百分比。 如果此数为 100%，则表明正在运行的应用程序达到你的 VM 非缓存 IOPS 限制的 IO 上限。
- 已使用的 VM 非缓存带宽百分比：通过将虚拟机上完成的总磁盘吞吐量与最大预配虚拟机吞吐量相比计算得出的百分比。 如果此数为 100%，则表明正在运行的应用程序达到你的 VM 非缓存带宽限制的 IO 上限。

## <a name="storage-io-metrics-example"></a>存储 IO 指标示例

让我们通过一个示例来了解如何使用这些新的存储 IO 利用率指标来帮助我们调试系统中的瓶颈。 系统设置与前面的示例相同，但是，这次附加的 OS 磁盘不缓存。

设置：

- Standard_D8s_v3
  - 缓存的 IOPS：16,000
  - 非缓存 IOPS：12,800
- P30 OS 磁盘
  - IOPS：5,000
  - 主机缓存：**已禁用**
- 两个 P30 数据磁盘 × 2
  - IOPS：5,000
  - 主机缓存：读取/写入
- 两个 P30 数据磁盘 × 2
  - IOPS：5,000
  - 主机缓存：**已禁用**

让我们对可创建 IO 活动的此虚拟机和磁盘组合运行基准测试。 若要了解如何在 Azure 上对存储 IO 进行基准测试，请参阅[在 Azure 磁盘存储上对应用程序进行基准测试](disks-benchmarks.md)。 通过基准测试工具，可以看到 VM 和磁盘组合可以实现 22,800 个 IOPS：

![突出显示了 r=22.8k 的 f i o 输出屏幕截图。](media/disks-metrics/utilization-metrics-example/fio-output.jpg)



Standard_D8s_v3 最多可以达到 28,600 个 IOPS。 让我们使用这些指标，调查正在进行的操作，并确定存储 IO 瓶颈。 在左侧窗格中，选择“指标”：

![左侧窗格中突出显示了“指标”的屏幕截图。](media/disks-metrics/utilization-metrics-example/metrics-menu.jpg)

我们先看一下 **已使用的 VM 缓存 IOPS 的百分比** 指标：

![显示“已使用的 VM 缓存 IOPS 的百分比”的屏幕截图。](media/disks-metrics/utilization-metrics-example/vm-cached.jpg)

此指标告诉我们，在 VM 上分配给缓存 IOPS 的 16,000 IOPS 中，使用了 61%。 此百分比意味着存储 IO 瓶颈与缓存的磁盘无关，因为该指标值没有达到 100%。 我们现在来看一下“已使用的 VM 未缓存 IOPS 的百分比”指标：

![显示“已使用的 VM 未缓存 IOPS 的百分比”的屏幕截图。](media/disks-metrics/utilization-metrics-example/vm-uncached.jpg)

此指标为 100%。 此指标告诉我们，在 VM 上分配给未缓存 IOPS 的所有 12,800 IOPS 均已使用。 可以修正此问题的一种方法是，将 VM 大小更改为可处理更多 IO 的更大的大小。 但在执行此操作之前，我们先来看一下附加的磁盘，以了解这些磁盘发生了多少 IOPS。 通过查看“已使用的 OS 磁盘 IOPS 的百分比”来看一下 OS 磁盘：

![显示“已使用的 OS 磁盘 IOPS 的百分比”的屏幕截图。](media/disks-metrics/utilization-metrics-example/os-disk.jpg)

此指标告诉我们，在为此 P30 OS 磁盘预配的 5,000 IOPS 中，使用了大约 90%。 此百分比表示操作系统磁盘上没有瓶颈。 现在我们通过查看 **已使用的数据磁盘 IOPS 的百分比** 来看一下附加到 VM 的数据磁盘：

![显示“已使用的数据磁盘 IOPS 的百分比”的屏幕截图。](media/disks-metrics/utilization-metrics-example/data-disks-no-splitting.jpg)

此指标告诉我们，在所有附加的磁盘上，平均已使用的 IOPS 百分比约为 42%。 此百分比是根据磁盘使用的 IOPS 计算得出的，并且不是从主机缓存中提供的。 让我们通过对这些指标应用拆分并按 LUN 值拆分来深入了解此指标：

![显示拆分后“已使用的数据磁盘 IOPS 的百分比”的屏幕截图。](media/disks-metrics/utilization-metrics-example/data-disks-splitting.jpg)

此指标告诉我们，LUN 3 和 2 上附加的数据磁盘使用了它们约 85% 的预配 IOPS。 下面是 VM 和磁盘体系结构中 IO 情况的示意图：

![存储 IO 指标示例的示意图。](media/disks-metrics/utilization-metrics-example/metrics-diagram.jpg)

## <a name="next-steps"></a>后续步骤

- [Azure Monitor 指标概述](../azure-monitor/essentials/data-platform-metrics.md)
- [指标聚合说明](../azure-monitor/essentials/metrics-aggregation-explained.md)
- [使用 Azure Monitor 创建、查看和管理指标警报](../azure-monitor/alerts/alerts-metric.md)