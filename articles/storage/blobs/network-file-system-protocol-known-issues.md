---
title: Azure Blob 存储中 NFS 3.0 的已知问题 | Microsoft Docs
description: 了解 Azure Blob 存储中网络文件系统 (NFS) 3.0 协议支持的限制和已知问题。
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 06/21/2021
ms.author: normesta
ms.reviewer: yzheng
ms.openlocfilehash: 8af0b4a5c26d70f9fdedc30d07c8953bfee76fb4
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2021
ms.locfileid: "113727123"
---
# <a name="known-issues-with-network-file-system-nfs-30-protocol-support-in-azure-blob-storage"></a>Azure Blob 存储中网络文件系统 (NFS) 3.0 协议支持的已知问题

本文介绍 Azure Blob 存储中网络文件系统 (NFS) 3.0 协议支持的限制和已知问题。

## <a name="nfs-30-support"></a>NFS 3.0 支持

- 无法在现有存储帐户上启用 NFS 3.0 支持。

- 启用 NFS 3.0 支持后，无法在存储帐户中将其禁用。

## <a name="nfs-30-features"></a>NFS 3.0 功能

尚不支持以下 NFS 3.0 功能。

- 基于 UDP 的 NFS 3.0。 仅支持基于 TCP 的 NFS 3.0。

- 使用网络锁定管理器 (NLM) 锁定文件。 装载命令必须包含 `-o nolock` 参数。

- 装载子目录。 只能装载根目录（容器）。

- 列出装载（例如：通过使用 `showmount -a` 命令）

- 列出导出（例如：通过使用 `showmount -e` 命令）

- 硬链接

- 以只读方式导出容器

## <a name="nfs-30-clients"></a>NFS 3.0 客户端

Windows 客户端尚不支持 NFS

## <a name="blob-storage-features"></a>Blob 存储功能

尚不支持以下 Blob 存储功能。

- [Azure Active Directory (AD) 安全性](../common/authorize-data-access.md?toc=/azure/storage/blobs/toc.json)
- [Azure 备份集成](../../backup/blob-backup-overview.md)
- [Azure 存储分析日志记录](../common/storage-analytics-logging.md?toc=/azure/storage/blobs/toc.json)
- [Blob 索引标记](storage-blob-index-how-to.md)
- [Blob 存储事件](storage-blob-event-overview.md)
- [Blob 版本控制](versioning-enable.md)
- [更改源](storage-blob-change-feed.md)
- [客户管理的帐户故障转移](../common/storage-disaster-recovery-guidance.md?toc=/azure/storage/blobs/toc.json)
- [用于 Azure 存储加密的客户提供的密钥](encryption-customer-provided-keys.md) 
- [加密范围](encryption-scope-overview.md)
- [用于生命周期管理的上次访问时间跟踪](storage-lifecycle-management-concepts.md#move-data-based-on-last-accessed-date-preview)
- [块 blob 的对象复制](object-replication-overview.md)
- [页 Blob](storage-blobs-introduction.md#blobs)
- [块 blob 的时间点还原](point-in-time-restore-overview.md)
- [blob 的软删除](soft-delete-blob-overview.md)
- [容器软删除](soft-delete-container-overview.md)
- [blob 快照](snapshots-overview.md)
- [静态网站托管](storage-blob-static-website.md)

## <a name="blob-storage-apis"></a>Blob 存储 API

可对相同的数据应用 NFS 3.0、Blob API 和 Data Lake Storage Gen2 API。 

本部分介绍对相同的数据应用 NFS 3.0、Blob API 和 Data Lake Storage Gen2 API 时的问题和限制。 

- 不能使用 NFS 3.0 和 Blob API 或 Data Lake Storage API 写入文件的同一实例。 如果使用 NFS 向某个文件进行写入，则调用 [获取阻止列表](/rest/api/storageservices/get-block-list)  Blob API 时将看不到该文件的块。 唯一的例外是在覆盖时可以使用。 可以使用任一 API 覆盖文件/Blob。 还可以使用零截断选项覆盖 NFS 3.0。

- 如果在使用[列出 Blob](/rest/api/storageservices/list-blobs) 操作时不指定分隔符，则结果会包含目录和 Blob。 如果选择使用分隔符，请只使用正斜杠 (`/`)。 这是唯一支持的分隔符。

- 如果使用[删除 Blob](/rest/api/storageservices/delete-blob) API 来删除目录，则只能在该目录为空的情况下将其删除。 这意味着，不能使用 Blob API 以递归方式删除目录。

这些 Blob REST API 不受支持：

* [放置 Blob（页）](/rest/api/storageservices/put-blob)
* [放置页](/rest/api/storageservices/put-page)
* [获取页面范围](/rest/api/storageservices/get-page-ranges)
* [增量复制 Blob](/rest/api/storageservices/incremental-copy-blob)
* [从 URL 放置页](/rest/api/storageservices/put-page-from-url)
* [放置块列表](/rest/api/storageservices/put-block-list)

具有分层命名空间的帐户不支持非托管 VM 磁盘。 若要在存储帐户中启用分层命名空间，请将非托管 VM 磁盘置于未启用分层命名空间功能的存储帐户中。 

## <a name="see-also"></a>另请参阅

- [Azure Blob 存储中的网络文件系统 (NFS) 3.0 协议支持](network-file-system-protocol-support.md)
- [使用网络文件系统 (NFS) 3.0 协议装载 Blob 存储](network-file-system-protocol-support-how-to.md)