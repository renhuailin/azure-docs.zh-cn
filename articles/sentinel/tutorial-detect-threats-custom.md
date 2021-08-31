---
title: 使用 Azure Sentinel 创建自定义分析规则以检测威胁 | Microsoft Docs
description: 了解如何使用 Azure Sentinel 创建自定义分析规则，以检测安全威胁。 利用事件分组、警报分组和警报扩充功能，了解自动禁用设置。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/17/2021
ms.author: yelevin
ms.openlocfilehash: 100d58d291fd40ba6b823c8aab4725f38bb917ed
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114468131"
---
# <a name="create-custom-analytics-rules-to-detect-threats"></a>创建自定义分析规则以检测威胁

将[数据源连接](quickstart-onboard.md)到 Azure Sentinel 后，需创建自定义分析规则，帮助发现环境中的威胁和异常行为。

分析规则将在你的整个环境中搜索特定事件或事件集，在达到特定事件阈值或条件时发出警报，生成故障事件以供 SOC 进行会审和调查，并通过自动化跟踪和修正流程来响应威胁。

> [!TIP]
> 创建自定义规则时，请使用现有规则作为模板或引用。 使用现有规则作为基线有助于在进行任何需要的更改之前构建大多数逻辑。
> 

> [!div class="checklist"]
> * 创建分析规则
> * 定义处理事件和警报的方式
> * 定义生成警报和故障事件的方式
> * 为规则选择自动化威胁响应

## <a name="create-a-custom-analytics-rule-with-a-scheduled-query"></a>使用计划查询创建自定义分析规则

1. 从 Azure Sentinel 导航菜单中，选择“分析”。

1. 在顶部的操作栏中，选择“+创建”，然后选择“计划查询规则”。 这样会打开“分析规则向导”。

    :::image type="content" source="media/tutorial-detect-threats-custom/create-scheduled-query-small.png" alt-text="创建计划查询" lightbox="media/tutorial-detect-threats-custom/create-scheduled-query-full.png":::

### <a name="analytics-rule-wizard---general-tab"></a>分析规则向导 -“常规”选项卡

- 提供一个唯一的“名称”和“说明”。 

- 在“策略”字段中，可以从攻击类别中进行选择，对规则进行分类。 这些类别基于 [MITRE ATT&CK](https://attack.mitre.org/) 框架的策略生成。

- 根据需要设置警报“严重性”。 

- 在你创建规则时，默认情况下规则的“状态”为“已启用”，这表示当你完成创建后，它将立即运行。 如果你不需要让此规则立即运行，请选择“已禁用”，这样就会将它添加到“可用规则”选项卡中，当你需要运行此规则时，可从该选项卡中启用它。

   :::image type="content" source="media/tutorial-detect-threats-custom/general-tab.png" alt-text="开始创建自定义分析规则":::

## <a name="define-the-rule-query-logic-and-configure-settings"></a>定义规则查询逻辑并配置设置

在“设置规则逻辑”选项卡中，可以直接在“规则查询”字段中编写查询，也可以在 Log Analytics 中创建查询，然后将创建的查询复制并粘贴到字段中。

- 采用 Kusto 查询语言 (KQL) 编写查询。 有关详细信息，请参阅 KQL [概念](/azure/data-explorer/kusto/concepts/)、[查询](/azure/data-explorer/kusto/query/)以及这篇很方便使用的[快速参考指南](/azure/data-explorer/kql-quick-reference)。

- 下面的屏幕截图中展示的示例查询 SecurityEvent 表，以显示[失败的 Windows 登录事件](/windows/security/threat-protection/auditing/event-4625)类型。

   :::image type="content" source="media/tutorial-detect-threats-custom/set-rule-logic-tab-1-new.png" alt-text="配置查询规则逻辑和设置" lightbox="media/tutorial-detect-threats-custom/set-rule-logic-tab-all-1-new.png":::

- 下面提供了另一个查询示例，此查询将在 [Azure 活动](../azure-monitor/essentials/activity-log.md)中创建的资源数异常时发出警报。

    ```kusto
    AzureActivity
    | where OperationName == "Create or Update Virtual Machine" or OperationName =="Create Deployment"
    | where ActivityStatus == "Succeeded"
    | make-series dcount(ResourceId)  default=0 on EventSubmissionTimestamp in range(ago(7d), now(), 1d) by Caller
    ```

    > [!NOTE]
    > **规则查询最佳做法**： 
    > - 查询长度应介于 1 到 10,000 个字符之间，并且不得包含“`search *`”或“`union *`”。 你可以使用[用户定义的函数](/azure/data-explorer/kusto/query/functions/user-defined-functions)来克服查询长度限制。
    >
    > - 不支持在 Log Analytics 查询窗口中使用 ADX 函数创建 Azure 数据资源管理器查询。
    >
    > - 在查询中使用 `bag_unpack` 函数时，如果使用“`project field1`”将列投影为字段而列不存在，此时查询将失败。 为了防止这种情况发生，必须按以下方式对列进行投影：
    >   - `project field1 = column_ifexists("field1","")`

### <a name="alert-enrichment"></a>警报扩充

> [!IMPORTANT]
> 警报扩充功能目前处于预览阶段。 请参阅 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)，了解适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。

- 使用“实体映射”配置部分，将查询结果中的参数映射到 Azure Sentinel 识别的实体。 实体可通过基本信息扩充规则的输出（警报和故障事件），这些信息将用作后续调查过程和补救操作的构建基块。 在“故障事件设置”选项卡中，你也可以根据这些信息将警报分组到故障事件中。

    有关详细信息，请参阅 [Azure Sentinel 中的实体](entities-in-azure-sentinel.md)。

    请参阅[在 Azure Sentinel 中将数据字段映射到实体](map-data-fields-to-entities.md)，查看完整的实体映射说明，并了解有关[向后兼容性](map-data-fields-to-entities.md#notes-on-the-new-version)的重要信息。

- 使用“自定义详细信息”配置部分，从查询中提取事件数据项，并在此规则生成的警报中展现这些项，这样你就可以从警报和故障事件中迅速了解事件内容。

    请详细了解如何在警报中展现自定义详细信息，并参阅[完整说明](surface-custom-details-in-alerts.md)。

- 使用警报详细信息配置部分，以将警报显示的详细信息定制为实际内容。 警报详细信息可以让你在警报的标题中显示攻击者的 IP 地址或帐户名称，使其显示在你的事件队列中，以便你能更详细、更清晰地了解威胁状况。

    请参阅有关[自定义警报详细信息](customize-alert-details.md)的完整说明。

### <a name="query-scheduling-and-alert-threshold"></a>查询计划和警报阈值

- 在“查询计划”部分中，设置以下参数：

   :::image type="content" source="media/tutorial-detect-threats-custom/set-rule-logic-tab-2.png" alt-text="设置查询计划和事件分组" lightbox="media/tutorial-detect-threats-custom/set-rule-logic-tab-all-2-new.png":::

    - 设置“运行查询的频率”，以控制查询的运行频率 - 可如每 5 分钟一次一样频繁，也可像每 14 天一次一样不频繁。

    - 设置“所查找数据的时段”，以确定查询所覆盖的数据的时段 - 例如，它可以查询过去 10 分钟的数据，也可以查询过去 6 小时的数据。 最大值为 14 天。

        > [!NOTE]
        > **查询间隔和回看时段**
        >
        >  这两个设置在某种程度上相互独立。 你可以采用一个短暂的间隔运行查询，来覆盖长于该间隔的时段（实际上是存在重叠的查询），但是不能采用超出该覆盖时段的间隔运行查询，否则整个查询覆盖面将存在缺口。
        >
        > **引入延迟**
        >
        > 为了考虑在源中生成事件与将事件引入 Azure Sentinel 之间可能出现的延迟，并确保实现完全覆盖且数据不会重复，Azure Sentinel 从计划的时间起延迟五分钟运行计划的分析规则。
        >
        > 有关为何需要设置此延迟以及此延迟如何解决该问题的详细技术说明，请参阅 Ron Marsiano 撰写的关于此主题的优质博文“[在 Azure Sentinel 计划的警报规则中处理引入延迟](https://techcommunity.microsoft.com/t5/azure-sentinel/handling-ingestion-delay-in-azure-sentinel-scheduled-alert-rules/ba-p/2052851)”。

- 使用“警报阈值”部分定义规则的敏感度级别。 例如，如果希望规则每次运行时只在查询返回超过 1000 个结果时生成警报，则将“生成警报的条件是查询结果数”设置为“大于”，并输入数字 1000。 这是一个必填字段，因此如果你不需要设置阈值（也就是说，你希望警报注册每一个事件），则在数字字段中输入 0。

### <a name="results-simulation"></a>结果模拟

在向导右侧的“结果模拟”区域中，选择“使用当前数据进行测试”，Azure Sentinel 将提供一个图表，其中是按照当前定义的计划查询将在最近 50 次运行过程中生成的结果（日志事件）。 如果要修改查询，请再次选择“使用当前数据进行测试”，以更新此图表。 此图表展示了定义的时段内的结果数，该时段由“查询计划”部分中的设置确定。

上面的屏幕截图中的查询的结果模拟如下所示。 左侧是默认视图，右侧是将鼠标指针悬停在图表上的某个时间点上时所显示的内容。

:::image type="content" source="media/tutorial-detect-threats-custom/results-simulation.png" alt-text="结果模拟屏幕截图":::

如果你发现查询将触发的警报过多或过于频繁，可以利用“查询计划”和“警报阈值”[部分](#query-scheduling-and-alert-threshold)中的设置进行试验，然后再次选择“使用当前数据进行测试”。

### <a name="event-grouping-and-rule-suppression"></a>事件分组和规则抑制

> [!IMPORTANT]
> 事件分组目前处于预览阶段。 请参阅 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)，了解适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。

- 在“事件分组”下，选择以下两种方式之一来处理将事件分组到警报的操作： 

    - 将所有事件分组到一个警报（默认设置）。 只要查询返回的结果数超过上面指定的警报阈值，此规则每次运行时就生成一个警报。 警报汇总了结果中返回的所有事件。 

    - 为每个事件触发警报。 此规则将针对查询返回的每一个事件生成一个独一无二的警报。 如果你希望单独显示各个事件，或者希望按特定参数（按用户、主机名或其他内容）将事件分组，此规则会很有用。 你可以在查询中定义这些参数。

        目前，规则可以生成的警报数上限为 20。 在特定规则中，如果“事件分组”设置为“为每个事件触发警报”，并且该规则的查询返回 20 个以上的事件，则前 19 个事件都会生成独一无二的警报，第 20 个警报将汇总返回的所有事件。 换句话说，第 20 个警报是将会按照“将所有事件分组到一个警报”选项生成的警报。

        如果选择此选项，Azure Sentinel 会将新字段“OriginalQuery”添加到查询结果中。 以下比较了现有查询字段和新字段：

        | 字段名称 | 包含 | 在此字段中运行查询<br>结果... |
        | - | :-: | :-: |
        | **查询** | 生成此警报实例的事件的压缩记录 | 生成此警报实例的事件 |
        | **OriginalQuery** | 在分析规则中编写的原始查询&nbsp; | 运行查询的时间范围内的最近事件，这些事件符合查询定义的参数 |
        |

        换句话说，OriginalQuery 字段的行为类似于查询字段的行为。 此额外字段的结果是，下面的[故障排除](#troubleshooting)部分中第一项描述的问题已经得到了解决。
 
    > [!NOTE]
    > 事件和警报之间有何区别？
    >
    > - 事件用于描述单次出现的操作。 例如，日志文件中的一个项可以计为一个事件。 在这个背景下，事件指的是分析规则中的查询返回的单个结果。
    >
    > - 警报是一组事件，从安全性角度而言，这些事件组合在一起后有重要意义。 如果某个事件具有重大的安全影响（例如管理员在非工作时间从国外登录），则警报可能会只包含这一个事件。
    >
    > - 那么，什么是故障事件呢？ Azure Sentinel 的内部逻辑会通过警报或一组警报生成故障事件。 SOC 分析人员围绕故障事件队列展开工作（会审、调查和修正）。
    > 
    > Azure Sentinel 从某些数据源引入原始事件，并从其他数据源引入已处理的警报。 请务必随时注意自己所处理的内容。

- 如果需要让规则在生成警报后于一个时段内（长于查询间隔）暂停运行，则可以在“抑制”部分中将“生成警报后停止运行查询”设置为“启用” 。 如果启用此设置，则必须将“停止运行查询的时长”设置为查询应停止运行的时长，最多为 24 小时。

## <a name="configure-the-incident-creation-settings"></a>配置故障事件生成设置

在“故障事件设置”选项卡中，可以选择是否将警报转化为可操作的故障事件，以及如何使用 Azure Sentinel 来执行此操作。 如果保留此选项卡不变，Azure Sentinel 将通过每个警报创建一个单独的事件。 你可以选择更改此选项卡中的设置，这样就不会创建任何故障事件，也不会将几个警报分组到一个故障事件中。

> [!IMPORTANT]
> “故障事件设置”选项卡目前处于预览阶段。 请参阅 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)，了解适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。

例如：

:::image type="content" source="media/tutorial-detect-threats-custom/incident-settings-tab.png" alt-text="定义故障事件生成和警报分组设置":::

### <a name="incident-settings"></a>事件设置

在“故障事件设置”部分中，默认情况下，将“通过此分析规则触发的警报创建故障事件”设置为“已启用”，这意味着 Azure Sentinel 将通过该规则触发的每一个警报单独创建一个故障事件。

- 如果你不需要让此规则生成任何故障事件（例如，在此规则只用于收集信息以供以后进行分析的情况下），则将此设置设为“已禁用”。

- 如果想要通过一组警报创建单个故障事件，而不是为每一个警报生成一个故障事件，请参阅下一节内容。

### <a name="alert-grouping"></a>警报分组

如果需要通过一组（最多 150 个）相似警报或重复警报（请参阅注释）生成一个故障事件，则在“警报分组”部分中则将“将此分析规则触发的相关警报分组到故障事件”设置为“已启用”，并设置以下参数。


- 将组范围限制为所选时段内创建的警报：确定将哪个时段内的相似警报或重复警报组合在一起。 此时段内对应的所有警报将共同生成一个或一组故障事件（具体取决于下面的分组设置）。 此时段之外的警报将单独生成一个或一组故障事件。

- 将此分析规则触发的警报分组到一个故障事件的依据：选择组合警报的依据：

    | 选项 | 说明 |
    | ------- | ---------- |
    | 如果所有实体都匹配，则将警报分组为单个事件 | 如果警报映射的每个实体（定义参见上面的[设置规则逻辑](#define-the-rule-query-logic-and-configure-settings)选项卡）值都相同，则将警报组合在一起。 建议使用此设置。 |
    | 将此规则触发的所有警报分组到单个事件中 | 将此规则生成的所有警报都组合在一起，即使这些警报没有相同的值也是如此。 |
    | 如果所选实体和详细信息匹配，则将警报分组到单个事件中 | 如果警报的值与各自下拉列表中选择的所有映射实体、警报详细信息和自定义详细信息完全相同，则将警报组合在一起。<br><br>例如，如果你想要基于源或目标 IP 地址创建单独的事件，或者想要对匹配特定实体和严重性的警报进行分组，则可能需要使用此设置。<br><br>注意：如果选择此选项，则必须至少为该规则选择一个实体类型或字段。 否则，规则验证将失败，并且不会创建规则。 |
    |

- 重新打开已关闭的匹配故障事件：如果某个故障事件已解决并已关闭，而稍后系统生成了应属于该故障事件的另一个警报，在这种情况下，如果要重新打开这个已关闭的故障事件，则将此设置设为“已启用”，如果要该警报创建一个新的故障事件，则将此设置保留为“已禁用”。
    
    > [!NOTE]
    > 最多可将 150 个警报分组到一个故障事件。 如果将警报分组到一个故障事件的规则生成了 150 个以上的警报，该规则将使用与初始故障事件详细信息同样的内容生成一个新故障事件，并将超出数目的警报分组到这个新的故障事件。

## <a name="set-automated-responses-and-create-the-rule"></a>设置自动响应并创建规则

1. 在“自动响应”选项卡中，可以根据此分析规则生成的一个或多个警报或基于警报创建的事件来设置自动化。
    - 对于基于警报的自动化，请从“警报自动化”下的下拉列表中选择要在生成警报时自动运行的任何 playbook。
    - 对于基于事件的自动化，请在“事件自动化（预览版）”下选择或创建自动化规则。 可以从这些自动化规则中调用 playbook（基于 **事件触发器** 的 playbook）以及自动分类、分配和关闭。
    - 有关创建 playbook 和自动化规则的详细信息和说明，请参阅[自动执行威胁响应](tutorial-respond-threats-playbook.md#automate-threat-responses)。
    - 有关警报触发器或事件触发器的适用场合的详细信息，请参阅[在 Azure Sentinel playbook 中使用触发器和操作](playbook-triggers-actions.md#azure-sentinel-triggers-summary)。 

    :::image type="content" source="media/tutorial-detect-threats-custom/automated-response-tab.png" alt-text="定义自动响应设置":::

1. 选择“查看和创建”，以查看新警报规则的所有设置。 当系统显示“验证通过”消息时，选择“创建”，将警报规则初始化。

    :::image type="content" source="media/tutorial-detect-threats-custom/review-and-create-tab.png" alt-text="查看所有设置并创建规则":::

## <a name="view-the-rule-and-its-output"></a>查看规则及其输出
  
- 可在主“分析”屏幕上的“可用规则”选项卡下的表格中找到新创建的自定义规则（类型为“已计划”）。 你可以从此列表启用、禁用或删除每个规则。

- 若要查看你创建的警报规则的结果，请转到“故障事件”页面，可以从此页面中会审、[调查故障事件](tutorial-investigate-cases.md)，并修正威胁。

- 你可以更新规则查询以排除误报。 有关详细信息，请参阅[在 Azure Sentinel 中处理误报](false-positives.md)。

> [!NOTE]
> 可通过 [Microsoft Graph 安全性](/graph/security-concept-overview)访问 Azure Sentinel 中生成的警报。 有关详细信息，请参阅 [Microsoft Graph 安全性警报文档](/graph/api/resources/security-api-overview)。

## <a name="export-the-rule-to-an-arm-template"></a>将规则导出到 ARM 模板

如果要将你的规则打包作为代码进行管理和部署，你可以轻松地[将规则导出到 Azure 资源管理器 (ARM) 模板](import-export-analytics-rules.md)。 你还可以从模板文件导入规则，以便在用户界面中进行查看和编辑。

## <a name="troubleshooting"></a>故障排除

### <a name="issue-no-events-appear-in-query-results"></a>问题：查询结果中未显示事件

如果“事件分组”设置为“为每个事件触发警报”，那么，在有些情况下，在稍后查看查询结果时（如从故障事件转回警报时），系统可能不会显示任何查询结果。 这是因为，事件与警报的联系是通过对特定事件的信息进行哈希处理，并在查询中包含哈希来完成的。 如果自警报生成后查询结果发生了更改，则哈希将不再有效，并且系统不会显示任何结果。 

若要查看事件，请从规则的查询中手动删除包含哈希的行，然后运行查询。

> [!NOTE]
> 此问题已得到解决，解决方式是在选中此事件分组选项时将新字段 OriginalQuery 添加到结果中。 请参阅上述[说明](#event-grouping-and-rule-suppression)。

### <a name="issue-a-scheduled-rule-failed-to-execute-or-appears-with-auto-disabled-added-to-the-name"></a>问题：计划的规则执行失败，或显示时其名称中添加了“自动禁用”

计划的查询规则运行失败，这种情况很少见，但也有可能发生。 根据故障的特定类型和导致故障的情况，Azure Sentinel 预先将故障分类为“暂时性”或“永久性”。

#### <a name="transient-failure"></a>暂时性故障

暂时性故障败是由于某种情况而发生的，这种情况是暂时性的，很快就会恢复正常，到时候规则执行就会成功。 Azure Sentinel 分类为暂时性故障的一些示例：

- 规则查询运行时间过长并超时。
- 数据源和 Log Analytics 之间或 Log Analytics 与 Azure Sentinel 之间的连接出现问题。
- 任何其他新故障和未知故障都被视为暂时性故障。

出现暂时性故障时，Azure Sentinel 会在预定的、不断增加的时间间隔后，继续尝试重新执行规则，一直持续到某个时间点。 在此之后，规则将只会在下一个计划的时间重新运行。 在任何情况下，规则都不会由于暂时性故障而自动禁用。

#### <a name="permanent-failure---rule-auto-disabled"></a>永久性故障 - 规则自动禁用

永久性故障是由允许规则运行的条件发生变化（如果没有人工干预，这些条件无法恢复到以前的状态）导致的。 分类为永久性故障的示例：

- 目标工作区（规则查询操作的工作区）已被删除。
- 目标表（规则查询操作的表）已被删除。
- Azure Sentinel 已从目标工作区中被删除。
- 规则查询使用的某个函数不再有效；已被修改或删除。
- 对规则查询的某个数据源的权限发生更改。
- 规则查询的某个数据源已被删除或断开连接。

如果同一个规则连续出现预定数量、同种类型的永久性故障，Azure Sentinel 将停止尝试执行该规则，还会执行以下步骤：

- 禁用规则。
- 将“自动禁用”字样添加到规则名称的开头。
- 将故障（和禁用）原因添加到规则的说明中。

通过按名称对规则列表进行排序，可以轻松确定是否存在自动禁用的规则。 自动禁用的规则将位于列表顶部或顶部附近。

SOC 管理员务必要定期检查规则列表，确定是否存在自动禁用的规则。

## <a name="next-steps"></a>后续步骤

使用分析规则检测来自 Azure Sentinel 的威胁时，请确保启用与连接的数据源关联的所有规则，以确保环境全方位安全。 启用分析规则的最有效方法是直接从数据连接器页面操作，该页面列出了所有相关规则。 有关详细信息，请参阅[连接数据源](connect-data-sources.md)。

也可通过 [API](/rest/api/securityinsights/) 和 [PowerShell](https://www.powershellgallery.com/packages/Az.SecurityInsights/0.1.0) 将规则推送到 Azure Sentinel，但这样做需要额外的工作量。 使用 API 或 PowerShell 时，必须先将规则导出到 JSON，然后才能启用规则。 在 Azure Sentinel 的多个实例（每个实例的设置都相同）中启用规则时，API 或 PowerShell 可能会有所帮助。

有关详细信息，请参阅：

有关详细信息，请参阅：

- [教程：使用 Azure Sentinel 调查事件](tutorial-investigate-cases.md)
- [在 Azure Sentinel 中使用实体对数据进行分类和分析](entities-in-azure-sentinel.md)
- [教程：在 Azure Sentinel 中结合自动化规则使用 playbook](tutorial-respond-threats-playbook.md)

此外，学习此示例：通过[自定义连接器](create-custom-connector.md)[监视 Zoom](https://techcommunity.microsoft.com/t5/azure-sentinel/monitoring-zoom-with-azure-sentinel/ba-p/1341516) 时使用自定义分析规则。
