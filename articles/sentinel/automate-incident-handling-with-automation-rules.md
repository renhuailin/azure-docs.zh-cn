---
title: 在 Azure Sentinel 中自动处理事件 | Microsoft Docs
description: 本文介绍如何使用自动化规则自动处理事件，以便最大程度地提高 SOC 响应安全威胁的效率和有效性。
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
ms.topic: conceptual
ms.date: 03/14/2021
ms.author: yelevin
ms.openlocfilehash: 1244959bac7a2c530444e3d4b36691d4f760529a
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121731055"
---
# <a name="automate-incident-handling-in-azure-sentinel-with-automation-rules"></a>在 Azure Sentinel 中使用自动化规则自动处理事件

本文介绍什么是 Azure Sentinel 自动化规则，以及如何使用它们来实现安全业务流程、自动化和响应 (SOAR) 操作，从而提高 SOC 的有效性并节省时间和资源。

> [!IMPORTANT]
>
> - 自动化规则功能目前为预览版。 请参阅 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)，了解适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。

## <a name="what-are-automation-rules"></a>什么是自动化规则？

自动化规则是 Azure Sentinel 中的一个新概念。 通过此功能，用户可以集中管理事件处理自动化。 除了允许向事件分配 playbook（之前只有警报），自动化规则还允许一次性自动执行多个分析规则的响应，自动标记、分配或关闭事件而无需 playbook，并控制执行操作的顺序。 自动化规则可简化 Azure Sentinel 中的自动化使用，并能够简化事件业务流程进程的复杂工作流。

## <a name="components"></a>组件

自动化规则由多个组件组成：

### <a name="trigger"></a>触发器

自动化规则由于生成事件而触发。 

复习一下，事件由分析规则根据警报生成，其中包括多种类型，如“[使用 Azure Sentinel 中的内置分析规则检测威胁](detect-threats-built-in.md)”教程中所述。

### <a name="conditions"></a>条件

可以定义复杂的条件集来管理何时应运行操作（参见以下内容）。 这些条件通常基于事件及其实体的属性状态或值，并且可以包含 `AND`/`OR`/`NOT`/`CONTAINS` 运算符。

### <a name="actions"></a>操作

可以定义在满足条件（参见上述内容）时运行的操作。 可以在规则中定义许多操作，还可以选择它们的运行顺序（参见以下内容）。 可以使用自动化规则定义以下操作，而无需使用 [playbook 的高级功能](automate-responses-with-playbooks.md)：

- 更改事件的状态，使工作流保持最新。

  - 当更改为“已关闭”时，指定[关闭原因](investigate-cases.md#closing-an-incident)并添加注释。 这有助于跟踪性能和有效性，并进行优化以减少[误报](false-positives.md)。

- 更改事件的严重性 – 可以基于事件中所涉及实体的现状、缺席、值或属性重新评估，以及重新确定优先级。

- 将事件指派给所有者 - 这样有助于将事件类型转给最适合处理它们的人员，或可用的最恰当人员来处理它们。

- 对事件添加标记 - 有利于按主题、攻击者或任何其他常见标准对事件进行分类。

此外，还可以定义操作来[运行 playbook](tutorial-respond-threats-playbook.md)，以便执行更复杂的响应操作，包括涉及外部系统的任何操作。 自动化规则中只能使用由[事件触发器](automate-responses-with-playbooks.md#azure-logic-apps-basic-concepts)激活的 playbook 。 可以定义操作来包括多个 playbook 或 playbook 与其他操作的组合，还可以定义它们的运行顺序。

### <a name="expiration-date"></a>到期日期

可以对自动化规则定义到期日期。 规则在该日期之后将被禁用。 该项适用于处理（即关闭）因计划的有时限的活动（如渗透测试）导致的“噪音”事件。

### <a name="order"></a>订单

可以定义运行自动化规则的顺序。 后面的自动化规则将根据之前自动化规则对事件采取行动后的状态来评估事件状况。

例如，如果“第一个自动化规则”将事件严重性从“中”变为“低”，而“第二个自动化规则”定义为仅对中高危险性事件运行，则不会对该事件运行第二个自动化规则。

## <a name="common-use-cases-and-scenarios"></a>常见用例和场景

### <a name="incident-triggered-automation"></a>事件触发的自动化

到目前为止，只有警报可以通过使用 playbook 来触发自动响应。 使用自动化规则，在生成事件时现在也可以通过事件来触发自动响应链，其中可能包括新的事件触发 playbook（[需要特殊权限](#permissions-for-automation-rules-to-run-playbooks)）。 

### <a name="trigger-playbooks-for-microsoft-providers"></a>适用于 Microsoft 提供程序的触发 playbook

自动化规则提供一种自动处理 Microsoft 安全警报的方法，即对基于警报生成的事件应用这些规则。 自动化规则可以调用 playbook（[需要特殊权限](#permissions-for-automation-rules-to-run-playbooks)），并将事件及其所有详细信息（包括警报和实体）传递给它们。 通常，Azure Sentinel 最佳做法规定使用事件队列作为安全操作的焦点。

Microsoft 安全警报包括以下各项：

- Microsoft Cloud App Security (MCAS)
- Azure AD 标识保护
- Azure Defender (ASC)
- Defender for IoT（以前称为 ASC for IoT）
- Microsoft Defender for Office 365（前身为 Office 365 ATP）
- 用于终结点的 Microsoft Defender（以前称为 MDATP）
- 用于标识的 Microsoft Defender（以前称为 Azure ATP）

### <a name="multiple-sequenced-playbooksactions-in-a-single-rule"></a>单个规则中包含多个排序的 playbook/操作

现在，可以在单个自动化规则中近乎完全地控制操作和 playbook 的执行顺序。 此外，还可以控制自动化规则本身的执行顺序。 这样，可以大大简化 playbook，将其缩减为单个任务或一系列简单的小任务，并将这些小 playbook 组合到不同自动化规则的不同组合中。

### <a name="assign-one-playbook-to-multiple-analytics-rules-at-once"></a>一次将一个 playbook 分配给多个分析规则

如果有任务需要自动执行所有分析规则（例如，在外部票证系统中生成支持票证），可以一次将单个 playbook 应用于任何或所有分析规则（包括未来建立的任何规则）。 这样一来，简单的重复性维护和整理任务则变得轻松很多。

### <a name="automatic-assignment-of-incidents"></a>自动分配事件

可以自动将事件分配给适当的所有者。 如果 SOC 有一位专门从事特定平台工作的分析师，则与该平台相关的任何事件都可以自动分配给该分析师。

### <a name="incident-suppression"></a>事件抑制

可以使用规则来自动解析已知属于误报/良性警报的事件，而无需使用 playbook。 例如，在运行渗透测试、执行计划内维护或升级或测试自动化过程时，可能会产生许多 SOC 想要忽略的误报事件。 有时限的自动化规则可以在这些事件产生时自动将其关闭，同时为它们标记生成原因描述符。

### <a name="time-limited-automation"></a>有时限的自动化

可以为自动化规则添加到期日期。 除事件抑制之外，可能有些情况下还需要有时限的自动化。 你可能希望在特定期限内，将特定类型的事件分配给特定用户（例如实习生或顾问）。 如果事先知道期限，则可以有效地使该规则在其相关性结束后处于禁用状态，而无需再记着去做这件事。

### <a name="automatically-tag-incidents"></a>自动标记事件

可以对事件自动添加任意文本标记，以便根据所选的任何标准对它们进行分组或分类。

## <a name="automation-rules-execution"></a>执行自动化规则

自动化规则可以按确定的顺序连续运行。 每个自动化规则在前一规则运行完成后执行。 在自动化规则中，所有操作都按其定义的顺序连续运行。

在某些情况下，可能会根据以下条件以不同方式处理自动化规则中的 playbook 操作：

| Playbook 运行时 | 自动化规则前进到下一个操作... |
| ----------------- | --------------------------------------------------- |
| 不到 1 秒 | Playbook 完成后立即 |
| 不到 2 分钟 | 在 playbook 开始运行后最多 2 分钟，<br>但在 playbook 完成后不超过 10 秒 |
| 超过 2 分钟 | Playbook 开始运行 2 分钟后，<br>无论其是否已完成 |
|

### <a name="permissions-for-automation-rules-to-run-playbooks"></a>自动化规则运行 playbook 的权限

当 Azure Sentinel 自动化规则运行 playbook 时，它将使用专门为此操作而获得授权的特殊 Azure Sentinel 服务帐户。 使用此帐户（相对于你的用户帐户）可提高服务的安全级别。

为使自动化规则运行 playbook，此帐户必须被授予访问 playbook 所在资源组的显式权限。 届时，任何自动化规则都将能够运行该资源组中的任何 playbook。

在配置自动化规则和添加“运行 playbook”操作时，将显示一个 playbook 的下拉列表。 Azure Sentinel 没有权限访问的 playbook 将显示为不可用（“灰显”）。 可以通过选择“管理 playbook 权限”链接，现场对 Azure Sentinel 授予访问 playbook 资源组的权限。

#### <a name="permissions-in-a-multi-tenant-architecture"></a>多租户体系结构中的权限

自动化规则完全支持跨工作区和[多租户部署](extend-sentinel-across-workspaces-tenants.md#managing-workspaces-across-tenants-using-azure-lighthouse)（对于多租户，使用 [Azure Lighthouse](../lighthouse/index.yml)）。

因此，如果 Azure Sentinel 部署使用多租户体系结构，则可以用一个租户中的自动化规则运行其他租户中的 playbook，但必须在 playbook 所在的租户中定义 Sentinel 运行该 playbook 的权限（而不是在自动化规则所在的租户中定义）。

在安全托管服务提供商 (MSSP) 的特定情况下，服务提供商租户将管理客户租户中的 Azure Sentinel 工作区，但有两种特殊情况值得注意：

- **在客户租户中创建的自动化规则配置为运行位于服务提供商租户中的 playbook。** 

    这种方法通常用于保护 playbook 中的知识产权。 此方案无需进行特殊设置即可运行。 在自动化规则中定义 playbook 操作时，如果进入为 Azure Sentinel 授予对 playbook 所在的相关资源组的权限的阶段（使用“管理 playbook 权限”面板），你将看到属于服务提供商租户的资源组，可从中进行选择。 [查看此处概述的整个过程](tutorial-respond-threats-playbook.md#respond-to-incidents)。

- **在客户工作区中创建（同时登录到服务提供商租户）的自动化规则配置为运行位于客户租户中的 playbook**。

    无需保护知识产权时使用此配置。 要使此方案有效，需要在这两个租户中为 Azure Sentinel 授予执行 playbook 的权限。 在客户租户中，可在“管理 playbook 权限”面板中授予权限，就像上述方案一样。 若要在服务提供商租户中授予相关权限，需要添加一个额外的 Azure Lighthouse 委派，该委派将在 playbook 所在的资源组上使用“Azure Sentinel 自动化参与者”角色授予对 Azure Security Insights 应用的访问权限 。

    场景如下所示：

    :::image type="content" source="./media/automate-incident-handling-with-automation-rules/automation-rule-multi-tenant.png" alt-text="多租户自动化规则体系结构":::

    请参阅[相关说明](tutorial-respond-threats-playbook.md#permissions-to-run-playbooks)进行设置。

## <a name="creating-and-managing-automation-rules"></a>创建和管理自动化规则

可以根据特定需要和用例，在 Azure Sentinel 体验的不同节点中创建和管理自动化规则。

- **“自动化”边栏选项卡**

    在“自动化规则”选项卡下的新“自动化”边栏选项卡（替换了“Playbook”边栏选项卡）中，可以集中管理自动化规则  。（现在，也可以在“Playbook”选项卡下管理 playbook。）在那里，可以创建新自动化规则和编辑现有的规则。 此外，还可以拖动自动化规则来更改其执行顺序，以及启用或禁用它们。

    在“自动化”边栏选项卡中，可以看到在工作区上定义的所有规则及其状态（已启用/已禁用），以及它们适用于哪些分析规则。

    如果需要将一个自动化规则应用于多个分析规则，可直接在“自动化”边栏选项卡中进行创建。 在顶部菜单中，依次单击“创建”和“添加新规则” ，随即将打开“创建新自动化规则”面板。 在此处，可以完全灵活地配置规则：可以将其应用于任何分析规则（包括未来的分析规则）以及定义最广泛的条件和操作。

- **分析规则向导**

    在分析规则向导的“自动响应”选项卡中，可以查看、管理和创建自动化规则，以应用于在向导中创建或编辑的特定分析规则。

    单击“创建”并从顶部菜单的“分析”边栏选项卡中选择一种规则类型（“计划的查询规则”或“Microsoft 事件创建规则”）后，或选择现有的分析规则并单击“编辑”后，将打开规则向导    。 选择“自动响应”选项卡后，将会看到一个名为“事件自动化”的部分，其中显示当前应用于此规则的自动化规则。 可以选择现有的自动化规则进行编辑，也可以单击“新建”创建一个新规则。

    你会发现，在这里创建自动化规则后，“创建新自动化规则”面板会显示“分析规则”条件不可用，这是因为此规则已设置为仅应用于在向导中编辑的分析规则。 所有其他配置选项仍可使用。

- **“事件”边栏选项卡**

    另外，还可以在“事件”边栏选项卡中创建自动化规则，以响应单个重复出现的事件。 在创建[抑制规则](#incident-suppression)来自动关闭“干扰”事件时，这种方法非常有用。 从队列中选择一个事件，然后单击顶部菜单中的“创建自动化规则”。

    你会发现，“创建新自动化规则”面板已对所有字段填充事件中的值。 它将为规则命名与事件相同的名称，将其应用于生成该事件的分析规则，并使用该事件中所有可用的实体作为规则条件。 此外，它还提供了建议的默认抑制（关闭）操作以及建议的规则到期日期。 可以根据需要添加或删除条件和操作，以及更改到期日期。

## <a name="auditing-automation-rule-activity"></a>审核自动化规则活动

你可能想要了解特定事件发生了什么状况，以及特定自动化规则可能或不可能对其执行哪些操作。 在“日志”边栏选项卡的“SecurityIncident”表中，可以找到完整的事件历史记录。 使用以下查询可查看所有自动化规则活动：

```kusto
SecurityIncident
| where ModifiedBy contains "Automation"
```

## <a name="next-steps"></a>后续步骤

在本文档中，你已学习了如何使用自动化规则来管理 Azure Sentinel 事件队列，以及实现一些基本的事件处理自动化。

- 有关高级自动化选项的详细信息，请参阅[在 Azure Sentinel 中利用 playbook 自动执行威胁响应](automate-responses-with-playbooks.md)。
- 有关实施自动化规则和 playbook 方面的帮助，请参阅[教程：在 Azure Sentinel 中使用 playbook 自动完成威胁响应](tutorial-respond-threats-playbook.md)。
