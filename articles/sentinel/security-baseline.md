---
title: 适用于 Azure Sentinel 的 Azure 安全基线
description: Azure Sentinel 安全基线为实现 Azure 安全基准中指定的安全建议提供过程指南和资源。
author: msmbaldwin
ms.service: azure-sentinel
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: a827268444fdbc2cdbbe5ac1220ad7d49a2a254f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "104604230"
---
# <a name="azure-security-baseline-for-azure-sentinel"></a>适用于 Azure Sentinel 的 Azure 安全基线

此安全基线对 Azure Sentinel 应用 [Azure 安全基准 1.0 版](../security/benchmarks/overview-v1.md)中的指南。 Azure 安全基准提供有关如何在 Azure 上保护云解决方案的建议。
内容按照 Azure 安全基准定义的安全控制措施和适用于 Azure Sentinel 的相关指南进行分组。 不适用于 Azure Sentinel 的控制措施已被排除在外。

 
若要查看 Azure Sentinel 如何完全映射到 Azure 安全基准，请查看[完整的 Azure Sentinel 安全基线映射文件](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)。

## <a name="network-security"></a>网络安全

[有关详细信息，请参阅 *Azure 安全基线：* 网络安全性](../security/benchmarks/security-control-network-security.md)。

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1：保护虚拟网络中的 Azure 资源

**指导**：不能将虚拟网络、子网或网络安全组直接与 Azure Sentinel 关联。 不过，可为与 Azure Sentinel 关联的 Log Analytics 工作区启用 Azure 专用终结点，来限制与专用网络之间的通信。

**责任**：客户

**Azure 安全中心监视**：无

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11：使用自动化工具来监视网络资源配置和检测更改

**指导**：使用 Azure 活动日志监视网络资源配置，并检测与 Azure Sentinel 工作区相关的网络资源的更改。 在 Azure Monitor 中创建当关键资源发生更改时触发的警报。

- [如何在 Azure Sentinel 中启用审核](resources.md)

- [如何查看和检索 Azure 活动日志事件](https://docs.microsoft.com/azure/azure-monitor/essentials/activity-log#view-the-activity-log)
 

- [如何在 Azure Monitor 中创建警报](../azure-monitor/alerts/alerts-activity-log.md)

**责任**：客户

**Azure 安全中心监视**：无

## <a name="logging-and-monitoring"></a>日志记录和监视

[有关详细信息，请参阅 *Azure 安全基线：* 日志记录和监视](../security/benchmarks/security-control-logging-monitoring.md)。

### <a name="22-configure-central-security-log-management"></a>2.2：配置中心安全日志管理

**指导**：Azure Sentinel 的审核日志在 Azure 活动日志中进行维护。 可将此数据从 Azure 活动日志流式传输到 Azure Sentinel 来查看它，然后可在 Azure Sentinel 中研究和分析此数据。

- [如何在 Azure Sentinel 中启用审核](resources.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：为 Azure 资源启用审核日志记录

**指导**：Azure Sentinel 的审核日志在 Azure 活动日志中进行维护。 可将此数据从 Azure 活动日志流式传输到 Azure Sentinel 来查看它，然后可在 Azure Sentinel 中研究和分析此数据。

- [如何在 Azure Sentinel 中启用审核](resources.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="25-configure-security-log-storage-retention"></a>2.5：配置安全日志存储保留期

**指导**：在 Azure Monitor 中，根据组织的合规性规定，为与 Azure Sentinel 工作区关联的 Log Analytics 工作区设置日志保持期。

- [如何设置日志保留参数](https://docs.microsoft.com/azure/azure-monitor/logs/manage-cost-storage#change-the-data-retention-period)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="26-monitor-and-review-logs"></a>2.6：监视和查看日志

**指导**：Azure Sentinel 的审核日志在 Azure 活动日志中进行维护。 可将此数据从 Azure 活动日志流式传输到 Azure Sentinel 来查看它，然后可在 Azure Sentinel 中搜索和分析此数据。 分析和监视 Azure Sentinel 活动日志实例的日志中是否存在异常行为。 使用 Azure Sentinel 工作区中的“日志”部分来执行查询，或者根据 Sentinel 日志创建警报。

- [如何在 Azure Sentinel 中启用审核](resources.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7：针对异常活动启用警报

**指导**：Azure Sentinel 的审核日志在 Azure 活动日志中进行维护。 可将此数据从 Azure 活动日志流式传输到 Azure Sentinel 来查看它，然后可在 Azure Sentinel 中搜索和分析此数据。 分析和监视 Azure Sentinel 活动日志实例的日志中是否存在异常行为。 使用 Azure Sentinel 工作区中的“日志”部分来执行查询，或者根据 Sentinel 日志创建警报。

- [如何在 Azure Sentinel 中启用审核](resources.md)

**责任**：客户

**Azure 安全中心监视**：无

## <a name="identity-and-access-control"></a>标识和访问控制

[有关详细信息，请参阅 *Azure 安全基线：* 标识和访问控制](../security/benchmarks/security-control-identity-access-control.md)。

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1：维护管理帐户的清单

**指导**：维护对你的 Azure Sentinel 工作区的控制平面（例如 Azure 门户）具有管理访问权限的用户帐户清单。 

可在 Azure 门户中为订阅使用“标识和访问控制”窗格来配置 Azure 基于角色的访问控制 (Azure RBAC)。 角色将应用到 Azure Active Directory (Azure AD) 中的用户、组、服务主体和托管标识。 Azure Sentinel 还使用 Azure RBAC 来提供可分配给 Azure 中的用户、组和服务的内置管理角色，例如 Azure Sentinel 参与者。 

- [了解自定义角色](../role-based-access-control/custom-roles.md)

- [了解 Azure Sentinel 中的 Azure RBAC](roles.md)

- [如何为工作簿配置 Azure RBAC](quickstart-get-visibility.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="33-use-dedicated-administrative-accounts"></a>3.3：使用专用管理帐户

**指南**：围绕专用管理帐户的使用创建标准操作程序。 使用 Azure 安全中心标识和访问管理来监视管理帐户的数量。

此外，为了跟踪专用管理帐户，你可以使用 Azure 安全中心或内置的 Azure 策略提供的建议，例如：

- 应该为你的订阅分配了多个所有者
- 应从订阅中删除拥有所有者权限的已弃用帐户
- 应从订阅中删除拥有所有者权限的外部帐户

访问所引用的链接可获得更多信息。

- [如何使用 Azure 安全中心监视标识和访问（预览）](../security-center/security-center-identity-access.md)

- [如何使用 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4：使用 Azure Active Directory 单一登录 (SSO)

**指导**：可通过 REST API 对 Azure Sentinel 工作区（例如 Azure 门户）进行控制平面访问，该访问支持 SSO。 若要进行身份验证，请将请求的授权标头设置为从 Azure Active Directory (Azure AD) 获取的 JSON Web 令牌。

- [了解 Azure Log Analytics REST API](/rest/api/loganalytics/)

- [了解 Azure AD 的 SSO](../active-directory/manage-apps/what-is-single-sign-on.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5：对所有基于 Azure Active Directory 的访问使用多重身份验证

**指导**：启用 Azure Active Directory (Azure AD) 多重身份验证，并遵循 Azure 安全中心标识和访问管理的建议。

- [如何在 Azure 中启用多重身份验证](../active-directory/authentication/howto-mfa-getstarted.md)

- [如何在 Azure 安全中心监视标识和访问](../security-center/security-center-identity-access.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3.6：使用由 Azure 管理的安全工作站执行管理任务

**指导**：使用启用了 Azure AD 多重身份验证的特权访问工作站 (PAW) 来登录和配置与 Azure Sentinel 相关的资源。 
 

 
- [特权访问工作站](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)
 

 
- [规划基于云的 Azure AD 多重身份验证部署](../active-directory/authentication/howto-mfa-getstarted.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7：记录来自管理帐户的可疑活动并对其发出警报

**指导**：当环境中出现可疑或不安全的活动时，可使用 Azure Active Directory (Azure AD) Privileged Identity Management (PIM) 生成日志和警报。

此外，还可使用 Azure AD 风险检测来查看警报和报告有风险的用户行为。

- [如何部署 Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [了解 Azure AD 风险检测](../active-directory/identity-protection/overview-identity-protection.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8：仅从批准的位置管理 Azure 资源

**指导**：使用“条件访问”中的“命名位置”，以仅允许从 IP 地址范围或国家/地区的特定逻辑分组访问 Azure 门户。

- [如何在 Azure 中配置命名位置](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="39-use-azure-active-directory"></a>3.9：使用 Azure Active Directory

**指导**：使用 Azure Active Directory (Azure AD) 作为 Azure Sentinel 实例的中心身份验证和授权系统。 Azure AD 通过对静态数据和传输中数据使用强加密来保护数据。 Azure AD 还会对用户凭据进行加盐、哈希处理和安全存储操作。

- [如何创建和配置新的 Azure AD 租户](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10：定期审查和协调用户访问

**指导**：Azure Active Directory (Azure AD) 提供了日志来帮助你发现过时的帐户。 此外，请使用 Azure 标识访问评审来有效管理组成员身份、对企业应用程序的访问和角色分配。 可以定期评审用户的访问权限，确保只有适当的用户才持续拥有访问权限。

- [了解 Azure AD 报告](/azure/active-directory/reports-monitoring)

- [如何使用 Azure 标识访问评审](../active-directory/governance/access-reviews-overview.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11：监视尝试访问已停用凭据的行为

**指导**：使用 Azure Active Directory (Azure AD) 作为 Azure Sentinel 工作区的集中身份验证和授权系统。 Azure AD 通过对静态数据和传输中数据使用强加密来保护数据。 Azure AD 还会对用户凭据进行加盐、哈希处理和安全存储操作。

可访问 Azure AD 登录活动、审核和风险事件日志源，来与 Azure Sentinel 或第三方系统信息和事件管理解决方案集成。

可以通过为 Azure AD 用户帐户创建诊断设置，并将审核日志和登录日志发送到 Log Analytics 工作区，来简化此过程。 可以在 Log Analytics 中配置所需的日志警报。

- [如何将 Azure 活动日志集成到 Azure Monitor](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

- [如何加入 Azure Sentinel](quickstart-onboard.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12：针对帐户登录行为偏差发出警报

**指导**：对于控制平面（例如 Azure 门户）中帐户登录行为的偏差，请使用 Azure Active Directory (Azure AD) 标识保护和风险检测功能进行配置，实现在检测到与用户标识相关的可疑操作时自动进行响应。 还可将数据引入 Azure Sentinel 以做进一步调查。

- [如何查看 Azure AD 风险登录](../active-directory/identity-protection/overview-identity-protection.md)

- [如何配置和启用标识保护风险策略](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [如何加入 Azure Sentinel](quickstart-onboard.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13：在支持场合下为 Microsoft 提供对相关客户数据的访问权限

**指导**：目前不适用；Azure Sentinel 或 Log Analytics 工作区尚不支持客户密码箱。

- [支持客户密码箱的服务列表](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability)

**责任**：客户

**Azure 安全中心监视**：无

## <a name="data-protection"></a>数据保护

[有关详细信息，请参阅 *Azure 安全基线：* 数据保护](../security/benchmarks/security-control-data-protection.md)。

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1：维护敏感信息的清单

**指导**：使用标记可以帮助跟踪存储或处理敏感信息的 Azure 资源。

- [如何创建和使用标记](../azure-resource-manager/management/tag-resources.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2：隔离存储或处理敏感信息的系统

**指导**：实现单独的订阅，从而选择性地为开发、测试和生产 Sentinel 工作区包含管理组。

- [如何创建其他 Azure 订阅](../cost-management-billing/manage/create-subscription.md)

- [如何创建管理组](../governance/management-groups/create-management-group-portal.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4：加密传输中的所有敏感信息

**指导**：如果使用 Azure 或本地虚拟机作为 Syslog 转发器，则需要配置 Syslog 守护程序（rsyslog 或 syslog-ng）来进行 TLS 通信。

- [使用通用事件格式连接外部解决方案](connect-common-event-format.md)

**责任**：共享

**Azure 安全中心监视**：无

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5：使用有效的发现工具识别敏感数据

**指导**：Azure 中尚不支持数据标识、分类和丢失防护功能。 如果需要出于合规性目的使用这些功能，请实施第三方解决方案。

对于 Microsoft 管理的底层平台，Microsoft 会将所有客户内容视为敏感数据，并会全方位地防范客户数据丢失和遭到透露。 为了确保 Azure 中的客户数据保持安全，Microsoft 实施并维护了一套可靠的数据保护控制措施和功能。

- [了解 Azure 中的客户数据保护](../security/fundamentals/protection-customer-data.md)

**责任**：共享

**Azure 安全中心监视**：无

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6：使用基于角色的访问控制来控制对资源的访问

**指导**：可在 Azure 门户中使用“标识和访问控制(IAM)”窗格来配置 Azure 基于角色的访问控制 (Azure RBAC)。 角色将应用到 Active Directory 中的用户、组、服务主体和托管标识。 对于个人和组，可使用内置的 Azure 角色或自定义角色。 

Azure Sentinel 使用 Azure RBAC 提供可在 Azure 中分配给用户、组和服务的内置角色。 通过 Azure RBAC，可在安全运营团队中使用和创建角色来授予对 Azure Sentinel 的适当访问权限。 根据角色，可精细控制具有 Azure Sentinel 访问权限的用户可看到的内容。 可直接在 Azure Sentinel 工作区中分配 Azur e角色，也可将其分配给该工作区所属的订阅或资源组。 有 3 个特定的内置 Azure Sentinel 角色：

- Azure Sentinel 读取者
- Azure Sentinel 响应者
- Azure Sentinel 参与者

除了 Azure Sentinel 专用的 Azure 角色，还有 Azure 和 Log Analytics 内置的 Azure 角色可授予一组更广泛的权限，其中包括对 Azure Sentinel 工作区和其他资源的访问权限：

Azure 角色包括所有者、参与者和读取者。 Azure 角色授予对所有 Azure 资源（包括 Log Analytics 工作区和 Azure Sentinel 资源）的访问权限。

Log Analytics 角色包括 Log Analytics 参与者和 Log Analytics 读取者。 Log Analytics 角色授予对所有 Log Analytics 工作区的访问权限。

此外，每个 Sentinel 工作簿都是一个 Azure 资源，你可为用户分配角色来管理访问权限。

- [如何配置 Azure RBAC](../role-based-access-control/role-assignments-portal.md)

- [了解自定义角色](../role-based-access-control/custom-roles.md)

- [了解 Sentinel 和 Log Analytics 中的角色](roles.md)

- [如何为工作簿配置 Azure RBAC](quickstart-get-visibility.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8：静态加密敏感信息

**指导**：Azure Sentinel 和 Azure Monitor Log Analytics 工作区当前使用 Microsoft 管理的密钥来加密任何包含的静态数据。 Sentinel 尚未完全支持自带密钥功能，但很快就将支持。 

 
- [Azure Monitor 客户管理的密钥概述](https://docs.microsoft.com/azure/azure-monitor/logs/customer-managed-keys#customer-managed-key-overview) 

 
- [了解 Azure Sentinel 中客户管理的密钥（预览）](customer-managed-keys.md)

**责任**：共享

**Azure 安全中心监视**：无

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9：记录对关键 Azure 资源的更改并对此类更改发出警报

**指导**：Azure Sentinel 的审核日志在 Azure 活动日志中进行维护。 可将此数据从 Azure 活动日志流式传输到 Azure Sentinel 来查看它，然后可在 Azure Sentinel 中研究和分析此数据。 在 Azure Sentinel 工作区的“日志”部分中，可创建在生产 Azure Sentinel 工作区和其他关键或相关资源发生更改时发出的警报。

- [如何在 Azure Sentinel 中启用审核](resources.md)

**责任**：客户

**Azure 安全中心监视**：无

## <a name="inventory-and-asset-management"></a>清单和资产管理

[有关详细信息，请参阅 *Azure 安全基线：* 清单和资产管理](../security/benchmarks/security-control-inventory-asset-management.md)。

### <a name="61-use-automated-asset-discovery-solution"></a>6.1：使用自动化资产发现解决方案

**指导**：使用 Azure Resource Graph 查询/发现订阅中的所有资源（例如计算、存储、网络、端口和协议等）。 确保租户中具有适当的（读取）权限，并枚举所有 Azure 订阅以及订阅中的资源。

尽管可以通过 Resource Graph 发现经典 Azure 资源，但我们强烈建议你今后还是创建并使用 Azure 资源管理器资源。

- [如何使用 Azure Resource Graph 创建查询](../governance/resource-graph/first-query-portal.md)

- [如何查看 Azure 订阅](/powershell/module/az.accounts/get-azsubscription)

- [了解 Azure RBAC](../role-based-access-control/overview.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="62-maintain-asset-metadata"></a>6.2：维护资产元数据

**指导**：将标记应用到 Azure资源，以便有条理地将元数据组织成某种分类。

- [如何创建和使用标记](../azure-resource-manager/management/tag-resources.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="63-delete-unauthorized-azure-resources"></a>6.3：删除未经授权的 Azure 资源

**指南**：在适用的情况下，请使用标记、管理组和单独的订阅来组织和跟踪 Azure 资产。 定期核对清单，确保及时地从订阅中删除未经授权的资源。

此外，在 Azure Policy 中使用以下内置策略定义，对可以在客户订阅中创建的资源类型施加限制：

- 不允许的资源类型
- 允许的资源类型

访问所引用的链接可获得更多信息。

- [如何创建其他 Azure 订阅](../cost-management-billing/manage/create-subscription.md)

- [如何创建管理组](../governance/management-groups/create-management-group-portal.md)

- [如何创建和使用标记](../azure-resource-manager/management/tag-resources.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5：监视未批准的 Azure 资源

**指导**：使用 Azure Policy 对可以在订阅中创建的资源类型施加限制。

使用 Azure Resource Graph 可查询/发现订阅中的资源。  确保环境中的所有 Azure 资源均已获得批准。

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [如何使用 Azure Graph 创建查询](../governance/resource-graph/first-query-portal.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="69-use-only-approved-azure-services"></a>6.9：仅使用已批准的 Azure 服务

**指南**：在 Azure Policy 中使用以下内置策略定义，对可以在客户订阅中创建的资源类型施加限制：

- 不允许的资源类型
- 允许的资源类型

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [如何使用 Azure Policy 拒绝特定的资源类型](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11：限制用户与 Azure 资源管理器进行交互的能力

**指南**：配置 Azure 条件访问，使其通过为“Microsoft Azure 管理”应用配置“阻止访问”，来限制用户与 Azure 资源管理器进行交互的能力。

- [如何配置条件访问以阻止访问 Azure 资源管理器](../role-based-access-control/conditional-access-azure-management.md)

**责任**：客户

**Azure 安全中心监视**：无

## <a name="secure-configuration"></a>安全配置

[有关详细信息，请参阅 *Azure 安全基线：* 安全配置](../security/benchmarks/security-control-secure-configuration.md)。

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1：为所有 Azure 资源建立安全配置

**指导**：通过 Azure Policy，为与 Sentinel 工作区关联的 Log Analytics 工作区定义和实现标准安全配置。 使用“Microsoft.OperationalInsights”命名空间中的 Azure Policy 别名创建自定义策略，来审核或强制实施 Log Analytics 工作区的配置。
 

- [如何查看可用的 Azure Policy 别名](/powershell/module/az.resources/get-azpolicyalias)

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3：维护安全的 Azure 资源配置

**指导**：使用 Azure Policy“[拒绝]”和“[不存在则部署]”效果对不同的 Azure 资源强制实施安全设置。

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [了解 Azure Policy 效果](../governance/policy/concepts/effects.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5：安全存储 Azure 资源的配置

**指南**：如果使用的是自定义 Azure 策略定义，请使用 Azure DevOps 或 Azure Repos 安全地存储和管理代码。
 

- [如何在 Azure DevOps 中存储代码](/azure/devops/repos/git/gitworkflow)

 
- [Azure Repos 文档](/azure/devops/repos/)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7：部署 Azure 资源的配置管理工具

**指导**：在“Microsoft.OperationalInsights”命名空间中使用内置的 Azure Policy 定义和 Azure Policy 别名创建自定义策略，来审核、强制实施系统配置并为其发出警报。 另外，开发一个用于管理策略例外的流程和管道。

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9：为 Azure 资源实施自动配置监视

**指导**：在“Microsoft.OperationalInsights”命名空间中使用内置的 Azure Policy 定义和 Azure Policy 别名创建自定义策略，来审核、强制实施系统配置并为其发出警报。 使用 Azure Policy 的“[审核]”、“[拒绝]”和“[不存在则部署]”效果自动强制实施 Azure 资源的配置。

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="711-manage-azure-secrets-securely"></a>7.11：安全管理 Azure 机密

**指导**：Azure Sentinel 支持使用各种连接器从多个源收集日志。 其中一些连接器需要使用 Log Analytics 工作区密钥进行设置。 设置这些连接器后，请使用 Azure Key Vault 存储密钥，以简化机密管理并避免意外的凭据泄露。

- [将 Sentinel 连接到数据源](connect-data-sources.md)

- [如何创建 Key Vault](../key-vault/secrets/quick-create-portal.md)

- [如何使用托管标识提供 Key Vault 身份验证](../key-vault/general/assign-access-policy-portal.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13：消除意外的凭据透露

**指南**：实施凭据扫描程序来识别代码中的凭据。 凭据扫描程序还会建议将发现的凭据转移到更安全的位置，例如 Azure Key Vault。

- [如何设置凭据扫描程序](https://secdevtools.azurewebsites.net/helpcredscan.html)

**责任**：客户

**Azure 安全中心监视**：无

## <a name="malware-defense"></a>恶意软件防护

[有关详细信息，请参阅 *Azure 安全基线：* 恶意软件防护](../security/benchmarks/security-control-malware-defense.md)。

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2：预先扫描要上传到非计算 Azure 资源的文件

**指导**：已在支持 Azure 服务（例如 Azure Sentinel 和 Log Analytics）的底层主机上启用 Microsoft Antimalware，但该软件不会针对客户内容运行。 

你需要负责预先扫描要上传到非计算 Azure 资源（包括 Log Analytics 工作区）的任何内容。 Microsoft 无法访问客户数据，因此无法代表你对客户内容执行反恶意软件扫描。

**责任**：客户

**Azure 安全中心监视**：无

## <a name="data-recovery"></a>数据恢复

[有关详细信息，请参阅 *Azure 安全基线：* 数据恢复](../security/benchmarks/security-control-data-recovery.md)。

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4：确保保护备份和客户管理的密钥

**指导**：在 Key Vault 中启用“软删除”，以防止意外删除或恶意删除密钥。 如果用户通过删除加密密钥或删除 Azure Sentinel 的访问权限来撤销密钥加密密钥，在一小时内，Azure Sentinel 将执行此更改，并表现为数据不再可用。 此时，将阻止执行任何使用持久性存储资源（如数据引入、持久配置更改和事件创建）的操作。 以前存储的数据不会删除，但仍无法访问。 不可访问的数据由数据保留策略管理，并根据该策略进行清除。

撤消或删除加密密钥后，唯一可行的操作是删除帐户。

如果在吊销后恢复访问操作，Azure Sentinel 会在一小时内恢复对数据的访问权限。

- [如何在 Key Vault 中启用“软删除”](../storage/blobs/soft-delete-blob-overview.md)

- [了解 Azure Sentinel 中客户管理的密钥](customer-managed-keys.md)

**责任**：客户

**Azure 安全中心监视**：无

## <a name="incident-response"></a>事件响应

[有关详细信息，请参阅 *Azure 安全基线：* 事件响应](../security/benchmarks/security-control-incident-response.md)。

### <a name="101-create-an-incident-response-guide"></a>10.1：创建事件响应指导

**指南**：为组织制定事件响应指南。 确保在书面的事件响应计划中定义人员职责，以及事件处理/管理从检测到事件后审查的各个阶段。

- [如何在 Azure 安全中心配置工作流自动化](../security-center/security-center-planning-and-operations-guide.md)

- [关于建立自己的安全事件响应流程的指南](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft 安全响应中心事件分析](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [客户还可以利用 NIST 的“计算机安全事件处理指南”来制定他们自己的事件响应计划](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2：创建事件评分和优先级设定过程

**指南**：安全中心向每个警报分配一个严重性，帮助你优先处理应首先调查的警报。 严重性取决于安全中心对调查结果或用于发出警报的分析的确信程度，以及对导致警报的活动背后存在恶意意图的确信程度。

 

此外，请明确标记订阅（例如 生产、非生产），并创建命名系统来对 Azure 资源进行明确标识和分类。

**责任**：客户

**Azure 安全中心监视**：无

### <a name="103-test-security-response-procedures"></a>10.3：测试安全响应过程

**指导**：定期执行演练来测试系统的事件响应功能。 识别弱点和差距，并根据需要修改计划。

- [请参阅 NIST 的刊物：Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)（IT 规划和功能的测试、培训与演练计划指南）

**责任**：客户

**Azure 安全中心监视**：无

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4：提供安全事件联系人详细信息，并针对安全事件配置警报通知

**指南**：如果 Microsoft 安全响应中心 (MSRC) 发现非法或未经授权的某方访问了客户的数据，Microsoft 将使用安全事件联系人信息与你取得联系。  事后审查事件，确保问题得到解决。

- [如何设置 Azure 安全中心安全联系人](../security-center/security-center-provide-security-contact-details.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5：将安全警报整合到事件响应系统中

**指导**：使用连续导出功能导出 Azure 安全中心警报和建议。 使用连续导出可以手动导出或者持续导出警报和建议。 可以使用 Azure 安全中心数据连接器将警报流式传输到 Azure Sentinel。

 

- [如何配置连续导出](../security-center/continuous-export.md)

- [如何将警报流式传输到 Azure Sentinel](connect-azure-security-center.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="106-automate-the-response-to-security-alerts"></a>10.6：自动响应安全警报

**指导**：使用 Azure 安全中心内的工作流自动化功能可以通过“逻辑应用”针对安全警报和建议自动触发响应。

- [如何配置工作流自动化和逻辑应用](../security-center/workflow-automation.md)

**责任**：客户

**Azure 安全中心监视**：无

## <a name="penetration-tests-and-red-team-exercises"></a>渗透测试和红队练习

[有关详细信息，请参阅 *Azure 安全基线：* 渗透测试和红队演练](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)。

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1：定期对 Azure 资源执行渗透测试，确保修正所有发现的关键安全问题

**指导**：请遵循 Microsoft 云渗透测试互动规则，确保你的渗透测试不违反 Microsoft 政策。 使用 Microsoft 红队演练策略和执行，以及针对 Microsoft 托管云基础结构、服务和应用程序执行现场渗透测试。 

- [参与的渗透测试规则](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft 云红色组合](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**责任**：共享

**Azure 安全中心监视**：无

## <a name="next-steps"></a>后续步骤

- 参阅 [Azure 安全基准 V2 概述](/azure/security/benchmarks/overview)
- 详细了解 [Azure 安全基线](/azure/security/benchmarks/security-baselines-overview)
