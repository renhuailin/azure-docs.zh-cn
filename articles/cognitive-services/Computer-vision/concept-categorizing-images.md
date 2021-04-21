---
title: 图像分类 - 计算机视觉
titleSuffix: Azure Cognitive Services
description: 了解与计算机视觉 API 的图像分类功能相关的概念。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 97a6a08730ae0c87df3b65185d48297e2338e69b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/20/2021
ms.locfileid: "107768100"
---
# <a name="categorize-images-by-subject-matter"></a>按主题对图像进行分类

除了标记和说明以外，计算机视觉还返回图像中检测到的基于分类的类别。 不同于标记，类别是在父/子继承层次结构中组织的，并且数量更少（86 个，与数千个标记截然相反）。 所有类别名称均采用英语。 它可以单独完成分类，也可以与新的标记模型共同完成。

## <a name="the-86-category-concept"></a>86 类别概念

计算机视觉可以使用下图中 86 个类别的列表广泛或具体地对图像进行分类。 有关文本格式的完整分类，请参阅[类别分类](category-taxonomy.md)。

![类别分类中所有类别的分组列表](./Images/analyze_categories-v2.png)

## <a name="image-categorization-examples"></a>图像分类示例

以下 JSON 响应表明计算机视觉在基于视觉特征对示例图像进行分类时返回的内容。

![在公寓楼顶上的一个女人](./Images/woman_roof.png)

```json
{
    "categories": [
        {
            "name": "people_",
            "score": 0.81640625
        }
    ],
    "requestId": "bae7f76a-1cc7-4479-8d29-48a694974705",
    "metadata": {
        "height": 200,
        "width": 300,
        "format": "Jpeg"
    }
}
```

下表说明了典型的图像集以及计算机视觉为每个图像返回的类别。

| 映像 | 类别 |
|-------|----------|
| ![四个人一起摆出一家人的姿势](./Images/family_photo.png) | people_group |
| ![一只小狗坐在草地上](./Images/cute_dog.png) | animal_dog |
| ![日落时站在山岩上的人](./Images/mountain_vista.png) | outdoor_mountain |
| ![桌子上有一堆面包](./Images/bread.png) | food_bread |

## <a name="use-the-api"></a>使用 API

分类功能属于[分析图像](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-ga/operations/56f91f2e778daf14a499f21b) API。 可以通过本机 SDK 或 REST 调用来调用此 API。 将 `Categories` 包括在 **visualFeatures** 查询参数中。 然后，在获取完整 JSON 响应时，就只需分析 `"categories"` 部分内容的字符串。

* [快速入门：计算机视觉 REST API 或客户端库](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)

## <a name="next-steps"></a>后续步骤

了解[标记图像](concept-tagging-images.md)和[描述图像](concept-describing-images.md)的相关概念。
