---
title: 适用于自动化的 Azure 安全基线
description: 自动化安全基线为实现 Azure 安全基准中指定的安全建议提供过程指南和资源。
author: msmbaldwin
ms.service: automation
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 55440c3bec940e0cd5fd4c4d644801e7012b5e95
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101701463"
---
# <a name="azure-security-baseline-for-automation"></a>适用于自动化的 Azure 安全基线

此安全基线将 [Azure 安全基准版本 1.0](../security/benchmarks/overview-v1.md) 中的指南应用到 azure 自动化。 Azure 安全基准提供有关如何在 Azure 上保护云解决方案的建议。
内容按照 Azure 安全基准定义的 **安全控制措施** 进行分组，以及适用于 azure 自动化的相关指南。 排除了不适用于 Azure Automation 的 **控件**。

 
若要查看 Azure 自动化如何完全映射到 Azure 安全基准，请参阅 [完整的 Azure 自动化安全基线映射文件](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)。

## <a name="network-security"></a>网络安全

[有关详细信息，请参阅 *Azure 安全基线：* 网络安全性](../security/benchmarks/security-control-network-security.md)。

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1：保护虚拟网络中的 Azure 资源

**指导**：Azure·自动化帐户尚不支持使用 Azure 专用链接来限制通过专用终结点访问服务的操作。 针对 Azure 中的资源进行身份验证和运行的 runbook 在 Azure 沙箱上运行，并利用共享的后端资源，Microsoft 负责彼此隔离;它们的网络是不受限制的，可以访问公共资源。 除了支持混合 Runbook 辅助角色之外，Azure 自动化目前不具备专用网络的虚拟网络集成。 如果你使用的是无混合 Runbook 辅助角色的现成服务，则此控制不适用。

若要进一步隔离 runbook，可以使用在 Azure 虚拟机上运行的混合 Runbook 辅助角色。 创建 Azure 虚拟机时，必须创建虚拟网络 (VNet) 或使用现有 VNet，并为 VM 配置子网。 确保所有部署的子网都应用了网络安全组，且具有特定于应用程序受信任端口和源的网络访问控制。 有关特定于服务的要求，请参阅该特定服务的安全建议。 或者，如果你有特定的要求，还可以使用 Azure 防火墙来满足该要求。

- [Azure 中的虚拟网络和虚拟机](../virtual-machines/network-overview.md)

- [如何创建虚拟网络](../virtual-network/quick-create-portal.md)

- [如何创建采用安全配置的 NSG](../virtual-network/tutorial-filter-network-traffic.md)

- [如何部署和配置 Azure 防火墙](../firewall/tutorial-firewall-deploy-portal.md)

- [Runbook 执行环境](https://docs.microsoft.com/azure/automation/automation-runbook-execution#runbook-execution-environment)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2：监视并记录虚拟网络、子网和网络接口的配置与流量

**指导**：除了支持混合 Runbook 辅助角色之外，Azure 自动化目前不具备专用网络的虚拟网络集成。 如果你使用的是无混合 Runbook 辅助角色的现成服务，则此控制不适用。

如果你使用的是由 Azure 虚拟机提供支持的混合 Runbook 辅助角色，请确保为包含这些辅助角色的子网启用网络安全组 (NSG)，并将流日志配置为将日志转发到存储帐户以进行流量审核。 你还可以将 NSG 流日志转发到 Log Analytics 工作区，并使用流量分析来提供有关 Azure 云中通信流的见解。 流量分析的优势包括能够可视化网络活动、识别热点、识别安全威胁、了解流量流模式，以及查明网络不当配置。

尽管 NSG 规则和用户定义的路由不适用于专用终结点，但仍支持出站连接的 NSG 流日志和监视信息，并且可以使用它们。

- [如何启用 NSG 流日志](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [如何启用和使用流量分析](../network-watcher/traffic-analytics.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4：拒绝与已知恶意的 IP 地址进行通信

**指导**：除了支持混合 Runbook 辅助角色之外，Azure 自动化目前不具备专用网络的虚拟网络集成。 如果你使用的是无混合 Runbook 辅助角色的现成服务，则此控制不适用。

如果你使用的是由 Azure 虚拟机支持的混合 Runbook 辅助角色，请在托管混合 Runbook 辅助角色的虚拟网络上启用分布式拒绝服务 (DDoS) 标准保护，以防范 DDoS 攻击。 使用 Azure 安全中心的威胁智能，可以拒绝与已知的恶意 IP 地址的通信。  在启用了威胁情报的每个虚拟网络段上配置 Azure 防火墙，并将配置为针对恶意网络流量进行 **警报和拒绝** 。

可以使用 Azure 安全中心的实时网络访问权限，在有限的时间段内将 Windows 虚拟机的公开权限限制为已批准的 IP 地址。  同时，使用 Azure 安全中心自适应网络强化建议进行 NSG 配置，以根据实际流量和威胁智能限制端口和源 Ip。

- [如何配置 DDoS 防护](../ddos-protection/manage-ddos-protection.md)

- [如何部署 Azure 防火墙](../firewall/tutorial-firewall-deploy-portal.md)

- [了解 Azure 安全中心集成的威胁情报](../security-center/azure-defender.md)

- [了解 Azure 安全中心自适应网络强化](../security-center/security-center-adaptive-network-hardening.md)

- [了解 Azure 安全中心实时网络访问控制](../security-center/security-center-just-in-time.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="15-record-network-packets"></a>1.5：记录网络数据包

**指导**：除了支持混合 Runbook 辅助角色之外，Azure 自动化目前不具备专用网络的虚拟网络集成。如果你使用的是无混合辅助角色的现成服务，则此控制不适用。

如果你使用的是由 Azure 虚拟机提供支持的混合 Runbook 辅助角色，则可以将 NSG 流日志记录到存储帐户中，以便为充当 Runbook 辅助角色的 Azure 虚拟机生成流记录。 调查异常活动时，可以启用网络观察程序数据包捕获，以便可以检查网络流量中是否存在异常活动和意外活动。

- [如何启用 NSG 流日志](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [如何启用网络观察程序](../network-watcher/network-watcher-create.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6：部署基于网络的入侵检测/入侵防护系统 (IDS/IPS)

**指导**：除了支持混合 Runbook 辅助角色之外，Azure 自动化目前不具备专用网络的虚拟网络集成。 如果你使用的是无混合 Runbook 辅助角色的现成服务，则此控制不适用。

如果你使用在 Azure 虚拟机上承载的混合 Runbook 辅助角色，则可以将网络观察程序提供的数据包捕获信息与开源 IDS 工具组合使用，以针对大范围的威胁为这些辅助角色计算机执行网络入侵检测。 此外，还可以根据情况将 Azure 防火墙部署到虚拟网络段，启用威胁情报并将其配置为针对恶意网络流量执行“发出警报并拒绝”操作。

- [使用网络观察程序和开源工具执行网络入侵检测](../network-watcher/network-watcher-intrusion-detection-open-source-tools.md)

- [如何部署 Azure 防火墙](../firewall/tutorial-firewall-deploy-portal.md)

- [如何配置 Azure 防火墙警报](../firewall/threat-intel.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8：最大程度地降低网络安全规则的复杂性和管理开销

**指导**：使用虚拟网络服务标记在 Azure 中配置的网络安全组或 Azure 防火墙上定义网络访问控制，这需要访问自动化资源。 创建安全规则时，可以使用服务标记代替特定的 IP 地址。 在规则的相应源或目标字段中指定服务标记名称（例如 GuestAndHybridManagement），你可以允许或拒绝相应服务的流量。 Microsoft 会管理服务标记包含的地址前缀，并会在地址发生更改时自动更新服务标记。

- [了解并使用服务标记](../virtual-network/service-tags-overview.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9：维护网络设备的标准安全配置

**指南**：定义和实现 azure 自动化与 azure 策略一起使用的网络资源的标准安全配置。

还可以使用 Azure 蓝图，通过在单个蓝图定义中打包关键环境项目（例如 Azure 资源管理器模板、Azure RBAC 控制措施和策略），来简化大规模的 Azure 部署。 可将蓝图应用到新的订阅，并通过版本控制来微调控制措施和管理。

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [用于网络的 Azure Policy 示例](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network)

- [如何创建 Azure 蓝图](../governance/blueprints/create-blueprint-portal.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="110-document-traffic-configuration-rules"></a>1.10：阐述流量配置规则

**指导**：将标记用于 NSG 以及其他与网络安全和流量流有关的资源。 对于单个 NSG 规则，请使用“说明”字段针对允许流量传入/传出网络的任何规则指定业务需求和/或持续时间等。

使用标记相关的任何内置 Azure Policy 定义（例如“需要标记及其值”）来确保使用标记创建所有资源，并在有现有资源不带标记时发出通知。

可以使用 Azure PowerShell 或 Azure CLI 根据资源的标记查找资源或对其执行操作。

- [如何创建和使用标记](../azure-resource-manager/management/tag-resources.md)

- [如何创建虚拟网络](../virtual-network/quick-create-portal.md)

- [如何创建采用安全配置的 NSG](../virtual-network/tutorial-filter-network-traffic.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11：使用自动化工具来监视网络资源配置和检测更改

**指导**：使用 Azure 活动日志监视资源配置，并检测网络资源的更改。 在 Azure Monitor 中创建当关键资源发生更改时触发的警报。

- [如何查看和检索 Azure 活动日志事件](/azure/azure-monitor/platform/activity-log-view)

- [如何在 Azure Monitor 中创建警报](/azure/azure-monitor/platform/activity-log#view-the-activity-log)

**责任**：客户

**Azure 安全中心监视**：无

## <a name="logging-and-monitoring"></a>日志记录和监视

[有关详细信息，请参阅 *Azure 安全基线：* 日志记录和监视](../security/benchmarks/security-control-logging-monitoring.md)。

### <a name="22-configure-central-security-log-management"></a>2.2：配置中心安全日志管理

**指导**：将日志数据转发到 Azure Monitor 日志以聚合由 Azure 自动化资源生成的安全数据。 在 Azure Monitor 中，使用日志查询来搜索和执行分析，并使用 Azure 存储帐户进行长期/存档存储。 Azure 自动化可以将 runbook 作业状态、作业流、自动化状态配置数据、更新管理和更改跟踪或清单日志发送到 Log Analytics 工作区。 此信息可从 Azure 门户、Azure PowerShell 和 Azure Monitor 日志 API 中查看，因此你可以进行简单的调查。

或者，可以启用将数据加入 Azure Sentinel 或第三方 SIEM 的功能。 

- [如何加入 Azure Sentinel](../sentinel/quickstart-onboard.md)

- [如何使用 Azure Monitor 收集平台日志和指标](/azure/azure-monitor/platform/diagnostic-settings)

- [如何开始使用 Azure Monitor 和第三方 SIEM 集成](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

- [将 Azure 自动化作业数据转发到 Azure Monitor 日志](automation-manage-send-joblogs-log-analytics.md)

- [将 DSC 与 Azure Monitor 日志集成](automation-dsc-diagnostics.md)

- [链接的 Log Analytics 工作区支持的区域](how-to/region-mappings.md)

- [查询更新管理日志](update-management/query-logs.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：为 Azure 资源启用审核日志记录

**指导**：使 Azure Monitor 可以访问审核和活动日志，其中包括事件源、日期、用户、时间戳、源地址、目标地址和其他有用元素。 

- [如何使用 Azure Monitor 收集平台日志和指标](/azure/azure-monitor/platform/diagnostic-settings) 

- [查看和检索 Azure 活动日志事件](/azure/azure-monitor/platform/activity-log#view-the-activity-log)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="25-configure-security-log-storage-retention"></a>2.5：配置安全日志存储保留期

**指导**：在 Azure Monitor 中，根据组织的合规性规章设置 Log Analytics 工作区保留期。 使用 Azure 存储帐户进行长期/存档存储。

- [更改 Log Analytics 中的数据保留期](/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

- [自动化帐户的数据保留详细信息](https://docs.microsoft.com/azure/automation/automation-managing-data#data-retention)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="26-monitor-and-review-logs"></a>2.6：监视和查看日志

**指导**：分析和监视日志中的异常行为，并定期查看结果。 使用 Azure Monitor 日志查询来查看日志并对日志数据执行查询。

或者，可以启用将数据加入 Azure Sentinel 或第三方 SIEM 的功能。 

- [如何加入 Azure Sentinel](../sentinel/quickstart-onboard.md)

- [了解 Azure Monitor 中的日志查询](/azure/azure-monitor/log-query/log-analytics-tutorial)

- [如何在 Azure Monitor 中执行自定义查询](/azure/azure-monitor/log-query/get-started-queries)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7：针对异常活动启用警报

**指导**：将 Azure 安全中心与 Azure Monitor 配合使用来监视安全日志和事件中的异常活动并发出警报。

或者，可以启用数据并将其载入 Azure Sentinel。

- [如何加入 Azure Sentinel](../sentinel/quickstart-onboard.md)

- [如何在 Azure 安全中心管理警报](../security-center/security-center-managing-and-responding-alerts.md)

- [如何针对 Azure Monitor 日志数据发出警报](/azure/azure-monitor/learn/tutorial-response)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="29-enable-dns-query-logging"></a>2.9：启用 DNS 查询日志记录

**指导**：根据组织的需求，从 Azure 市场实现 DNS 日志记录解决方案的第三方解决方案。

**责任**：客户

**Azure 安全中心监视**：无

## <a name="identity-and-access-control"></a>标识和访问控制

[有关详细信息，请参阅 *Azure 安全基线：* 标识和访问控制](../security/benchmarks/security-control-identity-access-control.md)。

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1：维护管理帐户的清单

**指南**：使用 Azure Active Directory (Azure AD) 可显式分配并可查询的内置管理员角色。 使用 Azure AD PowerShell 模块执行即席查询，以发现属于管理组的成员的帐户。 每次将自动化帐户运行方式帐户用于 runbook 时，请确保还会在你的清单中跟踪这些服务主体，因为它们常常具有提升的权限。 删除任何未使用的运行方式帐户，以最大程度地降低暴露的受攻击面。

- [如何使用 PowerShell 获取 Azure AD 中的目录角色](/powershell/module/azuread/get-azureaddirectoryrole)

- [如何使用 PowerShell 获取 Azure AD 中目录角色的成员](/powershell/module/azuread/get-azureaddirectoryrolemember)

- [删除运行方式帐户或经典运行方式帐户](delete-run-as-account.md)

- [管理 Azure 自动化运行方式帐户](manage-runas-account.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="32-change-default-passwords-where-applicable"></a>3.2：在适用的情况下更改默认密码

**指导**：Azure 自动化帐户没有默认密码的概念。  客户负责第三方应用程序和市场服务，该服务可以使用在服务或其混合 Runbook 辅助角色上运行的默认密码。

**责任**：客户

**Azure 安全中心监视**：无

### <a name="33-use-dedicated-administrative-accounts"></a>3.3：使用专用管理帐户

**指南**：围绕专用管理帐户的使用创建标准操作程序。 使用 Azure 安全中心标识和访问管理来监视管理帐户的数量。 每次将自动化帐户运行方式帐户用于 runbook 时，请确保还会在你的清单中跟踪这些服务主体，因为它们常常具有提升的权限。 将这些标识的权限范围限定为它们让你的 runbook 成功执行其自动化过程所需的最低特权权限。 删除任何未使用的运行方式帐户，以最大程度地降低暴露的受攻击面。

你还可以通过使用 Azure Active Directory (Azure AD) Privileged Identity Management Microsoft 服务的特权角色和 Azure 资源管理器来启用实时/刚好足够的访问权限。

- [详细了解 Privileged Identity Management](/azure/active-directory/privileged-identity-management/)

- [删除运行方式帐户或经典运行方式帐户](delete-run-as-account.md)

- [管理 Azure 自动化运行方式帐户](manage-runas-account.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4：使用 Azure Active Directory 单一登录 (SSO)

**指南**：尽可能使用 SSO 与 Azure Active Directory (Azure AD) ，而不是为每个服务配置单独的独立凭据。 请使用 Azure 安全中心标识和访问管理建议。

- [单一登录到 Azure AD 中的应用程序](../active-directory/manage-apps/what-is-single-sign-on.md)

- [如何在 Azure 安全中心监视标识和访问](../security-center/security-center-identity-access.md)

- [使用 Azure AD 向 Azure 进行身份验证](automation-use-azure-ad.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5：对所有基于 Azure Active Directory 的访问使用多重身份验证

**指南**：启用 Azure Active Directory (Azure AD) 多重身份验证，并遵循 Azure 安全中心的标识和访问管理建议。

- [如何在 Azure 中启用多重身份验证](../active-directory/authentication/howto-mfa-getstarted.md)

- [如何在 Azure 安全中心监视标识和访问](../security-center/security-center-identity-access.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6：对所有管理任务使用专用计算机（特权访问工作站）

**指南**：使用 paw，其中的多重身份验证配置为登录并配置生产环境中的 Azure 自动化帐户资源。 

- [了解特权访问工作站](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [如何在 Azure 中启用多重身份验证](../active-directory/authentication/howto-mfa-getstarted.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7：记录来自管理帐户的可疑活动并对其发出警报 

**指导**：使用 Azure Active Directory (Azure AD) 风险检测查看有关风险用户行为的警报和报告。 客户可以选择将 Azure 安全中心风险检测警报转发到 Azure Monitor 并使用操作组配置自定义警报/通知。

- [了解 Azure 安全中心风险检测 (可疑活动) ](../active-directory/identity-protection/overview-identity-protection.md)

- [如何将 Azure 活动日志集成到 Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [如何为自定义警报和通知配置操作组](/azure/azure-monitor/platform/action-groups)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8：仅从批准的位置管理 Azure 资源 

**指导**：建议使用条件访问命名位置，仅允许从 IP 地址范围或国家/地区的特定逻辑分组进行访问。 

- [如何在 Azure 中配置命名位置](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="39-use-azure-active-directory"></a>3.9：使用 Azure Active Directory

**指导**：使用 Azure Active Directory (Azure AD) 作为中心身份验证和授权系统。 Azure AD 通过对静态数据和传输中数据使用强加密来保护数据。 Azure AD 还会对用户凭据进行加盐、哈希处理和安全存储操作。 如果你使用混合 Runbook 辅助角色，则可以利用托管标识（而非运行方式帐户）来实现更无缝的安全权限。

- [如何创建和配置 Azure AD 实例](../active-directory-domain-services/tutorial-create-instance.md)

- [结合托管标识使用 Runbook 身份验证](https://docs.microsoft.com/azure/automation/automation-hrw-run-runbooks#runbook-auth-managed-identities)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10：定期审查和协调用户访问

**指导**：Azure Active Directory (Azure AD) 提供日志来帮助发现过时的帐户。 此外，使用 Azure 标识访问评审还可有效管理组成员身份、对企业应用程序的访问权限以及角色分配。 可以定期评审用户的访问权限，确保只有适当的用户才持续拥有访问权限。 每次将自动化帐户运行方式帐户用于 runbook 时，请确保还会在你的清单中跟踪这些服务主体，因为它们常常具有提升的权限。 删除任何未使用的运行方式帐户，以最大程度地降低暴露的受攻击面。

- [了解 Azure AD 报告](/azure/active-directory/reports-monitoring/)

- [如何使用 Azure 标识访问评审](../active-directory/governance/access-reviews-overview.md)

- [删除运行方式帐户或经典运行方式帐户](delete-run-as-account.md)

- [管理 Azure 自动化运行方式帐户](manage-runas-account.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11：监视尝试访问已停用凭据的行为

**指南**：你有权访问 Azure Active Directory (Azure AD) 登录活动、审核和风险事件日志源，这允许你与任何 SIEM/监视工具集成。

可以通过创建 Azure AD 用户帐户的诊断设置并将审核日志和登录日志发送到 Log Analytics 工作区来简化此过程。 你可以在 Log Analytics 工作区中配置所需的警报。

- [如何将 Azure 活动日志集成到 Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12：针对帐户登录行为偏差发出警报

**指南**：使用 Azure Active Directory (Azure AD) 的风险和标识保护功能，将自动响应配置为检测到与网络资源的用户标识相关的可疑操作。 还可以将数据引入 Azure Sentinel 中以便进一步调查。

- [如何查看 Azure AD 风险登录](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [如何配置和启用标识保护风险策略](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [如何加入 Azure Sentinel](../sentinel/quickstart-onboard.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13：在支持场合下为 Microsoft 提供对相关客户数据的访问权限

**指南**：对于 Azure Automation 帐户，Microsoft 支持人员可以在开放支持案例中访问平台资源元数据，而无需使用其他工具。

但是，当使用由 Azure 虚拟机支持的混合 Runbook 辅助角色，而第三方需要访问客户数据时 (如) ，请使用 Azure 虚拟机的客户密码箱 (预览) 来查看和批准或拒绝客户数据访问请求。

- [了解客户密码箱](../security/fundamentals/customer-lockbox-overview.md)

**责任**：客户

**Azure 安全中心监视**：无

## <a name="data-protection"></a>数据保护

[有关详细信息，请参阅 *Azure 安全基线：* 数据保护](../security/benchmarks/security-control-data-protection.md)。

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1：维护敏感信息的清单

**指导**：使用标记有助于跟踪存储或处理敏感信息的 Azure 自动化资源。 

- [如何创建和使用标记](../azure-resource-manager/management/tag-resources.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2：隔离存储或处理敏感信息的系统

**指导**：为开发、测试和生产实施单独的订阅和/或管理组。 使用独立的自动化帐户资源来隔离环境。 混合 Runbook 辅助角色之类的资源应当按虚拟网络/子网进行分隔，相应地进行标记，并在网络安全组 (NSG) 或 Azure 防火墙中进行保护。 对于存储或处理敏感数据的虚拟机，请实施相应的策略和过程，以便在不使用的时候将其关闭。

- [如何创建其他 Azure 订阅](../cost-management-billing/manage/create-subscription.md)

- [如何创建管理组](../governance/management-groups/create-management-group-portal.md)

- [如何创建和使用标记](../azure-resource-manager/management/tag-resources.md)

- [如何创建虚拟网络](../virtual-network/quick-create-portal.md)

- [如何创建采用安全配置的 NSG](../virtual-network/tutorial-filter-network-traffic.md)

- [如何部署 Azure 防火墙](../firewall/tutorial-firewall-deploy-portal.md)

- [如何通过 Azure 防火墙配置“警报”或“发出警报并拒绝”](../firewall/threat-intel.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3：监视和阻止未经授权的敏感信息传输

**指导**：使用混合 Runbook 辅助角色功能时，请利用 Azure 市场中有关网络外围的第三方解决方案，该解决方案可监视并阻止敏感信息的未授权传输，同时提醒信息安全专业人员。

对于 Microsoft 管理的基础平台，Microsoft 会将所有客户内容视为敏感数据，并全方位防范客户数据丢失和泄露。 为了确保 Azure 中的客户数据保持安全，Microsoft 已实施并维护一套可靠的数据保护控制机制和功能。

- [了解 Azure 中的客户数据保护](../security/fundamentals/protection-customer-data.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4：加密传输中的所有敏感信息

**指导**：加密传输中的所有敏感信息。 确保连接到 Azure 虚拟网络中的 Azure 资源的任何客户端能够协商 TLS 1.2 或更高版本。 Azure 自动化通过 webhook、DSC 节点、混合 runbook 辅助角色) 完全支持并强制实施传输层 (TLS) 1.2 以及所有外部 HTPS 终结 (点的所有客户端调用或更高版本。

请按照 Azure 安全中心的建议，了解静态加密和传输中加密（如果适用）。

- [了解 Azure 传输中的加密](https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit)

- [Azure 自动化 TLS 1.2 强制实施](../active-directory/hybrid/reference-connect-tls-enforcement.md)

**责任**：共享

**Azure 安全中心监视**：无

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5：使用有效的发现工具识别敏感数据

**指导**：使用第三方主动发现工具来确定组织的技术系统（包括现场或远程服务提供商处的技术系统）存储、处理或传输的所有敏感信息，并更新组织的敏感信息清单。

**责任**：客户

**Azure 安全中心监视**：无

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6：使用基于角色的访问控制来控制对资源的访问

**指导**：使用 Azure 基于角色的访问控制 (Azure RBAC) 通过内置角色定义来控制对 Azure 自动化资源的访问，并遵循最小权限访问模型或“权限刚好足够”访问模型为访问自动化资源的用户分配访问权限。 使用混合 Runbook 辅助角色时，请利用这些虚拟机的托管标识以避免使用服务主体。在同时使用多租户或混合 Runbook 辅助角色时，请确保对 Runbook 辅助角色的标识应用适当范围的 Azure RBAC 权限。

- [如何配置 Azure RBAC](../role-based-access-control/role-assignments-portal.md)

- [混合 Runbook 辅助角色的 Runbook 权限](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker#runbook-permissions-for-a-hybrid-runbook-worker)

- [管理角色权限和安全性](automation-role-based-access-control.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7：使用基于主机的数据丢失防护来强制实施访问控制

**指导**：Azure 自动化目前没有公开基础多租户 runbook 辅助角色的虚拟机，这由平台进行处理。 如果你使用的是无混合 Runbook 辅助角色的现成服务，则此控制不适用。

如果你使用由 Azure 虚拟机提供支持的混合 Runbook 辅助角色，则需要使用第三方基于主机的数据丢失防护解决方案来对托管的混合 Runbook 辅助角色虚拟机强制实施访问控制。

**责任**：客户

**Azure 安全中心监视**：无

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8：静态加密敏感信息

**指导**：为 Azure 自动化使用客户管理的密钥。 Azure 自动化支持使用客户管理的密钥来加密所有使用的安全资产，例如凭据、证书、连接和加密的变量。 将加密的变量用于 runbook 来满足所有持久变量查找需求，以防止意外暴露。

使用混合 Runbook 辅助角色时，将使用服务器端加密或 Azure 磁盘加密 (ADE) 对虚拟机上的虚拟磁盘进行静态加密。 Azure 磁盘加密利用 Windows 的 BitLocker 功能，通过来宾 VM 中的客户管理的密钥来加密托管磁盘。 使用客户托管密钥的服务器端加密改进了 ADE，它通过加密存储服务中的数据使你可以为 VM 使用任何 OS 类型和映像。

- [Azure 托管磁盘的服务器端加密](../virtual-machines/disk-encryption.md)

- [适用于 Windows VM 的 Azure 磁盘加密](../virtual-machines/windows/disk-encryption-overview.md)

- [为自动化帐户使用客户管理的密钥](https://docs.microsoft.com/azure/automation/automation-secure-asset-encryption#use-of-customer-managed-keys-for-an-automation-account)

- [Azure 自动化中的托管变量](shared-resources/variables.md)

**责任**：客户

**Azure 安全中心监视**： [azure 安全基准](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) 是安全中心的默认策略计划，是 [安全中心建议](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md)的基础。 安全中心会自动启用与此控件相关的 Azure 策略定义。 与此控件相关的警报可能需要 [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) 计划相关服务。

**Azure 策略内置定义-Microsoft Automation**：

[!INCLUDE [Resource Policy for Microsoft.Automation 4.8](../../includes/policy/standards/asb/rp-controls/microsoft.automation-4-8.md)]

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9：记录对关键 Azure 资源的更改并对此类更改发出警报

**指导**：当关键 Azure 资源（例如网络组件、Azure 自动化帐户和 runbook）发生更改时，请将 Azure Monitor 与 Azure 活动日志结合使用来创建警报。 

- [网络安全组的诊断日志记录](https://docs.microsoft.com/azure/private-link/private-link-overview#logging-and-monitoring)

- [如何针对 Azure 活动日志事件创建警报](/azure/azure-monitor/platform/alerts-activity-log)

**责任**：客户

**Azure 安全中心监视**：无

## <a name="vulnerability-management"></a>漏洞管理

[有关详细信息，请参阅 *Azure 安全基线：* 漏洞管理。](../security/benchmarks/security-control-vulnerability-management.md)

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1：运行自动漏洞扫描工具

**指导**：遵循 Azure 安全中心内关于在 Azure 资源上执行漏洞评估的建议。

- [Azure 安全中心的安全建议](../security-center/security-center-recommendations.md)

- [安全中心建议参考](../security-center/recommendations-reference.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4：比较连续进行的漏洞扫描

**指导**：以一致的间隔导出扫描结果，并比较结果以验证漏洞是否已修复。 使用 Azure 安全中心建议的漏洞管理建议时，客户可以转到所选解决方案的门户查看历史扫描数据。

**责任**：客户

**Azure 安全中心监视**：无

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5：使用风险评级过程来确定已发现漏洞的修正措施的优先级

**指导**：使用 Azure 安全中心提供的默认风险等级（安全分数）来帮助确定发现的漏洞的修正优先级。

- [了解 Azure 安全中心安全功能分数](../security-center/secure-score-security-controls.md)

**责任**：客户

**Azure 安全中心监视**：无

## <a name="inventory-and-asset-management"></a>清单和资产管理

[有关详细信息，请参阅 *Azure 安全基线：* 清单和资产管理](../security/benchmarks/security-control-inventory-asset-management.md)。

### <a name="61-use-automated-asset-discovery-solution"></a>6.1：使用自动化资产发现解决方案

**指导**：使用 Azure Resource Graph 查询并发现订阅中的所有 Azure 自动化资源。 确保你在租户中拥有适当的（读取）权限，并且可以枚举所有 Azure 订阅，以及订阅中的资源。

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

**指导**：在适用的情况下，请使用标记、管理组和单独的订阅来组织和跟踪 Azure 自动化资源。 定期核对清单，确保及时地从订阅中删除未经授权的资源。 删除任何未使用的运行方式帐户，以最大程度地降低暴露的受攻击面。

- [如何创建其他 Azure 订阅](../cost-management-billing/manage/create-subscription.md)

- [如何创建管理组](../governance/management-groups/create-management-group-portal.md)

- [如何创建和使用标记](../azure-resource-manager/management/tag-resources.md)

- [删除运行方式帐户或经典运行方式帐户](delete-run-as-account.md)

- [管理 Azure 自动化运行方式帐户](manage-runas-account.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4：定义并维护已批准的 Azure 资源的清单

**指导**：你将需要根据组织需求，创建已获批 Azure 资源以及已获批用于计算资源的软件的清单。

**责任**：客户

**Azure 安全中心监视**：无

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5：监视未批准的 Azure 资源

**指导**：在 Azure Policy 中使用以下内置策略定义，对可以在客户订阅中创建的资源类型施加限制： 

- 不允许的资源类型 
- 允许的资源类型 

此外，请使用 Azure Resource Graph 来查询/发现订阅中的资源。 这可以帮助实现基于高安全性的环境，如具有存储帐户的环境。 

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md) 

- [如何使用 Azure Resource Graph 创建查询](../governance/resource-graph/first-query-portal.md)

- [适用于 Azure 自动化的 Azure Policy 内置项示例](policy-reference.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7：删除未批准的 Azure 资源和软件应用程序

**指导**：根据客户的公司准则，客户可以阻止通过 Azure Policy 来创建或使用资源的操作。 你可以实施自己的流程来删除未经授权的资源。 在 Azure 自动化产品/服务中，可以安装、删除和管理允许 runbook 通过门户或 cmdlet 访问的 PowerShell 或 Python 模块。 应当为 runbook 删除或更新未批准的或旧的模块。

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [在 Azure 自动化中管理模块](shared-resources/modules.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="69-use-only-approved-azure-services"></a>6.9：仅使用已批准的 Azure 服务

**指导**：在 Azure Policy 中使用以下内置策略定义，对可以在客户订阅中创建的资源类型施加限制：
- 不允许的资源类型
- 允许的资源类型

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [如何使用 Azure Policy 拒绝特定的资源类型](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11：限制用户与 Azure 资源管理器进行交互的能力

**指导**：通过将“Microsoft Azure 管理”应用配置为阻止来自不安全的或未经批准的位置或设备的访问，使用 Azure 条件访问策略来限制用户与 Azure 资源管理器交互的功能。 

- [如何配置条件访问以阻止访问 Azure 资源管理器](../role-based-access-control/conditional-access-azure-management.md)

**责任**：客户

**Azure 安全中心监视**：无

## <a name="secure-configuration"></a>安全配置

[有关详细信息，请参阅 *Azure 安全基线：* 安全配置](../security/benchmarks/security-control-secure-configuration.md)。

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1：为所有 Azure 资源建立安全配置

**指导**：使用 Azure Policy 别名创建自定义策略，以审核或强制实施 Azure 自动化和相关资源的配置。 你还可以使用内置的 Azure Policy 定义。

此外，Azure 资源管理器能够以 JavaScript 对象表示法 (JSON) 导出模板，应该对其进行检查，以确保配置满足/超过组织的安全要求。

还可以使用来自 Azure 安全中心的建议作为 Azure 资源的安全配置基线。

- [如何查看可用的 Azure Policy 别名](/powershell/module/az.resources/get-azpolicyalias)

- [教程：创建和管理策略以强制实施符合性](../governance/policy/tutorials/create-and-manage.md)

- [适用于 Azure 自动化的 Azure Policy 内置项示例](policy-reference.md)

- [在 Azure 门户中将单资源和多资源导出到模板](../azure-resource-manager/templates/export-template-portal.md)

- [安全建议 - 参考指南](../security-center/recommendations-reference.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3：维护安全的 Azure 资源配置

**指导**：使用 Azure 资源管理器模板和 Azure Policy 安全地配置与 Azure 自动化关联的 Azure 资源。 Azure 资源管理器模板是用于部署 Azure 资源的基于 JSON 的文件，任何自定义模板都需要在代码存储库中安全地进行存储和维护。 使用源代码管理集成功能，可以通过源代码管理存储库中的脚本使自动化帐户中的 Runbook 保持最新。 使用 Azure Policy“[拒绝]”和“[不存在则部署]”对不同的 Azure 资源强制实施安全设置。

- [使用源代码管理集成](source-control-integration.md)

- [有关创建 Azure 资源管理器模板的信息](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md) 

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md) 

- [了解 Azure Policy 效果](../governance/policy/concepts/effects.md)

- [使用 Azure 资源管理器模板部署自动化帐户](/azure/automation/quickstart-create-account-template#deploy-the-template)

- [适用于 Azure 自动化的 Azure Policy 内置项示例](policy-reference.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5：安全存储 Azure 资源的配置

**指导**：使用 Azure DevOps 安全地存储和管理代码，如自定义 Azure 策略、Azure 资源管理器模板和 Desired State Configuration 脚本。 若要访问在 Azure DevOps 中管理的资源，可以向特定用户、内置安全组或 Azure Active Directory (Azure AD)（如果与 Azure DevOps 集成）中定义的组或 Active Directory（如果与 TFS 集成）授予或拒绝授予权限。 使用源代码管理集成功能，可以通过源代码管理存储库中的脚本使自动化帐户中的 Runbook 保持最新。

- [如何在 Azure DevOps 中存储代码](/azure/devops/repos/git/gitworkflow)

- [关于 Azure DevOps 中的权限和组](/azure/devops/organizations/security/about-permissions)

- [使用源代码管理集成](source-control-integration.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7：部署 Azure 资源的配置管理工具

**指导**：使用 Azure Policy 为 Azure 资源定义和实施标准安全配置。 使用 Azure Policy 别名创建自定义策略，审核或强制实施 Azure 资源的网络配置。 还可以使用与特定资源相关的内置策略定义。 

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [如何使用别名](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases)

- [适用于 Azure 自动化的 Azure Policy 内置项示例](policy-reference.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9：为 Azure 资源实施自动配置监视

**指导**：使用 Azure Policy 对 Azure 资源配置发出警报和进行审核。可以使用策略来检测未配置专用终结点的特定资源。

使用混合 Runbook 辅助角色功能时，利用 Azure 安全中心为 Azure 虚拟机执行基线扫描。  自动配置的其他方法包括： Azure 自动化状态配置。

- [如何在 Azure 安全中心修正建议](../security-center/security-center-remediate-recommendations.md)

- [Azure Automation State Configuration 入门](automation-dsc-getting-started.md)

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [适用于 Azure 自动化的 Azure Policy 内置项示例](policy-reference.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13：消除意外的凭据透露

**指南**：实施凭据扫描程序来识别代码中的凭据。 凭据扫描程序还会建议将发现的凭据转移到更安全的位置，例如 Azure Key Vault。 

- [如何设置凭据扫描程序](https://secdevtools.azurewebsites.net/helpcredscan.html)

**责任**：客户

**Azure 安全中心监视**：无

## <a name="data-recovery"></a>数据恢复

[有关详细信息，请参阅 *Azure 安全基线：* 数据恢复](../security/benchmarks/security-control-data-recovery.md)。

### <a name="91-ensure-regular-automated-back-ups"></a>9.1：确保定期执行自动备份

**指导**：使用 Azure 资源管理器部署 Azure 自动化帐户和相关资源。 Azure 资源管理器提供导出模板的功能，这些模板可用作还原 Azure 自动化帐户和相关资源的备份。 使用 Azure 自动化定期调用 Azure 资源管理器模板导出 API。

使用源代码管理集成功能，可以通过源代码管理存储库中的脚本使自动化帐户中的 Runbook 保持最新。

- [Azure 资源管理器概述](../azure-resource-manager/management/overview.md)

- [适用于 Azure 自动化资源的 Azure 资源管理器模板参考](/azure/templates/microsoft.automation/allversions)

- [使用 Azure 资源管理器模板创建自动化帐户](quickstart-create-automation-account-template.md)

- [在 Azure 门户中将单资源和多资源导出到模板](../azure-resource-manager/templates/export-template-portal.md)

- [资源组 - 导出模板](/rest/api/resources/resourcegroups/exporttemplate)

- [Azure 自动化简介](automation-intro.md)

- [如何在 Azure 中备份密钥保管库密钥](/powershell/module/az.keyvault/backup-azkeyvaultkey)

- [为自动化帐户使用客户管理的密钥](https://docs.microsoft.com/azure/automation/automation-secure-asset-encryption#use-of-customer-managed-keys-for-an-automation-account)

- [使用源代码管理集成](source-control-integration.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2：执行完整系统备份，并备份客户管理的所有密钥

**指导**：使用 Azure 资源管理器部署 Azure 自动化帐户和相关资源。 Azure 资源管理器提供导出模板的功能，这些模板可用作还原 Azure 自动化帐户和相关资源的备份。 使用 Azure 自动化定期调用 Azure 资源管理器模板导出 API。 在 Azure Key Vault 中备份客户管理的密钥。 你可以使用 Azure 门户或 PowerShell 将 runbook 导出到脚本文件。

- [Azure 资源管理器概述](../azure-resource-manager/management/overview.md)

- [适用于 Azure 自动化资源的 Azure 资源管理器模板参考](/azure/templates/microsoft.automation/allversions)

- [使用 Azure 资源管理器模板创建自动化帐户](quickstart-create-automation-account-template.md)

- [在 Azure 门户中将单资源和多资源导出到模板](../azure-resource-manager/templates/export-template-portal.md)

- [资源组 - 导出模板](/rest/api/resources/resourcegroups/exporttemplate)

- [Azure 自动化简介](automation-intro.md)

- [如何在 Azure 中备份密钥保管库密钥](/powershell/module/az.keyvault/backup-azkeyvaultkey)

- [为自动化帐户使用客户管理的密钥](https://docs.microsoft.com/azure/automation/automation-secure-asset-encryption#use-of-customer-managed-keys-for-an-automation-account)

- [自动化帐户的 Azure 数据备份](https://docs.microsoft.com/azure/automation/automation-managing-data#data-backup)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3：验证所有备份，包括客户管理的密钥

**指导**：确保能够将 Azure 资源管理器模板定期部署到隔离订阅（如果需要）。 测试对备份的客户管理的密钥进行还原。

- [使用 ARM 模板和 Azure 门户部署资源](../azure-resource-manager/templates/deploy-portal.md)

- [如何在 Azure 中还原密钥保管库密钥](/powershell/module/az.keyvault/restore-azkeyvaultkey)

- [为自动化帐户使用客户管理的密钥](https://docs.microsoft.com/azure/automation/automation-secure-asset-encryption#use-of-customer-managed-keys-for-an-automation-account)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4：确保保护备份和客户管理的密钥

**指导**：使用 Azure DevOps 安全地存储和管理 Azure 资源管理器模板之类的代码。 若要保护在 Azure DevOps 中管理的资源，可以向特定用户、内置安全组或 Azure Active Directory (Azure AD)（如果与 Azure DevOps 集成）中定义的组或 Active Directory（如果与 TFS 集成）授予或拒绝授予权限。

使用源代码管理集成功能，可以通过源代码管理存储库中的脚本使自动化帐户中的 Runbook 保持最新。

- [如何在 Azure DevOps 中存储代码](/azure/devops/repos/git/gitworkflow)

- [关于 Azure DevOps 中的权限和组](/azure/devops/organizations/security/about-permissions)

- [使用源代码管理集成](source-control-integration.md)

**责任**：客户

**Azure 安全中心监视**：无

## <a name="incident-response"></a>事件响应

[有关详细信息，请参阅 *Azure 安全基线：* 事件响应](../security/benchmarks/security-control-incident-response.md)。

### <a name="101-create-an-incident-response-guide"></a>10.1：创建事件响应指导

**指南**：为组织制定事件响应指南。 确保在书面的事件响应计划中定义人员职责，以及事件处理/管理从检测到事件后审查的各个阶段。

- [关于建立自己的安全事件响应流程的指南](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft 安全响应中心事件分析](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [客户还可以利用 NIST 的“计算机安全事件处理指南”来制定他们自己的事件响应计划](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2：创建事件评分和优先级设定过程

**指南**：安全中心向每个警报分配一个严重性，帮助你优先处理应首先调查的警报。 严重性取决于安全中心对调查结果或用于发出警报的分析的确信程度，以及对导致警报的活动背后存在恶意意图的确信程度。 

此外，请明确标记订阅（例如 生产、非生产）并创建命名系统来对 Azure 资源进行明确标识和分类，特别是处理敏感数据的资源。  你的责任是根据发生事件的 Azure 资源和环境的关键性确定修正警报的优先级。

- [Azure 安全中心中的安全警报](../security-center/security-center-alerts-overview.md)

- [使用标记整理 Azure 资源](../azure-resource-manager/management/tag-resources.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="103-test-security-response-procedures"></a>10.3：测试安全响应过程

**指导**：定期执行演练来测试系统的事件响应功能，以帮助保护 Azure 资源。 识别弱点和差距，并根据需要修改计划。

- [NIST 发布 - IT 计划和功能的测试、训练和演练计划指南](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4：提供安全事件联系人详细信息，并针对安全事件配置警报通知

**指导**：如果 Microsoft 安全响应中心 (MSRC) 发现数据被某方非法访问或未经授权访问，Microsoft 会使用安全事件联系信息联系用户。 事后审查事件，确保问题得到解决。

- [如何设置 Azure 安全中心安全联系人](../security-center/security-center-provide-security-contact-details.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5：将安全警报整合到事件响应系统中

**指南**：使用连续导出功能导出 Azure 安全中心警报和建议，以帮助确定 Azure 资源的风险。 使用连续导出可以手动导出或者持续导出警报和建议。 可以使用 Azure 安全中心数据连接器将警报流式传输到 Azure Sentinel。

- [如何配置连续导出](../security-center/continuous-export.md)

- [如何将警报流式传输到 Azure Sentinel](../sentinel/connect-azure-security-center.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="106-automate-the-response-to-security-alerts"></a>10.6：自动响应安全警报

**指导**：使用 Azure 安全中心内的工作流自动化功能，通过“逻辑应用”针对安全警报和建议自动触发响应，以保护 Azure 资源。

- [如何配置工作流自动化和逻辑应用](../security-center/workflow-automation.md)

**责任**：客户

**Azure 安全中心监视**：无

## <a name="penetration-tests-and-red-team-exercises"></a>渗透测试和红队练习

[有关详细信息，请参阅 *Azure 安全基线：* 渗透测试和红队演练](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)。

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1：定期对 Azure 资源执行渗透测试，确保修正所有发现的关键安全问题 

**指导**：请遵循 Microsoft 互动规则，确保你的渗透测试不违反 Microsoft 政策。 使用 Microsoft 红队演练策略和执行，以及针对 Microsoft 托管云基础结构、服务和应用程序执行现场渗透测试。

- [参与的渗透测试规则](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Microsoft 云红色组合](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**责任**：共享

**Azure 安全中心监视**：无

## <a name="next-steps"></a>后续步骤

- 参阅 [Azure 安全基准 V2 概述](/azure/security/benchmarks/overview)
- 详细了解 [Azure 安全基线](/azure/security/benchmarks/security-baselines-overview)
