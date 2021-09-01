---
title: 使用 Azure Sentinel 中的威胁指标 |Microsoft Docs
description: 本文介绍如何利用 Azure Sentinel 中的威胁情报指标查看、创建、管理、可视化和检测威胁。
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/27/2021
ms.author: yelevin
ms.openlocfilehash: 2ecc10c43600a20ceb7209f651f27e78e7b286be
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2021
ms.locfileid: "122181270"
---
# <a name="work-with-threat-indicators-in-azure-sentinel"></a>使用 Azure Sentinel 中的威胁指标

可以通过以下活动将威胁情报 (TI) 集成到 Azure Sentinel 中：

- 通过启用面向各种 TI [平台](connect-threat-intelligence-tip.md)和[源](connect-threat-intelligence-taxii.md)的数据连接器，将威胁情报导入 Azure Sentinel 。

- 在“日志”和 Azure Sentinel 的“威胁情报”边栏选项卡中查看和管理导入的威胁情报  。

- 使用内置的 Analytics 规则模板，根据导入的威胁情报，检测威胁并生成安全警报和事件 。

- 利用“威胁情报”工作簿直观显示 Azure Sentinel 中导入的威胁情报的关键信息 。

> [!IMPORTANT]
> 请注意，这些功能目前为预览版。 [Azure 预览版补充条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)包含适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。
>

## <a name="view-your-threat-indicators-in-azure-sentinel"></a>查看 Azure Sentinel 中的威胁指标

### <a name="find-and-view-your-indicators-in-logs"></a>在日志中查找和查看指标

可以在存储所有 Azure Sentinel 事件数据的“日志”的 ThreatIntelligenceIndicator 表（Azure Sentinel 组下）中查看成功导入的威胁指标，无论使用的是源还是连接器  。 该表是由其他 Azure Sentinel 功能（例如 Analytics 和工作簿）执行的威胁情报查询的基础。

1. 打开 [Azure 门户](https://portal.azure.com/)，导航到 Azure Sentinel 服务。

1. 选择使用任一威胁情报数据连接器将威胁指标导入到的工作区。

1. 从 Azure Sentinel 菜单的“常规”部分中选择“日志”。

1. ThreatIntelligenceIndicator 表位于 Azure Sentinel 组下方。

1. 选择表名称旁边的“预览数据”图标（眼睛），然后选择“在查询编辑器中查看”按钮以执行查询，该查询将显示此表中的记录。

结果应类似于下面所示的示例威胁指标：

:::image type="content" source="media/work-with-threat-indicators/threat-intel-sample-query.png" alt-text="示例查询数据":::

### <a name="find-and-view-your-indicators-in-the-threat-intelligence-blade"></a>在威胁情报边栏选项卡中查找和查看指标

还可以在新的“威胁情报”边栏选项卡（可从 Azure Sentinel 主菜单访问）中查看和管理指标。 可以对导入的威胁指标进行排序、筛选和搜索，甚至无需编写 Log Analytics 查询。 借助这个新功能，你还可以直接在 Azure Sentinel 界面中创建威胁指标，以及执行两个常见的威胁情报管理任务：指标标记和创建与安全调查相关的新指标。

#### <a name="create-a-new-indicator"></a>创建新指标

1. 在 [Azure 门户](https://portal.azure.com/)中，导航到“Azure Sentinel”服务。

1. 选择使用任一威胁情报数据连接器将威胁指标导入到的工作区。

1. 从 Azure Sentinel 菜单的“威胁管理”部分选择“威胁情报”。

1. 从页面顶部的菜单栏中选择“新增”按钮。

    :::image type="content" source="media/work-with-threat-indicators/threat-intel-add-new-indicator.png" alt-text="添加新的威胁指标" lightbox="media/work-with-threat-indicators/threat-intel-add-new-indicator.png":::

1. 选择指标类型，然后在新指标面板上填写此窗体。 必填字段标有红色的星号 (*)。

1. 选择“应用”。 系统会将该指标添加到指标列表，同时发送到日志中的 ThreatIntelligenceIndicator 表中。

#### <a name="tag-threat-indicators"></a>标记威胁指标

通过标记威胁指标，可以轻松地对它们进行分组，使其更易于查找。 通常，你可以将标记应用于与特定事件相关的指标，或应用于表示来自某个已知参与者或已知攻击活动的威胁的指标。 你可以单独标记威胁指标，也可以选择多个指标，并同时对它们进行标记。 下面显示了使用事件 ID 标记多个指标的示例。 由于标记采用自由格式，建议的做法是为威胁指标标记创建标准命名约定。 可以将多个标记应用于每个指示器。

:::image type="content" source="media/work-with-threat-indicators/threat-intel-tagging-indicators.png" alt-text="向威胁指标应用标记" lightbox="media/work-with-threat-indicators/threat-intel-tagging-indicators.png":::

## <a name="detect-threats-with-threat-indicator-based-analytics"></a>通过基于威胁指标的分析来检测威胁

在诸如 Azure Sentinel 之类的 SIEM 解决方案中，威胁指标最重要的用例是为威胁检测的分析规则提供支持。 这些基于指标的规则将数据源中的原始事件与威胁指标进行比较，以确定组织中安全威胁的存在。 在 Azure Sentinel 的“分析”中，你可以创建按计划运行的分析规则并生成安全警报。 规则由查询以及一些配置驱动，这些配置用于确定规则的运行频率、应生成安全警报和事件的查询结果类型，以及触发何种自动化作为响应。

你始终可以从头开始创建新的分析规则，不过，Azure Sentinel 提供了一组由 Microsoft 安全工程师创建的内置规则模板，你可以按原样使用这些模板，也可以根据需要进行修改。 你可以轻松识别使用威胁指标的规则模板，因为它们的标题都以“TI 将…”开头。 所有这些规则模板的运行方式都相似，唯一的区别在于所用的威胁指标类型（域、电子邮件、文件哈希、IP 地址或 URL）以及要匹配的事件类型。 每个模板都列出了规则起作用所需的数据源，因此你可以一目了然地看到是否已在 Azure Sentinel 中导入了必要的事件。 编辑并保存现有规则模板或创建新规则时，该规则会默认会启用。

### <a name="configure-a-rule-to-generate-security-alerts"></a>配置规则以生成安全警报

以下示例介绍了如何启用和配置规则，以使用导入到 Azure Sentinel 的威胁指标生成安全警报。 在此示例中，我们将使用名为“TI 将 IP 实体映射到 AzureActivity”的规则模板。 此规则会将所有 IP 地址类型的威胁指标与所有 Azure 活动事件进行匹配。 找到匹配项后，系统会生成一个警报，以及一个相应的事件，供安全操作团队进行调查。 只有启用了“威胁情报”数据连接器（用于导入威胁指标）和/或“Azure 活动”数据连接器（用于导入 Azure 订阅级事件），此分析规则才能成功运行。

1. 在 [Azure 门户](https://portal.azure.com/)中，导航到“Azure Sentinel”服务。

1. 选择将威胁指标（使用“威胁情报”数据连接器）和 Azure 活动数据（使用“Azure 活动”数据连接器）导入到的工作区。

1. 从 Azure Sentinel 菜单的“配置”部分中选择“分析”。

1. 选择“规则模板”选项卡以查看可用分析规则模板的列表。

1. 查找标题为“TI 将 IP 实体映射到 AzureActivity”的规则，确保已连接所有必需的数据源，如下所示。

    :::image type="content" source="media/work-with-threat-indicators/threat-intel-required-data-sources.png" alt-text="必需的数据源":::

1. 选择此规则，然后选择“创建规则”按钮。 此操作将打开一个向导来配置规则。 完成此处的设置，然后选择“下一步: 设置规则逻辑 >”按钮。

    :::image type="content" source="media/work-with-threat-indicators/threat-intel-create-analytics-rule.png" alt-text="创建分析规则":::

1. 向导的规则逻辑部分已预先填充了以下各项：
    - 将在规则中使用的查询。

    - 实体映射，用于指示 Azure Sentinel 如何识别帐户、IP 地址和 URL 等实体，以便事件和调查了解如何在此规则生成的任何安全警报中处理这些数据。

    - 此规则的运行计划。

    - 生成安全警报之前所需的查询结果数。

    模板中的默认设置为：
    - 每小时运行一次。

    - 将 ThreatIntelligenceIndicator 表中的所有 IP 地址威胁指标与在 AzureActivity 表中的最后一小时事件中找到的所有 IP 地址进行匹配。

    - 如果查询结果大于零（即找到任何匹配项），则生成安全警报。

    可以保留默认设置，或更改其中任何一个以满足你的要求，并且可以在“事件设置”选项卡上定义事件生成设置。有关详细信息，请参阅[创建自定义分析规则以检测威胁](detect-threats-custom.md)。 完成后，选择“自动响应”选项卡。

1. 配置根据此分析规则生成安全警报时要触发的任何自动化操作。 结合使用自动化规则和 playbook（由 Azure 逻辑应用提供支持）来完成 Azure Sentinel 中的自动化操作。 有关更多信息，请参阅[教程：在 Azure Sentinel 中结合自动化规则使用 playbook](./tutorial-respond-threats-playbook.md)。 完成后，选择“下一步：查看 >”按钮继续操作。

1. 当你看到规则验证已通过的消息时，请选择“创建”按钮并完成操作。

可以在 Azure Sentinel 的 Analytics 部分的“活动规则”选项卡中找到已启用的规则 。 你可以在其中编辑、启用、禁用、复制或删除活动规则。 新规则在激活后立即运行，并从那时起按定义的计划运行。

根据默认设置，规则每次按计划运行时，发现的任何结果都会生成安全警报。 可以在 Azure Sentinel 的“日志”部分，Azure Sentinel 组下的 SecurityAlert表中查看 Azure Sentinel 中的安全警报。

在 Azure Sentinel 中，根据分析规则生成的警报还会生成安全事件，这些事件可以在 Azure Sentinel 菜单的“威胁管理”下的“事件”中找到。 安全操作团队将对事件进行会审和调查，以确定适当的响应操作。 你可以在此[教程：使用 Azure Sentinel 调查事件](./investigate-cases.md)中找到详细信息。

## <a name="detect-threats-using-matching-analytics-public-preview"></a>使用匹配分析（公共预览版）检测威胁

使用内置的 Microsoft 威胁情报匹配分析分析规则模板来[创建规则](detect-threats-built-in.md#use-built-in-analytics-rules)，让 Azure Sentinel 将 Microsoft 生成的威胁情报数据与已引入到 Azure Sentinel 的日志相匹配。

将威胁情报数据与日志相匹配有助于生成高保真警报和事件，并应用相应的严重性。 找到匹配项后，生成的所有警报都将分组为事件。

根据每个可观察的基准，在 24 小时内对警报进行分组。 例如，在 24 小时时间段内生成的所有警报（与 `abc.com` 域匹配）都将分组为单个事件。

### <a name="triage-through-an-incident-generated-by-matching-analytics"></a>对通过匹配分析生成的事件进行会审

找到匹配项后，生成的所有警报都将分组为事件。

使用以下步骤会审 Microsoft 威胁情报匹配分析规则生成的事件：

1. 在 Azure Sentinel 工作区（启用了 Microsoft 威胁情报匹配分析规则）中，选择“事件”并搜索 Microsoft 威胁情报分析。

    找到的任何事件都显示在网格中。

1. 选择“查看完整详细信息”以查看实体和有关事件的其他详细信息，例如特定警报。

    例如：

    :::image type="content" source="media/work-with-threat-indicators/matching-analytics.png" alt-text="示例匹配分析详细信息。":::

找到匹配项后，该指标也会发布到 Log Analytics ThreatIntelligenceIndicators，并显示在“威胁情报”页面中。  对于此规则发布的任何指标，将源定义为 Microsoft 威胁情报分析。

例如，在 ThreatIntelligenceIndicators 日志中：

:::image type="content" source="media/work-with-threat-indicators/matching-analytics-logs.png" alt-text="ThreatIntelligenceIndicators 日志中显示的匹配分析。":::

在威胁情报页面：

:::image type="content" source="media/work-with-threat-indicators/matching-analytics-threat-intelligence.png" alt-text="威胁情报页面中显示的匹配分析。":::

### <a name="supported-log-sources-for-matching-analytics"></a>匹配分析支持的日志源

以下日志源当前支持 Microsoft 威胁情报匹配分析规则：

|日志源  |说明  |
|---------|---------|
|[CEF](connect-common-event-format.md)     |  对 Log Analytics CommonSecurityLog 表中所包括的所有 CEF 日志执行匹配，但 `DeviceVendor` 列为 `Cisco` 的除外。 <br><br>若要将 Microsoft 生成的威胁情报与 CEF 日志匹配，请确保在 CEF 日志的字段中映射 `RequestURL` 域。      |
|[DNS](connect-dns.md)     | 对于从客户端查找 DNS 查询到 DNS 服务 (`SubType == "LookupQuery"`) 的所有 DNS 日志，将进行匹配。 DNS 查询仅针对 IPv4 (`QueryType=”A”`) 和 IPv6 查询 (`QueryType=” AAAA”`)。<br><br>若要将 Microsoft 生成的威胁情报与 DNS 日志进行匹配，无需手动映射列，因为所有列都是 Windows DNS 服务器的标准列，默认情况下，域将位于 `Name` 列中。   |
|[Syslog](connect-syslog.md)     |  当前仅对 Syslog 事件（其中 `Facility` 为 `cron`）执行匹配。 <br><br>将 Microsoft 生成的威胁情报与 Syslog 进行匹配，无需手动映射列。 默认情况下，详细信息位于 Syslog 的 `SyslogMessage` 字段中，规则将直接从 SyslogMessage 对域进行分析。     |
|     |         |


## <a name="workbooks-provide-insights-about-your-threat-intelligence"></a>工作簿提供有关威胁情报的见解

你可以使用目标构建的 Azure Sentinel 工作簿直观显示 Azure Sentinel 中威胁情报的关键信息，并且可以根据业务需求轻松自定义工作簿。
现在介绍如何查找 Azure Sentinel 中提供的威胁情报工作簿，以及如何编辑工作簿的示例，以便对其进行自定义。

1. 在 [Azure 门户](https://portal.azure.com/)中，导航到“Azure Sentinel”服务。

1. 选择使用任一威胁情报数据连接器将威胁指标导入到的工作区。

1. 从 Azure Sentinel 菜单的“威胁管理”部分中选择“工作簿”。

1. 查找标题为“威胁情报”的工作簿，并验证 ThreatIntelligenceIndicator 表中是否包含数据，如下所示。

    :::image type="content" source="media/work-with-threat-indicators/threat-intel-verify-data.png" alt-text="验证数据":::

1. 选择“保存”按钮，然后选择用于存储工作簿的 Azure 位置。 如果要以任何方式修改工作簿并保存更改，就必须执行此步骤。

1. 现在，选择“查看保存的工作簿”按钮以打开工作簿进行查看和编辑。

1. 现在应该可以看到模板提供的默认图表。 若要修改图表，选择页面顶部的“编辑”按钮，进入工作簿的编辑模式。

1. 按威胁类型添加一个新的威胁指标图表。 滚动到页面底部，选择“添加查询”。

1. 将以下文本添加到“Log Analytics 工作区日志查询”文本框中：
    ```kusto
    ThreatIntelligenceIndicator
    | summarize count() by ThreatType
    ```

1. 在“可视化效果”下拉列表中，选择“条形图”。

1. 选择“完成编辑”按钮。 你已经为工作簿创建了一个新图表。

    :::image type="content" source="media/work-with-threat-indicators/threat-intel-bar-chart.png" alt-text="条形图":::

工作簿提供功能强大的交互式仪表板，让你能够深入了解 Azure Sentinel 的各个方面。 你可以用工作簿做很多事情；虽然系统提供的模板是一个很好的起点，但你可能想要深入了解并自定义这些模板，或者结合许多不同的数据源创建新的仪表板，以便通过独特的方式直观显示数据。 Azure Sentinel 工作簿基于 Azure Monitor 工作簿，因此，系统已经提供大量文档以及更多模板。 介绍如何[使用 Azure Monitor 工作簿创建交互式报表](../azure-monitor/visualize/workbooks-overview.md)的这篇文章是一个很好的起点。 

GitHub 上还有一个内容丰富的 [Azure Monitor 工作簿社区](https://github.com/microsoft/Application-Insights-Workbooks)，你可以下载更多模板并贡献自己的模板。

## <a name="next-steps"></a>后续步骤

在本文中，你将了解在 Azure Sentinel 过程中使用威胁情报指标的所有方式。 有关 Azure Sentinel 中威胁情报的详细信息，请参阅以下文章：
- [理解 Azure Sentinel 中的威胁情报](understand-threat-intelligence.md)。
- 将 Azure Sentinel 连接到 [STIX/TAXII 威胁情报源](./connect-threat-intelligence-taxii.md)。
- [将威胁情报平台连接](./connect-threat-intelligence-tip.md)到 Azure Sentinel。
- 查看哪些 [TIP 平台、TAXII 源和扩充](threat-intelligence-integration.md)可轻松与 Azure Sentinel 集成。