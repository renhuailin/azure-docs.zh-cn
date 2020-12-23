---
title: 使用顾问评分优化 Azure 工作负荷
description: 使用 Azure 顾问评分充分利用 Azure。
ms.topic: article
ms.date: 09/09/2020
ms.openlocfilehash: 11b20bc3b4d604d3a7ff4608cd1c21f129c1cb6d
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/17/2020
ms.locfileid: "97630116"
---
# <a name="optimize-azure-workloads-by-using-advisor-score"></a>使用顾问评分优化 Azure 工作负荷

## <a name="introduction-to-advisor-score"></a>顾问评分简介

Azure 顾问提供适用于你的工作负荷的最佳实践建议。 这些建议是个性化且可操作的，可帮助你：

* 改进工作负荷的状况并优化 Azure 部署。
* 遵循最佳做法主动防止顶级问题。
* 针对 [Microsoft Azure Well-Architected 框架](/azure/architecture/framework/)的五个要素评估 Azure 工作负荷。

作为顾问的核心功能，顾问分数有助于有效有效地实现这些目标。

若要充分利用 Azure，了解工作负荷优化旅程的位置至关重要。 你需要知道哪些服务或资源使用良好，哪些不是。 此外，还需要了解如何根据建议确定操作的优先级，以最大程度地提高结果。

跟踪和报告此优化旅程的进度也很重要。 利用 Advisor 评分，你可以通过新的游戏化体验轻松完成所有这些操作。

作为个性化的云顾问，Azure 顾问会持续评估使用情况遥测和资源配置，以检查行业最佳实践。 然后，顾问将其发现聚合为单个分数。 通过这一评分，你可以一目了然地了解是否需要使用必要的步骤来构建可靠、安全且经济高效的解决方案。

顾问评分包含总体分数，可以进一步细分为五个类别的分数。 每个顾问类别的一个分数表示 Well-Architected 框架的五个支柱。

您可以通过查看每日、每周和每月趋势的总体分数和类别分数，来跟踪一段时间内的进度。 您还可以设置基准来帮助您实现目标。

 ![显示 "顾问评分" 页的屏幕截图。](./media/advisor-score-1.png)

## <a name="interpret-an-advisor-score"></a>解释顾问分数

顾问显示总体顾问分数和顾问类别的细分，以百分比表示。 任何类别的分数为100%，表示由 Advisor 评估的所有资源都遵循顾问建议的最佳实践。 在该色谱的另一端，分数为0% 表示由顾问评估的资源均不遵循顾问的建议。 使用这些评分粒度，可以轻松实现以下流程：

* **顾问分数** 可帮助你根据顾问评分来比较工作负荷或订阅的工作方式。 你还可以查看历史趋势，了解你的走向。
* **按类别** 对每个建议进行评分，告诉你哪些未完成的建议将提高你的分数。 这些值反映了建议的权重和实现的预测缓动。 这些因素有助于确保最大程度地利用你的时间。 它们还可帮助你确定优先级。
* 每个建议的 **类别分数影响** 有助于你确定每个类别的修正操作的优先级。

每个建议对你的类别分数的贡献在 Azure 门户的 " **顾问评分** " 页上清楚地显示。 您可以按 " **潜在评分增加** " 列中列出的百分比点来增加每个类别分数。 此值反映了该类别中的建议权重，并预测了实现的易预测性，以解决可能最简单的任务。 将重点放在最重要的分数影响上的建议将有助于您最大程度地提高工作进度。

![显示顾问分数影响的屏幕截图。](./media/advisor-score-2.png)

如果任何顾问建议对于单个资源不相关，则可以推迟或消除这些建议。 它们将在下一次刷新时从分数计算中排除。 顾问还将使用此输入作为附加反馈来改进模型。

## <a name="how-is-an-advisor-score-calculated"></a>顾问分数如何计算？

顾问以百分比形式显示你的类别分数和总体顾问分数。 任何类别的分数为100% 表示 *由顾问评估* 的所有资源，请遵循顾问建议的最佳实践。 在该色谱的另一端，分数为0% 表示没有由顾问评估的资源遵循顾问建议。

**这五个类别中的每一个都有最高的潜在分数100。** 总体顾问分数计算为每个适用的类别分数的总和除以所有适用类别的最高潜在分数之和。 对于大多数订阅，这意味着顾问增加每个类别的分数并除以500。 但 *仅当使用由顾问评估的资源时，才计算每个类别分数*。

### <a name="advisor-score-calculation-example"></a>顾问评分计算示例

* **单个订阅分数：** 此示例是订阅的所有顾问类别分数的简单平均值。 如果顾问类别分数为- **费用** = 73， **可靠性** = 85， **卓越运营** = 77，且 **性能** = 100，则顾问分数将 (73 + 85 + 77 + 100) / (4x100) = 0.84% 或84%。
* **多个订阅分数：** 选择多个订阅时，生成的总体顾问分数为加权聚合类别分数。 此处，每个顾问类别分数基于订阅使用的资源进行聚合。 在 Advisor 具有加权的分类分数后，顾问将执行简单的平均计算，以便为你提供订阅的整体分数。

### <a name="scoring-methodology"></a>评分方法

可以通过四个步骤汇总顾问评分的计算：

1. 顾问计算 *受影响资源的零售成本*。 这些资源是订阅中至少有一项顾问建议的资源。
1. 顾问计算 *评估资源的零售成本*。 这些资源由顾问监视，无论它们是否有任何建议。
1. 对于每个建议类型，顾问将计算 *正常的资源比率*。 此比率是受影响资源的零售成本除以评估资源的零售成本。
1. 顾问将三个额外的权重应用于每个类别中的正常资源比率：

   * 对于影响更大的建议，其权重比不影响的建议更小。
   * 具有长期建议的资源将会更多地统计你的成绩。
   * 在 Advisor 中推迟或消除的资源将从分数计算中彻底删除。

顾问在顾问类别级别应用此模型，以便为每个类别提供顾问分数。 **安全性** 使用 [安全评分](../security-center/secure-score-security-controls.md#introduction-to-secure-score) 模型。 简单的平均生成最终的顾问分数。

## <a name="advisor-score-faqs"></a>顾问评分常见问题

### <a name="how-often-is-my-score-refreshed"></a>评分的刷新频率是多少？

每日至少刷新一次。

### <a name="why-do-some-recommendations-have-the-empty---value-in-the-category-score-impact-column"></a>为什么某些建议在 "分类分数影响" 列中的值为空？

顾问不会立即将新的建议或建议添加到计分模型中最近的更改。 经过一段较短的评估期（通常是几周）后，分数就会包括在内。

### <a name="why-is-the-cost-score-impact-greater-for-some-recommendations-even-if-they-have-lower-potential-savings"></a>为什么对于某些建议，成本评分会受到更大的影响，即使它们具有更低的可能节约？

你的 **成本** 分数反映了你在利用率较低的资源方面的潜在节约，并预测了实现这些建议的便利。 例如，额外权重适用于已闲置较长时间的受影响资源，即使可能节省的成本也较低。

### <a name="why-dont-i-have-a-score-for-one-or-more-categories-or-subscriptions"></a>为什么不是一个或多个类别或订阅的评分？

Advisor 只为具有顾问评估的资源的类别和订阅生成分数。

### <a name="what-if-a-recommendation-isnt-relevant"></a>如果建议不相关，该怎么办？

如果从 Advisor 关闭建议，则会从评分的计算中省略该建议。 消除建议还有助于 Advisor 提高建议质量。

### <a name="why-did-my-score-change"></a>为什么分数发生了变化？

如果通过采用顾问建议的最佳实践来更正受影响的资源，则分数可能会改变。 如果你或拥有订阅权限的任何人修改或创建了新资源，则你可能还会看到你的分数波动。 你的分数基于与所有资源的总成本相关的成本影响资源的比率。

### <a name="how-does-advisor-calculate-the-retail-cost-of-resources-on-a-subscription"></a>顾问如何计算订阅上资源的零售成本？

顾问使用 [Azure 定价](https://azure.microsoft.com/pricing/)上公布的现用现付费率。 这些费率不反映任何适用的折扣。 然后，将速率乘以分配该资源的最后一天的使用量。 省略资源成本计算的折扣使顾问分数在不同订阅、租户和注册之间相当相似，折扣可能会有所不同。

### <a name="do-i-need-to-view-the-recommendations-in-advisor-to-get-points-for-my-score"></a>是否需要查看 Advisor 中的建议才能获得评分的要点？

不能。 你的评分反映了你是否采用顾问推荐的最佳做法，即使你事先采取了这些最佳做法，也不会在 Advisor 中查看建议。

### <a name="does-the-scoring-methodology-differentiate-between-production-and-dev-test-workloads"></a>评分方法是否区分生产和开发测试工作负载？

不是。 但是，如果这些资源用于开发和测试，并且建议不适用，则可以取消对单独资源的建议。

### <a name="can-i-compare-scores-between-a-subscription-with-100-resources-and-a-subscription-with-100000-resources"></a>是否可以比较包含100资源的订阅与包含100000资源的订阅之间的分数？

评分方法旨在控制订阅和服务组合上的资源数。 具有更少资源的订阅的分数比具有更多资源的订阅的分数更高或更低。

### <a name="what-does-it-mean-when-i-see-coming-soon-in-the-score-impact-column"></a>当我在 "分数影响" 列中看到 "即将到来" 时，这是什么意思？

此消息表示建议是新的，我们正在努力将其引入顾问评分模型。 在分数计算中考虑此新建议后，你将看到建议的 "分数影响" 值。

### <a name="does-my-score-depend-on-how-much-i-spend-on-azure"></a>我的分数是否取决于我在 Azure 上花费了多少？

不能。 您的分数并不一定反映您所花费的时间。 不必要的支出会导致 **成本** 降低。

## <a name="access-advisor-score"></a>访问顾问分数

顾问评分处于 Azure 门户的公共预览版中。 在左窗格中的 " **顾问** " 部分下，参阅 " **顾问评分**"。

![显示顾问评分入口点的屏幕截图。](./media/advisor-score-3.png)

## <a name="next-steps"></a>后续步骤

有关顾问建议的详细信息，请参阅以下资源：

* [顾问简介](advisor-overview.md)
* [顾问入门](advisor-get-started.md)
* [顾问成本建议](advisor-cost-recommendations.md)
* [顾问性能建议](advisor-performance-recommendations.md)
* [顾问安全性建议](advisor-security-recommendations.md)
* [顾问卓越运营建议](advisor-operational-excellence-recommendations.md)
