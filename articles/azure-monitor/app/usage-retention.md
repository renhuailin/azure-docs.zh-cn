---
title: 使用 Application Insights 分析 Web 应用用户保留
description: 有多少用户回归到应用？
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 07/30/2021
ms.openlocfilehash: de83c898d7a471892f61fa299cdf2091a1f228e7
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121739793"
---
# <a name="user-retention-analysis-for-web-applications-with-application-insights"></a>使用 Azure Application Insights 为 Web 应用程序进行用户留存情况分析

[Application Insights](./app-insights-overview.md) 中的保留功能可帮助分析返回到应用的用户数及其执行特定任务或实现目标的频率。 例如，如果运行游戏网站，则可以会在输掉游戏后回归到网站的用户数与在获胜后回归的用户数进行比较。 此信息有助于改进用户体验和业务策略。

## <a name="get-started"></a>入门

如果在 Application Insights 门户的“留存情况”工具中没有看到数据，请[了解如何开始使用“使用情况”工具](usage-overview.md)。

## <a name="the-retention-workbook"></a>保留工作簿

要使用保留工作簿，请在 Application Insights 资源中导航到“使用情况” > “保留”并选择“保留分析工作簿”  。 也可在“工作簿”选项卡中选择“公共模板”，然后在“使用情况”下选择“用户保留分析”  。

:::image type="content" source="./media/usage-retention/workbooks-gallery.png" alt-text="公共模板上工作簿库的屏幕截图。" lightbox="./media/usage-retention/workbooks-gallery.png":::



### <a name="using-the-workbook"></a>使用工作簿

:::image type="content" source="./media/usage-retention/retention.png" alt-text="保留工作簿的屏幕截图，其中显示了折线图。" lightbox="./media/usage-retention/retention.png":::

- 默认情况下，留存情况显示执行了任意操作，然后在一段时间后返回并执行其他任意操作的所有用户。 可以选择事件的不同组合，将关注范围缩小为仅关注特定用户活动。
- 通过选择“添加筛选器”为属性添加一个或多个筛选器。 例如，可以关注特定国家或地区中的用户。 
- 整体留存情况图表显示选定时间段内用户留存情况的摘要。 
- 网格显示保留的用户数。 每行都表示在所显示的时间段内执行了任意事件的一群用户。 行中的每个单元格显示了该用户群中有多少人在之后的某个时间段内至少回归了一次。 某些用户可能在多个时间段内都回归过。 
- 见解卡中显示了排名前 5 的启动事件和排名前 5 的返回事件，帮助用户更好地了解留存情况报表。 

    :::image type="content" source="./media/usage-retention/retention-2.png" alt-text="保留工作簿的屏幕截图，其中显示了“# 周后返回的用户”图表。" lightbox="./media/usage-retention/retention-2.png":::

## <a name="use-business-events-to-track-retention"></a>使用业务事件跟踪留存情况

若要获得最有用的留存情况分析，请对表示重要业务活动的事件进行度量。 

例如，许多用户可能会打开应用中的页面，但是却不玩页面中显示的游戏。 因此，仅仅跟踪页面查看数将无法准确估计有多少人在玩过该游戏后又回来玩该游戏。 为了清晰了解回归玩家，应用应当在用户实际玩游戏时发送一个自定义事件。  

一种较好的做法是编写表示关键业务操作的自定义事件，并使用这些事件进行留存情况分析。 要捕获游戏结果，需要编写一行代码来向 Application Insights 发送一个自定义事件。 如果采用网页代码或 Node.JS 编写该事件，则它类似于以下内容：

```JavaScript
    appinsights.trackEvent("won game");
```

如果采用 ASP.NET 服务器代码，则如下所示：

```csharp
   telemetry.TrackEvent("won game");
```

[详细了解如何编写自定义事件](./api-custom-events-metrics.md#trackevent)。


## <a name="next-steps"></a>后续步骤
- - 要详细了解工作簿，请访问[工作簿概述](../visualize/workbooks-overview.md)。
- 若要启用使用体验，请首先发送[自定义事件](./api-custom-events-metrics.md#trackevent)或[页面视图](./api-custom-events-metrics.md#page-views)。
- 如果已经发送自定义事件或页面视图，请浏览使用情况工具，了解用户如何使用服务。
    - [用户、会话、事件](usage-segmentation.md)
    - [漏斗图](usage-funnels.md)
    - [用户流](usage-flows.md)
    - [工作簿](../visualize/workbooks-overview.md)
    - [添加用户上下文](./usage-overview.md)