---
title: Azure Stack Edge Mini R 限制 | Microsoft Docs
description: 介绍了Azure Stack Edge Mini R 的系统限制与建议的大小。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 10/13/2020
ms.author: alkohli
ms.openlocfilehash: 6f01d62d1d11f5ff90661482ffd2db112657eee5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "96466389"
---
# <a name="azure-stack-edge-mini-r-limits"></a>Azure Stack Edge Mini R 限制


在部署和操作 Azure Stack Edge Mini R 解决方案时请考虑这些限制。

## <a name="azure-stack-edge-service-limits"></a>Azure Stack Edge 服务限制

[!INCLUDE [azure-stack-edge-gateway-service-limits](../../includes/azure-stack-edge-gateway-service-limits.md)]

## <a name="azure-stack-edge-mini-r-device-limits"></a>Azure Stack Edge Mini R 设备限制

下表介绍了 Azure Stack Edge Mini R 设备的限制。

| 说明 | 限制|
|---|---:|
|不知道。 设备的文件数 | 1 亿 <!--check with devs-->|
|不知道。 每个容器的共享数 | 1|
|每个设备的最大 共享终结点和 REST 终结点数| 24 |
|每个设备的最大 分层存储帐户数| 24|
|写入到共享的最大文件大小| 500 GB|
|每个设备的最大资源组数| 800|

## <a name="azure-storage-limits"></a>Azure 存储限制

[!INCLUDE [azure-stack-edge-gateway-storage-limits](../../includes/azure-stack-edge-gateway-storage-limits.md)]

## <a name="data-upload-caveats"></a>数据上传注意事项

[!INCLUDE [azure-stack-edge-gateway-storage-data-upload-caveats](../../includes/azure-stack-edge-gateway-storage-data-upload-caveats.md)]

## <a name="azure-storage-account-size-and-object-size-limits"></a>Azure 存储帐户大小和对象大小限制

[!INCLUDE [azure-stack-edge-gateway-storage-acct-limits](../../includes/azure-stack-edge-gateway-storage-acct-limits.md)]

## <a name="azure-object-size-limits"></a>Azure 对象大小限制

[!INCLUDE [azure-stack-edge-gateway-storage-object-limits](../../includes/azure-stack-edge-gateway-storage-object-limits.md)]

## <a name="next-steps"></a>后续步骤

- [准备部署 Azure Stack Edge](azure-stack-edge-gpu-deploy-prep.md)
