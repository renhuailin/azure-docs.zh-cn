---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 08/02/2021
ms.author: alkohli
ms.openlocfilehash: 092484b09af008770260c64cd6b7e0ea15265811
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121801344"
---
下面是 Data Box 设备支持的存储帐户和存储类型的列表。 有关所有类型存储帐户的完整功能的完整列表，请参阅[存储帐户类型](../articles/storage/common/storage-account-overview.md#types-of-storage-accounts)。

对于导入顺序，下表显示了支持的存储帐户。

| **存储帐户/支持的存储类型** | **块 blob** |**页 blob** _ |_ *Azure 文件** |**备注**|
| --- | --- | -- | -- | -- |
| 经典标准 | Y | Y | Y |
| 常规用途 v1 标准  | Y | Y | Y | 支持热和冷。|
| 常规用途 v1 高级  |  | Y| | |
| 常规用途 v2 标准  | Y | Y | Y | 支持热和冷。|
| 常规用途 v2 高级  |  |Y | | |
| Azure Premium FileStorage |  |  | Y |  |  
| Blob 存储标准 |Y | | |支持热和冷。 |

\* - 上传到页 blob 的数据必须是 512 字节对齐，例如 VHD。

对于导出顺序，下表显示了支持的存储帐户。

| **存储帐户/支持的存储类型** | **块 blob** |**页 blob** _ |_ *Azure 文件** |**支持的访问层**|
| --- | --- | -- | -- | -- |
| 经典标准 | Y | Y | Y | |
| 常规用途 v1 标准  | Y | Y | Y | 热、冷|
| 常规用途 v1 高级  |  | Y| | |
| 常规用途 v2 标准  | Y | Y | Y | 热、冷|
| 常规用途 v2 高级  |  |Y | | |
| Azure Premium FileStorage |  |  | Y |  |
| Blob 存储标准 |Y | | |热、冷 |
| 块 Blob 存储高级 |Y | | |热、冷 |
| 页 Blob 存储高级 | |Y | | |

> [!IMPORTANT]
> - 对于常规用途的帐户，Data Box 不支持队列、表和磁盘存储类型的导入顺序。 对于导出顺序，Data Box 不支持用于常规用途帐户的队列、表、磁盘和 Azure Data Lake Gen 2 存储类型。
> - Data Box 不支持为 Blob 存储和块 Blob 存储帐户追加 Blob。
> - Azure Blob 存储中的网络文件系统 (NFS) 3.0 协议支持不受 Data Box 支持。
> - 上传到页 blob 的数据必须是 512 字节对齐，例如 VHD。
> - 最多可导出 80 TB。
> - 不会导出文件历史记录和 blob 快照。
> - 不支持导出存档 Blob。 在导出之前，将存档层中的 blob 解除冻结。 有关详细信息，请参阅[将存档 blob 解除冻结到联机层](../articles/storage/blobs/storage-blob-rehydration.md#rehydrate-an-archived-blob-to-an-online-tier)。
