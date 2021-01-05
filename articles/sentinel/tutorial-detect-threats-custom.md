---
title: 创建自定义分析规则，用 Azure Sentinel 检测威胁 |Microsoft Docs
description: 使用本教程来了解如何创建自定义分析规则以使用 Azure Sentinel 检测安全威胁。 利用事件分组和警报分组，并了解自动禁用。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/06/2020
ms.author: yelevin
ms.openlocfilehash: 5d856339632e0033e997e5c1665fab623fda9cd2
ms.sourcegitcommit: ab829133ee7f024f9364cd731e9b14edbe96b496
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/28/2020
ms.locfileid: "97795586"
---
# <a name="tutorial-create-custom-analytics-rules-to-detect-threats"></a>教程：创建自定义分析规则以检测威胁

将 [数据源连接](quickstart-onboard.md) 到 Azure Sentinel 后，你可以创建自定义规则，以便可以在你的环境中搜索特定的条件，并在符合条件时生成事件，以便你能够调查它们。 本教程将帮助你创建自定义规则，以检测 Azure Sentinel 的威胁。

本教程可帮助你检测 Azure Sentinel 的威胁。
> [!div class="checklist"]
> * 创建分析规则
> * 定义事件和警报的处理方式
> * 定义警报和事件的生成方式
> * 自动响应威胁

## <a name="create-a-custom-analytics-rule-with-a-scheduled-query"></a>使用计划的查询创建自定义分析规则

你可以创建自定义分析规则，以帮助发现你的环境中存在的威胁和异常行为。 此规则可确保你立即收到通知，以便你可以对威胁进行会审、调查和修正。

1. 在 Azure 门户的 Azure Sentinel 下，选择“分析”。

1. 在顶部菜单栏中，选择 " **+ 创建** "，并选择 " **计划的查询规则**"。 这会打开 **分析规则向导**。

    :::image type="content" source="media/tutorial-detect-threats-custom/create-scheduled-query-small.png" alt-text="创建计划查询" lightbox="media/tutorial-detect-threats-custom/create-scheduled-query-full.png":::

1. 在 " **常规** " 选项卡中，提供唯一 **名称** 和 **描述**。 在 " **策略** " 字段中，可以从分类规则所依据的各种攻击类别中进行选择。 根据需要设置警报 **严重性** 。 创建规则时，默认情况下会 **启用** 其 **状态**，这意味着它将在创建完成后立即运行。 如果你不希望它立即运行，请选择 " **禁用**"，并且该规则将添加到 " **活动规则** " 选项卡中，你可以在需要时从该选项卡中启用它。

    :::image type="content" source="media/tutorial-detect-threats-custom/general-tab.png" alt-text="开始创建自定义分析规则":::

## <a name="define-the-rule-query-logic-and-configure-settings"></a>定义规则查询逻辑并配置设置

1. 在 " **设置规则逻辑** " 选项卡中，可以直接在 **规则查询** 字段中编写查询，也可以在 Log Analytics 中创建查询，然后将其复制并粘贴到其中。 采用 Kusto 查询语言 (KQL) 编写查询。 了解有关 KQL [概念](/azure/data-explorer/kusto/concepts/) 和 [查询](/azure/data-explorer/kusto/query/)的详细信息，并查看此便捷的 [快速参考指南](/azure/data-explorer/kql-quick-reference)。

   :::image type="content" source="media/tutorial-detect-threats-custom/set-rule-logic-tab-1.png" alt-text="配置查询规则逻辑和设置" lightbox="media/tutorial-detect-threats-custom/set-rule-logic-tab-all-1.png":::

   - 在右侧的 " **结果模拟** " 区域中，选择 " **使用当前数据进行测试** "，并根据当前定义的计划，为你显示查询在最近50次生成的结果 (日志) 事件的图表。 如果修改查询，请再次选择 " **测试当前数据** " 以更新关系图。 此图显示了定义的时间段内的结果数，该值由 " **查询计划** " 部分中的设置确定。
  
      下面是上面的屏幕截图中查询的结果模拟的外观。 左侧为默认视图，当您将鼠标指针悬停在图形上的某个时间点上时，将显示其右侧。

     :::image type="content" source="media/tutorial-detect-threats-custom/results-simulation.png" alt-text="结果模拟屏幕截图":::

   - 如果发现查询触发的警报太多或过多，则可以在 " **警报阈值** " 部分中设置基线 (参见下面的 ") "。

      下面是一个示例查询，当在 Azure 活动中创建异常数量的资源时，该查询将向你发出警报。

      ```kusto
      AzureActivity
      | where OperationName == "Create or Update Virtual Machine" or OperationName =="Create Deployment"
      | where ActivityStatus == "Succeeded"
      | make-series dcount(ResourceId)  default=0 on EventSubmissionTimestamp in range(ago(7d), now(), 1d) by Caller
      ```

        > [!NOTE]
        > - 查询长度应介于1到10000个字符之间，并且不能包含 "search \* " 或 "union \* "。
        >
        > - **不支持** 使用 ADX 函数在 Log Analytics 查询窗口中创建 Azure 数据资源管理器查询。

1. 使用 " **地图实体** " 部分可将参数从查询结果链接到 Azure Sentinel 识别的实体。 这些实体构成进一步分析的基础，包括 " **事件设置** " 选项卡中的警报分组到事件。 

    详细了解 Azure Sentinel 中的 [实体](identify-threats-with-entity-behavior-analytics.md#entities-in-azure-sentinel) 。
  
1. 在 " **查询计划** " 部分中，设置以下参数：

    :::image type="content" source="media/tutorial-detect-threats-custom/set-rule-logic-tab-2.png" alt-text="设置查询计划和事件分组" lightbox="media/tutorial-detect-threats-custom/set-rule-logic-tab-all-2.png":::

    1. 设置 " **运行查询** "，以控制运行查询的频率（频率为每5分钟一次，或每天不经常出现一次）。

    1. 设置 **最后的查找数据** ，以确定查询所覆盖的数据的时间段（例如，它可以查询过去10分钟的数据或过去6小时的数据）。

        > [!NOTE]
        > **查询间隔和 lookback 期间**
        > - 这两个设置彼此独立，最多是一个点。 您可以在一小段时间间隔内运行查询，该时间段内覆盖的时间段比)  (的时间段长，但您不能以超过覆盖期的时间间隔运行查询，否则，整个查询覆盖范围内会出现空白。
        >
        > - 可以设置最多14天的 lookback 期限。
        >
        > **引入延迟**
        > - 若要考虑在源中的生成事件与将其引入到 Azure Sentinel 之间可能发生的 **延迟** ，并确保在没有重复数据的情况下完整覆盖，Azure Sentinel 将按计划的时间从 **五分钟的延迟** 运行计划分析规则。

1. 使用 " **警报阈值** " 部分可定义基线。 例如，如果希望仅在查询每次运行时返回超过1000个结果时才生成警报，请在 "**查询结果数****大于**" 时设置 "生成警报"，然后输入数字1000。 这是必填字段，因此，如果你不想设置基线，即，如果你希望警报注册每个事件–请在 "数字" 字段中输入0。
    
1. 在 " **事件分组**" 下，选择以下两种方式之一来处理将 **事件** 分组为 **警报**： 

    - 将 **所有事件分组为单个警报** (默认设置) 。 规则每次运行时都将生成一个警报，只要查询返回的结果比上面指定的 **警报阈值** 更多。 警报包括结果中返回的所有事件的摘要。 

    - 为每个事件触发警报。 规则为查询返回的每个事件生成一个唯一的警报。 如果希望单独显示事件，或者要按特定参数（按用户、主机名或其他内容）对事件进行分组，这会很有用。 您可以在查询中定义这些参数。
    
    目前，规则可以生成的警报数上限为20。 如果在某一特定规则中， **事件分组** 设置为为 **每个事件触发一个警报**，并且该规则的查询返回20个以上的事件，其中每个事件将生成一个唯一的警报，第20个事件将汇总返回的整个事件集。 换句话说，第20个警报是在 "将 **所有事件分组为单个警报** " 选项下生成的警报。

    > [!NOTE]
    > **事件** 和 **警报** 之间有何区别？
    >
    > - **事件** 是单个匹配项的说明。 例如，日志文件中的单个项可以作为事件进行计数。 在此上下文中，事件指分析规则中查询返回的单个结果。
    >
    > - **警报** 是从安全角度来看的事件的集合。 例如，如果事件有重大的安全影响，则警报可能包含单个事件，例如，来自办公时间之外的外国国家/地区的管理登录。
       >
    > - 那么，什么是 **事件**？ Azure Sentinel 的内部逻辑根据 **警报** 或警报组创建 **事件**。 事件队列是分析师的工作-分类、调查和修正的关键点。
    > 
    > Azure Sentinel 引入来自某些数据源的原始事件，并已处理来自其他数据源的警报。 需要注意的是，你可以随时处理哪一个。

    > [!IMPORTANT]
    > 事件分组目前为公共预览版。 此功能在提供时没有服务级别协议，不建议用于生产工作负荷。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
    
1. 在 " **禁止** 显示" 部分中，如果收到警报后，你可以在 " **生成警报后停止正在运行的查询** " 设置，你 **希望在超过** 查询间隔的时间段内挂起此规则的操作。 如果启用此设置，则必须将 **停止运行查询的** 时间设置为查询停止运行的时间，最长为24小时。

## <a name="configure-the-incident-creation-settings"></a>配置事件创建设置

在 " **事件设置** " 选项卡中，可以选择 Azure Sentinel 是否会将警报转换为可操作事件。 如果此选项卡为单独的选项卡，则 Azure Sentinel 将从每个警报和每个警报创建单个独立的事件。 你可以选择不创建事件，也可以通过更改此选项卡中的设置，将多个警报分组为一个事件。

> [!IMPORTANT]
> "事件设置" 选项卡目前为公共预览版。 此功能在提供时没有服务级别协议，不建议用于生产工作负荷。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

:::image type="content" source="media/tutorial-detect-threats-custom/incident-settings-tab.png" alt-text="定义事件创建和警报分组设置":::

1. 在 " **事件设置** " 部分中，默认情况下，将 **此分析规则触发的警报** 中的 "创建事件" 设置为 " **已启用**"，这意味着 Azure Sentinel 将从该规则触发的每个警报创建单个独立的事件。
       - 如果你不希望此规则导致创建任何事件 (例如，如果此规则只是为了收集用于后续分析) 的信息，请将此项设置为 " **已禁用**"。

1. 在 " **警报分组** " 部分中，如果想要从一组最多为150的类似或定期警报生成单个事件 (参阅 note) ，将 **组相关的警报（由此分析规则触发）触发** 到要 **启用** 的事件，并设置以下参数。

    - 将 **组限制为在所选时间范围内创建的警报**：确定将相似或重复警报分组到一起的时间范围。 此时间范围内的所有相应警报将共同生成事件或一组事件 (具体取决于下面) 的分组设置。 此时间范围外的警报将生成一个单独的事件或一组事件。

    - 通过以下 **方式将此分析规则触发的警报分组到单个事件中**：选择将警报组合在一起的基础：

        - **如果所有实体都匹配，则将警报分组到单个事件中**：如果在) 上的 "设置规则逻辑" 选项卡中定义的每个映射 (实体共享相同的值，则将这些警报组合在一起。 建议使用此设置。

        - **将此规则触发的所有警报分组为一个事件**：此规则生成的所有警报都将组合在一起，即使它们不共享相同的值。

        - **如果所选实体匹配，则将警报分组到单个事件中**：如果将警报与可从下拉列表中选择) 的某些映射 (实体共享相同的值，则将这些警报组合在一起。 例如，你可能想要根据源或目标 IP 地址创建单独的事件时使用此设置。

    - **重新打开已关闭的匹配事件**：如果事件已解决并已关闭，并且随后生成的警报应属于该事件，则将此设置设置为 " **启用** " （如果你想要重新打开已关闭的事件），并将此设置保留为 "已 **禁用** " （如果你希望警报创建新事件）。
    
        > [!NOTE]
        > 最多可将150个警报分组为一个事件。 如果一个规则生成的警报超过150个，并将其分组为单个事件，则会生成一个新事件，其中包含与原始事件相同的事件详细信息，并且多余的警报将被分组到新事件中。

## <a name="set-automated-responses-and-create-the-rule"></a>设置自动响应并创建规则

1. 在 " **自动响应** " 选项卡中，选择自定义规则生成警报时要自动运行的任何行动手册。 有关创建和自动执行行动手册的详细信息，请参阅 [应对威胁](tutorial-respond-threats-playbook.md)。

    :::image type="content" source="media/tutorial-detect-threats-custom/automated-response-tab.png" alt-text="定义自动响应设置":::

1. 选择 " **查看" 和 "创建** " 以查看新警报规则的所有设置，然后选择 "创建" **以初始化警报规则**。

    :::image type="content" source="media/tutorial-detect-threats-custom/review-and-create-tab.png" alt-text="检查所有设置并创建规则":::

## <a name="view-the-rule-and-its-output"></a>查看规则及其输出
  
1. 创建警报后，会将自定义规则添加到 " **活动规则**" 下的表中。 从此列表中，你可以启用、禁用或删除每个规则。

1. 若要查看所创建的警报规则的结果，请在 " **事件** " 页上，你可以在其中进行会审、 [调查事件](tutorial-investigate-cases.md)，并修正威胁。

> [!NOTE]
> Azure Sentinel 中生成的警报通过 [Microsoft Graph 安全机制](/graph/security-concept-overview)提供。 有关详细信息，请参阅 [Microsoft Graph 安全警报文档](/graph/api/resources/security-api-overview)。

## <a name="troubleshooting"></a>疑难解答

### <a name="issue-no-events-appear-in-query-results"></a>问题：查询结果中未显示任何事件

如果 **事件分组** 设置为为 **每个事件触发一个警报**，则在某些情况下，当稍后查看查询结果时 (例如，当透视回) 事件的警报时，可能不会显示任何查询结果。 这是因为，事件通过哈希处理特定事件的信息，并在查询中包含哈希来实现的。 如果自生成警报以来，查询结果发生了更改，则哈希将不再有效，且不会显示任何结果。 

若要查看这些事件，请从规则查询的哈希中手动删除该行，并运行查询。

### <a name="issue-a-scheduled-rule-failed-to-execute-or-appears-with-auto-disabled-added-to-the-name"></a>问题：计划的规则无法执行，或显示已将自动禁用添加到名称中

计划的查询规则无法运行，但可能会发生这种情况。 Azure Sentinel 根据故障的具体类型和发生故障的情况，将故障提前设置为暂时性或永久性。

#### <a name="transient-failure"></a>暂时性故障

暂时性故障发生的原因是临时的，并且不久会恢复正常，此时规则执行将会成功。 Azure Sentinel 归类为暂时性故障的一些示例如下：

- 规则查询运行时间太长，超时。
- 数据源和 Log Analytics 之间，或 Log Analytics 与 Azure Sentinel 之间存在连接问题。
- 任何其他新的和未知的故障都被视为暂时性的。

如果发生暂时性故障，Azure Sentinel 会继续尝试在预先确定的时间间隔之后再次执行规则，直到达到某个点。 之后，规则将仅在下一次计划的时间运行。 由于暂时性故障，规则永远不会自动禁用。

#### <a name="permanent-failure---rule-auto-disabled"></a>永久失败-规则自动禁用

发生永久性故障是由于允许规则运行的条件发生了变化，无需人工干预的情况下，将不会返回其以前的状态。 下面是分类为永久性的故障的一些示例：

- 删除了规则查询操作)  (的目标工作区。
- 删除了规则查询操作)  (的目标表。
- Azure Sentinel 已从目标工作区中删除。
- 规则查询使用的函数不再有效;已对其进行修改或删除。
- 更改了规则查询的数据源之一的权限。
- 已删除或断开规则查询的数据源之一。

如果 **预先确定的连续永久失败次数与相同的类型和相同的规则，** Azure Sentinel 停止尝试执行规则，同时执行以下步骤：

- 禁用规则。
- 将 **"自动禁用"** 字样添加到规则名称的开头。
- 将失败 (和禁用) 的原因添加到规则的说明。

通过按名称对规则列表进行排序，可以轻松地确定是否存在自动禁用的规则。 自动禁用的规则将在列表顶部或附近。

SOC 管理器应确保定期检查规则列表，以确定是否存在自动禁用的规则。

## <a name="next-steps"></a>后续步骤

在本教程中，已学习如何使用 Azure Sentinel 来检测威胁。

若要了解如何自动响应威胁，请 [在 Azure Sentinel 中设置自动威胁响应](tutorial-respond-threats-playbook.md)。
