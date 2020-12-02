---
title: Azure Stack Edge Pro R 限制 |Microsoft Docs
description: 描述 Azure Stack Edge Pro R 的系统限制和建议大小。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 10/13/2020
ms.author: alkohli
ms.openlocfilehash: dfff3bdd716c54a6c83dbc9fec63c794c1fba85b
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2020
ms.locfileid: "96466324"
---
# <a name="azure-stack-edge-pro-r-limits"></a>Azure Stack Edge Pro R 限制

部署和操作 Azure Stack Edge Pro R 解决方案时，请考虑这些限制。

## <a name="azure-stack-edge-pro-r-service-limits"></a>Azure Stack Edge Pro R 服务限制

[!INCLUDE [azure-stack-edge-gateway-service-limits](../../includes/azure-stack-edge-gateway-service-limits.md)]

## <a name="azure-stack-edge-pro-r-device-limits"></a>Azure Stack Edge Pro R 设备限制

下表描述了 Azure Stack Edge Pro R 设备的限制。

| 说明 | 值 |
|---|---|
|不是。 设备的文件数 |1 亿 |
|不是。 每个容器的共享数 |1 |
|最大值 每个设备的共享终结点和 REST 终结点| 24 |
|最大值 每台设备的分层存储帐户| 24|
|写入到共享的最大文件大小| 5 TB |
|每个设备的最大资源组数| 800 |

## <a name="azure-storage-limits"></a>Azure 存储限制

[!INCLUDE [azure-stack-edge-gateway-storage-limits](../../includes/azure-stack-edge-gateway-storage-limits.md)]

## <a name="data-upload-caveats"></a>数据上传注意事项

[!INCLUDE [azure-stack-edge-gateway-storage-data-upload-caveats](../../includes/azure-stack-edge-gateway-storage-data-upload-caveats.md)]

## <a name="azure-storage-account-size-and-object-size-limits"></a>Azure 存储帐户大小和对象大小限制

[!INCLUDE [azure-stack-edge-gateway-storage-acct-limits](../../includes/azure-stack-edge-gateway-storage-acct-limits.md)]


## <a name="azure-object-size-limits"></a>Azure 对象大小限制

[!INCLUDE [azure-stack-edge-gateway-storage-object-limits](../../includes/azure-stack-edge-gateway-storage-object-limits.md)]

## <a name="next-steps"></a>后续步骤

- [准备部署 Azure Stack Edge Pro R](azure-stack-edge-pro-r-deploy-prep.md)
