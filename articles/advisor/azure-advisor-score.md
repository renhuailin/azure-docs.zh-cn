---
title: 使用顾问分数优化 Azure 工作负载
description: 使用 Azure 顾问分数充分利用 Azure。
ms.topic: article
ms.date: 09/09/2020
ms.openlocfilehash: e439e5283f2d311d9efb3b30fe157750d589664e
ms.sourcegitcommit: 32ee8da1440a2d81c49ff25c5922f786e85109b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2021
ms.locfileid: "109787916"
---
# <a name="optimize-azure-workloads-by-using-advisor-score"></a>使用顾问分数优化 Azure 工作负载

## <a name="introduction-to-advisor-score"></a>顾问分数简介

Azure 顾问分数可为工作负载推荐最佳做法。 这些建议是因人而异，可操作性强，以便帮助你：

* 改进工作负载的状况并优化 Azure 部署。
* 遵循最佳做法，主动防范常见问题。
* 按照 [Microsoft Azure 良好架构的框架](/azure/architecture/framework/)中五大支柱，评估 Azure 工作负载。

作为顾问的核心功能，顾问分数可帮助你有效且高效地实现这些目标。

要充分利用 Azure，了解自己在工作负载优化旅程中所处的状况至关重要。 你需要知道哪些服务或资源使用良好以及哪些不好。 而且，还需要了解如何根据建议确定操作的优先级，以最大程度地优化结果。

此外，跟踪和报告此优化旅程的进度也很重要。 使用顾问分数，可以全新的游戏化体验轻松完成所有这些操作。

作为个性化的云顾问，Azure 顾问会持续评估使用情况遥测和资源配置，以检查行业最佳做法。 然后，顾问将其发现聚合为单个分数。 通过该分数，可以一目了然地了解是否需要采取必要的步骤来构建可靠、安全且经济高效的解决方案。

顾问分数包含一个总分，可以进一步细分为五个类别的分数。 每个顾问类别一个分数，代表良好架构框架的五个支柱。

可以查看每日、每周和每月总分和类别分数的趋势，从而跟踪随时间变化的完成进度。 此外，还可以设置基准来帮助实现目标。

 ![显示“顾问分数”页面的屏幕截图。](./media/advisor-score-1.png)

## <a name="interpret-an-advisor-score"></a>解释顾问分数

顾问会显示顾问总分和顾问类别细分，以百分比表示。 任何类别的分数为 100%，则表示顾问评估的所有资源都在遵循顾问建议的最佳做法。 该频谱的另一端为分数 0%，表示顾问评估的所有资源都未遵循顾问的建议。 使用这些分数粒度，可以轻松实现以下流：

* 顾问分数可帮助你根据顾问分数来衡量工作负荷或订阅的工作情况。 此外，还可以查看历史趋势，了解未来走向。
* 通过每个建议的类别分数，可了解哪些出色的建议对提高分数的贡献最大。 这些值反映建议的权重和预测的实现便利性。 这些因素有助于确保充分利用时间。 还可帮助确定优先级。
* 每项建议的类别分数影响有助于确定各个类别的修正操作优先级。

Azure 门户的“顾问分数”页面会清楚显示每项建议对类别分数的贡献。 您可以按“潜在分数增加”列中列出的百分比点来增加每个类别的分数。 此值反映类别中建议的权重以及预测的实现便利性，以便解决潜在最容易的任务。 专注于分数影响最高的建议，有助于利用时间取得最大的进步。

![显示顾问分数影响的屏幕截图。](./media/advisor-score-2.png)

如有任何顾问建议与个体资源不相关，则可以推迟或取消这些建议。 下次刷新时将会从计算中去除这些项目。 顾问还会将此输入信息作为其他反馈，进而完善模型。

## <a name="how-is-an-advisor-score-calculated"></a>顾问分数如何计算？

顾问以百分比形式显示类别分数和顾问总分。 任何类别的分数为 100%，则表示顾问评估的所有资源都在遵循顾问建议的最佳做法。 该频谱的另一端为分数 0%，表示顾问评估的所有资源都未遵循顾问的建议。

**在这 5 个类别中，每个类别可能获得的最高分数都是 100。** 顾问总分的计算方式为，求每个适用类别的分数之和，除以所有适用类别的最高潜在分数之和。 对于大多数订阅，这意味着顾问将每个类别的分数相加，然后除以 500。 但只有使用了顾问会评估的资源时，才会计算每个类别分数。

### <a name="advisor-score-calculation-example"></a>顾问分数计算示例

* 单个订阅分数：此示例是计算订阅所有顾问类别分数的简单平均值。 如果顾问类别分数为：成本 = 73，可靠性 = 85，卓越运营 = 77，以及性能 = 100，则顾问分数为 (73 + 85 + 77 + 100)/(4x100) = 0.84% 或 84%。
* 多个订阅分数：选择多个订阅时，生成的顾问总分为加权后聚合的类别分数。 对此，根据订阅使用的资源聚合每个顾问类别分数。 顾问在获得加权后聚合的类别分数后，进行简单的平均值计算，再提供订阅总分。

### <a name="scoring-methodology"></a>评分方法

顾问分数的计算可以归结为四步：

1. 顾问计算受影响资源的零售成本。 这些资源是订阅中至少有一项顾问建议的资源。
1. 顾问计算所评估资源的零售成本。 这些资源无论是否有任何建议，都由顾问监视。
1. 对于每个建议类型，顾问将计算 *正常的资源比率*。 此比率由受影响资源的零售成本除以评估资源的零售成本得出。
1. 顾问向每个类别中的正常资源比率应用 3 个附加权重：

   * 建议的影响越大权重越高，影响越小权重越低。
   * 涉及长期建议的资源在分数中的权重较高。
   * 在顾问中推迟或关闭的资源将从分数计算中彻底删除。

顾问在顾问类别级别应用此模型，从而为每个类别提供一个顾问分数。 安全性 使用[安全评分](../security-center/secure-score-security-controls.md#introduction-to-secure-score)模型。 简单求平均值则可得到最终的顾问分数。

## <a name="advisor-score-faqs"></a>顾问评分 FAQ

### <a name="how-often-is-my-score-refreshed"></a>我的分数多久刷新一次？

分数至少每日刷新一次。

### <a name="why-do-some-recommendations-have-the-empty---value-in-the-category-score-impact-column"></a>为什么有些建议在类别分数影响列是“-”空值？

顾问不会在评分模型中立即包含新建议或最近发生的变化的建议。 经过一小段评估期（通常是几周）后，分数中就会包括它们。

### <a name="why-is-the-cost-score-impact-greater-for-some-recommendations-even-if-they-have-lower-potential-savings"></a>为什么有些建议的潜在节省额很低，成本分数影响却很大？

成本分数反映利用率较低资源的潜在节省额，以及预测的实现这些建议的便利性。 例如，长时间闲置的受影响资源会被赋予额外的权重，即使潜在节省额较低。

### <a name="why-dont-i-have-a-score-for-one-or-more-categories-or-subscriptions"></a>为什么有一个或多个类别或订阅没有分数？

顾问只为包含顾问所评估资源的类别和订阅生成分数。

### <a name="what-if-a-recommendation-isnt-relevant"></a>如果建议不相关，应该怎么办？

如果从顾问中取消建议，则计算分数会将其略过。 取消建议还有助于顾问改善建议质量。

### <a name="why-did-my-score-change"></a>为什么分数发生了变化？

如果通过采用顾问所建议的最佳做法修正了受影响的资源，则分数可能会改变。 如果你或拥有订阅权限的任何人修改或创建了新资源，也可能会看到分数波动。 你的分数基于成本受影响的资源相对于所有资源总成本的比率进行计算。

### <a name="how-does-advisor-calculate-the-retail-cost-of-resources-on-a-subscription"></a>顾问如何计算订阅中资源的零售成本？

顾问采用 [Azure 定价](https://azure.microsoft.com/pricing/)上公布的即用即付费率。 这些费率不反映任何适用的折扣。 然后，用这些费率乘以最后一天分配该资源时的使用量。 计算资源成本时省略折扣，才能让折扣可能不同的订阅、租户和注册的顾问分数具有可比性。

### <a name="do-i-need-to-view-the-recommendations-in-advisor-to-get-points-for-my-score"></a>是否需要查看顾问中的建议，才能为分数加分？

否。 评分反映你是否采用了顾问推荐的最佳做法，即使你主动采取了这些最佳做法，但从未再顾问中查看建议。

### <a name="does-the-scoring-methodology-differentiate-between-production-and-dev-test-workloads"></a>生产和开发测试工作负载的评分方法是否有所不同？

没有，目前没有不同。 但是，如果这些资源适用于开发和测试，并且建议不适用，可以针对单独资源取消建议。

### <a name="can-i-compare-scores-between-a-subscription-with-100-resources-and-a-subscription-with-100000-resources"></a>是否可以比较包含 100 个资源的订阅和包含 100,000 个资源的订阅的分数？

评分方法旨在控制订阅和服务组合中的资源数。 资源较少的订阅，分数比资源更多的订阅或高或低。

### <a name="what-does-it-mean-when-i-see-coming-soon-in-the-score-impact-column"></a>在“分数影响”列中看到“即将推出”意味着什么？

该消息表示建议是新的，我们正在努力将其引入顾问评分模型。 将此新建议纳入分数计算后，则会看到该建议的分数影响值。

### <a name="does-my-score-depend-on-how-much-i-spend-on-azure"></a>分数是否取决于我在 Azure 上的支出？

否。 分数不一定反映你的支出金额。 不必要的支出反而会导致成本分数降低。

## <a name="access-advisor-score"></a>访问顾问分数

顾问评分位于 Azure 门户的公共预览版中。 在左侧窗格中的“顾问”部分下可看到顾问分数。

![显示“顾问分数”入口点的屏幕截图。](./media/advisor-score-3.png)

## <a name="next-steps"></a>后续步骤

有关顾问建议的详细信息，请参阅以下资源：

* [顾问简介](advisor-overview.md)
* [顾问入门](advisor-get-started.md)
* [顾问成本建议](advisor-cost-recommendations.md)
* [顾问性能建议](advisor-performance-recommendations.md)
* [顾问安全性建议](advisor-security-recommendations.md)
* [顾问卓越运营建议](advisor-operational-excellence-recommendations.md)
