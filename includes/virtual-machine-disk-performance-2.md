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
ms.openlocfilehash: 82b4c127f983f3133326bf7fb538e40713ef9655
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/17/2021
ms.locfileid: "100580397"
---
![图表中显示了 D s v 3 规范。](media/vm-disk-performance/dsv3-documentation.jpg)

- 最大非缓存磁盘吞吐量是虚拟机能够处理的默认存储最大限制。
- 启用主机缓存时，最大 *缓存* 存储吞吐量限制是一个单独的限制。

主机缓存的工作原理是，使存储更接近可以快速写入或读取的 VM。 文档中介绍了可供 VM 用于主机缓存的存储量。 例如，可以看到 Standard_D8s_v3 带有 200 GiB 的缓存存储。

你可以在创建虚拟机并附加磁盘时启用主机缓存。 还可以在现有 VM 的磁盘上打开和关闭主机缓存。

![屏幕截图显示了主机缓存。](media/vm-disk-performance/host-caching.jpg)

你可以调整主机缓存来满足每个磁盘的工作负荷要求。 可以将主机缓存设置为：

- 只读：适用于仅执行读取操作的工作负荷
- 读取/写入：适用于执行均衡的读取和写入操作的工作负荷

如果你的工作负载不符合上述任一模式，建议不要使用主机缓存。

让我们通过几个示例来了解不同的主机缓存设置，看其如何影响数据流和性能。 在这第一个示例中，我们将了解当主机缓存设置设为“只读”时，IO 请求会发生什么。

设置：

- Standard_D8s_v3
  - 缓存的 IOPS：16,000
  - 非缓存 IOPS：12,800
- P30 数据磁盘
  - IOPS：5,000
  - 主机缓存：**只读**

当执行读取操作且所需数据在缓存上可用时，缓存会返回所请求的数据。 不需要从磁盘读取数据。 此读取会计入 VM 的缓存限制。

![示意图中显示了读取主机缓存读取命中。](media/vm-disk-performance/host-caching-read-hit.jpg)

当执行读取操作且所需数据在缓存上不可用时，读取请求会被中继到磁盘。 然后，磁盘将数据提供给缓存和 VM。 此读取操作会同时计入 VM 的非缓存限制和 VM 的缓存限制。

![示意图中显示了读取主机缓存读取未命中。](media/vm-disk-performance/host-caching-read-miss.jpg)

执行写入操作时，必须同时写入缓存和磁盘，然后才能将其视为已完成。 此写入操作会同时计入 VM 的非缓存限制和 VM 的缓存限制。

![示意图中显示了读取主机缓存写入。](media/vm-disk-performance/host-caching-write.jpg)

接下来，我们将了解当主机缓存设置设为“读取/写入”时，IO 请求会发生什么。

设置：

- Standard_D8s_v3
  - 缓存的 IOPS：16,000
  - 非缓存 IOPS：12,800
- P30 数据磁盘
  - IOPS：5,000
  - 主机缓存：读取/写入

读取的处理方式与只读完全相同。 对于读取/写入缓存，只有写入存在差别。 在主机缓存设置为“读取/写入”的情况下进行写入时，只需要写入主机缓存即可将其视为完成。 然后，将该写入操作作为后台进程延迟写入磁盘。 这意味着当写入到缓存时，该写入计入缓存的 IO。 如果它被延迟写入到磁盘，则会计入未缓存的 IO。

![示意图中显示了读取/写入主机缓存写入。](media/vm-disk-performance/host-caching-read-write.jpg)

接下来，我们 Standard_D8s_v3 的虚拟机。 但是，这次我们会在磁盘上启用主机缓存。 另外，现在 VM 的 IOPS 限制为 16,000 IOPS。 附加到 VM 的三个基础 P30 磁盘每个可处理 5,000 IOPS。

设置：

- Standard_D8s_v3
  - 缓存的 IOPS：16,000
  - 非缓存 IOPS：12,800
- P30 OS 磁盘
  - IOPS：5,000
  - 主机缓存：读取/写入
- 两个 P30 数据磁盘 × 2
  - IOPS：5,000
  - 主机缓存：读取/写入

![示意图中显示了主机缓存示例。](media/vm-disk-performance/host-caching-example-without-remote.jpg)

应用程序使用了已启用缓存的 Standard_D8s_v3 虚拟机。 它发出了 15000 IOPS 的请求。 这些请求将分解为每个附加的基础磁盘 5,000 IOPS。 不会达到性能上限。

## <a name="combined-uncached-and-cached-limits"></a>组合的非缓存和缓存限制

虚拟机的缓存限制独立于其非缓存限制。 这意味着，你可以在附加到 VM 的磁盘上启用主机缓存，同时不在其他磁盘上启用主机缓存。 此配置使得虚拟机获得的总存储 IO 为缓存限制外加非缓存限制。

让我们通过一个示例来帮助你了解这些限制如何协同工作。 我们将继续讨论 Standard_D8s_v3 虚拟机和附加了高级磁盘的配置。

设置：

- Standard_D8s_v3
  - 缓存的 IOPS：16,000
  - 非缓存 IOPS：12,800
- P30 OS 磁盘
  - IOPS：5,000
  - 主机缓存：读取/写入
- 两个 P30 数据磁盘 × 2
  - IOPS：5,000
  - 主机缓存：读取/写入
- 两个 P30 数据磁盘 × 2
  - IOPS：5,000
  - 主机缓存：**已禁用**

![示意图中显示了具有远程存储的主机缓存示例。](media/vm-disk-performance/host-caching-example-with-remote.jpg)

在此示例中，在 Standard_D8s_v3 虚拟机上运行的应用程序发出了 25,000 IOPS 的请求。 该请求将分解为每个附加的磁盘 5,000 IOPS。 三个磁盘使用主机缓存，两个磁盘未使用主机缓存。

- 由于使用主机缓存的三个磁盘在缓存限制 (16,000) 内，因此这些请求会成功完成。 不会达到存储性能上限。
- 由于未使用主机缓存的两个磁盘在非缓存限制 (12,800) 内，因此这些请求也会成功完成。 不会达到上限。

