---
title: 什么是图像分析？
titleSuffix: Azure Cognitive Services
description: 图像分析服务使用预先训练的 AI 模型从图像中提取许多不同的视觉特征。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 06/21/2021
ms.author: pafarley
keywords: computer vision, computer vision applications, computer vision service
ms.openlocfilehash: eb7aba4367b7f8ce54141613e0d1f52ded1c26bb
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2021
ms.locfileid: "114284792"
---
# <a name="what-is-image-analysis"></a>什么是图像分析？

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

计算机视觉图像分析服务可以从图像中提取各种视觉特征。 例如，该服务可以确定图像是否包含成人内容、查找特定的品牌或对象，或查找人脸。

你可以通过客户端库 SDK，或者直接调用 [REST API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005) 使用图像分析。 按[快速入门](quickstarts-sdk/image-analysis-client-library.md)的说明开始使用。

> [!div class="nextstepaction"]
> [入门](quickstarts-sdk/image-analysis-client-library.md)

本文档包含以下类型的文章：
* [快速入门](./quickstarts-sdk/image-analysis-client-library.md)是分步说明，可按照其调用服务，并在短时间内获得结果。 
* [操作指南](./Vision-API-How-to-Topics/HowToCallVisionAPI.md)包含以更具体的方式或自定义方式使用服务的说明。
* [概念性文章](concept-tagging-images.md)对服务的功能和特性进行了深入说明。
* [教程](./tutorials/storage-lab-tutorial.md)是较长的指南，向你演示了如何在更广泛的业务解决方案中使用此服务作为组件。

## <a name="image-analysis-features"></a>图像分析功能

可以分析图像，以便提供有关视觉特性和特征的见解。 下表的所有特性均由[分析图像](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/56f91f2e778daf14a499f21b) API 提供。 按[快速入门](./quickstarts-sdk/image-analysis-client-library.md)的说明开始操作。


### <a name="tag-visual-features"></a>标记视觉特性

根据数千个可识别对象、生物、风景和操作识别并标记图像中的视觉特征。 如果标记含混不清或者不常见，API 响应会做出提示，阐明上下文或标记。 标记并不局限于主体（如前景中的人员），还包括设置（室内或室外）、家具、工具、植物、动物、附件、小配件等。 [标记视觉特性](concept-tagging-images.md)

:::image type="content" source="Images/tagging.png" alt-text="一个玩滑板的人的图像，在右侧列出了多个标记":::


### <a name="detect-objects"></a>检测物体

对象检测类似于添加标记，但 API 返回应用于每个标记的边框坐标。 例如，如果图像包含狗、猫和人，检测操作将列出这些对象及其在图像中的坐标。 可以使用此功能进一步处理图像中各对象之间的关系。 当图像中有多个相同标记的实例时，还会通知你。 [检测物体](concept-object-detection.md)

:::image type="content" source="Images/detect-objects.png" alt-text="一个办公室的图像，在笔记本电脑周围绘有一个矩形":::


### <a name="detect-brands"></a>检测品牌

根据一个包含数千全球徽标的数据库，确定图像或视频中的商业品牌。 可以使用此功能来执行特定的操作，例如，发现哪些品牌在社交媒体上最受欢迎，或者哪些品牌在社交产品排名上最靠前。 [检测品牌](concept-brand-detection.md)

### <a name="categorize-an-image"></a>对图像分类

使用具有父/子遗传层次结构的[类别分类](Category-Taxonomy.md)对整个图像进行标识和分类。 类别可单独使用或与我们的新标记模型结合使用。<br/>目前，英语是唯一可以对图像进行标记和分类的语言。 [对图像分类](concept-categorizing-images.md)

### <a name="describe-an-image"></a>描述图像

使用完整的句子，以人类可读语言生成整个图像的说明。 计算机视觉算法可根据图像中标识的对象生成各种说明。 分别对这些说明进行评估并生成置信度分数。 然后将返回置信度分数从高到低的列表。 [描述图像](concept-describing-images.md)

:::image type="content" source="Images/description.png" alt-text="奶牛的图像，右侧有简单说明":::

### <a name="detect-faces"></a>检测人脸

检测图像中的人脸，提供每个检测到的人脸的相关信息。 计算机视觉返回每个检测到的人脸的坐标、矩形、性别和年龄。<br/>计算机视觉提供了[人脸](../face/index.yml)服务功能的子集。 可以使用“人脸”服务进行更详细的分析，如面部识别和姿势检测。 [检测人脸](concept-detecting-faces.md)

### <a name="detect-image-types"></a>检测图像类型

检测图像特征，例如图像是否为素描，或者图像是剪贴画的可能性。 [检测图像类型](concept-detecting-image-types.md)

### <a name="detect-domain-specific-content"></a>检测特定于域的内容

使用域模型来检测和标识图像中特定领域的内容，例如名人和地标。 例如，如果图像中包含人物，则计算机视觉可以使用针对名人的域模型来确定图像中检测到的人物是否为已知名人。 [检测特定领域的内容](concept-detecting-domain-content.md)

### <a name="detect-the-color-scheme"></a>检测颜色方案

分析图像中的颜色使用情况。 计算机视觉可以确定图像是黑白的还是彩色的，而对于彩色图像，又可以确定主色和主题色。 [检测颜色方案](concept-detecting-color-schemes.md)



### <a name="generate-a-thumbnail"></a>生成缩略图

分析图像的内容，生成该图像的相应缩略图。 计算机视觉首先生成高质量缩略图，然后通过分析图像中的对象来确定“感兴趣区域”。 然后，计算机视觉会裁剪图像以满足感兴趣区域的要求。 可以根据用户需求，使用与原始图像的纵横比不同的纵横比显示生成的缩略图。 [生成缩略图](concept-generating-thumbnails.md)

:::image type="content" source="Images/thumbnail-demo.png" alt-text="一个人站在山上的图像，右侧是裁剪过的版本":::


### <a name="get-the-area-of-interest"></a>获取感兴趣区域

分析图像内容，以返回“感兴趣区域”的坐标。 计算机视觉并没有裁剪图像和生成缩略图，而是返回该区域的边框坐标，因此，进行调用的应用程序可以根据需要修改原始图像。 [获取感兴趣区域](concept-generating-thumbnails.md#area-of-interest)

### <a name="moderate-content-in-images"></a>管理图像中的内容

可以使用计算机视觉[检测图像中的成人内容](concept-detecting-adult-content.md)，并返回不同分类的置信度分数。 可以在滑尺上设置标记内容的阈值，以适应首选项。

## <a name="image-requirements"></a>图像要求

图像分析可以处理符合以下要求的图像：

- 图像必须以 JPEG、PNG、GIF 或 BMP 格式显示
- 图像的文件大小必须不到 4 兆字节 (MB)
- 图像的尺寸必须大于 50 x 50 像素

## <a name="data-privacy-and-security"></a>数据隐私和安全性

与所有认知服务一样，使用计算机视觉服务的开发人员应该了解 Microsoft 针对客户数据的政策。 请参阅 Microsoft 信任中心上的[“认知服务”页面](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices)来了解详细信息。

## <a name="next-steps"></a>后续步骤

参阅使用首选开发语言的快速入门指南，开始使用图像分析：

- [快速入门：计算机视觉 REST API 或客户端库](./quickstarts-sdk/image-analysis-client-library.md)
