---
title: Azure Blob 存储中的 NFS 3.0 性能注意事项 (预览版) |Microsoft Docs
description: 使用本文中的建议优化网络文件系统 (NFS) 3.0 存储请求的性能。
author: normesta
ms.subservice: blobs
ms.service: storage
ms.topic: conceptual
ms.date: 02/23/2021
ms.author: normesta
ms.reviewer: yzheng
ms.custom: references_regions
ms.openlocfilehash: f8a780afba1f5703fbe457e113ed1b455f1e9b64
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101744407"
---
# <a name="network-file-system-nfs-30-performance-considerations-in-azure-blob-storage-preview"></a>Azure Blob 存储中的网络文件系统 (NFS) 3.0 性能注意事项 (预览) 

Blob 存储现在支持网络文件系统 (NFS) 3.0 协议。 本文包含有助于优化存储请求性能的建议。 若要了解有关 Azure Blob 存储中的 NFS 3.0 支持的详细信息，请参阅 [Azure blob 存储中的网络文件系统 (nfs) 3.0 协议支持 (预览) ](network-file-system-protocol-support.md)。

> [!NOTE]
> Azure Blob 存储中的 NFS 3.0 协议支持提供公共预览版。 它支持在以下区域中具有标准层性能的 GPV2 存储帐户：澳大利亚东部、韩国中部和美国中南部。 预览版还支持所有公共区域中具有高级性能层的块 blob。

## <a name="add-clients-to-increase-throughput"></a>添加客户端以增加吞吐量 

Azure Blob 存储以线性方式缩放，直到达到最大存储帐户出口和入口限制。 因此，应用程序可以使用更多客户端实现更高的吞吐量。  若要查看存储帐户出口和入口限制，请参阅 [标准存储帐户的可伸缩性和性能目标](../common/scalability-targets-standard-account.md)。

下图显示了在添加更多客户端时，如何增加带宽。 在此图表中，客户端是虚拟机 (VM) ，帐户使用标准性能层。 

> [!div class="mx-imgBorder"]
> ![标准性能](./media/network-file-system-protocol-support-performance/standard-performance-tier.png)

下图显示了应用于使用 "高级" 性能层的帐户时的相同效果。

> [!div class="mx-imgBorder"]
> ![标准性能](./media/network-file-system-protocol-support-performance/premium-performance-tier.png)

## <a name="use-premium-performance-tier-for-small-scale-applications"></a>将高级性能层用于小型应用程序

并非所有应用程序都可以通过添加更多客户端来扩展。 对于这些应用程序， [Azure 高级块 blob 存储帐户](storage-blob-create-account-block-blob.md) 提供一致的低延迟和高事务速率。 高级块 blob 存储帐户可通过更少的线程和客户端达到最大带宽。 例如，使用单个客户端时，高级块 blob 存储帐户可以实现 **2.3 x** 的带宽，与使用标准性能常规用途 v2 存储帐户时所用的相同安装程序相比。 

下图中的每个条形显示了高级和标准性能存储帐户之间的带宽差异。 随着客户端数量的增加，这一差异会降低。  

> [!div class="mx-imgBorder"]
> ![相对性能](./media/network-file-system-protocol-support-performance/relative-performance.png)

## <a name="avoid-frequent-overwrites-on-date"></a>避免在日期频繁覆盖

完成覆盖操作所用的时间比新的写入操作长。 这是因为 NFS 覆盖操作（特别是部分就地文件编辑）是多个基础 blob 操作的组合：读取、修改和写入操作。 因此，需要经常进行编辑的应用程序并不适合启用 NFS 的 blob 存储帐户。 

## <a name="other-best-practice-recommendations"></a>其他最佳实践建议 

- 使用具有足够网络带宽的 Vm。

- 当工作负荷允许时使用多个装入点。

- 尽可能多地使用线程。

- 使用较大的块大小。

- 从与存储帐户位于同一区域的客户端发出存储请求。 这可以提高网络延迟。

## <a name="next-steps"></a>后续步骤

- 若要了解有关 Azure Blob 存储中的 NFS 3.0 支持的详细信息，请参阅 [Azure blob 存储中的网络文件系统 (nfs) 3.0 协议支持 (预览) ](network-file-system-protocol-support.md)。

- 若要开始，请参阅 [使用网络文件系统装载 Blob 存储 (NFS) 3.0 协议 (预览) ](network-file-system-protocol-support-how-to.md)。
