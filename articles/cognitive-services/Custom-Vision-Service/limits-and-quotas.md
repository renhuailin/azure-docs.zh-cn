---
title: 限制和配额 - 自定义影像服务
titleSuffix: Azure Cognitive Services
description: 本文介绍不同的授权密钥类型以及自定义视觉服务的限制和配额。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: pafarley
ms.openlocfilehash: 3392cc5f3ee9daef1ae8397f6829f4ca7a42373a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "98871394"
---
# <a name="limits-and-quotas"></a>限制和配额

自定义视觉服务有两层密钥。 可以通过 Azure 门户注册 F0（免费）或 S0（标准）订阅。 有关定价和事务的详细信息，请参阅相应的[认知服务定价页](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/)。

对于 S0 项目，预计每个项目训练图像数和每个项目的标记数量将随着时间的推移而增加。

|因子|**F0**|**S0**|
|-----|-----|-----|
|项目|2|100|
|每项目训练图像数 |5,000|100,000|
|每月预测数|10,000 |无限制|
|每项目标记数|50|500|
|迭代 |10|10|
|每个标记的最少标记图像数，分类（建议 50+） |5|5|
|每个标记的最少标记图像数，对象检测（建议 50+）|15|15|
|将预测图像存储的时间长度|30 天|30 天|
|带存储的[预测](https://go.microsoft.com/fwlink/?linkid=865445)操作数（每秒事务数）|2|10|
|不带存储的[预测](https://go.microsoft.com/fwlink/?linkid=865445)操作数（每秒事务数）|2|20|
|[TrainProject](https://go.microsoft.com/fwlink/?linkid=865446)（每秒 API 调用数）|2|10|
|[其他 API 调用](https://go.microsoft.com/fwlink/?linkid=865446)（每秒事务数）|10|10|
|接受的图像类型|jpg、png、bmp、gif|jpg、png、bmp、gif|
|最小图像高度/宽度（以像素为单位）|256（查看注释）|256（查看注释）|
|最大图像高度/宽度（以像素为单位）|10,240|10,240|
|最大图像大小（训练图像上传） |6 MB|6 MB|
|最大图像大小（预测）|4 MB|4 MB|
|每个图像的最多区域数（对象检测）|300|300|
|每个图像的最多标记数（分类）|100|100|

> [!NOTE]
> 小于 256 像素的图像可被接受，但会被放大。
> 图像纵横比不应大于 25
