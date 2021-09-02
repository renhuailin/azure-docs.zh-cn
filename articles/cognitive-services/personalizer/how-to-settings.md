---
title: 配置个性化体验创建服务
description: 服务配置包括服务处理奖励的方式、服务的探索频率、重新训练模型的频率，以及存储的数据量。
author: jeffmend
ms.author: jeffme
ms.manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: how-to
ms.date: 04/29/2020
ms.openlocfilehash: c868004bcf8ec23eb6607d791f4e9762ad20eca0
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2021
ms.locfileid: "122829935"
---
# <a name="configure-personalizer-learning-loop"></a>配置个性化体验创建服务学习循环

服务配置包括服务处理奖励的方式、服务的探索频率、重新训练模型的频率，以及存储的数据量。

在 Azure 门户中的“配置”页上为个性化体验创建服务配置学习循环。

<a name="configure-service-settings-in-the-azure-portal"></a>
<a name="configure-reward-settings-for-the-feedback-loop-based-on-use-case"></a>

## <a name="planning-configuration-changes"></a>计划配置更改

由于某些配置更改会[重置模型](#settings-that-include-resetting-the-model)，因此应计划配置更改。

如果计划使用[模式](concept-apprentice-mode.md)，请确保在切换到学徒模式之前查看个性化体验创建服务配置。

<a name="clear-data-for-your-learning-loop"></a>

## <a name="settings-that-include-resetting-the-model"></a>包括重置模型的设置

以下操作会使用最多过去 2 天可用的数据触发模型的重新训练。

* 回报
* 浏览

若要[清除](how-to-manage-model.md)所有数据，请使用“模型和学习设置”页。

## <a name="configure-rewards-for-the-feedback-loop"></a>为反馈循环配置奖励

为学习循环使用奖励配置服务。 更改以下值会重置当前个性化体验创建服务模型，并使用过去 2 天的数据重新训练模型。

> [!div class="mx-imgBorder"]
> ![配置反馈循环的奖励值](media/settings/configure-model-reward-settings.png)

|值|目的|
|--|--|
|奖励等待时间|设置个性化体验创建服务收集排名调用奖励值的时间长短，从发生排名调用的那一刻开始算起。 此值的设置方法是询问：“个性化体验创建服务等待奖励调用的时间是多长？” 在此时间范围后抵达的任何奖励将被记录，但不用于学习。|
|默认奖励|如果个性化体验创建服务在“奖励等待时间”范围内未收到与排名调用关联的奖励调用，则个性化体验创建服务会分配默认奖励。 默认情况下（并且是大多数情况下），默认奖励为零 (0)。|
|奖励聚合|如果收到了同一排名 API 调用的多个奖励，则会使用此聚合方法：**sum** 或 **earliest**。 Earliest 拾取收到的最早评分，丢弃剩余的评分。 如果希望在可能重复的调用之间保持唯一的奖励，则此方法很有用。 |

更改这些值后，请务必选择“保存”。

## <a name="configure-exploration-to-allow-the-learning-loop-to-adapt"></a>配置探索以允许学习循环适应

个性化功能可以发现新的模式，并通过探索备选项（而不是使用已训练的模型的预测）来不断适应用户的行为变化。 探索值确定要使用探索应答的排名调用百分比。

更改此值会重置当前个性化体验创建服务模型，并使用过去 2 天的数据重新训练模型。

![探索值确定要使用探索应答的排名调用百分比](media/settings/configure-exploration-setting.png)

更改此值后，请务必选择“保存”。

<a name="model-update-frequency"></a>

## <a name="configure-model-update-frequency-for-model-training"></a>配置模型训练的模型更新频率

模型更新频率用于设置训练模型的频率。

|频率设置|用途|
|--|--|
|1 分钟|在使用个性化体验创建服务调试应用程序代码、进行演示或以交互方式测试机器学习方面时，一分钟的更新频率非常有用。|
|15 分钟|如果你希望密切跟踪用户行为变化，则高模型更新频率非常有用。 例如，涉及实时新闻、病毒内容或实时产品投标的站点。 在这些情况下，可以使用 15 分钟的频率。 |
|1 小时|对于大多数用例，更低的更新频率是有效的。|

![“模型更新频率”设置重新训练新个性化体验创建服务模型的频率。](media/settings/configure-model-update-frequency-settings-15-minutes.png)

更改此值后，请务必选择“保存”。

## <a name="data-retention"></a>数据保留

“数据保留期”设置个性化体验创建服务要将数据日志保留多少天。 需要使用以往的数据日志来执行[脱机评估](concepts-offline-evaluation.md)。这些评估可以衡量个性化体验创建服务的有效性以及优化学习策略。

更改此值后，请务必选择“保存”。



## <a name="next-steps"></a>后续步骤

[了解如何管理模型](how-to-manage-model.md)
