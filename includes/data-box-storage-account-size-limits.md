---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 08/02/2021
ms.author: alkohli
ms.openlocfilehash: 1f026d0a8d038e532b9b0ec070a4849ebc8e8530
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121802374"
---
下面是对复制到存储帐户的数据的大小限制。 请确保上传的数据符合这些限制。 有关这些限制的最新信息，请参阅 [Blob 存储的可伸缩性和性能目标](../articles/storage/blobs/scalability-targets.md)和 [Azure 文件存储的可伸缩性和性能目标](../articles/storage/files/storage-files-scale-targets.md)。 

| 复制到 Azure 存储帐户的数据的大小                      | 默认限制          |
|---------------------------------------------------------------------|------------------------|
| 块 Blob 和页 blob                                            | 最大限制与[为 Azure 订阅规定的存储限制](../articles/azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits)相同，并且包含来自所有源（包括 Data Box）的数据。   |
| Azure 文件                                                          | <ul><li>如果在创建 Data Box 订单之前启用 Data Box，则 Data Box 将支持大型文件共享 (100 TiB)。<ul><li>如果在创建订单之前未启用大型文件共享，则支持的最大文件共享大小为 5 TiB。</li><li>为了提高数据上传期间的性能，建议[在存储帐户上启用大型文件共享并将共享容量增大到 100 TiB](../articles/storage/files/storage-how-to-create-file-share.md#enable-large-files-shares-on-an-existing-account)。 只有具有本地冗余存储 (LRS) 的存储帐户才支持大型文件共享。</li></ul><li>Data Box 支持 Azure 高级文件共享，允许存储帐户中的所有共享总共有 100 TiB。<ul><li>由于复制日志和审核日志占用了一定的空间，因此最大可用容量略低。 为复制日志和审核日志至少各保留 100 GiB。 有关详细信息，请参阅 [Azure Data Box 的审核日志 Azure Data Box Heavy](../articles/databox/data-box-audit-logs.md)。</li><li>“StorageAccount_AzFile”下的所有文件夹都须遵循此限制。 有关详细信息，请参阅[创建 Azure 文件共享](../articles/storage/files/storage-how-to-create-file-share.md)。</li></ul></li></ul> |
