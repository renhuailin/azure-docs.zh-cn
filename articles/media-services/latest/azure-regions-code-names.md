---
title: Azure 媒体服务 v3 的云和区域
description: 本文讨论了用于终结点的 URL 和区域的代码。
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: reference
ms.date: 10/28/2020
ms.author: inhenkel
ms.openlocfilehash: 74f30ebb766ea34603c0a30455837c47222967c0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "98954321"
---
# <a name="regional-code-names-and-endpoints"></a>区域代码名称和终结点

### <a name="region-code-name"></a>区域代码名

在命令或请求中使用 location 参数时，需要提供区域代码名称作为 location 值。 若要获取你的帐户所在的并且应当将你的调用路由到的区域的代码名称，可以在 Azure CLI 中运行以下命令行。

```azurecli-interactive
az account list-locations
```

运行上面所示的命令行后，会得到所有 Azure 区域的列表。 导航到具有你要查找的 *displayName* 的 Azure 区域，并将其 *name* 值用于 **位置** 参数。

例如，对于 Azure 区域“美国西部 2”（如下所示），你将“westus2”用于 **位置** 参数。

```json
   {
      "displayName": "West US 2",
      "id": "/subscriptions/00000000-23da-4fce-b59c-f6fb9513eeeb/locations/westus2",
      "latitude": "47.233",
      "longitude": "-119.852",
      "name": "westus2",
      "subscriptionId": null
    }
```

## <a name="endpoints"></a>终结点  

从不同的区域 Azure 云连接到媒体服务帐户时，应记住以下终结点。

### <a name="global-azure"></a>全球 Azure

| 服务 | 终结点 |
| ------- | -------- |
| Azure Resource Manager |  `https://management.azure.com/` |
| 身份验证 | `https://login.microsoftonline.com/` |
| 令牌受众 | `https://management.core.windows.net/` |

### <a name="azure-government"></a>Azure Government

| 服务 | 终结点 |
| ------- | -------- |
| Azure Resource Manager |  `https://management.usgovcloudapi.net/` |
| 身份验证 | `https://login.microsoftonline.us/` |
| 令牌受众 | `https://management.core.usgovcloudapi.net/` |

[!INCLUDE [Widevine is not available in the GovCloud region.](./includes/widevine-not-available-govcloud.md)]

### <a name="azure-germany"></a>Azure 德国

> [!NOTE]
> Azure 德国终结点仅适用于德国的主权云。

| 服务 | 终结点 |
| ------- | -------- |
| Azure Resource Manager | `https://management.cloudapi.de/` |
| 身份验证 | `https://login.microsoftonline.de/` |
| 令牌受众 | `https://management.core.cloudapi.de/`|

### <a name="azure-china-21vianet"></a>Azure 中国世纪互联

| 服务 | 终结点 |
| ------- | -------- |
| Azure Resource Manager | `https://management.chinacloudapi.cn/` |
| 身份验证 | `https://login.chinacloudapi.cn/` |
| 令牌受众 |  `https://management.core.chinacloudapi.cn/` |

## <a name="see-also"></a>另请参阅

* [Azure 区域](https://azure.microsoft.com/global-infrastructure/regions/)
* [区域代码名称和终结点](azure-regions-code-names.md)
* [Azure 地域](https://azure.microsoft.com/global-infrastructure/geographies/)
* [Azure 位置](https://azure.microsoft.com/global-infrastructure/locations/)

## <a name="next-steps"></a>后续步骤

[媒体服务 v3 概述](media-services-overview.md)
