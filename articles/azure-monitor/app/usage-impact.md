---
title: Application Insights 使用情况影响 - Azure Monitor
description: 分析不同的属性对应用部件的转换率造成的潜在影响。
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 07/30/2021
ms.openlocfilehash: 3e484cb0f083292ba22c7a30c79202d01d1b10eb
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121734363"
---
# <a name="impact-analysis-with-application-insights"></a>Application Insights 中的 Impact 分析功能

Impact 可以分析加载时间和其他属性对应用的各个部件的转换率造成的影响。 更准确地说，Impact 可以发现 **页面视图** 的 **任何维度**、**自定义事件** 或 **请求** 对不同 **页面视图** 或 **自定义事件** 造成的影响。 

## <a name="still-not-sure-what-impact-does"></a>仍不确定 Impact 的作用？

举例来说，假如团队中有人在争论站点某个部件的缓慢运行是否导致用户退出站点，则 Impact 就是解决争论的终极工具。 尽管用户能够容忍一定程度的缓慢，但 Impact 可以提供见解，让你知道如何最好地对优化措施和性能做出平衡，以将用户转换率最大化。

不过，分析性能只是 Impact 的一部分功能。 由于 Impact 支持自定义事件和维度，只需单击几下鼠标，就能解答用户浏览器的选择与不同转换率之间的相关性等问题。

> [!NOTE]
> Application Insights 资源必须包含页面视图或自定义事件才能使用 Impact 分析工作簿。 [了解如何使用 Application Insights JavaScript SDK 将应用设置为自动收集页面访问次数](./javascript.md)。 另请注意，由于分析的是相关性，因此样本大小非常重要。

## <a name="impact-analytics-workbook"></a>Impact 分析工作簿 

要使用 Impact 分析工作簿，请在 Application Insights 资源中导航到“使用情况” > “Impact”并选择“Impact 分析工作簿”。 也可在“工作簿”选项卡中选择“公共模板”，然后在“使用情况”下选择“用户 Impact 分析”。

:::image type="content" source="./media/usage-impact/workbooks-gallery.png" alt-text="公共模板上工作簿库的屏幕截图。" lightbox="./media/usage-impact/workbooks-gallery.png":::


### <a name="using-the-workbook"></a>使用工作簿

:::image type="content" source="./media/usage-impact/selected-event.png" alt-text="屏幕截图中显示了在何处选择初始页面视图、自定义事件或请求。" lightbox="./media/usage-impact/selected-event.png":::

1. 在“已选事件”下拉列表中选择事件
2. 在“分析其性能影响”下拉列表中选择指标
3. 在“影响事件”下拉列表中选择事件 
1. 如果你想添加筛选器，可以在“添加已选事件筛选器”和/或“添加影响事件筛选器”中执行此操作。


## <a name="is-page-load-time-impacting-how-many-people-convert-on-my-page"></a>页面加载时间是否影响了页面上的人员转换数量？

若要开始使用 Impact 工作簿解答问题，请选择初始页面视图、自定义事件或请求。

1. 在“已选事件”下拉列表中选择事件。
2. 将“分析依据”下拉列表中的选择保留为“持续时间”（在此上下文中，“持续时间”是“页面加载时间”的别名）。
3. 在“影响事件”下拉列表中，选择一个自定义事件。 此事件应该对应于在步骤 1 中选择的页面视图的 UI 元素。


:::image type="content" source="./media/usage-impact/impact.png" alt-text="屏幕截图显示了一个示例，该示例将所选事件作为按持续时间分析的主页。" lightbox="./media/usage-impact/impact.png":::

## <a name="what-if-im-tracking-page-views-or-load-times-in-custom-ways"></a>是否能以自定义的方式跟踪页面视图或加载时间？

Impact 支持标准和自定义的属性与度量值。 可以任意使用这两种方式。 如果不使用持续时间，可以使用基于主要和次要事件的筛选器来获取更具体的结果。

## <a name="do-users-from-different-countries-or-regions-convert-at-different-rates"></a>来自不同国家或地区的用户是否以不同的比率转换？

1. 在“已选事件”下拉列表中选择事件。
2. 在“分析依据”下拉列表中选择“国家或地区”
3. 在“影响事件”下拉列表中，选择与在步骤 1 中选择的页面视图中的 UI 元素对应的自定义事件。

:::image type="content" source="./media/usage-impact/regions.png" alt-text="屏幕截图显示了一个示例，其中已选事件作为按国家/地区分析的 GET。" lightbox="./media/usage-impact/regions.png":::

## <a name="how-does-the-impact-analysis-workbook-calculate-these-conversion-rates"></a>Impact 分析工作簿如何计算这些转换率？

在幕后，Impact 分析工作簿依赖于[皮尔逊相关系数](https://en.wikipedia.org/wiki/Pearson_correlation_coefficient)。 结果是在 -1 与 1 之间计算的，-1 表示负线性相关，1 表示正线性相关。

简单而言，Impact Analysis 工作原理的分解如下：

让 _A_ = 在第一个下拉列表中选择的主要页面视图/自定义事件/请求。 （已选事件）。

让 _B_ = 选择的次要页面视图/自定义事件（**影响以下对象的使用**）。

Impact 会查找选定时间范围内所有用户会话的样本。 对于每个会话，它会查找出现的每个 _A_。

然后，根据以下两个条件之一，将会话分解成两种不同的子会话：

- 转换后的子会话包括以 _B_ 事件结尾的会话，并包含 _B_ 前面发生的所有 _A_ 事件。
- 如果所有 _A_ 事件是在未发生终结性 _B_ 的情况下发生的，则发生未转换的子会话。

Impact 最终计算方式根据是按指标还是按维护分析而异。 使用指标时，将求子会话中所有 _A_ 的平均值。 使用维度时，每个 _A_ 的值为 _B_ 的赋值贡献 _1/N_，其中 _N_ 是子会话中 _A_ 的数目。

## <a name="next-steps"></a>后续步骤

- 要详细了解工作簿，请访问[工作簿概述](../visualize/workbooks-overview.md)。
- 若要启用使用体验，请首先发送[自定义事件](./api-custom-events-metrics.md#trackevent)或[页面视图](./api-custom-events-metrics.md#page-views)。
- 如果已经发送自定义事件或页面视图，请浏览使用情况工具，了解用户如何使用服务。
    - [漏斗图](usage-funnels.md)
    - [保留](usage-retention.md)
    - [用户流](usage-flows.md)
    - [工作簿](../visualize/workbooks-overview.md)
    - [添加用户上下文](./usage-overview.md)