---
title: Privileged Identity Management 的 Azure Active Directory 安全操作
description: 用于建立基线和使用 Azure Active Directory Privileged Identity Management (PIM) 监视 PIM 所控制帐户的潜在问题并发出警报的指南。
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 07/15/2021
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: f17ff93dde6c4bbb7df57d177bc7d1e3d746b347
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124814621"
---
# <a name="azure-active-directory-security-operations-for-privileged-identity-management-pim"></a>Privileged Identity Management (PIM) 的 Azure Active Directory 安全操作

业务资产的安全性取决于管理 IT 系统的特权帐户的完整性。 网络攻击者会针对管理员帐户和其他特权访问权限帐户发起凭据盗窃攻击，以尝试获取对敏感数据的访问权限。

就云服务来说，预防此类攻击并对其做出响应是云服务提供商和客户的共同责任。 

传统上，组织安全侧重于将网络的入口点和出口点作为安全外围。 但是，SaaS 应用程序和个人设备降低了这种方法的效率。 在 Azure Active Directory (Azure AD) 中，我们会将网络安全外围替代为组织标识层中的身份验证。 为用户分配特权管理角色后，必须在本地、云和混合环境中保护其访问权限 

你完全负责本地 IT 环境的所有安全层。 使用 Azure 云服务时，预防和响应是 Microsoft 作为云服务提供商和你作为客户的共同责任。 

* 有关共担责任模型的详细信息，请访问[云中共担责任](../../security/fundamentals/shared-responsibility.md)。

* 有关保护特权用户访问权限的详细信息，请参阅[确保 Azure AD 中混合部署和云部署的特权访问安全性](../roles/security-planning.md)

* 如需特权标识的各种视频、操作指南和重要概念的内容，请访问 [Privileged Identity Management 文档](../privileged-identity-management/index.yml)。 

Privileged Identity Management (PIM) 是一种 Azure AD 服务，你可以通过该服务管理、控制和监视对组织中重要资源的访问。 这些资源包括 Azure AD、Azure 和其他 Microsoft Online Services（例如 Microsoft 365 或 Microsoft Intune）中的资源。 你可以使用 PIM 来减轻以下风险：

* 确定和最大限度地减少有权访问安全信息和资源的人数。

* 检测对敏感资源的过多、不必要或误用的访问权限。

* 降低恶意参与者获得受保护信息或资源的访问权限的几率。

* 降低未经授权的用户意外影响敏感资源的可能性。

本文提供有关设置基线、审核特权帐户的登录和使用情况，以及可用来协助维护特权帐户完整性的资源和审核日志的指导。 

## <a name="where-to-look"></a>查看位置

用于调查和监视的日志文件包括： 

* [Azure AD 审核日志](../reports-monitoring/concept-audit-logs.md)

* [登录日志](../reports-monitoring/concept-all-sign-ins.md)

* [Microsoft 365 审核日志](/microsoft-365/compliance/auditing-solutions-overview) 

* [Azure Key Vault 日志](../../key-vault/general/logging.md?tabs=Vault)

在 Azure 门户中，可以查看 Azure AD 审核日志，并将其作为逗号分隔值 (CSV) 或 JavaScript 对象表示法 (JSON) 文件下载。 Azure 门户提供多种方法,将 Azure AD 日志与其他工具相集成，以便更好地自动执行监视和警报：

* **Azure Sentinel** - 通过提供安全信息和事件管理 (SIEM) 功能，实现企业级智能安全分析。 

* **Azure Monitor** – 实现对各种情况的自动监视和警报。 可以创建或使用工作簿来合并来自不同源的数据。

* 与 SIEM 集成的 [Azure 事件中心](../../event-hubs/event-hubs-about.md) - [ 通过 Azure 事件中心集成可将 Azure AD 日志集成到其他 SIEM](../reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md)，例如 Splunk、ArcSight、QRadar 和 Sumo Logic。 

* [Microsoft Cloud App Security](/cloud-app-security/what-is-cloud-app-security) (MCAS) – 支持发现和管理应用、跨应用和资源进行治理，以及检查云应用的符合性。 

本文的其余部分提供了有关设置基线以监视和发出警报的建议，以及如何使用层模型进行组织。 表格后面列出了预先生成的解决方案的链接。 你还可以使用前面的工具生成警报。 具体内容分为以下 PIM 主题领域：

* 基线

* Azure AD 角色分配 

* Azure AD 角色警报设置

* Azure 资源角色分配

* Azure 资源的访问权限管理

* 提升访问权限来管理 Azure 订阅

## <a name="baselines"></a>基线

下面是建议的基线设置：

| 要监视的内容| 风险级别| 建议| 角色| 说明 |
| - |- |- |- |- |
| Azure AD 角色分配| 高| <li>需要激活的理由。<li>需要批准才能激活。<li>设置两级审批者进程。<li>激活需要 Azure Active Directory 多重身份验证 (MFA)。<li>将最长特权提升持续时间设置为 8 小时。| <li>特权角色管理<li>全局管理员| 特权角色管理员可以在其 Azure AD 组织中自定义 PIM，包括更改激活合格角色分配的用户的体验。 |
| Azure 资源角色配置| 高| <li>需要激活的理由。<li>需要批准才能激活。<li>设置两级审批者进程。<li>激活需要 Azure MFA。<li>将最长特权提升持续时间设置为 8 小时。| <li>所有者<li>资源管理员<li>用户访问权限 <li>管理员<li>全局管理员<li>安全管理员| 如果不是计划内更改，请立即进行调查。 此设置可使攻击者访问你的环境中的 Azure 订阅。 |


## <a name="azure-ad-roles-assignment"></a>Azure AD 角色分配

特权角色管理员可以在其 Azure AD 组织中自定义 PIM。 这包括更改激活合格角色分配的用户的体验，如下所示：

* 防止不良参与者删除 Azure MFA 要求，以激活特权访问。

* 防止恶意用户绕过对激活特权访问的论证和批准。

| 要监视的内容| 风险级别| Where| 筛选器/子筛选器| 说明 |
| - |- |- |- |- |
| 在对特权帐户权限添加更改时发出警报| 高| Azure AD 审核日志| 类别 = 角色管理<br>－和－<br>活动类型 – 添加符合条件的成员（永久） <br>－和－<br>活动类型 – 添加符合条件的成员（符合条件） <br>－和－<br>状态 = 成功/失败<br>－和－<br>修改的属性 = Role.DisplayName| 监视特权角色管理员和全局管理员的任何更改并始终发出警报。 <li>这可能表示攻击者试图获取修改角色分配设置的权限<li> 如果没有定义的阈值，则在 60 分钟内向用户发出 4 次警报，并在 60 分钟内向特权帐户发出警报 2 次。 |
| 对特权帐户权限进行批量删除更改时发出警报| 高| Azure AD 审核日志| 类别 = 角色管理<br>－和－<br>活动类型 – 删除符合条件的成员（永久） <br>－和－<br>活动类型 – 删除符合条件的成员（符合条件） <br>－和－<br>状态 = 成功/失败<br>－和－<br>修改的属性 = Role.DisplayName| 如果不是计划内更改，请立即进行调查。 此设置可使攻击者访问你的环境中的 Azure 订阅。 |
| 更改 PIM 设置| 高| Azure AD 审核日志| 服务 = PIM<br>－和－<br>类别 = 角色管理<br>－和－<br>活动类型 = 在 PIM 中更新角色设置<br>－和－<br>状态原因 = 禁用激活时进行 MFA（示例）| 监视特权角色管理员和全局管理员的任何更改并始终发出警报。 <li>这表明攻击者已经获得了修改角色分配设置的权限<li>其中一项操作可能会降低 PIM 特权提升的安全性，使攻击者更容易获取特权帐户。 |
| 批准和拒绝特权提升| 高| Azure AD 审核日志| 服务 = 访问评审<br>－和－<br>类别 = UserManagement<br>－和－<br>活动类型 = 请求获批/被拒<br>－和－<br>发起的参与者 = UPN| 应监视所有特权提升。 记录所有特权提升，因为这样可以清楚地显示出攻击的时间线。 |
| 警报设置更改为已禁用。| 高| Azure AD 审核日志| 服务 = PIM<br>－和－<br>类别 = 角色管理<br>－和－<br>活动类型 = 禁用 PIM 警报<br>－和－<br>状态 = 成功/失败| 始终发出警报。 <li>帮助检测不良参与者删除与 Azure MFA 要求关联的警报以激活特权访问的行为。<li>帮助检测可疑或不安全的活动。 |


有关确定 Azure AD 审核日志中的角色设置更改的详细信息，请参阅[在 Privileged Identity Management 中查看 Azure AD 角色的审核历史](../privileged-identity-management/pim-how-to-use-audit-log.md)。 

## <a name="azure-resource-role-assignment"></a>Azure 资源角色分配

通过监视 Azure 资源角色分配，可以了解资源角色的活动和激活情况。 它们可能被误用，让资源产生攻击面。 监视此类活动时，尝试检测：

* 特定资源中的查询角色分配

* 所有子资源的角色分配

* 所有活动的和符合条件的角色分配更改

| 要监视的内容| 风险级别| Where| 筛选器/子筛选器| 说明 |
| - |- |- |- |- |
| 特权帐户活动的审核警报资源审核日志| 高| 在 PIM 中的“Azure 资源”中选择“资源审核”| 操作：已将符合条件的成员添加到 PIM 中的角色（时间限制） <br>－和－<br>主目标 <br>－和－<br>类型用户<br>－和－<br>状态 = 已成功<br>| 始终发出警报。 帮助检测添加符合条件的角色以管理 Azure 中所有资源的不良参与者。 |
| 禁用警报的审核警报资源审核| 中等| 在 PIM 中的“Azure 资源”中选择“资源审核”| 操作：禁用警报<br>－和－<br>主目标：向资源分配的所有者过多<br>－和－<br>状态 = 已成功| 帮助从“警报”窗格检测不良参与者禁用警报（此行为可绕过正在调查的恶意活动）的行为 |
| 禁用警报的审核警报资源审核| 中等| 在 PIM 中的“Azure 资源”中选择“资源审核”| 操作：禁用警报<br>－和－<br>主目标：向资源分配的永久所有者过多<br>－和－<br>状态 = 已成功| 从“警报”窗格防止不良参与者禁用警报（此行为可绕过正在调查的恶意活动） |
| 禁用警报的审核警报资源审核| 中等| 在 PIM 中的“Azure 资源”中选择“资源审核”| 操作：禁用警报<br>－和－<br>已创建“主目标重复”角色<br>－和－<br>状态 = 已成功| 从“警报”窗格防止不良参与者禁用警报（此行为可绕过正在调查的恶意活动） |


有关配置警报和审核 Azure 资源角色的信息，请参阅：

* [在 Privileged Identity Management 中为 Azure 资源角色配置安全警报](../privileged-identity-management/pim-resource-roles-configure-alerts.md)

* [在 Privileged Identity Management (PIM) 中查看 Azure 资源角色的审核报告](../privileged-identity-management/azure-pim-resource-rbac.md)

## <a name="access-management-for-azure-resources-and-subscriptions"></a>Azure 资源和订阅的访问管理

默认情况下，分配到“所有者”或“用户访问管理员”订阅角色的用户或组成员以及在 Azure AD 中启用订阅管理的 Azure AD 全局管理员具有资源管理员权限。 这些管理员可以对 Azure 资源使用 Privileged Identity Management (PIM) 来分配角色、配置角色设置，以及审查访问权限。 

具有资源管理员权限的用户可以管理资源的 PIM。 这种情况所带来的必须监视和缓解的风险在于，该功能可用于允许不良参与者获得 Azure 订阅资源（例如虚拟机或存储帐户）的特权访问权限。

| 要监视的内容| 风险级别| Where| 筛选器/子筛选器| 说明 |
| - |- |- |- |- |
| 提升| 高| “管理”中“属性”下的 Azure AD| 定期查看设置。<br>Azure 资源的访问权限管理| 全局管理员可以通过为 Azure 资源启用访问管理来提升权限。<br>确认不良参与者未获得在与 Active Directory 关联的所有 Azure 订阅和管理组中分配角色的权限。 |


有关详细信息，请参阅[在 Privileged Identity Management 中分配 Azure 资源角色](../privileged-identity-management/pim-resource-roles-assign-roles.md)

## <a name="next-steps"></a>后续步骤
请参阅以下安全操作指南文章：

[Azure AD 安全操作概述](security-operations-introduction.md)

[用户帐户的安全操作](security-operations-user-accounts.md)

[特权帐户的安全操作](security-operations-privileged-accounts.md)

[Privileged Identity Management 的安全操作](security-operations-privileged-identity-management.md)

[应用程序的安全操作](security-operations-applications.md)

[设备的安全操作](security-operations-devices.md)
 
[基础结构的安全操作](security-operations-infrastructure.md)