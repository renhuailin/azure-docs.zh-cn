---
title: 在 Application Insights 中进行用户、会话和事件分析
description: 对 Web 应用的用户进行人口统计学分析。
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 07/30/2021
ms.openlocfilehash: 09dc9ba915b0ecf99219aadd9214192b9f4b1e19
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121747683"
---
# <a name="users-sessions-and-events-analysis-in-application-insights"></a>在 Application Insights 中进行用户、会话和事件分析

查明人们何时使用 Web 应用，他们对哪些页面最感兴趣，用户在哪里以及他们使用什么浏览器和操作系统。 使用 [Application Insights](./app-insights-overview.md) 分析业务和使用情况遥测。

:::image type="content" source="./media/usage-segmentation/users.png" alt-text="屏幕截图以面积图的形式显示了“用户”选项卡。" lightbox="./media/usage-overview/users.png":::

## <a name="get-started"></a>入门

如果在 Application Insights 门户的“用户”、“会话”或“事件”边栏选项卡中没有看到数据，请[了解如何开始使用“使用情况”工具](usage-overview.md)。

## <a name="the-users-sessions-and-events-segmentation-tool"></a>用户、会话和事件分段工具

三个使用情况边栏选项卡使用相同的工具从三个视角对来自 Web 应用的遥测进行切片和切块。 通过对数据进行筛选和拆分，可以洞察不同页面和功能的相对使用情况。

* **“用户”工具**：多少人使用了应用及其功能。  将使用浏览器 cookie 中存储的匿名 ID 对用户进行计数。 使用不同浏览器或计算机的单个用户会被计为多个用户。
* **“会话”工具**：用户活动的多少会话包括了应用的特定页面和功能。 会话是在用户处于不活动状态半小时后或者在连续使用 24 小时后进行计数的。
* **“事件”工具**：应用的特定页面和功能是以何频率使用的。 当浏览器加载了应用中的一个页面时，会将其计为一次页面查看，前提是已[对它进行了检测设置](./javascript.md)。 

    自定义事件表示应用中发生的某个事件，通常是用户交互，例如选择按钮或完成某项任务。 可以在应用中插入代码来[生成自定义事件](./api-custom-events-metrics.md#trackevent)。

## <a name="querying-for-certain-users"></a>查询特定用户

通过在“用户”工具的顶部调整查询选项来探究不同的用户组：

- 期间：选择一个时间范围。
- 显示：选择要分析的一组用户。
- 使用者：选择具体的自定义事件、请求和页面视图。
- 事件：选择多个事件、请求和页面视图，这些视图会显示至少完成了一个项（不一定完成了所有已选择的事件、请求和页面视图）的用户。
- 依据 x 轴值：选择如何划分数据的存储段，可以按时间范围或按其他属性（例如浏览器或城市）来这样做。
- 拆分依据：选择对数据进行拆分或分段时要依据的属性。 
- 添加筛选器：将查询限制到特定的用户、会话或事件（根据其属性，例如浏览器或城市）。 
 
## <a name="meet-your-users"></a>了解用户

“了解用户”部分显示与当前查询匹配的五个示例用户的相关信息。 探索个人和群体的行为可以了解人们实际上是如何使用你的应用的。

## <a name="next-steps"></a>后续步骤

- 若要启用使用体验，请首先发送[自定义事件](./api-custom-events-metrics.md#trackevent)或[页面视图](./api-custom-events-metrics.md#page-views)。
- 如果已经发送自定义事件或页面视图，请浏览使用情况工具，了解用户如何使用服务。
    - [漏斗图](usage-funnels.md)
    - [保留](usage-retention.md)
    - [用户流](usage-flows.md)
    - [工作簿](../visualize/workbooks-overview.md)
    - [添加用户上下文](./usage-overview.md)