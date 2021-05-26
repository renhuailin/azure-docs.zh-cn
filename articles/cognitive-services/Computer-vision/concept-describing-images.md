---
title: 图像说明 - 计算机视觉
titleSuffix: Azure Cognitive Services
description: 与计算机视觉 API 的图像描述功能相关的概念。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 5cb266c6326bbaa5c8327201e2b2667bf7a00dc3
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/26/2021
ms.locfileid: "110465497"
---
# <a name="describe-images-with-human-readable-language"></a>使用人类可读语言描述图像

计算机视觉可以分析图像并生成描述其内容的人工可读的句子。 该算法实际返回基于不同视觉功能的多个描述，且每个描述都有一个置信度分数。 最终输出是按可信度从高到低排列的描述的列表。

## <a name="image-description-example"></a>图像说明示例

以下 JSON 响应表明计算机视觉在基于视觉特征对示例图像进行描述时返回的内容。

![曼哈顿建筑的黑白照片](./Images/bw_buildings.png)

```json
{
    "description": {
        "tags": ["outdoor", "building", "photo", "city", "white", "black", "large", "sitting", "old", "water", "skyscraper", "many", "boat", "river", "group", "street", "people", "field", "tall", "bird", "standing"],
        "captions": [
            {
                "text": "a black and white photo of a city",
                "confidence": 0.95301952483304808
            },
            {
                "text": "a black and white photo of a large city",
                "confidence": 0.94085190563213816
            },
            {
                "text": "a large white building in a city",
                "confidence": 0.93108362931954824
            }
        ]
    },
    "requestId": "b20bfc83-fb25-4b8d-a3f8-b2a1f084b159",
    "metadata": {
        "height": 300,
        "width": 239,
        "format": "Jpeg"
    }
}
```

## <a name="use-the-api"></a>使用 API

图像说明功能属于[分析图像](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/56f91f2e778daf14a499f21b) API。 可以通过本机 SDK 或 REST 调用来调用此 API。 将 `Description` 包括在 **visualFeatures** 查询参数中。 然后，在获取完整 JSON 响应时，就只需分析 `"description"` 部分内容的字符串。

* [快速入门：计算机视觉 REST API 或客户端库](./quickstarts-sdk/image-analysis-client-library.md?pivots=programming-language-csharp)

## <a name="next-steps"></a>后续步骤

了解[标记图像](concept-tagging-images.md)和[对图像进行分类](concept-categorizing-images.md)的相关概念。
