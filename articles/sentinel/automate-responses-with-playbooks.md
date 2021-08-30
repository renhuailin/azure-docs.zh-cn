---
title: 在 Azure Sentinel 中使用 playbook 实现威胁响应自动化 | Microsoft Docs
description: 本文介绍了 Azure Sentinel 中的自动化，并演示了如何使用 playbook 实现威胁防范和响应自动化。
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
ms.date: 06/29/2021
ms.author: yelevin
ms.openlocfilehash: bddd27b29a1546f0c985f7a5b3aa15027be75d46
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121726101"
---
# <a name="automate-threat-response-with-playbooks-in-azure-sentinel"></a>在 Azure Sentinel 中使用 playbook 实现威胁响应自动化

本文介绍了什么是 Azure Sentinel playbook，以及如何使用它们来实现安全业务流程、自动化和响应 (SOAR) 操作，在节省时间和资源的同时获得更好的结果。

## <a name="what-is-a-playbook"></a>什么是 playbook？

SIEM/SOC 团队通常会定期收到大量的安全警报和事件，数量之多甚至超出了可用人员的处理能力。 因此频繁出现许多警报被忽视、许多事件未调查的窘境，导致组织很容易在未注意到的情况下受到攻击。

这些警报和事件中的许多（如果不是大多数）警报和事件都符合定期模式，可通过特定的修正操作集和定义的修正操作集进行处理。

playbook 是可以作为例程从 Azure Sentinel 运行的修正操作的集合。 playbook 可以帮助 [**自动执行和协调威胁响应**](tutorial-respond-threats-playbook.md)；可以手动运行或设置为自动运行以响应特定警报或事件（当由分析规则或自动化规则触发时）。

例如，如果帐户和计算机受到损害，playbook 可以将计算机和网络隔离，并在 SOC 团队收到事件通知时阻止帐户。

Playbook 在订阅级别创建和应用，但新的“自动化”边栏选项卡中的“Playbook”选项卡显示了所有选定订阅上可用的所有 playbook。

### <a name="azure-logic-apps-basic-concepts"></a>Azure 逻辑应用的基本概念

Azure Sentinel 中的 playbook 基于在 [Azure 逻辑应用](../logic-apps/logic-apps-overview.md)中构建的工作流，这是一项云服务，可帮助计划、自动执行和协调整个企业范围内系统中的任务和工作流。 这意味着 playbook 可以利用逻辑应用的内置模板的所有功能和可定制性。

> [!NOTE]
> 由于 Azure 逻辑应用是单独的资源，因此可能需要支付额外费用。 有关更多详细信息，请访问 [Azure 逻辑应用](https://azure.microsoft.com/pricing/details/logic-apps/)定价页。

Azure 逻辑应用使用连接器与其他系统和服务通信。 下面是连接器及其一些重要属性的简要说明：

- **托管连接器：** 一组将 API 调用封装到特定产品或服务的操作和触发器。 Azure 逻辑应用提供数百个连接器，用于与 Microsoft 和非 Microsoft 服务进行通信。
  - [所有逻辑应用连接器及其文档的列表](/connectors/connector-reference/)

- **自定义连接器：** 你可能希望与那些无法用作预生成连接器的服务通信。 自定义连接器可以解决这个需求，因为它可以让你创建甚至共享连接器并定义其自己的触发器和操作。
  - [创建自己的自定义逻辑应用连接器](/connectors/custom-connectors/create-logic-apps-connector)

- **Azure Sentinel 连接器：** 若要创建与 Azure Sentinel 交互的 playbook，请使用 Azure Sentinel 连接器。
  - [Azure Sentinel 连接器文档](/connectors/azuresentinel/)

- **触发器：** 启动 playbook 的连接器组件。 它定义 playbook 在触发时期望接收的架构。 Azure Sentinel 连接器当前有两个触发器：
  - [警报触发器](/connectors/azuresentinel/#triggers)：playbook 接收警报作为输入。
  - [事件触发器](/connectors/azuresentinel/#triggers)：playbook 接收事件作为输入，同时接收其包含的所有警报和实体。

    > [!IMPORTANT]
    >
    > - playbook 的“事件触发器”功能目前提供“预览版”。 请参阅 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)，了解适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。

- **操作：** 操作是在触发器之后发生的所有步骤。 它们可以按顺序排列、并行排列或以复杂条件矩阵排列。

- **动态字段：** 临时字段，由触发器和操作的输出架构确定，由它们的实际输出进行填充，可在后续操作中使用。

### <a name="permissions-required"></a>所需的权限

 要使 SecOps 团队能够使用逻辑应用进行安全业务流程、自动化和响应 (SOAR) 操作（即在 Azure Sentinel 中创建并运行 playbook），可以将 Azure 角色分配给安全运营团队的特定成员或分配给整个团队。 下面介绍了应分配给他们的不同的可用角色和任务：

#### <a name="azure-roles-for-logic-apps"></a>用于逻辑应用的 Azure 角色

- “逻辑应用参与者”允许管理逻辑应用和运行 playbook，但不能更改对它们的访问（为此，需要“所有者”角色）。
- “逻辑应用操作员”允许读取、启用和禁用逻辑应用，但不能对其进行编辑或更新。

#### <a name="azure-roles-for-sentinel"></a>用于 Sentinel 的 Azure 角色

- “Azure Sentinel 参与者”角色允许将 playbook 附加到分析规则。
- “Azure Sentinel 响应方”角色允许手动运行 playbook。
- “Azure Sentinel 自动化参与者”允许自动执行规则以运行 playbook。 它不用于任何其他用途。

#### <a name="learn-more"></a>了解更多

- [详细了解 Azure 逻辑应用中的 Azure 角色](../logic-apps/logic-apps-securing-a-logic-app.md#access-to-logic-app-operations)。
- [详细了解 Azure Sentinel 中的 Azure 角色](roles.md)。

## <a name="steps-for-creating-a-playbook"></a>创建 playbook 的步骤

- [定义自动化方案](#use-cases-for-playbooks)。

- [构建 Azure 逻辑应用](tutorial-respond-threats-playbook.md)。

- [测试逻辑应用](#run-a-playbook-manually-on-an-alert)。

- 将 playbook 附加到[自动化规则](#incident-creation-automated-response)或[分析规则](#alert-creation-automated-response)，或[在需要时手动运行](#run-a-playbook-manually-on-an-alert)。

### <a name="use-cases-for-playbooks"></a>playbook 的用例

Azure 逻辑应用平台提供数百个操作和触发器，因此几乎可以创建任何自动化方案。 Azure Sentinel 建议从以下 SOC 方案开始：

#### <a name="enrichment"></a>扩充

收集数据并将其附加到事件，以便做出更明智的决策。

例如：

Azure Sentinel 事件由生成 IP 地址实体的分析规则从警报创建。

此事件将触发一个自动化规则，该规则使用以下步骤运行 playbook：

- [创建新的 Azure Sentinel 事件](/connectors/azuresentinel/#triggers)时启动。 事件中表示的实体存储在事件触发器的动态字段中。

- 对于每个 IP 地址，查询外部威胁情报提供程序（如 [Virus Total](https://www.virustotal.com/)）来检索更多数据。

- 添加返回的数据和见解作为事件的注释。

#### <a name="bi-directional-sync"></a>双向同步

Playbook 可用于将 Azure Sentinel 事件与其他票证系统同步。

例如：

为所有事件创建创建自动化规则，并附加一个在 ServiceNow 中打开票证的 playbook：

- [创建新的 Azure Sentinel 事件](/connectors/azuresentinel/#triggers)时启动。

- 在 [ServiceNow](/connectors/service-now/) 中创建新票证。

- 在票证中包含事件名称、重要字段和 Azure Sentinel 事件的 URL，以便于进行透视。

#### <a name="orchestration"></a>资源协调

使用 SOC 聊天平台更好地控制事件队列。

例如：

Azure Sentinel 事件由生成用户名和 IP 地址实体的分析规则从警报创建。

此事件将触发一个自动化规则，该规则使用以下步骤运行 playbook：

- [创建新的 Azure Sentinel 事件](/connectors/azuresentinel/#triggers)时启动。

- 向 [Microsoft Teams](/connectors/teams/) 或 [Slack](/connectors/slack/) 中的安全操作通道发送一条消息，确保你的安全分析师注意到此事件。

- 将警报中的所有信息通过电子邮件发送给高级网络管理员和安全管理员。该电子邮件中会包含“阻止”和“忽略”用户选项按钮 。

- 等到管理员收到回复，再继续运行。

- 如果管理员选择了“阻止”，则向防火墙发送一条命令以阻止警报中的 IP 地址，再向 Azure AD 发送一条命令以禁用该用户。

#### <a name="response"></a>响应

以最少人为依赖项立即响应威胁。

两个示例：

**示例 1：** 响应指示遭入侵的用户的分析规则，如 [Azure AD 标识保护](../active-directory/identity-protection/overview-identity-protection.md)所发现的：

   - [创建新的 Azure Sentinel 事件](/connectors/azuresentinel/#triggers)时启动。

   - 对于事件中怀疑遭入侵的每个用户实体：

     - 向用户发送一条 Teams 消息，请求确认用户采取了可疑操作。

     - 检查 Azure AD 标识保护，[将用户的状态确认为遭入侵](/connectors/azureadip/#confirm-a-risky-user-as-compromised)。 Azure AD 标识保护会将用户标记为“有风险”，并应用已配置的任何强制策略 - 例如，要求用户在下次登录时使用 MFA。

        > [!NOTE]
        > playbook 不会对用户启动任何强制操作，也不会启动强制策略的任何配置。 它只会告诉 Azure AD 标识保护根据需要应用任何已定义的策略。 所有强制操作都完全取决于在 Azure AD 标识保护中定义的相应策略。

**示例 2：** 响应指示遭入侵的计算机的分析规则，如 [Microsoft Defender For Endpoint](/windows/security/threat-protection/) 所发现的：

   - [创建新的 Azure Sentinel 事件](/connectors/azuresentinel/#triggers)时启动。

   - 使用 Azure Sentinel 中的“实体 - 获取主机”操作来分析事件实体中包含的可疑计算机。

   - 向 Microsoft Defender for Endpoint 发出命令以[隔离警报中的计算机](/connectors/wdatp/#actions---isolate-machine)。

## <a name="how-to-run-a-playbook"></a>如何运行 playbook

Playbook 既可“手动”运行，也可“自动”运行。

手动运行是指在收到警报后，可选择按需运行 playbook 作为所选警报的响应。 此功能目前仅支持警报，不支持事件。

自动运行它们意味着在分析规则中将它们设置为自动响应（警报），或在自动化规则中设置为操作（事件）。 [详细了解自动化规则](automate-incident-handling-with-automation-rules.md)。

### <a name="set-an-automated-response"></a>设置自动响应

安全运营团队可以大幅减少其工作量，因为他们可以完全自动化对重复出现的事件和警报类型的常规响应，从而让你可以更专注于处理独特事件和警报，分析模式和搜寻威胁等活动。

设置自动响应意味着每次触发分析规则时，除了创建警报，规则还会运行 playbook，该 playbook 将接收由规则创建的警报作为输入。

如果警报创建事件，该事件将触发自动化规则，自动化规则反过来又可能运行 playbook，playbook 将接收由警报创建的事件作为输入。

#### <a name="alert-creation-automated-response"></a>警报创建自动响应

对于通过创建警报触发并接收警报作为其输入的 playbook（其第一步是“触发 Azure Sentinel 警报时”），将此 playbook 附加到分析规则：

1. 编辑生成要为其定义自动响应的警报的[分析规则](detect-threats-custom.md)。

1. 在“自动响应”选项卡的“警报自动化”下，选择在创建警报时此分析规则将触发的 playbook。

#### <a name="incident-creation-automated-response"></a>事件创建自动响应

对于由创建事件触发并接收事件作为其输入的 playbook（其第一步是“触发 Azure Sentinel 事件时”），创建自动化规则并在其中定义“运行 playbook”操作。 可以通过两种方法实现此目的：

- 编辑生成要为其定义自动响应的事件的分析规则。 在“自动响应”选项卡的“事件自动化”下，创建一个自动化规则。 这将只为此分析规则创建自动响应。

- 从“自动化”边栏选项卡的“自动化规则”选项卡中，创建新的自动化规则并指定合适的条件和所需的操作。 此自动化规则将应用到满足指定条件的任何分析规则。

    > [!NOTE]
    > **Microsoft Azure Sentinel 自动化规则需要运行 playbook 的权限。**
    >
    > 若要从自动化规则运行 playbook，Azure Sentinel 需使用专门授权执行此操作的服务帐户。 使用此帐户（与用户帐户相对）可提高服务的安全性，并使自动化规则 API 能够支持 CI/CD 用例。
    >
    > 必须在 playbook 驻留的资源组中为此帐户授予显式权限（驻留）角色（以 **Microsoft Azure Sentinel 自动化参与者** 角色的形式）。 届时，任何自动化规则都将能够运行该资源组中的任何 playbook。
    >
    > 如果向自动化规则添加 **运行 playbook** 操作，将显示 playbook 的下拉列表以供选择。 Azure Sentinel 没有其权限的 playbook 将显示为“不可用”（“灰显”）。 可以通过选择“管理 playbook 权限”链接，当场向 Azure Sentinel 授予权限。
    >
    > 在多租户 ([Lighthouse](extend-sentinel-across-workspaces-tenants.md#managing-workspaces-across-tenants-using-azure-lighthouse)) 情况下，必须在 playbook 所在的租户上定义权限，即使调用 playbook 的自动化规则在其他租户中。 为此，必须拥有 playbook 的资源组的“所有者”权限。
    >
    > **托管安全服务提供程序 (MSSP)** 具有一种独特方案，其中，服务提供商在登录自己的租户时，会使用 [Azure Lighthouse](../lighthouse/index.yml) 在客户工作区创建自动化规则。 然后，此自动化规则会调用属于客户租户的 playbook。 在这种情况下，必须为 Microsoft Azure Sentinel 授予权限 **_这两个租户_ *_的权限。在客户租户中，可以在_* 管理 playbook 权限** 面板中授予权限，正如常规的多租户方案。 要在服务提供商租户中授予相关权限，需要添加一个额外的 Azure Lighthouse 委派，该委派将在 playbook 所在的资源组上使用 **Microsoft Azure Sentinel 自动化参与者** 角色授予对 **Azure Security Insights** 应用的访问权限。 [了解如何添加此委派](tutorial-respond-threats-playbook.md#permissions-to-run-playbooks)。

请参阅[创建自动化规则的完整说明](tutorial-respond-threats-playbook.md#respond-to-incidents)。

### <a name="run-a-playbook-manually-on-an-alert"></a>对警报手动运行 playbook

可从 Azure Sentinel 门户的以下边栏选项卡中进行手动触发：

- 在“事件”视图中，选择特定事件，打开其“警报”选项卡，然后选择一个警报。

- 在“调查”中，选择特定警报。

1. 单击“查看 playbook”查看所选的警报。 将显示以“触发 Azure Sentinel 警报时”开始且你有权访问的所有 playbook 的列表。

1. 在特定 playbook 行上单击“运行”以触发它。

1. 选择“运行”选项卡，查看在此警报上运行任何 playbook 的所有时间的列表。 任何刚完成的运行都可能需要几秒钟才能出现在此列表中。

1. 单击特定的运行会在逻辑应用中打开完整运行日志。

### <a name="run-a-playbook-manually-on-an-incident"></a>对事件手动运行 playbook

尚不支持。

## <a name="manage-your-playbooks"></a>管理 playbook

在“Playbook”选项卡中，会显示你有权访问的所有 playbook 的列表，该列表按当前显示在 Azure 中的订阅进行筛选。 全局页眉中的“目录 + 订阅”菜单中提供订阅筛选器。

单击 playbook 名称可打开 playbook 在逻辑应用中的主页。 “状态”列指示它已启用还是已禁用。

“触发器种类”表示启动此 playbook 的逻辑应用触发器。

| 触发器种类 | 指示 playbook 中的组件类型 |
|-|-|
| **Azure Sentinel 事件/警报** | playbook 使用其中一个 Sentinel 触发器（警报、事件）启动 |
| **使用 Azure Sentinel 操作** | playbook 使用非 Sentinel 触发器启动，但使用 Azure Sentinel 操作 |
| **其他** | playbook 不包括任何 Sentinel 组件 |
| **未初始化** | playbook 已创建，但不包含组件（触发器或操作）。 |
|

在 playbook 的“逻辑应用”页中，可以看到有关 playbook 的详细信息，包括所有运行时间的日志和结果（成功或失败以及其他详细信息）。 如果拥有适当的权限，还可以进入逻辑应用设计器并直接编辑 playbook。

### <a name="api-connections"></a>API 连接

API 连接用于将逻辑应用连接到其他服务。 每次对逻辑应用连接器进行新的身份验证时，都会创建一个类型为“API 连接”的新资源，其中包含配置服务访问权限时所提供的信息。

若要查看所有 API 连接，请在 Azure 门户的标头搜索框中输入“API 连接”。 请注意相关列：

- 显示名称 - 每次创建连接时为连接指定的“友好”名称。
- 状态 - 表示连接状态：错误、已连接。
- 资源组 - 在 playbook（逻辑应用）资源的资源组中创建 API 连接。

查看 API 连接的另一种方式是转到“所有资源”边栏选项卡，按类型“API 连接”进行筛选。 这样可以一次选择、标记和删除多个连接。

若要更改现有连接的授权，请输入连接资源，然后选择“编辑 API 连接”。

## <a name="recommended-playbooks"></a>建议的 playbook

以下建议的 playbook 和其他相似 playbook 在 [Azure Sentinel GitHub 存储库](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks)中提供：

- 创建警报或事件时会触发通知 playbook，并向配置目标发送通知：

    - [在 Microsoft Teams 通道中发布消息](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks/Post-Message-Teams)
    - [发送 Outlook 电子邮件通知](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks/Incident-Email-Notification)
    - [在 Slack 通道中发布消息](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks/Post-Message-Slack)

- 创建警报或事件时，会触发阻止 playbook，收集帐户、IP 地址和主机等实体信息，并阻止它们采取进一步行动：

    - [阻止 IP 地址的提示](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks/Block-IPs-on-MDATP-Using-GraphSecurity)。
    - [阻止 AAD 用户](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks/Block-AADUser)
    - [重置 AAD 用户密码](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks/Reset-AADUserPassword/)
    - [隔离计算机的提示](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks/Isolate-AzureVMtoNSG)

- 创建、更新或关闭 playbook 可以在 Azure Sentinel、Microsoft 365 安全服务或其他票证服务中创建、更新和关闭事件：

    - [更改事件的严重性](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks/Change-Incident-Severity)
    - [创建 ServiceNow 事件](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks/Create-SNOW-record)


## <a name="next-steps"></a>后续步骤

- [教程：在 Azure Sentinel 中使用 playbook 自动响应威胁](tutorial-respond-threats-playbook.md)
