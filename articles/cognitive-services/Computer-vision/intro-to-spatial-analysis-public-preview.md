---
title: 什么是空间分析？
titleSuffix: Azure Cognitive Services
description: 本文档介绍 Azure 空间分析容器的基本概念和功能。
services: cognitive-services
author: nitinme
manager: nitinme
ms.author: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 10/06/2021
ms.custom: contperf-fy22q2
ms.openlocfilehash: 9553c7e177bdc78b071d6ed68725879e46c9ef5e
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2021
ms.locfileid: "129668440"
---
# <a name="what-is-spatial-analysis"></a>什么是空间分析？

空间分析服务通过了解人员在给定地区的移动情况和状态，来帮助组织最大限度地提高其物理空间的价值。 通过该功能可从 CCTV 或监视摄像头引入视频，从视频流中提取见解，并可生成事件供其他系统使用。 根据摄像头的流输入内容，该服务可以执行一些任务，例如统计进入空间的人数，或衡量人们有无遵守佩戴口罩和社交距离的准则。

<!--This documentation contains the following types of articles:
* The [quickstarts](./quickstarts-sdk/analyze-image-client-library.md) are step-by-step instructions that let you make calls to the service and get results in a short period of time. 
* The [how-to guides](./Vision-API-How-to-Topics/HowToCallVisionAPI.md) contain instructions for using the service in more specific or customized ways.
* The [conceptual articles](tbd) provide in-depth explanations of the service's functionality and features.
* The [tutorials](./tutorials/storage-lab-tutorial.md) are longer guides that show you how to use this service as a component in broader business solutions.-->

## <a name="what-it-does"></a>作用

空间分析的核心操作是基于一个系统构建的，该系统可引入视频，检测视频中的人员，跟踪这些人员随时间变化的动向，并在人员与感兴趣区域交互时生成事件。

## <a name="spatial-analysis-features"></a>空间分析功能

| 功能 | 定义 |
|------|------------|
| **人员检测** | 此组件回答的问题是“这张图像中的人在哪里”？ 它会找到图像中的人员并向人员跟踪组件传递一个范围框坐标，指示每个人的位置。 |
| **人员跟踪** | 当人们在摄像头前方四处移动时，此组件会随时间推移连接人员检测。 它使用有关人们通常如何移动的时态逻辑，以及有关人们整体外观的基本信息。 它不会跨多个摄像头来跟踪人员。 如果有人离开视野范围的时长超过约一分钟，然后重新进入视野，则系统会将其视为新的人员。 人员跟踪不能跨摄像头唯一地识别个人。 它不会使用面部识别或步态跟踪。 |
| **口罩检测** | 该组件在摄像头视野内检测人脸的位置，并识别有无口罩。 AI 操作会扫描视频中的图像；在检测到人脸时，服务会围绕脸部提供一个范围框。 使用对象检测功能，识别范围框中有无口罩。 口罩检测不涉及区分人脸、预测或分类人脸特性或执行人脸识别。 |
| **感兴趣区域** | 此组件是输入视频帧中的用户定义区域或行。 当有人与视频中的该区域交互时，系统将生成一个事件。 例如，对于 PersonCrossingLine 操作，系统会在视频帧中定义一条线。 当有人越过该线时，系统将生成一个事件。 |
| **事件** | 事件是空间分析的主要输出。 每个操作会定期（例如每分钟一次）或每当触发了特定的触发器时引发一次特定事件。 事件包括输入视频中发生的情况，但不包括任何图像或视频。 例如，PeopleCount 操作可在每次人数有变化（触发器）时引发一个包含更新计数的事件，或每分钟引发一次（定期）。 |

## <a name="get-started"></a>入门

按照[快速入门](spatial-analysis-container.md)中的步骤设置空间分析容器并开始分析视频。

## <a name="responsible-use-of-spatial-analysis-technology"></a>合理使用空间分析技术

要了解如何合理使用空间分析技术，请参阅[透明声明](/legal/cognitive-services/computer-vision/transparency-note-spatial-analysis?context=%2fazure%2fcognitive-services%2fComputer-vision%2fcontext%2fcontext)。 Microsoft 的透明声明旨在帮助你了解 AI 技术的工作原理、系统所有者可通过哪些选择来影响系统性能和行为，以及保持系统全局观（包括技术、人员和环境）的重要性。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [快速入门：空间分析容器](spatial-analysis-container.md)
