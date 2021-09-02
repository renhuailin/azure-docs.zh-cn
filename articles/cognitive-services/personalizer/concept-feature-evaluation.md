---
title: 特征评估 - 个性化体验创建服务
titleSuffix: Azure Cognitive Services
description: 当你通过 Azure 门户在个性化体验创建服务资源中运行评估时，个性化体验创建服务会提供关于上下文和操作的哪些特征会影响模型的信息。
author: jeffmend
ms.author: jeffme
ms.manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 07/29/2019
ms.openlocfilehash: 3d504293da96ea6f0c22c2a54563b39b92e4485e
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2021
ms.locfileid: "122831015"
---
# <a name="feature-evaluation"></a>功能评估

当你通过 [Azure 门户](https://portal.azure.com)在个性化体验创建服务资源中运行评估时，个性化体验创建服务会提供关于上下文和操作的哪些特征会影响模型的信息。 

这可以用于以下目的：

* 设想你可以使用的其他特征，在了解模型中哪些特征更重要的过程中获得灵感。
* 查看哪些特征不重要，在可能的情况下将其删除，或进一步分析可能是什么因素在影响使用。
* 为编辑或策展团队提供关于值得纳入目录的新内容或产品的指导。
* 排查将特征发送到个性化体验创建服务时出现的常见问题和错误。

特征越重要，在模型中所占的权重就越高。 因为这些特征具有更高的权重，所以它们往往会在个性化体验创建服务获得的奖励评分较高的情况下出现。

## <a name="getting-feature-importance-evaluation"></a>获取特征重要性评估

若要查看特征重要性结果，必须运行评估。 评估会基于评估期间观察到的特征名称创建可人工阅读的特征标签。

生成的有关特征重要性的信息表示当前的个性化体验创建服务联机模型。 评估会分析在评估期结束时保存的模型的特征重要性，该模型使用当前的联机学习策略完成了在评估期间进行的所有训练。 

特征重要性结果不代表在评估期间测试或创建的其他策略和模型。  评估不会包括在评估期结束后发送给个性化体验创建服务的特征。

## <a name="how-to-interpret-the-feature-importance-evaluation"></a>如何解释特征重要性评估

个性化体验创建服务通过创建重要性相似的特征“组”来评估特征。 我们可以说一个组总体上比其他组更重要，但是在组内，特征是按字母顺序排列的。

有关每个特征的信息包括：

* 此特征来自 Context 还是来自 Action。
* 特征键和值。

例如，冰淇淋店订购应用可能会将“Context.Weather:Hot”视为非常重要的特征。

个性化体验创建服务会显示特征之间的相关性，这些特征在一起考虑的时候获得的奖励分数会更高。

例如，你可能会看到“Context.Weather:Hot”与“Action.MenuItem:IceCream”，以及“Context.Weather:Cold”与“Action.MenuItem:WarmTea”：

## <a name="actions-you-can-take-based-on-feature-evaluation"></a>你可以基于特征评估采取的操作

### <a name="imagine-additional-features-you-could-use"></a>设想你可以使用的其他特征

从模型中的较重要特征获得灵感。 例如，如果你在视频移动应用中看到“Context.MobileBattery:Low”，你可能会想到连接类型还可以让客户选择优先看某个视频剪辑，然后你就会在应用中添加有关连接类型和带宽的特征。

### <a name="see-what-features-are-not-important"></a>查看哪些特征不重要

在可能的情况下删除不重要的特征，或进一步分析可能是什么因素在影响使用。 特征排名较低的原因可能有很多。 其中一个原因是该特征确实不会影响用户行为。 但这也可能意味着此特征没有明确显示给用户。 

例如，视频网站可能会认为“Action.VideoResolution=4k”是一项重要性较低的特征，但这与用户调查相矛盾。 原因可能是应用程序甚至没有提及或显示视频分辨率，因此用户不会根据它改变自己的行为。

### <a name="provide-guidance-to-editorial-or-curation-teams"></a>为编辑或策展团队提供指导

提供关于值得纳入目录的新内容或产品的指导。 作为一个工具，个性化体验创建服务旨在增强人类洞察力和协作。 它用于实现此目标的一种方法是向编辑组说明产品、物品或内容的哪些方面会影响用户行为。 例如，视频应用程序场景可能显示有一个重要的特征叫做“Action.VideoEntities.Cat:true”，提示编辑团队引入更多的猫视频。

### <a name="troubleshoot-common-problems-and-mistakes"></a>排查常见问题和错误

可以通过更改应用程序代码来修复常见的问题和错误，以防止将不适当的或格式错误的特征发送给个性化体验创建服务。 

发送特征时的常见错误包括：

* 发送了个人身份信息 (PII)。 特定于个人的 PII（例如姓名、电话号码、信用卡号、IP 地址）不应当用于个性化体验创建服务。 如果你的应用程序需要跟踪用户，请使用不表明身份的 UUID 或某个其他的 UserID 编号。 在大多数情况下，这也是一个问题。
* 在存在大量用户的情况下，单个用户的交互不太可能比所有用户的交互更重要，因此，发送用户 ID（即使不是 PII）给模型带来的可能更多的是干扰而不是价值。
* 发送日期-时间字段时采用了精确的时间戳而不是特征化的时间值。 提供 Context.TimeStamp.Day=Monday 或 "Context.TimeStamp.Hour"="13" 之类的特征更有用。 每一个最多有 7 个或 24 个特征值。 但 "Context.TimeStamp":"1985-04-12T23:20:50.52Z" 太精确，对我们没什么用处，因为它不会再次出现。

## <a name="next-steps"></a>后续步骤

了解个性化体验创建服务的[可伸缩性和性能](concepts-scalability-performance.md)。

