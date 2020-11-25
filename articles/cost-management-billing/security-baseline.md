---
title: 成本管理的 Azure 安全基线
description: 成本管理安全基线提供过程指南和资源以实现 Azure 安全基准中指定的安全建议。
author: msmbaldwin
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 11/16/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 3270dae1fd37913ba51ca1da63bbb44d715e7d31
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/21/2020
ms.locfileid: "95021773"
---
# <a name="azure-security-baseline-for-cost-management"></a>成本管理的 Azure 安全基线

此安全基线将 [Azure 安全基准 2.0 版](../security/benchmarks/overview.md)中的指南应用到 Azure 成本管理。 Azure 安全基准提供有关如何在 Azure 上保护云解决方案的建议。 内容分为 Azure 安全基准定义的安全控制和适用于成本管理的相关指南。 不适用于成本管理的控制措施已排除。

若要查看成本管理如何完全映射到 Azure 安全基准，请参阅[成本管理安全基线映射的完整文件](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)。

## <a name="identity-management"></a>标识管理

有关详细信息，请参阅 [Azure 安全基准：标识管理](/azure/security/benchmarks/security-controls-v2-identity-management)。

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1：将 Azure Active Directory 标准化为标识和身份验证的中央系统

**指南**：Azure 成本管理与 Azure 的默认标识和访问管理服务 Azure Active Directory (Azure AD) 集成。 你应该在 Azure AD 上标准化，以便控制你的组织在以下方面的标识和访问管理：

- Microsoft 云资源，如 Azure 门户、Azure 存储、Azure 虚拟机（Linux 和 Windows）、Azure Key Vault、PaaS 和 SaaS 应用程序。

- 你的组织的资源，例如 Azure 上的应用程序，或公司网络资源。

在组织的云安全做法中，应优先处理 Azure AD 保护事宜。 Azure AD 提供标识安全分数，让你可以根据 Microsoft 的最佳做法建议来评估标识安全状况。 使用评分来估计你的配置与最佳做法建议的匹配程度，并改善你的安全状况。

注意：Azure AD 支持外部标识提供者，这些提供者允许没有 Microsoft 帐户的用户使用其外部标识登录到其应用程序和资源。

- [Azure AD 中的租户](../active-directory/develop/single-and-multi-tenant-apps.md)

- [如何创建和配置 Azure AD 实例](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [定义 Azure AD 租户](https://azure.microsoft.com/resources/securing-azure-environments-with-azure-active-directory/)  

- [对应用程序使用外部标识提供者](/azure/active-directory/b2b/identity-providers)

- [Azure AD 中的标识安全评分是什么](../active-directory/fundamentals/identity-secure-score.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3：对应用程序访问使用 Azure AD 单一登录 (SSO)

**指南**：Azure 成本管理使用 Azure Active Directory 来提供对 Azure 资源、云应用程序和本地应用程序的标识和访问管理。 这包括企业标识（如员工）以及外部标识（如合作伙伴和供应商）。 这样便可通过单一登录 (SSO) 管理本地和云中的组织数据和资源并对其进行安全访问。 将所有用户、应用程序和设备连接到 Azure AD，以实现无缝、安全的访问以及更好的可见性和控制。

- [了解使用 Azure AD 的应用程序 SSO](../active-directory/manage-apps/what-is-single-sign-on.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4：对所有基于 Azure Active Directory 的访问使用强身份验证控制

**指南**：Azure 成本管理与 Azure AD 集成，可支持通过多重身份验证 (MFA) 的强身份验证控制以及强无密码方法。

- 多重身份验证 (MFA) - 启用 Azure AD MFA 并遵循 Azure 安全中心标识和访问管理建议，以在 MFA 设置中实现一些最佳做法。 可对所有用户、所选用户强制执行 MFA，或可根据登录条件和风险因素在每个用户级别强制执行 MFA。

- 无密码身份验证 - 提供三个无密码身份验证选项：Windows Hello 企业版、Microsoft Authenticator 应用和本地身份验证方法（如智能卡）。

对于管理员和特权用户，请确保使用最高级别的强身份验证方法，然后向其他用户推出适当的强身份验证策略。

- [如何在 Azure 中启用 MFA](../active-directory/authentication/howto-mfa-getstarted.md) 

- [Azure Active Directory 的无密码身份验证选项简介](../active-directory/authentication/concept-authentication-passwordless.md) 

- [Azure AD 默认密码策略](../active-directory/authentication/concept-sspr-policy.md#password-policies-that-only-apply-to-cloud-user-accounts)

- [使用 Azure AD 密码保护来消除错误密码](../active-directory/authentication/concept-password-ban-bad.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5：监视帐户异常并发出警报

**指南**：Azure 成本管理与提供以下数据源的 Azure Active Directory 集成：

- 登录 - 在登录报告中，可了解托管应用程序的使用情况和用户登录活动。

- 审核日志 - 通过日志为 Azure AD 中的各种功能所做的所有更改提供可跟踪性。 审核日志的示例包括对 Azure AD 中的任何资源（例如添加或删除用户、应用、组、角色和策略）所做的更改。

- 风险登录 - 风险登录是指可能由非用户帐户合法拥有者进行的登录尝试。

- 已标记为存在风险的用户 - 风险用户是指可能已泄露的用户帐户。

这些数据源可与 Azure Monitor、Azure Sentinel 或第三方 SIEM 系统集成。

Azure 安全中心还可针对某些可疑活动发出警报，例如，失败的身份验证尝试次数过多，使用了订阅中的已弃用帐户。

Azure 高级威胁防护 (ATP) 是一种安全解决方案，它可使用 Active Directory 信号来识别、检测和调查高级威胁、泄露的标识以及恶意的内部操作。

- [Azure Active Directory 中的“审核活动”报表](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [如何查看 Azure AD 风险登录](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [如何确定标记为存在风险活动的 Azure AD 用户](/azure/active-directory/reports-monitoring/concept-user-at-risk) 

- [如何在 Azure 安全中心内监视用户的标识和访问活动](../security-center/security-center-identity-access.md) 

- [Azure 安全中心的威胁情报保护模块中的警报](../security-center/alerts-reference.md) 

- [如何将 Azure 活动日志集成到 Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Azure 安全中心监视**：是

**责任**：客户

## <a name="privileged-access"></a>特权访问

有关详细信息，请参阅 [Azure 安全基准：特权访问](/azure/security/benchmarks/security-controls-v2-privileged-access)。

### <a name="pa-1-protect-and-limit-highly-privileged-users"></a>PA-1：保护和限制具有较高权限的用户

**指南**：企业协议 (EA) 的成本管理有以下高特权帐户。

- 企业管理员

建议定期评审分配给企业协议 (EA) 内角色的用户。

还建议用作规则，限制高特权帐户或角色的数量并在提升的级别保护这些帐户，因为具有此特权的用户可以直接或间接地读取和修改 Azure 环境中的每个资源。

你可使用Azure AD Privileged Identity Management (PIM) 提供对 Azure 资源和 Azure AD 的实时 (JIT) 特权访问权限。 JIT 仅在用户需要执行特权任务时授予临时权限。 当 Azure AD 组织中存在可疑或不安全的活动时，PIM 还会生成安全警报。

- [管理 Azure 企业角色](manage/understand-ea-roles.md) 

- [Azure AD 中的管理角色权限](/azure/active-directory/users-groups-roles/directory-assign-admin-roles) 

- [使用 Azure Privileged Identity Management 安全警报](../active-directory/privileged-identity-management/pim-how-to-configure-security-alerts.md) 

- [确保 Azure AD 中混合部署和云部署的特权访问安全性](/azure/active-directory/users-groups-roles/directory-admin-roles-secure)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3：定期审查和协调用户访问权限

**指南**：Azure 成本管理依赖于适当访问权限来查看和管理组织成本数据。 访问权限由订阅级别的 Azure 基于角色的访问控制 (Azure RBAC) 控制，并通过具有计费范围的企业协议 (EA) 或 Microsoft 客户协议 (MCA) 的管理角色控制。 定期审核和评审授予的访问权限，确保用户或组具有所需的必备访问权限。

- [管理对 Azure 账单信息的访问权限](manage/manage-billing-access.md)

- [分配对成本管理数据的访问权限](costs/assign-access-acm-data.md)

- [管理 Azure 企业角色](manage/understand-ea-roles.md)

- [了解 Azure 中的 Microsoft 客户协议管理角色](manage/understand-mca-roles.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7：遵循 Just Enough Administration（最小特权原则） 

**指南**：Azure 成本管理与 Azure 基于角色的访问控制 (RBAC) 集成以管理资源（如预算、保存的报表等）。 使用 Azure RBAC，可通过角色分配来管理 Azure 资源访问。 可将这些角色分配给用户、组或服务主体。 某些资源具有预定义的内置角色，可以通过工具（例如 Azure CLI、Azure PowerShell 或 Azure 门户）来清点或查询这些角色。 通过 Azure RBAC 分配给资源的特权应始终限制为角色所需的特权。 这是对 Azure AD Privileged Identity Management (PIM) 的实时 (JIT) 方法的补充，应定期进行审查。

请使用内置角色来分配权限，仅在必要时创建自定义角色。

Azure 成本管理提供内置角色、读者和参与者。

- [Azure 成本管理读者](../role-based-access-control/built-in-roles.md#cost-management-reader)

- [Azure 成本管理参与者](../role-based-access-control/built-in-roles.md#cost-management-contributor)

什么是 Azure 基于角色的访问控制 (Azure RBAC) ../role-based-access-control/overview.md 

- [如何在 Azure 中配置 RBAC](../role-based-access-control/role-assignments-portal.md) 

- [如何使用 Azure AD 标识和访问评审](../active-directory/governance/access-reviews-overview.md)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="data-protection"></a>数据保护

[有关详细信息，请参阅 *Azure 安全基线：* 数据保护](/azure/security/benchmarks/security-controls-v2-data-protection)。

### <a name="dp-1-discovery-classify-and-label-sensitive-data"></a>DP-1：对敏感数据进行发现、分类和标记

**指南**：建议对敏感数据进行发现、分类和标记，以便可以设计适当的控制措施来确保敏感信息由组织的技术系统安全地存储、处理和传输。

对于 Azure 中的、本地的、Office 365 中的和其他位置中的 Office 文档内的敏感信息，请使用 Azure 信息保护（及其关联的扫描工具）。

使用 Azure SQL 信息保护有助于对 Azure SQL 数据库中存储的信息进行分类和标记。

- [使用 Azure 信息服务标记敏感信息](/azure/information-protection/what-is-information-protection) 

- [如何实现 Azure SQL 数据发现](/azure/sql-database/sql-database-data-discovery-and-classification)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="dp-2-protect-sensitive-data"></a>DP-2：保护敏感数据

**指南**：建议使用 Azure 基于角色的访问控制 (Azure RBAC)、基于网络的访问控制和 Azure 服务中的特定控制（例如 SQL 和其他数据库中的加密）来限制访问，从而保护敏感数据。

为了确保一致的访问控制，所有类型的访问控制都应符合企业分段策略。 企业分段策略还应根据敏感的或业务关键型的数据和系统的位置来确定。

对于 Microsoft 管理的基础平台，Microsoft 会将所有客户内容视为敏感数据，全方位防范客户数据丢失和泄露。 为了确保 Azure 中的客户数据始终安全，Microsoft 实施了一些默认的数据保护控制机制和功能。

- [分配对成本管理数据的访问权限](costs/assign-access-acm-data.md)

- [Azure 基于角色的访问控制 (RBAC)](../role-based-access-control/overview.md) 

- [了解 Azure 中的客户数据保护](../security/fundamentals/protection-customer-data.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="dp-3-monitor-for-unauthorized-transfer-of-sensitive-data"></a>DP-3：监视未经授权的敏感数据传输

**指南**：监视是否有向企业可见和控制范围以外的位置进行未经授权的数据传输。 这通常涉及监视那些可能意味着未经授权的数据外泄的异常活动（大型或异常传输）。

Azure 存储高级威胁防护 (ATP) 和 Azure SQL ATP 可以对可能意味着未经授权传输敏感信息的异常传输行为发出警报。

Azure 信息保护 (AIP) 提供的监视功能针对已分类并标记的信息。

如果要求满足数据丢失防护 (DLP) 规范，可以使用基于主机的 DLP 解决方案来强制实施检测性的和/或预防性的控制，以防止数据外泄。

- [启用 Azure SQL ATP](../azure-sql/database/threat-detection-overview.md) 

- [启用 Azure 存储 ATP](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection?tabs=azure-security-center)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="dp-5-encrypt-sensitive-data-at-rest"></a>DP-5：加密静态敏感数据

**指南**：为了对访问控制进行补充，Azure 成本管理的导出功能支持对静态数据进行 Azure 存储加密，以使用 Microsoft 托管的密钥加密来防范“带外”攻击（例如访问基础存储）。 此默认设置有助于确保攻击者无法轻松读取或修改数据。

有关详细信息，请参阅：

- [适用于静态数据的 Azure 存储加密](../storage/common/storage-service-encryption.md)

- [了解 Azure 中的静态加密](../security/fundamentals/encryption-atrest.md#encryption-at-rest-in-microsoft-cloud-services)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="asset-management"></a>资产管理

有关详细信息，请参阅 [Azure 安全基准：资产管理](/azure/security/benchmarks/security-controls-v2-asset-management)。

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1：确保安全团队可以了解与资产相关的风险

**指南**：确保在 Azure 租户和订阅中向安全团队授予了安全读取者权限，以便他们可以使用 Azure 安全中心监视安全风险。 

根据安全团队责任划分方式的不同，监视安全风险可能是中心安全团队或本地团队的责任。 也就是说，安全见解和风险必须始终在组织内集中聚合。 

安全读取者权限可以广泛应用于整个租户（根管理组），也可以限制到管理组或特定订阅。 

注意：若要了解工作负载和服务，可能需要更多权限。 

- [安全读取者角色概述](../role-based-access-control/built-in-roles.md#security-reader)

- [Azure 管理组概述](../governance/management-groups/overview.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>AM-4：确保资产生命周期管理的安全

**指南**：建立或更新安全策略，用于应对资产生命周期管理过程的可能具有重大影响的修改。 这些修改包括对以下内容的更改：标识提供者和访问权限、数据敏感度、网络配置，以及管理特权分配。

如果不再需要 Azure 资源，请将其删除。

- [删除 Azure 资源组和资源](../azure-resource-manager/management/delete-resource-group.md)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="logging-and-threat-detection"></a>日志记录和威胁检测

有关详细信息，请参阅 [Azure 安全基准：日志记录和威胁检测](/azure/security/benchmarks/security-controls-v2-logging-threat-detection)。

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2：启用 Azure 标识和访问管理的威胁检测

**指南**：Azure AD 提供以下用户日志，可在 Azure AD 报表中进行查看，也可将这些日志与 Azure Monitor、Azure Sentinel 或其他 SIEM/监视工具集成，以用于更复杂的监视和分析用例：

- 登录 - 登录报告提供有关托管应用程序的使用情况和用户登录活动的信息。

- 审核日志 - 通过日志为 Azure AD 中的各种功能所做的所有更改提供可跟踪性。 审核日志的示例包括对 Azure AD 中的任何资源（例如添加或删除用户、应用、组、角色和策略）所做的更改。

- 风险登录 - 风险登录是指可能由非用户帐户合法拥有者进行的登录尝试。

- 已标记为存在风险的用户 - 风险用户是指可能已泄露的用户帐户。

Azure 安全中心还可针对某些可疑活动发出警报，例如，失败的身份验证尝试次数过多，使用了订阅中的已弃用帐户。 除了基本的安全机制监视，Azure 安全中心的威胁防护模块还可从各个 Azure 计算资源（虚拟机、容器、应用服务）、数据资源（SQL 数据库和存储）和 Azure 服务层收集更深入的安全警报。 通过此功能，可查看各个资源内的帐户异常情况。

还建议定期评审分配给企业协议 (EA) 内角色的用户。 

- [管理 Azure 企业角色](manage/understand-ea-roles.md)

- [Azure Active Directory 中的“审核活动”报表](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [启用 Azure 标识保护](../active-directory/identity-protection/overview-identity-protection.md)

- [Azure 安全中心的威胁防护](/azure/security-center/threat-protection)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="incident-response"></a>事件响应

[有关详细信息，请参阅 *Azure 安全基线：* 事件响应](/azure/security/benchmarks/security-controls-v2-incident-response)。

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1：准备 - 更新 Azure 的事件响应流程

**指南**：确保你的组织具有安全事件的响应流程，已为 Azure 更新了这些流程，并会定期演练这些流程以确保准备就绪。

- [在企业环境中实现安全性](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [事件响应参考指南](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2：准备 - 设置事件通知

**指南**：在 Azure 安全中心中设置安全事件联系人信息。 如果 Microsoft 安全响应中心 (MSRC) 发现非法或未经授权的一方访问了你的数据，Microsoft 将使用此联系信息来与你取得联系。 还可以选择基于事件响应需求在不同的 Azure 服务中自定义事件警报和通知。 

- [如何设置 Azure 安全中心安全联系人](../security-center/security-center-provide-security-contact-details.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3：检测和分析 - 根据高质量警报创建事件

**指南**：确保你有创建高质量警报和衡量警报质量的流程。 这样，你就可以从过去的事件中吸取经验，并为分析人员确定警报的优先级，这样他们就不会浪费时间来处理误报。 

可以基于从过去的事件中吸取的经验、经过验证的社区来源以及各种工具来生成高质量警报，这些工具旨在通过融合和关联各种信号源来生成和清除警报。 

Azure 安全中心可跨许多 Azure 资产提供高质量的警报。 可以使用 ASC 数据连接器将警报流式传输到 Azure Sentinel。 借助 Azure Sentinel，可创建高级警报规则来自动生成事件以进行调查。 

使用导出功能导出 Azure 安全中心警报和建议，以帮助识别 Azure 资源的风险。 手动导出或持续导出警报和建议。

- [如何配置导出](../security-center/continuous-export.md)

- [如何将警报流式传输到 Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4：检测和分析 - 调查事件

**指南**：确保分析人员在调查潜在事件时可查询和使用不同的数据源，以全面了解所发生的情况。 应收集各种各样的日志，以跟踪整个终止链中潜在攻击者的活动，避免出现盲点。  还应确保收集见解和经验，以供其他分析人员使用和用作将来的历史参考资料。  

用于调查的数据源包括已从作用域内服务和正在运行的系统中收集的集中式日志记录源，但还可以包括以下内容：

- 网络数据 - 使用网络安全组的流日志、Azure 网络观察程序和 Azure Monitor 来捕获网络流日志和其他分析信息。 

- 正在运行的系统的快照： 

    - 使用 Azure 虚拟机的快照功能创建正在运行的系统磁盘的快照。 

    - 使用操作系统的本机内存转储功能来创建正在运行的系统内存的快照。

    - 使用 Azure 服务的快照功能或软件自带的功能来创建正在运行的系统的快照。

Azure Sentinel 提供几乎针对任何日志源的广泛数据分析，并提供一个事例管理门户来管理事件的整个生命周期。 调查过程中的情报信息可与事件相关联，以便进行跟踪和报告。 

- [Windows 计算机的磁盘快照](../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [Linux 计算机的磁盘快照](../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Microsoft Azure 支持诊断信息和内存转储收集](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [使用 Azure Sentinel 调查事件](../sentinel/tutorial-investigate-cases.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5：检测和分析 - 设置事件优先级

**指南**：根据警报严重性和资产敏感度，为分析人员提供上下文来确定应首要关注哪些事件。 

Azure 安全中心为每条警报分配严重性，方便你根据优先级来确定应该最先调查的警报。 严重性取决于安全中心对调查结果或用于发出警报的分析的可信度，以及对导致警报的活动背后存在恶意意图的可信度级别。

此外，使用标记来标记资源，并创建命名系统来对 Azure 资源进行标识和分类，特别是处理敏感数据的资源。  你的责任是根据发生事件的 Azure 资源和环境的关键性确定修正警报的优先级。

- [Azure 安全中心中的安全警报](../security-center/security-center-alerts-overview.md)

- [使用标记整理 Azure 资源](/azure/azure-resource-manager/resource-group-using-tags)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6：遏制、根除和恢复 - 自动执行事件处理

**指南**：自动执行重复性手动任务，以加快响应速度并减轻分析人员的负担。 执行手动任务需要更长的时间，这会导致减慢每个事件的速度，并减少分析人员可以处理的事件数量。 手动任务还会使分析人员更加疲劳，这会增加可导致延迟的人为错误的风险，并降低分析人员专注于复杂任务的工作效率。 使用 Azure 安全中心和 Azure Sentinel 中的工作流自动化功能，可自动触发操作或运行 playbook，对传入的安全警报作出响应。 playbook 执行多项操作，如发送通知、禁用帐户和隔离有问题的网络。 

- [在安全中心配置工作流自动化](../security-center/workflow-automation.md)

- [在 Azure 安全中心设置自动威胁响应](../security-center/tutorial-security-incident.md#triage-security-alerts)

- [在 Azure Sentinel 中设置自动威胁响应](../sentinel/tutorial-respond-threats-playbook.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

## <a name="posture-and-vulnerability-management"></a>状况和漏洞管理

有关详细信息，请参阅 [Azure 安全基准：态势和漏洞管理](/azure/security/benchmarks/security-controls-v2-posture-vulnerability-management)。

### <a name="pv-7-rapidly-and-automatically-remediate-software-vulnerabilities"></a>PV-7：快速自动修正软件漏洞

**指南**：快速部署软件更新，以修正操作系统和应用程序中的软件漏洞。

优先使用通用风险评分计划（例如通用漏洞评分系统）或第三方扫描工具提供的默认风险评级，并使用以下上下文根据环境进行定制：哪些应用程序存在较高的安全风险，哪些应用程序需要较长的运行时间。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8：执行定期攻击模拟

**指南**：根据需要，对 Azure 资源进行渗透测试和红队活动，确保修正所有关键的安全发现结果。
请遵循 Microsoft 云渗透测试互动规则，确保你的渗透测试不违反 Microsoft 政策。 使用 Microsoft 红队演练策略和执行，以及针对 Microsoft 托管云基础结构、服务和应用程序执行现场渗透测试。

- [Azure 中的渗透测试](../security/fundamentals/pen-testing.md)

- [参与的渗透测试规则](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft 云红色组合](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure 安全中心监视**：不适用

**责任**：共享

## <a name="governance-and-strategy"></a>治理和策略

有关详细信息，请参阅 [Azure 安全基准：治理和策略](/azure/security/benchmarks/security-controls-v2-governance-strategy)。

### <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1：定义资产管理和数据保护策略 

**指南**：确保为系统和数据的持续监视和保护记录并传达清晰的策略。 确定业务关键数据和系统的发现、评估、保护和监视优先级。 

此策略应包括针对以下元素的记录在案的指南、策略和标准： 

-   与业务风险相符的数据分类标准

-   安全组织对风险和资产清单的洞察力 

-   安全组织对 Azure 服务使用的审批 

-   资产在其生命周期中的安全性

-   与组织数据分类相符的必需访问控制策略

-   使用 Azure 原生的和第三方的数据保护功能

-   传输中数据用例和静态数据用例的数据加密要求

-   合适的加密标准

有关详细信息，请参阅以下资源：
- [Azure 安全体系结构建议 - 存储、数据和加密](https://docs.microsoft.com/azure/architecture/framework/security/storage-data-encryption?toc=/security/compass/toc.json&amp;bc=/security/compass/breadcrumb/toc.json)

- [Azure 安全基础知识 - Azure 数据安全、加密和存储](../security/fundamentals/encryption-overview.md)

- [云采用框架 - Azure 数据安全和加密最佳做法](https://docs.microsoft.com/azure/security/fundamentals/data-encryption-best-practices?toc=/azure/cloud-adoption-framework/toc.json&amp;bc=/azure/cloud-adoption-framework/_bread/toc.json)

- [Azure 安全基准 - 资产管理](/azure/security/benchmarks/security-controls-v2-asset-management)

- [Azure 安全基准 - 数据保护](/azure/security/benchmarks/security-controls-v2-data-protection)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2：定义企业分段策略 

**指南**：建立企业范围的策略，将标识、网络、应用程序、订阅、管理组和其他控制措施结合使用以对资产的访问进行细分。

仔细权衡安全分离需求与为需要彼此通信并访问数据的系统启用日常操作的需求。

确保跨控制类型（包括网络安全、标识和访问模型、应用程序权限/访问模型，以及人机过程控制）一致地实现分段策略。

- [有关 Azure 中的分段策略的指南（视频）](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [有关 Azure 中的分段策略的指南（文档）](/security/compass/governance#enterprise-segmentation-strategy)

- [使网络分段与企业分段策略相匹配](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="gs-3-define-security-posture-management-strategy"></a>GS-3：定义安全状况管理策略

**指南**：不断衡量并降低各个资产及其托管环境的风险。 确定高价值资产和暴露程度高的受攻击面（例如已发布的应用程序、网络入口和出口点、用户和管理员终结点等）的优先级。

- [Azure 安全基准 - 状况和漏洞管理](/azure/security/benchmarks/security-controls-v2-posture-vulnerability-management)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4：协调组织角色、职责和责任

**指南**：确保为安全组织中的角色和责任记录并传达清晰的策略。 优先考虑提供涉及安全决策的明确责任，对每个人进行共同职责模式培训，并为技术团队传授保护云的技术。

- [Azure 安全最佳做法 1 - 人员：针对云安全云之旅培训团队](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey)

- [Azure 安全最佳做法 2 - 人员：针对云安全技术培训团队](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology)

- [Azure 安全最佳做法 3 - 流程：针对云安全决策分配责任](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="gs-5-define-network-security-strategy"></a>GS-5：定义网络安全策略

**指南**：建立 Azure 网络安全方法，作为组织整体安全访问控制策略的一部分。  

此策略应包括针对以下元素的记录在案的指南、策略和标准： 

-   集中化的网络管理和安全职责

-   符合企业分段策略的虚拟网络分段模型

-   各种威胁和攻击场景中的补救策略

-   Internet 边缘及入口和出口策略

-   混合云和本地互连策略

-   最新的网络安全项目（例如网络关系图、参考网络体系结构）

有关详细信息，请参阅以下资源：
- [Azure 安全最佳做法 11 - 体系结构。单一的统一安全策略](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Azure 安全基准 - 网络安全](/azure/security/benchmarks/security-controls-v2-network-security)

- [Azure 网络安全概述](../security/fundamentals/network-overview.md)

- [企业网络体系结构策略](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6：定义标识和特权访问策略

**指南**：建立 Azure 标识和特权访问方法，作为组织整体安全访问控制策略的一部分。  

此策略应包括针对以下元素的记录在案的指南、策略和标准： 

-   集中化的标识和身份验证系统及其与其他内部和外部标识系统的互连

-   各种用例和条件中的强身份验证方法

-   保护权限高的用户

-   异常用户活动监视和处理  

-   用户标识和访问评审及协调流程

有关详细信息，请参阅以下资源：

- [Azure 安全基准 - 标识管理](/azure/security/benchmarks/security-controls-v2-identity-management)

- [Azure 安全基准 - 特权访问](/azure/security/benchmarks/security-controls-v2-privileged-access)

- [Azure 安全最佳做法 11 - 体系结构。单一的统一安全策略](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Azure 标识管理安全概述](../security/fundamentals/identity-management-overview.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7：定义日志记录和威胁响应策略

**指南**：建立日志记录和威胁响应策略，以在满足符合性要求的同时快速检测和修正威胁。 优先为分析师提供高质量警报和无缝体验，以便他们能够专注于威胁，而不是执行集成和手动步骤。 

此策略应包括针对以下元素的记录在案的指南、策略和标准： 

-   安全运营 (SecOps) 组织的角色和职责 

-   符合 NIST 或其他行业框架要求的明确定义的事件响应流程 

-   日志捕获和保留，用于支持威胁检测、事件响应和合规性需求

-   使用 SIEM、原生 Azure 功能和其他源，集中查看和关联有关威胁的信息 

-   与客户、供应商和公开的利益相关方之间的通信和通知计划

-   使用 Azure 原生的和第三方的平台进行事件处理，例如日志记录和威胁检测、取证以及攻击补救和根除

-   处理事件和事件后活动的流程，例如经验教训和证据保留

有关详细信息，请参阅以下资源：

- [Azure 安全基准 - 日志记录和威胁检测](/azure/security/benchmarks/security-controls-v2-logging-threat-detection)

- [Azure 安全基准 - 事件响应](/azure/security/benchmarks/security-controls-v2-incident-response)

- [Azure 安全最佳做法 4 - 流程。更新云的事件响应流程](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Azure 采用框架、日志记录和报告决策指南](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Azure 企业规模、管理和监视](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="next-steps"></a>后续步骤

- 参阅 [Azure 安全基准 V2 概述](/azure/security/benchmarks/overview)
- 详细了解 [Azure 安全基线](/azure/security/benchmarks/security-baselines-overview)
