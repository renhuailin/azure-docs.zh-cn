---
title: 什么是光学字符识别？
titleSuffix: Azure Cognitive Services
description: 光学字符识别 (OCR) 服务提取图像中的可见文本，并将其作为结构化字符串返回。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 06/21/2021
ms.author: pafarley
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 3c644a62dfd48f86fd21a39af57211ff7c20ae36
ms.sourcegitcommit: 096e7972e2a1144348f8d648f7ae66154f0d4b39
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2021
ms.locfileid: "112520613"
---
# <a name="what-is-optical-character-recognition"></a>什么是光学字符识别？

利用光学字符识别 (OCR)，你可以从图像（如街道符号和产品的照片）和文档（发票、帐单、财务报表、文章等）中提取印刷体文本或手写文本。 Microsoft 的 OCR 技术支持提取[多种语言](./language-support.md)的印刷体文本。 按[快速入门](./quickstarts-sdk/client-library.md)的说明开始操作。

![OCR 演示](./Images/ocr-demo.gif)

本文档包含以下类型的文章：
* [快速入门](./quickstarts-sdk/client-library.md)是分步说明，可按照其调用服务，并在短时间内获得结果。 
* [操作指南](./Vision-API-How-to-Topics/call-read-api.md)包含以更具体的方式或自定义方式使用服务的说明。
<!--* The [conceptual articles](Vision-API-How-to-Topics/call-read-api.md) provide in-depth explanations of the service's functionality and features.
* The [tutorials](./tutorials/storage-lab-tutorial.md) are longer guides that show you how to use this service as a component in broader business solutions. -->

## <a name="read-api"></a>读取 API 

计算机视觉[读取 API](https://centraluseuap.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/5d986960601faab4bf452005) 是 Azure 最新的 OCR 技术（[了解新功能](./whats-new.md)），可从图像和多页 PDF 文档中提取印刷文本（多种语言）、手写文本（仅英语）、数字和货币符号。 该技术经过优化，可以从多文本图像中提取文本，也可从包含混合语言的多页 PDF 文档中提取文本。 它支持检测同一图像或文档中的印刷文本和手写文本。

![OCR 如何将图像和文档转换为带有提取文本的结构化输出](./Images/how-ocr-works.svg)

## <a name="input-requirements"></a>输入要求

**读取** 调用采用图像和文档作为输入。 这些输入需满足以下要求：

* 支持的文件格式：JPEG、PNG、BMP、PDF 和 TIFF
* 对于 PDF 和 TIFF 文件，最多处理 2000 个页面（对于免费层，只处理前两个页面）。
* 文件大小必须小于 50 MB（对于免费层，则为 6 MB），且尺寸介于 50 x 50 和 10000 x 10000 像素之间。 

## <a name="supported-languages"></a>支持的语言
对于印刷体文本，读取 API 总共支持 73 种语言。 请参阅 [OCR 支持的语言](./language-support.md#optical-character-recognition-ocr)完整列表。 手写体 OCR 仅支持英语。

## <a name="key-features"></a>主要功能

读取 API 包括以下功能。 

* 73 种语言的打印文本提取
* 英语手写文本提取
* 具有位置和置信度分数的文本行和字词
* 不需要语言标识
* 支持混合语言、混合模式（打印和手写）
* 从大型多页文档中选择页面和页面范围
* 文本行的自然阅读顺序
* 文本行的手写分类
* 本地部署可用的 Distroless Docker 容器

了解[如何使用 OCR 功能](./vision-api-how-to-topics/call-read-api.md)。

## <a name="use-the-cloud-api-or-deploy-on-premise"></a>使用云 API 或本地部署
大多数客户都青睐读取 3.x 云 API，因为它易于集成，而且其现成可用的性质能够提高工作效率。 Azure 和计算机视觉服务将处理缩放、性能、数据安全与合规需求，你只需将工作重心放在满足客户需求上。

对于本地部署，可以使用[读取 Docker 容器（预览版）](./computer-vision-how-to-install-containers.md)在你自己的本地环境中部署新的 OCR 功能。 容器非常适合用于满足特定的安全性和数据管理要求。

> [!WARNING]
> 计算机视觉 2.0 RecognizeText 操作即将弃用，将由本文所述的新[读取 API](#read-api) 所取代。 现有客户应[改用读取操作](upgrade-api-versions.md)。

## <a name="data-privacy-and-security"></a>数据隐私和安全性

与所有认知服务一样，使用计算机视觉服务的开发人员应该了解 Microsoft 针对客户数据的政策。 请参阅 Microsoft 信任中心上的[“认知服务”页面](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices)来了解详细信息。

## <a name="next-steps"></a>后续步骤

- 开始使用 [OCR（读取）REST API 或客户端库快速入门](./quickstarts-sdk/client-library.md)。
- 了解[读取 3.2 REST API](https://centraluseuap.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/5d986960601faab4bf452005)。
