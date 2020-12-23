---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 01/30/2019
ms.author: alkohli
ms.openlocfilehash: 87c28b906b1f810857fee86ba8a94a0834e1de68
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2020
ms.locfileid: "96028099"
---
本部分介绍 Azure 存储服务的限制，以及适用于 Azure Stack Edge/Data Box Gateway 服务的 Azure 文件、Azure 块 blob 和 Azure 页 blob 的必要命名约定。 请仔细查看存储限制并遵循所有建议。

有关 Azure 存储服务限制的最新信息以及对共享、容器和文件进行命名的最佳做法，请转到：

- [命名和引用容器](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)
- [命名和引用共享](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata)
- [块 blob 和页 blob 约定](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)

> [!IMPORTANT]
> 如果有任何文件或目录超出了 Azure 存储服务限制，或者不符合 Azure 文件/Blob 命名约定，则这些文件或目录不会通过 Azure Stack Edge/Data Box Gateway 服务引入到 Azure 存储中。