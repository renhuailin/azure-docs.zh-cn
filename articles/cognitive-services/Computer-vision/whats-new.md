---
title: 计算机视觉有哪些新增功能？
titleSuffix: Azure Cognitive Services
description: 本文包含有关计算机视觉的新闻。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 05/24/2021
ms.author: pafarley
ms.openlocfilehash: b7292206aacbbca5c8e76ce573dc6fd0b3e32578
ms.sourcegitcommit: d858083348844b7cf854b1a0f01e3a2583809649
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2021
ms.locfileid: "122835749"
---
# <a name="whats-new-in-computer-vision"></a>计算机视觉中的新增功能

了解服务中的新增功能。 这些内容可能包括发布说明、视频、博客文章和其他类型的信息。 请将本页加入书签，以随时了解该服务的最新信息。

## <a name="august-2021"></a>2021 年 8 月

### <a name="image-tagging-language-expansion"></a>图像标记语言扩展

图像标记器的[最新版本 (v3.2)](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/56f91f2e778daf14a499f200) 现支持 50 种语言的标记。 有关详细信息，请参阅[语言支持](language-support.md)页。

## <a name="may-2021"></a>2021 年 5 月

### <a name="spatial-analysis-container-update"></a>空间分析容器更新

已发布提供新功能集的[空间分析容器](spatial-analysis-container.md)新版本。 借助此 Docker 容器，可分析实时流视频，了解人们与他们在物理环境中的移动之间的空间关系。 

* 现可配置[空间分析操作](spatial-analysis-operations.md)来检测人面朝的方向。 
    * 可通过配置 `enable_orientation` 参数，为 `personcrossingline` 和 `personcrossingpolygon` 操作启用方向分类器。 默认情况下，此选项设置为 off。

* [空间分析操作](spatial-analysis-operations.md)现在还提供可检测人步行/奔跑速度的配置
     * 可以通过打开 `enable_speed` 分类器（默认情况下为关闭状态）来检测 `personcrossingline` 和 `personcrossingpolygon` 操作的速度。 输出结果反映在 `speed`、`avgSpeed` 和 `minSpeed` 输出中。


## <a name="april-2021"></a>2021 年 4 月

### <a name="computer-vision-v32-ga"></a>计算机视觉 v3.2 GA

计算机视觉 API v3.2 现已正式发布，进行了以下更新：
* 改善了图像标记模型：根据图像中显示的对象、操作和内容分析视觉对象内容并生成相关标记。 此模型通过[标记图像 API](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/56f91f2e778daf14a499f200) 提供。 有关详细信息，请参阅图像分析[操作指南](./vision-api-how-to-topics/howtocallvisionapi.md)和[概述](./overview-image-analysis.md)。
* 更新的内容审查模型：检测是否存在成人内容并提供标志来筛选包含成人、猥亵和血腥视觉内容的图像。 可通过[分析 API](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/56f91f2e778daf14a499f21b) 获取此模型。 有关详细信息，请参阅图像分析[操作指南](./vision-api-how-to-topics/howtocallvisionapi.md)和[概述](./overview-image-analysis.md)。
* [用于 73 种语言的 OCR（读取）](./language-support.md#optical-character-recognition-ocr)，这些语言包括简体中文和繁体中文、日语、韩语和拉丁语言。
* [OCR (读取) ](./overview-ocr.md) 还可作为本地部署的 [Distroless 容器](./computer-vision-how-to-install-containers.md?tabs=version-3-2)。

> [!div class="nextstepaction"]
> [请参阅计算机视觉 v3.2 GA](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/5d986960601faab4bf452005)

## <a name="march-2021"></a>2021 年 3 月

### <a name="computer-vision-32-public-preview-update"></a>计算机视觉 3.2 公共预览版更新

计算机视觉 API 3.2 公共预览版已经更新。 该预览版包含所有计算机视觉功能以及已更新的读取 API 和分析 API。

> [!div class="nextstepaction"]
> 请参阅[计算机视觉 v3.2 公共预览版 3](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/5d986960601faab4bf452005)

## <a name="february-2021"></a>2021 年 2 月

### <a name="read-api-v32-public-preview-with-ocr-support-for-73-languages"></a>读取 API v3.2 公共预览版（带有对 73 种语言的 OCR 支持）
计算机视觉的读取 API v3.2 公共预览版（可用作云服务和 Docker 容器）包括以下更新：
* [用于 73 种语言的 OCR](./language-support.md#optical-character-recognition-ocr)，这些语言包括简体中文和繁体中文、日语、韩语和拉丁语言。
* 文本行输出的自然读取顺序（仅限拉丁语言）
* 文本行的手写样式分类以及置信度分数（仅限拉丁语言）。
* 对于多页文档，仅提取所选页面的文本。
* 可为本地部署用作 [Distroless 容器](./computer-vision-how-to-install-containers.md?tabs=version-3-2)。

若要了解详细信息，请参阅[读取 API 操作指南](Vision-API-How-to-Topics/call-read-api.md)。

> [!div class="nextstepaction"]
> [使用读取 API v3.2 公共预览版](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/5d986960601faab4bf452005)


## <a name="january-2021"></a>2021 年 1 月

### <a name="spatial-analysis-container-update"></a>空间分析容器更新

已发布提供新功能集的[空间分析容器](spatial-analysis-container.md)新版本。 借助此 Docker 容器，可分析实时流视频，了解人们与他们在物理环境中的移动之间的空间关系。 

* 现可配置[空间分析操作](spatial-analysis-operations.md)来检测某人是否正戴着口罩等保护性面罩。 
    * 可通过配置 `ENABLE_FACE_MASK_CLASSIFIER` 参数，为 `personcount`、`personcrossingline` 和 `personcrossingpolygon` 操作启用口罩分类器。
    * 系统将以元数据的形式返回 `face_mask` 和 `face_noMask` 属性，其中有在视频流中检测到的每个人的置信度分数
* personcrossingpolygo 操作已得到扩展，可计算一个人在某个区域中的停留时间。 可将该操作的区域配置中的 `type` 参数设置为 `zonedwelltime`，类型为 personZoneDwellTimeEvent 的新事件将包括 `durationMs` 字段，该字段填充了该人员在该区域中停留的毫秒数。
* 中断性变更：已将 personZoneEvent 事件重命名为 personZoneEnterExitEvent 。 此事件在某人进入或离开该区域时由 personcrossingpolygon 操作引发，并提供与所穿过区域的编号侧相关的方向信息。
* 可在所有操作中将视频 URL 作为“专用参数/已模糊处理”提供。 模糊处理现在是可选操作，仅当 `KEY` 和 `IV` 作为环境变量提供时才有效。
* 默认情况下，对所有操作启用了校准。 设置 `do_calibration: false` 可禁用它。
* 已通过 `enable_recalibration` 参数增加对自动重新校准的支持（默认禁用），请参阅[空间分析操作](./spatial-analysis-operations.md)了解有关详细信息
* 照相机校准参数设置为 `DETECTOR_NODE_CONFIG`。 有关详细信息，请参阅[空间分析操作](./spatial-analysis-operations.md)。


## <a name="october-2020"></a>2020 年 10 月

### <a name="computer-vision-api-v31-ga"></a>计算机视觉 API v3.1 GA

正式发布的计算机视觉 API 已升级到 v3.1。

## <a name="september-2020"></a>2020 年 9 月

### <a name="spatial-analysis-container-preview"></a>空间分析容器预览版

[空间分析容器](spatial-analysis-container.md)现提供预览版。 利用计算机视觉的空间分析功能，你可以分析实时流视频，了解人们与他们在物理环境中的移动之间的空间关系。 空间分析是一种可以在本地使用的 Docker 容器。 

### <a name="read-api-v31-public-preview-adds-ocr-for-japanese"></a>读取 API v3.1 公共预览版添加了日语的 OCR
计算机视觉的读取 API v3.1 公共预览版添加了以下功能：
* 日语的 OCR
* 对于每个文本行，指示呈现效果是手写体还是打印样式，并随附置信度评分（仅限拉丁语言）。
* 对于多页文档，仅提取所选页面或页面范围的文本。

* 此预览版本的读取 API 支持英语、荷兰语、法语、德语、意大利语、日语、葡萄牙语、简体中文和西班牙语。

若要了解详细信息，请参阅[读取 API 操作指南](Vision-API-How-to-Topics/call-read-api.md)。

> [!div class="nextstepaction"]
> [详细了解读取 API v3.1 公共预览版 2](https://westus2.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-preview-2/operations/5d986960601faab4bf452005)

## <a name="july-2020"></a>2020 年 7 月

### <a name="read-api-v31-public-preview-with-ocr-for-simplified-chinese"></a>读取 API v3.1 公共预览版包含简体中文的 OCR
计算机视觉的读取 API v3.1 公共预览版添加了对简体中文的支持。

* 此预览版本的读取 API 支持英语、荷兰语、法语、德语、意大利语、葡萄牙语、简体中文和西班牙语。

若要了解详细信息，请参阅[读取 API 操作指南](Vision-API-How-to-Topics/call-read-api.md)。

> [!div class="nextstepaction"]
> [详细了解读取 API v3.1 公共预览版 1](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-preview-1/operations/5d986960601faab4bf452005)

## <a name="may-2020"></a>2020 年 5 月
计算机视觉 API 3.0 版本正式发布，并对读取 API 进行了更新：

* 支持英语、荷兰语、法语、德语、意大利语、葡萄牙语和西班牙语
* 准确度改进
* 每个已提取单词的置信度分数
* 新输出格式

若要了解详细信息，请参阅 [OCR 概述](overview-ocr.md)。

## <a name="march-2020"></a>2020 年 3 月

* 现在，将对此服务的所有 HTTP 请求强制执行 TLS 1.2。 有关详细信息，请参阅 [Azure 认知服务安全性](../cognitive-services-security.md)。

## <a name="january-2020"></a>2020 年 1 月

### <a name="read-api-30-public-preview"></a>读取 API 3.0 公共预览版

现在，可以使用 Read API 3.0 版从图像中提取印刷体文本或手写文本。 与早期版本相比，3.0 版提供了：
* 准确度改进
* 新输出格式
* 每个已提取单词的置信度分数
* 使用语言参数同时支持西班牙语和英语

按照[提取文本快速入门](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/REST/CSharp-hand-text.md?tabs=version-3)，开始使用 3.0 API。

## <a name="cognitive-service-updates"></a>认知服务更新

[认知服务的 Azure 更新公告](https://azure.microsoft.com/updates/?product=cognitive-services)
