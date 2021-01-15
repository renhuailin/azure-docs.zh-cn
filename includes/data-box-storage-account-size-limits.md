---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 01/12/2021
ms.author: alkohli
ms.openlocfilehash: 6cabac4c59b09d146c1e42762402043622edb60e
ms.sourcegitcommit: f5b8410738bee1381407786fcb9d3d3ab838d813
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/14/2021
ms.locfileid: "98225500"
---
下面是复制到存储帐户中的数据大小限制。 请确保上传的数据符合这些限制。 有关这些限制的最新信息，请参阅 [Blob 存储的可伸缩性和性能目标](../articles/storage/blobs/scalability-targets.md)和 [Azure 文件存储的可伸缩性和性能目标](../articles/storage/files/storage-files-scale-targets.md)。

| 复制到 Azure 存储帐户的数据的大小                      | 默认限制          |
|---------------------------------------------------------------------|------------------------|
| 块 Blob 和页 blob                                            | 最大限制与为 [Azure 订阅定义的存储限制](../articles/azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits) 相同，并且包括来自所有源的数据，包括 Data Box。   |
| Azure 文件                                                          | Data Box 支持大型文件共享 (100 TiB) 如果在创建 Data Box 顺序之前启用了此功能。 <br> 如果在创建订单之前未启用，则支持的文件共享最大大小为 5 TiB。 <br> Data Box 支持 Azure 高级文件共享，该共享允许存储帐户中的所有共享共100个 TiB。 <br> 由于复制日志和审核日志使用的空间，最大可用容量略少。 为复制日志和审核日志保留每个最低 100 GiB。 有关详细信息，请参阅 [Azure Data Box 的审核日志 Azure Data Box Heavy](../articles/databox/data-box-audit-logs.md)。 <br> StorageAccount_AzureFiles 下的所有文件夹都须遵循此限制  。 <br> 有关详细信息，请参阅 [启用和创建大型文件共享](../articles/storage/files/storage-files-how-to-create-large-file-share.md)      |
