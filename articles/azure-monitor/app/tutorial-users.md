---
title: 在 Application Insights 中了解客户 | Microsoft Docs
description: 本教程介绍如何使用 Application Insights 了解客户对应用程序的使用情况。
ms.topic: tutorial
author: lgayhardt
ms.author: lagayhar
ms.date: 07/30/2021
ms.custom: mvc
ms.openlocfilehash: 8848d7db278576f8f165b55f6b69ce998bfe7629
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121732025"
---
# <a name="use-azure-application-insights-to-understand-how-customers-are-using-your-application"></a>使用 Azure Application Insights 了解客户对应用程序的使用情况

 Application Insights 收集使用情况信息，帮助了解用户与应用程序的交互情况。  本教程详细介绍可用于分析此信息的不同资源。  将了解如何执行以下操作：

> [!div class="checklist"]
> * 分析有关访问应用程序的用户的详细信息
> * 使用会话信息分析客户对应用程序的使用情况
> * 定义漏斗，以便比较所需用户活动及其实际活动 
> * 创建用于将可视化效果和查询整合到单个文档中的工作簿
> * 对类似用户分组，以便放在一起分析
> * 了解哪些用户会回到应用程序
> * 检查用户如何浏览应用程序


## <a name="prerequisites"></a>必备条件

为完成此教程：

- 使用以下工作负荷安装 [Visual Studio 2019](https://www.visualstudio.com/downloads/)：
    - ASP.NET 和 Web 开发
    - Azure 开发
- 下载并安装 [Visual Studio Snapshot Debugger](https://aka.ms/snapshotdebugger)。
- 将 .NET 应用程序部署到 Azure 并[启用 Application Insights SDK](../app/asp-net.md)。 
- [从应用程序发送遥测数据](../app/usage-overview.md#send-telemetry-from-your-app)，添加自定义事件/页面视图
- 发送[用户上下文](./usage-overview.md)，跟踪用户在一段时间内执行的操作及充分利用使用情况功能。

## <a name="log-in-to-azure"></a>登录 Azure
通过 [https://portal.azure.com](https://portal.azure.com) 登录到 Azure 门户。

## <a name="get-information-about-your-users"></a>获取有关用户的信息
借助“用户”  面板，可使用各种方式了解有关用户的重要详细信息。 此面板可用于了解用户的连接位置、其客户端的详细信息及其所访问的应用程序区域等信息。 

1. 在 Application Insights 资源中的“使用情况”下，在菜单中选择“用户”。
2. 默认视图显示过去 24 小时内连接到应用程序的唯一用户的数量。  可更改时间范围并设置各种其他条件来筛选此信息。

3. 单击“期间”  下拉列表，将时间范围更改为 7 天。  此操作可增加面板中不同图表内包含的数据。

4. 单击“拆分方式”  下拉列表，向图表添加按用户属性分类的细分。  选择“国家或地区”  。  该图表包含的数据相同，但可查看每个国家/地区的用户数的细分。

      :::image type="content" source="./media/tutorial-users/user-1.png" alt-text="“用户”选项卡的查询生成器屏幕截图。" lightbox="./media/tutorial-users/user-1.png":::

5. 将光标放在图表中的不同条形图上进行查看，请注意，每个国家/地区的计数仅反映由该条形图表示的时间范围。
6. 选择“查看更多见解”以了解更多详细信息。 

      :::image type="content" source="./media/tutorial-users/user-2.png" alt-text="查看更多见解的“用户”选项卡屏幕截图。" lightbox="./media/tutorial-users/user-2.png":::


## <a name="analyze-user-sessions"></a>分析用户会话
“会话”  面板与“用户”  面板类似。  其中“用户”  面板可帮助了解有关访问应用程序的用户的详细信息，“会话”  面板可帮助了解这些用户对应用程序的使用情况。  

1. 用户使用情况，选择“会话”。
2. 查看图表，请注意，用于筛选和细分数据的选项与“用户”  面板中的相关选项相同。

     :::image type="content" source="./media/tutorial-users/sessions.png" alt-text="显示条形图的“会话”选项卡屏幕截图。" lightbox="./media/tutorial-users/sessions.png":::

4. 若要查看会话时间线，请选择“查看更多见解”，然后在活动会话下，选择一个时间线上的“查看会话时间线” 。 会话时间线会显示会话中的每个操作。 此操作可帮助确定具有大量异常的会话等信息。

     :::image type="content" source="./media/tutorial-users/timeline.png" alt-text="选择了时间线的“会话”选项卡屏幕截图。" lightbox="./media/tutorial-users/timeline.png":::

## <a name="group-together-similar-users"></a>对类似用户进行分组
**队列** 是指一组按类似特征进行分组的用户。  可使用队列筛选其他面板中的数据，以便分析特定用户组。  例如，可能只想分析完成了购买的用户。

1.  在一个使用情况选项卡（“用户”、“会话”、“事件”等）顶部选择“创建队列”。

1.  从库中选择模板。

    :::image type="content" source="./media/tutorial-users/cohort.png" alt-text="屏幕截图：队列模板库。" lightbox="./media/tutorial-users/cohort.png":::
1.  编辑队列，然后选择“保存”。
1.  若要查看队列，请从“显示”下拉菜单中选择它。 

    :::image type="content" source="./media/tutorial-users/cohort-2.png" alt-text="屏幕截图：“显示下拉列表”，其中显示队列。" lightbox="./media/tutorial-users/cohort-2.png":::


## <a name="compare-desired-activity-to-reality"></a>比较所需活动与实际活动
尽管前几个面板侧重于应用程序用户执行的操作，但“漏斗”  面板侧重于希望用户执行的操作。  漏斗表示应用程序中的一组步骤和执行这些步骤的用户所占的百分比。  例如，可创建用于度量通过搜索产品连接到应用程序的用户所占百分比的漏斗。  随后可查看将该产品添加到购物车中的用户所占的百分比，以及完成购买的用户所占的百分比。

1. 在菜单中选择“漏斗”，然后选择“编辑”。 

3. 通过为每个步骤选择一个操作，创建至少具有两个步骤的漏斗。  使用 Application Insights 收集的使用情况数据生成操作列表。

    :::image type="content" source="./media/tutorial-users/funnel.png" alt-text="“漏斗”选项卡的屏幕截图，其中选择了“编辑”选项卡上的步骤。" lightbox="./media/tutorial-users/funnel.png":::

4. 选择“视图”选项卡，以查看结果。 右侧的窗口显示第一个活动之前和最后一个活动之后的最常见事件，帮助了解特定顺序下的用户倾向。

     :::image type="content" source="./media/tutorial-users/funnel-2.png" alt-text="视图上的漏斗选项卡的屏幕截图。" lightbox="./media/tutorial-users/funnel-2.png":::

4. 若要保存漏斗，请选择“保存”。 

## <a name="learn-which-customers-return"></a>了解哪些客户会返回

**保留期** 可帮助了解哪些用户会回到应用程序。  

1. 选择菜单中的“保留期”，然后选择“保留分析工作簿”。
2. 默认情况下，经过分析的信息包含执行任何操作后，再返回应用程序执行操作的用户。  例如，可将此筛选器更改为仅包含完成购买后返回应用程序的用户。

      :::image type="content" source="./media/tutorial-users/retention.png" alt-text="显示与为保留筛选器设置的条件匹配的用户图形的屏幕截图。" lightbox="./media/tutorial-users/retention.png":::

3. 针对不同的持续时间以图表和表格的形式显示匹配标准的返回用户。 典型模式为返回用户的数量在一段时间内平稳下降。  从一个时段到下一个时段出现突然下降可能会引起关注。 

      :::image type="content" source="./media/tutorial-users/retention-2.png" alt-text="保留工作簿的屏幕截图，其中显示了“# 周后返回的用户”图表。" lightbox="./media/tutorial-users/retention-2.png":::

## <a name="analyze-user-navigation"></a>分析用户导航
**用户流** 可视化用户在应用程序的页面和功能之间导航的情况。  这有助于回答诸如用户通常从特定页面的哪个位置移动、用户通常如何退出应用程序以及是否存在任何定期重复的操作等问题。

1.  在菜单中选择“用户流”。
2.  单击“新建”来新建用户流，然后选择“编辑”来编辑其详细信息。
3.  将“时间范围”增加为 7 天，然后选择初始事件。  用户流将跟踪从该事件开始的用户会话。

     :::image type="content" source="./media/tutorial-users/flowsedit.png" alt-text="显示如何创建新用户流的屏幕截图。" lightbox="./media/tutorial-users/flowsedit.png":::
    
4.  此时会显示用户流，可查看不同用户路径及其会话计数。  蓝线指示用户在当前操作后执行的操作。  红线指示用户会话结束。

   :::image type="content" source="./media/tutorial-users/flows.png" alt-text="显示用户流的用户路径和会话计数的屏幕截图。" lightbox="./media/tutorial-users/flows.png":::

5.  若要从用户流中删除事件，请选择操作角落中的 x，然后选择“创建图形”。  图表会重新绘制，并删除该事件的所有实例。 选择“编辑”查看现在已添加到“已排除的事件”中的事件。

    :::image type="content" source="./media/tutorial-users/flowsexclude.png" alt-text="显示用户流的排除事件列表的屏幕截图。" lightbox="./media/tutorial-users/flowsexclude.png":::

## <a name="consolidate-usage-data"></a>整合使用情况数据
**工作簿** 可将数据可视化效果、Analytics 查询和文本合并到交互式文档中。  工作簿可用于对常见使用情况信息分组、整合来自特定事件的信息或向团队报告应用程序使用情况。

1.  在菜单中选择“工作簿”。
2.  选择“新建”以新建工作簿。
3.  我们已经提供一个包含前一天的所有使用情况数据（显示为条形图）的查询。  用户可使用此查询、手动编辑此查询，或选择“示例”来从其他有用查询中进行选择。

    :::image type="content" source="./media/tutorial-users/sample-queries.png" alt-text="显示可以使用的示例按钮和示例查询列表的屏幕截图。" lightbox="./media/tutorial-users/sample-queries.png":::

4.  选择“完成编辑”。
5.  选择顶部窗格中的“编辑”来编辑工作簿顶部的文本。  其采用 markdown 格式。

6.  选择“添加用户”，添加包含用户信息的图表。  编辑图表的详细信息（如果需要），然后选择“完成编辑”将其保存。

要详细了解工作簿，请访问[工作簿概述](../visualize/workbooks-overview.md)。

## <a name="next-steps"></a>后续步骤
现在，你已学会如何分析用户，请继续完成下一教程，学习如何创建将此信息与其他有关应用程序的有用数据合并的自定义仪表板。

> [!div class="nextstepaction"]
> [创建自定义仪表板](./tutorial-app-dashboards.md)