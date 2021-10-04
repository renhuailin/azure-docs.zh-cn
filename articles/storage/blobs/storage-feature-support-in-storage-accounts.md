---
title: Azure 存储帐户中的 Blob 存储功能支持
description: 确定对给定存储帐户类型的每个存储帐户功能的支持级别，以及对帐户启用的设置。
author: normesta
ms.subservice: blobs
ms.service: storage
ms.topic: conceptual
ms.date: 09/08/2021
ms.author: normesta
ms.openlocfilehash: 758a89e8366e338fe5aaaee7fd6b89d9f7daa588
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128604011"
---
# <a name="blob-storage-feature-support-in-azure-storage-accounts"></a>Azure 存储帐户中的 Blob 存储功能支持

本文说明某个功能是完全支持（正式发布）、在预览级别受支持还是尚不支持。 支持级别受存储帐户类型以及帐户是否启用了某些功能或协议的影响。

随着支持继续扩展，这些表中出现的项会随时间而改变。

## <a name="standard-general-purpose-v2-accounts"></a>标准常规用途 v2 帐户

| 存储功能 | Blob 存储（默认支持） | Data Lake Storage Gen2 <sup>1</sup>   | NFS 3.0 <sup>1</sup>  |
|---------------|-------------------|---|---|
| [访问层 - 存档](storage-blob-storage-tiers.md) | ![是](../media/icons/yes-icon.png) | ![是](../media/icons/yes-icon.png) | ![是](../media/icons/yes-icon.png) |
| [访问层 - 冷访问层](storage-blob-storage-tiers.md)   | ![是](../media/icons/yes-icon.png) | ![是](../media/icons/yes-icon.png) | ![是](../media/icons/yes-icon.png)|
| [访问层 - 热访问层](storage-blob-storage-tiers.md) | ![是](../media/icons/yes-icon.png) | ![是](../media/icons/yes-icon.png) | ![是](../media/icons/yes-icon.png) |
| [匿名公共访问](anonymous-read-access-configure.md) | ![是](../media/icons/yes-icon.png) | ![是](../media/icons/yes-icon.png) | ![是](../media/icons/yes-icon.png)|
| [Azure Active Directory 安全](authorize-access-azure-active-directory.md) | ![是](../media/icons/yes-icon.png) | ![是](../media/icons/yes-icon.png) | ![否](../media/icons/no-icon.png) |
| [Blob 清单](blob-inventory.md) | ![是](../media/icons/yes-icon.png) | ![是](../media/icons/yes-icon.png)  <sup>2</sup> | ![是](../media/icons/yes-icon.png) <sup>2</sup> |
| [Blob 索引标记](storage-manage-find-blobs.md) | ![是](../media/icons/yes-icon.png) | ![否](../media/icons/no-icon.png) | ![否](../media/icons/no-icon.png) |
| [blob 快照](snapshots-overview.md) | ![是](../media/icons/yes-icon.png) | ![是](../media/icons/yes-icon.png)  <sup>2</sup> | ![否](../media/icons/no-icon.png) |
| [Blob 存储 API](reference.md) | ![是](../media/icons/yes-icon.png) | ![是](../media/icons/yes-icon.png) <sup>3</sup> | ![是](../media/icons/yes-icon.png) <sup>3</sup>|
| [Blob 存储 Azure CLI 命令](storage-quickstart-blobs-cli.md) | ![是](../media/icons/yes-icon.png) | ![是](../media/icons/yes-icon.png) | ![是](../media/icons/yes-icon.png) |
| [Blob 存储事件](storage-blob-event-overview.md) | ![是](../media/icons/yes-icon.png) | ![是](../media/icons/yes-icon.png) <sup>3</sup>  | ![否](../media/icons/no-icon.png) |
| [Blob 存储 PowerShell 命令](storage-quickstart-blobs-powershell.md) | ![是](../media/icons/yes-icon.png) | ![是](../media/icons/yes-icon.png) | ![是](../media/icons/yes-icon.png) |
| [Blob 版本控制](versioning-overview.md) | ![是](../media/icons/yes-icon.png) | ![否](../media/icons/no-icon.png) | ![否](../media/icons/no-icon.png) |
| [Blobfuse](storage-how-to-mount-container-linux.md) | ![是](../media/icons/yes-icon.png) | ![是](../media/icons/yes-icon.png) | ![是](../media/icons/yes-icon.png) |
| [更改源](storage-blob-change-feed.md) | ![是](../media/icons/yes-icon.png) | ![否](../media/icons/no-icon.png) | ![否](../media/icons/no-icon.png) |
| [自定义域](storage-custom-domain-name.md) | ![是](../media/icons/yes-icon.png) | ![是](../media/icons/yes-icon.png)  <sup>2</sup> | ![是](../media/icons/yes-icon.png) <sup>2</sup> |
| [客户管理的帐户故障转移](../common/storage-disaster-recovery-guidance.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) | ![是](../media/icons/yes-icon.png) | ![否](../media/icons/no-icon.png) | ![否](../media/icons/no-icon.png) |
| [客户管理的密钥（加密）](../common/customer-managed-keys-overview.md?toc=/azure/storage/blobs/toc.json) | ![是](../media/icons/yes-icon.png) | ![是](../media/icons/yes-icon.png) | ![是](../media/icons/yes-icon.png) |
| [客户提供的密钥（加密）](encryption-customer-provided-keys.md) | ![是](../media/icons/yes-icon.png) | ![否](../media/icons/no-icon.png) | ![否](../media/icons/no-icon.png) |
| [数据冗余选项](../common/storage-redundancy.md?toc=/azure/storage/blobs/toc.json) | ![是](../media/icons/yes-icon.png) | ![是](../media/icons/yes-icon.png) | ![是](../media/icons/yes-icon.png) |
| [加密范围](encryption-scope-overview.md) | ![是](../media/icons/yes-icon.png) | ![否](../media/icons/no-icon.png) | ![否](../media/icons/no-icon.png) |
| [不可变存储](immutable-storage-overview.md) | ![是](../media/icons/yes-icon.png) | ![是](../media/icons/yes-icon.png)  <sup>2</sup> | ![是](../media/icons/yes-icon.png) <sup>2</sup> |
| [用于生命周期管理的上次访问时间跟踪](lifecycle-management-overview.md#move-data-based-on-last-accessed-time) | ![是](../media/icons/yes-icon.png) | ![是](../media/icons/yes-icon.png) | ![否](../media/icons/no-icon.png) |
| [生命周期管理策略（删除 blob）](./lifecycle-management-overview.md) | ![是](../media/icons/yes-icon.png) | ![是](../media/icons/yes-icon.png) | ![是](../media/icons/yes-icon.png) |
| [生命周期管理策略（分层）](./lifecycle-management-overview.md) | ![是](../media/icons/yes-icon.png) | ![是](../media/icons/yes-icon.png) | ![是](../media/icons/yes-icon.png) |
| [登录 Azure Monitor](./monitor-blob-storage.md) | ![是](../media/icons/yes-icon.png) <sup>2</sup> | ![是](../media/icons/yes-icon.png)  <sup>2</sup> | ![是](../media/icons/yes-icon.png) <sup>2</sup> |
| [Azure Monitor 中的指标](./monitor-blob-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) | ![是](../media/icons/yes-icon.png) | ![是](../media/icons/yes-icon.png) | ![是](../media/icons/yes-icon.png) |
| [块 blob 的对象复制](object-replication-overview.md) | ![是](../media/icons/yes-icon.png) | ![否](../media/icons/no-icon.png) | ![否](../media/icons/no-icon.png) |
| [页 Blob](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-page-blobs) | ![是](../media/icons/yes-icon.png) |![否](../media/icons/no-icon.png) | ![否](../media/icons/no-icon.png) |
| [块 blob 的时间点还原](point-in-time-restore-overview.md) | ![是](../media/icons/yes-icon.png) | ![否](../media/icons/no-icon.png) | ![否](../media/icons/no-icon.png) |
| [blob 的软删除](./soft-delete-blob-overview.md) | ![是](../media/icons/yes-icon.png) | ![是](../media/icons/yes-icon.png)  <sup>2</sup>   <sup>3</sup> | ![否](../media/icons/no-icon.png) |
| [容器软删除](soft-delete-container-overview.md) | ![是](../media/icons/yes-icon.png) | ![是](../media/icons/yes-icon.png) | ![否](../media/icons/no-icon.png) |
| [静态网站](storage-blob-static-website.md) | ![是](../media/icons/yes-icon.png) | ![是](../media/icons/yes-icon.png) | ![是](../media/icons/yes-icon.png) |
| [存储分析日志（经典）](../common/storage-analytics-logging.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) | ![是](../media/icons/yes-icon.png) | ![是](../media/icons/yes-icon.png) <sup>3</sup> | ![否](../media/icons/no-icon.png) |
| [存储分析指标（经典）](../common/storage-analytics-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) | ![是](../media/icons/yes-icon.png) | ![是](../media/icons/yes-icon.png) | ![是](../media/icons/yes-icon.png) |

<sup>1</sup>    Data Lake Storage Gen2 和网络文件系统 (NFS) 3.0 协议都需要已启用分层命名空间的存储帐户。

<sup>2</sup>    功能在预览级别受支持。

<sup>3</sup>    请参阅 [Azure Data Lake Storage Gen2 的已知问题](data-lake-storage-known-issues.md)。 这些问题适用于已启用分层命名空间功能的所有帐户。

## <a name="premium-block-blob-accounts"></a>高级块 blob 帐户

| 存储功能 | Blob 存储（默认支持） | Data Lake Storage Gen2 <sup>1</sup> | NFS 3.0 <sup>1</sup> |
|---------------|-------------------|---|---|
| [访问层 - 存档](storage-blob-storage-tiers.md)  | ![否](../media/icons/no-icon.png) | ![否](../media/icons/no-icon.png) | ![否](../media/icons/no-icon.png) |
| [访问层 - 冷访问层](storage-blob-storage-tiers.md) | ![否](../media/icons/no-icon.png) | ![否](../media/icons/no-icon.png) | ![否](../media/icons/no-icon.png) |
| [访问层 - 热访问层](storage-blob-storage-tiers.md) | ![否](../media/icons/no-icon.png) | ![否](../media/icons/no-icon.png) | ![否](../media/icons/no-icon.png) |
| [匿名公共访问](anonymous-read-access-configure.md) | ![是](../media/icons/yes-icon.png) | ![是](../media/icons/yes-icon.png) | ![是](../media/icons/yes-icon.png) |
| [Azure Active Directory 安全](authorize-access-azure-active-directory.md) | ![是](../media/icons/yes-icon.png) | ![是](../media/icons/yes-icon.png) | ![否](../media/icons/no-icon.png) |
| [Blob 清单](blob-inventory.md) | ![是](../media/icons/yes-icon.png) | ![是](../media/icons/yes-icon.png)  <sup>2</sup> | ![是](../media/icons/yes-icon.png) <sup>2</sup> |
| [Blob 索引标记](storage-manage-find-blobs.md) | ![否](../media/icons/no-icon.png) | ![否](../media/icons/no-icon.png) | ![否](../media/icons/no-icon.png) |
| [blob 快照](snapshots-overview.md) | ![是](../media/icons/yes-icon.png) | ![是](../media/icons/yes-icon.png)  <sup>2</sup> | ![否](../media/icons/no-icon.png) |
| [Blob 存储 API](reference.md) | ![是](../media/icons/yes-icon.png) | ![是](../media/icons/yes-icon.png) <sup>3</sup> | ![是](../media/icons/yes-icon.png) <sup>3</sup>|
| [Blob 存储 Azure CLI 命令](storage-quickstart-blobs-cli.md) | ![是](../media/icons/yes-icon.png) | ![是](../media/icons/yes-icon.png) | ![是](../media/icons/yes-icon.png) |
| [Blob 存储事件](storage-blob-event-overview.md) | ![是](../media/icons/yes-icon.png) <sup>3</sup>  | ![是](../media/icons/yes-icon.png) <sup>3</sup>| ![否](../media/icons/no-icon.png) |
| [Blob 存储 PowerShell 命令](storage-quickstart-blobs-powershell.md) | ![是](../media/icons/yes-icon.png) | ![是](../media/icons/yes-icon.png) | ![是](../media/icons/yes-icon.png) |
| [Blob 版本控制](versioning-overview.md) | ![是](../media/icons/yes-icon.png) | ![否](../media/icons/no-icon.png) | ![否](../media/icons/no-icon.png) |
| [Blobfuse](storage-how-to-mount-container-linux.md) | ![是](../media/icons/yes-icon.png) | ![是](../media/icons/yes-icon.png) | ![是](../media/icons/yes-icon.png) |
| [更改源](storage-blob-change-feed.md) | ![否](../media/icons/no-icon.png) | ![否](../media/icons/no-icon.png) | ![否](../media/icons/no-icon.png) |
| [自定义域](storage-custom-domain-name.md) | ![是](../media/icons/yes-icon.png) | ![是](../media/icons/yes-icon.png)  <sup>2</sup> | ![是](../media/icons/yes-icon.png) <sup>2</sup> |
| [客户管理的帐户故障转移](../common/storage-disaster-recovery-guidance.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) | ![否](../media/icons/no-icon.png) | ![否](../media/icons/no-icon.png) | ![否](../media/icons/no-icon.png) |
| [客户管理的密钥（加密）](../common/customer-managed-keys-overview.md?toc=/azure/storage/blobs/toc.json)  | ![是](../media/icons/yes-icon.png) | ![是](../media/icons/yes-icon.png) | ![是](../media/icons/yes-icon.png) |
| [客户提供的密钥（加密）](encryption-customer-provided-keys.md) | ![是](../media/icons/yes-icon.png) | ![否](../media/icons/no-icon.png) | ![否](../media/icons/no-icon.png) |
| [数据冗余选项](../common/storage-redundancy.md?toc=/azure/storage/blobs/toc.json) | ![是](../media/icons/yes-icon.png) | ![是](../media/icons/yes-icon.png) | ![是](../media/icons/yes-icon.png) |
| [加密范围](encryption-scope-overview.md) | ![是](../media/icons/yes-icon.png) | ![否](../media/icons/no-icon.png) | ![否](../media/icons/no-icon.png) |
| [不可变存储](immutable-storage-overview.md) | ![是](../media/icons/yes-icon.png) | ![是](../media/icons/yes-icon.png)  <sup>2</sup> | ![是](../media/icons/yes-icon.png) <sup>2</sup> |
| [用于生命周期管理的上次访问时间跟踪](lifecycle-management-overview.md#move-data-based-on-last-accessed-time) | ![是](../media/icons/yes-icon.png) | ![是](../media/icons/yes-icon.png) | ![否](../media/icons/no-icon.png) |
| [生命周期管理策略（删除 blob）](./lifecycle-management-overview.md) | ![是](../media/icons/yes-icon.png) | ![是](../media/icons/yes-icon.png) | ![是](../media/icons/yes-icon.png) |
| [生命周期管理策略（分层）](./lifecycle-management-overview.md) | ![否](../media/icons/no-icon.png) | ![否](../media/icons/no-icon.png) | ![否](../media/icons/no-icon.png) |
| [登录 Azure Monitor](./monitor-blob-storage.md) | ![是](../media/icons/yes-icon.png) <sup>2</sup> | ![是](../media/icons/yes-icon.png)  <sup>2</sup> | ![是](../media/icons/yes-icon.png) <sup>2</sup> |
| [Azure Monitor 中的指标](./monitor-blob-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) | ![是](../media/icons/yes-icon.png) | ![是](../media/icons/yes-icon.png)  <sup>2</sup> | ![是](../media/icons/yes-icon.png) <sup>2</sup> |
| [块 blob 的对象复制](object-replication-overview.md) | ![是](../media/icons/yes-icon.png) | ![否](../media/icons/no-icon.png) | ![否](../media/icons/no-icon.png) |
| [页 Blob](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-page-blobs) | ![是](../media/icons/yes-icon.png) | ![否](../media/icons/no-icon.png) | ![否](../media/icons/no-icon.png) |
| [块 blob 的时间点还原](point-in-time-restore-overview.md) | ![否](../media/icons/no-icon.png) | ![否](../media/icons/no-icon.png) | ![否](../media/icons/no-icon.png) |
| [blob 的软删除](./soft-delete-blob-overview.md)   | ![是](../media/icons/yes-icon.png) | ![是](../media/icons/yes-icon.png)  <sup>2</sup>   <sup>3</sup> | ![否](../media/icons/no-icon.png) |
| [容器软删除](soft-delete-container-overview.md) | ![是](../media/icons/yes-icon.png) | ![是](../media/icons/yes-icon.png) | ![否](../media/icons/no-icon.png) |
| [静态网站](storage-blob-static-website.md) | ![是](../media/icons/yes-icon.png) | ![是](../media/icons/yes-icon.png) | ![是](../media/icons/yes-icon.png) |
| [存储分析日志（经典）](../common/storage-analytics-logging.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) | ![是](../media/icons/yes-icon.png) | ![是](../media/icons/yes-icon.png)  <sup>2</sup>  <sup>3</sup> | ![否](../media/icons/no-icon.png)|
| [存储分析指标（经典）](../common/storage-analytics-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) | ![是](../media/icons/yes-icon.png) | ![是](../media/icons/yes-icon.png) | ![是](../media/icons/yes-icon.png) |

<sup>1</sup>    Data Lake Storage Gen2 和网络文件系统 (NFS) 3.0 协议都需要已启用分层命名空间的存储帐户。

<sup>2</sup>    功能在预览级别受支持。

<sup>3</sup>    请参阅 [Azure Data Lake Storage Gen2 的已知问题](data-lake-storage-known-issues.md)。 这些问题适用于已启用分层命名空间功能的所有帐户。

## <a name="see-also"></a>另请参阅

- [Azure Data Lake Storage Gen2 的已知问题](data-lake-storage-known-issues.md)

- [Azure Blob 存储中网络文件系统 (NFS) 3.0 协议支持的已知问题](network-file-system-protocol-known-issues.md)
