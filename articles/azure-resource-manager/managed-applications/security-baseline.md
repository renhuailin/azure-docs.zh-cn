---
title: Azure 托管应用程序的 azure 安全基线
description: Azure 托管应用程序安全基准为实现 Azure 安全基准中指定的安全建议提供过程指南和资源。
author: msmbaldwin
ms.service: managed-applications
ms.topic: conceptual
ms.date: 12/01/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 9183fbfd6b4b90ee98917c91709b154ab0da9be7
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2020
ms.locfileid: "96501404"
---
# <a name="azure-security-baseline-for-azure-managed-applications"></a>Azure 托管应用程序的 azure 安全基线

此安全基线将 [Azure 安全基准版本 2.0](../../security/benchmarks/overview.md) 中的指南应用于 Azure 托管应用程序。 Azure 安全基准提供有关如何在 Azure 上保护云解决方案的建议。 内容由 Azure 安全基准定义的 **安全控制** 和适用于 Azure 托管应用程序的相关指南进行分组。 排除了不适用于 Azure 托管应用程序的 **控件**。

若要查看 Azure 托管应用程序如何完全映射到 Azure 安全基准，请参阅 [完整的 Azure 托管应用程序安全基线映射文件](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)。

## <a name="network-security"></a>网络安全

[有关详细信息，请参阅 *Azure 安全基线：* 网络安全性](/azure/security/benchmarks/security-controls-v2-network-security)。

### <a name="ns-6-simplify-network-security-rules"></a>NS-6：简化网络安全规则

**指南**：使用 Azure 虚拟网络服务标记定义针对 Azure 托管应用程序资源配置的网络安全组或 azure 防火墙上的网络访问控制。 创建安全规则时，可以使用服务标记代替特定的 IP 地址。 通过在规则的相应 "源" 或 "目标" 字段中指定服务标记名称 (例如： AzureResourceManager) ，可以允许或拒绝相应服务的流量。 Microsoft 会管理服务标记包含的地址前缀，并会在地址发生更改时自动更新服务标记。

- [使用 Azure 资源管理器服务标记](../../virtual-network/service-tags-overview.md#available-service-tags)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="identity-management"></a>标识管理

有关详细信息，请参阅 [Azure 安全基准：标识管理](/azure/security/benchmarks/security-controls-v2-identity-management)。

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1：将 Azure Active Directory 标准化为中央标识和身份验证系统

**指南**： Azure 托管应用程序使用 Azure Active Directory (Azure AD) 作为默认的标识和访问管理服务。 标准化 Azure AD 以管理组织的标识和访问管理。

Azure 提供以下 Azure 内置角色，用于使用 Azure AD 和 OAuth 授权访问托管应用程序：

- 托管应用程序参与者角色：允许创建托管应用程序资源。

- 托管应用程序操作员角色：允许你读取和执行对托管应用程序资源的操作

- 托管应用程序读者：可用于读取托管应用中的资源，并请求 JIT 访问。

有关详细信息，请参阅以下资源：

- [托管应用程序参与者角色](../../role-based-access-control/built-in-roles.md#managed-application-contributor-role)

- [托管应用程序操作员角色](../../role-based-access-control/built-in-roles.md#managed-application-contributor-role)

- [托管应用程序读者](../../role-based-access-control/built-in-roles.md#managed-applications-reader)

- [Azure Active Directory 中的租赁](../../active-directory/develop/single-and-multi-tenant-apps.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="im-2-manage-application-identities-securely-and-automatically"></a>IM-2：安全且自动地管理应用程序标识

**指南**：使用 Azure 托管的标识向托管应用程序授予权限。 建议使用 Azure 托管标识功能，而不是创建功能更强大的人工帐户来访问或执行资源，以限制管理其他凭据的需求。 还可以向 Azure 托管应用程序服务分配托管标识本身，以对支持 Azure AD 身份验证的其他 Azure 服务/资源进行本机身份验证。 这可用于在检索机密时启用从 Azure 托管应用程序轻松访问 Azure Key Vault。 使用托管标识时，标识由 Azure 平台管理，并且不要求你预配或轮换任何机密。

Azure 托管应用程序支持向应用程序授予两种类型的标识：

- 系统分配的标识将绑定到配置资源。 如果删除配置资源，则会将其删除。 配置资源只能有一个系统分配的标识。

- 用户分配的标识是可分配给配置资源的独立 Azure 资源。 配置资源可以有多个用户分配的标识。

如果无法利用托管标识，请在 Azure 托管应用程序资源级别创建具有受限权限的服务主体。 配置这些具有证书凭据的服务主体并仅回退到客户端密码。 在这两种情况下，都可以将 Azure Key Vault 与 Azure 托管标识结合使用，以便运行时环境 (例如，Azure function) 可以从密钥保管库检索凭据。

- [如何使用 Azure 托管应用程序的托管标识](publish-managed-identity.md)

- [Azure 托管标识](../../active-directory/managed-identities-azure-resources/overview.md)

- [Azure 服务主体](/powershell/azure/create-azure-service-principal-azureps) 

- [使用 Azure Key Vault 进行安全主体注册](../../key-vault/general/authentication.md#app-identity-and-security-principals)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3：使用 Azure AD 单一登录 (SSO) 进行应用程序访问

**指南**： Azure 托管应用程序使用 Azure Active Directory 提供对 Azure 资源、云应用程序和本地应用程序的标识和访问管理。 此内容包括企业标识（例如员工）以及外部标识（如合作伙伴和供应商）。 这样，单一登录 (SSO) 便可以管理和保护对本地和云中的组织数据和资源的访问。 将所有用户、应用程序和设备连接到 Azure AD，实现无缝的安全访问和更好的可见性和控制。

- [了解 Azure AD 的应用程序 SSO](../../active-directory/manage-apps/what-is-single-sign-on.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4：对所有基于 Azure Active Directory 的访问使用强身份验证控制

**指南**： Azure 托管应用程序使用通过多重身份验证和无密码方法支持强身份验证控制的 Azure Active Directory。
- 多重身份验证-启用 Azure AD 多重身份验证，并遵循 Azure 安全中心的标识和访问管理建议，以获取多重身份验证设置中的一些最佳实践。 多重身份验证可在所有用户上强制执行，根据登录条件和风险因素，选择用户或按用户级别。
- 无密码身份验证 - 有三个无密码身份验证选项可用：Windows Hello 企业版、Microsoft Authenticator 应用和本地身份验证方法（如智能卡）。

对于管理员和特权用户，请确保使用的是最高级别的强身份验证。

- [如何在 Azure 中启用多重身份验证](../../active-directory/authentication/howto-mfa-getstarted.md) 

- [Azure Active Directory 的无密码身份验证选项简介](../../active-directory/authentication/concept-authentication-passwordless.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5：监视并提醒帐户异常

**指南**： Azure 托管应用程序与提供以下数据源的 Azure Active Directory 集成：
- 登录 - 在登录报告中，可了解托管应用程序的使用情况和用户登录活动。
- 审核日志 - 通过日志为 Azure AD 中的各种功能所做的所有更改提供可跟踪性。 审核日志的示例包括对 Azure AD 中的任何资源（例如添加或删除用户、应用、组、角色和策略）所做的更改。
- 风险登录 - 风险登录是指可能由非用户帐户合法拥有者进行的登录尝试。
- 已标记为存在风险的用户 - 风险用户是指可能已泄露的用户帐户。

这些数据源可以与 Azure Monitor、Azure Sentinel 或第三方 SIEM 系统集成。

Azure 安全中心还可针对某些可疑活动发出警报，例如，失败的身份验证尝试次数过多，使用了订阅中的已弃用帐户。

Azure 高级威胁防护 (ATP) 是一种安全解决方案，它可使用 Active Directory 信号来识别、检测和调查高级威胁、泄露的标识以及恶意的内部操作。

- [Azure Active Directory 中的“审核活动”报表](../../active-directory/reports-monitoring/concept-audit-logs.md)

- [如何查看 Azure AD 风险登录](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [如何在 Azure 安全中心内监视用户的标识和访问活动](../../security-center/security-center-identity-access.md)

- [如何将 Azure 活动日志集成到 Azure Monitor](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6：基于条件限制 Azure 资源访问

**指南**： Azure 托管应用程序支持基于用户定义的条件对更精细的访问控制进行 Azure AD 条件性访问，如某些 IP 范围中的用户登录需要使用多重身份验证进行登录。 精细身份验证会话管理策略还可用于不同的用例。

- [Azure 条件访问概述](../../active-directory/conditional-access/overview.md) 

- [常见的条件访问策略](../../active-directory/conditional-access/concept-conditional-access-policy-common.md) 

- [使用条件访问配置身份验证会话管理](../../active-directory/conditional-access/howto-conditional-access-session-lifetime.md)

**Azure 安全中心监视**：是

**责任**：客户

## <a name="privileged-access"></a>特权访问

有关详细信息，请参阅 [Azure 安全基准：特权访问](/azure/security/benchmarks/security-controls-v2-privileged-access)。

### <a name="pa-1-protect-and-limit-highly-privileged-users"></a>PA-1：保护和限制具有较高权限的用户

**指南**： Azure 托管应用程序使用 Azure Active Directory (Azure AD) 进行身份验证和访问。 最关键的内置角色 Azure AD 是全局管理员和特权角色管理员，因为分配给这两个角色的用户可以委派管理员角色：
- 全局管理员/公司管理员：具有此角色的用户可访问 Azure AD 中的所有管理功能，还可访问使用 Azure AD 标识的服务。
- 特权角色管理员：具有此角色的用户可管理 Azure AD 和 Azure AD Privileged Identity Management (PIM) 中的角色分配。 此外，该角色可管理 PIM 和管理单元的各个方面。

注意：如果你使用的是分配了某些特权的自定义角色，则可能有其他关键角色需要进行管理。 此外，你可能还需要将类似的控制措施应用于关键业务资产的管理员帐户。

你可使用Azure AD Privileged Identity Management (PIM) 提供对 Azure 资源和 Azure AD 的实时 (JIT) 特权访问权限。 JIT 仅在用户需要执行特权任务时授予临时权限。 当 Azure AD 组织中存在可疑或不安全的活动时，PIM 还会生成安全警报。

- [Azure AD 中的管理角色权限](/azure/active-directory/users-groups-roles/directory-assign-admin-roles)

- [使用 Azure Privileged Identity Management 安全警报](../../active-directory/privileged-identity-management/pim-how-to-configure-security-alerts.md)

- [确保 Azure AD 中混合部署和云部署的特权访问安全性](/azure/active-directory/users-groups-roles/directory-admin-roles-secure)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2：限制对关键业务型系统的管理访问权限

**指南**： Azure 托管应用程序使用 azure RBAC 来隔离对关键业务系统的访问权限，方法是限制授予对这些订阅和管理组的特权访问权限的帐户。

确保你还将访问权限限制为对业务关键访问具有管理访问权限的管理、标识和安全系统，如 Active Directory 域控制器 (DCs) 、安全工具和系统管理工具，以及安装在业务关键系统上的代理。 入侵这些管理和安全系统的攻击者可以立即将它们用作损害业务关键型资产的武器。

所有类型的访问控制都应符合企业分段策略，确保访问控制保持一致。

- [Azure 组件和参考模型](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [访问管理组](../../governance/management-groups/overview.md#management-group-access)

- [Azure 订阅管理员](../../cost-management-billing/manage/add-change-subscription-administrator.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3：定期审查和协调用户访问权限

**指南**：定期查看用户帐户和访问分配，以确保帐户及其访问级别有效。

Azure 托管应用程序使用 Azure Active Directory (AAD) 帐户来管理其资源、定期查看用户帐户和访问分配，以确保帐户及其访问有效。 可使用 Azure AD 访问评审来审查组成员身份、对企业应用程序的访问权限和角色分配。 Azure AD 报告提供日志来帮助发现过时的帐户。 你还可以使用 Azure AD Privileged Identity Management 来创建访问评审报表工作流，以便于审核过程。

此外，还可以将 Azure Privileged Identity Management 配置为在创建过多的管理员帐户时发出警报，并识别过时或配置不正确的管理员帐户。

注意：某些 Azure 服务支持不通过 Azure AD 管理的本地用户和角色。 需要单独管理这些用户。

- [在 Privileged Identity Management (PIM) 中创建对 Azure 资源角色的访问评审](../../active-directory/privileged-identity-management/pim-resource-roles-start-access-review.md) 

- [如何使用 Azure AD 标识和访问评审](../../active-directory/governance/access-reviews-overview.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4：在 Azure AD 中设置紧急访问

**指南**： Azure 托管应用程序使用 Azure Active Directory 管理其资源。 为了防止意外退出 Azure AD 组织，请设置一个紧急访问帐户，以便在正常管理帐户无法使用时进行访问。 紧急访问帐户通常拥有较高的权限，因此请不要将其分配给特定的个人。 紧急访问帐户只能用于“不受限”紧急情况，即不能使用正常管理帐户的情况。

应确保妥善保管紧急访问帐户的凭据（例如密码、证书或智能卡），仅将其告诉只能在紧急情况下有权使用它们的个人。

- [在 Azure AD 中管理紧急访问帐户](../../active-directory/roles/security-emergency-access.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="pa-5-automate-entitlement-management"></a>PA-5：将权利管理自动化 

**指南**： Azure 托管应用程序与 Azure Active Directory 集成，以管理其资源。 使用 Azure AD 的权利管理功能可自动执行访问请求工作流，包括访问权限分配、审查和过期。 还支持两阶段或多阶段审批。

- [什么是 Azure AD 访问评审](../../active-directory/governance/access-reviews-overview.md)

- [什么是 Azure AD 权利管理](../../active-directory/governance/entitlement-management-overview.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6：使用特权访问工作站

**指导**：安全的独立工作站对于确保敏感角色（如管理员、开发人员和关键服务操作员）的安全至关重要。 使用高度安全的用户工作站和/或 Azure 堡垒执行与托管应用程序相关的管理任务。 使用 Azure Active Directory、Microsoft Defender 高级威胁防护 (ATP) 和/或 Microsoft Intune 部署安全的托管用户工作站，用于执行管理任务。 可通过集中管理安全的工作站来强制实施安全配置，包括强身份验证、软件和硬件基线、受限的逻辑和网络访问。

- [了解特权访问工作站](../../active-directory/devices/concept-azure-managed-workstation.md)

- [部署特权访问工作站](../../active-directory/devices/howto-azure-managed-workstation.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7：遵循 Just Enough Administration（最小特权原则） 

**指南**： Azure 托管应用程序与 azure 基于角色的访问控制集成， (RBAC) 管理其资源。 使用 Azure RBAC，可通过角色分配来管理 Azure 资源访问。 可以将这些角色分配给用户、组服务主体和托管标识。 某些资源具有预定义的内置角色，可以通过工具（例如 Azure CLI、Azure PowerShell 或 Azure 门户）来清点或查询这些角色。 通过 Azure RBAC 分配给资源的特权应始终限制为角色所需的特权。 这是对 Azure AD Privileged Identity Management (PIM) 的实时 (JIT) 方法的补充，应定期进行审查。

如果可能，请使用内置角色来分配权限，并且仅在需要时才创建自定义角色。

Azure 提供以下 Azure 内置角色，用于使用 Azure AD 和 OAuth 授权访问托管应用程序：

- 托管应用程序参与者角色：允许创建托管应用程序资源。

- 托管应用程序操作员角色：允许你读取和执行对托管应用程序资源的操作

- 托管应用程序读者：可用于读取托管应用中的资源，并请求 JIT 访问。

有关详细信息，请参阅以下资源：

- [托管应用程序参与者角色](../../role-based-access-control/built-in-roles.md#managed-application-contributor-role)

- [托管应用程序操作员角色](../../role-based-access-control/built-in-roles.md#managed-application-contributor-role)

- [托管应用程序读者](../../role-based-access-control/built-in-roles.md#managed-applications-reader)

- [什么是 Azure 基于角色的访问控制 (Azure RBAC)](../../role-based-access-control/overview.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="pa-8-choose-approval-process-for-microsoft-support"></a>PA-8：选择 Microsoft 支持的批准流程  

**指南**：对 Microsoft 可能需要访问 Azure 托管应用程序数据的支持方案实施组织审批流程。 客户密码箱当前不适用于托管应用程序方案。

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="data-protection"></a>数据保护

[有关详细信息，请参阅 *Azure 安全基线：* 数据保护](/azure/security/benchmarks/security-controls-v2-data-protection)。

### <a name="dp-2-protect-sensitive-data"></a>DP-2：保护敏感数据

**指南**：对于使用自己的密钥进行加密，你可以使用自己的存储帐户来存储托管应用程序配置文件。

- [使用自己的存储对托管配置文件进行数据保护](https://docs.microsoft.com/azure/azure-resource-manager/managed-applications/publish-service-catalog-app?tabs=azure-powershell#bring-your-own-storage-for-the-managed-application-definition)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="dp-5-encrypt-sensitive-data-at-rest"></a>DP-5：加密静态敏感数据

**指南**：定义你的应用程序及其资源的托管应用程序定义可以存储在你自己的存储帐户中，可通过客户管理的加密密钥进行配置。

对于您不希望将自己的存储用于托管应用程序定义的情况，Azure 默认提供静态数据加密。

- [为托管应用程序定义自带存储](https://docs.microsoft.com/azure/azure-resource-manager/managed-applications/publish-service-catalog-app?tabs=azure-powershell#bring-your-own-storage-for-the-managed-application-definition)

- [了解 Azure 中的静态加密](../../security/fundamentals/encryption-atrest.md#encryption-at-rest-in-microsoft-cloud-services) 

- [如何配置客户管理的加密密钥](/azure/storage/common/storage-encryption-keys-portal)

**Azure 安全中心监视**：目前不可用

**责任**：共享

## <a name="asset-management"></a>资产管理

有关详细信息，请参阅 [Azure 安全基准：资产管理](/azure/security/benchmarks/security-controls-v2-asset-management)。

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1：确保安全团队可以了解与资产相关的风险

**指南**：确保在 Azure 租户和订阅中向安全团队授予了安全读取者权限，以便他们可以使用 Azure 安全中心监视安全风险。 

根据安全团队责任划分方式的不同，监视安全风险可能是中心安全团队或本地团队的责任。 也就是说，安全见解和风险必须始终在组织内集中聚合。 

安全读取者权限可以广泛应用于整个租户（根管理组），也可以限制到管理组或特定订阅。 

注意：若要了解工作负载和服务，可能需要更多权限。 

- [安全读取者角色概述](../../role-based-access-control/built-in-roles.md#security-reader)

- [Azure 管理组概述](../../governance/management-groups/overview.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2：确保安全团队有权访问资产清单和元数据

**指南**：将标记应用于 Azure 资源、资源组和订阅，以逻辑方式将它们组织到分类。 每个标记均由名称和值对组成。 例如，可以对生产中的所有资源应用名称“Environment”和值“Production”。

在创建托管应用程序时提供的标记也适用于托管资源组。 应用程序的发布者可以在部署后提供自己的托管资源的其他标记。

- [按资源托管的应用程序 UI 元素标记](microsoft-common-tagsbyresource.md)

- [如何使用 Azure Resource Graph 浏览器创建查询](../../governance/resource-graph/first-query-portal.md) 

- [有关标记资产的详细信息，请参阅资源命名和标记决策指南](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="am-3-use-only-approved-azure-services"></a>AM-3：仅使用已批准的 Azure 服务

**指南**： Azure 托管应用程序使用 azure 策略支持基于 azure 资源管理器的部署和配置强制。 请使用 Azure Policy 来审核和限制用户可以在你的环境中预配哪些服务。 使用 Azure Resource Graph 查询和发现订阅中的资源。 你也可以使用 Azure Monitor 来创建规则，以便在检测到未经批准的服务时触发警报。

- [如何配置和管理 Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

- [如何使用 Azure Policy 拒绝特定的资源类型](../../governance/policy/samples/built-in-policies.md#general)

- [如何使用 Azure Resource Graph 浏览器创建查询](../../governance/resource-graph/first-query-portal.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>AM-4：确保资产生命周期管理的安全

**指南**：可以通过删除托管应用程序资源来删除托管应用程序资源及其连接的托管资源组。 删除托管应用程序资源时，它还会删除该托管资源组及其内容。 其他生命周期功能由应用程序的发布者指定，用户可在其中通过允许的操作为用户授予对底层托管资源的生命周期的其他权限。 有关使用者管理的资源，请咨询托管应用程序的发布者。

- [清理托管应用程序资源](https://docs.microsoft.com/azure/azure-resource-manager/managed-applications/tutorial-create-managed-app-with-custom-provider?tabs=azurecli-interactive#clean-up-resources)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5：限制用户与 Azure 资源管理器进行交互的能力

**指南**：通过为 "Microsoft Azure 管理" 应用配置 "阻止访问"，使用 Azure 条件访问限制用户与 Azure 资源管理器的交互能力。

- [如何配置条件访问来阻止对 Azure 资源管理器的访问](../../role-based-access-control/conditional-access-azure-management.md)

**Azure 安全中心监视**：是

**责任**：客户

## <a name="logging-and-threat-detection"></a>日志记录和威胁检测

有关详细信息，请参阅 [Azure 安全基准：日志记录和威胁检测](/azure/security/benchmarks/security-controls-v2-logging-threat-protection)。

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2：启用 Azure 标识和访问管理的威胁检测

**指南**： Azure AD 提供以下用户日志，这些用户日志可在 Azure AD 报告中查看，或与 Azure Monitor、Azure Sentinel 或其他 SIEM/监视工具集成，以实现更复杂的监视和分析用例：登录–登录报表提供有关托管应用程序和用户登录活动的使用情况的信息。
审核日志 - 通过日志为 Azure AD 中的各种功能所做的所有更改提供可跟踪性。 审核日志的示例包括对 Azure AD 中的任何资源（例如添加或删除用户、应用、组、角色和策略）所做的更改。
风险登录 - 风险登录是指可能由非用户帐户合法拥有者进行的登录尝试。
已标记为存在风险的用户 - 风险用户是指可能已泄露的用户帐户。

Azure 安全中心还可针对某些可疑活动发出警报，例如，失败的身份验证尝试次数过多，使用了订阅中的已弃用帐户。 除了基本的安全机制监视，Azure 安全中心的威胁防护模块还可从各个 Azure 计算资源（虚拟机、容器、应用服务）、数据资源（SQL 数据库和存储）和 Azure 服务层收集更深入的安全警报。 此功能可让你查看单个资源内的帐户异常情况。

- [Azure Active Directory 中的“审核活动”报表](../../active-directory/reports-monitoring/concept-audit-logs.md)

- [启用 Azure 标识保护](../../active-directory/identity-protection/overview-identity-protection.md)

- [Azure 安全中心的威胁防护](/azure/security-center/threat-protection)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4：为 Azure 资源启用日志记录

**指南**：自动可用的活动日志包含对托管应用程序资源 (PUT、POST、DELETE) 的所有写入操作，但读取操作 (获取) 。 活动日志可用于在进行故障排除时查找错误，或监视组织中的用户如何对资源进行修改。

- [如何使用 Azure Monitor 收集平台日志和指标](../../azure-monitor/platform/diagnostic-settings.md) 

- [了解 Azure 中的日志记录和不同的日志类型](../../azure-monitor/platform/platform-logs-overview.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5：集中管理和分析安全日志

**指南**：集中日志记录、存储和分析以启用关联。 对于每个日志源，请确保已分配数据所有者、访问指南、存储位置、用于处理和访问数据的工具以及数据保留要求。
确保正在将 Azure 活动日志集成到中央日志记录。 通过 Azure Monitor 引入日志，以聚合终结点设备、网络资源和其他安全系统生成的安全数据。 在 Azure Monitor 中，使用 Log Analytics 工作区来查询和执行分析，并使用 Azure 存储帐户进行长期存档存储。

此外，启用数据并将数据集成到 Azure Sentinel 或第三方 SIEM。

许多组织选择使用 Azure Sentinel 作为 "热" 数据，这些数据经常使用，而 Azure 存储则用于不频繁使用的 "冷" 数据。

- [如何使用 Azure Monitor 收集平台日志和指标](../../azure-monitor/platform/diagnostic-settings.md)

- [如何加入 Azure Sentinel](../../sentinel/quickstart-onboard.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="lt-6-configure-log-storage-retention"></a>LT-6：配置日志存储保留期

**指南**：确保用于存储托管应用程序资源所创建日志的任何存储帐户或 Log Analytics 工作区的日志保留期都已根据组织的符合性法规进行了设置。
在 Azure Monitor 中，可根据组织的合规性规则设置 Log Analytics 工作区保持期。 使用 Azure 存储、Data Lake 或 Log Analytics 工作区帐户用于长期和存档存储。

- [如何配置 Log Analytics 工作区保留期](../../azure-monitor/platform/manage-cost-storage.md)

- [在 Azure 存储帐户中存储资源日志](/azure/azure-monitor/platform/resource-logs-collect-storage)

**Azure 安全中心监视**：是

**责任**：客户

## <a name="incident-response"></a>事件响应

[有关详细信息，请参阅 *Azure 安全基线：* 事件响应](/azure/security/benchmarks/security-controls-v2-incident-response)。

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1：准备 - 更新 Azure 的事件响应流程

**指导**：确保组织具有响应安全事件的流程，已为 Azure 更新这些流程，并定期运用这些流程来确保就绪性。

- [在企业环境中实现安全性](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [事件响应参考指南](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2：准备 - 设置事件通知

**指导**：在 Azure 安全中心中设置安全事件联系人信息。 如果 Microsoft 安全响应中心 (MSRC) 发现非法或未经授权的一方访问了你的数据，Microsoft 将使用此联系信息来与你取得联系。 还可以选择基于事件响应需求在不同的 Azure 服务中自定义事件警报和通知。 

- [如何设置 Azure 安全中心安全联系人](../../security-center/security-center-provide-security-contact-details.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3：检测和分析 - 基于高质量警报创建事件

**指导**：确保具有创建高质量警报和衡量警报质量的流程。 这样，你就可以从过去的事件中吸取经验，并为分析人员确定警报的优先级，这样他们就不会浪费时间来处理误报。 

可以基于过去的事件经验、经验证的社区源以及旨在通过融合和关联各种信号源来生成和清理警报的工具构建高质量警报。 

Azure 安全中心可跨多个 Azure 资产提供高质量的警报。 可以使用 ASC 数据连接器将警报流式传输到 Azure Sentinel。 借助 Azure Sentinel，可创建高级警报规则来自动生成事件以进行调查。 

使用导出功能导出 Azure 安全中心警报和建议，以帮助识别 Azure 资源的风险。 手动导出或持续导出警报和建议。

- [如何配置导出](../../security-center/continuous-export.md)

- [如何将警报流式传输到 Azure Sentinel](../../sentinel/connect-azure-security-center.md)

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

- [Windows 计算机的磁盘快照](../../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [Linux 计算机的磁盘快照](../../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Microsoft Azure 支持诊断信息和内存转储收集](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [使用 Azure Sentinel 调查事件](../../sentinel/tutorial-investigate-cases.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5：检测和分析 - 确定事件优先级

**指南**：根据警报严重性和资产敏感度，为分析人员提供上下文来确定应首要关注哪些事件。 

Azure 安全中心为每条警报分配严重性，方便你根据优先级来确定应该最先调查的警报。 严重性取决于安全中心在查找或用于发出警报的分析中的置信度，以及导致警报的活动的恶意意图的置信度。

此外，使用标记来标记资源，并创建命名系统来对 Azure 资源进行标识和分类，特别是处理敏感数据的资源。 你的责任是根据发生事件的 Azure 资源和环境的关键性确定修正警报的优先级。

- [Azure 安全中心中的安全警报](../../security-center/security-center-alerts-overview.md)

- [使用标记整理 Azure 资源](/azure/azure-resource-manager/resource-group-using-tags)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6：包含、根除和恢复 - 自动执行事件处理

**指导**：自动执行手动重复性任务来加快响应时间并减轻分析人员的负担。 执行手动任务需要更长的时间，这会导致减慢每个事件的速度，并减少分析人员可以处理的事件数量。 手动任务还会使分析人员更加疲劳，这会增加可导致延迟的人为错误的风险，并降低分析人员专注于复杂任务的工作效率。 

使用 Azure 安全中心和 Azure Sentinel 中的工作流自动化功能，可自动触发操作或运行 playbook，对传入的安全警报作出响应。 playbook 执行多项操作，如发送通知、禁用帐户和隔离有问题的网络。 

- [在安全中心配置工作流自动化](../../security-center/workflow-automation.md)

- [在 Azure 安全中心设置自动威胁响应](../../security-center/tutorial-security-incident.md#triage-security-alerts)

- [在 Azure Sentinel 中设置自动威胁响应](../../sentinel/tutorial-respond-threats-playbook.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

## <a name="posture-and-vulnerability-management"></a>安全状况和漏洞管理

有关详细信息，请参阅 [Azure 安全基准：安全状况和漏洞管理](/azure/security/benchmarks/security-controls-v2-vulnerability-management)。

### <a name="pv-1-establish-secure-configurations-for-azure-services"></a>PV-1：为所有 Azure 服务建立安全配置 

**指南**：通过使基础结构和 DevOps 团队易于安全配置所使用的 Azure 服务，为其定义安全 guardrails。

配置 Azure 策略，以审核和强制实施与托管应用程序部署相关的资源的配置。

你可以使用 Azure 蓝图在单个蓝图定义中自动部署和配置服务和应用程序环境，包括 Azure 资源管理器模板、Azure RBAC 分配和 Azure 策略分配。

- [云采用框架企业级登陆区域](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture#landing-zone-expanded-definition)

- [创建和管理策略以强制实施符合性](../../governance/policy/tutorials/create-and-manage.md)

- [Azure 蓝图](../../governance/blueprints/overview.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="pv-2-sustain-secure-configurations-for-azure-services"></a>PV-2：为所有 Azure 服务维护安全配置

**指南**：使用 Azure 安全中心监视与 Azure 托管应用程序相关的资源，并使用 azure 策略 [拒绝] 和 [部署（如果不存在]）效果来维持安全配置。
- [了解 Azure Policy 效果](../../governance/policy/concepts/effects.md)

- [创建和管理策略以强制实施符合性](../../governance/policy/tutorials/create-and-manage.md)

- [Azure 蓝图](../../governance/blueprints/overview.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8：执行定期攻击模拟

**指导**：根据需要，对 Azure 资源进行渗透测试或红队活动，并确保修正所有关键安全发现。
请遵循 Microsoft 云渗透测试互动规则，确保你的渗透测试不违反 Microsoft 政策。 使用 Microsoft 红队演练策略和执行，以及针对 Microsoft 托管云基础结构、服务和应用程序执行现场渗透测试。

- [Azure 中的渗透测试](../../security/fundamentals/pen-testing.md)

- [参与的渗透测试规则](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft 云红色组合](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure 安全中心监视**：不适用

**责任**：共享

## <a name="backup-and-recovery"></a>备份和恢复

有关详细信息，请参阅 [Azure 安全基准：备份和恢复](/azure/security/benchmarks/security-controls-v2-backup-recovery)。

### <a name="br-3-validate-all-backups-including-customer-managed-keys"></a>BR-3：验证所有备份，包括客户管理的密钥

**指南**：在你自己的存储帐户中存储托管应用程序定义时，请确保你可以还原任何关联的客户托管密钥，这些密钥用于该帐户的加密，这些密钥存储在 Azure Key Vault 中。

- [为托管应用程序定义自带存储](https://docs.microsoft.com/azure/azure-resource-manager/managed-applications/publish-service-catalog-app?tabs=azure-powershell#bring-your-own-storage-for-the-managed-application-definition)

- [如何在 Azure 中还原 Key Vault 密钥](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultkey?view=azps-5.1.0&amp;preserve-view=true)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="br-4-mitigate-risk-of-lost-keys"></a>BR-4：减少密钥丢失风险

**指南**：如果你要为你的托管应用程序定义引入自己的存储，请确保已准备好度量值，以防丢失和恢复用于对定义进行加密的密钥。 启用软删除和清除保护 Azure Key Vault，以存储客户管理的密钥，以防意外或恶意删除的密钥。  

- [为托管应用程序定义自带存储](https://docs.microsoft.com/azure/azure-resource-manager/managed-applications/publish-service-catalog-app?tabs=azure-powershell#bring-your-own-storage-for-the-managed-application-definition)

- [如何在 Key Vault 中启用软删除和清除保护](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

**Azure 安全中心监视**：是

**责任**：客户

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
- [Azure 安全体系结构建议 - 存储、数据和加密](https://docs.microsoft.com/azure/architecture/framework/security/storage-data-encryption?toc=/security/compass/toc.json&amp;bc=/security/compass/breadcrumb/toc.json)

- [Azure 安全基础知识 - Azure 数据安全、加密和存储](../../security/fundamentals/encryption-overview.md)

- [云采用框架 - Azure 数据安全和加密最佳做法](https://docs.microsoft.com/azure/security/fundamentals/data-encryption-best-practices?toc=/azure/cloud-adoption-framework/toc.json&amp;bc=/azure/cloud-adoption-framework/_bread/toc.json)

- [Azure 安全基准 - 资产管理](/azure/security/benchmarks/security-benchmark-v2-asset-management)

- [Azure 安全基准 - 数据保护](/azure/security/benchmarks/security-benchmark-v2-data-protection)

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

- [Azure 安全基准 - 状况和漏洞管理](/azure/security/benchmarks/security-benchmark-v2-posture-vulnerability-management)

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

-   Internet 边缘及入口和出口策略

-   混合云和本地互连策略

-   最新的网络安全项目（例如网络关系图、参考网络体系结构）

有关详细信息，请参阅以下资源：
- [Azure 安全最佳做法 11 - 体系结构。单一的统一安全策略](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Azure 安全基准 - 网络安全](/azure/security/benchmarks/security-benchmark-v2-network-security)

- [Azure 网络安全概述](../../security/fundamentals/network-overview.md)

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

- [Azure 安全基准 - 标识管理](/azure/security/benchmarks/security-benchmark-v2-identity-management)

- [Azure 安全基准 - 特权访问](/azure/security/benchmarks/security-benchmark-v2-privileged-access)

- [Azure 安全最佳做法 11 - 体系结构。单一的统一安全策略](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Azure 标识管理安全概述](../../security/fundamentals/identity-management-overview.md)

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

- [Azure 安全基准 - 日志记录和威胁检测](/azure/security/benchmarks/security-benchmark-v2-logging-threat-detection)

- [Azure 安全基准 - 事件响应](/azure/security/benchmarks/security-benchmark-v2-incident-response)

- [Azure 安全最佳做法 4 - 流程。更新云的事件响应流程](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Azure 采用框架、日志记录和报告决策指南](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Azure 企业规模、管理和监视](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="next-steps"></a>后续步骤

- 参阅 [Azure 安全基准 V2 概述](/azure/security/benchmarks/overview)
- 详细了解 [Azure 安全基线](/azure/security/benchmarks/security-baselines-overview)
