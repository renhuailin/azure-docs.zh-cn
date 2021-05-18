---
title: 提供了视频索引器的区域 - Azure
titleSuffix: Azure Media Services
description: 本文讨论 Azure 媒体服务视频索引器可用的 Azure 区域。
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 09/14/2020
ms.author: juliako
ms.openlocfilehash: 6ebdb22f50efbefc695f9752c6e6fc333571828c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "90530925"
---
# <a name="azure-regions-in-which-video-indexer-exists"></a>存在视频索引器的 Azure 区域

视频索引器 API 包含一个 **位置** 参数，应当将其设置为要将调用路由到的 Azure 区域。 这必须是一个[提供了视频索引器的 Azure 区域](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services&regions=all)。

## <a name="locations"></a>位置

必须提供 Azure 区域代码名称作为 `location` 参数的值。 如果在预览版模式下使用视频索引器，则应当使用 `"trial"` 作为值。 `trial` 为参数 `location` 的默认值。 否则，若要获取你的帐户所在的并且应当将你的调用路由到的 Azure 区域的代码名称，可以使用 Azure 门户或运行 [Azure CLI](/cli/azure) 命令。

### <a name="azure-portal"></a>Azure 门户

1. 登录到[视频索引器](https://www.videoindexer.ai/)网站。
1. 选择页面右上角的“用户帐户”。
1. 在右上角找到帐户位置。  

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/location/location1.png" alt-text="位置":::
    
###  <a name="cli-command"></a>CLI 命令

```azurecli-interactive
az account list-locations
```

运行上面所示的命令行后，会得到所有 Azure 区域的列表。 导航到具有你要查找的 *displayName* 的 Azure 区域，并将其 *name* 值用于 **位置** 参数。

例如，对于 Azure 区域“美国西部 2”（如下所示），你将“westus2”用于 **位置** 参数。

```json
   {
      "displayName": "West US 2",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/locations/westus2",
      "latitude": "47.233",
      "longitude": "-119.852",
      "name": "westus2",
      "subscriptionId": null
    }
```

## <a name="next-steps"></a>后续步骤

- [使用 API 自定义语言模型](customize-language-model-with-api.md)
- [使用 API 自定义品牌模型](customize-brands-model-with-api.md)
- [使用 API 自定义人员模型](customize-person-model-with-api.md)
