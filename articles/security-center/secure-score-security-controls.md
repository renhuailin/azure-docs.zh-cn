---
title: Azure 安全中心的安全评分
description: 介绍 Azure 安全中心的安全评分及其安全控制
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetd: c42d02e4-201d-4a95-8527-253af903a5c6
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2021
ms.author: memildin
ms.openlocfilehash: 24822777b06fadf87ca446d9b7ff8ba4df34adc5
ms.sourcegitcommit: 49ea056bbb5957b5443f035d28c1d8f84f5a407b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/09/2021
ms.locfileid: "100007664"
---
# <a name="secure-score-in-azure-security-center"></a>Azure 安全中心的安全评分

## <a name="introduction-to-secure-score"></a>安全评分简介

Azure 安全中心有两个主要目标： 

- 帮助你了解当前的安全状况
- 帮助你有效提高安全性

使你能够实现这些目标的安全中心的核心功能是安全功能分数。

安全中心会持续评估资源、订阅和组织的安全问题。 然后，它将所有调查结果汇总成一个分数，让你可以一目了然地了解当前的安全状况：分数越高，识别出的风险级别就越低。

Azure 门户页面显示的安全功能分数为百分比值，但原值也一目了然：

:::image type="content" source="./media/secure-score-security-controls/single-secure-score-via-ui.png" alt-text="门户中显示的总体安全功能分数":::

若要提高安全性，请查看安全中心的建议页面，了解提高分数需要采取的有效措施。 每项建议都包含有助于你修正特定问题的说明。

建议会被分组到各项安全控件中。 每个控件都是相关安全建议的逻辑组，反映易受攻击的攻击面。 只有修正控制中针对单个资源的所有建议后，分数才会提高。 若要查看你的组织对每个单独攻击面的保护力度，请查看每个安全控件的分数。

有关详细信息，请参阅下面的[如何计算安全功能分数](secure-score-security-controls.md#how-your-secure-score-is-calculated)。 


## <a name="access-your-secure-score"></a>访问安全功能分数

可以通过 Azure 门户或以编程方式查找总体安全功能分数以及每个订阅的分数，如以下各部分所述：

- [从门户获取安全功能分数](#get-your-secure-score-from-the-portal)
- [从 REST API 获取安全功能分数](#get-your-secure-score-from-the-rest-api)
- [从 Azure Resource Graph (ARG) 获取安全功能分数](#get-your-secure-score-from-azure-resource-graph-arg)

### <a name="get-your-secure-score-from-the-portal"></a>从门户获取安全功能分数

安全中心会在门户中突出显示你的分数：这是“安全中心”概述页面中显示的第一个主磁贴。 选择此磁贴，会转到专用安全功能分数页，其中显示按订阅细分的分数。 选择单个订阅可查看重要建议的详细列表，以及实现这些建议将对订阅分数产生的潜在影响。 

概括而言，你的安全功能分数将显示在安全中心门户页面的以下位置。

- 在安全中心的“概述”（主仪表板）上的磁贴中：

    :::image type="content" source="./media/secure-score-security-controls/score-on-main-dashboard.png" alt-text="安全中心仪表板上的安全功能分数":::

- 在 "专用 **安全分数** " 页中，你可以查看你的订阅和管理组的安全评分：

    :::image type="content" source="./media/secure-score-security-controls/score-on-dedicated-dashboard.png" alt-text="安全中心安全分数页面上的订阅安全分数":::

    :::image type="content" source="./media/secure-score-security-controls/secure-score-management-groups.png" alt-text="安全中心安全分数页面上管理组的安全评分":::

    > [!NOTE]
    > 你没有足够权限的任何管理组会将其分数显示为 "受限"。 

- 在“建议”页面的顶部：

    :::image type="content" source="./media/secure-score-security-controls/score-on-recommendations-page.png" alt-text="安全中心建议页面上的安全功能分数":::

### <a name="get-your-secure-score-from-the-rest-api"></a>从 REST API 获取安全功能分数

可以通过安全功能分数 API 访问分数。 通过 API 方法，可灵活地查询数据，久而久之构建自己的安全功能分数报告机制。 例如，你可以使用[安全功能分数 API](/rest/api/securitycenter/securescores) 来获取特定订阅的分数。 此外，你可以使用 [ API](/rest/api/securitycenter/securescorecontrols) 列出订阅的安全控件和当前分数。

![正在通过 API 检索单个安全功能分数](media/secure-score-security-controls/single-secure-score-via-api.png)

有关构建在安全功能分数 API 之上的工具示例，请参阅 [GitHub 社区的安全功能分数区域](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score)。 

### <a name="get-your-secure-score-from-azure-resource-graph-arg"></a>从 Azure Resource Graph (ARG) 获取安全功能分数

使用 Azure Resource Graph (ARG)，可以通过可靠的筛选、分组和排序功能，快速访问你的云环境中的资源信息。 这是以编程方式或从 Azure 门户中查询 Azure 订阅中的信息的一种快速且有效的方式。 [详细了解 Azure Resource Graph](../governance/resource-graph/index.yml)。

使用 ARG 访问多个订阅的安全功能分数：

1. 在 Azure 门户中，打开 Azure Resource Graph Explorer。

    :::image type="content" source="./media/security-center-identity-access/opening-resource-graph-explorer.png" alt-text="启动 Azure Resource Graph Explorer 建议页面" :::

1. 输入你的 Kusto 查询（使用下面的示例作为指导）。

    - 此查询返回订阅 ID、当前分数（以分数和百分比表示）以及订阅的最大分数。 

        ```kusto
        SecurityResources 
        | where type == 'microsoft.security/securescores' 
        | extend current = properties.score.current, max = todouble(properties.score.max)
        | project subscriptionId, current, max, percentage = ((current / max)*100)
        ```

    - 该查询返回所有安全控件的状态。 对于每个控件，你将获得运行不正常资源的数量、当前分数和最高分数。 

        ```kusto
        SecurityResources 
        | where type == 'microsoft.security/securescores/securescorecontrols'
        | extend SecureControl = properties.displayName, unhealthy = properties.unhealthyResourceCount, currentscore = properties.score.current, maxscore = properties.score.max
        | project SecureControl , unhealthy, currentscore, maxscore
        ```

1. 选择“运行查询”。




## <a name="tracking-your-secure-score-over-time"></a>跟踪一段时间内的安全评分

如果你是具有 Pro 帐户的 Power BI 用户，则可以使用“一段时间内的安全评分”Power BI 面板跟踪一段时间内的安全评分，并调查任何更改。

> [!TIP]
> 可以在 GitHub 上 Azure 安全中心社区的专门区域中找到此面板，以及可以通过编程方式使用安全评分的其他工具： https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score

该面板包含以下两个报表，可帮助你分析安全状态：

- **资源摘要** - 提供有关资源运行状况的摘要数据。
- **安全评分摘要** - 提供有关评分进度的摘要数据。 使用“每个订阅一段时间内的安全评分”图表查看评分的变化。 如果发现评分发生了巨大变化，请检查“检测到的可能会影响你安全评分的更改”表，以查找可能引起变化的可能更改。 此表显示了已删除的资源、新部署的资源或其安全状态针对其中一项建议发生了更改的资源。

:::image type="content" source="./media/secure-score-security-controls/power-bi-secure-score-dashboard.png" alt-text="随时间推移的可选安全评分 Power BI 仪表板跟踪安全分数并调查更改":::





## <a name="how-your-secure-score-is-calculated"></a>如何计算安全功能分数 

建议页面上清楚地显示了每个安全控制对总体安全评分的贡献。

[![安全评分增强版引入了安全控制](media/secure-score-security-controls/security-controls.png)](media/secure-score-security-controls/security-controls.png#lightbox)

若要获得某个安全控制所有可能的分数，你的所有资源都必须符合该安全控制中的所有安全建议。 例如，安全中心针对如何保护管理端口提供了多条建议。 现在必须修正所有建议，才能改变安全评分。

例如，名为“应用系统更新”的安全控制的最高分为 6 分，你可以在该控制可能增加的分数值上的工具提示中看到它：

[![安全控制“应用系统更新”](media/secure-score-security-controls/apply-system-updates-control.png)](media/secure-score-security-controls/apply-system-updates-control.png#lightbox)

此控制（应用系统更新）的最高分始终为 6 分。 此示例中一共有 50 个资源。 因此，我们将最高分除以 50，结果是每个资源贡献 0.12 分。 

* **可能增加的分数**（0.12 x 8 个运行不正常的资源 = 0.96）- 该控制中剩余可增加的分数。 如果修正此控制中的所有建议，分数将增加 2%（本例中为 0.96 分，四舍五入为 1 分）。 
* **当前分数**（0.12 x 42 个正常运行的资源 = 5.04）- 此控制的当前分数。 每个控制都为总分贡献分数。 在此示例中，该控制为当前安全总分贡献了 5.04 分。
* **最高分** - 完成某个控制中的所有建议后可获得的最高分数。 控制的最高分表明该控制的相对重要性。 可使用最高分值来会审要优先处理的问题。 


### <a name="calculations---understanding-your-score"></a>计算 - 了解你的分数

|指标|公式和示例|
|-|-|
|**安全控制的当前分数**|<br>![用于计算安全控件分数的公式](media/secure-score-security-controls/secure-score-equation-single-control.png)<br><br>每一个安全控制都计入安全评分。 受控制中的建议影响的每个资源都计入控制的当前分数。 各个控制的当前分数是对该控制中资源状态的度量。<br>![工具提示显示了计算安全控制的当前分数时使用的值](media/secure-score-security-controls/security-control-scoring-tooltips.png)<br>在此示例中，最高分 6 将除以 78，因为这是正常运行的资源和运行不正常的资源的总和。<br>6/78 = 0.0769<br>将其乘以正常运行的资源数量 (4) 可得出当前分数：<br>0.0769 * 4 = 0.31<br><br>|
|**安全评分**<br>一个订阅|<br>![用于计算订阅的安全评分的公式](media/secure-score-security-controls/secure-score-equation-single-sub.png)<br><br>![启用了所有控制的单个订阅的安全评分](media/secure-score-security-controls/secure-score-example-single-sub.png)<br>在此示例中，单个订阅启用了所有安全控制（可能的最高分为 60 分）。 该分数显示了可能的最高分 60 分中的 28 分，其余 32 分反映在安全控制的“可能增加的分数”数字中。<br>![控制和可能增加的分数的列表](media/secure-score-security-controls/secure-score-example-single-sub-recs.png)|
|**安全评分**<br>多个订阅|<br>![用于计算多个订阅的安全评分的公式](media/secure-score-security-controls/secure-score-equation-multiple-subs.png)<br><br>在计算多个订阅的综合得分时，安全中心将加入每个订阅的“权重”。 订阅的相对权重由安全中心基于资源数量等因素来决定。<br>每个订阅的当前分数的计算方式与单个订阅的计算方式相同，但会按公式所示应用权重。<br>查看多个订阅时，安全评分会计算所有已启用策略中的所有资源，并将其对每个安全控制的最高分的综合影响进行分组。<br>![启用了所有控制的多个订阅的安全评分](media/secure-score-security-controls/secure-score-example-multiple-subs.png)<br>综合得分不是平均值，而是指所有订阅中所有资源状态的计算状况。<br>同样，在这里，如果转到建议页面并将可能得到的分数相加，你会发现结果是当前分数 (24) 与最高得分 (60) 之差。|
||||

### <a name="which-recommendations-are-included-in-the-secure-score-calculations"></a>安全功能分数计算中包括哪些建议？

只有内置建议才会影响安全评分。

计算安全分数时不包括标记为“预览”的建议。 仍应尽可能按这些建议修正，以便在预览期结束时，它们会有助于提升评分。

预览建议示例如下：

:::image type="content" source="./media/secure-score-security-controls/example-of-preview-recommendation.png" alt-text="带有预览标志的建议":::

## <a name="improve-your-secure-score"></a>提高安全分数

若要提高安全评分，请修正建议列表中的安全建议。 既可以为每个资源手动修正每个建议，也可以使用“快速修复!” 选项（如果有）对一组资源快速应用建议修正。 有关详细信息，请参阅[修正建议](security-center-remediate-recommendations.md)。

改善分数并确保用户不会创建对分数产生负面影响的资源的另一种方法是在相关建议上配置 "强制" 和 "拒绝" 选项。 有关详细信息，请参阅[使用“强制执行/拒绝”建议防止错误配置](prevent-misconfigurations.md)。

## <a name="security-controls-and-their-recommendations"></a>安全控制及其建议

下表列出了 Azure 安全中心的安全控制。 对于每个控制，可以看到为所有资源修正该控制中列出的所有建议后，安全评分可以增加的最高分数。 

安全中心提供的安全建议是针对每个组织环境中的可用资源量身定制的。 可以通过 [禁用策略](tutorial-security-policy.md#disable-security-policies-and-disable-recommendations) 并 [从建议中豁免特定资源](exempt-resource.md)来进一步自定义建议。 
 
建议每个组织仔细检查其分配的 Azure Policy 计划。 

> [!TIP]
> 有关查看和编辑你的计划的详细信息，请参阅[使用安全策略](tutorial-security-policy.md)。 

即使安全中心的默认安全计划是基于行业最佳做法和标准的，但在某些情况下，下面列出的内置建议可能并不完全适合你的组织。 因此，有时有必要调整默认计划，而又不影响安全性，以确保其与组织自己的策略保持一致。 你必须符合的行业标准、法规标准和基准。<br><br>
<div class="foo">

<style type="text/css"> .tg  {border-collapse:collapse;border-spacing:0;} .tg td{border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px; overflow:hidden;padding:10px 5px;word-break:normal;} .tg th{border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:18px; font-weight:normal;overflow:hidden;padding:10px 5px;word-break:normal;} .tg .tg-cly1{text-align:left;vertical-align:middle} .tg .tg-lboi{border-color:inherit;text-align:left;vertical-align:middle} </style>

[!INCLUDE [security-center-controls-and-recommendations](../../includes/asc/security-control-recommendations.md)]

</div>




## <a name="secure-score-faq"></a>安全评分 FAQ

### <a name="if-i-address-only-three-out-of-four-recommendations-in-a-security-control-will-my-secure-score-change"></a>如果仅处理某个安全控制四分之三的建议，安全评分是否会变化？
否。 为单个资源修正所有建议后，安全评分才会变化。 若要获得某个控制的最高分，必须为所有资源修正所有建议。

### <a name="if-a-recommendation-isnt-applicable-to-me-and-i-disable-it-in-the-policy-will-my-security-control-be-fulfilled-and-my-secure-score-updated"></a>如果某个建议对我不适用，我在策略中禁用它，我能否达到安全控制的要求，我的安全评分是否会更新？
是的。 如果建议不适用于你的环境，建议禁用它们。 有关如何禁用特定建议的说明，请参阅[禁用安全策略](./tutorial-security-policy.md#disable-security-policies-and-disable-recommendations)。

### <a name="if-a-security-control-offers-me-zero-points-towards-my-secure-score-should-i-ignore-it"></a>如果某个安全控制为安全评分贡献的分数为零，我应该忽略它吗？
在某些情况下，你会看到某个控制的最高分大于零，但影响为零。 如果通过修复资源增加的分数可忽略不计，则会将其舍入为零。 请勿忽略这些建议，因为它们仍然可以改善安全性。 唯一的例外是“其他最佳做法”控制。 修正这些建议不会提高分数，但会提高整体安全性。

## <a name="next-steps"></a>后续步骤

本文介绍了安全评分及其引入的安全控制。 如需相关材料，请参阅以下文章：

- [了解建议的不同元素](security-center-recommendations.md)
- [了解如何修正建议](security-center-remediate-recommendations.md)
- [查看基于 GitHub 的工具以便以编程方式使用安全评分](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score)