---
title: Azure HPC 缓存方案
description: 介绍如何了解计算作业是否适合 Azure HPC 缓存
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 03/29/2021
ms.author: v-erkel
ms.openlocfilehash: 84d5948cd4d3fbf69c3e8e983dedc280f1f7d950
ms.sourcegitcommit: b5508e1b38758472cecdd876a2118aedf8089fec
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2021
ms.locfileid: "113585728"
---
# <a name="is-your-job-a-good-fit-for-azure-hpc-cache"></a>你的作业是否适合 Azure HPC 缓存？

Azure HPC 缓存可以为各专业中的高性能计算作业加快对数据的访问速度。 但这并不适用于所有类型的工作流。 本文介绍了如何确定 HPC 缓存是否适合你的需求。

[概述](hpc-cache-overview.md)文章还简要概述了何时使用 Azure HPC 缓存，并提供了一些用例。

此外，请阅读[此文](nfs-blob-considerations.md)，了解如何充分利用[装载了 NFS 的 blob 存储](../storage/blobs/network-file-system-protocol-support.md)。

## <a name="nfs-version-30-applications"></a>NFS 版本 3.0 应用程序

Azure HPC 缓存仅支持 NFS 3.0 客户端。

## <a name="high-read-to-write-ratio"></a>高读写比率

计算客户端的读取操作多于写入操作的工作负载通常适合缓存。 例如，如果读写比率为 80/20 或 70/30，则 Azure HPC 缓存就很有用了，它可以通过缓存提供经常请求的文件，而不必反复从远程存储提取它们这些文件。

比起普通客户端直接向存储发出请求，首次提取文件并将其存储在缓存中通常会有较小的额外延迟，但客户端下次请求同一文件时，效率就会提高。 这尤其适用于大型文件。 如果每个客户端请求都是唯一的，则 HPC 缓存的影响将受到限制。 但文件越大，在首次访问后的性能就越好。

## <a name="file-based-analytic-workload"></a>基于文件的分析工作负载

Azure HPC 缓存非常适合于使用基于文件的数据并在大量计算客户端上运行的管道，尤其是在计算客户端为 Azure 虚拟机时。 它可以帮助解决由于文件访问时间过长导致的性能缓慢或不一致问题。

## <a name="remote-data-access"></a>远程数据访问

如果工作负载需要访问无法再靠近计算资源的远程数据，Azure HPC 缓存可帮助减少延迟。 例如，记录可能位于广域网环境的远端、其他 Azure 区域或客户数据中心。 （这有时称为“文件突发”。）

## <a name="heavy-request-load"></a>繁重的请求负载

如果大量客户端同时请求源中的数据，Azure HPC 缓存可以加速文件访问。 例如，Azure HPC 缓存在与高性能计算群集配合使用时，可通过缓存为大量并发请求提供可伸缩性。

## <a name="compute-resources-are-located-in-azure"></a>计算资源位于 Azure 中

Azure 虚拟机是高性能计算工作负载的可缩放且经济高效的解决方案。 Azure HPC 缓存可以通过将这些虚拟机所需的信息放到更靠近的位置来提供帮助，尤其是原始数据存储在远程系统中时。

如果客户想要在 Azure 虚拟机上“按原样”运行其当前管道，则 Azure HPC 缓存可以提供基于 POSIX 的共享存储（或缓存）解决方案以实现可缩放性。

通过使用 Azure HPC 缓存，无需重新构建工作管道即可对 Azure Blob 存储进行本机调用。 你可以在原始系统上访问数据，也可以使用 HPC 缓存将其移动到新的 blob 容器。

## <a name="next-steps"></a>后续步骤

* 在[概述](hpc-cache-overview.md)和[先决条件](hpc-cache-prerequisites.md)文章中详细了解如何规划和配置缓存
* 了解将[启用了 NFS 的 Blob 存储](nfs-blob-considerations.md)与 Azure HPC 缓存配合使用的注意事项
