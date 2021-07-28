---
title: Site Recovery 的 Azure 安全基线
description: Site Recovery 安全基线为实现 Azure 安全基准中指定的安全建议提供过程指南和资源。
author: msmbaldwin
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/29/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 429bb1ffdf40ed9906082e00d4ffd1156a4e5e0b
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "105967840"
---
# <a name="azure-security-baseline-for-site-recovery"></a>Site Recovery 的 Azure 安全基线

此安全基线对 Site Recovery 应用 [Azure 安全基准 1.0 版](../security/benchmarks/overview-v1.md)中的指南。 Azure 安全基准提供有关如何在 Azure 上保护云解决方案的建议。 内容按照 Azure 安全基准定义的安全控制措施和适用于 SiteRecovery 的相关指南进行分组。 排除了不适用于 SiteRecovery 或 Microsoft 为其责任方的控件。

若要了解 SiteRecovery 如何完全映射到 Azure 安全基准，请参阅[完整的 SiteRecovery 安全基线映射文件](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)。

## <a name="network-security"></a>网络安全

[有关详细信息，请参阅 *Azure 安全基线：* 网络安全性](../security/benchmarks/security-control-network-security.md)。

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1：保护虚拟网络中的 Azure 资源

**指导**：Microsoft Azure Site Recovery 不支持部署到 Azure 虚拟网络。 使用 Azure 专用终结点配置 Site Recovery 服务，以通过网络执行安全通信。

- [Azure Site Recovery 专用链接支持](azure-to-azure-how-to-enable-replication-private-endpoints.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8：最大程度地降低网络安全规则的复杂性和管理开销

**指导**：Site Recovery 服务支持服务标记，这允许客户打开仅通向特定服务和端口的流量。 若要允许对 Site Recovery 服务进行出站访问，客户必须允许其防火墙或网络安全组上的“AzureSiteRecovery”服务标记。

- [使用服务标记的出站连接](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-using-service-tags)

**责任**：客户

Azure 安全中心监视：无

### <a name="110-document-traffic-configuration-rules"></a>1.10：阐述流量配置规则

**指导**：为网络安全组以及与网络安全和通信流相关的其他资源使用资源标记。 对于单独的网络安全组规则，请使用“说明”字段来记录那些允许流量进出网络的规则。 

引入与标记相关的任何内置 Azure Policy 定义（例如“需要标记及其值”）来确保使用标记创建所有资源，并在现有资源不带标记时发出通知。 

可使用 Azure PowerShell 或 Azure CLI，基于资源的标记查找资源或对其执行操作。 

- [如何创建和使用标记](../azure-resource-manager/management/tag-resources.md)

- [如何创建 Azure 虚拟网络](../virtual-network/quick-create-portal.md) 

- [如何使用网络安全组规则筛选网络流量](../virtual-network/tutorial-filter-network-traffic.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11：使用自动化工具来监视网络资源配置和检测更改

**指导**：使用 Azure 活动日志监视与 Site Recovery 服务相关的网络资源配置的任何更改。 在 Azure Monitor 中创建警报，以便系统在关键 Site Recovery 网络资源发生更改时通知你。

- [查看和检索 Azure 活动日志事件](https://docs.microsoft.com/azure/azure-monitor/essentials/activity-log#view-the-activity-log)

- [使用 Azure Monitor 创建、查看和管理活动日志警报](../azure-monitor/alerts/alerts-activity-log.md)

**责任**：客户

Azure 安全中心监视：无

## <a name="logging-and-monitoring"></a>日志记录和监视

[有关详细信息，请参阅 *Azure 安全基线：* 日志记录和监视](../security/benchmarks/security-control-logging-monitoring.md)。

### <a name="22-configure-central-security-log-management"></a>2.2：配置中心安全日志管理

**指导**：启用 Azure 活动日志诊断设置以审核日志记录，并将日志发送到 Log Analytics 工作区、Azure 存储帐户或 Azure 事件中心进行存档。

使用 Azure 活动日志数据确定针对 Azure 资源执行的任何写入操作（PUT、POST、DELETE）的“操作内容、操作人员和操作时间”。

在 Azure Monitor 中引入 Site Recovery 日志以聚合生成的安全数据。 在 Azure Monitor 中，使用 Log Analytics 工作区来查询和执行分析，并使用存储帐户进行长期存储或存档存储。 另外，你可以启用 Azure Sentinel 或第三方安全信息和事件管理 (SIEM) 解决方案并将数据载入其中。

- [如何启用 Azure 活动日志的诊断设置](../azure-monitor/essentials/activity-log.md)

- [使用 Azure Monitor 日志监视 Site Recovery](monitor-log-analytics.md)

- [如何加入 Azure Sentinel](../sentinel/quickstart-onboard.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：为 Azure 资源启用审核日志记录

**指导**：启用 Azure 活动日志诊断设置以审核日志记录，并将日志发送到 Log Analytics 工作区、Azure 存储帐户或 Azure 事件中心进行存档。 

使用 Azure 活动日志数据确定针对 Azure 资源执行的任何写入操作（PUT、POST、DELETE）的“操作内容、操作人员和操作时间”。

通过 Azure Monitor 引入 Site Recovery 日志以聚合生成的安全数据。 在 Azure Monitor 中，使用 Log Analytics 工作区来查询和执行分析，并使用存储帐户进行长期/存档存储。 启用 Azure Sentinel 或第三方安全信息和事件管理 (SIEM) 解决方案并将数据载入其中。

- [如何启用 Azure 活动日志的诊断设置](../azure-monitor/essentials/activity-log.md)

- [使用 Azure Monitor 日志监视 Site Recovery](monitor-log-analytics.md)

- [如何加入 Azure Sentinel](../sentinel/quickstart-onboard.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="25-configure-security-log-storage-retention"></a>2.5：配置安全日志存储保留期

**指导**：使用 Azure Monitor，根据组织的合规性规定，为与 Azure 恢复服务保管库关联的 Log Analytics 工作区设置日志保留期。 

- [如何设置日志保留参数](https://docs.microsoft.com/azure/azure-monitor/logs/manage-cost-storage#change-the-data-retention-period)

**责任**：客户

Azure 安全中心监视：无

### <a name="26-monitor-and-review-logs"></a>2.6：监视和审查日志

**指导**：启用 Azure 活动日志诊断设置，并将日志发送到 Log Analytics 工作区。 

在 Log Analytics 中执行查询，以搜索字词、识别趋势、分析模式，以及基于从恢复服务保管库收集的活动日志数据提供见解。

- [监视 Site Recovery](site-recovery-monitor-and-troubleshoot.md)

- [如何启用 Azure 活动日志的诊断设置](../azure-monitor/essentials/activity-log.md)

- [如何收集和分析 Azure Monitor 的 Log Analytics 工作区中的 Azure 活动日志](../azure-monitor/essentials/activity-log.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7：针对异常活动启用警报

**指导**：使用 Azure Monitor 日志和 Log Analytics 监视 Azure Site Recovery 复制的计算机。 在 Azure Monitor 中使用 Log Analytics 编写和测试日志查询，并以交互方式分析日志数据。 Azure Monitor 收集活动和资源日志，以及其他监视数据。 

可视化和查询日志结果，并配置警报来根据监视的数据采取措施。 在已加入 Azure Sentinel 的 Log Analytics 工作区上设置警报，因为它提供了安全业务流程自动化响应 (SOAR) 解决方案。 这样便可以创建自动化解决方案（例如 playbook）并将其用于修正安全问题。 使用 Azure Monitor 在 Log Analytics 工作区中创建自定义日志警报。 

- [监视 Site Recovery](site-recovery-monitor-and-troubleshoot.md)

- [如何加入 Azure Sentinel](../sentinel/quickstart-onboard.md)

- [使用 Azure Monitor 创建、查看和管理日志警报](../azure-monitor/alerts/alerts-log.md)

**责任**：客户

Azure 安全中心监视：无

## <a name="identity-and-access-control"></a>标识和访问控制

[有关详细信息，请参阅 *Azure 安全基线：* 标识和访问控制](../security/benchmarks/security-control-identity-access-control.md)。

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1：维护管理帐户的清单

**指导**：默认情况下，不分配任何角色。 需要根据业务需求显式分配它们。 可以通过 PowerShell CLI 或 Azure Active Directory (Azure AD) 检查任何角色分配，以发现作为管理组成员的帐户。

- [如何使用 PowerShell 获取 Azure AD 中的目录角色](/powershell/module/azuread/get-azureaddirectoryrole)

- [如何使用 PowerShell 获取 Azure AD 中目录角色的成员](/powershell/module/azuread/get-azureaddirectoryrolemember)

**责任**：客户

Azure 安全中心监视：无

### <a name="33-use-dedicated-administrative-accounts"></a>3.3：使用专用管理帐户

**指南**：围绕专用管理帐户的使用创建标准操作程序。 使用安全中心的标识和访问管理功能来监视管理帐户的数量。

此外，为了跟踪专用管理帐户，你可以使用安全中心或内置的 Azure 策略提供的建议，例如： 

- 应该为你的订阅分配了多个所有者 
- 应从订阅中删除拥有所有者权限的已弃用帐户 

- 应从订阅中删除拥有所有者权限的外部帐户

创建一个流程来跟踪管理帐户的标识和访问控制，并定期查看它。

- [如何使用 Azure 安全中心监视标识和访问](../security-center/security-center-identity-access.md)

- [如何使用 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4：将单一登录 (SSO) 与 Azure Active Directory 配合使用

**指导**：将 Azure 应用注册与服务主体配合使用来检索令牌，以便使用该令牌通过 API 调用与恢复服务保管库进行交互。

- [如何调用 Azure REST API](/rest/api/azure/#how-to-call-azure-rest-apis-with-postman)

- [如何将客户端应用程序（服务主体）注册到 Azure Active Directory (Azure AD)](/rest/api/azure/#register-your-client-application-with-azure-ad)

- [Azure 恢复服务 API 信息](/rest/api/recoveryservices)

**责任**：客户

Azure 安全中心监视：无

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5：对所有基于 Azure Active Directory 的访问使用多重身份验证

**指导**：启用 Azure Active Directory 多重身份验证，并遵循安全中心的标识和访问建议。

- [规划 Azure Active Directory 多重身份验证部署](../active-directory/authentication/howto-mfa-getstarted.md)

- [监视标识和访问](../security-center/security-center-identity-access.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6：对所有管理任务使用专用计算机（特权访问工作站）

**指导**：将安全的、由 Azure 托管的工作站（也称为特权访问工作站 (PAW)）与 Azure Active Directory 多重身份验证配合使用来执行管理任务，以及对 Site Recovery 资源执行特权操作。 

- [特权访问工作站](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [规划基于云的 Azure Active Directory 多重身份验证部署](../active-directory/authentication/howto-mfa-getstarted.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7：记录来自管理帐户的可疑活动并对其发出警报

指导：当环境中出现可疑或不安全的活动时，可使用 Azure Active Directory (Azure AD) 的 Privileged Identity Management (PIM) 功能生成日志和警报。

使用 Azure AD 风险检测功能查看有关高风险用户行为的警报和报告。

- [如何部署 Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [了解 Azure AD 风险检测](../active-directory/identity-protection/overview-identity-protection.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="38-manage-azure-resources-only-from-approved-locations"></a>3.8：仅从批准的位置管理 Azure 资源

**指导**：使用条件访问命名位置，仅允许从 IP 地址范围、区域或国家/地区的特定逻辑分组访问 Azure 门户。
- [如何在 Azure 中配置命名位置](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="39-use-azure-active-directory"></a>3.9：使用 Azure Active Directory

指导：使用 Azure Active Directory (Azure AD) 作为 Site Recovery 的中央身份验证和授权系统。 Azure AD 通过对静态数据和传输中数据使用强加密来保护数据，并对用户凭据进行加盐和哈希处理，然后安全地存储它们。

- [如何创建和配置 Azure AD 实例](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10：定期审查和协调用户访问

**指导**：使用 Azure Active Directory (Azure AD) 日志来帮助发现过时的帐户。

使用 Azure AD 的标识和访问评审来有效管理组成员身份、对企业应用程序的访问以及角色分配。

创建一个流程来定期评审用户访问权限，以确保只有经历过完整访问评审的用户才能继续访问。

- [了解 Azure AD 报告](/azure/active-directory/reports-monitoring/)

- [如何使用 Azure 标识访问评审](../active-directory/governance/access-reviews-overview.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11：监视尝试访问已停用凭据的行为

指导：使用 Azure Active Directory (Azure AD) 作为 Site Recovery 资源的中央身份验证和授权系统。 Azure AD 通过对静态数据和传输中数据使用强加密以及加盐、哈希处理和安全存储用户凭据来保护数据。

你有权访问 Azure AD 登录活动、审核和风险事件日志源，从而将它们与 Azure 市场中提供的 Azure Sentinel 或者任何 SIEM 或监视工具相集成。

可为 Azure AD 用户帐户创建诊断设置，并将审核和登录日志发送到 Log Analytics 工作区来进一步简化此过程。 可在 Log Analytics 工作区中配置所需的警报。

- [如何将 Azure 活动日志集成到 Azure Monitor](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

- [如何加入 Azure Sentinel](../sentinel/quickstart-onboard.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12：针对帐户登录行为偏差发出警报

**指导**：使用 Azure Active Directory (Azure AD) 作为恢复服务保管库的中心身份验证和授权系统。

使用 Azure AD 标识保护功能来检测帐户登录行为，并配置对检测到的与用户标识相关的可疑操作的自动响应。 还可将数据引入 Azure Sentinel 来进一步调查。

- [如何查看 Azure AD 风险登录](../active-directory/identity-protection/overview-identity-protection.md)

- [如何配置和启用标识保护风险策略](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [如何加入 Azure Sentinel](../sentinel/quickstart-onboard.md)

**责任**：客户

Azure 安全中心监视：无

## <a name="data-protection"></a>数据保护

[有关详细信息，请参阅 *Azure 安全基线：* 数据保护](../security/benchmarks/security-control-data-protection.md)。

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1：维护敏感信息的清单

**指导**：使用标记可以帮助跟踪存储或处理敏感信息的 Azure 资源。

- [如何创建和使用标记](../azure-resource-manager/management/tag-resources.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2：隔离存储或处理敏感信息的系统

**指导**：为开发、测试和生产恢复服务保管库采用单独的订阅或管理组。 使用虚拟网络或子网分隔资源，对其进行相应的标记，并由网络安全组或 Azure 防火墙提供保护。 

当存储或处理敏感数据的虚拟机处于不使用状态时将其关闭。 实施策略和过程，以使其成为一个重复性流程。 

- [如何创建其他 Azure 订阅](../cost-management-billing/manage/create-subscription.md)

- [如何创建管理组](../governance/management-groups/create-management-group-portal.md)

- [Site Recovery 概述](site-recovery-overview.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3：监视和阻止未经授权的敏感信息传输

**指导**：使用专用链接或专用终结点、网络安全组和服务标记来降低启用了 Site Recovery 的虚拟机的数据泄露几率。

Microsoft 会管理 Site Recovery 使用的基础平台，并将所有客户内容视为敏感数据，防范客户数据丢失和泄露。 Microsoft 实施并维护了一套可靠的数据保护控制措施和功能，以确保 Azure 中的客户数据保持安全。 

- [了解 Azure 中的客户数据保护](../security/fundamentals/protection-customer-data.md)

- [通过 Azure 专用终结点复制虚拟机](azure-to-azure-how-to-enable-replication-private-endpoints.md)

- [通过 Azure Site Recovery 服务标记复制虚拟机](azure-to-azure-about-networking.md)

**责任**：共享

Azure 安全中心监视：无

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4：加密传输中的所有敏感信息

**指导**：Site Recovery 使用安全的 https 通道，该通道使用高级加密标准 (AES 256) 进行加密，从 Azure 工作负荷服务器通向承载在恢复服务保管库后面的 Site Recovery 服务。

在 2019 年底前启用的区域中，Site Recovery 支持的当前 TLS 版本为 TLS 1.0、TLS 1.1、TLS 1.2。 对于任何新区域，TLS1.2 是唯一受支持的 TLS 版本。

- [了解 Azure Site Recovery 的传输中加密](physical-azure-set-up-source.md)

**责任**：共享

Azure 安全中心监视：无

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5：使用有效的发现工具识别敏感数据

**指导**：数据标识、分类和丢失防护功能尚不适用于 Site Recovery。 

可以根据合规性需要实施第三方解决方案。

Microsoft 会管理 Site Recovery 使用的基础平台，并将所有客户内容视为敏感数据，防范客户数据丢失和泄露。 它已实施并维护一套可靠的数据保护控制机制和功能，以确保 Azure 中的客户数据始终安全。 

- [了解 Azure 中的客户数据保护](../security/fundamentals/protection-customer-data.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4.6：使用 Azure RBAC 管理对资源的访问

**指导**：使用 Azure 基于角色的访问控制 (Azure RBAC) 管理对与 Site Recovery 资源相关的数据和资源的访问。 

使用 Azure RBAC 划分工作职责，并授予其所需的适当访问权限。 使用内置的 Site Recovery 角色控制 Site Recovery 管理操作。

- [如何配置 Azure RBAC](../role-based-access-control/role-assignments-portal.md)

- [使用基于角色的访问控制管理 Azure Site Recovery](site-recovery-role-based-linked-access-control.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8：静态加密敏感信息

**指导**：使用平台管理的密钥和客户管理的密钥启用双重加密 Site Recovery 中提供了此功能。 

Site Recovery 支持数据的静态加密。 对于 Azure IaaS 工作负荷，请使用存储服务加密 (SSE) 对数据进行静态加密。 

使用通过客户管理的密钥加密的恢复服务保管库时，只有客户能够访问加密密钥。 Microsoft 从不保留副本，没有密钥的访问权限，而且任何时候都不会解密从主要位置传输到灾难恢复位置的数据。 

- [Azure Site Recovery 对客户管理的密钥的支持](azure-to-azure-how-to-enable-replication-cmk-disks.md)

**责任**：共享

Azure 安全中心监视：无

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9：记录对关键 Azure 资源的更改并对此类更改发出警报

指导：将 Azure Monitor 与 Azure 活动日志结合使用，以创建在关键资源发生更改时发出的警报。 这些资源可能包括恢复服务保管库的生产实例、Site Recovery 服务的资源和相关资源。
- [如何针对 Azure 活动日志事件创建警报](../azure-monitor/alerts/alerts-activity-log.md)

**责任**：客户

Azure 安全中心监视：无

## <a name="inventory-and-asset-management"></a>清单和资产管理

[有关详细信息，请参阅 *Azure 安全基线：* 清单和资产管理](../security/benchmarks/security-control-inventory-asset-management.md)。

### <a name="61-use-automated-asset-discovery-solution"></a>6.1：使用自动化资产发现解决方案

**指导**：使用 Azure Resource Graph 来查询或发现你的订阅中的所有资源（包括恢复服务保管库）。 确保租户中具有适当的读取权限，并枚举所有 Azure 订阅以及订阅中的资源。

尽管可以通过 Resource Graph 发现经典 Azure 资源，但我们强烈建议你今后还是创建并使用 Azure 资源管理器资源。

- [如何使用 Azure Graph 创建查询](../governance/resource-graph/first-query-portal.md)

- [如何查看 Azure 订阅](/powershell/module/az.accounts/get-azsubscription)

- [了解 Azure RBAC](../role-based-access-control/overview.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="62-maintain-asset-metadata"></a>6.2：维护资产元数据

**指导**：将标记应用于恢复服务保管库和其他相关资源，Site Recovery 会将这些资源与元数据一起使用，以逻辑方式将它们组织到分类中。
- [如何创建和使用标记](../azure-resource-manager/management/tag-resources.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="63-delete-unauthorized-azure-resources"></a>6.3：删除未经授权的 Azure 资源

**指导**：使用标记、管理组和单独的订阅（如果适用）来组织和跟踪 Site Recovery（恢复服务保管库）和其他相关资源。 

此外，使用 Azure Policy 对可使用以下内置策略定义在客户订阅中创建的资源类型施加限制： 

- 不允许的资源类型
- 允许的资源类型

定期核对清单，确保及时地从订阅中删除未经授权的资源。

- [如何创建其他 Azure 订阅](../cost-management-billing/manage/create-subscription.md)

- [如何创建管理组](../governance/management-groups/create-management-group-portal.md)

- [如何创建和使用标记](../azure-resource-manager/management/tag-resources.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4：定义并维护已批准 Azure 资源的清单

**指导**：根据客户的组织需求，创建已获批 Azure 资源以及已获批用于计算资源的软件的清单。

**责任**：客户

Azure 安全中心监视：无

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5：监视未批准的 Azure 资源

**指导**：在 Azure Policy 中使用以下内置策略定义，对可以在客户订阅中创建的资源类型施加限制： 

- 不允许的资源类型 
- 允许的资源类型

使用 Azure Resource Graph 来查询和发现订阅中的资源。

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [如何使用 Azure Resource Graph 创建查询](../governance/resource-graph/first-query-portal.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="69-use-only-approved-azure-services"></a>6.9：仅使用已批准的 Azure 服务

**指导**：在 Azure Policy 中使用以下内置策略定义，对可以在客户订阅中创建的资源类型施加限制：

- 不允许的资源类型 
- 允许的资源类型

了解如何在 Azure 中创建和管理策略对于保持与公司标准和服务级别协议的符合性来说非常重要。

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [如何使用 Azure Policy 拒绝特定的资源类型](/azure/governance/policy/samples)

**责任**：客户

Azure 安全中心监视：无

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11：限制用户与 Azure 资源管理器进行交互的能力

**指南**：使用 Azure 条件访问可通过为“Microsoft Azure 管理”应用配置“阻止访问”，限制用户与 Azure 资源管理器进行交互的能力。 这可防止在高度安全的环境中创建和更改资源。

- [如何配置条件访问以阻止访问 Azure 资源管理器](../role-based-access-control/conditional-access-azure-management.md)

**责任**：客户

Azure 安全中心监视：无

## <a name="secure-configuration"></a>安全配置

[有关详细信息，请参阅 *Azure 安全基线：* 安全配置](../security/benchmarks/security-control-secure-configuration.md)。

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1：为所有 Azure 资源建立安全配置

**指导**：使用 Azure Policy 为恢复服务保管库定义和实施标准安全配置。 

在“Microsoft.RecoveryServices”命名空间中使用 Azure Policy 别名创建自定义策略，以审核或强制实施 Site Recovery 服务的恢复服务保管库资源的配置。
- [如何查看可用的 Azure Policy 别名](/powershell/module/az.resources/get-azpolicyalias)

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3：维护安全的 Azure 资源配置

**指导**：使用 Azure Policy“[拒绝]”和“[不存在则部署]”效果对不同的 Azure 资源强制实施安全设置。
- [了解 Azure Policy 效果](../governance/policy/concepts/effects.md)

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5：安全存储 Azure 资源的配置

**指导**：如果你对恢复服务保管库和相关资源使用自定义 Azure Policy 定义，请选择 Azure Repos 来安全地存储和管理代码。

- [如何在 Azure DevOps 中存储代码](/azure/devops/repos/git/gitworkflow)

- [Azure Repos 文档](/azure/devops/repos/)

**责任**：客户

Azure 安全中心监视：无

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7：部署 Azure 资源的配置管理工具

**指导**：在“Microsoft.RecoveryServices”命名空间中使用内置的 Azure Policy 定义和 Azure Policy 别名创建自定义策略，以审核、强制实施系统配置并为其发出警报。 

另外，开发一个用于管理策略例外的流程和管道。

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9：为 Azure 资源实施自动配置监视

**指导**：在“Microsoft.RecoveryServices”命名空间中使用内置的 Azure Policy 定义和 Azure Policy 别名创建自定义策略，以审核、强制实施系统配置并为其发出警报。 

使用 Azure Policy 的“[审核]”、“[拒绝]”和“[不存在则部署]”效果自动强制实施 Azure 资源的配置。
- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="711-manage-azure-secrets-securely"></a>7.11：安全管理 Azure 机密

**指导**：客户应当管理与 Azure Key Vault 集成的 Site Recovery 机密，同时为启用了 Azure 磁盘加密的虚拟机启用灾难恢复。 

- [如何创建 Key Vault](../key-vault/general/quick-create-portal.md)

- [如何向 Key Vault 进行身份验证](../key-vault/general/authentication.md)

- [如何分配 Key Vault 访问策略](../key-vault/general/assign-access-policy-portal.md)

- [如何使用 Site Recovery 为启用了 Azure 磁盘加密的虚拟机启用 DR](azure-to-azure-how-to-enable-replication-ade-vms.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="712-manage-identities-securely-and-automatically"></a>7.12：安全自动管理标识

指导：Site Recovery 仅在客户可以在恢复服务保管库上启用系统托管的标识时才支持系统托管的标识。 这种方法同样适用于灾难恢复产品/服务中用于定义访问边界的资源。

使用托管标识在 Azure Active Directory (Azure AD) 中为 Azure 服务提供一个自动托管的标识。

使用托管标识可以向支持 Azure AD 身份验证的任何服务（包括 Key Vault）进行身份验证，无需在代码中放入任何凭据。

- [如何与 Azure 托管标识集成](https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity?tabs=core2x)

- [如何在恢复服务保管库上启用系统托管标识](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-enable-replication-private-endpoints#enable-the-managed-identity-for-the-vault)

**责任**：客户

Azure 安全中心监视：无

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13：消除意外的凭据透露

**指南**：实施凭据扫描程序来识别代码中的凭据。 凭据扫描程序还会建议将发现的凭据转移到更安全的位置，例如 Azure Key Vault。

- [如何设置凭据扫描程序](https://secdevtools.azurewebsites.net/helpcredscan.html)

**责任**：客户

Azure 安全中心监视：无

## <a name="malware-defense"></a>恶意软件防护

[有关详细信息，请参阅 *Azure 安全基线：* 恶意软件防护](../security/benchmarks/security-control-malware-defense.md)。

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2：预先扫描要上传到非计算 Azure 资源的文件

**指导**：在支持 Azure 服务（例如 Site Recovery）的底层主机上已启用 Microsoft Antimalware，但是，该软件不会针对你的内容运行。 预扫描任何要上传到非计算 Azure 资源（例如应用服务、Data Lake Storage 和 Blob 存储）的文件。

使用安全中心的数据服务威胁检测来检测上传到存储帐户的恶意软件。

- [了解适用于 Azure 云服务和虚拟机的 Microsoft Antimalware](../security/fundamentals/antimalware.md)

- [了解 Azure 安全中心的数据服务威胁检测](../security-center/azure-defender.md)

**责任**：客户

Azure 安全中心监视：无

## <a name="data-recovery"></a>数据恢复

[有关详细信息，请参阅 *Azure 安全基线：* 数据恢复](../security/benchmarks/security-control-data-recovery.md)。

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2：执行完整系统备份，并备份客户管理的所有密钥

**指导**：Site Recovery 会根据客户在其工作负荷上配置的内容，在内部使用 Azure 存储帐户来维护灾难恢复解决方案的状态。

Site Recovery 服务元数据使用的所有存储资源，其配置的类型为：读取访问异地冗余存储 (RA-GRS)。 高于 GRS 的类型（例如 RAGRS、RAG-ZRS）的存储帐户将数据复制到次要区域（离源数据的主位置数百英里以外），以便在中断期间继续为客户提供灾难恢复服务。

这超出了客户职责，由 Site Recovery 团队在内部进行处理。 客户在 Azure 中备份 Key Vault 密钥。

- [如何在 Azure 中备份密钥保管库密钥](/powershell/module/az.keyvault/backup-azkeyvaultkey)

**责任**：客户

**Azure 安全中心监视**：[Azure 安全基准](/azure/governance/policy/samples/azure-security-benchmark)是安全中心的默认策略计划，是[安全中心建议](/azure/security-center/security-center-recommendations)的基础。 安全中心会自动启用与此控件相关的 Azure Policy 定义。 与此控件相关的警报可能需要相关服务的 [Azure Defender](/azure/security-center/azure-defender) 计划。

**Azure Policy 内置定义 - Microsoft.RecoveryServices**：

[!INCLUDE [Resource Policy for Microsoft.RecoveryServices 9.2](../../includes/policy/standards/asb/rp-controls/microsoft.recoveryservices-9-2.md)]

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3：验证所有备份，包括客户管理的密钥

**指导**：对已备份的由客户管理的密钥定期进行还原测试。

- [如何在 Azure 中还原密钥保管库密钥](/powershell/module/az.keyvault/restore-azkeyvaultkey)

**责任**：客户

Azure 安全中心监视：无

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4：确保保护备份和客户管理的密钥

**指导**：将存储服务加密 (SSE) 与 Azure 的基于基础结构即服务 (IaaS) 的虚拟机配合使用，对数据进行静态加密。 在 Key Vault 中启用“软删除”，以防止意外删除或恶意删除密钥。

- [如何在密钥保管库中启用软删除](https://docs.microsoft.com/azure/storage/blobs/soft-delete-blob-overview?tabs=azure-portal)

**责任**：客户

Azure 安全中心监视：无

## <a name="incident-response"></a>事件响应

[有关详细信息，请参阅 *Azure 安全基线：* 事件响应](../security/benchmarks/security-control-incident-response.md)。

### <a name="101-create-an-incident-response-guide"></a>10.1：创建事件响应指导

**指南**：为组织制定事件响应指南。 

确保在书面的事件响应计划中定义人员的所有职责，以及事件处理或管理从检测到事件后审查的各个阶段。

- [如何在 Azure 安全中心配置工作流自动化](../security-center/security-center-planning-and-operations-guide.md)

- [关于建立自己的安全事件响应流程的指南](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process)

- [Microsoft 安全响应中心事件分析](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process)

- [客户还可以利用 NIST 的“计算机安全事件处理指南”来制定他们自己的事件响应计划](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**责任**：客户

Azure 安全中心监视：无

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2：创建事件评分和优先级设定过程

**指导**：根据安全中心分配的警报严重性，确定应当优先调查哪些警报。 严重性取决于安全中心对调查结果或用于发出警报的分析的确信程度，以及对导致警报的活动背后存在恶意意图的确信程度。

清楚地标记订阅（例如生产、非生产），并创建命名系统来对 Azure 资源进行明确标识和分类。

- [Azure 安全中心中的安全警报](../security-center/security-center-alerts-overview.md) 

- [使用标记整理 Azure 资源](../azure-resource-manager/management/tag-resources.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="103-test-security-response-procedures"></a>10.3：测试安全响应过程

**指导**：定期执行演练来测试系统的事件响应功能。 识别弱点和差距，并根据需要修改计划

- [参阅 NIST 出版物 - IT 计划和功能的测试、训练和演练计划指南](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**责任**：客户

Azure 安全中心监视：无

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4：提供安全事件联系人详细信息，并针对安全事件配置警报通知

**指南**：如果 Microsoft 安全响应中心 (MSRC) 发现非法或未经授权的某方访问了客户的数据，Microsoft 将使用安全事件联系人信息与你取得联系。 

创建一个流程来审核已发生的事件，以确保解决问题。

- [如何设置 Azure 安全中心安全联系人](../security-center/security-center-provide-security-contact-details.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5：将安全警报整合到事件响应系统中

**指导**：使用连续导出功能导出安全中心警报和建议。 使用连续导出可以手动导出或者持续导出警报和建议。 

根据需要使用安全中心数据连接器将警报流式传输到 Azure Sentinel。
- [如何配置连续导出](../security-center/continuous-export.md)

- [如何将警报流式传输到 Azure Sentinel](../sentinel/connect-azure-security-center.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="106-automate-the-response-to-security-alerts"></a>10.6：自动响应安全警报

**指导**：使用安全中心内的工作流自动化功能可以通过“逻辑应用”针对安全警报和建议自动触发响应。
- [如何配置工作流自动化和逻辑应用](../security-center/workflow-automation.md)

**责任**：客户

Azure 安全中心监视：无

## <a name="penetration-tests-and-red-team-exercises"></a>渗透测试和红队练习

[有关详细信息，请参阅 *Azure 安全基线：* 渗透测试和红队演练](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)。

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1：定期对 Azure 资源执行渗透测试，确保修正所有发现的关键安全问题

**指导**：请遵循 Microsoft 云渗透测试互动规则，确保你的渗透测试不违反 Microsoft 政策。 使用 Microsoft 红队演练策略和执行，以及针对 Microsoft 托管云基础结构、服务和应用程序执行现场渗透测试。

- [参与的渗透测试规则](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Microsoft 云红色组合](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**责任**：共享

Azure 安全中心监视：无

## <a name="next-steps"></a>后续步骤

- 参阅 [Azure 安全基准 V2 概述](/azure/security/benchmarks/overview)
- 详细了解 [Azure 安全基线](/azure/security/benchmarks/security-baselines-overview)
