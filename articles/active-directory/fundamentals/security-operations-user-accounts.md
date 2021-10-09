---
title: Azure Active Directory 用户帐户安全操作
description: 有关建立用户帐户基线以及如何对其监视和警报潜在安全问题的指南。
services: active-directory
author: BarbaraSelden
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 07/15/2021
ms.author: baselden
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: f78765e7b0d26b2767dabf4b1fdc4ccdd6873123
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124803604"
---
# <a name="azure-active-directory-security-operations-for-user-accounts"></a>Azure Active Directory 用户帐户安全操作

用户标识是保护组织和数据的最重要方面之一。 本文提供有关监视帐户创建、删除和帐户使用的指南。 第一部分介绍如何监视帐户的异常创建和删除。 第二部分介绍如何监视帐户异常使用情况。 

如果你尚未阅读 [Azure Active Directory (Azure AD) 安全操作概述](security-operations-introduction.md)，建议先阅读再继续操作。

本文介绍一般用户帐户。 有关特权帐户，请参阅“安全操作–特权帐户”。

## <a name="define-a-baseline"></a>定义基线

若要发现异常行为，首先必须定义正常和预期的行为。 为组织定义预期行为有助于确定发生意外行为的情况。 此外，此定义还有助于减少监视和警报时误报的噪音级别。 

定义预期行为后，则可以执行基线监视来验证期望。 利用该信息，可以监视所有超出定义容差范围的日志。 

对于非按照正常进程创建的帐户，使用 Azure AD 审核日志、Azure AD 登录日志和目录属性作为数据源。 下面是一些建议，其旨在帮助你考虑和定义组织的正常情况。

* 用户帐户创建 – 评估以下各项：

   * 用于创建和管理用户帐户的工具和进程的策略和原则。 例如，是否存在适用于用户帐户属性的标准属性和格式。 

  * 已批准的帐户创建源。 例如，源自于 Active Directory (AD)、Azure Active Directory 或 HR 系统（如 Workday）。

  * 非使用已批准源创建的帐户的警报策略。 你的组织是否有受控制的组织列表？

  * 为并非使用权利管理或其他正常进程创建的帐户预配来宾帐户和警报参数。

  * 并非由批准的用户管理员创建、修改或禁用的帐户的策略和警报参数。

  * 缺少标准属性（如员工 ID 或未遵循组织命名约定）的帐户的监视和警报策略。

  * 帐户删除和保留的策略、原则和进程。

* 本地用户帐户 –对于与 Azure AD Connect 同步的帐户，请评估以下各项：

  * 同步范围内的林、域和组织单位 (OU)。 批准的管理员是谁？哪些人可以更改这些设置以及检查范围的频率是多久？

  * 同步的帐户类型。 例如，用户帐户和/或服务帐户。 

  * 创建特权本地帐户的进程，以及控制此类帐户同步的方式。

  * 创建本地用户帐户的进程，以及管理此类帐户同步的方式。

有关保护和监视本地帐户的详细信息，请参阅[保护 Microsoft 365 免受本地攻击](protect-m365-from-on-premises-attacks.md)。

* 云用户帐户 – 评估以下各项：

  * 直接在 Azure AD 中预配和管理云帐户的进程。

  * 确定作为 Azure AD 云帐户预配的用户类型的进程。 例如，是只允许特权帐户，还是也允许用户帐户？

  * 创建和维护受信任的个人列表的进程，以及创建和管理云用户帐户所需的进程。

  * 创建和维护非批准云帐户的警报策略的进程。 

## <a name="where-to-look"></a>查看位置

用于调查和监视的日志文件包括： 

* [Azure AD 审核日志](../reports-monitoring/concept-audit-logs.md)

* [登录日志](../reports-monitoring/concept-all-sign-ins.md)

* [Microsoft 365 审核日志](/microsoft-365/compliance/auditing-solutions-overview) 

* [Azure Key Vault 日志](../../key-vault/general/logging.md?tabs=Vault)

* [风险用户日志](../identity-protection/howto-identity-protection-investigate-risk.md)

* [UserRiskEvents 日志](../identity-protection/howto-identity-protection-investigate-risk.md)

在 Azure 门户中，可以查看 Azure AD 审核日志，并将下载为逗号分隔值 (CSV) 或 JavaScript 对象表示法 (JSON) 文件。 Azure 门户提供多种方法,将 Azure AD 日志与其他工具相集成，以便更好地自动执行监视和警报：

* [Azure Sentinel](../../sentinel/overview.md) - 通过提供安全信息和事件管理 (SIEM) 功能，实现企业级智能安全分析。 

* [Azure Monitor](../../azure-monitor/overview.md) – 实现对各种情况的自动监视和警报。 可以创建或使用工作簿来合并来自不同源的数据。

* 与 SIEM 集成的 [Azure 事件中心](../../event-hubs/event-hubs-about.md)- 通过 Azure 事件中心集成[可将 Azure AD 日志集成到其他 SIEM](../reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md)，例如 Splunk、ArcSight、QRadar 和 Sumo Logic。

* [Microsoft Cloud App Security](/cloud-app-security/what-is-cloud-app-security) (MCAS) – 支持发现和管理应用、跨应用和资源进行治理以及检查云应用合规性。 

大部分你将监视和发出警报的内容由条件访问策略决定。 可以使用[条件访问见解和报告工作簿](../conditional-access/howto-conditional-access-insights-reporting.md)来检查一个或多个条件访问策略对登录的影响以及包括设备状态在内的策略结果。 可通过此工作簿查看影响摘要并确定特定时间段的影响。 还可以使用此工作簿来调查特定用户的登录情况。 

 本文接下来将按威胁类型介绍应该监视和发出相关警报的对象。 如果有特定预生成解决方案，我们会链接到这些解决方案，或在表格之后提供示例。 除此之外，可以使用前面的工具来生成警报。 

## <a name="account-creation"></a>帐户创建

异常帐户创建可能指示存在安全问题。 存续期较短的帐户、未遵循命名标准的帐户以及并非按正常进程创建的帐户均应接受调查。

### <a name="short-lived-accounts"></a>存续期较短的帐户

应在 Azure AD 中监视非按正常标识管理进程进行的帐户创建和删除。 存续期较短的帐户是指在短时间范围内创建和删除的帐户。 这类帐户的创建和快速删除可能意味着，恶意行动者正在试图通过创建帐户、使用帐户，然后删除帐户的方式来避免接受检测。

存续期较短的帐户模式可能指示，未经批准的人员或进程可能有权创建和删除不符合已确定的进程和策略的帐户。 这类行为将从目录中删除可见标记。 

如果未能快速发现创建和删除帐户的数据痕迹，则调查事件所需的信息可能已不再存在。 例如，帐户可能被删除，然后又从回收站被清除。 审核日志将保留 30 天。 不过，可以将日志导出到 Azure Monitor 或安全信息和事件管理 (SIEM) 解决方案，以便长期保留。 

| 要监视的内容                                                  | 风险级别 | Where               | 筛选器/子筛选器                                                                                                                                         | 说明                                                                                                                               |
|------------------------------------------------------------------|------------|---------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------|
| 短时间范围内的帐户创建和删除事件。  | 高       | Azure AD 审核日志 | 活动：添加用户<br>状态 = 成功<br>－和－<br>活动：删除用户<br>状态 = 成功<br>                                                          | 搜索用户主体名称 (UPN) 事件。 查找在 24小 时内创建又删除的帐户。                          |
| 由未经批准的用户或进程创建和删除的帐户。 | 中等     | Azure AD 审核日志 | 发起者（行动者）– 用户主体名称<br>－和－<br>活动：添加用户<br>状态 = 成功<br>和/或<br>活动：删除用户<br>状态 = 成功      | 如果行动者不是已批准的用户，请配置以发送警报。                                                                    |
| 来自非批准源的帐户。                              | 中等     | Azure AD 审核日志 | 活动：添加用户<br>状态 = 成功<br>目标 = 用户主体名称                                                                                 | 如果条目并非来自已批准的域，或是已知被阻止的域，请配置以发送警报。                               |
| 分配给特权角色的帐户。                          | 高       | Azure AD 审核日志 | 活动：添加用户<br>状态 = 成功<br>－和－<br>活动：删除用户<br>状态 = 成功<br>－和－<br>活动 = 将成员添加到角色<br>状态 = 成功 | 如果帐户被分配到 Azure AD 角色、Azure 角色或特权组成员身份，请发送警报并优先进行调查。 |

对于特权和非特权帐户，均应进行监视和发送警报。 不过，由于特权帐户具有管理权限，因此它们在监视、警报和响应进程中应当优先级更高。 

### <a name="accounts-not-following-naming-policies"></a>未遵循命名策略的帐户

未遵循命名策略的用户帐户创建时可能未遵循组织策略。 

最佳做法是为用户对象制定一项命名策略。 命名策略可方便管理，并有助于确保一致性。 此外，策略还有助于发现用户创建未遵循批准的进程的情况。 恶意行动者可能不知道你的命名标准，这样可以更轻松地检测未遵循组织进程预配的帐户。

组织往往采用特定的格式和属性来创建用户和/或特权帐户。 例如：

* 管理员帐户 UPN = ADM_firstname.lastname@tenant.onmicrosoft.com

* 用户帐户 UPN = Firstname.Lastname@contoso.com

此外，用户帐户通常还具有标识真实用户的属性。 例如，EMPID = XXXNNN。 下面是一些建议，这些建议可帮助你考虑和定义组织的正常情况，以及在为未遵循组织命名约定的帐户定义日志条目基线时要考虑的事项：

* 未遵循命名约定的帐户。 例如，`nnnnnnn@contoso.com` 与 `firstname.lastname@contoso.com`。 

* 未填充标准属性或格式不正确的帐户。 例如，没有有效的员工 ID。

| 要监视的内容| 风险级别| Where| 筛选器/子筛选器| 说明 |
| - | - | - | - | - |
| 未定义所需属性的用户帐户。| 低| Azure AD 审核日志| 活动：添加用户<br>状态 = 成功| 查找标准属性为 null 或格式不正确的帐户。 例如 EmployeeID |
| 使用错误命名格式创建的用户帐户。| 低| Azure AD 审核日志| 活动：添加用户<br>状态 = 成功| 查找 UPN 未遵循命名策略的帐户。 |
| 未遵循命名策略的特权帐户。| 高| Azure 订阅| [使用 Azure 门户列出 Azure 角色分配 - Azure RBAC](../../role-based-access-control/role-assignments-list-portal.md)| 列出订阅的角色分配，并在登录名称不符合组织规定的格式时发出警报。 例如，以 ADM_ 作为前缀。 |
| 未遵循命名策略的特权帐户。| 高| Azure AD 目录| [列出 Azure AD 角色分配](../roles/view-assignments.md)| 列出 Azure AD 角色的角色分配，并在 UPN 不符合组织规定格式时发出警报。 例如，以 ADM_ 作为前缀。 |



有关分析的详细信息，请参阅：

* 对于 Azure AD 审核日志 - [分析 Azure Monitor 日志中的文本数据](../../azure-monitor/logs/parse-text.md)

* 对于 Azure 订阅 - [使用 Azure PowerShell 列出 Azure 角色分配](../../role-based-access-control/role-assignments-list-powershell.md)

* 对于 Azure Active Directory - [列出 Azure AD 角色分配](../roles/view-assignments.md) 

### <a name="accounts-created-outside-normal-processes"></a>未按照正常进程创建的帐户

按照标准过程创建用户和特权帐户非常重要，这样即可安全地控制标识的生命周期。 如果未遵循建立的进程来预配和撤销用户，可能会带来安全风险。 此外，不遵循建立的进程外操作还可能导致标识管理问题。 潜在风险包括：

* 可能无法按照组织策略管理用户帐户和特权帐户。 这样可能导致帐户管理不当，攻击面扩大。

* 由此，使得恶意行动者出于恶意目的创建帐户的情况更难被检测到。 对于未遵循建立的程序创建的有效帐户，则更难检测到出于恶意目的而创建帐户或修改权限的情况。 

建议必须根据组织策略创建用户帐户和特权帐户。 例如，应使用正确的命名标准、组织信息以及在适当的标识治理范围内来创建帐户。 组织应严格控制谁有权创建、管理和删除标识。 应当严格管理用于创建这些帐户的角色，并且特权仅在遵循已建立的工作流进行审批并获取这些权限后才有效。

| 要监视的内容| 风险级别| Where| 筛选器/子筛选器| 说明 |
| - | - | - | - | - |
| 由未经批准的用户或进程创建或删除的用户帐户。| 中等| Azure AD 审核日志| 活动：添加用户<br>状态 = 成功<br>和/或<br>活动：删除用户<br>状态 = 成功<br>－和－<br>发起者（行动者）– 用户主体名称| 对于由未经批准的用户或进程创建的帐户发出警报。 使用提升特权创建的帐户优先。 |
| 基于非批准源创建或删除的用户帐户。| 中等| Azure AD 审核日志| 活动：添加用户<br>状态 = 成功<br>-或-<br>活动：删除用户<br>状态 = 成功<br>－和－<br>目标 = 用户主体名称| 当域为非批准的域或为已知被阻止的域时发出警报。 |


## <a name="unusual-sign-ins"></a>异常登录

看到用户身份验证失败是正常情况。 但看到失败模式或故障块可能指示用户标识发生状况。 例如，密码喷射或暴力攻击的情况，或者用户帐户遭到入侵时。 监视模式并在模式出现时发出警报至关重要。 这样有助于确保用户和组织的数据安全。 

成功似乎是一切都很好。 但这可能意味着，恶意行动者已成功访问服务。 监视成功登录可帮助检测正在获取访问权限，但并非应具有该访问权限的用户帐户。 用户身份验证成功是 Azure AD 登录日志中的正常条目。 建议进行监视并发出警报，以检测出现模式的情况。 这样有助于确保用户帐户和组织的数据安全。 


在设计和操作日志监视和警报策略时，请考虑可通过 Azure 门户获取的工具。 通过标识保护，可自动检测、保护和修正基于标识的风险。 标识保护使用智能式机器学习和启发式系统来检测风险，并为用户和登录分配风险分数。客户可以根据风险级别来配置何时允许或拒绝访问的策略，以允许用户安全地自动进行风险补救。 以下标识保护风险检测会立即通知风险级别：

| 要监视的内容 | 风险级别 | Where | 筛选器/子筛选器 | 说明 |
| - | - | - | - | - |
| 用户风险检测的凭据泄露| 高| Azure AD 风险检测日志| UX：凭据泄漏 <br><br>API：请参阅 [riskDetection 资源类型 - Microsoft Graph beta 版本](/graph/api/resources/riskdetection?view=graph-rest-beta&preserve-view=true)| 请参阅[什么是风险？Azure AD 标识保护](../identity-protection/concept-identity-protection-risks.md) |
| Azure AD 威胁情报用户风险检测| 高| Azure AD 风险检测日志| UX：Azure AD 威胁情报 <br><br>API：请参阅 [riskDetection 资源类型 - Microsoft Graph beta 版本](/graph/api/resources/riskdetection?view=graph-rest-beta&preserve-view=true)| 请参阅[什么是风险？Azure AD 标识保护](../identity-protection/concept-identity-protection-risks.md) |
| 匿名 IP 地址登录风险检测| 多种多样| Azure AD 风险检测日志| UX：匿名 IP 地址 <br><br>API：请参阅 [riskDetection 资源类型 - Microsoft Graph beta 版本](/graph/api/resources/riskdetection?view=graph-rest-beta&preserve-view=true)| 请参阅[什么是风险？Azure AD 标识保护](../identity-protection/concept-identity-protection-risks.md) |
| 异常位置登录风险检测| 多种多样| Azure AD 风险检测日志| UX：异常位置登录 <br><br>API：请参阅 [riskDetection 资源类型 - Microsoft Graph beta 版本](/graph/api/resources/riskdetection?view=graph-rest-beta&preserve-view=true)| 请参阅[什么是风险？Azure AD 标识保护](../identity-protection/concept-identity-protection-risks.md) |
| 异常令牌| 多种多样| Azure AD 风险检测日志| UX：异常令牌 <br><br>API：请参阅 [riskDetection 资源类型 - Microsoft Graph beta 版本](/graph/api/resources/riskdetection?view=graph-rest-beta&preserve-view=true)| 请参阅[什么是风险？Azure AD 标识保护](../identity-protection/concept-identity-protection-risks.md) |
| 受恶意软件感染的 IP 地址登录风险检测| 多种多样| Azure AD 风险检测日志| UX：受恶意软件感染的 IP 地址 <br><br>API：请参阅 [riskDetection 资源类型 - Microsoft Graph beta 版本](/graph/api/resources/riskdetection?view=graph-rest-beta&preserve-view=true)| 请参阅[什么是风险？Azure AD 标识保护](../identity-protection/concept-identity-protection-risks.md) |
| 可疑浏览器登录风险检测| 多种多样| Azure AD 风险检测日志| UX：可疑浏览器 <br><br>API：请参阅 [riskDetection 资源类型 - Microsoft Graph beta 版本](/graph/api/resources/riskdetection?view=graph-rest-beta&preserve-view=true)| 请参阅[什么是风险？Azure AD 标识保护](../identity-protection/concept-identity-protection-risks.md) |
| 不熟悉的登录属性登录风险检测| 多种多样| Azure AD 风险检测日志| UX：不熟悉的登录属性 <br><br>API：请参阅 [riskDetection 资源类型 - Microsoft Graph beta 版本](/graph/api/resources/riskdetection?view=graph-rest-beta&preserve-view=true)| 请参阅[什么是风险？Azure AD 标识保护](../identity-protection/concept-identity-protection-risks.md) |
| 恶意 IP 地址登录风险检测| 多种多样| Azure AD 风险检测日志| UX：恶意 IP 地址<br><br>API：请参阅 [riskDetection 资源类型 - Microsoft Graph beta 版本](/graph/api/resources/riskdetection?view=graph-rest-beta&preserve-view=true)| 请参阅[什么是风险？Azure AD 标识保护](../identity-protection/concept-identity-protection-risks.md) |
| 可疑收件箱操作规则登录风险检测| 多种多样| Azure AD 风险检测日志| UX：可疑收件箱操作规则<br><br>API：请参阅 [riskDetection 资源类型 - Microsoft Graph beta 版本](/graph/api/resources/riskdetection?view=graph-rest-beta&preserve-view=true)| 请参阅[什么是风险？Azure AD 标识保护](../identity-protection/concept-identity-protection-risks.md) |
| 密码喷射登录风险检测| 高| Azure AD 风险检测日志| UX：密码喷射<br><br>API：请参阅 [riskDetection 资源类型 - Microsoft Graph beta 版本](/graph/api/resources/riskdetection?view=graph-rest-beta&preserve-view=true)| 请参阅[什么是风险？Azure AD 标识保护](../identity-protection/concept-identity-protection-risks.md) |
| 不可能旅行登录风险检测| 多种多样| Azure AD 风险检测日志| UX：不可能旅行<br><br>API：请参阅 [riskDetection 资源类型 - Microsoft Graph beta 版本](/graph/api/resources/riskdetection?view=graph-rest-beta&preserve-view=true)| 请参阅[什么是风险？Azure AD 标识保护](../identity-protection/concept-identity-protection-risks.md) |
| 新国家/地区登录风险检测| 多种多样| Azure AD 风险检测日志| UX：新国家/地区<br><br>API：请参阅 [riskDetection 资源类型 - Microsoft Graph beta 版本](/graph/api/resources/riskdetection?view=graph-rest-beta&preserve-view=true)| 请参阅[什么是风险？Azure AD 标识保护](../identity-protection/concept-identity-protection-risks.md) |
| 来自匿名 IP 地址的活动登录风险检测| 多种多样| Azure AD 风险检测日志| UX：来自匿名 IP 地址的活动<br><br>API：请参阅 [riskDetection 资源类型 - Microsoft Graph beta 版本](/graph/api/resources/riskdetection?view=graph-rest-beta&preserve-view=true)| 请参阅[什么是风险？Azure AD 标识保护](../identity-protection/concept-identity-protection-risks.md) |
| 可疑收件箱转发登录风险检测| 多种多样| Azure AD 风险检测日志| UX：可疑收件箱转发<br><br>API：请参阅 [riskDetection 资源类型 - Microsoft Graph beta 版本](/graph/api/resources/riskdetection?view=graph-rest-beta&preserve-view=true)| 请参阅[什么是风险？Azure AD 标识保护](../identity-protection/concept-identity-protection-risks.md) |
| Azure AD 威胁情报登录风险检测| 高| Azure AD 风险检测日志| UX：Azure AD 威胁情报<br>API：请参阅 [riskDetection 资源类型 - Microsoft Graph beta 版本](/graph/api/resources/riskdetection?view=graph-rest-beta&preserve-view=true)| 请参阅[什么是风险？Azure AD 标识保护](../identity-protection/concept-identity-protection-risks.md) |

有关详细信息，请访问[什么是标识保护](../identity-protection/overview-identity-protection.md)。 


### <a name="what-to-look-for"></a>要查找什么

对 Azure AD 登录日志中的数据配置监视，以确保发出警报并遵循组织的安全策略。 这些功能的示例包括：

* 身份验证失败：作为人类，我们都会不时遇到密码错误的情况。 但是，许多身份验证失败可能指示恶意行动者正在试图获取访问权限。 攻击的程度有所不同，可能是每小时几次到更高频率。 例如，密码喷射通常针对许多帐户使用较简单的密码，而暴力攻击会针对目标帐户尝试多个密码。 

* 身份验证中断：Azure AD 中断表示注入了其他进程来满足身份验证，例如在 CA 策略中强制实施控制。 这是正常事件，在应用程序配置不正确时可能会发生这种情况。 但在看到用户帐户出现许多中断时，可能指示该帐户发生了某种情况。 

  * 例如，如果筛选某个用户的登录日志后发现大量登录状态 = 已中断和条件访问 = 失败。 深入发掘，可能会在身份验证详细信息中发现密码正确，但需要强身份验证。 这可能意味着用户未完成多重身份验证 (MFA) - 可能指示用户密码已泄露，并且恶意行动者无法完成 MFA。

* 智能锁定：Azure AD 提供智能锁定服务，该服务将熟悉和不熟悉位置的概念引入了身份验证过程。 访问熟悉位置的用户帐户可能会成功进行身份验证，而不熟悉同一位置的恶意行动者在多次尝试后将被阻止。 查找已锁定并将进一步调查的帐户。 

* IP 更改：看到源自不同 IP 地址的用户属于正常情况。 但是，零信任状态永远不会信任，并始终进行验证。 看到大量 IP 地址和登录失败可能指示存在入侵。 查找源自多个 IP 地址的多次身份验证失败的模式。 请注意，虚拟专用网 (VPN) 连接可能会导致误报。 无论存在何种挑战，我们都建议监视 IP 地址更改，如果可能，请使用 Azure AD 标识保护来自动检测和缓解这些风险。

* 位置：通常，用户需要用户帐户位于同一地理位置。 此外，还需要从拥有员工或业务关系的位置登录。 如果用户帐户来自不同的国际位置，所用时间少于到达该位置所需的时间，则指示该用户帐户被滥用。 请注意，VPN 可能会导致误报，建议监视从偏远地理位置登录的用户帐户，并且如果可能，请使用 Azure AD 标识保护来自动检测和缓解这些风险。

对于此风险区域，建议同时监视标准用户帐户和特权帐户，但优先调查特权帐户。 特权帐户是任何 Azure AD 租户中最重要的帐户。 有关特权帐户的具体指导，请参阅“安全操作 – 特权帐户”。 

### <a name="how-to-detect"></a>如何检测

使用 Azure 标识保护和 Azure AD 登录日志，帮助发现异常登录特征指示的威胁。 有关标识保护的信息，请参阅[什么是标识保护](../identity-protection/overview-identity-protection.md)。 此外，还可以将数据复制到 Azure Monitor 或 SIEM 进行监视和警报。 若要为环境定义正常情况并设置基线，请确定以下各项：

* 你认为对用户群正常的参数。

* 用户在呼叫服务台或执行自助密码重置之前一段时间的平均密码尝试次数。

* 在发出警报之前要允许的失败尝试次数，以及对于用户帐户和特权帐户是否不同。

* 在发出警报之前要允许的 MFA 尝试次数，以及对于用户帐户和特权帐户是否不同。

* 是否启用了旧式身份验证，以及停用路线图。 

* 适用于组织的已知出口 IP 地址。

* 用户运营的国家/地区。

* 是否有在某个网络位置或国家/地区中保持固定的用户组。

* 确定特定于组织的任何其他异常登录标志。 例如，一周或一年内组织未运营的天数或时间。

在确定环境中帐户类型的正常范围后，请考虑以下事项，以帮助确定要监视和发出警报的方案，并微调警报。

* 如果配置了标识保护，是否需要监视和发出警报？

* 对于特权帐户，是否存在更严格条件以用来监视和发出警报？ 例如，要求只能从受信任的 IP 地址使用特权帐户。

* 设置的基线是否过于激进？ 警报过多可能会导致警报被忽略或错过。

配置标识保护有助于确保实施支持安全基线策略的保护。 例如，如果风险 = 高，则阻止用户。 此风险级别指示用户帐户遭到入侵的置信度很高。 有关设置登录风险策略和用户风险策略的详细信息，请访问[标识保护策略](../identity-protection/concept-identity-protection-policies.md)。 有关设置条件访问的详细信息，请访问条件[条件访问：基于登录风险的条件访问](../conditional-access/howto-conditional-access-policy-risk.md)。

下面是根据条目的影响和严重性按重要性顺序列出的项目。

### <a name="monitoring-for-failed-unusual-sign-ins"></a>监视失败的异常登录

| 要监视的内容| 风险级别| Where| 筛选器/子筛选器| 说明 |
| - |- |- |- |- |
| 登录尝试失败。| 中 - 如果是独立事件<br>高 - 如果许多帐户遇到相同的模式或 VIP。| Azure AD 登录日志| 状态 = 失败<br>－和－<br>登录错误代码 50126 - <br>由于用户名或密码无效，验证凭据时出错。| 定义基线阈值，监视并调整以适应组织的行为，并限制生成虚假警报。 |
| 智能锁定事件。| 中 - 如果是独立事件<br>高 - 如果许多帐户遇到相同的模式或 VIP。| Azure AD 登录日志| 状态 = 失败<br>－和－<br>登录错误代码 = 50053 – IdsLocked| 定义基线阈值，监视并调整以适应组织的行为，并限制生成虚假警报。 |
| 中断| 中等 - 如果是独立事件<br>高 - 如果许多帐户遇到相同的模式或 VIP。| Azure AD 登录日志| 500121，在强身份验证请求期间，身份验证失败。 <br>-或-<br>50097，需要设备身份验证；或 50074，需要强身份验证。 <br>-或-<br>50155，DeviceAuthenticationFailed<br>-或-<br>50158，ExternalSecurityChallenge - 不符合外部安全质询<br>-或-<br>53003，失败原因 = 被 CA 阻止| 监视中断并发出警报。<br>定义基线阈值，监视并调整以适应组织的行为，并限制生成虚假警报。 |


下面是根据条目的影响和严重性按重要性顺序列出的项目。

| 要监视的内容| 风险级别| Where| 筛选器/子筛选器| 说明 |
| - |- |- |- |- |
| 多重身份验证 (MFA) 欺诈警报。| 高| Azure AD 登录日志| 状态 = 失败<br>－和－<br>详细信息 = 已拒绝 MFA<br>| 监视任何条目并发出警报。 |
| 来自未运营的国家/地区的身份验证失败。| 中等| Azure AD 登录日志| 位置 = \<unapproved location\>| 监视任何条目并发出警报。 |
| 由于使用旧版协议或未使用协议，身份验证失败。| 中等| Azure AD 登录日志| 状态 = 失败<br>－和－<br>客户端应用程序 = 其他客户端、POP、IMAP、MAPI、SMTP、ActiveSync| 监视任何条目并发出警报。 |
| 被 CA 阻止的失败。| 中等| Azure AD 登录日志| 错误代码 = 53003 <br>－和－<br>失败原因 = 被 CA 阻止| 监视任何条目并发出警报。 |
| 增加了任何类型的失败身份验证。| 中等| Azure AD 登录日志| 捕获跨板块失败的增加情况。 即，今天的失败总数比上周同一天高 10% 以上。| 如果没有设定阈值，则监视失败次数是否增加 10% 或更高并发出警报。 |
| 一周中国家/地区未执行正常业务操作的时间和日期进行的身份验证。| 低| Azure AD 登录日志| 捕获未按照正常操作日期\时间进行的交互身份验证。 <br>状态 = 成功<br>－和－<br>位置 = \<location\><br>－和－<br>日期\时间 = \<not normal working hours\>| 监视任何条目并发出警报。 |
| 帐户已禁用/被阻止登录| 低| Azure AD 登录日志| 状态 = 失败<br>－和－<br>错误代码 = 50057，该用户帐户已被禁用。| 这可能指示有人在他们离开组织后试图获取帐户访问权限。 虽然该帐户已被阻止，但记录这类活动并发出警报仍然很重要。 |


### <a name="monitoring-for-successful-unusual-sign-ins"></a>监视成功的异常登录

 | 要监视的内容| 风险级别| Where| 筛选器/子筛选器| 说明 |
| - |- |- |- |- |
| 超出预期控制的特权帐户身份验证。| 高| Azure AD 登录日志| 状态 = 成功<br>－和－<br>UserPricipalName = \<Admin account\><br>－和－<br>位置 = \<unapproved location\><br>－和－<br>IP 地址 = \<unapproved IP\><br>设备信息 = \<unapproved Browser, Operating System\><br>| 监视特权帐户超出预期控制的成功身份验证并发出警报。 下面列出了三项常用控制。 |
| 仅需要单重身份验证时。| 低| Azure AD 登录日志| 状态 = 成功<br>身份验证要求 = 单重身份验证| 定期监视此行为，并确保这是预期行为。 |
| 发现未注册使用 MFA 的特权帐户。| 高| Azure Graph API| 对管理员帐户查询 IsMFARegistered eq false。 <br>[列出 credentialUserRegistrationDetails - Microsoft Graph beta 版本 | Microsoft Docs](/graph/api/reportroot-list-credentialuserregistrationdetails?view=graph-rest-beta&preserve-view=true&tabs=http)| 进行审核与调查，以确定是有意为之还是疏忽所致。 |
| 来自组织未运营的国家/地区的成功身份验证。| 中等| Azure AD 登录日志| 状态 = 成功<br>位置 = \<unapproved country\>| 监视不等于提供的城市名称的任何条目并发出警报。 |
| 身份验证成功，会话被 CA 阻止。| 中等| Azure AD 登录日志| 状态 = 成功<br>－和－<br>错误代码 = 53003 – 失败原因，被 CA 阻止| 监视和调查身份验证成功但会话被 CA 阻止的情况。 |
| 禁用旧式身份验证后身份验证成功。| 中等| Azure AD 登录日志| 状态 = 成功 <br>－和－<br>客户端应用程序 = 其他客户端、POP、IMAP、MAPI、SMTP、ActiveSync| 如果组织已禁用旧式身份验证，则监视成功执行旧式身份验证的情况并发出警报。 |


建议定期查看只需要单重身份验证的中等业务影响 (MBI) 和高业务影响 (HBI) 应用的身份验证。 对于每个身份验证，都需要确定是否需要单重身份验证。 此外，根据位置查看成功身份验证的增加情况或出现意外次数。 

| 要监视的内容| 风险级别| Where| 筛选器/子筛选器| 说明 |
| - | - |- |- |- |
| 使用单重身份验证的 MBI 和 HBI 应用的身份验证。| 低| Azure AD 登录日志| 状态 = 成功<br>－和－<br>应用 ID = \<HBI app\> <br>－和－<br>身份验证要求 = 单重身份验证。| 查看并确认此配置是否有意为之。 |
| 一周或一年中国家/地区未执行正常业务操作的日期和时间进行的身份验证。| 低| Azure AD 登录日志| 捕获未按照正常操作日期\时间进行的交互身份验证。 <br>状态 = 成功<br>位置 = \<location\><br>日期/时间 = \<not normal working hours\>| 监视一周或一年中国家/地区未执行正常业务操作的身份验证日期和时间，并发出警报。 |
| 可量化的成功登录增加情况。| 低| Azure AD 登录日志| 捕获跨板块成功身份验证的增加情况。 即，今天的成功总数比上周同一天高 10% 以上。| 如果没有设置的阈值，则监视成功身份验证是否增加 10% 或更高并发出警报。 |

## <a name="next-steps"></a>后续步骤
请参阅以下安全操作指南文章：

[Azure AD 安全操作概述](security-operations-introduction.md)

[用户帐户的安全操作](security-operations-user-accounts.md)

[特权帐户的安全操作](security-operations-privileged-accounts.md)

[Privileged Identity Management 的安全操作](security-operations-privileged-identity-management.md)

[应用程序的安全操作](security-operations-applications.md)

[设备的安全操作](security-operations-devices.md)
 
[基础结构的安全操作](security-operations-infrastructure.md)
