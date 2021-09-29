---
title: 将 NFS Blob 存储与 Azure HPC 缓存配合使用
description: 介绍将 ADLS-NFS blob 存储与 Azure HPC 缓存配合使用时的过程和限制
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 07/12/2021
ms.author: v-erkel
ms.openlocfilehash: 090003e25c2d8ca13be27bf94bc12b4941cda798
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124814660"
---
# <a name="use-nfs-mounted-blob-storage-with-azure-hpc-cache"></a>将装载了 NFS 的 blob 存储与 Azure HPC 缓存配合使用

可以将装载 NFS 的 blob 容器与 Azure HPC 缓存配合使用。 有关详细信息，请参阅 Blob 存储文档站点上的 [Azure blob 存储中的 NFS 3.0 协议支持](../storage/blobs/network-file-system-protocol-support.md)。

Azure HPC 缓存在其 ADLS-NFS 存储目标类型中使用已启用 NFS 的 blob 存储。 这些存储目标与常规 NFS 存储目标类似，而且与常规 Azure Blob 目标有一些重叠。

本文介绍使用 ADLS-NFS 存储目标时应了解的策略和限制。

此外，还应阅读 NFS blob 文档，尤其是以下介绍兼容和不兼容场景的部分：

* [功能概述](../storage/blobs/network-file-system-protocol-support.md)
* [性能注意事项](../storage/blobs/network-file-system-protocol-support-performance.md)
* [已知问题和限制](../storage/blobs/network-file-system-protocol-known-issues.md)

## <a name="understand-consistency-requirements"></a>了解一致性要求

HPC 缓存要求 ADLS-NFS 存储目标保持高度一致。 默认情况下，已启用 NFS 的 blob 存储不会严格更新文件元数据，这会导致 HPC 缓存无法准备比较文件版本。

为了解决这种差异，Azure HPC 缓存会自动在用作存储目标的任何已启用 NFS 的 blob 容器上禁用 NFS 属性缓存。

此设置在容器的生存期内保持不变，即使将其从缓存中删除也是如此。

## <a name="pre-load-data-with-nfs-protocol"></a>通过 NFS 协议预加载数据
<!-- cross-referenced from hpc-cache-ingest.md and here -->

在已启用 NFS 的 blob 容器上，只能通过创建某文件时所用的同一协议来编辑该文件。 也就是说，如果使用 Azure REST API 填充容器，则不能使用 NFS 来更新这些文件。 由于 Azure HPC 缓存只使用 NFS，因此无法编辑使用 Azure REST API 创建的任何文件。 （详细了解 [blob 存储 API 已知问题](../storage/blobs/data-lake-storage-known-issues.md#blob-storage-apis)）

如果容器为空，或者文件是使用 NFS 创建的，则不会出现缓存问题。

如果容器中的文件使用 Azure Blob 的 REST API（而不是 NFS）创建，则 Azure HPC 缓存受到限制，仅可对原始文件执行以下操作：

* 列出目录中的文件。
* 读取文件（并将其保存在缓存中，以便后续读取）。
* 删除文件。
* 清空文件（将其截断为 0）。
* 保存文件的副本。 该副本标记为 NFS 创建的文件，可以使用 NFS 进行编辑。

Azure HPC 缓存无法编辑使用 REST 创建的文件的内容。 这意味着缓存不能将已更改的文件从客户端保存回存储目标。

了解此限制很重要，因为如果在未使用 NFS 创建的文件上使用读/写缓存使用量模型，则可能会导致数据完整性问题。

> [!TIP]
> 请参阅[了解缓存使用量模型](cache-usage-models.md)，了解关于读写缓存的详细信息。

### <a name="write-caching-scenarios"></a>写入缓存方案

以下缓存使用量模型包括写入缓存：

* **超过 15% 的写入操作**
* 大于 15% 的写入量，每 30 秒检查一次后备服务器是否有更改
* 大于 15% 的写入量，每 60 秒检查一次后备服务器是否有更改
* 大于 15% 的写入量，每 30 秒写回到服务器一次

写入缓存使用量模型应仅用于使用 NFS 创建的文件。

如果尝试对 REST 创建的文件使用写入缓存，则文件更改可能会丢失。 这是因为缓存不会立即尝试将文件编辑保存到存储容器。

尝试将写入数缓存到 REST 创建的文件中会对数据产生风险的原因如下：

1. 缓存接受来自客户端的编辑，并在每次更改后返回一条成功消息。
1. 缓存会将更改的文件保存在其存储中，并等待其他更改。
1. 经过一段时间后，缓存会尝试将更改后的文件保存到后端容器。 此时，缓存会收到一条错误消息，因为它正尝试使用 NFS 写入 REST 创建的文件。

   此刻告诉客户端计算机并未接受其更改已来不及，并且缓存无法更新原始文件。 因此，来自客户端的更改将丢失。

### <a name="read-caching-scenarios"></a>读取缓存场景

读取缓存场景适用于通过 NFS 或 Azure Blob REST API 创建的文件。

这些使用情况量模型仅使用读取缓存：

* **读取操作频繁，写入操作罕见**
* 客户端写入到 NFS 目标，同时绕过缓存
* **读取操作频繁，每 3 小时检查一次备份服务器**

可以将这些使用情况量模型用于 REST API 或 NFS 创建的文件。 任何从客户端发送到后端容器的 NFS 写入仍会失败，但都将立即失败并向客户端返回一条错误消息。

读取缓存工作流仍可包含文件更改，只要未缓存这些更改。 例如，客户端可能会访问容器中的文件，但以新文件形式写回其更改，或者将修改后的文件保存在其他位置。

## <a name="recognize-network-lock-manager-nlm-limitations"></a>识别网络锁定管理器 (NLM) 限制

已启用 NFS 的 blob 容器不支持网络锁定管理器 (NLM)，这是一种常用的 NFS 协议，可防止文件冲突。

如果 NFS 工作流最初为硬件存储系统而编写，则客户端应用程序可能会包含 NLM 请求。 若要在将进程移到启用 NFS 的 blob 存储时解决此限制，请确保客户端在装载缓存时禁用 NLM。

若要禁用 NLM，请使用客户端的 ``mount`` 命令中的 ``-o nolock`` 选项。 此选项可防止客户端请求 NLM 锁定并在响应中接收错误。 ``nolock`` 选项在不同操作系统中以不同方式实现；有关详细信息，请查看客户端 OS 文档 (man 5 nfs)。

## <a name="streamline-writes-to-nfs-enabled-containers-with-hpc-cache"></a>利用 HPC 缓存简化对已启用 NFS 的容器的写入

Azure HPC 缓存有助于提升工作负载性能，包括将更改写入到 ADLS-NFS 存储目标。

> [!NOTE]
> 如果希望通过 Azure HPC 缓存修改 ADLS-NFS 存储容器的文件，则必须使用 NFS 来对其进行填充。

已启用 NFS 的 blob [性能注意事项一文](../storage/blobs/network-file-system-protocol-support-performance.md)中概述的一项限制是，ADLS-NFS 存储在覆盖现有文件方面不是非常有效。 如果将 Azure HPC 缓存与装载 NFS 的 blob 存储配合使用，则缓存会在客户端修改活动文件时处理间歇重写。 向后端容器写入文件的延迟会对客户端隐藏。

请记住[通过 NFS 协议预加载数据](#pre-load-data-with-nfs-protocol)中所述的限制。

## <a name="next-steps"></a>后续步骤

* 了解 [ADLS-NFS 存储目标先决条件](hpc-cache-prerequisites.md#nfs-mounted-blob-adls-nfs-storage-requirements)
* 创建[已启用 NFS 的 blob 存储帐户](../storage/blobs/network-file-system-protocol-support-how-to.md)
