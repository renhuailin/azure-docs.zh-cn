---
title: Azure 安全中心中的工作簿库
description: 了解如何通过集成的 Azure Monitor 工作簿库创建丰富的交互式 Azure 安全中心数据报表
author: memildin
ms.author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 07/07/2021
ms.openlocfilehash: dda75aedab0c17f14d2725ff9759d7ab30203474
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2021
ms.locfileid: "129712931"
---
# <a name="create-rich-interactive-reports-of-security-center-data"></a>创建丰富的交互式安全中心数据报表

[Azure Monitor 工作簿](../azure-monitor/visualize/workbooks-overview.md)提供了一块灵活的画布，以用于分析数据以及在 Azure 门户中创建丰富的视觉报表。 使用工作簿可以在整个 Azure 中接入多个数据源，并将其组合成统一的交互式体验。

工作簿提供了一组丰富的功能，用于可视化 Azure 数据。 有关每种可视化效果类型的详细示例，请参阅[可视化效果示例和文档](../azure-monitor/visualize/workbooks-text-visualizations.md)。 

在 Azure 安全中心内，你可以访问内置工作簿来跟踪组织的安全状况。 你还可以生成自定义工作簿，以查看安全中心或其他受支持的数据源中的各种数据。

:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-snip.png" alt-text="一段时间内的安全功能分数工作簿。":::

## <a name="availability"></a>可用性

| 方面                          | 详细信息                                                                                                                                      |
|---------------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------|
| 发布状态：                  | 正式发布版 (GA)                                                                                                                    |
| 定价：                        | 免费                                                                                                                                         |
| 所需角色和权限： | 若要保存工作簿，必须至少对目标资源组具有[工作簿参与者](../role-based-access-control/built-in-roles.md#workbook-contributor)权限 |
| 云：                         | :::image type="icon" source="./media/icons/yes-icon.png"::: 商用云<br>:::image type="icon" source="./media/icons/yes-icon.png"::: 国家/地区/主权云（Azure 政府、Azure 中国世纪互联） |
|                                 |                                                                                                                                              |

## <a name="workbooks-gallery-in-azure-security-center"></a>Azure 安全中心中的工作簿库

利用集成的 Azure 工作簿功能，Azure 安全中心可以轻松构建自定义交互式工作簿。 安全中心还包含一个库，其中有可供自定义的下列工作簿：

- [“一段时间内的安全功能分数”工作簿](#use-the-secure-score-over-time-workbook) - 跟踪订阅的分数以及对资源建议的更改
- [“系统更新”工作簿](#use-the-system-updates-workbook) - 按资源、OS 和严重性等查看缺失的系统更新
- [“漏洞评估发现结果”工作簿](#use-the-vulnerability-assessment-findings-workbook) - 查看对 Azure 资源进行漏洞扫描的发现结果
- [一段时间内的合规性"工作簿](#use-the-compliance-over-time-workbook) - 查看订阅是否符合所选法规或行业标准的状态 

:::image type="content" source="media/custom-dashboards-azure-workbooks/workbooks-gallery-security-center.png" alt-text="Azure 安全中心中内置工作簿的库。":::

选择一个提供的工作簿或创建自己的工作簿。

> [!TIP]
> 使用“编辑”按钮自定义提供的任何工作簿以满足你的需求。 完成编辑后，选择“保存”，所做的更改将保存到新工作簿中。
> 
> :::image type="content" source="media/custom-dashboards-azure-workbooks/editing-supplied-workbooks.png" alt-text="编辑提供的工作簿以根据特定需求进行自定义。":::

### <a name="use-the-secure-score-over-time-workbook"></a>使用“一段时间内的安全功能分数”工作簿

此工作簿使用 Log Analytics 工作区中的安全功能分数数据。 需要从连续导出工具中导出该数据，如[从 Azure 门户中的安全中心页面配置连续导出](continuous-export.md?tabs=azure-portal)中所述。

设置连续导出时，将导出频率设置为“流式处理更新”和“快照”。

:::image type="content" source="media/custom-dashboards-azure-workbooks/export-frequency-both.png" alt-text="对于“一段时间内的安全功能分数”工作簿，需要从连续导出配置中的“导出频率”设置中选择这两个选项。":::

> [!NOTE]
> 快照每周导出一次，因此你需要等待至少一周的时间来导出第一个快照，然后才能查看此工作簿中的数据。

> [!TIP]
> 若要在组织中配置连续导出，请使用[大规模配置连续导出](continuous-export.md?tabs=azure-policy)中所述的提供的 Azure Policy“DeployIfNotExist”策略。

“一段时间内的安全功能分数”工作簿有五个图形，用于向所选工作区报告订阅：

|图形  |示例  |
|---------|---------|
|上周和上个月的分数趋势<br>使用此部分监视订阅的当前分数和分数的一般趋势。|:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-table-1.png" alt-text="内置工作簿的安全功能分数趋势。":::|
|所有所选订阅的聚合分数<br>将鼠标悬停在趋势线中的任何点上可查看所选时间范围内任何日期的聚合分数。|:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-table-2.png" alt-text="所有选择的订阅的聚合分数。":::|
|对大多数运行不正常资源的建议<br>此表可帮助你对所选时间段内大多数资源更改为运行不正常的建议进行会审。|:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-table-3.png" alt-text="导致运行不正常的资源最多的建议。":::|
|特定安全控件的分数<br>安全中心的安全控件是建议的逻辑分组。 此图表一目了然地显示了所有控件的每周分数。|:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-table-4.png" alt-text="所选时间段内安全控制措施的分数。":::|
|资源更改<br>此处列出了在所选时间段内已更改状态（正常、运行不正常或不适用）的大多数资源的建议。 从列表中选择任何建议，以打开列出特定资源的新表。|:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-table-5.png" alt-text="导致运行状况状态发生更改的资源最多的建议。":::|

### <a name="use-the-system-updates-workbook"></a>使用“系统更新”工作簿

此工作簿基于安全建议“应在计算机上安装系统更新”。

该工作簿有助于识别具有未完成更新的计算机。

可以根据以下内容查看所选订阅的情况：

- 具有未完成的更新的资源列表
- 资源中缺少的更新列表

:::image type="content" source="media/custom-dashboards-azure-workbooks/system-updates-report.png" alt-text="安全中心的系统更新工作簿（基于缺少的更新安全建议）":::

### <a name="use-the-vulnerability-assessment-findings-workbook"></a>使用“漏洞评估结果”工作簿

安全中心包括计算机的漏洞扫描程序、容器注册表中的容器和 SQL 服务器。

详细了解如何使用这些扫描程序：

- [通过集成 Qualys 扫描程序扫描计算机](deploy-vulnerability-assessment-vm.md)
- [扫描注册表映像是否存在漏洞](defender-for-container-registries-usage.md)
- [扫描 SQL 资源是否存在漏洞](defender-for-sql-on-machines-vulnerability-assessment.md)

每个扫描程序的结果在单独的建议中报告：

- 应修正虚拟机中的漏洞
- 应修正 Azure 容器注册表映像中的漏洞（由 Qualys 提供技术支持）
- 应修正关于 SQL 数据库的漏洞评估结果
- 应修正关于计算机上 SQL 服务器的漏洞评估结果

此工作簿收集这些结果，并按严重性、资源类型和类别对其进行整理。

:::image type="content" source="media/custom-dashboards-azure-workbooks/vulnerability-assessment-findings-report.png" alt-text="安全中心的漏洞评估结果报表。":::


### <a name="use-the-compliance-over-time-workbook"></a>使用“一段时间内的合规性”工作簿

Azure 安全中心会不断将资源的配置与行业标准、法规和基准中的要求进行比较。 内置标准包括 NIST SP 800-53、SWIFT CSP CSCF v2020、加拿大联邦 PBMM、HIPAA HITRUST 等。 可以使用“法规合规性”仪表板选择与组织相关的具体标准。 有关详情，请参阅[在监管合规仪表板中自定义标准集](update-regulatory-compliance-packages.md)。

通过此工作簿，可以使用添加到仪表板的各种标准跟踪一段时间内的合规性状态。

:::image type="content" source="media/custom-dashboards-azure-workbooks/compliance-over-time-select-standards.png" alt-text="为“一段时间内的合规性”报表选择标准。":::

从报表的概述区域选择标准时，下面的窗格将显示更详细的明细：

:::image type="content" source="media/custom-dashboards-azure-workbooks/compliance-over-time-details.png" alt-text="有关具体标准的详细更改明细。":::

可以继续深入，一直到建议级别，以查看已通过或未通过每个控件的资源。 

> [!TIP]
> 对于报表的每个面板，可以使用“导出到Excel”选项将数据导出 Excel。
>
> :::image type="content" source="media/custom-dashboards-azure-workbooks/export-workbook-data.png" alt-text="将合规性工作簿数据导出到 Excel。":::


## <a name="import-workbooks-from-other-workbook-galleries"></a>从其他工作簿库导入工作簿

如果已在其他 Azure 服务中生成工作簿，并且想要将其移动到 Azure 安全中心工作簿库中：

1. 打开目标工作簿。

1. 在工具栏中选择“编辑”。

    :::image type="content" source="media/custom-dashboards-azure-workbooks/editing-workbooks.png" alt-text="编辑 Azure Monitor 工作簿。":::

1. 在工具栏中选择“</>”以进入高级编辑器。

    :::image type="content" source="media/custom-dashboards-azure-workbooks/editing-workbooks-advanced-editor.png" alt-text="启动高级编辑器以获取库模板 JSON 代码。":::

1. 复制工作簿的库模板 JSON。

1. 打开安全中心中的工作簿库，并从菜单栏中选择“新建”。
1. 选择“</>”以进入高级编辑器。
1. 粘贴在整个库模板 JSON 中。
1. 选择“应用”。
1. 在工具栏上选择“另存为”。

    :::image type="content" source="media/custom-dashboards-azure-workbooks/editing-workbooks-save-as.png" alt-text="将工作簿保存到安全中心内的库。":::

1. 输入保存工作簿所需的详细信息：
   1. 工作簿的名称
   2. 所需区域
   3. 订阅、资源组和适当的共享。

你将在“最近修改的工作簿”类别中找到保存的工作簿。


## <a name="next-steps"></a>后续步骤

本文介绍了安全中心的集成的 Azure Monitor 工作簿页面，其中包含内置报表和用于生成你自己的自定义交互式报表的选项。

- 详细了解 [Azure Monitor 工作簿](../azure-monitor/visualize/workbooks-overview.md)
- 内置工作簿从安全中心的建议中提取数据。 了解[安全建议 - 参考指南](recommendations-reference.md)中的许多安全建议