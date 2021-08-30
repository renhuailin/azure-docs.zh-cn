---
title: 推出了 Azure 视频分析器媒体版（以前称为视频索引器）的区域
titleSuffix: Azure Video Analyzer for Media
description: 本文讨论推出了 Azure 视频分析器媒体版（以前称为视频索引器）的 Azure 区域。
services: azure-video-analyzer
author: Juliako
manager: femila
ms.topic: article
ms.subservice: azure-video-analyzer-media
ms.date: 09/14/2020
ms.author: juliako
ms.openlocfilehash: 1db75ea3ba67d4e8f0551495274ec3fff38e29b4
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/15/2021
ms.locfileid: "112119184"
---
# <a name="azure-regions-in-which-video-analyzer-for-media-exists"></a>推出了视频分析器媒体版的 Azure 区域

Azure 视频分析器媒体版（以前称为视频索引器）API 包含一个位置参数，应当将其设置为要将调用路由到的 Azure 区域。 这必须是一个[推出了视频分析器媒体版的 Azure 区域](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services&regions=all)。

## <a name="locations"></a>位置

必须提供 Azure 区域代码名称作为 `location` 参数的值。 如果在预览版模式下使用视频分析器媒体版，则应当使用 `"trial"` 作为值。 `trial` 为参数 `location` 的默认值。 否则，若要获取你的帐户所在的并且应当将你的调用路由到的 Azure 区域的代码名称，可以使用 Azure 门户或运行 [Azure CLI](/cli/azure) 命令。

### <a name="azure-portal"></a>Azure 门户

1. 登录到[视频分析器媒体版](https://www.videoindexer.ai/)网站。
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
