---
title: Azure Stack Edge Pro 限制 | Microsoft Docs
description: 了解部署和操作 Azure Stack Edge Pro 时的限制和建议大小，包括服务限制、设备限制和存储限制。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 10/12/2020
ms.author: alkohli
ms.openlocfilehash: f1bb5662b48765c08ec58d1f7fa9d341de4937bc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "91992761"
---
# <a name="azure-stack-edge-pro-limits"></a>Azure Stack Edge Pro 限制

在部署和操作 Microsoft Azure Stack Edge Pro 解决方案时请考虑这些限制。 

## <a name="azure-stack-edge-service-limits"></a>Azure Stack Edge 服务限制

[!INCLUDE [data-box-edge-gateway-service-limits](../../includes/data-box-edge-gateway-service-limits.md)]

## <a name="azure-stack-edge-device-limits"></a>Azure Stack Edge 设备限制

下表介绍了 Azure Stack Edge Pro 设备的限制。 

下表介绍了 Azure Stack Edge 设备的限制。

| 说明 | 值 |
|---|---|
|否。 设备的文件数 |1 亿 |
|否。 每个容器的共享数 |1 |
|每个设备的最大 共享终结点和 REST 终结点数| 24 |
|每个设备的最大 分层存储帐户数| 24|
|写入到共享的最大文件大小| 5 TB |
|每个设备的最大资源组数| 800 |

## <a name="azure-storage-limits"></a>Azure 存储限制

[!INCLUDE [data-box-edge-gateway-storage-limits](../../includes/data-box-edge-gateway-storage-limits.md)]

## <a name="data-upload-caveats"></a>数据上传注意事项

[!INCLUDE [data-box-edge-gateway-storage-data-upload-caveats](../../includes/data-box-edge-gateway-storage-data-upload-caveats.md)]

## <a name="azure-storage-account-size-and-object-size-limits"></a>Azure 存储帐户大小和对象大小限制

[!INCLUDE [data-box-edge-gateway-storage-acct-limits](../../includes/data-box-edge-gateway-storage-acct-limits.md)]


## <a name="azure-object-size-limits"></a>Azure 对象大小限制

[!INCLUDE [data-box-edge-gateway-storage-object-limits](../../includes/data-box-edge-gateway-storage-object-limits.md)]

## <a name="next-steps"></a>后续步骤

- [准备部署 Azure Stack Edge Pro](azure-stack-edge-deploy-prep.md)
