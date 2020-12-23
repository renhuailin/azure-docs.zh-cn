---
title: include 文件
description: include 文件
services: virtual-machines
author: albecker1
ms.service: virtual-machines
ms.topic: include
ms.date: 10/12/2020
ms.author: albecker1
ms.custom: include file
ms.openlocfilehash: 8eff9da82fdfa5749fd1c2bc04652d5c8ce8dfd2
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/24/2020
ms.locfileid: "92518039"
---
![显示 r = 22.8 k 的 f i o 输出屏幕截图。](media/vm-disk-performance/utilization-metrics-example/fio-output.jpg)

Standard_D8s_v3 可以获得总共 28600 IOPS。 使用度量值，让我们调查正在进行的操作，并确定存储 IO 瓶颈。 在左侧窗格中，选择 " **指标**"：

![显示左窗格中突出显示的指标的屏幕截图。](media/vm-disk-performance/utilization-metrics-example/metrics-menu.jpg)

首先，让我们看一下我们的 **VM 缓存 IOPS 消耗百分比** 指标：

![显示 V M 缓存的 i/o 已用百分比的屏幕截图。](media/vm-disk-performance/utilization-metrics-example/vm-cached.jpg)

此指标告诉我们，使用了61% 的 VM 上已分配给缓存 IOPS 的 16000 IOPS。 此百分比表示存储 IO 瓶颈与缓存的磁盘无关，因为它不在100%。 现在，让我们看看我们的 **VM 未缓存 IOPS 使用百分比** 指标：

![显示 V M 未缓存的 i/o 已用百分比的屏幕截图。](media/vm-disk-performance/utilization-metrics-example/vm-uncached.jpg)

此指标为100%。 它告诉我们，使用了所有分配给 VM 上的未缓存 IOPS 的 12800 IOPS。 解决此问题的一种方法是将 VM 的大小更改为可处理额外 IO 的更大大小。 但在执行此操作之前，让我们先看一下附加的磁盘，找出他们看到的 IOPS 数。 查看 os 磁盘的 **消耗百分比**：

![显示已使用的 O S 磁盘 i/o P 的屏幕截图。](media/vm-disk-performance/utilization-metrics-example/os-disk.jpg)

此指标告诉我们，为此 P30 OS 磁盘预配的 5000 IOPS 大约90%。 此百分比表示操作系统磁盘上没有瓶颈。 现在，让我们通过查看已 **使用的数据磁盘 IOPS 百分比**来查看附加到 VM 的数据磁盘：

![显示数据磁盘已使用的数据的屏幕截图百分比。](media/vm-disk-performance/utilization-metrics-example/data-disks-no-splitting.jpg)

此指标告诉我们，所有附加磁盘的平均 IOPS 使用百分比约为42%。 此百分比是根据磁盘使用的 IOPS 计算得出的，并且不是从主机缓存中提供的。 让我们通过对这些指标应用 *拆分* 并按 LUN 值进行拆分来深入了解此指标：

![显示数据磁盘 I O P S 使用拆分的数据的屏幕截图。](media/vm-disk-performance/utilization-metrics-example/data-disks-splitting.jpg)

此指标告诉我们，LUN 3 和2上附加的数据磁盘的使用约85% 的预配 IOPS。 下面是 VM 和磁盘体系结构中 IO 的外观示意图：

![存储 I O 指标示例示意图。](media/vm-disk-performance/utilization-metrics-example/metrics-diagram.jpg)
