---
title: 基于 Azure Sentinel 数据创建 Power BI 报表
description: 了解如何使用从 Azure Sentinel Log Analytics 导出的查询创建 Power BI 报表。 在 Power BI 服务和 Teams 通道中与他人共享报表。
author: batamig
ms.author: bagol
ms.service: azure-sentinel
ms.topic: conceptual
ms.date: 06/08/2021
ms.openlocfilehash: 0e877439b18d4c7ff6c4b21baa18b3b0fd1586b3
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121724150"
---
# <a name="tutorial-create-a-power-bi-report-from-azure-sentinel-data"></a>教程：基于 Azure Sentinel 数据创建 Power BI 报表

[Power BI](https://powerbi.microsoft.com/) 是一种报表与分析平台，可将数据转换为一致、沉浸式的交互式可视化效果。 Power BI 可使你轻松连接到数据源，可视化和发现关系，并与你想要的任何人共享见解。

可以基于 Azure Sentinel Log Analytics 工作区的数据创建 Power BI 报表，并与无权访问 Azure Sentinel 的人员共享这些报表。 例如，你可能想要与应用所有者共享登录尝试失败的信息，而不授予其 Azure Sentinel 访问权限。 Power BI 可视化效果可提供一目了然的数据。

在本教程中，你将了解：

> [!div class="checklist"]
> * 将 Log Analytics Kusto 查询导出到 Power BI M 语言查询。
> * 使用 Power BI Desktop 中的 M 查询创建可视化效果和报表。
> * 将报表发布到 Power BI 服务，并与他人共享。
> * 将报表添加到 Teams 通道。

你在 Power BI 服务中授予访问权限的人员以及 Teams 通道的成员无需 Azure Sentinel 权限即可查看报表。

> [!NOTE]
> 本教程为一个排名靠前的客户问题提供基于场景的过程：在 Power BI 中查看 Azure Sentinel 数据的分析报告。 有关详细信息，请参阅[连接数据源](connect-data-sources.md)和[可视化收集的数据](get-visibility.md)。
>
## <a name="prerequisites"></a>先决条件

要完成本教程，需要：

- 至少拥有对监视登录尝试的 Azure Sentinel Log Analytics 工作区的读取访问权限。
- 拥有 Log Analytics 工作区读取访问权限的 Power BI 帐户。
- [从 Microsoft Store 安装的 Power BI Desktop](https://aka.ms/pbidesktopstore)。

## <a name="export-a-query-from-log-analytics"></a>从 Log Analytics 导出查询

在 Azure Sentinel Log Analytics 工作区中创建、运行和导出 Kusto 查询。 

1. 要创建简单查询，在 Azure Sentinel Log Analytics 工作区中选择“日志”。 在查询编辑器的“新建查询 1”下，输入以下 Kusto 查询：
   
   ```kusto
   SigninLogs
   |  where TimeGenerated >ago(7d)
   | summarize Attempts = count(), Failed=countif(ResultType !=0), Succeeded = countif(ResultType ==0) by AppDisplayName
   |  top 10 by Failed
   | sort by Failed
   ```
   
   或者，使用你喜爱的 Azure Sentinel Log Analytics Kusto 查询。
   
1. 选择“运行”以运行查询并生成结果。
   
   :::image type="content" source="media/powerbi/query.png" alt-text="显示 Log Analytics Kusto 查询和结果的屏幕截图。":::
   
1. 要将查询导出为 Power BI M 查询格式，选择“导出”，然后选择“导出为 Power BI（M 查询）”。 Log Analytics 会将查询导出到名为“PowerBIQuery.txt”的文本文件。
   
   :::image type="content" source="media/powerbi/export.png" alt-text="显示查询导出到 Power BI M 格式的屏幕截图。":::
   
1. 复制导出文件的内容。

## <a name="get-the-data-in-power-bi-desktop"></a>在 Power BI Desktop 中获取数据

在 Power BI Desktop 中运行导出的 M 查询，以获取数据。

1. 打开 Power BI Desktop，登录到你的 Power BI 帐户，该帐户拥有对 Log Analytics 工作区的读取访问权限。
   
   :::image type="content" source="media/powerbi/sign-in.png" alt-text="显示登录到 Power BI Desktop 的屏幕截图。":::
   
1. 在 Power BI 功能区中，选择“获取数据”，然后选择“空查询”。 此时会打开“Power Query 编辑器”。
   
   :::image type="content" source="media/powerbi/blank-query.png" alt-text="显示在 Power BI Desktop 的“获取数据”下选择了“空查询”的屏幕截图。":::
   
1. 在“Power Query 编辑器”中，选择“高级编辑器”。
   
1. 将导出的“PowerBIQuery.txt”文件的复制内容粘贴到“高级编辑器”窗口，然后选择“完成”。
   
   :::image type="content" source="media/powerbi/advanced-editor.png" alt-text="显示粘贴到“Power BI 高级编辑器”中的 M 查询的屏幕截图。":::
   
1. 在“Power Query 编辑器”中，将查询重命名为“App_signin_stats”，然后选择“关闭并应用”。
   
   :::image type="content" source="media/powerbi/close-apply.png" alt-text="显示重命名的查询以及 Power Query 编辑器中的“关闭并应用”命令的屏幕截图。":::

## <a name="create-visualizations-from-the-data"></a>基于数据创建可视化效果

现在，数据已在 Power BI 中，你可以创建可视化效果，以提供有关数据的见解。

### <a name="create-a-table-visual"></a>创建视觉对象表

首先，创建能够显示所有查询结果的表。

1. 要将表可视化效果添加到 Power BI Desktop 画布，请选择“可视化效果”下的“表”图标 。
   
   :::image type="content" source="media/powerbi/table.png" alt-text="显示 Power BI Desktop 中“可视化效果”下的“表”图标的屏幕截图。":::
   
1. 在“字段”下，选择查询中的所有字段，以便它们都显示在表中。 如果表未显示所有数据，可拖动表的所选项目控点来放大表。
   
   :::image type="content" source="media/powerbi/select-fields.png" alt-text="显示为表可视化效果选择的所有字段的屏幕截图。":::
   
### <a name="create-a-pie-chart"></a>创建饼图

接下来，创建一个饼图，显示哪些应用程序的登录尝试失败次数最多。

1. 在视觉对象表外单击或点击，以取消选择视觉对象表，然后在“可视化效果”下，选择“饼图”图标。
   
   :::image type="content" source="media/powerbi/pie-chart.png" alt-text="显示 Power BI Desktop 中“可视化效果”下的“饼图”图标的屏幕截图。":::
   
1. 在“图例”选项卡井中选择“AppDisplayName”，或从“字段”窗格拖动。 在“值”选项卡井中选择“失败”，或从“字段”拖动  。 饼图现在会显示每个应用程序的登录尝试失败次数。
   
   :::image type="content" source="media/powerbi/failed.png" alt-text="显示饼图的屏幕截图，饼图中显示了每个应用程序的登录尝试失败次数。":::
   
### <a name="create-a-new-quick-measure"></a>创建新的快速度量值

你还希望显示每个应用程序的登录尝试失败百分比。 由于你的查询没有百分比列，可以创建一个新度量值来显示此信息。

1. 在“可视化”下，选择“堆积柱形图”图标，以创建堆积柱形图。
   
   :::image type="content" source="media/powerbi/column-chart.png" alt-text="显示 Power BI Desktop 中“可视化效果”下的“堆积柱形图”图标的屏幕截图。":::
   
1. 选择新的可视化效果后，请选择功能区中的“快速度量值”。
   
1. 在“快速度量值”窗口中，选择“计算”下的“除”。 将“字段”中的“失败次数”拖动到“分子”字段，将“字段”中的“尝试次数”拖动到“分母”。     。
   
   :::image type="content" source="media/powerbi/quick-measures.png" alt-text="显示“快速度量值”窗口中的设置的屏幕截图。":::
   
1. 选择“确定”。 新度量值将显示在“字段”窗格中。
   
1. 在“字段”窗格中选择新度量值，然后在功能区的“格式设置”下，选择“百分比”。
   
   :::image type="content" source="media/powerbi/percentage.png" alt-text="显示“字段”窗格中选择的新度量值以及功能区中“格式设置”下选择的“百分比”的屏幕截图。":::
   
1. 在画布上选择柱形图可视化效果，选择“AppDisplayName”字段或将其拖动到“轴”选项卡井中，并将新的“失败次数除以尝试次数”度量值拖动到“值”选项卡井中。 该图表现在会显示每个应用程序的登录尝试失败次数百分比。
   
   :::image type="content" source="media/powerbi/failed-percentage.png" alt-text="显示柱形图的屏幕截图，其中显示了每个应用程序的失败尝试百分比。":::
   
### <a name="refresh-the-data-and-save-the-report"></a>刷新数据并保存报表

1. 选择“刷新”以获取来自 Azure Sentinel 的最新数据。
   
   :::image type="content" source="media/powerbi/refresh.png" alt-text="显示功能区“刷新”按钮的屏幕截图。":::
   
1. 选择“文件” > “保存”，保存你的 Power BI 报表。

## <a name="create-a-power-bi-online-workspace"></a>创建 Power BI Online 工作区

要创建 Power BI 工作区来共享报表：

1. 使用你用于 Power BI Desktop 和 Azure Sentinel 读取访问的同一帐户登录 [powerbi.com](https://powerbi.com)。
   
1. 在“工作区”下，选择“创建工作区”。 为工作区“管理报表”命名，然后选择“保存”。
   
   :::image type="content" source="media/powerbi/create-workspace.png" alt-text="显示在 Power BI 服务中“创建工作区”的屏幕截图。":::
   
1. 要向用户和组授予对工作区的访问权限，请选择新工作区名称旁边的“更多选项”三点菜单，然后选择“工作区访问权限”。
   
   :::image type="content" source="media/powerbi/workspace-access.png" alt-text="显示工作区“更多选项”菜单中的“工作区访问权限”的屏幕截图。":::
   
1. 在“工作区访问权限”侧窗格中，可以添加用户的电子邮件地址，并为每位用户分配角色。 角色包括“管理员”、“成员”、“参与者”和“查看者”。

## <a name="publish-the-power-bi-report"></a>发布 Power BI 报表

现在，可以使用 Power BI Desktop 发布你的 Power BI 报表，以便其他人可以看到它。

1. 在 Power BI Desktop 的新报表中，选择“发布”。
   
   :::image type="content" source="media/powerbi/publish.png" alt-text="显示 Power BI Desktop 功能区中的“发布”的屏幕截图。":::
   
1. 选择要发布到的“管理报表”工作区，然后选择“选择”。
   
   :::image type="content" source="media/powerbi/select-workspace.png" alt-text="显示选择要发布到的 Power BI“管理报表”工作区的屏幕截图。":::
   
## <a name="import-the-report-to-a-microsoft-teams-channel"></a>将报表导入 Microsoft Teams 通道

你还希望“管理团队”通道的成员能够查看报表。 将报表添加到 Teams 通道：

1. 在“管理团队”通道中，选择 + 以添加选项卡，然后在“添加选项卡”窗口中，搜索并选择“Power BI”。

   :::image type="content" source="media/powerbi/add-tab.png" alt-text="显示在 Teams 的“添加选项卡”窗口中选择“Power BI”的屏幕截图。":::
   
1. 从 Power BI 报表列表中选择你的新报表，然后选择“保存”。 该报表将显示在 Teams 通道的新选项卡中。
   
   :::image type="content" source="media/powerbi/teams.png" alt-text="显示在 Teams 通道的选项卡中显示“Power BI”报表的屏幕截图。":::

## <a name="schedule-report-refresh"></a>计划报表刷新

按计划刷新 Power BI 报表，以便更新后的数据始终显示在报表中。

1. 在 Power BI 服务中，选择要发布报表的工作区。
   
1. 在报表的数据集旁，选择“更多选项”菜单 > “设置”。
   
   :::image type="content" source="media/powerbi/settings.png" alt-text="显示 Power BI 报表数据集中“更多选项”菜单下的“设置”的屏幕截图。":::
   
1. 选择“编辑凭据”，为拥有 Log Analytics 工作区读取访问权限的帐户提供凭据。
   
1. 在“计划刷新”下，将滑块设置为“开”，并设置报表的刷新计划。
   
   :::image type="content" source="media/powerbi/schedule.png" alt-text="显示 Power BI 报表数据集的计划刷新设置的屏幕截图。":::

## <a name="next-steps"></a>后续步骤

- [将 Azure Monitor 日志数据导入到 Power BI 中](../azure-monitor/visualize/powerbi.md)
- [Power Query M 公式语言](/powerquery-m/)