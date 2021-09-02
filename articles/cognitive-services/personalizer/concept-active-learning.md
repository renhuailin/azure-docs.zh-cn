---
title: 学习策略 - 个性化体验创建服务
description: 学习设置决定了模型训练的超参数。 相同数据基于不同的学习设置训练的两个模型会产生不同的结果。
author: jeffmend
ms.author: jeffme
ms.manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: aad256981001d3b115570ddac0c3cf4993bc2da1
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2021
ms.locfileid: "122830736"
---
# <a name="learning-policy-and-settings"></a>学习策略和设置

学习设置决定了模型训练的超参数。 相同数据基于不同的学习设置训练的两个模型会产生不同的结果。

通过 Azure 门户在个性化体验创建服务资源上设置[学习策略和设置](how-to-settings.md#configure-rewards-for-the-feedback-loop)。

## <a name="import-and-export-learning-policies"></a>导入和导出学习策略

可以通过 Azure 门户导入和导出学习策略文件。 使用此方法可以保存、测试、替换现有策略，并在源代码管理中将其存档为项目，以供将来参考和审核。

了解[如何](how-to-manage-model.md#import-a-new-learning-policy)通过 Azure 门户为个性化体验创建服务资源导入和导出学习策略。

## <a name="understand-learning-policy-settings"></a>了解学习策略设置

不应更改学习策略中的设置。 仅当你了解所做的更改会对个性化体验创建服务造成何种影响时，才能更改设置。 如果不了解这种后果，则会引起问题，包括使个性化体验创建服务模型失效。

个性化体验创建服务使用 [vowpalwabbit](https://github.com/VowpalWabbit) 对事件进行训练和评分。 有关如何使用 vowpalwabbit 编辑学习设置的信息，请参阅 [vowpalwabbit 文档](https://github.com/VowpalWabbit/vowpal_wabbit/wiki/Command-line-arguments)。 有了正确的命令行参数后，使用以下格式将命令保存到文件（将 arguments 属性值替换为所需的命令）并上传文件，以便在 Azure 门户的“模型和学习设置”窗格中为个性化体验创建服务资源导入学习设置。

以下 `.json` 是一个学习策略示例。

```json
{
  "name": "new learning settings",
  "arguments": " --cb_explore_adf --epsilon 0.2 --power_t 0 -l 0.001 --cb_type mtr -q ::"
}
```

## <a name="compare-learning-policies"></a>比较学习策略

可以通过执行[脱机评估](concepts-offline-evaluation.md)，来比较不同的学习策略针对个性化体验创建服务日志中以往数据的有效性。

[上传自己的学习策略](how-to-manage-model.md)以便与当前学习策略进行比较。

## <a name="optimize-learning-policies"></a>优化学习策略

个性化体验创建服务可以在[脱机评估](how-to-offline-evaluation.md)中创建优化的学习策略。 优化的学习策略会在脱机评估中生成更好的奖励评分，在个性化体验创建服务中联机使用时会产生更好的结果。

优化学习策略后，可以将其直接应用到个性化体验创建服务，使之立即取代当前策略。 或者可以保存优化的策略供进一步的评估，将来可以决定是要放弃、保存还是应用它。

## <a name="next-steps"></a>后续步骤

* 了解[活动和非活动事件](concept-active-inactive-events.md)。
