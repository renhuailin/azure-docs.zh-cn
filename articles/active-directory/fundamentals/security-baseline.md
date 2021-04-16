---
title: Azure Active Directory 的 Azure 安全基线
description: Azure Active Directory 安全基线为实现 Azure 安全基准中指定的安全建议提供过程指导和资源。
author: msmbaldwin
ms.service: active-directory
ms.topic: conceptual
ms.date: 04/07/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 8d4203b7b5d7742bf198778864fa4f42b7423596
ms.sourcegitcommit: c6a2d9a44a5a2c13abddab932d16c295a7207d6a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/09/2021
ms.locfileid: "107285997"
---
# <a name="azure-security-baseline-for-azure-active-directory"></a>Azure Active Directory 的 Azure 安全基线

此安全基线将 [Azure 安全基准 2.0 版](../../security/benchmarks/overview.md)中的指南应用到 Azure Active Directory。 Azure 安全基准提供有关如何在 Azure 上保护云解决方案的建议。 内容按“安全控制”分组，这些控制是按适用于 Azure Active Directory 的 Azure 安全基准和相关的指导定义的。 

> [!NOTE]
> 此基线排除了不适用于 Azure Active Directory 或属于 Microsoft 职责范围的控制。 若要了解 Azure Active Directory 如何完全映射到 Azure 安全基准，请参阅[完整的 Azure Active Directory 安全基线映射文件](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)。

## <a name="network-security"></a>网络安全

[有关详细信息，请参阅 *Azure 安全基线：* 网络安全性](/azure/security/benchmarks/security-controls-v2-network-security)。

### <a name="ns-6-simplify-network-security-rules"></a>NS-6：简化网络安全规则

**指导**：使用 Azure 虚拟网络服务标记，在为 Azure Active Directory 资源配置的网络安全组或 Azure 防火墙上定义网络访问控制。 创建安全规则时，可以使用服务标记代替特定的 IP 地址。 通过在规则的相应“源”或“目标”字段中指定服务标记名称（例如“AzureActiveDirectory”），可允许或拒绝相应服务的流量。 Microsoft 会管理服务标记包含的地址前缀，并会在地址发生更改时自动更新服务标记。 

- [了解并使用服务标记](../../virtual-network/service-tags-overview.md)

**责任**：客户

Azure 安全中心监视：无

## <a name="identity-management"></a>标识管理

有关详细信息，请参阅 [Azure 安全基准：标识管理](/azure/security/benchmarks/security-controls-v2-identity-management)。

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1：将 Azure Active Directory 标准化为中央标识和身份验证系统

**指导**：使用 Azure Active Directory (Azure AD) 作为默认标识和访问管理服务。 你应该使 Azure AD 标准化，以便控制组织在以下资源中的标识和访问管理： 
- Microsoft 云资源，如 Azure 门户、Azure 存储、Azure 虚拟机（Linux 和 Windows）、Azure Key Vault、PaaS 和 SaaS 应用程序。 
- 你的组织的资源，例如 Azure 上的应用程序，或公司网络资源。 
 

在组织的云安全做法中，应优先处理 Azure AD 保护事宜。 Azure AD 提供标识安全分数，帮助你评估与 Microsoft 的最佳做法建议相关的标识安全状况。 使用评分来估计你的配置与最佳做法建议的匹配程度，并改善你的安全状况。 

Azure AD 支持外部标识，使没有 Microsoft 帐户的用户可以使用其外部标识登录到其应用程序和资源。 

- [Azure Active Directory 中的租赁](../develop/single-and-multi-tenant-apps.md)

- [如何创建和配置 Azure AD 实例](active-directory-access-create-new-tenant.md)

- [使用应用程序的外部标识提供者](/azure/active-directory/b2b/identity-providers)

- [Azure Active Directory 中的标识安全分数是什么](identity-secure-score.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="im-2-manage-application-identities-securely-and-automatically"></a>IM-2：安全且自动地管理应用程序标识

**指导**：对服务或自动化之类的非人工帐户使用 Azure 资源的托管标识，建议使用 Azure 托管标识功能，而不是创建更加强大的人工帐户来访问或执行资源。 可以通过预定义的访问授权规则以原生方式向支持 Azure Active Directory (Azure AD) 身份验证的 Azure 服务/资源进行身份验证，而无需使用在源代码或配置文件中硬编码的凭据。 不能将 Azure 托管标识分配给 Azure AD 资源，但 Azure AD 是使用向其他服务的资源分配的托管标识进行身份验证的机制。

- [Azure 资源的托管标识](../managed-identities-azure-resources/overview.md)

- [支持 Azure 资源托管标识的服务](../managed-identities-azure-resources/services-support-managed-identities.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3：使用 Azure AD 单一登录 (SSO) 进行应用程序访问

**指导**：使用 Azure Active Directory 来提供对 Azure 资源、云应用程序和本地应用程序的标识和访问管理。 此内容包括企业标识（例如员工）以及外部标识（如合作伙伴和供应商）。 这样，单一登录 (SSO) 便可以管理和保护对本地和云中的组织数据和资源的访问。 将所有用户、应用程序和设备连接到 Azure AD，实现无缝的安全访问和更好的可见性和控制。

 
- [了解 Azure AD 的应用程序 SSO](../manage-apps/what-is-single-sign-on.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4：对所有基于 Azure Active Directory 的访问使用强身份验证控制

**指导**：通过 Azure Active Directory，利用多重身份验证 (MFA) 和强无密码身份验证方法支持强身份验证控制。
- 多重身份验证 - 启用 Azure AD MFA，并遵循 Azure 安全中心标识和访问管理建议，以获得 MFA 设置中的一些最佳做法。 可基于登录条件和风险因素，对所有用户、精选用户或在每用户级别强制执行 MFA。
- 无密码身份验证 - 提供三个无密码身份验证选项：Windows Hello 企业版、Microsoft Authenticator 应用和本地身份验证方法（如智能卡）。

对于管理员和特权用户，请确保使用最高级别的强身份验证方法，然后将相应的强身份验证策略推广到其他用户。

Azure AD 支持旧式基于密码的身份验证，例如采用基线密码策略的纯云帐户（直接在 Azure AD 中创建的用户帐户），或采用本地密码策略的混合帐户（在本地 Active Directory 中创建的用户帐户）。 使用基于密码的身份验证时，Azure AD 提供了密码保护功能，以防止用户设置容易猜出的密码。 Microsoft 提供了基于遥测进行更新的受禁密码的全局列表，客户可以根据自己的需求（例如品牌、文化参考等）来补充列表。 此密码保护可用于纯云帐户和混合帐户。

单纯基于密码凭据的身份验证容易遭受常见的攻击方法攻击。 为了提高安全性，请使用强身份验证，例如 MFA 和强密码策略。 对于可能具有默认密码的第三方应用程序和市场服务，应在初次设置服务时更改这些设置。

 
- [如何部署 Azure AD MFA](../authentication/howto-mfa-getstarted.md) 

 

 
- [Azure Active Directory 的无密码身份验证选项简介](../authentication/concept-authentication-passwordless.md) 

 

 
- [Azure AD 默认密码策略](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-policy#password-policies-that-only-apply-to-cloud-user-accounts)

- [使用 Azure AD 密码保护来消除错误密码](../authentication/concept-password-ban-bad.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5：监视并提醒帐户异常

**指导**：Azure Active Directory 提供以下数据源：

 
- 登录 - 在登录报告中，可了解托管应用程序的使用情况和用户登录活动。

 
- 审核日志 - 通过日志为 Azure AD 中的各种功能所做的所有更改提供可跟踪性。 审核日志的示例包括对 Azure AD 中的任何资源（例如添加或删除用户、应用、组、角色和策略）所做的更改。

 
- 风险登录 - 风险登录是指可能由非用户帐户合法拥有者进行的登录尝试。

 
- 已标记为存在风险的用户 - 风险用户是指可能已泄露的用户帐户。

 

这些数据源可与 Azure Monitor、Azure Sentinel 或第三方 SIEM 系统集成。

 

 
Azure 安全中心还可针对某些可疑活动发出警报，例如，失败的身份验证尝试次数过多，使用了订阅中的已弃用帐户。

 

 
Azure 高级威胁防护 (ATP) 是一种安全解决方案，它可使用 Active Directory 信号来识别、检测和调查高级威胁、泄露的标识以及恶意的内部操作。

 

 
- [Azure Active Directory 中的“审核活动”报表](../reports-monitoring/concept-audit-logs.md) 

 

 
- [如何查看 Azure AD 风险登录](/azure/active-directory/reports-monitoring/concept-risky-sign-ins) 

 

 
- [如何确定标记为存在风险活动的 Azure AD 用户](/azure/active-directory/reports-monitoring/concept-user-at-risk) 

 

 
- [如何在 Azure 安全中心内监视用户的标识和访问活动](../../security-center/security-center-identity-access.md) 

 

 
- [Azure 安全中心的威胁情报保护模块中的警报](../../security-center/alerts-reference.md) 

 

 
- [如何将 Azure 活动日志集成到 Azure Monitor](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6：基于条件限制 Azure 资源访问

**指导**：使用 Azure Active Directory (Azure AD) 条件访问，以实现基于用户定义的条件进行更精细的访问控制，例如，从特定 IP 范围登录的用户将需要使用 MFA 进行登录。 精细身份验证会话管理策略还可用于不同的用例。

 
- [Azure AD 条件访问概述](../conditional-access/overview.md) 

 

 
- [常用条件访问策略](../conditional-access/concept-conditional-access-policy-common.md) 

 

 
- [使用条件访问配置身份验证会话管理](../conditional-access/howto-conditional-access-session-lifetime.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="im-8-secure-user-access-to-legacy-applications"></a>IM-8：保护用户对旧版应用程序的访问

**指导**：确保为旧版应用程序和它们存储和处理的数据提供新式访问控制和会话监视。 虽然通常使用 VPN 来访问旧版应用程序，但它们通常只有基本的访问控制和有限的会话监视。

 
使用 Azure AD 应用程序代理，你可以将旧版本地应用程序发布到采用 SSO 的远程用户，同时使用 Azure AD 条件访问显式验证远程用户和设备的可信度。

 

 
另外，Microsoft Cloud App Security 是一种云访问安全代理 (CASB) 服务，其提供的控制可用于监视用户的应用程序会话并阻止操作（适用于旧版本地应用程序和云软件即服务 (SaaS) 应用程序）。

 

 
- [Azure Active Directory 应用程序代理](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy#what-is-application-proxy) 

 

 
- [Microsoft Cloud App 安全最佳做法](/cloud-app-security/best-practices)

**责任**：客户

Azure 安全中心监视：无

## <a name="privileged-access"></a>特权访问

有关详细信息，请参阅 [Azure 安全基准：特权访问](/azure/security/benchmarks/security-controls-v2-privileged-access)。

### <a name="pa-1-protect-and-limit-highly-privileged-users"></a>PA-1：保护和限制具有较高权限的用户

**指导**：Azure AD 中最重要的内置角色是全局管理员和特权角色管理员，因为分配到这两种角色的用户可以委托管理员角色：

- 全局管理员：具有此角色的用户可访问 Azure AD 中的所有管理功能，还可访问使用 Azure AD 标识的服务。

- 特权角色管理员：具有此角色的用户可管理 Azure AD 和 Azure AD Privileged Identity Management (PIM) 中的角色分配。 此外，该角色可管理 PIM 和管理单元的各个方面。

如果你使用的是分配了某些特权的自定义角色，则可能有其他关键角色需要进行管理。 此外，你可能还需要将类似的控制措施应用于关键业务资产的管理员帐户。

Azure AD 具有高特权帐户：这些用户和服务主体被直接或间接分配到全局管理员或特权角色管理员角色以及 Azure AD 和 Azure 中的其他高特权角色，或者符合获得这些角色的条件。

请限制高特权帐户的数量并在提升的级别保护这些帐户，因为具有此特权的用户可以直接或间接地读取和修改 Azure 环境中的每个资源。

你可使用Azure AD Privileged Identity Management (PIM) 提供对 Azure 资源和 Azure AD 的实时 (JIT) 特权访问权限。 JIT 仅在用户需要执行特权任务时授予临时权限。 当 Azure AD 组织中存在可疑或不安全的活动时，PIM 还会生成安全警报。

- [Azure AD 中的管理角色权限](/azure/active-directory/users-groups-roles/directory-assign-admin-roles) 

- [使用 Azure Privileged Identity Management 安全警报](../privileged-identity-management/pim-how-to-configure-security-alerts.md) 

- [确保 Azure AD 中混合部署和云部署的特权访问安全性](/azure/active-directory/users-groups-roles/directory-admin-roles-secure)

**责任**：客户

Azure 安全中心监视：无

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2：限制对关键业务型系统的管理访问权限

**指导**：使用 Azure Active Directory Privileged Identity Management 和多重身份验证限制对业务关键系统的管理权限。

- [Privileged Identity Management 对角色激活请求的审批](../privileged-identity-management/azure-ad-pim-approval-workflow.md)

- [多重身份验证和条件访问](../conditional-access/howto-conditional-access-policy-admin-mfa.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3：定期审查和协调用户访问权限

**指导**：定期审查用户帐户访问权限分配，以确保帐户及其访问权限有效，重点关注全局管理员和特权角色管理员等高特权角色。 可以使用 Azure Active Directory (Azure AD) 访问评审来审查 Azure AD 角色和 Azure 角色的组成员身份、对企业应用程序的访问权限以及角色分配。 Azure AD 报告提供日志来帮助发现过时的帐户。 你还可使用 Azure AD Privileged Identity Management 来创建访问评审报表工作流以便于执行评审。

此外，Azure Privileged Identity Management 还可配置为在创建过多管理员帐户时发出警报，并识别过时或配置不正确的管理员帐户。

- [在 Privileged Identity Management (PIM) 中创建对 Azure AD 角色的访问评审](../privileged-identity-management/pim-how-to-start-security-review.md)

- [在 Privileged Identity Management (PIM) 中创建对 Azure 资源角色的访问评审](../privileged-identity-management/pim-resource-roles-start-access-review.md)

- [如何使用 Azure AD 标识和访问评审](../governance/access-reviews-overview.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4：在 Azure AD 中设置紧急访问

**指导**：为了防止意外退出 Azure AD 组织，请设置一个紧急访问帐户，以便在正常管理帐户无法使用时进行访问。 紧急访问帐户通常拥有较高的权限，因此请不要将其分配给特定的个人。
紧急访问帐户只能用于“不受限”紧急情况，即不能使用正常管理帐户的情况。

应确保妥善保管紧急访问帐户的凭据（例如密码、证书或智能卡），仅将其告诉只能在紧急情况下有权使用它们的个人。

- [在 Azure AD 中管理紧急访问帐户](/azure/active-directory/users-groups-roles/directory-emergency-access)

**责任**：客户

Azure 安全中心监视：无

### <a name="pa-5-automate-entitlement-management"></a>PA-5：将权利管理自动化 

**指导**：使用 Azure AD 的权利管理功能可自动执行访问请求工作流，包括访问权限分配、审查和过期。 还支持两阶段或多阶段审批。

- [什么是 Azure AD 权利管理](../governance/entitlement-management-overview.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6：使用特权访问工作站

**指导**：安全的独立工作站对于确保敏感角色（如管理员、开发人员和关键服务操作员）的安全至关重要。 使用高度安全的用户工作站和/或 Azure Bastion 执行管理任务。 使用 Azure Active Directory、Microsoft Defender 高级威胁防护 (ATP) 和/或 Microsoft Intune 部署安全的托管用户工作站，用于执行管理任务。 可通过集中管理安全的工作站来强制实施安全配置，包括强身份验证、软件和硬件基线、受限的逻辑和网络访问。

- [在特权访问过程中保护设备](/security/compass/privileged-access-devices)

- [特权访问实现](/security/compass/privileged-access-deployment)

**责任**：客户

Azure 安全中心监视：无

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7：遵循 Just Enough Administration（最小特权原则） 

**指导**：通过将用户分配到具有用户完成管理任务所需最低权限的角色，客户可以为其 Azure Active Directory (Azure AD) 部署配置最小特权。

- [Azure AD 中的管理角色权限](../roles/permissions-reference.md)

- [在 Azure AD 中分配管理角色](../roles/manage-roles-portal.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="pa-8-choose-approval-process-for-microsoft-support"></a>PA-8：选择 Microsoft 支持的审批流程  

**指导**：Azure Active Directory 不支持客户密码箱。 Microsoft 可以通过非密码箱方式与客户协作，以便获得批准来访问客户数据。

**责任**：共享

Azure 安全中心监视：无

## <a name="data-protection"></a>数据保护

[有关详细信息，请参阅 *Azure 安全基线：* 数据保护](/azure/security/benchmarks/security-controls-v2-data-protection)。

### <a name="dp-2-protect-sensitive-data"></a>DP-2：保护敏感数据

**指导**：考虑按照下面的指南来实现对敏感数据的保护：

- **隔离：** 目录是 Azure Active Directory (Azure AD) 服务用来存储和处理客户数据的数据边界。 客户应在目录中设置“国家/地区”属性，以便确定其 Azure AD 客户数据最应该驻留的位置。

- **分段：** 全局管理员角色可以完全控制所有目录数据，以及用于管理访问和处理指令的规则。 一个目录可以被分割成若干个管理单元，并可以预配由这些单元的管理员管理的用户和组。全局管理员可以向组织中的其他主体分配预定义的角色或他们可创建的自定义角色，以便将职责委托给这些主体。

 
- **访问权限：** 可对用户、组、角色、应用程序或设备应用权限。 此分配可以是永久性的，也可以是临时性的，具体取决于 Privileged Identity Management 配置。 
  
- **加密：** Azure AD 对所有静态或传输中的数据进行加密。 此服务不允许客户使用其自己的加密密钥对目录数据进行加密。 

若要了解所选国家/地区到目录的物理位置的具体映射，请参阅“数据所在位置”一文。

- [数据所在位置](https://www.microsoft.com/trust-center/privacy/data-location)文章

客户会使用各种 Azure AD 工具、功能和应用程序来与其目录交互，请参阅“Azure Active Directory - 数据所在位置”一文

- [“数据所在位置”仪表板](https://msit.powerbi.com/view?r=eyJrIjoiYzEyZTc5OTgtNTdlZS00ZTVkLWExN2ItOTM0OWU4NjljOGVjIiwidCI6IjcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0NyIsImMiOjV9)

- [Azure AD 角色文档](/azure/active-directory/roles/)

**责任**：客户

Azure 安全中心监视：无

### <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4：加密传输中的敏感信息

**指导**：为了对访问控制进行补充，应该对传输中的数据使用加密技术防止“带外”攻击（例如流量捕获），以确保攻击者无法轻松读取或修改数据。

Azure AD 支持使用 TLS v1.2 或更高版本进行的传输中数据加密。

虽然这对于专用网络上的流量来说是可选的，但对于外部和公共网络上的流量来说，这是至关重要的。 对于 HTTP 流量，请确保连接到 Azure 资源的任何客户端能够协商 TLS v1.2 或更高版本。 对于远程管理，请使用 SSH（适用于 Linux）或 RDP/TLS（适用于 Windows），而不是使用未加密的协议。 应当禁用已过时的 SSL、TLS 和 SSH 版本和协议，以及弱密码。

默认情况下，Azure 为在 Azure 数据中心之间传输的数据提供加密。

- [了解 Azure 传输中的加密](https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit) 

- [有关 TLS 安全性的信息](/security/engineering/solving-tls1-problem) 

- [传输中的 Azure 数据的双重加密](https://docs.microsoft.com/azure/security/fundamentals/double-encryption#data-in-transit)

**责任**：客户

Azure 安全中心监视：无

## <a name="asset-management"></a>资产管理

有关详细信息，请参阅 [Azure 安全基准：资产管理](/azure/security/benchmarks/security-controls-v2-asset-management)。

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1：确保安全团队可以了解与资产相关的风险

**指南**：确保在 Azure 租户和订阅中向安全团队授予了安全读取者权限，以便他们可以使用 Azure 安全中心监视安全风险。 

根据安全团队责任划分方式的不同，监视安全风险可能是中心安全团队或本地团队的责任。 也就是说，安全见解和风险必须始终在组织内集中聚合。 

安全读取者权限可以广泛应用于整个租户（根管理组），也可以限制到管理组或特定订阅。 

若要了解工作负载和服务，可能需要更多权限。 

- [安全读取者角色概述](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#security-reader)

- [Azure 管理组概述](../../governance/management-groups/overview.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5：限制用户与 Azure 资源管理器进行交互的能力

**指导**：通过为“Microsoft Azure 管理”应用配置“阻止访问”，使用 Azure Active Directory (Azure AD) 条件访问来限制用户通过 Azure 门户与 Azure AD 交互的功能。

- [如何配置条件访问来阻止对 Azure 资源管理器的访问](../../role-based-access-control/conditional-access-azure-management.md)

**责任**：客户

Azure 安全中心监视：无

## <a name="logging-and-threat-detection"></a>日志记录和威胁检测

有关详细信息，请参阅 [Azure 安全基准：日志记录和威胁检测](/azure/security/benchmarks/security-controls-v2-logging-threat-protection)。

### <a name="lt-1-enable-threat-detection-for-azure-resources"></a>LT-1：为 Azure 资源启用威胁检测

**指导**：将 Azure Active Directory (Azure AD) 标识保护内置的威胁检测功能用于 Azure AD 资源。 
 
 
 

 
Azure AD 会生成活动日志，这些日志通常用于检测和搜寻威胁。 Azure AD 登录日志会记录用户、服务和应用的身份验证和授权活动。 Azure AD 审核日志会跟踪对 Azure AD 租户的更改，其中包括会提升或降低安全状况的更改。

- [Azure Active Directory 标识保护是什么？](../identity-protection/overview-identity-protection.md)

- [将 Azure AD 标识保护数据连接到 Azure Sentinel](../../sentinel/connect-azure-ad-identity-protection.md)

- [将 Azure Active Directory 数据连接到 Azure Sentinel](../../sentinel/connect-azure-active-directory.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2：启用 Azure 标识和访问管理的威胁检测

**指导**：Azure Active Directory (Azure AD) 提供以下用户日志，这些日志可在 Azure AD 报告中查看，也可与 Azure Monitor、Azure Sentinel 或其他 SIEM/监视工具集成，以用于更复杂的监视和分析用例： 
- 登录 - 登录报告提供有关托管应用程序的使用情况和用户登录活动的信息。 
- 审核日志 - 通过日志为 Azure AD 中的各种功能所做的所有更改提供可跟踪性。 审核日志的示例包括对 Azure AD 中的任何资源（例如添加或删除用户、应用、组、角色和策略）所做的更改。 
- 风险登录 - 风险登录是指可能由非用户帐户合法拥有者进行的登录尝试。 
- 有风险的用户 - 有风险的用户指示可能已被入侵的用户帐户。 

默认会启用标识保护风险检测，并且无需执行加入过程。 粒度或风险数据是由许可证 SKU 决定的。 

- [Azure Active Directory 中的“审核活动”报表](../reports-monitoring/concept-audit-logs.md)  

- [启用 Azure 标识保护](../identity-protection/overview-identity-protection.md)  

- [Azure 安全中心的威胁防护](/azure/security-center/threat-protection)

**责任**：客户

Azure 安全中心监视：无

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4：为 Azure 资源启用日志记录

**指导**：Azure Active Directory (Azure AD) 生成活动日志。 与某些 Azure 服务不同，Azure AD 并没有对活动日志和资源日志进行区分。 Azure 门户的 Azure AD 部分中自动提供活动日志，这些日志可导出到 Azure Monitor、Azure 事件中心、Azure 存储、SIEM 和其他位置。
 
- 登录 - 登录报告提供有关托管应用程序的使用情况和用户登录活动的信息。  
 
- 审核日志 - 通过日志为 Azure AD 中的各种功能所做的所有更改提供可跟踪性。 审核日志的示例包括对 Azure AD 中的任何资源（例如添加或删除用户、应用、组、角色和策略）所做的更改。  
 
 

Azure AD 还提供与安全相关的日志，你可在 Azure AD 报表中查看这些日志，也可将这些日志与 Azure Monitor、Azure Sentinel 或其他 SIEM/监视工具集成，以用于更复杂的监视和分析用例： 
- 风险登录 - 风险登录是指可能由非用户帐户合法拥有者进行的登录尝试。
- 有风险的用户 - 有风险的用户指示可能已被入侵的用户帐户。 

默认会启用标识保护风险检测，并且无需执行加入过程。 粒度或风险数据是由许可证 SKU 决定的。 

 
- [Azure Active Directory 中的活动和安全性报表](../reports-monitoring/overview-reports.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5：集中管理和分析安全日志

**指导**：如果客户需要将安全日志集中在一起，以便简化威胁搜寻和安全状况分析过程，我们建议遵循以下准则：
 
- 集中记录存储和分析来实现关联。 对于 Azure AD 内的每个日志源，请确保已分配数据所有者、访问指南、存储位置、用于处理和访问数据的工具以及数据保留要求。  
 
- 确保正在将 Azure 活动日志集成到中央日志记录。 通过 Azure Monitor 引入日志，以聚合终结点设备、网络资源和其他安全系统生成的安全数据。 在 Azure Monitor 中，使用 Log Analytics 工作区来查询和执行分析，并使用 Azure 存储帐户进行长期存档存储。  
 
- 另外，请启用 Azure Sentinel 或第三方 SIEM 并将数据载入其中。  
 

许多组织选择将 Azure Sentinel 用于频繁使用的“热”数据，并将 Azure 存储用于不太频繁使用的“冷”数据。  
 

- [如何使用 Azure Monitor 收集平台日志和指标](/azure/azure-monitor/platform/diagnostic-settings)   
 

- [如何加入 Azure Sentinel](../../sentinel/quickstart-onboard.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="lt-6-configure-log-storage-retention"></a>LT-6：配置日志存储保留期

**指导**：确保用于存储 Azure Active Directory 登录日志、审核日志和风险数据日志的所有存储帐户或 Log Analytics 工作区都根据组织的合规性规定设置了日志保留期。

在 Azure Monitor 中，可根据组织的合规性规则设置 Log Analytics 工作区保持期。 将 Azure 存储、Data Lake 或 Log Analytics 工作区帐户用于长期存储和存档存储。

- [如何配置 Log Analytics 工作区保留期](/azure/azure-monitor/platform/manage-cost-storage)  

- [在 Azure 存储帐户中存储资源日志](/azure/azure-monitor/platform/resource-logs-collect-storage)

**责任**：客户

Azure 安全中心监视：无

## <a name="incident-response"></a>事件响应

[有关详细信息，请参阅 *Azure 安全基线：* 事件响应](/azure/security/benchmarks/security-controls-v2-incident-response)。

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1：准备 - 更新 Azure 的事件响应流程

**指导**：确保组织具有响应安全事件的流程，已为 Azure 更新这些流程，并定期运用这些流程来确保就绪性。

- [在企业环境中实现安全性](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [事件响应参考指南](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**责任**：客户

Azure 安全中心监视：无

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2：准备 - 设置事件通知

**指导**：在 Azure 安全中心中设置安全事件联系人信息。 如果 Microsoft 安全响应中心 (MSRC) 发现非法或未经授权的一方访问了你的数据，Microsoft 将使用此联系信息来与你取得联系。 还可以选择基于事件响应需求在不同的 Azure 服务中自定义事件警报和通知。 

- [如何设置 Azure 安全中心安全联系人](../../security-center/security-center-provide-security-contact-details.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3：检测和分析 - 基于高质量警报创建事件 

**指导**：确保具有创建高质量警报和衡量警报质量的流程。 这样，你就可以从过去的事件中吸取经验，并为分析人员确定警报的优先级，这样他们就不会浪费时间来处理误报。 

可以基于过去的事件经验、经验证的社区源以及旨在通过融合和关联各种信号源来生成和清理警报的工具构建高质量警报。 

Azure 安全中心可跨许多 Azure 资产提供高质量的警报。 可以使用 ASC 数据连接器将警报流式传输到 Azure Sentinel。 借助 Azure Sentinel，可创建高级警报规则来自动生成事件以进行调查。 

使用导出功能导出 Azure 安全中心警报和建议，以帮助识别 Azure 资源的风险。 手动导出或持续导出警报和建议。

- [如何配置导出](../../security-center/continuous-export.md)

- [如何将警报流式传输到 Azure Sentinel](../../sentinel/connect-azure-security-center.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4：检测和分析 - 调查事件

**指南**：确保分析人员在调查潜在事件时可查询和使用不同的数据源，以全面了解所发生的情况。 应收集各种各样的日志，以跟踪整个终止链中潜在攻击者的活动，避免出现盲点。  还应确保收集见解和经验，以供其他分析人员使用和用作将来的历史参考资料。  

用于调查的数据源包括已从作用域内服务和正在运行的系统中收集的集中式日志记录源，但还可以包括以下内容：

- 网络数据 - 使用网络安全组的流日志、Azure 网络观察程序和 Azure Monitor 来捕获网络流日志和其他分析信息。 

- 正在运行的系统的快照： 

    - 使用 Azure 虚拟机的快照功能创建正在运行的系统磁盘的快照。 

    - 使用操作系统的本机内存转储功能来创建正在运行的系统内存的快照。

    - 使用 Azure 服务的快照功能或软件自带的功能来创建正在运行的系统的快照。

Azure Sentinel 提供几乎针对任何日志源的广泛数据分析，并提供一个事例管理门户来管理事件的整个生命周期。 调查过程中的情报信息可与事件相关联，以便进行跟踪和报告。 

- [Windows 计算机的磁盘快照](../../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [Linux 计算机的磁盘快照](../../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Microsoft Azure 支持诊断信息和内存转储收集](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [使用 Azure Sentinel 调查事件](../../sentinel/tutorial-investigate-cases.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5：检测和分析 - 确定事件优先级

**指南**：根据警报严重性和资产敏感度，为分析人员提供上下文来确定应首要关注哪些事件。 

Azure 安全中心为每条警报分配严重性，方便你根据优先级来确定应该最先调查的警报。 严重性取决于安全中心对调查结果或用于发出警报的分析的确信程度，以及对导致警报的活动背后存在恶意意图的确信程度。

此外，使用标记来标记资源，并创建命名系统来对 Azure 资源进行标识和分类，特别是处理敏感数据的资源。  你的责任是根据发生事件的 Azure 资源和环境的关键性确定修正警报的优先级。

- [Azure 安全中心中的安全警报](../../security-center/security-center-alerts-overview.md)

- [使用标记整理 Azure 资源](/azure/azure-resource-manager/resource-group-using-tags)

**责任**：客户

Azure 安全中心监视：无

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6：包含、根除和恢复 - 自动执行事件处理

**指导**：自动执行手动重复性任务来加快响应时间并减轻分析人员的负担。 执行手动任务需要更长的时间，这会导致减慢每个事件的速度，并减少分析人员可以处理的事件数量。 手动任务还会使分析人员更加疲劳，这会增加可导致延迟的人为错误的风险，并降低分析人员专注于复杂任务的工作效率。 使用 Azure 安全中心和 Azure Sentinel 中的工作流自动化功能，可自动触发操作或运行 playbook，对传入的安全警报作出响应。 playbook 执行多项操作，如发送通知、禁用帐户和隔离有问题的网络。 

- [在安全中心配置工作流自动化](../../security-center/workflow-automation.md)

- [在 Azure 安全中心设置自动威胁响应](https://docs.microsoft.com/azure/security-center/tutorial-security-incident#triage-security-alerts)

- [在 Azure Sentinel 中设置自动威胁响应](../../sentinel/tutorial-respond-threats-playbook.md)

**责任**：客户

Azure 安全中心监视：无

## <a name="posture-and-vulnerability-management"></a>安全状况和漏洞管理

有关详细信息，请参阅 [Azure 安全基准：安全状况和漏洞管理](/azure/security/benchmarks/security-controls-v2-vulnerability-management)。

### <a name="pv-1-establish-secure-configurations-for-azure-services"></a>PV-1：为所有 Azure 服务建立安全配置 

**指导**：Microsoft 标识和访问管理解决方案可帮助 IT 部门保护对本地和云中的应用程序和资源的访问。 组织务必要遵循安全最佳做法，以确保其标识和访问管理实现是安全的，并且对于攻击有较高的可复原能力。 

根据标识和访问管理实现策略，组织应按照 Microsoft 最佳做法的指导，为标识基础结构提供保护。 

此外，与外部合作伙伴协作的组织还应评估和实现适当的管理、安全和合规配置，以降低安全风险并保护敏感资源。

- [Azure 标识管理和访问控制安全最佳实践](../../security/fundamentals/identity-management-best-practices.md)

- [保护标识基础结构的五个步骤](../../security/fundamentals/steps-secure-identity.md)

- [保护 Azure Active Directory 和 Microsoft 365 中的外部协作](secure-external-access-resources.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="pv-2-sustain-secure-configurations-for-azure-services"></a>PV-2：为所有 Azure 服务维护安全配置

**指导**：Microsoft 安全功能分数为组织提供了一种衡量安全状况的方法，并提供了有助于保护组织免受威胁的建议。 建议组织定期审查其安全分数，以便采取建议的改进措施，从而改善标识安全状况。 

- [什么是 Azure Active Directory 中的标识安全分数？](identity-secure-score.md)

- [Microsoft 安全分数](/microsoft-365/security/mtp/microsoft-secure-score)

**责任**：客户

Azure 安全中心监视：无

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8：执行定期攻击模拟

**指导**：根据需要，对 Azure 资源进行渗透测试或红队活动，并确保修正所有关键安全发现。
请遵循 Microsoft 云渗透测试互动规则，确保你的渗透测试不违反 Microsoft 政策。 使用 Microsoft 红队演练策略和执行，以及针对 Microsoft 托管云基础结构、服务和应用程序执行现场渗透测试。

- [Azure 中的渗透测试](../../security/fundamentals/pen-testing.md)

- [参与的渗透测试规则](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft 云红色组合](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**责任**：共享

Azure 安全中心监视：无

## <a name="governance-and-strategy"></a>治理和策略

有关详细信息，请参阅 [Azure 安全基准：治理和策略](/azure/security/benchmarks/security-controls-v2-governance-strategy)。

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
- [Azure 安全体系结构建议 - 存储、数据和加密](/azure/architecture/framework/security/storage-data-encryption)
 

 
- [Azure 安全基础知识 - Azure 数据安全、加密和存储](../../security/fundamentals/encryption-overview.md) 

 
- [云采用框架 - Azure 数据安全和加密最佳做法](../../security/fundamentals/data-encryption-best-practices.md) 

 
- [Azure 安全基准 - 资产管理](/azure/security/benchmarks/security-benchmark-v2-asset-management) 

 
- [Azure 安全基准 - 数据保护](/azure/security/benchmarks/security-benchmark-v2-data-protection)

**责任**：客户

Azure 安全中心监视：无

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2：定义企业分段策略 

**指导**：建立企业范围的策略，以便使用标识、网络、应用程序、订阅、管理组和其他控件的组合来细分对资产的访问。

仔细权衡安全分离需求与为需要彼此通信并访问数据的系统启用日常操作的需求。

确保跨控制类型（包括网络安全、标识和访问模型、应用程序权限/访问模型，以及人机过程控制）一致地实现分段策略。

- [有关 Azure 中的分段策略的指南（视频）](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [有关 Azure 中的分段策略的指南（文档）](/security/compass/governance#enterprise-segmentation-strategy)

- [使网络分段与企业分段策略相匹配](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**责任**：客户

Azure 安全中心监视：无

### <a name="gs-3-define-security-posture-management-strategy"></a>GS-3：定义安全状况管理策略

**指导**：持续衡量并缓解你的个人资产及其托管环境的风险。 确定高价值资产和暴露程度高的受攻击面（例如已发布的应用程序、网络入口和出口点、用户和管理员终结点等）的优先级。

- [Azure 安全基准 - 状况和漏洞管理](/azure/security/benchmarks/security-benchmark-v2-posture-vulnerability-management)

**责任**：客户

Azure 安全中心监视：无

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4：协调组织角色、职责和责任

**指导**：确保为安全组织中的角色和责任记录并传达明确的策略。 优先考虑提供涉及安全决策的明确责任，对每个人进行共同职责模式培训，并为技术团队传授保护云的技术。

- [Azure 安全最佳做法 1 - 人员：针对云安全历程培训团队](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey)

- [Azure 安全最佳做法 2 - 人员：针对云安全技术培训团队](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology)

- [Azure 安全最佳做法 3 - 流程：针对云安全决策分配责任](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

**责任**：客户

Azure 安全中心监视：无

### <a name="gs-5-define-network-security-strategy"></a>GS-5：定义网络安全策略

**指导**：建立 Azure 网络安全方法，作为组织整体安全访问控制策略的一部分。  

此策略应包括针对以下元素的记录在案的指南、策略和标准： 

-   集中化的网络管理和安全职责

-   符合企业分段策略的虚拟网络分段模型

-   各种威胁和攻击场景中的补救策略

-   Internet 边缘及入口和出口策略

-   混合云和本地互连策略

-   最新的网络安全项目（例如网络关系图、参考网络体系结构）

有关详细信息，请参阅以下资源：
- [Azure 安全最佳做法 11 - 体系结构。单一的统一安全策略](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Azure 安全基准 - 网络安全](/azure/security/benchmarks/security-benchmark-v2-network-security)

- [Azure 网络安全概述](../../security/fundamentals/network-overview.md)

- [企业网络体系结构策略](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**责任**：客户

Azure 安全中心监视：无

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6：定义标识和特权访问策略

**指导**：建立 Azure 标识和特权访问方法，作为组织整体安全访问控制策略的一部分。  

此策略应包括针对以下元素的记录在案的指南、策略和标准： 

-   集中化的标识和身份验证系统及其与其他内部和外部标识系统的互连

-   各种用例和条件中的强身份验证方法

-   保护权限高的用户

-   异常用户活动监视和处理  

-   用户标识和访问评审及协调流程

有关详细信息，请参阅以下资源：

- [Azure 安全基准 - 标识管理](/azure/security/benchmarks/security-benchmark-v2-identity-management)

- [Azure 安全基准 - 特权访问](/azure/security/benchmarks/security-benchmark-v2-privileged-access)

- [Azure 安全最佳做法 11 - 体系结构。单一的统一安全策略](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Azure 标识管理安全概述](../../security/fundamentals/identity-management-overview.md)

**责任**：客户

Azure 安全中心监视：无

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

- [Azure 安全基准 - 日志记录和威胁检测](/azure/security/benchmarks/security-benchmark-v2-logging-threat-detection)

- [Azure 安全基准 - 事件响应](/azure/security/benchmarks/security-benchmark-v2-incident-response)

- [Azure 安全最佳做法 4 - 流程。更新云的事件响应流程](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Azure 采用框架、日志记录和报告决策指南](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Azure 企业规模、管理和监视](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**责任**：客户

Azure 安全中心监视：无

## <a name="next-steps"></a>后续步骤

- 参阅 [Azure 安全基准 V2 概述](/azure/security/benchmarks/overview)
- 详细了解 [Azure 安全基线](/azure/security/benchmarks/security-baselines-overview)
