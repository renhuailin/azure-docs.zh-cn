---
title: 虚拟网络的 Azure 安全基线
description: 虚拟网络安全基线为实现 Azure 安全基准中指定的安全建议提供过程指南和资源。
author: msmbaldwin
ms.service: virtual-network
ms.topic: conceptual
ms.date: 10/01/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: d183edbe82d59b9d0c1ebe075dd2639137d5081c
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/14/2021
ms.locfileid: "98200866"
---
# <a name="azure-security-baseline-for-virtual-network"></a>虚拟网络的 Azure 安全基线

此安全基线将 [Azure 安全基准版本 1.0](../security/benchmarks/overview-v1.md) 中的指南应用于 Azure 虚拟网络。 Azure 安全基准提供有关如何在 Azure 上保护云解决方案的建议。 内容按“安全控制”分组，这些控制根据适用于 Azure 虚拟网络的 Azure 安全基准和相关指南进行定义。 排除了不适用于 Azure 虚拟网络的控制。

若要查看 Azure 虚拟网络如何完全映射到 Azure 安全基准，请参阅[完整的 Azure 虚拟网络安全基线映射文件](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)。

## <a name="network-security"></a>网络安全性

[有关详细信息，请参阅 *Azure 安全基线：* 网络安全](../security/benchmarks/security-control-network-security.md)。

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2：监视并记录虚拟网络、子网和网络接口的配置与流量

**指导**：使用安全中心并遵循网络保护建议来帮助保护 Azure 中的网络资源。 

将网络安全组流日志发送到 Log Analytics 工作区，并使用流量分析提供有关 Azure 云中流量流的见解。 流量分析提供可视化网络活动、识别热点、识别安全威胁、了解流量流模式，以及查明网络不当配置的功能。 

使用 Azure Monitor 日志可以深入了解环境。 工作区应该用于整理和分析数据，并可与 Application Insights 和安全中心等其他 Azure 服务集成。 

选择要发送到 Azure 存储帐户或事件中心的资源日志。 也可以使用不同的平台来分析日志。 

- [如何启用 NSG 流日志](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [如何启用和使用流量分析](../network-watcher/traffic-analytics.md)

- [了解安全中心提供的网络安全性](../security-center/security-center-network-recommendations.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4：拒绝与已知恶意的 IP 地址进行通信

**指导**：在 Azure 虚拟网络上启用分布式拒绝服务 (DDoS) 标准保护，以防范 DDoS 攻击。

在组织的每个网络边界上部署 Azure 防火墙，启用基于威胁情报的筛选并将其配置为针对恶意网络流量执行“发出警报并拒绝”操作。

使用安全中心的威胁防护功能来检测与已知恶意 IP 地址的通信。

应用 Azure 安全中心针对网络安全组配置的自适应网络强化建议，这些配置基于实际流量和威胁情报限制端口和源 IP。 

- [使用 Azure 门户管理 Azure DDoS 防护标准](manage-ddos-protection.md)

- [基于 Azure 防火墙威胁智能的筛选](../firewall/threat-intel.md)

- [安全中心的威胁防护](/azure/security-center/threat-protection)

- [Azure 安全中心内的自适应网络强化](../security-center/security-center-adaptive-network-hardening.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="15-record-network-packets"></a>1.5：记录网络数据包

**指导**：除了常用的数据包捕获工具，还可以使用 VPN 网关的数据包捕获来记录网络数据包。 

还可以通过 Azure 市场产品中提供的数据包代理合作伙伴解决方案，查看提供终端接入点 (TAP) 或网络可见性功能的基于代理或 NVA 的解决方案。

- [为 VPN 网关配置数据包捕获](../vpn-gateway/packet-capture.md) 

- [网络虚拟设备合作伙伴](https://azure.microsoft.com/solutions/network-appliances)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6：部署基于网络的入侵检测/入侵防护系统 (IDS/IPS)

**指导**：使用部署在启用威胁情报的虚拟网络上的 Azure 防火墙。 使用基于 Azure 防火墙威胁情报的筛选功能，发出警报或拒绝往返于已知恶意 IP 地址和域的流量。 IP 地址和域源自 Microsoft 威胁智能源。 

也可以从 Azure 市场中选择一种产品/服务，该产品/服务应支持包含有效负载检查功能的 IDS/IPS 功能。

在组织的每个网络边界上部署所选的防火墙解决方案，以检测和/或拒绝恶意流量。

- [如何部署 Azure 防火墙](../firewall/tutorial-firewall-deploy-portal.md)

- [如何配置 Azure 防火墙警报](../firewall/threat-intel.md)

- [Azure 市场](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8：最大程度地降低网络安全规则的复杂性和管理开销

**指导**：在网络安全组或 Azure 防火墙中使用虚拟网络服务标记来定义网络访问控制。 创建安全规则时，可以使用服务标记代替特定 IP 地址。 在规则的相应源或目标字段中指定服务标记名称（例如 ApiManagement），允许或拒绝相应服务的流量。 Microsoft 会管理服务标记包含的地址前缀，并会在地址发生更改时自动更新服务标记。

使用应用程序安全组来帮助简化复杂的安全配置。 通过应用程序安全组，可将网络安全配置为应用程序结构的自然扩展。 这使你能够对虚拟机进行分组，并基于这些组定义网络安全策略。

- [了解并使用服务标记](service-tags-overview.md)

- [了解并使用应用程序安全组](/azure/virtual-network/security-overview#application-security-groups)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9：维护网络设备的标准安全配置

**指导**：使用 Azure Policy 为网络资源定义和实现标准安全配置，并评审内置网络策略定义以供实现。

请参阅安全中心的默认策略，其中提供了与虚拟网络相关的可用安全建议。

使用 Azure 蓝图，通过在单个蓝图定义中打包关键环境项目（例如 Azure 资源管理器模板、Azure 基于角色的访问控制 (Azure RBAC) 分配和策略），来简化大规模的 Azure 部署。 可将 Azure 蓝图应用到新的订阅，通过版本控制来微调控制措施和管理。 

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [用于网络的 Azure Policy 示例](../governance/policy/samples/built-in-policies.md#network) 

- [如何创建 Azure 蓝图](../governance/blueprints/create-blueprint-portal.md)

- [在 Azure 安全中心启用监视](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Security%20Center/AzureSecurityCenter.json)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="110-document-traffic-configuration-rules"></a>1.10：阐述流量配置规则

**指导**：将标记用于网络安全组以及其他与网络安全和流量流有关的资源。 使用各个网络安全组规则的“说明”字段为允许流量传入/传出网络的任何规则指定业务需要、持续时间以及其他信息。
使用标记相关的任何内置 Azure Policy 定义（例如“需要标记及其值”）来确保使用标记创建所有资源，并在有现有资源不带标记时发出通知。

选择 Azure PowerShell 或 Azure CLI，基于资源的标记查找资源或对其执行操作。

- [如何创建和使用标记](/azure/azure-resource-manager/resource-group-using-tags)

- [如何创建虚拟网络](quick-create-portal.md)

- [如何创建采用安全配置的 NSG](tutorial-filter-network-traffic.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11：使用自动化工具来监视网络资源配置和检测更改

**指导**：使用 Azure 活动日志监视网络资源配置，并检测虚拟网络的更改。 在 Azure Monitor 中创建当关键资源发生更改时触发的警报。

- [如何查看和检索 Azure 活动日志事件](/azure/azure-monitor/platform/activity-log-view)

- [如何在 Azure Monitor 中创建警报](../azure-monitor/platform/alerts-activity-log.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

## <a name="logging-and-monitoring"></a>日志记录和监视

[有关详细信息，请参阅 *Azure 安全基线：* 日志记录和监视](../security/benchmarks/security-control-logging-monitoring.md)。

### <a name="22-configure-central-security-log-management"></a>2.2：配置中心安全日志管理

**指导**：启用 Azure Monitor 以访问审核和活动日志，其中包括事件源、日期、用户、时间戳、源地址、目标地址和其他有用元素。 

在 Azure Monitor 中，使用 Log Analytics 工作区查询和执行分析，并使用 Azure 存储帐户进行长期/存档存储。
或者，可以启用将数据加入 Azure Sentinel 或第三方 SIEM 的功能。 

- [如何使用 Azure Monitor 收集平台日志和指标](../azure-monitor/platform/diagnostic-settings.md) 

- [查看和检索 Azure 活动日志事件](/azure/azure-monitor/platform/activity-log-view)

- [如何加入 Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：为 Azure 资源启用审核日志记录

**指导**：使 Azure Monitor 可以访问审核和活动日志，其中包括事件源、日期、用户、时间戳、源地址、目标地址和其他有用元素。

- [如何使用 Azure Monitor 收集平台日志和指标](../azure-monitor/platform/diagnostic-settings.md) 

- [查看和检索 Azure 活动日志事件](/azure/azure-monitor/platform/activity-log-view)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="25-configure-security-log-storage-retention"></a>2.5：配置安全日志存储保留期

**指南**：在 Azure Monitor 中，根据组织的合规性规则设置 Log Analytics 工作区保持期。 使用 Azure 存储帐户进行安全日志存储保留期的长期/存档存储。

- [更改 Log Analytics 中的数据保留期](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

- [如何为 Azure 存储帐户日志配置保留策略](../storage/common/storage-monitor-storage-account.md#configure-logging)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="26-monitor-and-review-logs"></a>2.6：监视和查看日志

**指导**：分析和监视日志中的异常行为，并定期查看结果。 使用 Azure Monitor 的 Log Analytics 工作区来查询和执行分析，并使用 Azure 存储帐户进行长期/存档存储。 

或者，可以启用将数据加入 Azure Sentinel 或第三方 SIEM 的功能。 

- [了解 Log Analytics 工作区](../azure-monitor/log-query/get-started-portal.md)

- [如何在 Azure Monitor 中执行自定义查询](../azure-monitor/log-query/get-started-queries.md)

- [如何加入 Azure Sentinel](../sentinel/quickstart-onboard.md)

- [如何开始使用 Azure Monitor 和第三方 SIEM 集成](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7：针对异常活动启用警报

**指导**：使用安全中心和 Log Analytics 工作区监视安全日志和事件中的异常活动并发出警报。

或者，可以启用将数据加入 Azure Sentinel 或用于警报的第三方 SIEM 的功能。

- [如何在安全中心管理警报](../security-center/security-center-managing-and-responding-alerts.md)

- [如何针对 Log Analytics 日志数据发出警报](../azure-monitor/learn/tutorial-response.md)

- [如何加入 Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="29-enable-dns-query-logging"></a>2.9：启用 DNS 查询日志记录

**指导**：根据你的组织需要，从 Azure 市场为 DNS 日志记录解决方案实现第三方解决方案。

**Azure 安全中心监视**：目前不可用

**责任**：客户

## <a name="identity-and-access-control"></a>标识和访问控制

[有关详细信息，请参阅 *Azure 安全基线：* 标识和访问控制](../security/benchmarks/security-control-identity-access-control.md)。

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1：维护管理帐户的清单

**指导**：使用可显式分配并可查询的 Azure Active Directory (Azure AD) 内置管理员角色。 

使用 Azure AD PowerShell 模块执行即席查询，以发现属于管理组的成员的帐户。

- [如何使用 PowerShell 获取 Azure AD 中的目录角色](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [如何使用 PowerShell 获取 Azure AD 中目录角色的成员](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="33-use-dedicated-administrative-accounts"></a>3.3：使用专用管理帐户

**指南**：围绕专用管理帐户的使用创建标准操作程序。 使用安全中心标识和访问管理来监视管理帐户的数量。

使用 Microsoft 服务和 Azure 资源管理器 Azure AD Privileged Identity Management 特权角色，启用实时/刚好足够的访问权限。 

- [详细了解 Privileged Identity Management](/azure/active-directory/privileged-identity-management/)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4：使用 Azure Active Directory 单一登录 (SSO)

**指导**：使用 Azure Active Directory (Azure AD) SSO，而不是为每个服务配置单个独立凭据。 使用安全中心的标识和访问管理建议。

- [单一登录到 Azure Active Directory 中的应用程序](../active-directory/manage-apps/what-is-single-sign-on.md) 

- [如何在 Azure 安全中心监视标识和访问](../security-center/security-center-identity-access.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5：对所有基于 Azure Active Directory 的访问使用多重身份验证

**指导**：启用 Azure Active Directory (Azure AD) 多重身份验证 (MFA)，并遵循安全中心标识和访问管理建议。

- [如何在 Azure 中启用 MFA](../active-directory/authentication/howto-mfa-getstarted.md) 

- [如何在 Azure 安全中心监视标识和访问](../security-center/security-center-identity-access.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3.6：使用由 Azure 管理的安全工作站执行管理任务

**指导**：使用配置了多重身份验证 (MFA) 的特权访问工作站 (PAW) 来登录并访问 Azure 网络资源。

- [了解特权访问工作站](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/) 

- [如何在 Azure 中启用 MFA](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7：记录来自管理帐户的可疑活动并对其发出警报

**指导**：使用 Azure Active Directory (Azure AD) 风险检测查看有关风险用户行为的警报和报告。 

请将 Azure 安全中心风险检测警报引入 Azure Monitor 中，并使用操作组配置自定义警报/通知。

- [了解安全中心风险检测 (可疑活动) ](/azure/active-directory/reports-monitoring/concept-risk-events) 

- [如何将 Azure 活动日志集成到 Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) 

- [如何为自定义警报和通知配置操作组](../azure-monitor/platform/action-groups.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8：仅从批准的位置管理 Azure 资源

**指导**：使用条件访问命名位置，仅允许从 IP 地址范围或国家/地区的特定逻辑分组进行访问。

- [如何在 Azure 中配置命名位置](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="39-use-azure-active-directory"></a>3.9：使用 Azure Active Directory

**指导**：使用 Azure Active Directory (Azure AD) 作为服务的中心身份验证和授权系统。 Azure AD 通过对静态数据和传输中数据使用强加密以及加盐、哈希处理和安全存储用户凭据来保护数据。  

- [如何创建和配置 Azure AD 实例](../active-directory-domain-services/tutorial-create-instance.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10：定期审查和协调用户访问

**指导**：使用 Azure Active Directory (Azure AD) 提供日志来帮助发现过时的帐户。 

可以执行 Azure 标识访问评审来高效地管理组成员身份、对企业应用程序的访问和角色分配。 应定期评审用户的访问权限，确保只有活动用户才持续拥有访问权限。

- [了解 Azure AD 报告](/azure/active-directory/reports-monitoring/)

- [如何使用 Azure 标识访问评审](../active-directory/governance/access-reviews-overview.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11：监视尝试访问已停用凭据的行为

**指导**：使用任何 SIEM 或基于访问的监视工具，来集成 Azure Active Directory (Azure AD) 登录活动、审核和风险事件日志源。 

通过为 Azure Active Directory 用户帐户创建诊断设置并将审核日志和登录日志发送到 Log Analytics 工作区来简化此过程。 可以在 Log Analytics 工作区中配置任何所需的警报。

- [如何将 Azure 活动日志集成到 Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12：针对帐户登录行为偏差发出警报

**指南**：使用 Azure Active Directory (Azure AD) 的风险和标识保护功能，配置对检测到的与虚拟网络用户标识相关的可疑操作的自动响应。 将数据引入 Azure Sentinel 以进行进一步调查。

- [如何查看 Azure AD 风险登录](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [如何配置和启用标识保护风险策略](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [如何加入 Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

## <a name="data-protection"></a>数据保护

[有关详细信息，请参阅 *Azure 安全基线：* 数据保护](../security/benchmarks/security-control-data-protection.md)。

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4：加密传输中的所有敏感信息

**指导**：加密传输中的所有敏感信息。 确保连接到虚拟网络中的 Azure 资源的任何客户端都能够协商 TLS 1.2 或更高版本。 请按照安全中心的建议，了解静态加密和传输中加密。 

Microsoft 提供了多个可供客户使用的选项，用于在 Azure 网络内部和外部通过 Internet 向最终用户提供数据传输的安全。 其中包括通过虚拟专用网（使用 IPsec/IKE 加密）、传输层安全性 (TLS) 1.2 或更高版本（通过 Azure 组件，例如应用程序网关或 Azure Front Door）、直接在 Azure 虚拟机上的协议（例如 Windows IPsec 或 SMB）等进行通信。

此外，对于在 Azure 数据中心之间传输的所有 Azure 流量，启用使用 MACsec（数据链路层的 IEEE 标准）的“默认加密”，以确保客户数据的机密性和完整性。

- [了解 Azure 传输中的加密](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

**Azure 安全中心监视**：是

**责任**：共享

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4.6：使用 Azure RBAC 管理对资源的访问 

**指导**：使用 Azure 基于角色的访问控制 (Azure RBAC) 来管理对数据和资源的访问。 否则，请使用特定于服务的访问控制方法。 

选择内置角色，如所有者、参与者或网络参与者，并将角色分配到适当的范围。 例如，可将具有虚拟网络所需特定权限的虚拟网络功能的子集分配给这些角色中的任何一个。 

- [如何配置 Azure RBAC](../role-based-access-control/role-assignments-portal.md)

- [计划虚拟网络](virtual-network-vnet-plan-design-arm.md#permissions)

- [评审 Azure 内置角色](../role-based-access-control/built-in-roles.md#networking)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9：记录对关键 Azure 资源的更改并对此类更改发出警报

**指导**：结合使用 Azure Monitor 和 Azure 活动日志，创建在关键 Azure 资源（例如虚拟网络和网络安全组）发生更改时发出的警报。

- [网络安全组的诊断日志记录](virtual-network-nsg-manage-log.md)

- [如何针对 Azure 活动日志事件创建警报](../azure-monitor/platform/alerts-activity-log.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

## <a name="inventory-and-asset-management"></a>库存和资产管理

[有关详细信息，请参阅 *Azure 安全基线：* 清单和资产管理](../security/benchmarks/security-control-inventory-asset-management.md)。

### <a name="61-use-automated-asset-discovery-solution"></a>6.1：使用自动化资产发现解决方案

**指导**：使用 Azure Resource Graph 查询和发现订阅中的所有网络资源（例如虚拟网络）。 确保你在租户中拥有适当的（读取）权限，并且可以枚举所有 Azure 订阅，以及订阅中的资源。

- [如何使用 Azure Graph 创建查询](../governance/resource-graph/first-query-portal.md) 

- [如何查看 Azure 订阅](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0) 

- [了解 Azure RBAC](../role-based-access-control/overview.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="62-maintain-asset-metadata"></a>6.2：维护资产元数据

**指导**：将标记应用于 Azure 资源，从而将元数据按逻辑组织到分类中。

- [如何创建和使用标记](/azure/azure-resource-manager/resource-group-using-tags)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="63-delete-unauthorized-azure-resources"></a>6.3：删除未经授权的 Azure 资源

**指导**：使用标记、管理组和单独订阅（如果适用）来组织和跟踪虚拟网络和相关资源。 定期核对清单，确保及时地从订阅中删除未经授权的资源。

- [如何创建其他 Azure 订阅](/azure/billing/billing-create-subscription) 

- [如何创建管理组](/azure/governance/management-groups/create) 

- [如何创建和使用标记](/azure/azure-resource-manager/resource-group-using-tags)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4：定义并维护已批准的 Azure 资源的清单

**指导**：你将需要根据组织需求，创建已获批 Azure 资源以及已获批用于计算资源的软件的清单。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5：监视未批准的 Azure 资源

**指导**：在 Azure Policy 中使用以下内置策略定义，对可以在客户订阅中创建的资源类型施加限制：
- 不允许的资源类型 

- 允许的资源类型 

在基于高安全性的环境（例如具有 Azure 存储帐户的环境）中使用 Azure Resource Graph 查询或发现订阅中的资源。 

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md) 

- [如何使用 Azure Graph 创建查询](../governance/resource-graph/first-query-portal.md)

- [适用于虚拟网络的 Azure Policy 内置示例](/azure/virtual-network/policy-samples)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7：删除未批准的 Azure 资源和软件应用程序

**指导**：根据组织策略的要求，阻止使用 Azure Policy 创建或使用资源。 实现删除未授权资源的过程。

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="69-use-only-approved-azure-services"></a>6.9：仅使用已批准的 Azure 服务

**指导**：在 Azure Policy 中使用以下内置策略定义，对可以在客户订阅中创建的资源类型施加限制：
- 不允许的资源类型 

- 允许的资源类型 

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md) 

- [如何使用 Azure Policy 拒绝特定的资源类型](/azure/governance/policy/samples/not-allowed-resource-types)

- [适用于虚拟网络的 Azure Policy 内置示例](/azure/virtual-network/policy-samples)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11：限制用户与 Azure 资源管理器进行交互的能力

**指南**：通过为 "Microsoft Azure 管理" 应用配置 "阻止访问"，使用 Azure 条件访问来限制用户与 Azure 资源管理器的交互能力。

- [如何配置条件访问以阻止访问 Azure 资源管理器](../role-based-access-control/conditional-access-azure-management.md)

**Azure 安全中心监视**：是

**责任**：客户

## <a name="secure-configuration"></a>安全配置

[有关详细信息，请参阅 *Azure 安全基线：* 安全配置](../security/benchmarks/security-control-secure-configuration.md)。

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1：为所有 Azure 资源建立安全配置

**指导**：使用 Azure Policy 别名创建自定义策略，以审核或强制实施 Azure 网络资源的配置并使用内置 Azure Policy 目标。

使用 Azure 资源管理器导出 JavaScript 对象表示法 (JSON) 中你构建的任何模板，并对其进行评审，以确保配置满足或超过组织的安全要求。

实现来自安全中心的建议作为 Azure 资源的安全配置基线。

- [如何查看可用的 Azure Policy 别名](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [教程：创建和管理策略以强制实施符合性](../governance/policy/tutorials/create-and-manage.md)

- [适用于虚拟网络的 Azure Policy 内置示例](/azure/virtual-network/policy-samples)

- [在 Azure 门户中将单资源和多资源导出到模板](../azure-resource-manager/templates/export-template-portal.md)

- [安全建议 - 参考指南](../security-center/recommendations-reference.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3：维护安全的 Azure 资源配置

**指导**：使用 Azure 资源管理器模板和 Azure 策略安全地配置与虚拟网络和相关资源关联的 Azure 资源。  Azure 资源管理器模板是基于 JSON（JavaScript 对象表示法）的文件，用于与 Azure 资源一起部署虚拟机。 Microsoft 对基本模板进行维护。  

使用 Azure Policy“[拒绝]”和“[不存在则部署]”效果对不同的 Azure 资源强制实施安全设置。

- [有关创建 Azure 资源管理器模板的信息](../virtual-machines/windows/ps-template.md) 

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md) 

- [了解 Azure Policy 效果](../governance/policy/concepts/effects.md)

- [适用于虚拟网络的 Azure 资源管理器模板示例](template-samples.md)

- [适用于虚拟网络的 Azure Policy 内置示例](/azure/virtual-network/policy-samples)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5：安全存储 Azure 资源的配置

**指导**：使用 Azure DevOps 安全地存储和管理代码，如自定义 Azure 策略、Azure 资源管理器模板、Desired State Configuration 脚本。 等等。

若要访问希望在 Azure DevOps 中管理的资源（例如代码、生成和工作跟踪），必须具有对这些资源的权限。 大多数权限通过内置安全组授予。 你可以向特定用户、内置安全组或者 Azure Active Directory (Azure AD)（如果与 Azure DevOps 集成）或 Active Directory（如果与 Team Foundation Server 集成）中定义的组授予权限，或拒绝向其授予权限。

- [如何在 Azure DevOps 中存储代码](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops) 

- [关于 Azure DevOps 中的权限和组](/azure/devops/organizations/security/about-permissions)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7：部署 Azure 资源的配置管理工具

**指导**：使用 Azure Policy 为 Azure 资源定义和实施标准安全配置。 使用 Azure Policy 别名创建自定义策略，以审核或强制实施 Azure 资源的网络配置以及与特定资源相关的任何内置策略定义。 

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [如何使用别名](../governance/policy/concepts/definition-structure.md#aliases)

- [适用于虚拟网络的 Azure Policy 内置示例](/azure/virtual-network/policy-samples)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9：为 Azure 资源实施自动配置监视

**指导**：使用安全中心对 Azure 虚拟网络和相关资源执行基线扫描。 使用 Azure Policy 发出警告并审核 Azure 资源配置。

- [如何在安全中心修正建议](../security-center/security-center-remediate-recommendations.md)

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [适用于虚拟网络的 Azure Policy 内置示例](/azure/virtual-network/policy-samples)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="711-manage-azure-secrets-securely"></a>7.11：安全管理 Azure 机密

**指导**：将托管服务标识与 Azure Key Vault 结合使用，以便简化和保护托管在 Azure 虚拟网络中的 Azure 资源的机密管理。

- [如何与 Azure 托管标识集成](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md) 

- [如何创建 Key Vault](/azure/key-vault/quick-create-portal) 

- [如何使用托管标识提供 Key Vault 身份验证](/azure/key-vault/managed-identity)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13：消除意外的凭据透露

**指南**：实施凭据扫描程序来识别代码中的凭据。 凭据扫描程序还会建议将发现的凭据转移到更安全的位置，例如 Azure Key Vault。

- [如何设置凭据扫描程序](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure 安全中心监视**：目前不可用

**责任**：客户

## <a name="data-recovery"></a>数据恢复

[有关详细信息，请参阅 *Azure 安全基线：* 数据恢复](../security/benchmarks/security-control-data-recovery.md)。

### <a name="91-ensure-regular-automated-back-ups"></a>9.1：确保定期执行自动备份

**指导**：使用 Azure 资源管理器部署虚拟网络和相关资源。 Azure 资源管理器提供导出模板的功能，这些模板可用作还原虚拟网络和相关资源的备份。  使用 Azure 自动化定期调用 Azure 资源管理器模板导出 API。

- [Azure 资源管理器概述](../azure-resource-manager/management/overview.md)

- [适用于虚拟网络的 Azure 资源管理器模板示例](template-samples.md)

- [在 Azure 门户中将单资源和多资源导出到模板](../azure-resource-manager/templates/export-template-portal.md)

- [资源组 - 导出模板](/rest/api/resources/resourcegroups/exporttemplate)

- [Azure 自动化简介](../automation/automation-intro.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2：执行完整系统备份，并备份客户管理的所有密钥

**指导**：使用 Azure 资源管理器部署虚拟网络和相关资源。 Azure 资源管理器提供导出模板的功能，这些模板可用作还原虚拟网络和相关资源的备份。 使用 Azure 自动化定期调用 Azure 资源管理器模板导出 API。 在 Azure Key Vault 中备份客户管理的密钥。

- [Azure 资源管理器概述](../azure-resource-manager/management/overview.md)

- [适用于虚拟网络的 Azure 资源管理器模板示例](template-samples.md)

- [在 Azure 门户中将单资源和多资源导出到模板](../azure-resource-manager/templates/export-template-portal.md)

- [资源组 - 导出模板](/rest/api/resources/resourcegroups/exporttemplate)

- [Azure 自动化简介](../automation/automation-intro.md)

- [如何在 Azure 中备份密钥保管库密钥](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3：验证所有备份，包括客户管理的密钥

**指导**：定期将 Azure 资源管理器模板部署到独立订阅，并对备份的客户管理的密钥进行还原测试。

- [使用 ARM 模板和 Azure 门户部署资源](../azure-resource-manager/templates/deploy-portal.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4：确保保护备份和客户管理的密钥

**指南**：使用 Azure DevOps 安全地存储和管理你的代码，例如自定义 Azure Policy 定义和 Azure 资源管理器模板。 

向特定用户、内置安全组或者 Azure Active Directory (Azure AD)（如果与 Azure DevOps 集成）或 Active Directory（如果与 Team Foundation Server 集成）中定义的组授予权限，或拒绝向其授予权限。  

使用 Azure 基于角色的访问控制 (Azure RBAC) 保护客户管理的密钥。   

在密钥保管库中启用软删除和清除保护，以防止意外删除或恶意删除密钥。  

- [如何在 Azure DevOps 中存储代码](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [关于 Azure DevOps 中的权限和组](/azure/devops/organizations/security/about-permissions)

- [如何在密钥保管库中启用软删除和清除保护](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal) 

- [Azure 存储 Blob 的软删除](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="incident-response"></a>事件响应

[有关详细信息，请参阅 *Azure 安全基线：* 事件响应](../security/benchmarks/security-control-incident-response.md)。

### <a name="101-create-an-incident-response-guide"></a>10.1：创建事件响应指导

**指南**：为组织制定事件响应指南。 确保在书面的事件响应计划中定义人员职责，以及事件处理/管理从检测到事件后审查的各个阶段。  

- [关于建立自己的安全事件响应流程的指南](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft 安全响应中心事件分析](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [利用 NIST 的“计算机安全事件处理指南”，帮助制定自己的事件响应计划](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2：创建事件评分和优先级设定过程

**指导**：安全中心为每条警报分配严重性，以帮助你优先处理应该最先调查的警报。 严重性取决于安全中心在发出警报时所依据的检测结果和分析结果的置信度，以及导致发出警报的活动的恶意企图的置信度。

使用标记清楚地标记订阅（例如生产或非生产）并创建命名系统来对 Azure 资源进行明确标识和分类，特别是处理敏感数据的资源。  你的责任是根据发生事件的 Azure 资源和环境的关键性确定修正警报的优先级。

- [安全中心的“安全警报”](../security-center/security-center-alerts-overview.md)

- [使用标记整理 Azure 资源](/azure/azure-resource-manager/resource-group-using-tags)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="103-test-security-response-procedures"></a>10.3：测试安全响应过程

**指南**：定期执行演练来测试系统的事件响应功能，以帮助保护 Azure 资源。 识别弱点和差距，并根据需要修改计划。

- [NIST 发布 - IT 计划和功能的测试、训练和演练计划指南](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4：提供安全事件联系人详细信息，并针对安全事件配置警报通知

**指导**：如果 Microsoft 安全响应中心 (MSRC) 发现数据被某方非法访问或未经授权访问，Microsoft 会使用安全事件联系信息联系用户。 事后审查事件，确保问题得到解决。

- [如何设置安全中心安全联系人](../security-center/security-center-provide-security-contact-details.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5：将安全警报整合到事件响应系统中

**指导**：使用连续导出功能导出安全中心警报和建议，以便确定 Azure 资源的风险。 使用连续导出可以手动导出或者持续导出警报和建议。 

也可以使用安全中心数据连接器将警报流式传输到 Azure Sentinel。

- [如何配置连续导出](../security-center/continuous-export.md)

- [如何将警报流式传输到 Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="106-automate-the-response-to-security-alerts"></a>10.6：自动响应安全警报

**指导**：使用安全中心内的工作流自动化功能，通过“逻辑应用”针对安全警报和建议自动触发响应，以保护 Azure 资源。

- [如何配置工作流自动化和逻辑应用](../security-center/workflow-automation.md)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="penetration-tests-and-red-team-exercises"></a>渗透测试和红队练习

[有关详细信息，请参阅 *Azure 安全基线：* 渗透测试和红队演练](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)。

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1：定期对 Azure 资源执行渗透测试，确保修正所有发现的关键安全问题

**指导**：请遵循 Microsoft 互动规则，确保你的渗透测试不违反 Microsoft 政策。 使用 Microsoft 红队演练策略和执行，以及针对 Microsoft 托管云基础结构、服务和应用程序执行的现场渗透测试。

- [参与的渗透测试规则](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Microsoft 云红色组合](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure 安全中心监视**：不适用

**责任**：共享

## <a name="next-steps"></a>后续步骤

- 请参阅 [Azure 安全基准](/azure/security/benchmarks/overview)
- 详细了解 [Azure 安全基线](/azure/security/benchmarks/security-baselines-overview)
