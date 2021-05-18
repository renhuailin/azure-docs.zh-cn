---
title: Microsoft Azure Data Box 系统要求 | Microsoft Docs
description: 了解有关 AzureData Box 以及连接到 Data Box 的客户端的重要系统要求。
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 85a0ce20f1d8e5a7e943efc088c19a8ad1912fc1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "101706022"
---
# <a name="azure-data-box-system-requirements"></a>Azure Data Box 系统要求

本文介绍 Microsoft Azure Data Box 以及连接到 Data Box 的客户端的重要系统要求。 建议在部署 Data Box 之前仔细查看信息，然后在部署和操作期间根据需要进行参考。

系统需求包括：

* 软件要求：对于连接到 Data Box 的主机，介绍了支持的操作系统、文件传输协议、存储帐户、存储类型以及用于本地 Web UI 的浏览器。
* 网络要求： 对于 Data Box，介绍了要让 Data Box 最佳运行，网络连接和端口需要满足的要求。


## <a name="software-requirements"></a>软件要求

软件要求包括支持的操作系统、文件传输协议、存储帐户、存储类型以及用于本地 Web UI 的浏览器。

### <a name="supported-operating-systems-for-clients"></a>客户端支持的操作系统

[!INCLUDE [data-box-supported-os-clients](../../includes/data-box-supported-os-clients.md)]


### <a name="supported-file-transfer-protocols-for-clients"></a>客户端支持的文件传输协议

[!INCLUDE [data-box-supported-file-systems-clients](../../includes/data-box-supported-file-systems-clients.md)]

> [!IMPORTANT] 
> 对于导出命令，不支持通过 REST 连接到 Data Box 共享。 

### <a name="supported-storage-accounts"></a>支持的存储帐户

[!INCLUDE [data-box-supported-storage-accounts](../../includes/data-box-supported-storage-accounts.md)]

### <a name="supported-storage-types"></a>受支持的存储类型

[!INCLUDE [data-box-supported-storage-types](../../includes/data-box-supported-storage-types.md)]

### <a name="supported-web-browsers"></a>受支持的 Web 浏览器

[!INCLUDE [data-box-supported-web-browsers](../../includes/data-box-supported-web-browsers.md)]

## <a name="networking-requirements"></a>网络要求

数据中心需要有高速网络。 强烈建议你至少建立一个 10-GbE 连接。 如果 10-GbE 连接不可用，可以使用 1-GbE 数据链路来复制数据，但复制速度会受影响。

### <a name="port-requirements"></a>端口要求

下表列出了需要在防火墙中打开以允许 SMB 或 NFS 流量的端口。 在此表中，入（入站）表示传入客户端请求访问设备的方向。  “出”（出站）表示 Data Box 设备从外部（超出部署范围）发送数据的方向。  例如，数据可能出站到 Internet。

[!INCLUDE [data-box-port-requirements](../../includes/data-box-port-requirements.md)]


## <a name="next-steps"></a>后续步骤

* [部署 Azure Data Box](data-box-deploy-ordered.md)
