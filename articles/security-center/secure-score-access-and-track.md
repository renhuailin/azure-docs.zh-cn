---
title: 跟踪 Azure 安全中心中的安全功能分数
description: 了解在 Azure 安全中心访问和跟踪安全功能分数的多种方法。
author: memildin
ms.author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 02/25/2021
ms.openlocfilehash: 9373806b87144005407861b2043799f0c62ce982
ms.sourcegitcommit: f3b930eeacdaebe5a5f25471bc10014a36e52e5e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/16/2021
ms.locfileid: "112237911"
---
# <a name="access-and-track-your-secure-score"></a>访问和跟踪安全分数

可以通过 Azure 门户或以编程方式查找总体安全功能分数以及每个订阅的分数，如以下各部分所述：

> [!TIP]
> 有关如何计算分数的详细说明，请参阅[计算 - 了解分数](secure-score-security-controls.md#calculations---understanding-your-score)。

## <a name="get-your-secure-score-from-the-portal"></a>从门户获取安全功能分数

安全中心会在门户中突出显示你的分数：这是“安全中心”概述页面中显示的第一个主磁贴。 选择此磁贴，会转到专用安全功能分数页，其中显示按订阅细分的分数。 选择单个订阅可查看重要建议的详细列表，以及实现这些建议将对订阅分数产生的潜在影响。 

概括而言，你的安全功能分数将显示在安全中心门户页面的以下位置。

- 在安全中心的“概述”（主仪表板）上的磁贴中：

    :::image type="content" source="./media/secure-score-security-controls/score-on-main-dashboard.png" alt-text="安全中心仪表板上的安全功能分数":::

- 在专用的“安全功能分数”页面中，可查看订阅和管理组的安全功能分数：

    :::image type="content" source="./media/secure-score-security-controls/score-on-dedicated-dashboard.png" alt-text="安全中心“安全功能分数”页面上的订阅的安全功能分数":::

    :::image type="content" source="./media/secure-score-security-controls/secure-score-management-groups.png" alt-text="安全中心的“安全功能分数”页面上的管理组的安全功能分数":::

    > [!NOTE]
    > 你对其没有足够权限的管理组都会将其分数显示为“受限”。 

- 在“建议”页面的顶部：

    :::image type="content" source="./media/secure-score-security-controls/score-on-recommendations-page.png" alt-text="安全中心建议页面上的安全功能分数":::

## <a name="get-your-secure-score-from-the-rest-api"></a>从 REST API 获取安全功能分数

可以通过安全功能分数 API 访问分数。 通过 API 方法，可灵活地查询数据，久而久之构建自己的安全功能分数报告机制。 例如，你可以使用[安全功能分数 API](/rest/api/securitycenter/securescores) 来获取特定订阅的分数。 此外，你可以使用 [ API](/rest/api/securitycenter/securescorecontrols) 列出订阅的安全控件和当前分数。

![通过 API 检索单个安全分数。](media/secure-score-security-controls/single-secure-score-via-api.png)

有关构建在安全功能分数 API 之上的工具示例，请参阅 [GitHub 社区的安全功能分数区域](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score)。 

## <a name="get-your-secure-score-from-azure-resource-graph"></a>从 Azure Resource Graph 获取安全功能分数

使用 Azure Resource Graph (ARG)，可以通过可靠的筛选、分组和排序功能，快速访问你的云环境中的资源信息。 这是以编程方式或从 Azure 门户中查询 Azure 订阅中的信息的一种快速且有效的方式。 [详细了解 Azure Resource Graph](../governance/resource-graph/index.yml)。

若要使用 Azure Resource Graph 访问多个订阅的安全功能分数：

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

### <a name="secure-score-over-time-report-in-workbooks-page"></a>工作簿页中的“一段时间内的安全功能分数”报表

安全中心的工作簿页面包含一个现成的报表，可用于直观地跟踪订阅和安全控制措施等内容的分数。 有关详细信息，请参阅[创建丰富的交互式安全中心数据报表](custom-dashboards-azure-workbooks.md)。

:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-snip.png" alt-text="Azure 安全中心的工作簿库中的“一段时间内的安全功能分数报表”部分":::

### <a name="power-bi-pro-dashboards"></a>Power BI Pro 仪表板

如果你是具有 Pro 帐户的 Power BI 用户，则可以使用“一段时间内的安全评分”Power BI 面板跟踪一段时间内的安全评分，并调查任何更改。

> [!TIP]
> 可以在 GitHub 上的 Azure 安全中心社区的专用区域中找到这个仪表板，以及其他以编程方式使用安全分数的工具： https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score

该面板包含以下两个报表，可帮助你分析安全状态：

- **资源摘要** - 提供有关资源运行状况的摘要数据。
- **安全评分摘要** - 提供有关评分进度的摘要数据。 使用“每个订阅一段时间内的安全评分”图表查看评分的变化。 如果发现评分发生了巨大变化，请检查“检测到的可能会影响你安全评分的更改”表，以查找可能引起变化的可能更改。 此表显示了已删除的资源、新部署的资源或其安全状态针对其中一项建议发生了更改的资源。

:::image type="content" source="./media/secure-score-security-controls/power-bi-secure-score-dashboard.png" alt-text="可选的一段时间内的安全分数 Power BI 仪表板，用于跟踪一段时间内的安全分数并调查更改。":::


## <a name="next-steps"></a>后续步骤

本文介绍了如何访问和跟踪安全功能分数。 如需相关材料，请参阅以下文章：

- [了解建议的不同元素](security-center-recommendations.md)
- [了解如何修正建议](security-center-remediate-recommendations.md)
- [查看基于 GitHub 的工具以便以编程方式使用安全评分](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score)