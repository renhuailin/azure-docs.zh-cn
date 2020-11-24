---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 06/24/2020
ms.author: alkohli
ms.openlocfilehash: 2868e5a53686cfa94dc206c1aab65fe866d19b6d
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/24/2020
ms.locfileid: "95559752"
---
下面是对复制到存储帐户的数据的大小限制。 请确保上传的数据符合这些限制。 有关这些限制的最新信息，请参阅 [Blob 存储的可伸缩性和性能目标](../articles/storage/blobs/scalability-targets.md)和 [Azure 文件存储的可伸缩性和性能目标](../articles/storage/files/storage-files-scale-targets.md)。

| 复制到 Azure 存储帐户的数据的大小                      | 默认限制          |
|---------------------------------------------------------------------|------------------------|
| 块 Blob 和页 blob                                            | 最大限制与为 [Azure 订阅定义的存储限制](../articles/azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits) 相同，并且包括来自所有源的数据，包括 Data Box。 |
| Azure 文件                                                          | Data Box 支持 (100TiB) 的大型文件共享（如果在创建 Data Box 顺序之前启用）。 <br> 如果在创建订单之前未启用，则支持的文件共享最大大小为 5 TiB。 <br> 目前尚不支持高级文件共享。<br> StorageAccount_AzureFiles 下的所有文件夹都须遵循此限制  。 <br> 有关详细信息，请参阅 [启用和创建大型文件共享](../articles/storage/files/storage-files-how-to-create-large-file-share.md)      |