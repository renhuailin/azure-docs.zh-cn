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
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "100575372"
---
# <a name="overview-of-computer-vision-spatial-analysis"></a>计算机视觉空间分析概述

计算机视觉空间分析是 Azure 认知服务计算机视觉的一项新功能，通过了解人员在特定区域的动向和状态，帮助组织实现物理空间的价值最大化。 通过该功能可从 CCTV 或监视摄像头引入视频，运行 AI 操作从视频流中提取见解，并可生成事件供其他系统使用。 根据摄像头的流输入内容，AI 操作可以执行一些任务，例如统计进入空间的人数，或衡量人们有无遵守佩戴口罩和社交距离的准则。

## <a name="the-basics-of-spatial-analysis"></a>空间分析基础知识

如今，空间分析的核心操作都是基于引入视频的管道构建的，它可检测视频中的人员，跟踪人员随时间变化的动向，并在用户与感兴趣区域交互时生成事件。

## <a name="spatial-analysis-terms"></a>空间分析术语

| 术语 | 定义 |
|------|------------|
| 人员检测 | 此组件回答的问题是“这张图像中的人在哪里”？ 它会找到图像中的人员并向人员跟踪组件传递一个范围框，指示每个人的位置。 |
| 人员跟踪 | 当人们在摄像头前方四处移动时，此组件会随时间推移连接人员检测。 它使用人们通常如何移动的临时逻辑以及人们这样做的总体状况基本信息来实现这一点。 它不会跨多个摄像头来跟踪人员。 如果有人离开摄像头的视野时长超过约一分钟，然后重新进入摄像头视野，则系统会将其视为新用户。 人员跟踪不能跨摄像头唯一地识别个人。 它不会使用面部识别或步态跟踪。 |
| 口罩检测 | 该组件在摄像头视野内检测人脸的位置，并识别有无口罩。 为此，AI 操作会扫描视频中的图像，在检测到人脸时，服务会围绕脸部提供一个范围框。 使用对象检测功能，识别范围框中有无口罩。 口罩检测不涉及区分人脸不同、预测或分类人脸特性或执行面部识别。 |
| 感兴趣区域 | 这是配置过程中在输入视频中定义的一个区域或一条线。 当有人与视频中的该区域交互时，系统将生成一个事件。 例如，对于 PersonCrossingLine 操作，系统会在视频中定义一条线。 当有人越过该线时，系统将生成一个事件。 |
| 事件 | 事件是空间分析的主要输出。 每个操作都会定期（例如每分钟一次） 或在触发特定条件时发出特定事件。 事件包括输入视频中发生的情况，但不包括任何图像或视频。 例如，PeopleCount 操作可在每次人数有变化（触发器）时发出一个包含更新计数的事件，或每分钟发送一次（定期）。 |

## <a name="responsible-use-of-spatial-analysis-technology"></a>合理使用空间分析技术

要了解如何合理使用空间分析技术，请参阅[透明声明](/legal/cognitive-services/computer-vision/transparency-note-spatial-analysis?context=%2fazure%2fcognitive-services%2fComputer-vision%2fcontext%2fcontext)。 Microsoft 的透明声明旨在帮助你了解 AI 技术的工作原理、系统所有者可通过哪些选择来影响系统性能和行为，以及保持系统全局观（包括技术、人员和环境）的重要性。

## <a name="spatial-analysis-gating-for-public-preview"></a>空间分析公共预览版限制

为确保空间分析的使用符合其设计方案，我们将通过应用程序进程向客户提供这种技术。 要获取空间分析的访问权限，首先需要填写我们的在线登记表。 [在此开始申请](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyQZ7B8Cg2FEjpibPziwPcZUNlQ4SEVORFVLTjlBSzNLRlo0UzRRVVNPVy4u)。

空间分析公共预览版访问权限由 Microsoft 全权决定，决定因素包括我们的资格标准、审批程序以及控制预览期间支持限定客户数量的能力。 在公共预览版中，我们要寻找的是与 Microsoft 关系密切、有意就建议用例以及有利于践行我们 AI 责任承诺的其他方案与我们合作的客户。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [开始使用空间分析容器](spatial-analysis-container.md)