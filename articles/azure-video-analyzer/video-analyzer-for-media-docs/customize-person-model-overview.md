---
title: 在 Azure 视频分析器媒体版（以前为视频索引器）中自定义人员模型 - Azure
description: 本文概述了什么是 Azure 视频分析器媒体版（以前为视频索引器）中的人员模型，以及如何自定义它。
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: kumud
ms.openlocfilehash: 1d76c1124be542a22707b358ce1d050dfac380f8
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128614857"
---
# <a name="customize-a-person-model-in-video-analyzer-for-media"></a>在视频分析器媒体版中自定义人员模型

Azure 视频分析器媒体版（以前为视频索引器）支持视频中的名人识别。 名人识别功能基于通常请求的数据源（如 IMDB、Wikipedia 和 LinkedIn 影响者排名）涵盖约一百万张人脸。 对于视频分析器媒体版未能识别的人脸，系统仍然会检测，但会作未命名处理。 客户可以构建自定义人员模型并启用视频分析器媒体版来识别默认未能识别的人脸。 客户可以通过将人员姓名与人脸的图像文件配对来构建这些人员的模型。  

如果你的帐户适用于不同的用例，则可以为每个帐户创建多个人员模型，从而获得许多便利。 例如，如果帐户中的内容将被分类到不同的频道，你可能希望为每个频道创建一个单独的人员模型。 

> [!NOTE]
> 每个人员模型最多支持 1000,000人，每个帐户最多包含 50 个人员模型。 

创建模型后，即可在上传/索引或重新索引视频时提供特定人员模型的模型 ID，这样就可以使用该模型。 为视频训练新的人脸时，会更新与该视频相关联的具体自定义模型。 

如果不需要多人员模型支持，则在进行上传/索引或重新索引操作时不要为视频分配人员模型 ID。 在这种情况下，视频分析器媒体版会使用帐户中的默认人员模型。 

可以使用视频分析器媒体版网站来编辑在视频中检测到的人脸，并管理帐户中的多个自定义人员模型，如[使用网站自定义人员模型](customize-person-model-with-website.md)主题中所述。 此外，也可使用 API，如 [使用 API 自定义人员模型](customize-person-model-with-api.md)中所述。
