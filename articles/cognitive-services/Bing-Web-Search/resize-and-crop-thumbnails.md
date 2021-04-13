---
title: 重设图像缩略图大小并进行剪裁 - 必应 Web 搜索 API
titleSuffix: Azure Cognitive Services
description: 来自必应搜索 API 的一些答案包括指向由必应提供的略缩图的 URL，你可以重设略缩图的大小并进行裁剪，并且可以包含查询参数。
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: 05A08B01-89FF-4781-AFE7-08DA92F25047
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 07/08/2019
ms.author: aahi
ms.openlocfilehash: a85c5b2333418367742678a529b69c95164eda53
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "96350477"
---
# <a name="resize-and-crop-thumbnail-images"></a>调整和裁剪缩略图图像

> [!WARNING]
> 必应搜索 API 将从认知服务迁移到必应搜索服务。 从 2020 年 10 月 30 日开始，必应搜索的任何新实例都需按照[此处](/bing/search-apis/bing-web-search/create-bing-search-service-resource)所述的过程进行预配。
> 使用认知服务进行预配的必应搜索 API 将在未来三年或在企业协议结束前（以先发生者为准）得到支持。
> 有关迁移说明，请参阅[必应搜索服务](/bing/search-apis/bing-web-search/create-bing-search-service-resource)。

来自必应搜索 API 的一些答案包括指向由必应提供的略缩图的 URL，你可以重设略缩图的大小并进行裁剪，并且可以包含查询参数。 例如：

`https://<host>/th?id=AMMS_92772df988...&w=110&h=73&rs=1&qlt=80&cdv=1&pid=16.1`

如果要显示这些略缩图的子集，请提供查看剩余图像的选项。

> [!NOTE]
> 按照必应搜索 API [使用和显示要求](use-display-requirements.md)，请确保裁剪和重设略缩图大小将提供尊重第三方权利的搜索场景。

## <a name="resize-a-thumbnail"></a>重设略缩图大小 

若要重设略缩图大小，必应建议仅在略缩图的 URL 中指定 `w`（宽度）或 `h`（高度）查询参数。 仅指定高度和宽度可让必应维持图像的原始比例。 以像素为单位指定宽和高。 

例如，如果原始略缩图大小为 480x620：

`https://<host>/th?id=JN.5l3yzwy%2f%2fHj59U6XhssIQ&pid=Api&w=480&h=620`

并且你希望减小其大小，请将 `w` 参数设置为新的值（例如 `336`）并删除 `h` 参数：

`https://<host>/th?id=JN.5l3yzwy%2f%2fHj59U6XhssIQ&pid=Api&w=336`

如果仅指定略缩图的高度或宽度，则将保持图像的原始纵横比。 如果同时指定这两个参数，则不会保留纵横比，必应将向图像的边界添加白色的填充。

例如，如果将 480x359 的图像重设为 200x200 而不进行裁剪，则全宽尺寸将包含图像，但对于高度来说，将在图像顶部和底部包含 25 像素的白色填充。 如果图像大小为 359x480，则左侧和右侧边界将包含白色填充。 如果裁剪图像，不会添加白色填充内容。  

下图显示缩略图的原始大小 (480x300)。  
  
![原始风景图](./media/resize-crop/bing-resize-crop-landscape.png)  
  
下图显示调整为 200x200 大小后的图像。 图像的纵横比得到保留，顶部和底部边界将使用白色填充（此处包含黑色边界以显示填充）。  
  
![调整大小后的风景图](./media/resize-crop/bing-resize-crop-landscape-resized.png)  

如果指定大于图像原始宽度和高度的尺寸，必应将在左侧和顶部边界添加白色填充。  

## <a name="request-different-thumbnail-sizes"></a>请求不同的略缩图大小

若要请求不同的略缩图像大小，请从略缩图的 URL 中删除所有查询参数（`id` 和 `pid` 参数除外）。 然后，添加具有所需图像大小（以像素为单位）的 `&w`（宽度）或 `&h`（高度）查询参数，但不能同时添加两者。 必应将维持图像的原始纵横比。 

若要将上述 URL 指定的图像的宽度增加到 165 像素，则需要使用以下 URL：

`https://<host>/th?id=AMMS_92772df988...&w=165&pid=16.1`

如果请求大于图像原始大小的图像，必应会按需要在图像周围添加白色的填充。 例如，如果图像的原始大小为 474x316，而你将 `&w` 设置为 500，必应将返回大小为 500x333 的图像。 此图像沿顶部和底部边缘有 8.5 像素的白色填充，并且左侧和右侧边缘有 13 像素的填充。

若要在请求的大小大于图像的原始大小的情况下阻止必应添加白色填充，请将 `&p` 查询参数设置为 0。 例如，如果将 `&p=0` 参数包含在上述 URL 中，必应将返回 474x316 的图像，而不是 500x333 的图像：

`https://<host>/th?id=AMMS_92772df988...&w=500&p=0&pid=16.1`

如果同时指定 `&w` 和 `&h` 查询参数，必应将保持图像的纵横比并按照需要添加白色填充。 例如，如果图像的原始大小为 474x316，而你将宽度和高度参数设置为 200x200 (`&w=200&h=200`)，必应将返回在顶部和底部包含 33 像素白色填充的图像。 如果将 `&p` 查询参数包含在内，必应将返回 200x134 的图像。

## <a name="crop-a-thumbnail"></a>裁剪略缩图 

若要裁剪略缩图，请将 `c`（裁剪）查询参数包含在内。 可以使用下列值：
  
- `4` &mdash; 盲比例  
- `7` &mdash; 智能比例  

### <a name="smart-ratio-cropping"></a>智能比例裁剪

如果请求智能比例裁剪（方法是将 `c` 参数设置为 `7`），必应将从感兴趣区域中心向外裁剪图像，同时保持图像的纵横比。 感兴趣区域是图像中被必应确定为包含最重要内容的区域。 下图是感兴趣区域的示例。  
  
![感兴趣区域](./media/resize-crop/bing-resize-crop-regionofinterest.png)

如果重设图像大小并请求智能比例裁剪，必应会将图像减小到所请求的大小，同时保持纵横比。 然后，必应会根据重设的尺寸裁剪图像。 例如，如果重设的宽度小于或等于高度，必应将在感兴趣区域的中心左侧和右侧裁剪图像。 否则，必应将在感兴趣区域的中心顶部和底部裁剪图像。  
  
 
下图显示使用“智能比例”裁剪将图像缩成 200x200 大小的情形。 由于必应从左上角测量图像，图像的底部部分将被裁剪。 
  
![裁剪成 200x200 大小的风景图](./media/resize-crop/bing-resize-crop-landscape200x200c7.png) 
  
下图显示使用“智能比例”裁剪将图像缩成 200x100 大小的情形。 由于必应从左上角测量图像，图像的底部部分将被裁剪。 
   
![裁剪成 200x100 大小的风景图](./media/resize-crop/bing-resize-crop-landscape200x100c7.png)
  
下图显示使用“智能比例”裁剪将图像缩成 100x200 大小的情形。 由于必应从中间测量图像，图像的左侧和右侧部分将被裁剪。
  
![裁剪成 100x200 大小的风景图](./media/resize-crop/bing-resize-crop-landscape100x200c7.png) 

如果必应无法确定图像的感兴趣区域，服务将使用盲比例裁剪。  

### <a name="blind-ratio-cropping"></a>盲比例裁剪

如果请求盲比例裁剪（方法是将 `c` 参数设置为 `4`），必应将使用以下规则来裁剪图像。  
  
- 如果 `(Original Image Width / Original Image Height) < (Requested Image Width / Requested Image Height)`，则将从左上角开始测量图像并在底部进行裁剪。  
- 如果 `(Original Image Width / Original Image Height) > (Requested Image Width / Requested Image Height)`，则将从中间测量图像并向左和右进行裁剪。  

下面是 225x300 大小的纵向图。  
  
![原始向日葵图](./media/resize-crop/bing-resize-crop-sunflower.png)
  
下图显示使用“盲比例”裁剪将图像缩成 200x200 大小的情形。 图像是从左上角测量的，这会导致图像的底部部分被裁剪。  
  
![裁剪成 200x200 大小的向日葵图](./media/resize-crop/bing-resize-crop-sunflower200x200c4.png)
  
下图显示使用“盲比例”裁剪将图像缩成 200x100 大小的情形。 图像是从左上角测量的，这会导致图像的底部部分被裁剪。  
  
![裁剪成 200x100 大小的向日葵图](./media/resize-crop/bing-resize-crop-sunflower200x100c4.png)
  
下图显示使用“盲比例”裁剪将图像缩成 100x200 大小的情形。 图像从中心进行测量，因此会裁剪其左侧和右侧。  
  
![裁剪成 100x200 大小的向日葵图](./media/resize-crop/bing-resize-crop-sunflower100x200c4.png)

## <a name="next-steps"></a>后续步骤

* [什么是必应搜索 API？](bing-api-comparison.md)
* [必应搜索 API 使用和显示要求](use-display-requirements.md)