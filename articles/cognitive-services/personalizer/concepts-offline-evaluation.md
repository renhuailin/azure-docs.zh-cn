---
title: 使用脱机评估方法 - 个性化体验创建服务
titleSuffix: Azure Cognitive Services
description: 本文将介绍如何使用脱机评估来度量应用的有效性和分析学习循环。
author: jeffmend
ms.author: jeffme
ms.manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: 95217acd5c423c71eb75a43ee1e756d6b95b696d
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2021
ms.locfileid: "122829773"
---
# <a name="offline-evaluation"></a>脱机评估

使用脱机评估方法可以在不更改代码或影响用户体验的情况下，测试和评估个性化体验创建服务的有效性。 脱机评估使用从应用程序发送到排名 API 和奖励 API 的既往数据来比较不同排名的执行方式。

脱机评估是针对日期范围执行的。 范围的最迟时间是当前时间。 范围的开头不能大于针对[数据保留期](how-to-settings.md)指定的天数。

脱机评估可帮助解答以下问题：

* 个性化体验创建服务为成功的个性化提供的排名如何？
    * 个性化体验创建服务联机机器学习策略实现的平均奖励是多少？
    * 应用程序在默认情况下的有效性与使用个性化体验创建服务时的有效性存在什么差别？
    * 个性化随机选择的比较有效性是多少？
    * 手动指定的不同学习策略的比较有效性是多少？
* 哪些上下文特征对个性化的成功做出的贡献较大或较小？
* 哪些操作特征对个性化的成功做出的贡献较大或较小？

此外，脱机评估可用于发现更优化的学习策略，个性化体验创建服务将来可以使用这些策略来改善结果。

脱机评估不提供有关用于探索的事件百分比的指导。

## <a name="prerequisites-for-offline-evaluation"></a>脱机评估的先决条件

下面是代表性脱机评估的重要注意事项：

* 有充足的数据。 建议的最小数目是 50,000 个事件。
* 从具有代表性用户行为和流量的周期收集数据。

## <a name="discovering-the-optimized-learning-policy"></a>发现优化的学习策略

个性化体验创建服务可以使用脱机评估过程来自动发现更佳的学习策略。

执行脱机评估后，可以看到个性化体验创建服务使用新策略与使用当前联机策略时的比较有效性。 然后，可以通过下载学习策略并将其上传到“模型和策略”面板中，在个性化体验创建服务中应用该学习策略以使其立即生效。 你也可以下载它供将来分析或使用。

评估中包括的当前策略：

| 学习设置 | 用途|
|--|--|
|**联机策略**| 个性化体验创建服务中当前使用的学习策略 |
|**Baseline**|应用程序的默认设置（由排名调用中发送的第一个操作确定）|
|**随机策略**|一个假想的排名行为，始终从提供的操作中返回随机操作选项。|
|**自定义策略**|启动评估时上传的附加学习策略。|
|**优化策略**|如果使用发现优化策略的选项启动了评估，则也会比较该策略，你可以下载该策略，或将其设为联机学习策略，以替换当前策略。|

## <a name="understanding-the-relevance-of-offline-evaluation-results"></a>了解脱机评估结果的相关性

运行脱机评估时，分析结果的置信边界极其重要。 如果边界较宽，则表示应用程序未收到足够的数据，因此无法进行精确或有意义的奖励评估。 随着系统累积的数据越来越多且运行脱机评估的时间越来越长，置信间隔会变得更窄。

## <a name="how-offline-evaluations-are-done"></a>如何执行脱机评估

脱机评估是使用一种称作“反事实评估”的方法执行的。

个性化体验创建服务基于这种假设：用户行为（即奖励）无法以回溯方式预测（如果用户反映的事物不同于他们看到的事物，则个性化体验创建服务不知道发生了什么情况），而只能从测得的奖励中学习。

下面是用于评估的概念过程：

```
[For a given _learning policy), such as the online learning policy, uploaded learning policies, or optimized candidate policies]:
{
    Initialize a virtual instance of Personalizer with that policy and a blank model;

    [For every chronological event in the logs]
    {
        - Perform a Rank call

        - Compare the reward of the results against the logged user behavior.
            - If they match, train the model on the observed reward in the logs.
            - If they don't match, then what the user would have done is unknown, so the event is discarded and not used for training or measurement.

    }

    Add up the rewards and statistics that were predicted, do some aggregation to aid visualizations, and save the results.
}
```

脱机评估仅使用观察到的用户行为。 此过程会丢弃大量数据，尤其是应用程序使用大量的操作执行排名调用时。


## <a name="evaluation-of-features"></a>特征评估

脱机评估可以提供相关的信息用于确定操作或上下文的多少个特定特征构成了较高奖励的权重。 该信息是使用针对给定时间段和数据的评估计算的，并会随时变化。

我们建议查看特征评估并提出以下问题：

* 应用程序或系统可以连同更有效的特征线一起提供其他哪些附加特征？
* 可以删除哪些低效的特征？ 低效特征会增大机器学习中的干扰。
* 是否意外包含了任何特征？ 这些特征的示例包括：用户身份信息、重复的 ID，等等。
* 出于法规或负责使用方面的考量，是否不应使用任何不需要的特征进行个性化？ 是否有特征可以代理（即，紧密镜像或关联）不需要的特征？


## <a name="next-steps"></a>后续步骤

[配置个性化体验创建服务](how-to-settings.md)
[运行脱机评估](how-to-offline-evaluation.md) 了解[个性化体验创建服务的工作原理](how-personalizer-works.md)
