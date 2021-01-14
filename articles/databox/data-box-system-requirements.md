---
title: Microsoft Azure Data Box 系统要求 | Microsoft Docs
description: 了解 Azure Data Box 和连接到 Data Box 的客户端的重要系统要求。
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 12/23/2020
ms.author: alkohli
ms.openlocfilehash: 0190e295c2ab206242ab8a44a09ffb42746d75bd
ms.sourcegitcommit: f5b8410738bee1381407786fcb9d3d3ab838d813
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/14/2021
ms.locfileid: "98209538"
---
# <a name="azure-data-box-system-requirements"></a>Azure Data Box 系统要求

本文介绍了 Microsoft Azure Data Box 和连接到 Data Box 的客户端的重要系统要求。 建议你在部署 Data Box 之前仔细查看此信息，然后在部署和操作期间需要时进行参考。

系统需求包括：

* **软件要求：** 对于连接到 Data Box 的主机，为本地 web UI 介绍了支持的操作系统、文件传输协议、存储帐户、存储类型和浏览器。
* **网络要求：** 对于 Data Box，描述了用于 Data Box 最佳操作的网络连接和端口的要求。


## <a name="software-requirements"></a>软件要求

软件要求包括本地 web UI 支持的操作系统、文件传输协议、存储帐户、存储类型和浏览器。

### <a name="supported-operating-systems-for-clients"></a>客户端支持的操作系统

[!INCLUDE [data-box-supported-os-clients](../../includes/data-box-supported-os-clients.md)]


### <a name="supported-file-transfer-protocols-for-clients"></a>客户端支持的文件传输协议

[!INCLUDE [data-box-supported-file-systems-clients](../../includes/data-box-supported-file-systems-clients.md)]

> [!IMPORTANT] 
> 对于出口订单，不支持通过 REST 连接到 Data Box 共享。 

### <a name="supported-storage-accounts"></a>支持的存储帐户

[!INCLUDE [data-box-supported-storage-accounts](../../includes/data-box-supported-storage-accounts.md)]

### <a name="supported-storage-types"></a>受支持的存储类型

[!INCLUDE [data-box-supported-storage-types](../../includes/data-box-supported-storage-types.md)]

### <a name="supported-web-browsers"></a>受支持的 Web 浏览器

[!INCLUDE [data-box-supported-web-browsers](../../includes/data-box-supported-web-browsers.md)]

## <a name="networking-requirements"></a>网络要求

数据中心需要有高速网络。 我们强烈建议你至少具有 1 10-GbE 连接。 如果 10 GbE 连接不可用，则可以使用 1-GbE 数据链接来复制数据，但复制速度会受到影响。

### <a name="port-requirements"></a>端口要求

下表列出了需要在防火墙中打开以允许 SMB 或 NFS 流量的端口。 在此表中 *，* (*入站*) 是指传入客户端请求访问设备的方向。 *Out* (或 *出站*) 是指 Data Box 设备在部署之外的外部发送数据的方向。 例如，数据可能对 Internet 出站。

[!INCLUDE [data-box-port-requirements](../../includes/data-box-port-requirements.md)]


## <a name="next-steps"></a>后续步骤

* [部署 Azure Data Box](data-box-deploy-ordered.md)
