---
title: 成人、猥亵、底层内容-计算机视觉
titleSuffix: Azure Cognitive Services
description: 与使用计算机视觉 APi 检测图像中的成人内容相关的概念。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 5cc8a4508ceeda245fbc10a81e16f3ecf05284c7
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/21/2020
ms.locfileid: "95013605"
---
# <a name="detect-adult-content"></a>检测成人内容

计算机视觉可以检测图像中的成人材料，使开发人员能够限制其软件中显示这些图像。 应用内容标记并使用评分（介于 0 和 1 之间），以便开发人员可以根据自己的偏好来解释结果。

> [!NOTE]
> 此功能的大部分由 [Azure 内容审查器](../content-moderator/overview.md) 服务提供。 有关更严格的内容审核方案（例如，文本审核和人工审核工作流）的解决方案，请参阅此替代方案。

## <a name="content-flag-definitions"></a>内容标记定义

"成人" 分类中有几个不同的类别：

- **成人** 图像定义为本质上是色情的，通常表现裸体和性行为。
- “不雅”图像的定义为实际具有性暗示且往往包含露骨的描绘性内容（比“成人”图像少）的图像。
- **底层** 映像定义为描述血腥的映像。

## <a name="use-the-api"></a>使用 API

你可以通过 " [分析映像](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b) API" 检测成人内容。 将的值添加 `Adult` 到 **visualFeatures** 查询参数时，API 会 &mdash; `isAdultContent` `isRacyContent` `isGoryContent` &mdash; 在其 JSON 响应中返回三个布尔属性：和。 此方法还返回相应的属性 &mdash; `adultScore` ，以及 `racyScore` `goreScore` &mdash; 表示每个相应类别的零和一个的置信度得分。

- [快速入门：在 .NET SDK ( 分析映像) ](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
- [快速入门：分析图像 (REST API)](./quickstarts/csharp-analyze.md)