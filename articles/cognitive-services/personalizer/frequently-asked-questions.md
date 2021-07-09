---
title: 常见问题解答 - 个性化体验创建服务
description: 本文包含有关个性化体验创建服务故障排除常见问题的解答。
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: troubleshooting
ms.date: 02/26/2020
ms.author: erhopf
author: erhopf
ms.manager: nitinme
ms.openlocfilehash: aad2c7cecf71f5237c66efc4eb266fef52a4186e
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2021
ms.locfileid: "108778372"
---
# <a name="personalizer-frequently-asked-questions"></a>个性化体验创建服务常见问题解答

本文包含有关个性化体验创建服务故障排除常见问题的解答。

## <a name="configuration-issues"></a>配置问题

### <a name="i-changed-a-configuration-setting-and-now-my-loop-isnt-performing-at-the-same-learning-level-what-happened"></a>我更改了某个配置设置，但现在我的循环没有在同一学习级别上执行。 发生了什么情况？

某些配置设置会[重置你的模型](how-to-settings.md#settings-that-include-resetting-the-model)。 在计划配置更改时，应小心谨慎。

### <a name="when-configuring-personalizer-with-the-api-i-received-an-error-what-happened"></a>我在使用 API 配置个性化体验创建服务时，收到错误消息。 发生了什么情况？

如果你使用单个 API 请求来配置服务并更改学习行为，就会收到错误。 需要进行两个单独的 API 调用：首先配置服务，然后切换学习行为。

## <a name="transaction-errors"></a>事务错误

### <a name="i-get-an-http-429-too-many-requests-response-from-the-service-what-can-i-do"></a>我收到了来自服务的 HTTP 429 响应（请求数过多）。 我该怎么办？

如果你在创建个性化体验创建服务实例时选择了免费价格层，则允许的排名请求数存在配额限制。 请（针对你的个性化体验创建服务资源，在 Azure 门户的“指标”窗格中）查看你的排名 API 的 API 调用率，如果调用量预计会增加到超过所选定价层的阈值，则（在“定价层”窗格中）调整定价层。

### <a name="im-getting-a-5xx-error-on-rank-or-reward-apis-what-should-i-do"></a>我收到有关排名 API 或奖励 API 的 5xx 错误。 应采取何种操作？

这些问题应该是透明的。 如果问题持续出现，请与支持部门联系，具体步骤是，在个性化体验创建服务资源的 Azure 门户中，在“支持 + 故障排除”部分中选择“新建支持请求”。

## <a name="learning-loop"></a>学习循环

### <a name="the-learning-loop-doesnt-attain-a-100-match-to-the-system-without-personalizer-how-do-i-fix-this"></a>在没有个性化体验创建服务的情况下，学习循环与系统之间的匹配率不会达到 100%。 如何修复此问题？

学习循环达不到目标的原因如下：
* 通过排名 API 调用发送的特征数量不足
* 发送的特征中存在 bug，例如，将非聚合特征数据（如时间戳）发送到排名 API
* 循环处理过程存在 bug，例如，没有为事件将奖励数据发送到奖励 API

若要解决此问题，需要更改处理方式：要么更改发送到循环的特征，要么确保奖励是排名响应质量的正确评估。

### <a name="the-learning-loop-doesnt-seem-to-learn-how-do-i-fix-this"></a>学习循环似乎没有学习。 如何修复此问题？

学习循环需要几千个奖励调用才能使排名调用有效地确定优先级。

如果你不确定学习循环当前的行为方式，请运行[脱机评估](concepts-offline-evaluation.md)，并应用已更正的学习策略。

### <a name="i-keep-getting-rank-results-with-all-the-same-probabilities-for-all-items-how-do-i-know-personalizer-is-learning"></a>我一直获得排名结果，所有项目的概率都相同。 我如何实现知道个性化体验创建服务在学习？

当个性化体验创建服务刚刚启动并且具有空模型，或者当你重置个性化体验创建服务循环，并且模型仍处于模型更新频率期间内时，它将在排名 API 结果中返回相同的概率。

新的更新周期开始时，将使用更新的模型，你会看到概率发生变化。

### <a name="the-learning-loop-was-learning-but-seems-to-not-learn-anymore-and-the-quality-of-the-rank-results-isnt-that-good-what-should-i-do"></a>学习循环在学习，但似乎没有更多学习内容，排名结果的质量并不理想。 应采取何种操作？

* 确保已在该个性化体验创建服务资源（学习循环）的 Azure 门户中完成和应用了一个评估。
* 确保所有奖励都通过奖励 API 发送并进行处理。

### <a name="how-do-i-know-that-the-learning-loop-is-getting-updated-regularly-and-is-used-to-score-my-data"></a>如何知道学习循环会定期更新，并用于对我的数据评分？

你可以在 Azure 门户的“模型和学习设置”页中找到模型的上次更新时间。 如果你看到旧时间戳，很可能是因为你没有发送排名和奖励调用。 如果服务没有传入数据，则不会更新学习。 如果你发现循环的更新频率不够，可以编辑该循环的模型更新频率。

## <a name="offline-evaluations"></a>脱机评估

### <a name="an-offline-evaluations-feature-importance-returns-a-long-list-with-hundreds-or-thousands-of-items-what-happened"></a>脱机评估的特征重要性会返回包含数百个或数千个项目的长列表。 发生了什么情况？

这通常是由于发送了时间戳、用户 ID 或一些其他细微的特征。

### <a name="i-created-an-offline-evaluation-and-it-succeeded-almost-instantly-why-is-that-i-dont-see-any-results"></a>我创建了一个脱机评估，几乎瞬间就成功了。 为什么会这样？ 我怎么看不到任何结果？

脱机评估使用该时间段内事件的定型模型数据。 如果你在评估的开始时间和结束时间之间未发送任何数据，则评估完成后不会提供何结果。 通过选择你确定已发送到个性化体验创建服务的事件的时间范围来提交新的脱机评估。

## <a name="learning-policy"></a>学习策略

### <a name="how-do-i-import-a-learning-policy"></a>如何导入学习策略？

详细了解如何[学习策略概念](concept-active-learning.md#understand-learning-policy-settings)以及[如何应用](how-to-manage-model.md)新学习策略。 如果你不想选择学习策略，可以根据当前事件，使用[脱机评估](how-to-offline-evaluation.md)来建议学习策略。


## <a name="security"></a>安全性

### <a name="the-api-key-for-my-loop-has-been-compromised-what-can-i-do"></a>我的循环的 API 密钥已被入侵。 我该怎么办？

在交换客户端以使用另一个密钥后，你可以重新生成一个密钥。 通过使用两个密钥，可以延迟传播密钥，而无需任何停机时间。 建议定期执行此操作，将它作为一种安全措施。


## <a name="next-steps"></a>后续步骤

[配置模型更新频率](how-to-settings.md#model-update-frequency)