---
title: 什么是 Azure 人脸服务？
titleSuffix: Azure Cognitive Services
description: Azure 人脸服务提供用于检测、识别和分析图像中人脸的 AI 算法。
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: overview
ms.date: 04/19/2021
ms.author: pafarley
ms.custom: cog-serv-seo-aug-2020
keywords: 人脸识别, 人脸识别软件, 人脸分析, 人脸匹配, 人脸识别应用, 按图像搜索人脸, 人脸识别搜索
ms.openlocfilehash: 15d8043beb32d8f3c7fa1b237e1ba25310983b86
ms.sourcegitcommit: d11ff5114d1ff43cc3e763b8f8e189eb0bb411f1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2021
ms.locfileid: "122822281"
---
# <a name="what-is-the-azure-face-service"></a>什么是 Azure 人脸服务？

> [!WARNING]
> 2020 年 6 月 11 日，Microsoft 宣布在以人权为基础的强有力的法规颁布之前，将不向美国的警察局出售人脸识别技术。 同样地，如果客户是美国警察局、由美国警察局准许使用或针对美国警察局使用 Azure 服务，则这些客户不得使用这类服务中包含的人脸识别特性或功能（例如人脸或视频索引器）。 创建新的人脸资源时，必须在 Azure 门户中确认并同意将不会由美国警察局使用该服务或是为美国警察局使用该服务，并且已查看了[负责任的 AI (RAI) 文档](https://go.microsoft.com/fwlink/?linkid=2164191)，将根据它使用此服务。

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Azure 人脸服务提供用于检测、识别和分析图像中人脸的 AI 算法。 人脸识别软件在许多不同情形中都十分重要，例如身份验证、无接触访问控制和实现隐私的人脸模糊。

身份验证：针对政府颁发的 ID 卡（如护照或驾照）或是其他注册图像验证用户的身份，以便授予对数字或物理服务的访问权限或是恢复帐户。 特定访问情形包括建立新帐户、验证工作人员或管理联机评估。 身份验证可在人员加入时执行一次，并在有人访问数字或物理服务时重复进行。

无接触访问控制：与当前方法（例如卡或票证）相比，选择人脸识别可实现增强的访问控制体验，同时降低卡共享、丢失或被盗的卫生和安全风险。 人脸识别可在机场、体育场、主题公园或建筑物以及办公室、医院、健身房、俱乐部或学校接待亭的签入循环中帮助人员进行签入过程。

人脸编修：编辑或模糊视频中录制的检测到的人脸，以保护其隐私。

本文档包含以下类型的文章：
* [快速入门](./Quickstarts/client-libraries.md)是分步说明，可按照其调用服务，并在短时间内获得结果。 
* [操作指南](./Face-API-How-to-Topics/HowtoDetectFacesinImage.md)包含以更具体的方式或自定义方式使用服务的说明。
* [概念性文章](./concepts/face-detection.md)对服务的功能和特性进行了深入说明。
* [教程](./enrollment-overview.md)是较长的指南，向你演示了如何在更广泛的业务解决方案中使用此服务作为组件。

## <a name="face-detection-and-analysis"></a>人脸检测和分析

在所有其他情况下，需要将人脸检测作为第一步。 检测 API 可以检测图像中的人脸，并返回其位置的矩形坐标。 它还会返回表示存储的人脸数据的唯一 ID，在后续操作用于识别或验证人脸。

人脸检测还可提取一组人脸相关属性，例如身头部姿势、年龄、情感、面部毛发和眼镜。 这些属性是一般预测，而不是实际分类。 某些属性可用于确保在用户将自己添加到人脸服务时，应用程序获得高质量的人脸数据（例如，如果用户戴着太阳镜，则应用程序可能会建议用户取下太阳镜）。

> [!NOTE]
> 也可以通过[计算机视觉服务](../computer-vision/overview.md)使用人脸检测功能。 但是，如果你想要使用其他人脸操作，如识别、验证、查找相似或人脸分组，应改用此服务。

有关人脸检测和分析的详细信息，请参阅[人脸检测](concepts/face-detection.md)概念文章。 另请参阅[检测 API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) 参考文档。


## <a name="identity-verification"></a>身份验证

现代企业和应用可以使用人脸识别和人脸验证操作来验证用户是否是其声称的身份。 

### <a name="identification"></a>标识

人脸识别可以被视为“一对多”匹配。 根据其人脸数据与查询人脸的匹配程度，返回匹配候选项。 此方案用于向对某组人员授予建筑物进出权限或验证设备的用户。

下图显示名为 `"myfriends"` 的数据库的示例。 每个组最多可以包含 100 万个不同的 person 对象。 每个人员对象可以注册最多 248 张人脸。

![包含三个列（代表不同的人）的网格，每个列包含三行人脸图像](./Images/person.group.clare.jpg)

创建并训练一个组后，可以对组使用新检测到的人脸执行识别。 如果人脸被标识为组中的某一人员，则返回该人员对象。

### <a name="verification"></a>验证

验证操作会回答问题“这两个人脸是否属于同一人？”。 验证也称为“一对一”匹配，因为探测人脸数据只与单个注册人脸进行比较。 验证在识别方案中用于仔细检查给定匹配是否准确。 

有关身份验证的详细信息，请参阅[人脸识别](concepts/face-recognition.md)概念指南或[识别](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)和[验证](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a) API 参考文档。


## <a name="find-similar-faces"></a>查找相似人脸

查找相似人脸操作会在目标人脸和一组候选人脸之间进行人脸匹配，找出与目标人脸相似的一小组人脸。 这对于按图像进行人脸搜索很有用。 

该服务支持两种工作模式：matchPerson 和 matchFace 。 使用[验证 API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a) 针对同一人进行筛选后，matchPerson 模式会返回相似人脸。 matchFace 模式会忽略同一人筛选器。 它返回相似候选人脸的列表，这些人脸不一定属于同一人。

以下示例显示了目标人脸：

![一位微笑的女士](./Images/FaceFindSimilar.QueryFace.jpg)

这些图像是候选人脸：

![五位微笑的人的图像。 图像 a 和 b 显示同一人。](./Images/FaceFindSimilar.Candidates.jpg)

为了查找四张相似人脸，matchPerson 模式会返回 a 和 b，它们与目标人脸显示的是同一个人。 matchFace 模式返回 a、b、c 和 d&mdash;恰好四个候选项，即使某些选项与目标不是同一人或者相似度低，也是如此。 有关详细信息，请参阅[人脸识别](concepts/face-recognition.md)概念指南或[查找相似人脸 API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) 参考文档。

## <a name="group-faces"></a>对人脸分组

组操作会基于相似性将未知人脸的集合分为几个较小的组。 每个组是原始人脸集合的互不相交真子集。 它还返回单个“messyGroup”数组，其中包含未找到相似性的人脸 ID。

返回的一个组中的所有人脸可能属于同一人，但一个人可能有多个不同的组。 这些组按其他因素（例如表情）区分。 有关详细信息，请参阅[人脸识别](concepts/face-recognition.md)概念指南或[分组 API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238) 参考文档。

## <a name="sample-app"></a>示例应用

以下示例应用程序显示了人脸服务的多种用法：

- [FamilyNotes UWP 应用](https://github.com/Microsoft/Windows-appsample-familynotes)是一个通用 Windows 平台 (UWP) 应用，可以在家庭便笺共享方案中使用人脸识别、语音、Cortana、墨迹和相机。

## <a name="data-privacy-and-security"></a>数据隐私和安全性

与所有认知服务资源一样，使用人脸服务的开发人员必须了解 Microsoft 针对客户数据的政策。 有关详细信息，请参阅 Microsoft 信任中心内的[“认知服务”页](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices)。

## <a name="next-steps"></a>后续步骤

按照快速入门中的说明，使用所选语言对人脸识别应用的基本组件进行编码。

- [客户端库快速入门](quickstarts/client-libraries.md)。
