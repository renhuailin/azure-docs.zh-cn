---
title: include 文件
description: include 文件
services: virtual-machines
author: albecker1
ms.service: virtual-machines
ms.topic: include
ms.date: 09/25/2020
ms.author: albecker1
ms.custom: include file
ms.openlocfilehash: 4770ac0181c64ef800aa02ba87284c8add357e36
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/24/2020
ms.locfileid: "92518038"
---
本文介绍如何在将 Azure 虚拟机和 Azure 磁盘结合使用时，澄清磁盘性能以及其工作原理。 还介绍了如何诊断磁盘 IO 的瓶颈，以及可以进行哪些更改以优化性能。

## <a name="how-does-disk-performance-work"></a>磁盘性能如何工作？
Azure 虚拟机根据虚拟机类型和大小，每秒有输入/输出操作 (IOPS) 和吞吐量性能限制。 OS 磁盘和数据磁盘可以附加到虚拟机。 磁盘具有其各自的 IOPS 和吞吐量限制。

当应用程序请求的 IOPS 或吞吐量超出为虚拟机或附加磁盘分配的 IOPS 或吞吐量时，应用程序的性能会受到限制。 上限时，应用程序的性能将会提高。 这可能导致负面后果，如增加延迟。 让我们通过几个示例来阐明这一概念。 要使这些示例易于理解，只需查看 IOPS。 但是，相同的逻辑也适用于吞吐量。

## <a name="disk-io-capping"></a>磁盘 IO 上限

**程序**

- Standard_D8s_v3
  - 未缓存 IOPS：12800
- E30 OS 磁盘
  - IOPS：500
- 两个 E30 数据磁盘×2
  - IOPS：500

![显示磁盘级上限的关系图。](media/vm-disk-performance/disk-level-throttling.jpg)

虚拟机上运行的应用程序向虚拟机发出要求 10000 IOPS 的请求。 VM 允许所有这些，因为 Standard_D8s_v3 虚拟机最多可执行 12800 IOPS。

10000 IOPS 请求会划分为三个不同磁盘的三个不同请求：

- 1000 IOPS 请求操作系统磁盘。
- 每个数据磁盘请求 4500 IOPS。

所有附加的磁盘都是 E30 磁盘，只能处理 500 IOPS。 因此，它们每个都响应 500 IOPS。 应用程序的性能由附加的磁盘上限，并且只能处理 1500 IOPS。 如果使用更好执行的磁盘（如高级 SSD P30 磁盘），则应用程序可在 10000 IOPS 的情况下工作。

## <a name="virtual-machine-io-capping"></a>虚拟机 IO 上限

**程序**

- Standard_D8s_v3
  - 未缓存 IOPS：12800
- P30 OS 磁盘
  - IOPS：5000
- 两个 P30 数据磁盘×2
  - IOPS：5000

![显示虚拟机级别上限的关系图。](media/vm-disk-performance/vm-level-throttling.jpg)

虚拟机上运行的应用程序发出请求，要求 15000 IOPS。 遗憾的是，仅将 Standard_D8s_v3 虚拟机设置为处理 12800 IOPS。 应用程序受虚拟机限制的限制，必须分配分配的 12800 IOPS。

请求的 12800 IOPS 分为三个不同磁盘的三个不同请求：

- 4267 IOPS 请求操作系统磁盘。
- 每个数据磁盘请求 4266 IOPS。

所有附加的磁盘都是 P30 的磁盘，可处理 5000 IOPS。 因此，它们会回复其请求的数量。
