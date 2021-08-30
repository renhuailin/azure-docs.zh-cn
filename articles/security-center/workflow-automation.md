---
title: Azure 安全中心的工作流自动化 | Microsoft Docs
description: 了解如何在 Azure 安全中心创建工作流并将其自动化
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 05/03/2021
ms.author: memildin
ms.openlocfilehash: ecedf0854d7d670cf88a8dcb729a01adaa88b646
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121750672"
---
# <a name="automate-responses-to-security-center-triggers"></a>自动执行对安全中心触发器的响应

每个安全计划都包含事件响应的多个工作流。 这些流程可能包含通知相关利益干系人、启动更改管理进程，以及应用特定的修正步骤。 安全专家建议你尽可能多地将这些流程自动化。 自动化可减少开销， 还可确保根据你预定义的要求快速、一致地执行处理步骤，从而增强安全性。

本文介绍 Azure 安全中心的工作流自动化功能。 此功能可根据安全警报、建议和监管合规性触发逻辑应用。 例如，你可能希望安全中心在出现警报时向特定用户发送电子邮件。 你还将了解如何使用 [Azure 逻辑应用](../logic-apps/logic-apps-overview.md)创建逻辑应用。


## <a name="availability"></a>可用性

|方面|详细信息|
|----|:----|
|发布状态：|正式发布版 (GA)|
|定价：|免费|
|所需角色和权限：|资源组上的安全管理员角色或所有者角色 <br>还必须具有对目标资源的写入权限<br><br>若要使用 Azure 逻辑应用工作流，还必须具有以下逻辑应用角色/权限：<br> 逻辑应用读取/触发访问需要- [逻辑应用操作员](../role-based-access-control/built-in-roles.md#logic-app-operator)权限（此角色无法创建或编辑逻辑应用，仅可运行现有应用）<br> 创建和修改逻辑应用需要- [逻辑应用参与者](../role-based-access-control/built-in-roles.md#logic-app-contributor)权限<br>如果要使用逻辑应用连接器，可能需要使用额外的凭据登录到各自的服务（例如 Outlook/Teams/Slack 实例）|
|云：|:::image type="icon" source="./media/icons/yes-icon.png"::: 商用云<br>:::image type="icon" source="./media/icons/yes-icon.png"::: 国家/地区/主权（Azure 政府、Azure 中国世纪互联）|
|||



## <a name="create-a-logic-app-and-define-when-it-should-automatically-run"></a>创建一个逻辑应用，并定义它应自动运行的时间 

1. 从安全中心的边栏选择“工作流自动化”。

    :::image type="content" source="./media/workflow-automation/list-of-workflow-automations.png" alt-text="工作流自动化列表。":::

    在此页上，你可创建新的自动化规则，还可启用、禁用或删除现有规则。

1. 若要定义新工作流，请单击“添加工作流自动化”。 

    此时会出现一个窗格，其中包含用于新的自动化的选项。 可在此处输入：
    1. 自动化的名称和说明。
    1. 将启动此自动工作流的触发器。 例如，你可能希望在生成包含“SQL”的安全警报时运行逻辑应用。

        > [!NOTE]
        > 如果触发器是包含“子建议”（例如“应修正关于 SQL 数据库的漏洞评估结果”）的建议，逻辑应用不会针对每个新的安全发现触发，仅当父建议的状态发生更改时才会触发。

    1. 满足触发条件时将运行的逻辑应用。 

        :::image type="content" source="./media/workflow-automation/add-workflow.png" alt-text="添加工作流自动化窗格。":::

1. 在“操作”部分中，单击“新建”以开始逻辑应用创建过程。

    你将转到 Azure 逻辑应用。

    [![创建新的逻辑应用。](media/workflow-automation/logic-apps-create-new.png)](media/workflow-automation/logic-apps-create-new.png#lightbox)

1. 输入名称、资源组和位置，然后单击“创建”。

1. 在新的逻辑应用中，可从安全类别中选择内置的预定义模板。 也可定义在触发此进程时要发生的自定义事件流。

    > [!TIP]
    > 有时在逻辑应用中，参数作为字符串的一部分包含在连接器中，而不是包含在自己的字段中。 有关如何提取参数的示例，请参阅[在生成 Azure 安全中心工作流自动化时使用逻辑应用参数](https://techcommunity.microsoft.com/t5/azure-security-center/working-with-logic-app-parameters-while-building-azure-security/ba-p/1342121)的步骤 #14。

    逻辑应用设计器支持以下安全中心触发器：

    - 创建或触发 Azure 安全中心建议时 - 如果逻辑应用依赖于已弃用或已替换的建议，自动化将停止工作，你需更新触发器。 若要跟踪对建议的更改，请参阅 [Azure 安全中心发行说明](release-notes.md)。

    - 创建或触发 Azure 安全中心警报时 - 你可自定义触发器，使其仅与你关注的严重性级别的警报关联。
    
    - **当创建或触发安全中心监管合规性评估时**：根据监管合规性的更新触发自动化。

    > [!NOTE]
    > 如果使用名为“触发 Azure 安全中心警报的响应时”的旧触发器，逻辑应用不会通过工作流自动化功能启动。 请改用上述的任一触发器。 

    [![示例逻辑应用。](media/workflow-automation/sample-logic-app.png)](media/workflow-automation/sample-logic-app.png#lightbox)

1. 定义逻辑应用后，回到工作流自动化定义窗格（“添加工作流自动化”）。 单击“刷新”，确保新的逻辑应用可供选择。

    ![刷新。](media/workflow-automation/refresh-the-list-of-logic-apps.png)

1. 选择逻辑应用并保存自动化。 请注意，“逻辑应用”下拉列表仅显示支持上述安全中心连接器的逻辑应用。


## <a name="manually-trigger-a-logic-app"></a>手动触发逻辑应用

查看任何安全警报或建议时，还可手动运行逻辑应用。

若要手动运行逻辑应用，请打开警报或建议，然后单击“触发逻辑应用”：

[![手动触发逻辑应用。](media/workflow-automation/manually-trigger-logic-app.png)](media/workflow-automation/manually-trigger-logic-app.png#lightbox)


## <a name="configure-workflow-automation-at-scale-using-the-supplied-policies"></a>使用提供的策略大规模配置工作流自动化

自动执行组织的监视和事件响应流程可以显著缩短调查和缓解安全事件所需的时间。

若要在整个组织中部署自动化配置，请使用提供的 Azure Policy“DeployIfNotExist”策略（如下所述）来创建和配置工作流自动化过程。

开始使用[工作流自动化模板](https://github.com/Azure/Azure-Security-Center/tree/master/Workflow%20automation)。

实施这些策略：

1. 从下表中选择要应用的策略：

    |目标  |策略  |策略 ID  |
    |---------|---------|---------|
    |安全警报的工作流自动化              |[为 Azure 安全中心警报部署工作流自动化](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2ff1525828-9a90-4fcf-be48-268cdd02361e)|f1525828-9a90-4fcf-be48-268cdd02361e|
    |安全建议的工作流自动化     |[为 Azure 安全中心建议部署工作流自动化](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f73d6ab6c-2475-4850-afd6-43795f3492ef)|73d6ab6c-2475-4850-afd6-43795f3492ef|
    |用于法规合规性的工作流自动化发生更改|[部署 Azure 安全中心合规工作流自动化](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f509122b9-ddd9-47ba-a5f1-d0dac20be63c)|509122b9-ddd9-47ba-a5f1-d0dac20be63c|
    ||||

    > [!TIP]
    > 还可通过搜索 Azure Policy 来找到这些策略：
    > 1. 打开 Azure Policy。
    > :::image type="content" source="./media/continuous-export/opening-azure-policy.png" alt-text="访问 Azure Policy。":::
    > 2. 在 Azure Policy 菜单中，选择“定义”并按名称进行搜索。 

1. 在相关的 Azure Policy 页中，选择“分配”。
    :::image type="content" source="./media/workflow-automation/export-policy-assign.png" alt-text="分配 Azure Policy。":::

1. 打开每个选项卡，并根据需要设置参数：
    1. 在“基础信息”选项卡中，设置策略的范围。 若要使用集中式管理，请将策略分配给包含将使用工作流自动化配置的订阅的管理组。 
    1. 在“参数”选项卡中，设置资源组和数据类型详细信息。 
        > [!TIP]
        > 每个参数都有一个说明可用选项的工具提示。
        >
        > 使用 Azure Policy 的“参数”选项卡 (1)，可以访问与安全中心的工作流自动化页面 (2) 类似的配置选项。
        > :::image type="content" source="./media/workflow-automation/azure-policy-next-to-workflow-automation.png" alt-text="比较工作流自动化和 Azure Policy 中的参数。" lightbox="./media/workflow-automation/azure-policy-next-to-workflow-automation.png":::

    1. （可选）若要将此分配应用于现有订阅，请打开“修正”选项卡，然后选择用于创建修正任务的选项。

1. 查看“摘要”页，并选择“创建”。


## <a name="data-types-schemas"></a>数据类型架构

若要查看传递到逻辑应用实例的安全警报或建议事件的原始事件架构，请访问[工作流自动化数据类型架构](https://aka.ms/ASCAutomationSchemas)。 如果你没有使用上述安全中心的内置逻辑应用连接器，而是使用逻辑应用的通用 HTTP 连接器，这将非常有用，而且你可根据需要使用事件 JSON 架构手动分析它。


## <a name="faq---workflow-automation"></a>常见问题解答 - 工作流自动化

### <a name="does-workflow-automation-support-any-business-continuity-or-disaster-recovery-bcdr-scenarios"></a>工作流自动化是否支持任何业务连续性或灾难恢复 (BCDR) 场景？

针对目标资源正遇到故障或其他灾难的 BCDR 场景准备环境时，组织应负责根据 Azure 事件中心、Log Analytics 工作区和逻辑应用中的指南建立备份，防止数据丢失。

对于处于活动状态的每个自动化，建议创建相同（禁用）的自动化，并将其存储在其他位置。 发生中断时，可以启用这些备份自动化并维护正常操作。

详细了解 [Azure 逻辑应用的业务连续性和灾难恢复](../logic-apps/business-continuity-disaster-recovery-guidance.md)。

## <a name="next-steps"></a>后续步骤

在本文中，你学习了如何创建逻辑应用、如何在安全中心自动执行这些应用以及如何手动运行它们。

如需查看相关材料，请参阅： 

- [关于如何使用工作流自动化来自动执行安全响应的 Microsoft Learn 模块](/learn/modules/resolve-threats-with-azure-security-center/)
- [Azure 安全中心的安全建议](security-center-recommendations.md)
- [Azure 安全中心中的安全警报](security-center-alerts-overview.md)
- [关于 Azure 逻辑应用](../logic-apps/logic-apps-overview.md)
- [适用于 Azure 逻辑应用的连接器](../connectors/apis-list.md)
- [工作流自动化数据类型架构](https://aka.ms/ASCAutomationSchemas)