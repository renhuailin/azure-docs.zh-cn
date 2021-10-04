---
title: 特权帐户的 Azure Active Directory 安全操作
description: 了解如何设置基线，并对 Azure Active Directory 中特权帐户的潜在安全问题进行监视和警报。
services: active-directory
author: BarbaraSelden
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 07/15/2021
ms.author: baselden
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3c4a9afd9e2470085509c809153fd2abd9a63c27
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124796431"
---
# <a name="security-operations-for-privileged-accounts"></a>特权帐户的安全操作

业务资产的安全性取决于管理 IT 系统的特权帐户的完整性。 网络攻击者会利用凭据窃取攻击和其他手段，以特权帐户为目标，获取对敏感数据的访问权限。

传统上，组织安全侧重于将网络的入口点和出口点作为安全外围。 然而，软件即服务 (SaaS) 应用和 Internet 上的个人设备使这种方法不太有效。 

Azure Active Directory (Azure AD) 使用标识和访问管理 (IAM) 作为控制平面。 在组织的标识层中，分配有特权管理角色的用户掌握着控制权限。 无论是在本地、云中还是混合环境中，都必须保护用于访问的帐户。

你对本地 IT 环境各个层级的安全完全负责。 使用 Azure 服务时，预防和响应是 Microsoft 作为云服务提供商和你作为客户的共同责任。

* 有关共担责任模型的详细信息，请访问[云中共担责任](../../security/fundamentals/shared-responsibility.md)。

* 有关保护特权用户访问权限的详细信息，请访问[确保 Azure AD 中混合部署和云部署的特权访问安全性](../roles/security-planning.md)。

* 如需特权标识的各种视频、操作指南和重要概念的内容，请访问 [Privileged Identity Management 文档](../privileged-identity-management/index.yml)。

## <a name="where-to-look"></a>查看位置

用于调查和监视的日志文件包括： 

* [Azure AD 审核日志](../reports-monitoring/concept-audit-logs.md)

* [Microsoft 365 审核日志](/microsoft-365/compliance/auditing-solutions-overview) 

* [Azure Key Vault 见解](../../azure-monitor/insights/key-vault-insights-overview.md)

在 Azure 门户中，可以查看 Azure AD 审核日志，并将其作为逗号分隔值 (CSV) 或 JavaScript 对象表示法 (JSON) 文件下载。 Azure 门户提供多种方法将 Azure AD 日志与其他工具相集成，以便更好地自动执行监视和警报：

* [Azure Sentinel](../../sentinel/overview.md) – 提供安全信息和事件管理 (SIEM) 功能，实现企业级智能安全分析。 

* [Azure Monitor](../../azure-monitor/overview.md) – 实现对各种情况的自动监视和警报。 可以创建或使用工作簿合并来自不同源的数据。

* 与 SIEM 集成的 [Azure 事件中心](../../event-hubs/event-hubs-about.md) - 通过 Azure 事件中心集成[可将 Azure AD 日志推送到其他 SIEM](../reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md)，例如 Splunk、ArcSight、QRadar 和 Sumo Logic。

* [Microsoft Cloud App Security](/cloud-app-security/what-is-cloud-app-security) (MCAS) – 支持发现和管理应用、跨应用和资源进行治理，以及检查云应用的符合性。 

* Microsoft Graph - 可以导出数据和用户 MS Graph 来执行更多分析。 有关 MS Graph 的详细信息，请访问 [Microsoft Graph PowerShell SDK 和 Azure Active Directory 标识保护](../identity-protection/howto-identity-protection-graph-api.md)。 

* [标识保护](../identity-protection/overview-identity-protection.md) – 生成可用于帮助进行调查的三份关键报告：

   * 风险用户 - 包含有关哪些用户存在风险的信息、有关检测的详细信息、所有风险登录的历史记录，以及风险历史记录。

   * 风险登录 – 包含有关可能表明有可疑情况的登录情况的信息。 有关根据此报告中的信息进行调查的详细信息，请访问[操作指南：调查风险](../identity-protection/howto-identity-protection-investigate-risk.md)。 

   * 风险检测 – 包含在检测到风险时触发的其他风险的信息，以及其他相关的信息，例如登录位置和 Microsoft Cloud App Security (MCAS) 的任何详细信息。

 

虽然我们不鼓励这种做法，但特权帐户可以具有长期管理权限。 如果选择使用长期特权，帐户遭入侵后会造成极为不利的影响。 建议优先监视特权帐户，并将这些帐户纳入 Privileged Identity Management (PIM) 配置。 有关 PIM 的详细信息，请参阅[开始使用 Privileged Identity Management](../privileged-identity-management/pim-getting-started.md)。 此外，建议验证管理员帐户，确保其：

* 是必需的。

* 具有执行所需活动的最小特权。

* 至少使用 MFA 进行保护。

* 通过特权访问工作站 (PAW) 或安全管理员工作站 (SAW) 设备运行。 

本文的其余部分将按威胁类型介绍我们建议监视和发出警报的内容。 如果有特定预生成解决方案，我们会在表后链接到这些解决方案。 除此之外，可以使用前面的工具来生成警报。 具体而言，本文会详细介绍如何设置基线、审核特权帐户的登录和使用情况，以及可用来协助维护特权帐户完整性的工具和资源。 具体内容分为以下主题领域：

* 紧急“破窗”帐户 

* 特权帐户登录

* 特权帐户更改

* 特权组

* 特权分配和提升

## <a name="emergency-access-accounts"></a>紧急访问帐户

要防止被意外锁定在 Azure Active Directory (Azure AD) 租户之外，这一点很重要。 通过在组织中创建紧急访问帐户，可缓解意外锁定的影响。 紧急访问帐户也称为“破窗”帐户，就像在火警警报器等物理安全设备上标识的“紧急情况下破窗逃生”信息。

紧急访问帐户拥有较高的特权，因此请不要将其分配给特定的个人。 紧急访问帐户只能用于紧急情况或“需要破窗”的情况，即无法使用正常特权帐户的情况。 例如，当条件访问策略配置错误并锁定了所有正常管理帐户时。 限制紧急帐户只能在绝对必要时使用。

另请参阅 [Azure AD 中面向管理员的安全访问做法](../roles/security-planning.md)，获取在紧急情况下如何操作的指导。

每次使用紧急访问帐户时，都发送高优先级警报。

### <a name="discovery"></a>发现 (Discovery)

由于只有在发生紧急情况时才会使用“破窗”帐户，因此你的监视应该不会发现任何帐户活动。 每次使用或更改紧急访问帐户时，都发送高优先级警报。  以下任何事件都可能表明恶意行动者正试图入侵你的环境。

* 使用的帐户 - 对使用这种类型的帐户的任何活动进行监视并发出警报，包括：

* 登录

* 帐户密码更改 

* 帐户权限/角色更改 

* 添加或更改凭据或身份验证方法 

有关管理紧急访问帐户的详细信息，请参阅[在 Azure AD 中管理紧急访问管理员帐户](../roles/security-emergency-access.md)。 有关为紧急帐户创建警报的详细信息，请参阅[创建警报规则](../roles/security-emergency-access.md)。 

## <a name="privileged-account-sign-in"></a>特权帐户登录

使用 Azure AD 登录日志作为数据源，以监视所有特权帐户的登录活动。 除了登录成功和失败的信息外，日志还包含以下详细信息：

* 中断
* 设备
* 位置
* 风险
* 应用程序
* 日期和时间
* 帐户是否已禁用
* 锁定
* MFA 欺诈
* CA 失败

### <a name="things-to-monitor"></a>要监视的内容

可以在 Azure AD 登录日志中监视特权帐户登录事件。 对特权帐户的以下事件进行警报和调查。

| 要监视的内容 | 风险级别 |  Where |  筛选器/子筛选器 | 说明 |
| - | - | - | - | - |
| 登录失败，超出密码阈值 | 高 | Azure AD 登录日志 | 状态 = 失败<br>－和－<br>错误代码 = 50126 | 定义基线阈值，监视并调整以适应组织的行为，并限制生成虚假警报。 |
| 因不符合 CA 要求而失败 |高 | Azure AD 登录日志 | 状态 = 失败<br>－和－<br>错误代码 = 53003<br>－和－<br>失败原因 = 被 CA 阻止 | 这可能表示攻击者正在尝试入侵帐户 |
| 未遵循命名策略的特权帐户。| | Azure 订阅 | [使用 Azure 门户列出 Azure 角色分配 - Azure RBAC](../../role-based-access-control/role-assignments-list-portal.md)| 列出订阅的角色分配，并在登录名不符合组织规定的格式时发出警报。 例如，以 ADM_ 作为前缀。 |
| 中断 |  高/中 | Azure AD 登录 | 状态 = 已中断<br>－和－<br>错误代码 = 50074<br>－和－<br>失败原因 = 需要强身份验证<br>状态 = 已中断<br>－和－<br>错误代码 = 500121<br>失败原因 = 在强身份验证请求期间，身份验证失败 | 这可能表示攻击者拥有该帐户的密码，但无法通过 MFA 质询。 | 
| 未遵循命名策略的特权帐户。| 高 | Azure AD 目录 | [列出 Azure AD 角色分配](../roles/view-assignments.md)| 对于 Azure AD 角色警报，在 UPN 不符合组织规定格式时列出角色分配。 例如，以 ADM_ 作为前缀。 |
| 发现未注册使用 MFA 的特权帐户。 | 高 | Azure AD 图形 API| 对管理员帐户使用 IsMFARegistered eq false 查询。 [列出 credentialUserRegistrationDetails - Microsoft Graph beta 版本](/graph/api/reportroot-list-credentialuserregistrationdetails?view=graph-rest-beta&preserve-view=true&tabs=http) | 进行审核与调查，以确定是有意为之还是疏忽所致。 |
| 帐户锁定 | 高 | Azure AD 登录日志 | 状态 = 失败<br>－和－<br>错误代码 = 50053 | 定义基线阈值，监视并调整以适应组织的行为，并限制生成虚假警报。 |
| 帐户已禁用/被阻止登录 | 低 | Azure AD 登录日志 | 状态 = 失败<br>－和－<br>目标 = 用户 UPN<br>－和－<br>错误代码 = 50057 | 这可能表示有人在离开组织后尝试获取帐户访问权限。 虽然帐户被阻止，但记录这类活动并发出警报仍然很重要。 |
| MFA 欺诈警报/阻止 | 高 | Azure AD 登录日志/Azure Log Analytics | 是否成功 = false<br>－和－<br>结果详细信息 = MFA 被拒绝<br>－和－<br>目标 = 用户 | 特权用户表示其尚未启动 MFA 提示，并可能表示攻击者拥有该帐户的密码。 |
| 特权帐户在预期控制之外登录。 |  | Azure AD 登录日志 | 状态 = 失败<br>UserPricipalName = \<Admin account\><br>位置 = \<unapproved location\><br>IP 地址 = \<unapproved IP\><br>设备信息 = \<unapproved Browser, Operating System\> | 监视确定为“未批准”的任何条目并发出警报。 |
| 在正常登录时间之外 | 高 | Azure AD 登录日志 | 状态 = 成功<br>－和－<br>位置 =<br>－和－<br>时间 = 在工作时间之外 | 监视登录是否发生在预期时间之外，并发出警报。 务必确定每个特权帐户的正常工作模式，并在正常工作时间之外发生计划外更改时发出警报。 在正常工作时间之外登录可能表示存在入侵或可能的内部威胁。 | 
| 标识保护风险 | 高 | 标识保护日志 | 风险状态 = 有风险<br>－和－<br>风险级别 = 低/中/高<br>－和－<br>活动 = 不熟悉的登录/TOR 等 | 这表示在帐户登录时检测到一些异常，应发出警报。 | 
| 密码更改 | 高 | Azure AD 审核日志 | 活动参与者 = 管理员/自助服务<br>－和－<br>目标 = 用户<br>－和－<br>状态 = 成功/失败 | 在任何管理员帐户密码更改时发出警报，特别是全局管理员、用户管理员、订阅管理员和紧急访问帐户。 编写针对所有特权帐户的查询。 | 
| 更改旧身份验证协议 | 高 | Azure AD 登录日志 | 客户端应用 = 其他客户端、IMAP、POP3、MAPI、SMTP 等<br>－和－<br>用户名 = UPN<br>－和－<br>应用 = Exchange（示例） | 许多攻击使用旧身份验证，因此，如果用户的身份验证协议发生更改，则可能表示存在攻击。 |
| 新设备或位置 | 高 | Azure AD 登录日志 | 设备信息 = 设备 ID<br>－和－<br>浏览者<br>－和－<br>OS<br>－和－<br>符合/托管<br>－和－<br>目标 = 用户<br>－和－<br>位置 | 大多数管理员活动应来自有限位置的[特权访问设备](/security/compass/privileged-access-devices)。 因此，会对新的设备或位置发出警报。 |
| 审核警报设置发生更改。 | 高 | Azure AD 审核日志 | 服务 = PIM<br>－和－<br>类别 = 角色管理<br>－和－<br>活动 = 禁用 PIM 警报<br>－和－<br>状态 = 成功 | 如果对核心警报的更改属于意料之外，应发出警报。 |


## <a name="changes-by-privileged-accounts"></a>特权帐户进行的更改 

监视特权帐户完成和尝试的所有更改。 这使你能够确定每个特权帐户的正常活动，并对偏离预期的活动发出警报。 Azure AD 审核日志用于记录此类事件。 有关 Azure AD 审核日志的详细信息，请参阅 [Azure Active Directory 中的审核日志](../reports-monitoring/concept-audit-logs.md)。 

### <a name="azure-active-directory-domain-services"></a>Azure Active Directory 域服务 

在 Azure Active Directory 域服务中分配了权限的特权帐户可以执行 Azure AD DS 的任务，这些任务会影响使用 Azure AD 域服务的 Azure 托管虚拟机 (VM) 的安全状况。 在 VM 上启用安全审核并监视日志。 有关启用 Azure AD DS 审核的详细信息以及视为敏感特权访问的列表，请参阅以下资源。

* [为 Azure Active Directory 域服务启用安全审核](../../active-directory-domain-services/security-audit-events.md)

* [审核敏感特权的使用](/windows/security/threat-protection/auditing/audit-sensitive-privilege-use)

| 要监视的内容                                                         | 风险级别 | Where               | 筛选器/子筛选器                                                                                                                                                                                                                                                                                                                                                                                                                                                                    | 说明                                                                                                                                                                                                                                                                                                                                                                                                                             |
|-------------------------------------------------------------------------|------------|---------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 尝试和完成的更改                                  | 高       | Azure AD 审核日志 | 日期和时间<br>－和－<br>服务<br>－和－<br>活动的类别和名称（内容）<br>－和－<br>状态 = 成功或失败<br>－和－<br>目标<br>－和－<br>发起方/参与者（人员）                                                                                                                                                                                                                                                                                                | 对于任何计划外的更改，应立即发出警报。 应保留相关的日志，以协助进行任何调查。 应立即调查任何会降低租户安全性的租户级更改（指向基础结构文档的链接）。 例如：将帐户排除在 MFA 或条件访问之外。 [在发生对应用的任何添加或更改时](security-operations-applications.md)发出警报。 |
| **示例**<br>尝试或完成对高价值应用或服务的更改 | 高       | 审核日志           | 服务<br>－和－<br>活动的类别和名称                                                                                                                                                                                                                                                                                                                                                                                                                                | <li>日期和时间 <li>服务 <li>活动的类别和名称 <li>状态 = 成功或失败 <li>目标 <li>发起方/参与者（人员）                                                                                                                                                                                                                                                                                        |
| Azure AD DS 中的特权更改                                             | 高       | AD DS               | 查找事件 [4673](/windows/security/threat-protection/auditing/event-4673) | [为 Azure Active Directory 域服务启用安全审核](../../active-directory-domain-services/security-audit-events.md)<br>[审核敏感特权的使用](/windows/security/threat-protection/auditing/audit-sensitive-privilege-use)。 有关所有特权事件的列表，请参阅此文章。                                                                                                                            |


## <a name="changes-to-privileged-accounts"></a>对特权帐户的更改

调查对特权帐户的身份验证规则和特权所做的更改，尤其当此类更改会提供更大的特权或在 Azure AD 环境中执行任务的能力时。 

| 要监视的内容| 风险级别| Where| 筛选器/子筛选器| 说明 |
| - | - | - | - | - |
| 特权帐户创建。| 中等| Azure AD 审核日志| 服务 = 核心目录<br>－和－<br>类别 = 用户管理<br>－和－<br>活动类型 = 添加用户<br>－关联－<br>类别类型 = 角色管理<br>－和－<br>活动类型 = 将成员添加到角色<br>－和－<br>修改的属性 = Role.DisplayName| 监视任何特权帐户的创建。 寻找创建和删除帐户之间时间跨度较短的关联。 |
| 身份验证方法的更改。| 高| Azure AD 审核日志| 服务 = 身份验证方法<br>－和－<br>活动类型 = 用户注册的安全信息<br>－和－<br>类别 = 用户管理| 这可能表示攻击者为帐户添加了一种身份验证方法，以便能够持续访问。 |
| 在特权帐户权限发生更改时发出警报。| 高| Azure AD 审核日志| 类别 = 角色管理<br>－和－<br>活动类型 – 添加符合条件的成员（永久）<br>－和－<br>活动类型 – 添加符合条件的成员（符合条件）<br>－和－<br>状态 = 成功/失败<br>－和－<br>修改的属性 = Role.DisplayName| 这尤其适用于分配有未知或超出其正常职责范围的角色的帐户。 |
| 未使用的特权帐户。| 中等| Azure AD 访问评审| | 对非活动特权用户帐户进行每月评审。 |
| 免除条件访问的帐户| 高| Azure Monitor 日志<br>-或-<br>访问评审| 条件访问 - 见解和报告| 任何免除 CA 的帐户都很可能会绕过安全控制，更容易遭到入侵。 “破窗”账户可以免除。 请参阅本文后续部分中关于如何监视“破窗”帐户中的信息。|


有关如何监视条件访问策略异常的详细信息，请参阅[条件访问见解和报告](../conditional-access/howto-conditional-access-insights-reporting.md)。

有关发现未使用的特权帐户的详细信息，请参阅[在 Privileged Identity Management 中创建对 Azure AD 角色的访问评审](../privileged-identity-management/pim-create-azure-ad-roles-and-resource-roles-review.md)

 
## <a name="assignment-and-elevation"></a>特权分配和提升

设置永久预配有特权提升能力的特权帐户可能会增加攻击面和安全边界的风险。 请使用特权提升程序进行实时访问。 这种类型的系统使您能够分配特权角色的资格，管理员只有在执行需要这些特权的任务时，才会将其特权提升到这些角色。 使用特权提升程序使您能够监视特权帐户的特权提升和不使用情况。 

### <a name="establish-a-baseline"></a>建立基线

要监视异常，必须先创建基线。 确定以下内容

* 管理员帐户 

   * 你的特权帐户策略

   * 使用本地帐户管理本地资源。

   * 使用基于云的帐户管理基于云的资源。

   * 分离与监视本地和基于云的资源的管理权限的方法。

* 特权角色保护 

   * 针对具有管理权限的角色的保护策略。

   * 使用特权帐户的组织政策。

   * 维护永久特权与提供有时限、经批准的访问的策略及原则

 

以下概念和信息可帮助你确定策略。

* 实时管理原则 - 使用 Azure AD 日志来捕获信息，以执行环境中常见的管理任务。 确定完成任务通常需要的时间。 

* “恰到好处的管理”（Just Enough Administration）原则 - 确定执行管理任务所需的[最小特权角色](../roles/delegate-by-task.md)，这可能是一个自定义角色。 

* 建立特权提升策略–了解提升特权的类型以及每个任务所需的时间，创建可反映环境中提升特权使用情况的策略。 例如，定义一种策略，将全局管理员访问权限限制为 1 小时。

 建立基线并设置策略后，可以配置监视以检测策略之外的使用情况并发出警报。 

### <a name="discovery"></a>发现 (Discovery)

建议你特别注意并调查特权分配和提升的更改。 

### <a name="things-to-monitor"></a>要监视的内容

可以使用 Azure AD 审核日志和 Azure Monitor 日志监视特权帐户更改。 具体而言，建议在监视过程中包括以下内容。 

| 要监视的内容| 风险级别| Where| 筛选器/子筛选器| 说明 |
| - | - | - | - | - |
| 添加到符合条件的特权角色。| 高| Azure AD 审核日志| 服务 = PIM<br>－和－<br>类别 = 角色管理<br>－和－<br>活动类型 – 完成将成员添加到角色（符合条件）<br>－和－<br>状态 = 成功/失败<br>－和－<br>修改的属性 = Role.DisplayName| 任何符合角色条件的帐户现在都被授予特权访问权限。 如果分配超出预期，或分配到不属于帐户持有人职责的角色，则进行调查。 |
| 在 PIM 之外分配角色。| 高| Azure AD 审核日志| 服务 = PIM<br>－和－<br>类别 = 角色管理<br>－和－<br>活动类型 = 将成员添加到角色（永久）<br>－和－<br>状态 = 成功/失败<br>－和－<br>修改的属性 = Role.DisplayName| 这些都应密切监视并发出警报。 如果可能，不应在 PIM 之外为用户分配角色。 |
| 提升| 中等| Azure AD 审核日志| 服务 = PIM<br>－和－<br>类别 = 角色管理<br>－和－<br>活动类型 – 完成将成员添加到角色（PIM 激活）<br>－和－<br>状态 = 成功/失败<br>－和－<br>修改的属性 = Role.DisplayName| 提升特权后，特权帐户现在就可以做出可能影响你的租户安全的更改。 所有特权提升都应进行记录，如果发生在该用户的标准模式之外，则应在出现计划外情况时发出警报并进行调查。 |
| 批准和拒绝特权提升| 低| Azure AD 审核日志| 服务 = 访问评审<br>－和－<br>类别 = UserManagement<br>－和－<br>活动类型 = 请求获批/被拒<br>－和－<br>启动的执行组件 = UPN| 监视所有特权提升，因为它可以清楚地显示出攻击的时间线。 |
| 更改 PIM 设置| 高| Azure AD 审核日志| 服务 = PIM<br>－和－<br>类别 = 角色管理<br>－和－<br>活动类型 = 在 PIM 中更新角色设置<br>－和－<br>状态原因 = 禁用激活时进行 MFA（示例）| 这些操作可能会降低 PIM 提升的安全性，使攻击者更容易获取特权帐户。 |
| 在 SAW/ PAW 上没有出现特权提升| 高| Azure AD 登录日志| 设备 ID<br>－和－<br>浏览者<br>－和－<br>OS<br>－和－<br>符合/托管<br>关联：<br>服务 = PIM<br>－和－<br>类别 = 角色管理<br>－和－<br>活动类型 – 完成将成员添加到角色（PIM 激活）<br>－和－<br>状态 = 成功/失败<br>－和－<br>修改的属性 = Role.DisplayName| 如果配置此项，应立即调查任何在非 PAW/SAW 设备上进行提升的尝试，因为这可能表示有攻击者在试图使用该帐户。 |
| 提升特权以管理所有 Azure 订阅| 高| Azure Monitor| 活动日志选项卡 <br>目录活动选项卡 <br> 操作名称 = 将调用方分配到用户访问管理员 <br> －和－ <br> 事件类别 = 管理 <br> －和－<br>状态 = 成功、启动、失败<br>－和－<br>事件发起者| 如果不是计划中的更改，应立即进行调查。 此设置可能会允许攻击者访问你的环境中的 Azure 订阅。 |


有关管理特权提升的详细信息，请参阅[提升访问权限以管理所有 Azure 订阅和管理组](../../role-based-access-control/elevate-access-global-admin.md)。 有关使用 Azure AD 日志中的信息来监视特权提升的详细信息，请参阅 Azure Monitor 文档中的 [Azure 活动日志](../../azure-monitor/essentials/activity-log.md)。

有关为 Azure 角色配置警报的详细信息，请参阅[在 Privileged Identity Management 中为 Azure 资源角色配置安全警报](../privileged-identity-management/pim-resource-roles-configure-alerts.md)。 

 ## <a name="next-steps"></a>后续步骤

请参阅以下安全操作指南文章：

[Azure AD 安全操作概述](security-operations-introduction.md)

[用户帐户的安全操作](security-operations-user-accounts.md)

[特权帐户的安全操作](security-operations-privileged-accounts.md)

[Privileged Identity Management 的安全操作](security-operations-privileged-identity-management.md)

[应用程序的安全操作](security-operations-applications.md)

[设备的安全操作](security-operations-devices.md)

 
[基础结构的安全操作](security-operations-infrastructure.md)