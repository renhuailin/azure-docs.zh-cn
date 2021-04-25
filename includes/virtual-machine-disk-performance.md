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
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "92518038"
---
本文阐述了磁盘性能，以及在将 Azure 虚拟机和 Azure 磁盘组合使用时磁盘性能的工作原理。 此外还介绍了如何诊断磁盘 IO 的瓶颈，以及可以进行哪些更改以优化性能。

## <a name="how-does-disk-performance-work"></a>磁盘性能工作原理
Azure 虚拟机具有每秒输入/输出操作数 (IOPS) 和吞吐量性能限制，这些限制由虚拟机类型和大小决定。 OS 磁盘和数据磁盘可以附加到虚拟机。 磁盘具有各自的 IOPS 和吞吐量限制。

当应用程序所请求的 IOPS 或吞吐量大于为虚拟机或附加磁盘分配的 IOPS 或吞吐量时，应用程序的性能会达到上限。 达到上限时，应用程序的性能会降低。 这可能会导致负面后果，例如延迟增大。 让我们通过几个示例来阐明此概念。 为了使这些示例更容易理解，我们只看 IOPS。 但是，同样的逻辑也适用于吞吐量。

## <a name="disk-io-capping"></a>磁盘 IO 上限

设置：

- Standard_D8s_v3
  - 未缓存的 IOPS：12,800
- E30 OS 磁盘
  - IOPS：500
- 两个 E30 数据磁盘 × 2
  - IOPS：500

![示意图中显示了磁盘级上限。](media/vm-disk-performance/disk-level-throttling.jpg)

在虚拟机上运行的应用程序向虚拟机发出要求 10,000 个 IOPS 的请求。 VM 允许所有这些请求，因为 Standard_D8s_v3 虚拟机最多可以执行 12,800 个 IOPS。

10,000 IOPS 的请求随后会被分解为对不同磁盘的三个不同请求：

- 向操作系统磁盘请求 1,000 IOPS。
- 向每个数据磁盘请求 4,500 IOPS。

所有附加的磁盘都是 E30 磁盘，只能处理 500 IOPS。 因此，它们每个都以 500 IOPS 返回响应。 应用程序的性能会受到附加磁盘的限制，只能处理 1,500 IOPS。 如果使用性能更好的磁盘（例如，高级 SSD P30 磁盘），则应用程序的峰值性能可以达到 10,000 IOPS。

## <a name="virtual-machine-io-capping"></a>虚拟机 IO 上限

设置：

- Standard_D8s_v3
  - 未缓存的 IOPS：12,800
- P30 OS 磁盘
  - IOPS：5,000
- 两个 P30 数据磁盘 × 2
  - IOPS：5,000

![示意图中显示了虚拟机级上限。](media/vm-disk-performance/vm-level-throttling.jpg)

在虚拟机上运行的应用程序发出需要 15,000 IOPS 的请求。 遗憾的是，Standard_D8s_v3 虚拟机仅预配为处理 12,800 IOPS。 应用程序受限于虚拟机限制，必须对分配给它的 12,800 IOPS 进行分配。

请求的这 12,800 IOPS 随后会被分解为对不同磁盘的三个不同请求：

- 向操作系统磁盘请求 4,267 IOPS。
- 向每个数据磁盘请求 4,266 IOPS。

所有附加的磁盘都是可处理 5,000 IOPS 的 P30 磁盘。 因此，它们将以所请求的数量返回响应。
