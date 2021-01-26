---
title: Azure Lighthouse 的 azure 安全基线
description: Azure Lighthouse security 基线为实现 Azure 安全基准中指定的安全建议提供过程指南和资源。
author: msmbaldwin
ms.service: lighthouse
ms.topic: conceptual
ms.date: 11/19/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: cd43b6c7b11cde17bc9f9923374baf3416e5b370
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/26/2021
ms.locfileid: "98791856"
---
# <a name="azure-security-baseline-for-azure-lighthouse"></a>Azure Lighthouse 的 azure 安全基线

此安全基线将 [Azure 安全基准版本 2.0](../security/benchmarks/overview.md) 中的指南应用到 azure Lighthouse。 Azure 安全基准提供有关如何在 Azure 上保护云解决方案的建议。 内容按照 Azure 安全基准定义的 **安全控件** 进行分组，以及适用于 azure Lighthouse 的相关指南。 不适用于 Azure Lighthouse 的 **控件** 已被排除。

若要查看 Azure Lighthouse 如何完全映射到 Azure 安全基准，请参阅 [完整的 Azure Lighthouse security 基线映射文件](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)。

## <a name="identity-management"></a>标识管理

有关详细信息，请参阅 [Azure 安全基准：标识管理](../security/benchmarks/security-controls-v2-identity-management.md)。

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1：将 Azure Active Directory 标准化为中央标识和身份验证系统

**指南**： Azure Lighthouse 使用 Azure Active Directory (Azure AD) 作为默认的标识和访问管理服务。 标准化 Azure AD 来管理组织的标识和访问管理：
- Microsoft 云资源，如 Azure 门户、Azure 存储、Azure 虚拟机（Linux 和 Windows）、Azure Key Vault、PaaS 和 SaaS 应用程序。
- 你的组织的资源，例如 Azure 上的应用程序，或公司网络资源。

使用 Azure Lighthouse，管理租户中的指定用户拥有 Azure 内置角色，使其能够访问客户租户中的委托订阅和/或资源组。 当前支持除所有者或具有 DataActions 权限的任何内置角色以外的所有内置角色。 仅在向托管标识分配角色时才支持使用用户访问管理员角色。 不支持自定义角色和经典订阅管理员角色。

- [Azure Active Directory 中的租赁](../active-directory/develop/single-and-multi-tenant-apps.md) 

- [如何创建和配置 Azure AD 实例](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) 

- [使用应用程序的外部标识提供者](../active-directory/external-identities/identity-providers.md) 

- [Azure Active Directory 中的标识安全分数是什么](../active-directory/fundamentals/identity-secure-score.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="im-2-manage-application-identities-securely-and-automatically"></a>IM-2：安全且自动地管理应用程序标识

**指南**： azure 托管标识可以对支持 Azure AD 身份验证的 azure 服务和资源进行身份验证。 身份验证是通过预定义的访问授权规则启用的，避免了在源代码或配置文件中使用硬编码的凭据。 使用 Azure Lighthouse，在客户的订阅中具有 "用户访问管理员" 角色的用户可以在该客户的租户中创建一个托管标识。 虽然 Azure Lighthouse 通常不支持此角色，但可以在此特定方案中使用此角色，这使得具有此权限的用户可以将一个或多个特定内置角色分配到托管标识。

对于不支持托管标识的服务，则请使用 Azure AD 在资源级别创建权限受限的服务主体。 Azure Lighthouse 允许服务主体根据在载入过程中获得的角色访问客户资源。 建议使用证书凭据配置服务主体，并回退到客户端机密。 在这两种情况下，都可以将 Azure Key Vault 与 Azure 托管标识结合使用，以便运行时环境（例如 Azure 函数）可以从密钥保管库中检索凭据。

- [Azure 托管标识](../active-directory/managed-identities-azure-resources/overview.md)

- [Azure 服务主体](/powershell/azure/create-azure-service-principal-azureps)

- [使用 Azure Key Vault 进行安全主体注册](../key-vault/general/authentication.md#authorize-a-security-principal-to-access-key-vault)

- [创建可向客户租户中的托管标识分配角色的用户](how-to/deploy-policy-remediation.md#create-a-user-who-can-assign-roles-to-a-managed-identity-in-the-customer-tenant)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4：对所有基于 Azure Active Directory 的访问使用强身份验证控制

**指南**：对于管理租户中的所有用户，需要多重身份验证 (MFA) ，包括将有权访问委派的客户资源的用户。 建议你要求客户同时在其租户中实施 MFA。

- [如何在 Azure 中启用 MFA](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5：监视并提醒帐户异常

**指南**： Azure AD 提供以下数据源： 

-   登录 - 登录报告提供有关托管应用程序的使用情况和用户登录活动的信息。

-   审核日志 - 对于通过 Azure AD 中的各种功能所做的所有更改，可以通过日志为其提供可跟踪性。 所记录的更改审核日志的示例包括添加或删除用户、应用、组、角色和策略。

-   风险登录 - 风险登录是指可能由非用户帐户合法拥有者进行的登录尝试。

-   已标记为存在风险的用户 - 风险用户是指可能已泄露的用户帐户。

这些数据源可与 Azure Monitor、Azure Sentinel 或第三方 SIEM 系统集成。

使用 Azure Lighthouse 的服务提供商可以将 Azure AD 日志转发到 Azure Sentinel，并跨租户查看/设置警报，以监视和发出有关帐户异常的警报。

- [Azure AD 中的审核活动报告](../active-directory/reports-monitoring/concept-audit-logs.md)

- [如何查看 Azure AD 风险登录](../active-directory/identity-protection/overview-identity-protection.md)

- [大规模管理 Azure Sentinel 工作区](how-to/manage-sentinel-workspaces.md)

- [将数据从 Azure AD 连接到 Azure Sentinel](../sentinel/connect-azure-active-directory.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6：基于条件限制 Azure 资源访问

**指南**： Azure Lighthouse 不支持委派的客户资源的条件性访问功能。 在管理租户中，使用 Azure AD 条件性访问，基于用户定义的条件进行更精细的访问控制，如要求通过特定 IP 范围的用户登录使用多重身份验证 (MFA) 。 还可以通过 Azure AD 条件访问策略对不同的用例使用精细的身份验证会话管理。 

你应要求对管理租户中的所有用户进行 MFA，包括将有权访问委派的客户资源的用户。 建议你要求客户同时在其租户中实施 MFA。

- [Azure 条件访问概述](../active-directory/conditional-access/overview.md)

- [常见条件访问策略](../active-directory/conditional-access/concept-conditional-access-policy-common.md)

- [使用条件访问配置身份验证会话管理](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="privileged-access"></a>特权访问

有关详细信息，请参阅 [Azure 安全基准：特权访问](../security/benchmarks/security-controls-v2-privileged-access.md)。

### <a name="pa-1-protect-and-limit-highly-privileged-users"></a>PA-1：保护和限制具有较高权限的用户

**指南**：限制高特权用户帐户的数量，并在提升的级别保护这些帐户。 启用和使用 Azure Lighthouse 不需要全局管理员帐户。

若要访问租户级别的活动日志数据，必须在根范围 (/) 向帐户分配监视读取器 Azure 内置角色。 由于根作用域上的 "监视读取者" 角色是广泛的访问级别，因此建议将此角色分配给服务主体帐户，而不是分配给单个用户或组。 此分配必须由具有 "全局管理员" 角色的用户执行，并且具有其他提升的访问权限。 此提升的访问权限应该在进行角色分配之前立即添加，然后在分配完成时删除。

- [Azure AD 中的管理角色权限](../active-directory/roles/permissions-reference.md)

- [分配访问以监视租户级活动日志数据](how-to/monitor-delegation-changes.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2：限制对关键业务型系统的管理访问权限

**指南**： azure Lighthouse 使用 azure RBAC) 的 azure 基于角色的访问控制 (azure RBAC 通过限制授予了对订阅和管理组的特权访问权限的帐户，隔离对关键系统的访问。

请务必遵循最低权限原则，使用户仅具有完成特定任务所需的权限。

确保还限制了对管理、标识和安全系统的访问，因为这些系统对业务关键型资产具有管理访问权限，这些系统包括 Active Directory 域控制器 (DC)、安全工具以及在业务关键型系统上安装了代理的系统管理软件等。 入侵这些管理和安全系统的攻击者可以立即将它们用作损害业务关键型资产的武器。

所有类型的访问控制都应符合企业分段策略，确保访问控制保持一致。

- [Azure 组件和参考模型](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [访问管理组](../governance/management-groups/overview.md#management-group-access)

- [Azure 订阅管理员](../cost-management-billing/manage/add-change-subscription-administrator.md)

- [为 Azure Lighthouse 定义用户和角色的最佳实践](concepts/tenants-users-roles.md#best-practices-for-defining-users-and-roles)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3：定期审查和协调用户访问权限

**指南**： Azure Lighthouse 使用 Azure Active Directory (Azure AD) 帐户来管理其资源、定期查看用户帐户和访问分配，以确保帐户及其访问有效。 可使用 Azure AD 访问评审来审查组成员身份、对企业应用程序的访问权限和角色分配。 Azure AD 报告提供日志来帮助发现过时的帐户。 你还可使用 Azure AD Privileged Identity Management 来创建访问评审报表工作流以便于执行评审。

客户可以在 Azure 门户中通过 Azure Lighthouse 查看向管理租户中的用户授予的访问级别。 他们可以随时删除此访问权限。

此外，Azure Privileged Identity Management 还可配置为在创建过多管理员帐户时发出警报，并识别过时或配置不正确的管理员帐户。

注意：某些 Azure 服务支持不通过 Azure AD 管理的本地用户和角色。 你需要单独管理这些用户。

- [在 Privileged Identity Management (PIM) 中创建对 Azure 资源角色的访问评审](../active-directory/privileged-identity-management/pim-resource-roles-start-access-review.md) 

- [删除对委派的访问权限](how-to/remove-delegation.md)

- [如何使用 Azure AD 标识和访问评审](../active-directory/governance/access-reviews-overview.md)

- [查看 Azure 门户中的 "服务提供程序" 页](how-to/view-manage-service-providers.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4：在 Azure AD 中设置紧急访问

**指南**： Azure Lighthouse 与 Azure Active Directory 集成，以管理其资源。 为了防止意外退出 Azure AD 组织，请设置一个紧急访问帐户，以便在正常管理帐户无法使用时进行访问。 紧急访问帐户通常拥有较高的权限，因此请不要将其分配给特定的个人。 紧急访问帐户只能用于“不受限”紧急情况，即不能使用正常管理帐户的情况。

应确保妥善保管紧急访问帐户的凭据（例如密码、证书或智能卡），仅将其告诉只能在紧急情况下有权使用它们的个人。

- [在 Azure AD 中管理紧急访问帐户](../active-directory/roles/security-emergency-access.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="pa-5-automate-entitlement-management"></a>PA-5：将权利管理自动化 

**指南**： Azure Lighthouse 与 Azure Active Directory (Azure AD) 管理其资源。 使用 Azure AD 的权利管理功能可自动执行访问请求工作流，包括访问权限分配、审查和过期。 还支持两阶段或多阶段审批。

- [什么是 Azure AD 访问评审](../active-directory/governance/access-reviews-overview.md) 

- [什么是 Azure AD 权利管理](../active-directory/governance/entitlement-management-overview.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6：使用特权访问工作站

**指导**：安全的独立工作站对于确保敏感角色（如管理员、开发人员和关键服务操作员）的安全至关重要。 根据你的要求，你可以使用高度安全的用户工作站和/或 Azure 堡垒在生产环境中使用 Azure Lighthouse 执行管理任务。 使用 Azure Active Directory、Microsoft Defender 高级威胁防护 (ATP) 和/或 Microsoft Intune 部署安全的托管用户工作站，用于执行管理任务。 可集中管理安全工作站，强制实施安全配置，包括强身份验证、软件和硬件基线，以及受限制的逻辑和网络访问。 

- [了解特权访问工作站](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [部署特权访问工作站](/security/compass/privileged-access-deployment)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7：遵循 Just Enough Administration（最小特权原则） 

**指南**： azure Lighthouse 与 azure 基于角色的访问控制集成 (RBAC) ，用于管理其资源。 使用 Azure RBAC，可通过角色分配来管理 Azure 资源访问。 可以将这些内置角色分配给用户、组、服务主体和托管标识。 某些资源具有预定义的内置角色，可以通过工具（例如 Azure CLI、Azure PowerShell 或 Azure 门户）来清点或查询这些角色。 通过 Azure RBAC 分配给资源的特权应始终限制为角色所需的特权。 这是对 Azure AD Privileged Identity Management (PIM) 的实时 (JIT) 方法的补充，应定期进行审查。 请使用内置角色来分配权限，仅在必要时创建自定义角色。

Azure Lighthouse 允许使用 Azure 内置角色访问委派的客户资源。 在大多数情况下，你需要将这些角色分配给一个组或服务主体，而不是分配给多个单独的用户帐户。 这样，便可添加或删除单位用户的访问权限，而无需在访问要求更改时更新和重新发布计划。

若要将客户资源委托给管理租户，必须由客户租户中具有 " (载入" 订阅的 "所有者" 角色的非来宾帐户（其中包含要载入) 的资源组）执行部署。

- [了解 Azure Lighthouse 中的租户、用户和角色](concepts/tenants-users-roles.md)

- [如何将最低权限原则应用到 Azure Lighthouse](concepts/recommended-security-practices.md#assign-permissions-to-groups-using-the-principle-of-least-privilege)

- [什么是 Azure 基于角色的访问控制 (Azure RBAC)](../role-based-access-control/overview.md) 

- [如何使用 Azure AD 标识和访问评审](../active-directory/governance/access-reviews-overview.md)

**Azure 安全中心监视**：是

**责任**：客户

## <a name="asset-management"></a>资产管理

有关详细信息，请参阅 [Azure 安全基准：资产管理](../security/benchmarks/security-controls-v2-asset-management.md)。

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1：确保安全团队可以了解与资产相关的风险

**指南**：确保在 Azure 租户和订阅中向安全团队授予了安全读取者权限，以便他们可以使用 Azure 安全中心监视安全风险。 

根据安全团队责任划分方式的不同，监视安全风险可能是中心安全团队或本地团队的责任。 也就是说，安全见解和风险必须始终在组织内集中聚合。 

安全读取者权限可以广泛应用于整个租户（根管理组），也可以限制到管理组或特定订阅。 

注意：若要了解工作负载和服务，可能需要更多权限。 

- [安全读取者角色概述](../role-based-access-control/built-in-roles.md#security-reader)

- [Azure 管理组概述](../governance/management-groups/overview.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2：确保安全团队有权访问资产清单和元数据

**指南**：客户的安全团队可以查看活动日志，以查看使用 Azure Lighthouse 的服务提供商执行的活动。 

如果服务提供商要允许其安全团队查看委派的客户资源，安全团队的授权应包括 "读者" 内置角色。

- [客户如何查看服务提供商活动](how-to/view-service-provider-activity.md)

- [如何在将客户加入 Azure Lighthouse 时指定角色](how-to/onboard-customer.md#define-roles-and-permissions)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="am-3-use-only-approved-azure-services"></a>AM-3：仅使用已批准的 Azure 服务

**指导**：请使用 Azure Policy 来审核和限制用户可以在你的环境中预配哪些服务。 使用 Azure Resource Graph 查询和发现订阅中的资源。 你也可以使用 Azure Monitor 来创建规则，以便在检测到未经批准的服务时触发警报。

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md) 

- [如何使用 Azure Policy 拒绝特定的资源类型](../governance/policy/samples/built-in-policies.md#general) 

- [如何使用 Azure Resource Graph 浏览器创建查询](../governance/resource-graph/first-query-portal.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>AM-4：确保资产生命周期管理的安全

**指南**：删除不再需要通过 Azure Lighthouse 委托的资源的访问权限，以便服务提供商不再具有访问权限。 可以由客户或服务提供商删除访问权限。 

- [删除对委派的访问权限](how-to/remove-delegation.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5：限制用户与 Azure 资源管理器进行交互的能力

**指南**： Azure Lighthouse 与 Azure Active Directory (Azure AD 的) 用于标识和身份验证。 你可以通过为“Microsoft Azure 管理”应用配置“阻止访问”，使用 Azure 条件访问来限制用户与 Azure 资源管理器交互的能力。

- [如何配置条件访问来阻止对 Azure 资源管理器的访问](../role-based-access-control/conditional-access-azure-management.md)

**Azure 安全中心监视**：是

**责任**：客户

## <a name="logging-and-threat-detection"></a>日志记录和威胁检测

有关详细信息，请参阅 [Azure 安全基准：日志记录和威胁检测](../security/benchmarks/security-controls-v2-logging-threat-detection.md)。

### <a name="lt-1-enable-threat-detection-for-azure-resources"></a>LT-1：为 Azure 资源启用威胁检测

**指南**：通过 Azure Lighthouse，可以监视客户的 Azure 资源，以了解潜在的威胁和异常情况。 专注于获取高质量警报以减少误报，便于分析人员进行分类整理。 警报可能源自日志数据、代理或其他数据。

使用 Azure 安全中心内置的威胁检测功能，该功能基于监视 Azure 服务遥测和分析服务日志。 数据是使用 Log Analytics 代理收集的，该代理从系统中读取各种与安全相关的配置和事件日志，然后将数据复制到工作区进行分析。 

此外，使用 Azure Sentinel 构建分析规则，以便在客户环境中寻找与特定条件相匹配的威胁。 规则匹配时，这些规则会生成事件，以便调查每个事件。 Azure Sentinel 还可以导入第三方威胁情报，以增强其威胁检测功能。 

- [Azure 安全中心的威胁防护](../security-center/azure-defender.md)

- [Azure 安全中心安全警报参考指南](../security-center/alerts-reference.md)

- [创建自定义分析规则以检测威胁](../sentinel/tutorial-detect-threats-custom.md)

- [大规模管理 Azure Sentinel 工作区](how-to/manage-sentinel-workspaces.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2：启用 Azure 标识和访问管理的威胁检测

**指南**：通过 azure Lighthouse，你可以使用 Azure 安全中心来提醒你所管理的客户租户中的某些可疑活动，例如，过多的身份验证尝试次数过多，并且订阅中的帐户已弃用。

Azure Active Directory (Azure AD) 提供以下用户日志，可在 Azure AD 报表中查看这些用户日志，或与 Azure Monitor、Azure Sentinel 或其他 SIEM/监视工具进行集成，以实现更复杂的监视和分析用例：
- 登录–登录报表提供有关托管应用程序和用户登录活动的使用情况的信息。
- 审核日志 - 通过日志为 Azure AD 中的各种功能所做的所有更改提供可跟踪性。 审核日志的示例包括对 Azure AD 中的任何资源（例如添加或删除用户、应用、组、角色和策略）所做的更改。
- 有风险的登录是指可能已由不是用户帐户合法所有者的用户执行的登录尝试的指示符的指示。
- 已标记为存在风险的用户 - 风险用户是指可能已泄露的用户帐户。

Azure 安全中心还可针对某些可疑活动发出警报，例如，失败的身份验证尝试次数过多，使用了订阅中的已弃用帐户。 除了基本的安全机制监视，Azure 安全中心的威胁防护模块还可从各个 Azure 计算资源（虚拟机、容器、应用服务）、数据资源（SQL 数据库和存储）和 Azure 服务层收集更深入的安全警报。 此功能提供单个资源内的帐户异常的可见性。 

- [Azure Lighthouse 的增强的服务和方案](concepts/cross-tenant-management-experience.md#enhanced-services-and-scenarios)

- [Azure Active Directory 中的“审核活动”报表](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [启用 Azure 标识保护](../active-directory/identity-protection/overview-identity-protection.md) 

- [Azure 安全中心的威胁防护](../security-center/azure-defender.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4：为 Azure 资源启用日志记录

**指南**：自动可用的活动日志包含 Azure Lighthouse 资源的所有写入操作 (PUT、POST、DELETE) ，但读取操作 (获取) 。 活动日志可用于在进行故障排除时查找错误，或监视组织中的用户如何对资源进行修改。

使用 Azure Lighthouse，你可以通过可缩放的方式在你管理的客户租户中使用 Azure Monitor 日志。 直接在客户租户中创建 Log Analytics 工作区，以便客户数据保留在其租户中，而不是导出到你的租户中。 这也允许对 Log Analytics 支持的任何资源或服务进行集中监视，从而更灵活地了解所监视的数据类型。

已委派 Azure Lighthouse 订阅的客户可以查看 Azure 活动日志数据，查看所执行的所有操作。 这使客户能够全面了解服务提供商正在执行的操作，以及由用户自己 Azure Active Directory 中的用户 (Azure AD) 租户中的用户完成的操作。

- [如何使用 Azure Monitor 收集平台日志和指标](../azure-monitor/platform/diagnostic-settings.md) 

- [了解 Azure 中的日志记录和不同的日志类型](../azure-monitor/platform/platform-logs-overview.md)

- [大规模监视委托的资源](how-to/monitor-at-scale.md)

- [查看服务提供商活动](how-to/view-service-provider-activity.md)

**Azure 安全中心监视**：是

**责任**：共享

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5：集中管理和分析安全日志

**指导**：集中记录存储和分析来实现关联。 对于每个日志源，请确保已分配数据所有者、访问指南、存储位置、用于处理和访问数据的工具以及数据保留要求。

确保将 Azure 活动日志集成到中央日志记录。 通过 Azure Monitor 引入日志，以聚合终结点设备、网络资源和其他安全系统生成的安全数据。 在 Azure Monitor 中，使用 Log Analytics 工作区来查询和执行分析，并使用 Azure 存储帐户进行长期存档存储。

另外，请启用 Azure Sentinel 或第三方 SIEM 并将数据载入其中。

使用 Azure Lighthouse，你可以通过可缩放的方式在你管理的客户租户中使用 Azure Monitor 日志。 直接在客户租户中创建 Log Analytics 工作区，以便客户数据保留在其租户中，而不是导出到你的租户中。 这也允许对 Log Analytics 支持的任何资源或服务进行集中监视，从而更灵活地了解所监视的数据类型。

已委派 Azure Lighthouse 订阅的客户可以查看 Azure 活动日志数据，查看所执行的所有操作。 这使客户能够全面了解服务提供商正在执行的操作，以及由用户自己 Azure Active Directory 中的用户 (Azure AD) 租户中的用户完成的操作。

许多组织选择将 Azure Sentinel 用于频繁使用的“热”数据，并将 Azure 存储用于不太频繁使用的“冷”数据。

- [如何使用 Azure Monitor 收集平台日志和指标](../azure-monitor/platform/diagnostic-settings.md)

- [大规模监视委托的资源](how-to/monitor-at-scale.md)

- [客户如何查看服务提供商活动](how-to/view-service-provider-activity.md)

- [大规模管理 Azure Sentinel 工作区](how-to/manage-sentinel-workspaces.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="lt-6-configure-log-storage-retention"></a>LT-6：配置日志存储保留期

**指南**： Azure Lighthouse 当前不会生成任何与安全相关的日志。 要查看服务提供商活动的客户可以根据符合性、法规和业务要求配置日志保留。 

在 Azure Monitor 中，可根据组织的合规性规则设置 Log Analytics 工作区保持期。 将 Azure 存储、Data Lake 或 Log Analytics 工作区帐户用于长期存储和存档存储。

- [更改 Log Analytics 中的数据保留期](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

- [如何为 Azure 存储帐户日志配置保留策略](../storage/common/storage-monitor-storage-account.md#configure-logging)

- [Azure 安全中心警报和建议导出](../security-center/continuous-export.md) 配置，并且客户无法设置任何日志保留期。

- [客户如何查看服务提供商的活动日志数据](how-to/view-service-provider-activity.md)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="incident-response"></a>事件响应

[有关详细信息，请参阅 *Azure 安全基线：* 事件响应](../security/benchmarks/security-controls-v2-incident-response.md)。

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1：准备 - 更新 Azure 的事件响应流程

**指导**：确保组织具有响应安全事件的流程，已为 Azure 更新这些流程，并定期运用这些流程来确保就绪性。

- [在企业环境中实现安全性](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [事件响应参考指南](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2：准备 - 设置事件通知

**指导**：在 Azure 安全中心中设置安全事件联系人信息。 如果 Microsoft 安全响应中心 (MSRC) 发现非法或未经授权的一方访问了你的数据，Microsoft 将使用此联系信息来与你取得联系。 还可以选择基于事件响应需求在不同的 Azure 服务中自定义事件警报和通知。 

- [如何设置 Azure 安全中心安全联系人](../security-center/security-center-provide-security-contact-details.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3：检测和分析 - 基于高质量警报创建事件

**指导**：确保具有创建高质量警报和衡量警报质量的流程。 这样，你就可以从过去的事件中吸取经验，并为分析人员确定警报的优先级，这样他们就不会浪费时间来处理误报。 

可以基于过去的事件经验、经验证的社区源以及旨在通过融合和关联各种信号源来生成和清理警报的工具构建高质量警报。 

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

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5：检测和分析 - 确定事件优先级

**指南**：根据警报严重性和资产敏感度，为分析人员提供上下文来确定应首要关注哪些事件。 

Azure 安全中心为每条警报分配严重性，方便你根据优先级来确定应该最先调查的警报。 严重性取决于安全中心对调查结果或用于发出警报的分析的可信度，以及对导致警报的活动背后存在恶意意图的可信度级别。

此外，使用标记来标记资源，并创建命名系统来对 Azure 资源进行标识和分类，特别是处理敏感数据的资源。  你的责任是根据发生事件的 Azure 资源和环境的关键性确定修正警报的优先级。

- [Azure 安全中心中的安全警报](../security-center/security-center-alerts-overview.md)

- [使用标记整理 Azure 资源](../azure-resource-manager/management/tag-resources.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6：包含、根除和恢复 - 自动执行事件处理

**指导**：自动执行手动重复性任务来加快响应时间并减轻分析人员的负担。 执行手动任务需要更长的时间，这会导致减慢每个事件的速度，并减少分析人员可以处理的事件数量。 手动任务还会使分析人员更加疲劳，这会增加可导致延迟的人为错误的风险，并降低分析人员专注于复杂任务的工作效率。 使用 Azure 安全中心和 Azure Sentinel 中的工作流自动化功能，可自动触发操作或运行 playbook，对传入的安全警报作出响应。 playbook 执行多项操作，如发送通知、禁用帐户和隔离有问题的网络。 

- [在安全中心配置工作流自动化](../security-center/workflow-automation.md)

- [在 Azure 安全中心设置自动威胁响应](../security-center/tutorial-security-incident.md#triage-security-alerts)

- [在 Azure Sentinel 中设置自动威胁响应](../sentinel/tutorial-respond-threats-playbook.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

## <a name="posture-and-vulnerability-management"></a>安全状况和漏洞管理

有关详细信息，请参阅 [Azure 安全基准：安全状况和漏洞管理](../security/benchmarks/security-controls-v2-posture-vulnerability-management.md)。

### <a name="pv-1-establish-secure-configurations-for-azure-services"></a>PV-1：为所有 Azure 服务建立安全配置 

**指南**： azure Lighthouse 支持 Azure 安全中心提供的以下特定于服务的策略，用于审核和强制执行 azure 资源的配置。 这可以在 Azure 安全中心或 Azure 策略计划中进行配置。

- 允许通过 Azure Lighthouse 管理租户 Id

- 审核是否将作用域委派给管理租户

可以使用 Azure 蓝图，在单个蓝图定义中自动部署和配置服务和应用程序环境，包括 Azure 资源管理器模板、Azure RBAC 控件和策略。

- [Azure Lighthouse 策略](samples/policy-reference.md)

- [教程-创建和管理策略以强制实施符合性](../governance/policy/tutorials/create-and-manage.md) 

- [Azure 蓝图](../governance/blueprints/overview.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="pv-2-sustain-secure-configurations-for-azure-services"></a>PV-2：为所有 Azure 服务维护安全配置

**指南**： azure Lighthouse 支持 Azure 安全中心提供的以下特定于服务的策略，用于审核和强制执行 azure 资源的配置。 这可以在 Azure 安全中心或 Azure 策略计划中进行配置。

- [Azure Lighthouse 策略](samples/policy-reference.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="pv-3-establish-secure-configurations-for-compute-resources"></a>PV-3：为计算资源建立安全配置

**指南**：使用 Azure 安全中心和 azure 策略在所有计算资源（包括 vm、容器和其他）上建立安全配置。

- [如何监视 Azure 安全中心建议](../security-center/security-center-recommendations.md) 

- [安全建议 - 参考指南](../security-center/recommendations-reference.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8：执行定期攻击模拟

**指导**：根据需要，对 Azure 资源进行渗透测试或红队活动，并确保修正所有关键安全发现。
请遵循 Microsoft 云渗透测试互动规则，确保你的渗透测试不违反 Microsoft 政策。 使用 Microsoft 红队演练策略和执行，以及针对 Microsoft 托管云基础结构、服务和应用程序执行现场渗透测试。

- [Azure 中的渗透测试](../security/fundamentals/pen-testing.md)

- [参与的渗透测试规则](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft 云红色组合](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure 安全中心监视**：不适用

**责任**：共享

## <a name="governance-and-strategy"></a>治理和策略

有关详细信息，请参阅 [Azure 安全基准：治理和策略](../security/benchmarks/security-controls-v2-governance-strategy.md)。

### <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1：定义资产管理和数据保护策略 

**指导**：确保为系统和数据的持续监视和保护记录并传达明确的策略。 确定业务关键数据和系统的发现、评估、保护和监视优先级。 

此策略应包括针对以下元素的记录在案的指南、策略和标准： 

-   与业务风险相符的数据分类标准

-   安全组织对风险和资产清单的洞察力 

-   安全组织对 Azure 服务使用的审批 

-   资产在其生命周期中的安全性

-   与组织数据分类相符的必需访问控制策略

-   使用 Azure 原生和第三方数据保护功能

-   传输中数据用例和静态数据用例的数据加密要求

-   合适的加密标准

有关详细信息，请参阅以下资源：
- [Azure 安全体系结构建议 - 存储、数据和加密](/azure/architecture/framework/security/storage-data-encryption?bc=%2fsecurity%2fcompass%2fbreadcrumb%2ftoc.json&toc=%2fsecurity%2fcompass%2ftoc.json)

- [Azure 安全基础知识 - Azure 数据安全、加密和存储](../security/fundamentals/encryption-overview.md)

- [云采用框架 - Azure 数据安全和加密最佳做法](../security/fundamentals/data-encryption-best-practices.md?bc=%2fazure%2fcloud-adoption-framework%2f_bread%2ftoc.json&toc=%2fazure%2fcloud-adoption-framework%2ftoc.json)

- [Azure 安全基准 - 资产管理](../security/benchmarks/security-controls-v2-incident-response.md)

- [Azure 安全基准 - 数据保护](../security/benchmarks/security-controls-v2-data-protection.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2：定义企业分段策略 

**指导**：建立企业范围的策略，以便使用标识、网络、应用程序、订阅、管理组和其他控件的组合来细分对资产的访问。

仔细权衡安全分离需求与为需要彼此通信并访问数据的系统启用日常操作的需求。

确保跨控制类型（包括网络安全、标识和访问模型、应用程序权限/访问模型，以及人机过程控制）一致地实现分段策略。

- [有关 Azure 中的分段策略的指南（视频）](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [有关 Azure 中的分段策略的指南（文档）](/security/compass/governance#enterprise-segmentation-strategy)

- [使网络分段与企业分段策略相匹配](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="gs-3-define-security-posture-management-strategy"></a>GS-3：定义安全状况管理策略

**指导**：持续衡量并缓解你的个人资产及其托管环境的风险。 确定高价值资产和暴露程度高的受攻击面（例如已发布的应用程序、网络入口和出口点、用户和管理员终结点等）的优先级。

- [Azure 安全基准 - 状况和漏洞管理](../security/benchmarks/security-controls-v2-posture-vulnerability-management.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4：协调组织角色、职责和责任

**指导**：确保为安全组织中的角色和责任记录并传达明确的策略。 优先考虑提供涉及安全决策的明确责任，对每个人进行共同职责模式培训，并为技术团队传授保护云的技术。

- [Azure 安全最佳做法 1 - 人员：针对云安全历程培训团队](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey)

- [Azure 安全最佳做法 2 - 人员：针对云安全技术培训团队](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology)

- [Azure 安全最佳做法 3 - 流程：针对云安全决策分配责任](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="gs-5-define-network-security-strategy"></a>GS-5：定义网络安全策略

**指导**：建立 Azure 网络安全方法，作为组织整体安全访问控制策略的一部分。  

此策略应包括针对以下元素的记录在案的指南、策略和标准： 

-   集中化的网络管理和安全职责

-   符合企业分段策略的虚拟网络分段模型

-   各种威胁和攻击场景中的补救策略

-   Internet 边缘和入口和出口战略/azure/security/benchmarks/security-controls-v2-logging-threat-detection
-   混合云和本地互连策略

-   最新的网络安全项目（例如网络关系图、参考网络体系结构）

有关详细信息，请参阅以下资源：
- [Azure 安全最佳做法 11 - 体系结构。单一的统一安全策略](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Azure 安全基准 - 网络安全](../security/benchmarks/security-controls-v2-logging-threat-detection.md)

- [Azure 网络安全概述](../security/fundamentals/network-overview.md)

- [企业网络体系结构策略](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6：定义标识和特权访问策略

**指导**：建立 Azure 标识和特权访问方法，作为组织整体安全访问控制策略的一部分。  

此策略应包括针对以下元素的记录在案的指南、策略和标准： 

-   集中化的标识和身份验证系统及其与其他内部和外部标识系统的互连

-   各种用例和条件中的强身份验证方法

-   保护权限高的用户

-   异常用户活动监视和处理  

-   用户标识和访问评审及协调流程

有关详细信息，请参阅以下资源：

- [Azure 安全基准 - 标识管理](../security/benchmarks/security-controls-v2-identity-management.md)

- [Azure 安全基准 - 特权访问](../security/benchmarks/security-controls-v2-privileged-access.md)

- [Azure 安全最佳做法 11 - 体系结构。单一的统一安全策略](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Azure 标识管理安全概述](../security/fundamentals/identity-management-overview.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7：定义日志记录和威胁响应策略

**指导**：建立日志记录和威胁响应策略，以快速检测和修正威胁，同时满足合规性要求。 优先为分析人员提供高质量警报和无缝体验，以便他们能够专注于威胁而不是集成和手动步骤。 

此策略应包括针对以下元素的记录在案的指南、策略和标准： 

-   安全运营 (SecOps) 组织的角色和职责 

-   符合 NIST 或其他行业框架要求的明确定义的事件响应流程 

-   日志捕获和保留，用于支持威胁检测、事件响应和合规性需求

-   使用 SIEM、原生 Azure 功能和其他源，集中查看和关联有关威胁的信息 

-   与客户、供应商和公开的利益相关方之间的通信和通知计划

-   使用 Azure 原生的和第三方的平台进行事件处理，例如日志记录和威胁检测、取证以及攻击补救和根除

-   处理事件和事件后活动的流程，例如经验教训和证据保留

有关详细信息，请参阅以下资源：

- [Azure 安全基准 - 日志记录和威胁检测](../security/benchmarks/security-controls-v2-logging-threat-detection.md)

- [Azure 安全基准 - 事件响应](../security/benchmarks/security-controls-v2-incident-response.md)

- [Azure 安全最佳做法 4 - 流程。更新云的事件响应流程](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Azure 采用框架、日志记录和报告决策指南](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Azure 企业规模、管理和监视](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="next-steps"></a>后续步骤

- 参阅 [Azure 安全基准 V2 概述](../security/benchmarks/overview.md)
- 详细了解 [Azure 安全基线](../security/benchmarks/security-baselines-overview.md)