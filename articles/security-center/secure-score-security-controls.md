---
title: Azure 安全中心的安全评分
description: 介绍 Azure 安全中心的安全评分及其安全控制
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: article
ms.date: 09/01/2021
ms.author: memildin
ms.openlocfilehash: de0983d981aa4bc229f29e151c37cda0be90cdec
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123434676"
---
# <a name="secure-score-in-azure-security-center"></a>Azure 安全中心的安全评分

## <a name="introduction-to-secure-score"></a>安全评分简介

Azure 安全中心有两个主要目标： 

- 帮助你了解当前的安全状况
- 帮助你有效提高安全性

使你能够实现这些目标的安全中心的核心功能是安全功能分数。

安全中心会持续评估资源、订阅和组织的安全问题。 然后，它将所有调查结果汇总成一个分数，让你可以一目了然地了解当前的安全状况：分数越高，识别出的风险级别就越低。

Azure 门户页面显示的安全功能分数为百分比值，但原值也一目了然：

:::image type="content" source="./media/secure-score-security-controls/single-secure-score-via-ui.png" alt-text="门户中显示的总体安全分数。":::

若要提高安全性，请查看安全中心的建议页面，了解提高分数需要采取的有效措施。 每项建议都包含有助于你修正特定问题的说明。

建议会被分组到各项安全控件中。 每个控件都是相关安全建议的逻辑组，反映易受攻击的攻击面。 只有修正控制中针对单个资源的所有建议后，分数才会提高。 若要查看你的组织对每个单独攻击面的保护力度，请查看每个安全控件的分数。

有关详细信息，请参阅下面的[如何计算安全功能分数](secure-score-security-controls.md#how-your-secure-score-is-calculated)。 

## <a name="how-your-secure-score-is-calculated"></a>如何计算安全功能分数 

建议页面上清楚地显示了每个安全控制对总体安全评分的贡献。

:::image type="content" source="./media/secure-score-security-controls/security-controls.png" alt-text="Azure 安全中心的安全控制及其对安全功能分数的影响" lightbox="./media/secure-score-security-controls/security-controls.png":::

若要获得某个安全控制所有可能的分数，你的所有资源都必须符合该安全控制中的所有安全建议。 例如，安全中心针对如何保护管理端口提供了多条建议。 现在必须修正所有建议，才能改变安全评分。

### <a name="example-scores-for-a-control"></a>控制的示例分数

:::image type="content" source="./media/secure-score-security-controls/remediate-vulnerabilities-control.png" alt-text="应用系统更新安全控制。" lightbox="./media/secure-score-security-controls/remediate-vulnerabilities-control.png":::


在此示例中：

| #  | 名称                                           | 说明                                                                                                                                                                                                                                                                                                                                                                                                                                |
|:-:|------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 1 | 修正漏洞安全控制 | 此控制对多个与发现和解决已知漏洞相关的建议进行分组。                                                                                                                                                                                                                                                                                                                                   |
| 2 | 最高分数                                  | 实现控制中的所有建议后可以获得的最高分数。 控制的最高分表明该控制的相对重要性，并且对于每个环境都是固定的。 可使用最高分值来会审要优先处理的问题。<br>有关所有控制及其最高分的列表，请参阅[安全控制及其建议](#security-controls-and-their-recommendations)。 |
| 3 | 资源数                        | 有 35 个资源受此控制的影响。<br>若要了解每个资源可能的贡献份额，请将最高分除以资源数。<br>对于此示例，6/35=0.1714<br>每个资源贡献 0.1714 分。                                                                                                                                                                                          |
| 4 | 当前分数                              | 此控制的当前分数。<br>当前分数=[每个资源的分数]*[正常运行的资源数]<br> 0.1714 x 5 个正常运行的资源 = 0.86<br>每个控制都为总分贡献分数。 在此示例中，该控制为当前安全功能总分贡献了 0.86 分。                                                                                                                                               |
| 5 | 潜在分数增加                   | 控制中可提升的剩余分值。 如果实现此控制中的所有建议，分数将增加 9%。<br>潜在分数增加=[每个资源的分数]*[运行不正常的资源数]<br> 0.1714 x 30 个运行不正常的资源 = 5.14<br>                                                                                                                                                        |
|   |                                                |                                                                                                                                                                                                                                                                                                                                                                                                                                            |



### <a name="calculations---understanding-your-score"></a>计算 - 了解你的分数

|指标|公式和示例|
|-|-|
|**安全控制的当前分数**|<br>![用于计算安全控件分数的公式。](media/secure-score-security-controls/secure-score-equation-single-control.png)<br><br>每一个安全控制都计入安全评分。 受控制中的建议影响的每个资源都计入控制的当前分数。 各个控制的当前分数是对该控制中资源状态的度量。<br>![工具提示显示了计算安全控制的当前分数时使用的值](media/secure-score-security-controls/security-control-scoring-tooltips.png)<br>在此示例中，最高分 6 将除以 78，因为这是正常运行的资源和运行不正常的资源的总和。<br>6/78 = 0.0769<br>将其乘以正常运行的资源数量 (4) 可得出当前分数：<br>0.0769 * 4 = 0.31<br><br>|
|**安全评分**<br>一个订阅|<br>![用于计算订阅的安全评分的公式](media/secure-score-security-controls/secure-score-equation-single-sub.png)<br><br>![启用了所有控制的单个订阅的安全评分](media/secure-score-security-controls/secure-score-example-single-sub.png)<br>在此示例中，单个订阅启用了所有安全控制（可能的最高分为 60 分）。 该分数显示了可能的最高分 60 分中的 28 分，其余 32 分反映在安全控制的“可能增加的分数”数字中。<br>![控制和可能增加的分数的列表](media/secure-score-security-controls/secure-score-example-single-sub-recs.png)|
|**安全评分**<br>多个订阅|<br>![用于计算多个订阅的安全评分的公式。](media/secure-score-security-controls/secure-score-equation-multiple-subs.png)<br><br>在计算多个订阅的综合得分时，安全中心将加入每个订阅的“权重”。 订阅的相对权重由安全中心基于资源数量等因素来决定。<br>每个订阅的当前分数的计算方式与单个订阅的计算方式相同，但会按公式所示应用权重。<br>查看多个订阅时，安全评分会计算所有已启用策略中的所有资源，并将其对每个安全控制的最高分的综合影响进行分组。<br>![启用了所有控制的多个订阅的安全评分](media/secure-score-security-controls/secure-score-example-multiple-subs.png)<br>综合得分不是平均值，而是指所有订阅中所有资源状态的计算状况。<br>同样，在这里，如果转到建议页面并将可能得到的分数相加，你会发现结果是当前分数 (24) 与最高得分 (60) 之差。|


### <a name="which-recommendations-are-included-in-the-secure-score-calculations"></a>安全功能分数计算中包括哪些建议？

只有内置建议才会影响安全评分。

计算安全分数时不包括标记为“预览”的建议。 仍应尽可能按这些建议修正，以便在预览期结束时，它们会有助于提升评分。

预览建议示例如下：

:::image type="content" source="./media/secure-score-security-controls/example-of-preview-recommendation.png" alt-text="带有预览标志的建议。":::

## <a name="improve-your-secure-score"></a>提高安全分数

若要提高安全评分，请修正建议列表中的安全建议。 可以为每个资源手动修正每个建议，也可以使用“修复”选项（若可用）快速解决多个资源上的问题。 有关详细信息，请参阅[修正建议](security-center-remediate-recommendations.md)。

要改善分数并确保用户不会创建对分数产生负面影响的资源，另一种方法是在相关建议上配置“强制”和“拒绝”选项。 有关详细信息，请参阅[使用“强制执行/拒绝”建议防止错误配置](prevent-misconfigurations.md)。

## <a name="security-controls-and-their-recommendations"></a>安全控制及其建议

下表列出了 Azure 安全中心的安全控制。 对于每个控制，可以看到为所有资源修正该控制中列出的所有建议后，安全评分可以增加的最高分数。 

通过安全中心提供的安全建议集是针对每个组织的环境中的可用资源量身定制的。 可以通过[禁用策略](tutorial-security-policy.md#disable-security-policies-and-disable-recommendations)并[从建议中排除特定资源](exempt-resource.md)来进一步自定义建议。 
 
建议每个组织仔细检查其分配的 Azure Policy 计划。 

> [!TIP]
> 有关查看和编辑你的计划的详细信息，请参阅[使用安全策略](tutorial-security-policy.md)。 

即使安全中心的默认安全计划是基于行业最佳做法和标准的，但在某些情况下，下面列出的内置建议可能并不完全适合你的组织。 因此，有时有必要在不影响安全性的情况下调整默认计划，以确保其与组织自己的策略、行业标准、监管标准和你必须满足的基准保持一致。<br><br>
<div class="foo">

<style type="text/css"> .tg  {border-collapse:collapse;border-spacing:0;} .tg td{border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px; overflow:hidden;padding:10px 5px;word-break:normal;} .tg th{border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:18px; font-weight:normal;overflow:hidden;padding:10px 5px;word-break:normal;} .tg .tg-cly1{text-align:left;vertical-align:middle} .tg .tg-lboi{border-color:inherit;text-align:left;vertical-align:middle} </style>

[!INCLUDE [security-center-controls-and-recommendations](../../includes/asc/security-control-recommendations.md)]

</div>




## <a name="faq---secure-score"></a>常见问题解答 - 安全分数

### <a name="if-i-address-only-three-out-of-four-recommendations-in-a-security-control-will-my-secure-score-change"></a>如果仅处理某个安全控制四分之三的建议，安全评分是否会变化？
不是。 为单个资源修正所有建议后，安全评分才会变化。 若要获得某个控制的最高分，必须为所有资源修正所有建议。

### <a name="if-a-recommendation-isnt-applicable-to-me-and-i-disable-it-in-the-policy-will-my-security-control-be-fulfilled-and-my-secure-score-updated"></a>如果某个建议对我不适用，我在策略中禁用它，我能否达到安全控制的要求，我的安全评分是否会更新？
是的。 如果建议不适用于你的环境，建议禁用它们。 有关如何禁用特定建议的说明，请参阅[禁用安全策略](./tutorial-security-policy.md#disable-security-policies-and-disable-recommendations)。

### <a name="if-a-security-control-offers-me-zero-points-towards-my-secure-score-should-i-ignore-it"></a>如果某个安全控制为安全评分贡献的分数为零，我应该忽略它吗？
在某些情况下，你会看到某个控制的最高分大于零，但影响为零。 如果通过修复资源增加的分数可忽略不计，则会将其舍入为零。 请勿忽略这些建议，因为它们仍然可以改善安全性。 唯一的例外是“其他最佳做法”控制。 修正这些建议不会提高分数，但会提高整体安全性。

## <a name="next-steps"></a>后续步骤

本文介绍了安全分数及包含的安全控件。 

> [!div class="nextstepaction"]
> [访问和跟踪安全分数](secure-score-access-and-track.md)

如需相关材料，请参阅以下文章：

- [了解建议的不同元素](security-center-recommendations.md)
- [了解如何修正建议](security-center-remediate-recommendations.md)
- [查看基于 GitHub 的工具以便以编程方式使用安全评分](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score)