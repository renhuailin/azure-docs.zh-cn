---
title: 什么是计算机视觉？
titleSuffix: Azure Cognitive Services
description: 使用计算机视觉服务，你可以访问用于处理图像并返回信息的高级算法。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 09/28/2021
ms.author: pafarley
ms.custom:
- seodec18
- cog-serv-seo-aug-2020
- contperf-fy21q2
keywords: computer vision, computer vision applications, computer vision service
ms.openlocfilehash: c5aad28fc8b2052f9a5af1233085a93b6a25c479
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129360164"
---
# <a name="what-is-computer-vision"></a>什么是计算机视觉？

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

使用 Azure 的计算机视觉服务，你可以访问高级算法，这些算法根据你感兴趣的视觉功能处理图像并返回信息。 

| 服务|说明|
|---|---|
| [光学字符识别 (OCR)](overview-ocr.md)|光学字符识别 (OCR) 服务从图像中提取文本。 你可以使用新读取 API 从图像和文档中提取印刷体文本和手写文本。 此 API 使用基于深度学习的模型，并处理各种表面和后台上的文本。 包括业务文档、发票、收据、海报、名片、信件和白板。 这些 OCR API 支持提取[多种语言](./language-support.md)的印刷体文本。 按 [OCR 快速入门](quickstarts-sdk/client-library.md)的说明开始操作。|
|[图像分析](overview-image-analysis.md)| 图像分析服务从图像中提取许多视觉特征，例如对象、人脸、成人内容和自动生成的文本说明。 按照[图像分析快速入门](quickstarts-sdk/image-analysis-client-library.md)开始操作。|
| [空间分析](intro-to-spatial-analysis-public-preview.md)| 空间分析服务会分析视频源上人员的状态和移动，并生成其他系统可以响应的事件。 安装[空间分析容器](spatial-analysis-container.md)以开始工作。|

## <a name="computer-vision-for-digital-asset-management"></a>用于数字资产管理的计算机视觉

计算机视觉可以支持许多数字资产管理 (DAM) 方案。 DAM 是组织、存储和检索富媒体资产以及管理数字权利和权限的业务流程。 例如，公司可能希望基于可见徽标、面部、物体、颜色等来分组和标识图像。 或者，你可能希望自动[生成图像的标题](./Tutorials/storage-lab-tutorial.md)，并附加关键字，使其可供搜索。 有关使用认知服务、Azure 认知搜索和智能报表的一体式 DAM 解决方案，请参阅 GitHub 上的[知识挖掘解决方案加速器指南](https://github.com/Azure-Samples/azure-search-knowledge-mining)。 有关其他 DAM 示例，请参阅[计算机视觉解决方案模板](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates)存储库。

## <a name="image-requirements"></a>图像要求

计算机视觉可以分析符合以下要求的图像：

- 图像必须以 JPEG、PNG、GIF 或 BMP 格式显示
- 图像的文件大小必须不到 4 兆字节 (MB)
- 图像的尺寸必须大于 50 x 50 像素
  - 对于读取 API，图像的尺寸必须介于 50 x 50 和 10000 x 10000 像素之间。

## <a name="data-privacy-and-security"></a>数据隐私和安全性

与所有认知服务一样，使用计算机视觉服务的开发人员应该了解 Microsoft 针对客户数据的政策。 请参阅 Microsoft 信任中心上的[“认知服务”页面](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices)来了解详细信息。

## <a name="next-steps"></a>后续步骤

参考快速入门的说明，以首选开发语言实现和运行服务。

* [快速入门：光学字符识别 (OCR)](quickstarts-sdk/client-library.md)
* [快速入门：图像分析](quickstarts-sdk/image-analysis-client-library.md)
* [快速入门：空间分析容器](spatial-analysis-container.md)
