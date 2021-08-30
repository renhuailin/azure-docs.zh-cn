---
title: 连续导出可以将 Azure 安全中心的警报和建议发送到 Log Analytics 工作区或 Azure 事件中心
description: 了解如何配置到 Log Analytics 工作区或 Azure 事件中心的安全警报和建议的连续导出
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 07/07/2021
ms.author: memildin
ms.openlocfilehash: 1d8feb49be378abed2a63030c6329e9e8a13d48a
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121750726"
---
# <a name="continuously-export-security-center-data"></a>连续导出安全中心数据

Azure 安全中心会生成详细的安全警报和建议。 可以通过门户或编程工具查看它们。 你可能还需要部分或全部导出此信息，以使用环境中的其他监视工具进行跟踪。 

“连续导出”使你可以完全自定义将要导出的内容，以及要导出到的位置 。 例如，可以对其进行配置，以便：

- 将所有高严重性警报发送到 Azure 事件中心
- 将 SQL 服务器的漏洞评估扫描中的所有中等或较高严重性结果发送到特定的 Log Analytics 工作区
- 将生成的特定建议即时传递到事件中心或 Log Analytics 工作区 
- 每当控件的分数变化 0.01 或更大时，订阅的安全分数就会发送到 Log Analytics 工作区 

即使此功能称为“连续”，也可以选择每周导出一次安全分数或合规性数据的快照。

本文介绍如何配置到 Log Analytics 工作区或 Azure 事件中心的连续导出。

> [!NOTE]
> 如果需要将安全中心与 SIEM 集成，请参阅[将警报流式传输到 SIEM、SOAR 或 IT 服务管理解决方案](export-to-siem.md)。

> [!TIP]
> 安全中心还提供了用于执行一次性手动导出到 CSV 的选项。 在[手动一次性导出警报和建议](#manual-one-time-export-of-alerts-and-recommendations)中了解详细信息。


## <a name="availability"></a>可用性

|方面|详细信息|
|----|:----|
|发布状态：|正式发布版 (GA)|
|定价：|免费|
|所需角色和权限：|<ul><li>资源组的安全管理员或所有者 </li><li>对目标资源的写入权限。</li><li>如果使用的是下面所述的 Azure Policy“DeployIfNotExist”策略，则还需要分配策略的权限</li><li>若要将数据导出到事件中心，需要对事件中心策略具有写入权限。</li><li>导出到 Log Analytics 工作区：<ul><li>如果具有 SecurityCenterFree 解决方案，则至少需要工作区解决方案读取权限：`Microsoft.OperationsManagement/solutions/read`</li><li>如果没有 SecurityCenterFree 解决方案，则需要工作区解决方案写入权限：`Microsoft.OperationsManagement/solutions/action`</li><li>详细了解 [Azure Monitor 和 Log Analytics 工作区解决方案](../azure-monitor/insights/solutions.md)</li></ul></li></ul>|
|云：|:::image type="icon" source="./media/icons/yes-icon.png"::: 商用云<br>:::image type="icon" source="./media/icons/yes-icon.png":::国家/地区/主权（Azure 政府、Azure 中国世纪互联）| 
|||


## <a name="what-data-types-can-be-exported"></a>可以导出哪些数据类型？

连续导出可以在以下数据类型发生更改时导出它们：

- 安全警报。
- 安全建议。
- 安全检测结果。 这些检测结果可将视为“子”建议，属于“父”建议。 例如：
    - “应在计算机上安装系统更新”建议包含有关各未完成的系统更新的“子级”建议。
    - “应修复虚拟机中的漏洞”建议包含有关漏洞扫描程序所识别漏洞的“子级”建议。
    > [!NOTE]
    > 如果使用 REST API 配置连续导出，请始终在结果中加入父级。 
- 按订阅或按控制安全评分。
- 合规性数据。


## <a name="set-up-a-continuous-export"></a>设置连续导出 

可以通过安全中心 REST API 在 Azure 门户的“安全中心”页中配置连续导出，或使用提供的 Azure Policy 模板进行大规模配置。 选择下面相应的选项卡，以获取每项的详细信息。

### <a name="use-the-azure-portal"></a>[**使用 Azure 门户**](#tab/azure-portal)

### <a name="configure-continuous-export-from-the-security-center-pages-in-azure-portal"></a>在 Azure 门户的“安全中心”页中配置连续导出

无论是设置连续导出到 Log Analytics 工作区的操作还是连续导出到 Azure 事件中心的操作，都需要执行以下步骤。

1. 从安全中心的侧栏中，选择“定价和设置”。

1. 选择要为其配置数据导出的特定订阅。

1. 从该订阅的设置页的侧栏中，选择“连续导出”。

    :::image type="content" source="./media/continuous-export/continuous-export-options-page.png" alt-text="Azure 安全中心内的导出选项。":::

    可以在这里看到导出选项。 每个可用的导出目标有一个选项卡。 

1. 选择要导出的数据类型，并从每种类型的筛选器中进行选择（例如，仅导出严重程度高的警报）。

1. 选择适当的导出频率：
    - 流式处理 - 更新资源的运行状况状态时，将发送评估（如果没有更新，则不发送任何数据）。
    - 快照 - 每周发送一次每个订阅的所有法规合规性评估的当前状态快照。 这是一个预览功能，用于提供安全评分和法规合规性数据的每周快照。 若要识别快照数据，请查看字段 ``IsSnapshot``。

1. （可选）如果你的选择包含这些建议中的一个，可以将漏洞评估结果与它们包括在一起：
    - SQL 数据库应已解决漏洞结果
    - 计算机上的 SQL 服务器应已解决漏洞结果
    - 应修正 Azure 容器注册表映像中的漏洞（由 Qualys 提供技术支持）
    - 应修正虚拟机中的漏洞
    - 应在计算机上安装系统更新

    若要将结果与这些建议包括在一起，请启用“包括安全结果”选项。

    :::image type="content" source="./media/continuous-export/include-security-findings-toggle.png" alt-text="在连续导出配置中包含安全检测结果切换开关。" :::

1. 从“导出目标”区域中，选择要将数据保存到其中的位置。 数据可以保存在不同订阅的目标中（例如，保存在中央事件中心实例或中央 Log Analytics 工作区中）。
1. 选择“保存”。

### <a name="use-the-rest-api"></a>[**使用 REST API**](#tab/rest-api)

### <a name="configure-continuous-export-using-the-rest-api"></a>使用 REST API 配置连续导出

可以通过 Azure 安全中心[自动化 API](/rest/api/securitycenter/automations) 来配置和管理连续导出。 使用此 API 创建或更新规则，以便将内容导出到以下任何可能的目标：

- Azure 事件中心
- Log Analytics 工作区
- Azure 逻辑应用 

API 提供了 Azure 门户中没有的其他功能，例如：

* **更大的卷** - API 允许你在单个订阅上创建多个导出配置。 安全中心门户 UI 中的“连续导出”页仅支持每个订阅一个导出配置。

* **其他功能** - API 提供了 UI 中未显示的其他参数。 例如，你可以将标记添加到自动化资源，并根据一组更广泛的警报和建议属性（与安全中心的门户 UI 的“连续导出”页中提供的属性相比）来定义导出。

* **更明确的作用域** - API 为导出配置的作用域提供更精细的级别。 使用 API 定义导出时，可以在资源组级别执行此操作。 如果使用安全中心门户 UI 中的“连续导出”页面，则必须在订阅级别定义它。

    > [!TIP]
    > 如果使用 API 设置了多个导出配置，或者使用了仅限 API 的参数，则不会将这些额外功能显示在安全中心 UI 中。 而是会出现一个横幅，通知你存在其他配置。

在 [REST API 文档](/rest/api/securitycenter/automations)中详细了解自动化 API。

### <a name="deploy-at-scale-with-azure-policy"></a>[**使用 Azure Policy 大规模部署**](#tab/azure-policy)

### <a name="configure-continuous-export-at-scale-using-the-supplied-policies"></a>使用提供的策略大规模配置连续导出

自动执行组织的监视和事件响应流程可以显著缩短调查和缓解安全事件所需的时间。

若要在组织中部署连续导出配置，请使用提供的 Azure Policy“DeployIfNotExist”策略（如下所述）来创建和配置连续导出过程。

**若要实施这些策略**

1. 从下表中选择要应用的策略：

    |目标  |策略  |策略 ID  |
    |---------|---------|---------|
    |将内容连续导出到事件中心|[为 Azure 安全中心警报和建议部署“导出到事件中心”配置](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fcdfcce10-4578-4ecd-9703-530938e4abcb)|cdfcce10-4578-4ecd-9703-530938e4abcb|
    |将内容连续导出到 Log Analytics 工作区|[为 Azure 安全中心警报和建议配置“导出到 Log Analytics 工作区”配置](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fffb6f416-7bd2-4488-8828-56585fef2be9)|ffb6f416-7bd2-4488-8828-56585fef2be9|
    ||||

    > [!TIP]
    > 还可通过搜索 Azure Policy 来找到这些策略：
    > 1. 打开 Azure Policy。
    > :::image type="content" source="./media/continuous-export/opening-azure-policy.png" alt-text="访问 Azure Policy。":::
    > 2. 在 Azure Policy 菜单中，选择“定义”并按名称进行搜索。 

1. 在相关的 Azure Policy 页中，选择“分配”。
    :::image type="content" source="./media/continuous-export/export-policy-assign.png" alt-text="分配 Azure Policy。":::

1. 打开每个选项卡，并根据需要设置参数：
    1. 在“基础信息”选项卡中，设置策略的范围。 若要使用集中式管理，请将策略分配给包含将使用连续导出配置的订阅的管理组。 
    1. 在“参数”选项卡中，设置资源组和数据类型详细信息。 
        > [!TIP]
        > 每个参数都有一个说明可用选项的工具提示。
        >
        > 使用 Azure Policy 的“参数”选项卡 (1) 可以访问与安全中心的“连续导出”页面 (2) 相同的配置选项。
        > :::image type="content" source="./media/continuous-export/azure-policy-next-to-continuous-export.png" alt-text="比较连续导出和 Azure Policy 中的参数。" lightbox="./media/continuous-export/azure-policy-next-to-continuous-export.png":::
    1. （可选）若要将此分配应用于现有订阅，请打开“修正”选项卡，然后选择用于创建修正任务的选项。
1. 查看“摘要”页，并选择“创建”。

--- 

## <a name="information-about-exporting-to-a-log-analytics-workspace"></a>有关导出到 Log Analytics 工作区的信息

如果要分析 Log Analytics 工作区中的 Azure 安全中心数据，或将 Azure 警报与安全中心警报一起使用，请设置连续导出到 Log Analytics 工作区。

### <a name="log-analytics-tables-and-schemas"></a>Log Analytics 表和架构

安全警报和建议将分别存储在 SecurityAlert 和 SecurityRecommendation 表中 。 

包含这些表的 Log Analytics 解决方案的名称取决于是否启用了 Azure Defender：Security（“安全和审核”）或 SecurityCenterFree。 

> [!TIP]
> 若要查看目标工作区中的数据，必须启用解决方案“安全和审核”或“SecurityCenterFree”中的一个 。

![Log Analytics 中的 *SecurityAlert* 表。](./media/continuous-export/log-analytics-securityalert-solution.png)

若要查看导出的数据类型的事件架构，请访问 [Log Analytics 表架构](https://aka.ms/ASCAutomationSchemas)。


##  <a name="view-exported-alerts-and-recommendations-in-azure-monitor"></a>在 Azure Monitor 中查看导出的警报和建议

还可以选择在 [Azure Monitor](../azure-monitor/alerts/alerts-overview.md) 中查看导出的安全警报和/或建议。 

Azure Monitor 为各种 Azure 警报（包括诊断日志、指标警报以及基于 Log Analytics 工作区查询的自定义警报）提供统一的警报体验。

若要在 Azure Monitor 中查看来自安全中心的警报和建议，请根据 Log Analytics 查询（日志警报）配置警报规则：

1. 从 Azure Monitor 的“警报”页上，选择“新建警报规则” 。

    ![Azure Monitor 的“警报”页。](./media/continuous-export/azure-monitor-alerts.png)

1. 在“创建规则”页中，配置新规则（与[在 Azure Monitor 中配置日志警报规则](../azure-monitor/alerts/alerts-unified-log.md)的方式相同）：

    * 对于“资源”，请选择要向其中导出安全警报和建议的 Log Analytics 工作区。

    * 对于“条件”，请选择“自定义日志搜索” 。 在出现的页中，配置查询、回溯周期和频率周期。 在搜索查询中，可以键入 SecurityAlert 或 SecurityRecommendation 以查询在启用“连续导出到 Log Analytics”功能时安全中心持续导出的数据类型 。 
    
    * （可选）配置要触发的[操作组](../azure-monitor/alerts/action-groups.md)。 操作组可以触发电子邮件发送、ITSM 票证、Webhook 等。
    ![Azure Monitor 警报规则。](./media/continuous-export/azure-monitor-alert-rule.png)

现将在 Azure Monitor 警报中看到新的 Azure 安全中心警报或建议（取决于所配置的连续导出规则和在 Azure Monitor 警报规则中定义的条件），并自动触发操作组（如果已提供）。

## <a name="manual-one-time-export-of-alerts-and-recommendations"></a>手动一次性导出警报和建议

若要下载警报或建议的 CSV 报表，请打开“安全警报”或“建议”页，然后选择“下载 CSV 报表”按钮。

:::image type="content" source="./media/continuous-export/download-alerts-csv.png" alt-text="将警报数据下载为 CSV 文件。" lightbox="./media/continuous-export/download-alerts-csv.png":::

> [!NOTE]
> 这些报表包含当前所选订阅中的资源的警报和建议。


## <a name="faq---continuous-export"></a>常见问题解答 - 连续导出

### <a name="what-are-the-costs-involved-in-exporting-data"></a>导出数据时涉及哪些费用？

启用连续导出不会产生费用。 在 Log Analytics 工作区中引入和保留数据可能会产生费用，具体取决于你的配置。 

详细了解 [Log Analytics 工作区定价](https://azure.microsoft.com/pricing/details/monitor/)。

详细了解 [Azure 事件中心定价](https://azure.microsoft.com/pricing/details/event-hubs/)。


### <a name="does-the-export-include-data-about-the-current-state-of-all-resources"></a>导出是否包含所有资源的当前状态数据？

错误。 连续导出用于流式传输事件：

- 不会导出在启用导出之前收到的警报。
- 当资源的合规性状态发生更改时就会发送建议。 例如，当某个资源的状态从正常变为不正常时。 因此，与警报一样，将不会导出针对自启用导出以来未更改状态的资源的建议。
- 每个安全控制或订阅的安全功能分数在一个安全控制的分数变化 0.01 或更大时发送。 
- 合规性状态在资源的合规性状态更改时发送。



### <a name="why-are-recommendations-sent-at-different-intervals"></a>为什么建议以不同的时间间隔发送？

不同的建议有不同的合规性评估时间间隔，从几分钟到几天不等。 因此，建议出现在导出中所需的时间会有所不同。

### <a name="does-continuous-export-support-any-business-continuity-or-disaster-recovery-bcdr-scenarios"></a>连续导出是否支持所有业务连续性或灾难恢复 (BCDR) 场景？

针对目标资源正遇到故障或其他灾难的 BCDR 场景准备环境时，组织应负责根据 Azure 事件中心、Log Analytics 工作区和逻辑应用中的指南建立备份，防止数据丢失。

有关详细信息，请参阅 [Azure 事件中心 - 异地灾难恢复](../event-hubs/event-hubs-geo-dr.md)。


### <a name="is-continuous-export-available-with-azure-security-center-free"></a>Azure 安全中心是否可以免费使用连续导出？

是！ 请注意，许多安全中心警报只有在启用了 Azure Defender 之后才会提供。 预览将在导出数据中获取的警报的一种好方法是，查看 Azure 门户的“安全中心”页中显示的警报。



## <a name="next-steps"></a>后续步骤

本文介绍了如何配置建议和警报的连续导出。 另外还介绍了如何将警报数据下载为 CSV 文件。 

如需相关资料，请参阅以下文档： 

- 详细了解[工作流自动化模板](https://github.com/Azure/Azure-Security-Center/tree/master/Workflow%20automation)。
- [Azure 事件中心文档](../event-hubs/index.yml)
- [Azure Sentinel 文档](../sentinel/index.yml)
- [Azure Monitor 文档](../azure-monitor/index.yml)
- [导出数据类型架构](https://aka.ms/ASCAutomationSchemas)