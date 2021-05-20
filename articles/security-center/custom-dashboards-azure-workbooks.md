---
title: Azure 安全中心中的工作簿库
description: 了解如何通过集成的 Azure Monitor 工作簿库创建丰富的交互式 Azure 安全中心数据报表
author: memildin
ms.author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 03/04/2021
ms.openlocfilehash: 198702f619e490e8000e4430aab23a7f6bfb6d85
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "102107370"
---
# <a name="create-rich-interactive-reports-of-security-center-data"></a>创建丰富的交互式安全中心数据报表

[Azure Monitor 工作簿](../azure-monitor/visualize/workbooks-overview.md)提供了一块灵活的画布，以用于分析数据以及在 Azure 门户中创建丰富的视觉报表。 使用工作簿可以在整个 Azure 中接入多个数据源，并将其组合成统一的交互式体验。

工作簿提供了一组丰富的功能，用于可视化 Azure 数据。 有关每种可视化效果类型的详细示例，请参阅[可视化效果示例和文档](../azure-monitor/visualize/workbooks-text-visualizations.md)。 

在 Azure 安全中心内，可以访问内置报表以跟踪组织的安全状况。 还可以生成自定义报表，以查看安全中心或其他支持的数据源中的各种数据。

:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-snip.png" alt-text="一段时间内的安全功能分数报告":::

## <a name="availability"></a>可用性

| 方面                          | 详细信息                                                                                                                                      |
|---------------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------|
| 发布状态：                  | 预览<br>[!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)]                                                       |
| 定价：                        | 免费                                                                                                                                         |
| 所需角色和权限： | 若要保存工作簿，必须至少拥有目标资源组的工作簿参与者权限                                      |
| 云：                         | ![是](./media/icons/yes-icon.png) 商业云<br>![是](./media/icons/yes-icon.png) 国家/主权（US Gov、中国 Gov、其他 Gov） |
|                                 |                                                                                                                                              |

## <a name="workbooks-gallery-in-azure-security-center"></a>Azure 安全中心中的工作簿库

利用集成的 Azure 工作簿功能，Azure 安全中心可以轻松生成你自己的自定义交互式报表。 安全中心还包括一个工作簿库，其中包含以下可供你自定义的报表：

- 一段时间内的安全功能分数 - 跟踪订阅的分数以及对资源建议的更改
- 系统更新 - 按资源、OS 和严重性等查看缺失的系统更新
- 漏洞评估发现结果 - 查看对 Azure 资源进行漏洞扫描的发现结果

:::image type="content" source="media/custom-dashboards-azure-workbooks/workbooks-gallery-security-center.png" alt-text="Azure 安全中心中内置工作簿的库":::

选择一个提供的报表或创建自己的报表。

> [!TIP]
> 使用“编辑”按钮自定义任何提供的报表以使自己满意。 完成编辑后，选择“保存”，所做的更改将保存到新工作簿中。
> 
> :::image type="content" source="media/custom-dashboards-azure-workbooks/editing-supplied-workbooks.png" alt-text="编辑提供的工作簿以根据特定需求进行自定义":::

### <a name="use-the-secure-score-over-time-report"></a>使用“一段时间内的安全分数”报表

此报表使用 Log Analytics 工作区中的安全分数数据。 需要从连续导出工具中导出该数据，如[从 Azure 门户中的安全中心页面配置连续导出](continuous-export.md?tabs=azure-portal)中所述。

设置连续导出时，将导出频率设置为“流式处理更新”和“快照”。

:::image type="content" source="media/custom-dashboards-azure-workbooks/export-frequency-both.png" alt-text="对于“一段时间内的安全分数”工作簿，你将需要从连续导出配置中的导出频率设置中选择这两个选项":::

> [!NOTE]
> 快照每周导出一次，因此你需要等待至少一周才能导出第一个快照，然后才能查看此报表中的数据。

> [!TIP]
> 若要在组织中配置连续导出，请使用[大规模配置连续导出](continuous-export.md?tabs=azure-policy)中所述的提供的 Azure Policy“DeployIfNotExist”策略。

“一段时间内的安全分数”报表有五个图形，用于向所选工作区报告订阅：


|Graph  |示例  |
|---------|---------|
|上周和上个月的分数趋势<br>使用此部分监视订阅的当前分数和分数的一般趋势。|:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-table-1.png" alt-text="内置报表的安全分数趋势":::|
|所有所选订阅的聚合分数<br>将鼠标悬停在趋势线中的任何点上可查看所选时间范围内任何日期的聚合分数。|:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-table-2.png" alt-text="所有所选订阅的聚合分数":::|
|对大多数运行不正常资源的建议<br>此表可帮助你对所选时间段内大多数资源更改为运行不正常的建议进行会审。|:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-table-3.png" alt-text="对大多数运行不正常资源的建议":::|
|特定安全控件的分数<br>安全中心的安全控件是建议的逻辑分组。 此图表一目了然地显示了所有控件的每周分数。|:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-table-4.png" alt-text="所选时间段内安全控件的分数":::|
|资源更改<br>此处列出了在所选时间段内已更改状态（正常、运行不正常或不适用）的大多数资源的建议。 从列表中选择任何建议，以打开列出特定资源的新表。|:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-table-5.png" alt-text="已更改运行状况状态的大多数资源的建议":::|

### <a name="use-the-system-updates-report"></a>使用“系统更新”报表

此报表基于安全建议“应在计算机上安装系统更新”。

该报表有助于识别具有未完成的更新的计算机。

可以根据以下内容查看所选订阅的情况：

- 具有未完成的更新的资源列表
- 资源中缺少的更新列表

:::image type="content" source="media/custom-dashboards-azure-workbooks/system-updates-report.png" alt-text="安全中心的系统更新报告（基于缺少的更新安全建议）":::

### <a name="use-the-vulnerability-assessment-findings-report"></a>使用“漏洞评估结果”报表

安全中心包括计算机的漏洞扫描程序、容器注册表中的容器和 SQL 服务器。

详细了解如何使用这些扫描程序：

- [通过集成 VA 扫描程序扫描计算机](deploy-vulnerability-assessment-vm.md)
- [扫描注册表映像是否存在漏洞](defender-for-container-registries-usage.md)
- [扫描 SQL 资源是否存在漏洞](defender-for-sql-on-machines-vulnerability-assessment.md)

每个扫描程序的结果在单独的建议中报告：

- 应修正虚拟机中的漏洞
- 应修正 Azure 容器注册表映像中的漏洞（由 Qualys 提供技术支持）
- 应修正关于 SQL 数据库的漏洞评估结果
- 应修正关于计算机上 SQL 服务器的漏洞评估结果

此报表收集这些结果，并按严重性、资源类型和类别对其进行组织。

:::image type="content" source="media/custom-dashboards-azure-workbooks/vulnerability-assessment-findings-report.png" alt-text="安全中心的漏洞评估结果报表":::


## <a name="import-workbooks-from-other-workbook-galleries"></a>从其他工作簿库导入工作簿

如果已在其他 Azure 服务中生成工作簿，并且想要将其移动到 Azure 安全中心工作簿库中：

1. 打开目标工作簿。

1. 在工具栏中选择“编辑”。

    :::image type="content" source="media/custom-dashboards-azure-workbooks/editing-workbooks.png" alt-text="编辑 Azure Monitor 工作簿":::

1. 在工具栏中选择“</>”以进入高级编辑器。

    :::image type="content" source="media/custom-dashboards-azure-workbooks/editing-workbooks-advanced-editor.png" alt-text="启动高级编辑器以获取库模板 JSON 代码":::

1. 复制工作簿的库模板 JSON。

1. 打开安全中心中的工作簿库，并从菜单栏中选择“新建”。
1. 选择“</>”以进入高级编辑器。
1. 粘贴在整个库模板 JSON 中。
1. 选择“应用”。
1. 在工具栏上选择“另存为”。

    :::image type="content" source="media/custom-dashboards-azure-workbooks/editing-workbooks-save-as.png" alt-text="将工作簿保存到安全中心中的库":::

1. 输入保存工作簿所需的详细信息：
   1. 工作簿的名称
   2. 所需区域
   3. 订阅、资源组和适当的共享。

你将在“最近修改的工作簿”类别中找到保存的工作簿。


## <a name="next-steps"></a>后续步骤

本文介绍了安全中心的集成的 Azure Monitor 工作簿页面，其中包含内置报表和用于生成你自己的自定义交互式报表的选项。

- 详细了解 [Azure Monitor 工作簿](../azure-monitor/visualize/workbooks-overview.md)
- 内置报表从安全中心的建议中提取数据。 了解[安全建议 - 参考指南](recommendations-reference.md)中的许多安全建议