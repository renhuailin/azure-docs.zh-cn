---
title: Azure 安全中心的工作流自动化 | Microsoft Docs
description: 了解如何在 Azure 安全中心创建工作流并将其自动化
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 10/27/2020
ms.author: memildin
ms.openlocfilehash: a7341362183aee4a23556a164677bc320babdfec
ms.sourcegitcommit: 693df7d78dfd5393a28bf1508e3e7487e2132293
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/28/2020
ms.locfileid: "92900828"
---
# <a name="automate-responses-to-security-center-triggers"></a>自动响应安全中心触发器

每个安全计划都包含事件响应的多个工作流。 这些流程可能包含通知相关利益干系人、启动更改管理进程，以及应用特定的修正步骤。 安全专家建议你尽可能多地将这些流程自动化。 自动化可减少开销， 还可确保根据你预定义的要求快速、一致地执行处理步骤，从而增强安全性。

本文介绍 Azure 安全中心的工作流自动化功能。 此功能可根据安全警报和建议触发逻辑应用。 例如，你可能希望安全中心在出现警报时向特定用户发送电子邮件。 你还将了解如何使用 [Azure 逻辑应用](../logic-apps/logic-apps-overview.md)创建逻辑应用。


## <a name="availability"></a>可用性

|方面|详细信息|
|----|:----|
|发布状态：|正式发布 (GA)|
|定价：|免费|
|所需角色和权限：|资源组上的安全管理员角色或所有者角色 <br>还必须具有对目标资源的写入权限<br><br>若要使用 Azure 逻辑应用工作流，还必须具有以下逻辑应用角色/权限：<br> 逻辑应用读取/触发访问需要- [逻辑应用操作员](../role-based-access-control/built-in-roles.md#logic-app-operator)权限（此角色无法创建或编辑逻辑应用，仅可运行现有应用）<br> 创建和修改逻辑应用需要- [逻辑应用参与者](../role-based-access-control/built-in-roles.md#logic-app-contributor)权限<br>如果要使用逻辑应用连接器，可能需要使用额外的凭据登录到各自的服务（例如 Outlook/Teams/Slack 实例）|
|云：|![是](./media/icons/yes-icon.png) 商业云<br>![是](./media/icons/yes-icon.png) 国家/主权（US Gov、中国 Gov、其他 Gov）|
|||



## <a name="create-a-logic-app-and-define-when-it-should-automatically-run"></a>创建一个逻辑应用，并定义它应自动运行的时间 

1. 从安全中心的边栏选择“工作流自动化”。

    :::image type="content" source="./media/workflow-automation/list-of-workflow-automations.png" alt-text="工作流自动化列表&quot;:::

    在此页上，你可创建新的自动化规则，还可启用、禁用或删除现有规则。

1. 若要定义新工作流，请单击“添加工作流自动化”。 

    此时会出现一个窗格，其中包含用于新的自动化的选项。 可在此处输入：
    1. 自动化的名称和说明。
    1. 将启动此自动工作流的触发器。 例如，你可能希望在生成包含“SQL”的安全警报时运行逻辑应用。

        > [!NOTE]
        > 如果触发器是包含 &quot;子建议" 的建议，例如 **应修正 SQL 数据库上的漏洞评估结果** ，则逻辑应用对于每个新的安全查找都不会触发;仅当父建议的状态发生更改时。

    1. 满足触发条件时将运行的逻辑应用。 

        :::image type="content" source="./media/workflow-automation/add-workflow.png" alt-text="工作流自动化列表&quot;:::

    在此页上，你可创建新的自动化规则，还可启用、禁用或删除现有规则。

1. 若要定义新工作流，请单击“添加工作流自动化”。 

    此时会出现一个窗格，其中包含用于新的自动化的选项。 可在此处输入：
    1. 自动化的名称和说明。
    1. 将启动此自动工作流的触发器。 例如，你可能希望在生成包含“SQL”的安全警报时运行逻辑应用。

        > [!NOTE]
        > 如果触发器是包含 &quot;子建议" ) 。 单击“刷新”，确保新的逻辑应用可供选择。

    ![刷新](media/workflow-automation/refresh-the-list-of-logic-apps.png)

1. 选择逻辑应用并保存自动化。 请注意，“逻辑应用”下拉列表仅显示支持上述安全中心连接器的逻辑应用。


## <a name="manually-trigger-a-logic-app"></a>手动触发逻辑应用

查看任何安全警报或建议时，还可手动运行逻辑应用。

若要手动运行逻辑应用，请打开警报或建议，然后单击“触发逻辑应用”：

[![手动触发逻辑应用](media/workflow-automation/manually-trigger-logic-app.png)](media/workflow-automation/manually-trigger-logic-app.png#lightbox)


## <a name="configure-workflow-automation-at-scale-using-the-supplied-policies"></a>使用提供的策略大规模配置工作流自动化

自动执行组织的监视和事件响应流程可以显著缩短调查和缓解安全事件所需的时间。

若要在组织中部署自动化配置，请使用下面所述的 Azure 策略 "DeployIfNotExist" 策略来创建和配置工作流自动化过程。

开始使用[工作流自动化模板](https://github.com/Azure/Azure-Security-Center/tree/master/Workflow%20automation)。

若要实施这些策略：

1. 从下表中选择要应用的策略：

    |目标  |策略  |策略 ID  |
    |---------|---------|---------|
    |安全警报的工作流自动化|[为 Azure 安全中心警报部署工作流自动化](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2ff1525828-9a90-4fcf-be48-268cdd02361e)|f1525828-9a90-4fcf-be48-268cdd02361e|
    |安全建议的工作流自动化|[为 Azure 安全中心建议部署工作流自动化](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f73d6ab6c-2475-4850-afd6-43795f3492ef)|73d6ab6c-2475-4850-afd6-43795f3492ef|
    ||||

    > [!TIP]
    > 还可以通过搜索 Azure 策略来查找这些内容：
    > 1. 打开 Azure 策略。
    > :::image type="content" source="./media/continuous-export/opening-azure-policy.png" alt-text="工作流自动化列表&quot;:::

    在此页上，你可创建新的自动化规则，还可启用、禁用或删除现有规则。

1. 若要定义新工作流，请单击“添加工作流自动化”。 

    此时会出现一个窗格，其中包含用于新的自动化的选项。 可在此处输入：
    1. 自动化的名称和说明。
    1. 将启动此自动工作流的触发器。 例如，你可能希望在生成包含“SQL”的安全警报时运行逻辑应用。

        > [!NOTE]
        > 如果触发器是包含 &quot;子建议" **分配** "。
    :::image type="content" source="./media/workflow-automation/export-policy-assign.png" alt-text="工作流自动化列表&quot;:::

    在此页上，你可创建新的自动化规则，还可启用、禁用或删除现有规则。

1. 若要定义新工作流，请单击“添加工作流自动化”。 

    此时会出现一个窗格，其中包含用于新的自动化的选项。 可在此处输入：
    1. 自动化的名称和说明。
    1. 将启动此自动工作流的触发器。 例如，你可能希望在生成包含“SQL”的安全警报时运行逻辑应用。

        > [!NOTE]
        > 如果触发器是包含 &quot;子建议" 选项卡 (1) 提供与安全中心的工作流自动化页面 (2) 相同的配置选项。
        > :::image type="content" source="./media/workflow-automation/azure-policy-next-to-workflow-automation.png" alt-text="工作流自动化列表&quot;:::

    在此页上，你可创建新的自动化规则，还可启用、禁用或删除现有规则。

1. 若要定义新工作流，请单击“添加工作流自动化”。 

    此时会出现一个窗格，其中包含用于新的自动化的选项。 可在此处输入：
    1. 自动化的名称和说明。
    1. 将启动此自动工作流的触发器。 例如，你可能希望在生成包含“SQL”的安全警报时运行逻辑应用。

        > [!NOTE]
        > 如果触发器是包含 &quot;子建议" lightbox="./media/workflow-automation/azure-policy-next-to-workflow-automation.png":::

    1. （可选）若要将此分配应用于现有订阅，请打开 " **修正** " 选项卡，然后选择用于创建修正任务的选项。

1. 查看 "摘要" 页，然后选择 " **创建** "。


## <a name="data-types-schemas"></a>数据类型架构

若要查看传递到逻辑应用实例的安全警报或建议事件的原始事件架构，请访问[工作流自动化数据类型架构](https://aka.ms/ASCAutomationSchemas)。 如果你没有使用上述安全中心的内置逻辑应用连接器，而是使用逻辑应用的通用 HTTP 连接器，这将非常有用，而且你可根据需要使用事件 JSON 架构手动分析它。


## <a name="faq-for-workflow-automation"></a>工作流自动化的常见问题解答

### <a name="does-workflow-automation-support-any-business-continuity-or-disaster-recovery-bcdr-scenarios"></a>工作流自动化是否支持 BCDR) 方案的任何业务连续性或灾难恢复 (？

在为 BCDR 方案（其中目标资源遇到中断或其他灾难）准备环境时，组织应负责根据 Azure 事件中心、Log Analytics 工作区和逻辑应用中的准则建立备份，以防止数据丢失。

对于每个活动的自动化，我们建议创建一个 (禁用) 自动化的相同，并将其存储在其他位置。 发生服务中断时，可以启用这些备份自动化并维护正常操作。

了解有关 [Azure 逻辑应用的业务连续性和灾难恢复的](../logic-apps/business-continuity-disaster-recovery-guidance.md)详细信息。

## <a name="next-steps"></a>后续步骤

在本文中，你学习了如何创建逻辑应用、如何在安全中心自动执行这些应用以及如何手动运行它们。 

有关相关材料，请参阅： 

- [关于如何使用工作流自动化来自动执行安全响应的 Microsoft Learn 模块](/learn/modules/resolve-threats-with-azure-security-center/)
- [Azure 安全中心的安全建议](security-center-recommendations.md)
- [Azure 安全中心中的安全警报](security-center-alerts-overview.md)
- [关于 Azure 逻辑应用](../logic-apps/logic-apps-overview.md)
- [适用于 Azure 逻辑应用的连接器](../connectors/apis-list.md)
- [工作流自动化数据类型架构](https://aka.ms/ASCAutomationSchemas)