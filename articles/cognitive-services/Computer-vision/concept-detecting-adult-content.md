---
title: 成人、猥亵、底层内容-计算机视觉
titleSuffix: Azure Cognitive Services
description: 与使用计算机视觉 API 检测图像中的成人内容相关的概念。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 5d5961ecae2fbc154ae6f1acd74df2bb74024fa1
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2020
ms.locfileid: "96532612"
---
# <a name="detect-adult-content"></a>检测成人内容

计算机视觉可以检测图像中的成人材料，使开发人员能够限制其软件中显示这些图像。 内容标志应用于0到1之间的分数，因此开发人员可以根据自己的喜好解释结果。

> [!NOTE]
> 此功能的大部分由 [Azure 内容审查器](../content-moderator/overview.md) 服务提供。 有关更严格的内容审核方案（例如，文本审核和人工审核工作流）的解决方案，请参阅此替代方案。

## <a name="content-flag-definitions"></a>内容标记定义

"成人" 分类包含几个不同的类别：

- **成人** 图像本质上表现清晰，并且通常会表现裸体和性行为。
- **猥亵** 图像本质上是色情的，其中包含的内容通常比标记为 **成人** 的图像包含的色情内容少。
- **底层** 图像显示血糖/血腥。

## <a name="use-the-api"></a>使用 API

你可以通过 " [分析映像](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b) API" 检测成人内容。 将的值添加 `Adult` 到 **visualFeatures** 查询参数时，API 会 &mdash; `isAdultContent` `isRacyContent` `isGoryContent` &mdash; 在其 JSON 响应中返回三个布尔属性：和。 此方法还返回相应的属性 &mdash; `adultScore` ，以及 `racyScore` `goreScore` &mdash; 表示每个相应类别的零和一个的置信度得分。

- [快速入门：计算机视觉 REST API 或客户端库](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
