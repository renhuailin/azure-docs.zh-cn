---
title: 计算机视觉空间分析简介
titleSuffix: Azure Cognitive Services
description: 本文档介绍计算机视觉空间分析容器的基本概念和功能。
services: cognitive-services
author: tchristiani
manager: nitinme
ms.author: terrychr
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 12/14/2020
ms.openlocfilehash: 402ee6d5efdd489914cb7d283c7c46d4f7d175f6
ms.sourcegitcommit: 9514d24118135b6f753d8fc312f4b702a2957780
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/07/2021
ms.locfileid: "97968052"
---
# <a name="introduction-to-computer-vision-spatial-analysis"></a>计算机视觉空间分析简介

计算机视觉空间分析是 Azure 认知服务计算机视觉的一项新功能，通过了解人员在给定领域的变动和状态，帮助组织最大限度地提高其物理空间的价值。 利用它，您可以从 CCTV 或监视摄像机中引入视频，运行 AI 技能从视频流中提取见解，并生成其他系统所使用的事件。 使用摄像流输入时，AI 技能可以执行一些操作，例如，计算输入空间的人员数或与社会 distancing 准则的符合性。

## <a name="the-basics-of-spatial-analysis"></a>空间分析的基础知识

如今，空间分析的核心操作都是在引入视频的管道上构建的，它在视频中检测人员，随着时间的推移跟踪用户，并在用户与感兴趣的区域交互时生成事件。

## <a name="spatial-analysis-terms"></a>空间分析术语

| 术语 | 定义 |
|------|------------|
| 人员检测 | 此组件回答的问题是 "此图像中的人在哪里"？ 它在图像中找到人，并传递一个范围框，指示每个用户的位置和人员跟踪组件。 |
| 人员跟踪 | 当人们在照相机的前方四处移动时，此组件会随着时间的推移连接人员检测。 它使用临时逻辑来了解人们通常如何移动，并使用有关用户整体外观的基本信息来实现此目的。 它无法跟踪多个照相机或 reidentify 的人，超过大约一分钟的时间已消失。 跟踪不使用任何生物识别标记，如人脸识别或 gait 跟踪。 |
| 感兴趣区域 | 这是配置过程中在输入视频中定义的区域或行。 当某个人与视频的区域交互时，系统将生成一个事件。 例如，对于 PersonCrossingLine 技能，会在视频中定义线条。 当某个人越过该行时，将生成一个事件。 |
| 事件 | 事件是空间分析的主输出。 每项技能定期 (ex 发出特定事件。 每分钟一次) 或发生特定的触发器。 此事件包括有关输入视频中发生的内容的信息，但不包括任何图像或视频。 例如，PeopleCount 技能每次更改 (触发器) 或每分钟 (定期) 时，都可以发出包含更新的计数的事件。 |

## <a name="example-use-cases-for-spatial-analysis"></a>空间分析的示例用例

下面是我们设计和测试空间分析时需要注意的示例用例。

**社交 Distancing 合规性** -办公室空间包含多个照相机，它们使用空间分析通过测量人员之间的距离来监视社交 Distancing 的符合性。 设施经理可以使用热图，显示一段时间内社交 distancing 符合性的聚合统计信息，以调整工作区并使社会 distancing 更轻松。

**购物者分析** -杂货商店使用位于产品显示器上的照相机来度量商品更改对商店流量的影响。 系统允许商店经理识别哪些新产品会对订婚进行最大的更改。

**队列管理** -当等待时间太长，使其能够打开更多行时，位于结帐队列的照相机会向经理提供警报。 队列放弃上的历史数据可提供对使用者行为的见解。

**构建占有 & 分析** -办公室建筑使用侧重于进入的相机来度量 footfall，以及用户如何使用工作区。 Insights 允许建筑经理调整服务和布局，以便更好地为 occupants 提供服务。

**最小员工检测** -在数据中心中，照相机监视围绕服务器的活动。 当员工以物理方式修复敏感设备时，出于安全原因，在修复过程中始终需要有两个人。 照相机用于验证是否遵循了此原则。

**工作区优化** -在快速的餐厅餐厅中，厨房中的照相机用于生成有关员工工作流的聚合信息。 经理使用此方法来改进团队的流程和培训。

## <a name="considerations-when-choosing-a-use-case"></a>选择用例时的注意事项

**避免严重安全警报** -空间分析不适用于关键安全实时警报。 如果需要实时警报来触发干预以防发生伤害，就不应依赖于这种情况，例如，当某人出现时关闭一片繁重的机械。 它可以使用统计信息和干预降低风险，从而减少风险的行为，例如人们进入限制/禁止的区域。

**避免将用于与雇用相关的决策** -空间分析提供了有关空间中人员的位置和移动的概率指标。 尽管此数据对于聚合过程改进可能很有用，但数据并不是单个辅助角色性能的良好指标，不应用于做出与雇用相关的决策。

**避免将用于卫生保健相关的决策** -空间分析提供与人员移动相关的概率和部分数据。 数据不适用于与运行状况相关的决策。

**避免在受保护的空间中使用** -通过评估相机位置和位置、调整感兴趣的角度和区域使其不会忽略受保护的区域（如卫生间）来保护个人的隐私。

**仔细考虑在学校或老年护理设施中使用的功能** -空间分析并未对包含低于18岁或成人65的成人的数据进行大量测试。 我们建议客户在这些年龄主流的环境中全面评估其方案的错误率。

**仔细考虑在公共空间中使用** -评估相机位置和位置、调整感兴趣的角度和区域以最大程度地减少从公共空间收集的范围。 公共空间（如街道和公园）中的照明和天气会显著影响空间分析系统的性能，并且极难在公共空间中提供有效的泄露。

## <a name="spatial-analysis-gating-for-public-preview"></a>公共预览版的空间分析门

若要确保空间分析用于设计它的方案，我们将通过应用程序流程向客户提供此技术。 若要获取对空间分析的访问权限，需要首先填写我们的在线进气窗体。 [在此处开始应用程序](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyQZ7B8Cg2FEjpibPziwPcZUNlQ4SEVORFVLTjlBSzNLRlo0UzRRVVNPVy4u)。

对空间分析公开预览版的访问权限取决于 Microsoft 唯一的决定，它取决于我们的资格标准、审核流程和可用性，可在此封闭预览版中支持有限数量的客户。 在公共预览版中，我们正在寻找与 Microsoft 有重大关系的客户，对我们在建议用例上与我们合作，并与我们的责任 AI 承诺保持联系。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [空间分析的特征和限制](https://docs.microsoft.com/legal/cognitive-services/computer-vision/accuracy-and-limitations?context=%2fazure%2fcognitive-services%2fComputer-vision%2fcontext%2fcontext)
