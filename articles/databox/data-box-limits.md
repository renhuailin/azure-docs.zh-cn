---
title: Azure Data Box 限制 | Microsoft Docs
description: 介绍 Microsoft Azure Data Box 组件和连接的系统限制与建议大小。
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 07/16/2021
ms.author: alkohli
ms.openlocfilehash: 820daf5400d60df85973d45840bd7a4a1c553198
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114463904"
---
# <a name="azure-data-box-limits"></a>Azure Data Box 限制

部署和操作 Microsoft Azure Data Box 时请考虑这些限制。 下表介绍了 Data Box 的这些限制。

## <a name="data-box-service-limits"></a>Data Box 服务限制

[!INCLUDE [data-box-service-limits](../../includes/data-box-service-limits.md)]

## <a name="data-box-limits"></a>Data Box 限制

- Data Box 最多可存储导入和导出的 5 亿个文件。
- Data Box 在云中最多支持 512 个容器或共享。 用户共享中的顶层目录将成为云中的容器或 Azure 文件共享。 
- 由于 ReFS 元数据空间消耗，Data Box 用量容量可能小于 80 TB。
- Data Box 在 NFS 共享上一次最多支持 10 个客户端连接。

## <a name="azure-storage-limits"></a>Azure 存储限制

[!INCLUDE [data-box-storage-limits](../../includes/data-box-storage-limits.md)]

## <a name="data-upload-caveats"></a>数据上传注意事项


### <a name="for-import-order"></a>导入命令

Data Box 导入命令的注意事项包括：

[!INCLUDE [data-box-data-upload-caveats](../../includes/data-box-data-upload-caveats.md)]

### <a name="for-export-order"></a>导出命令

Data Box 导出命令的注意事项包括：

- Data Box 是一种基于 Windows 的设备，不支持区分大小写的文件名。 例如，Azure 中可能有两个不同的文件，其名称大小写不同。 请勿使用 Data Box 来导出这些文件，因为文件将在设备上被覆盖。
- 如果输入文件或标记中存在引用相同数据的重复标记，则 Data Box 导出可能会跳过或覆盖文件。 Azure 门户显示的文件数和数据大小可能不同于设备上数据的实际大小。 
- Data Box 通过 SMB 将数据导出到基于 Windows 的系统，并受文件和文件夹的 SMB 限制的约束。 名称不受支持的文件和文件夹不会导出。
- 从前缀到容器的映射为 1:1。
- 文件名的大小最大为 1024 个字符。 超过此长度的文件名不会被导出。 
- （在命令创建过程中上传的）*Xml* 文件中的重复前缀会导出 。 重复的前缀不会被省略。
- 页 blob 和容器名称区分大小写。 如果大小写不匹配，将找不到 blob 和/或容器。
 

## <a name="azure-storage-account-size-limits"></a>Azure 存储帐户大小限制

[!INCLUDE [data-box-storage-account-size-limits](../../includes/data-box-storage-account-size-limits.md)]

## <a name="azure-object-size-limits"></a>Azure 对象大小限制

[!INCLUDE [data-box-object-size-limits](../../includes/data-box-object-size-limits.md)]

## <a name="azure-block-blob-page-blob-and-file-naming-conventions"></a>Azure 块 blob、页 blob 和文件命名约定

[!INCLUDE [data-box-naming-conventions](../../includes/data-box-naming-conventions.md)]
