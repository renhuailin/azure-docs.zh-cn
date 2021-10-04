---
title: Azure Blob 存储中的 NFS 3.0 性能注意事项 | Microsoft Docs
description: 使用本文中的建议优化网络文件系统 (NFS) 3.0 存储请求的性能。
author: normesta
ms.subservice: blobs
ms.service: storage
ms.topic: conceptual
ms.date: 06/21/2021
ms.author: normesta
ms.reviewer: yzheng
ms.openlocfilehash: 8fb4583fbf04637c58795d6532dcce82ccb8168e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128624968"
---
# <a name="network-file-system-nfs-30-performance-considerations-in-azure-blob-storage"></a>Azure Blob 存储中的网络文件系统 (NFS) 3.0 性能注意事项

Blob 存储现在支持网络文件系统 (NFS) 3.0 协议。 本文包含有助于优化存储请求性能的建议。 若要了解有关 Azure Blob 存储中 NFS 3.0 支持的详细信息，请参阅 [Azure Blob 存储中的网络文件系统 (NFS) 3.0 协议支持](network-file-system-protocol-support.md)。

## <a name="add-clients-to-increase-throughput"></a>添加客户端以增加吞吐量

Azure Blob 存储可线性扩展，直至达到最大存储帐户流出量和流入量限制。 因此，应用程序可以通过使用更多客户端实现更高的吞吐量。 若要查看存储帐户流出量和流入量限制，请参阅[标准存储帐户的可伸缩性和性能目标](../common/scalability-targets-standard-account.md)。

下图显示了添加更多客户端时带宽的增长情况。 在此图表中，客户端是虚拟机 (VM)，帐户使用的是标准性能层。

> [!div class="mx-imgBorder"]
> ![标准性能](./media/network-file-system-protocol-support-performance/standard-performance-tier.png)

下图显示了应用于使用高级性能层的帐户时取得了相同的效果。

> [!div class="mx-imgBorder"]
> ![高级性能层](./media/network-file-system-protocol-support-performance/premium-performance-tier.png)

## <a name="use-premium-performance-tier-for-small-scale-applications"></a>将高级性能层用于小型应用程序

并非所有应用程序都可以通过添加更多客户端实现扩展。 对于这些应用程序，[Azure 高级块 blob 存储帐户](../common/storage-account-create.md)提供一致的低延迟和高事务率。 高级块 blob 存储帐户可以用更少的线程和客户端达到最大带宽。 例如，使用单个客户端时，与使用标准性能常规用途 v2 存储帐户的相同设置相比，高级块 blob 存储帐户可以实现 2.3 倍带宽。

下图中的每个条形图显示了高级和标准性能存储帐户在实现带宽方面的差异。 随着客户端数量的增加，这一差异会降低。

> [!div class="mx-imgBorder"]
> ![相对性能](./media/network-file-system-protocol-support-performance/relative-performance.png)

## <a name="improve-read-ahead-size-to-increase-large-file-read-throughput"></a>提高预读大小以提高大型文件读取吞吐量

read_ahead_kb 内核参数表示满足给定读取请求后应读取的其他数据量。 可以将此参数增大到 16MB，以提高大型文件读取吞吐量。

```
export AZMNT=/your/container/mountpoint

echo 15728640 > /sys/class/bdi/0:$(stat -c "%d" $AZMNT)/read_ahead_kb
```

## <a name="avoid-frequent-overwrites-on-data"></a>避免频繁执行日期覆盖操作

完成覆盖操作比完成新的写入操作需要更长的时间。 这是因为 NFS 覆盖操作（特别是部分就地文件编辑）是多个基础 blob 操作的组合：读取、修改和写入操作。 因此，需要频繁进行就地编辑的应用程序并不适合启用了 NFS 的 blob 存储帐户。

## <a name="deploy-azure-hpc-cache-for-latency-sensitive-applications"></a>为延迟敏感型应用程序部署 Azure HPC 缓存

除了高吞吐量之外，某些应用程序可能还需要低延迟。 可以部署 [Azure HPC 缓存](../../hpc-cache/nfs-blob-considerations.md)来显著改善延迟。 详细了解 [Blob 存储中的延迟](storage-blobs-latency.md)。

## <a name="other-best-practice-recommendations"></a>其他最佳做法建议

- 使用网络带宽充足的 VM。

- 在工作负载允许的情况下使用多个装入点。

- 使用尽可能多的线程。

- 使用较大的块大小。

- 从与存储帐户位于同一区域的客户端发出存储请求。 这可以改善网络延迟。

## <a name="next-steps"></a>后续步骤

- 若要了解有关 Azure Blob 存储中 NFS 3.0 支持的详细信息，请参阅 [Azure Blob 存储中的网络文件系统 (NFS) 3.0 协议支持](network-file-system-protocol-support.md)。

- 若要开始操作，请参阅[使用网络文件系统 (NFS) 3.0 协议装载 Blob 存储](network-file-system-protocol-support-how-to.md)。
