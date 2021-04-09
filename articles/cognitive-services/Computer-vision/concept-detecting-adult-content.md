---
title: 成人、色情或血腥内容 - 计算机视觉
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
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "96532612"
---
# <a name="detect-adult-content"></a>检测成人内容

计算机视觉可以检测图像中的成人内容，以便开发人员可以限制这些图像在其软件中的显示。 应用内容标记并使用评分（介于 0 和 1 之间），以便开发人员可以根据自己的偏好来解释结果。

> [!NOTE]
> 此功能的大部分由 [Azure 内容审查器](../content-moderator/overview.md)服务提供。 有关更严格的内容审核方案（例如，文本审核和人工审核工作流）的解决方案，请参阅此替代方案。

## <a name="content-flag-definitions"></a>内容标记定义

“成人”分类包含几种不同的类别：

- “成人”图像本质上是明显与性有关的图像，通常表现为裸体和性行为。
- “不雅”图像为实际具有性暗示且往往包含露骨的描绘性内容（比“成人”图像少）的图像 。
- “血腥”图像表现为血液/血块。

## <a name="use-the-api"></a>使用 API

可以通过[分析图像](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b) API 来检测成人内容。 将值 `Adult` 添加到 visualFeatures 查询参数时，此 API 会在其 JSON 响应中返回三个布尔属性：`isAdultContent`、`isRacyContent` 和 `isGoryContent`。 该方法还返回相应的属性 `adultScore`、`racyScore` 和 `goreScore`，这些属性表示每个相应类别的介于 0 到 1 之间的置信度分数。

- [快速入门：计算机视觉 REST API 或客户端库](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
