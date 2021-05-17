---
title: 在视频索引器中自定义人员模型 - Azure
titleSuffix: Azure Media Services
description: 本文概述了什么是视频索引器中的人员模型，以及如何自定义它。
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: kumud
ms.openlocfilehash: 613ad2d0ab90bef016e80a33be12b71cba5e2f71
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "87047051"
---
# <a name="customize-a-person-model-in-video-indexer"></a>在视频索引器中自定义人员模型

视频索引器支持视频名人识别功能。 名人识别功能基于通常请求的数据源（如 IMDB、Wikipedia 和 LinkedIn 影响者排名）涵盖约一百万张人脸。 对于视频索引器未能识别的人脸，系统仍然会检测，但不会保留没有姓名。 客户可以构建自定义人员模型并启用视频索引器来识别默认未能识别的人脸。 客户可以通过将人员姓名与人脸的图像文件配对来构建这些人员的模型。  

如果你的帐户适用于不同的用例，则可以为每个帐户创建多个人员模型，从而获得许多便利。 例如，如果帐户中的内容将被分类到不同的频道，你可能希望为每个频道创建一个单独的人员模型。 

> [!NOTE]
> 每个人员模型最多支持 1000,000人，每个帐户最多包含 50 个人员模型。 

创建模型后，即可在上传/索引或重新索引视频时提供特定人员模型的模型 ID，这样就可以使用该模型。 为视频训练新的人脸时，会更新与该视频相关联的具体自定义模型。 

如果不需要多人员模型支持，则在进行上传/索引或重新索引操作时不要为视频分配人员模型 ID。 在这种情况下，视频索引器会使用帐户中的默认自定义人员模型。 

可以使用视频索引器网站来编辑在视频中检测到的人脸，并管理帐户中的多个自定义人员模型，如[使用网站自定义人员模型](customize-person-model-with-website.md)主题中所述。 此外，也可使用 API，如 [使用 API 自定义人员模型](customize-person-model-with-api.md)中所述。
