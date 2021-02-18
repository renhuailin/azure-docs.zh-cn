---
title: 空间分析概述
titleSuffix: Azure Cognitive Services
description: 本文档介绍计算机视觉空间分析容器的基本概念和功能。
services: cognitive-services
author: nitinme
manager: nitinme
ms.author: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/01/2021
ms.openlocfilehash: ad05dd59c925242baf5c2b0e36c1f51bc4fec5d4
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/17/2021
ms.locfileid: "100575372"
---
# <a name="overview-of-computer-vision-spatial-analysis"></a>计算机视觉空间分析概述

计算机视觉空间分析是 Azure 认知服务计算机视觉的一项新功能，通过了解人员在给定领域的变动和状态，帮助组织最大限度地提高其物理空间的价值。 它允许从 CCTV 或监视摄像机中引入视频，运行 AI 操作以从视频流中提取见解，并生成由其他系统使用的事件。 使用摄像流输入时，AI 操作可以执行一些操作，例如，计算输入空间的人员数，或度量与人脸掩码和社会 distancing 准则的符合性。

## <a name="the-basics-of-spatial-analysis"></a>空间分析的基础知识

如今，空间分析的核心操作都是在引入视频的管道上构建的，它在视频中检测人员，随着时间的推移跟踪用户，并在用户与感兴趣的区域交互时生成事件。

## <a name="spatial-analysis-terms"></a>空间分析术语

| 术语 | 定义 |
|------|------------|
| 人员检测 | 此组件回答的问题是 "此图像中的人在哪里"？ 它在图像中找到人，并传递一个范围框，指示每个用户的位置和人员跟踪组件。 |
| 人员跟踪 | 当人们在照相机的前方四处移动时，此组件会随着时间的推移连接人员检测。 它使用临时逻辑来了解人们通常如何移动，并使用有关用户整体外观的基本信息来实现此目的。 它不跟踪跨多个照相机的人。 如果某个人将相机中的视图的字段作为大约一分钟的时间，然后重新进入相机视图，系统会将此视为一个新用户。 跟踪不能跨相机唯一地标识个人。 它不使用面部识别或 gait 跟踪。 |
| 面部掩码检测 | 此组件检测人脸在相机视图中的位置，并确定是否存在面部掩码。 为此，AI 操作会扫描视频中的图像;检测到人脸时，该服务在人脸周围提供一个边界框。 使用对象检测功能，它确定边界框中是否存在人脸掩码。 面部面具检测不涉及到不同人脸、预测或分类面部特性或执行面部识别。 |
| 感兴趣区域 | 这是配置过程中在输入视频中定义的区域或行。 当某个人与视频的区域交互时，系统将生成一个事件。 例如，对于 PersonCrossingLine 操作，会在视频中定义一行。 当某个人越过该行时，将生成一个事件。 |
| 事件 | 事件是空间分析的主输出。 每个操作都会定期 (ex 发出特定事件。 每分钟一次) 或发生特定的触发器。 此事件包括有关输入视频中发生的内容的信息，但不包括任何图像或视频。 例如，PeopleCount 操作可以在每次更改用户计数 (触发器时，每分钟) 或每 (定期) 发出包含更新计数的事件。 |

## <a name="responsible-use-of-spatial-analysis-technology"></a>使用空间分析技术的责任

若要了解如何使用空间分析技术，请参阅 [透明度注释](/legal/cognitive-services/computer-vision/transparency-note-spatial-analysis?context=%2fazure%2fcognitive-services%2fComputer-vision%2fcontext%2fcontext)。 Microsoft 的透明度注释旨在帮助你了解 AI 技术的工作原理、系统所有者可做出的影响系统性能和行为的选项，以及考虑整个系统（包括技术、人员和环境）的重要性。

## <a name="spatial-analysis-gating-for-public-preview"></a>公共预览版的空间分析门

若要确保空间分析用于设计它的方案，我们将通过应用程序流程向客户提供此技术。 若要获取对空间分析的访问权限，需要首先填写我们的在线进气窗体。 [在此处开始应用程序](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyQZ7B8Cg2FEjpibPziwPcZUNlQ4SEVORFVLTjlBSzNLRlo0UzRRVVNPVy4u)。

对空间分析公开预览版的访问权限取决于 Microsoft 唯一的决定，它取决于我们的资格标准、审核流程和可用性，可在此封闭预览版中支持有限数量的客户。 在公共预览版中，我们正在寻找与 Microsoft 有重大关系的客户，对我们在建议用例上与我们合作，并与我们的责任 AI 承诺保持联系。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [空间分析容器入门](spatial-analysis-container.md)