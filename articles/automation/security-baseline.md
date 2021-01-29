---
title: 适用于 Azure 自动化的 Azure 安全基线
description: 适用于自动化的 Azure 安全基线
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 01/07/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 79282e99894bccad4f3c265cdffe4bee2e465950
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2021
ms.locfileid: "99052747"
---
# <a name="azure-security-baseline-for-automation"></a>适用于自动化的 Azure 安全基线

适用于自动化的 Azure 安全基线包含可帮助你改善部署安全状况的建议。

此服务的基线摘自 [Azure 安全基准版本 1.0](../security/benchmarks/overview.md)，其中提供了有关如何根据我们的最佳做法指导保护 Azure 上的云解决方案的建议。

有关详细信息，请参阅 [Azure 安全基线概述](../security/benchmarks/security-baselines-overview.md)。

## <a name="network-security"></a>网络安全性

有关详细信息，请参阅[安全控制：网络安全](../security/benchmarks/security-control-network-security.md)。

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1：保护虚拟网络中的 Azure 资源

**指导**：Azure·自动化帐户尚不支持使用 Azure 专用链接来限制通过专用终结点访问服务的操作。 针对 Azure 中的资源进行身份验证和运行的 runbook 在 Azure 沙箱上运行，并利用共享的后端资源，Microsoft 负责彼此隔离;它们的网络是不受限制的，可以访问公共资源。 除了支持混合 Runbook 辅助角色之外，Azure 自动化目前不具备专用网络的虚拟网络集成。 如果你使用的是无混合 Runbook 辅助角色的现成服务，则此控制不适用。

若要进一步隔离 runbook，可以使用在 Azure 虚拟机上运行的混合 Runbook 辅助角色。 创建 Azure 虚拟机时，必须创建虚拟网络 (VNet) 或使用现有 VNet，并为 VM 配置子网。 确保所有部署的子网都应用了网络安全组，且具有特定于应用程序受信任端口和源的网络访问控制。 有关特定于服务的要求，请参阅该特定服务的安全建议。

或者，如果你有特定的要求，还可以使用 Azure 防火墙来满足该要求。

* [Azure 中的虚拟网络和虚拟机](../virtual-machines/network-overview.md)

* [如何创建虚拟网络](../virtual-network/quick-create-portal.md)

* [如何创建采用安全配置的 NSG](../virtual-network/tutorial-filter-network-traffic.md)

* [如何部署和配置 Azure 防火墙](../firewall/tutorial-firewall-deploy-portal.md)

* [Runbook 执行环境](./automation-runbook-execution.md#runbook-execution-environment)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2：监视和记录虚拟网络、子网和 NIC 的配置与流量

**指导**：除了支持混合 Runbook 辅助角色之外，Azure 自动化目前不具备专用网络的虚拟网络集成。 如果你使用的是无混合 Runbook 辅助角色的现成服务，则此控制不适用。

如果你使用的是由 Azure 虚拟机提供支持的混合 Runbook 辅助角色，请确保为包含这些辅助角色的子网启用网络安全组 (NSG)，并将流日志配置为将日志转发到存储帐户以进行流量审核。 你还可以将 NSG 流日志转发到 Log Analytics 工作区，并使用流量分析来提供有关 Azure 云中通信流的见解。 流量分析的优势包括能够可视化网络活动、识别热点、识别安全威胁、了解流量流模式，以及查明网络不当配置。

尽管 NSG 规则和用户定义的路由不适用于专用终结点，但针对出站连接的 NSG 流日志和监视信息仍受支持，并且可供使用。

* [如何启用 NSG 流日志](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [如何启用和使用流量分析](../network-watcher/traffic-analytics.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="13-protect-critical-web-applications"></a>1.3：保护关键 Web 应用程序

**指导**：不适用；此建议适用于 Azure 应用服务或计算资源上运行的 Web 应用程序。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4：拒绝与已知恶意的 IP 地址进行通信

**指导**：除了支持混合 Runbook 辅助角色之外，Azure 自动化目前不具备专用网络的虚拟网络集成。 如果你使用的是无混合 Runbook 辅助角色的现成服务，则此控制不适用。

如果你使用的是由 Azure 虚拟机支持的混合 Runbook 辅助角色，请在托管混合 Runbook 辅助角色的虚拟网络上启用分布式拒绝服务 (DDoS) 标准保护，以防范 DDoS 攻击。 使用 Azure 安全中心的威胁智能，可以拒绝与已知的恶意 IP 地址的通信。 在启用了威胁情报的每个虚拟网络段上配置 Azure 防火墙，并将配置为针对恶意网络流量进行 **警报和拒绝** 。

可以使用 Azure 安全中心的实时网络访问权限，在有限的时间段内将 Windows 虚拟机的公开权限限制为已批准的 IP 地址。 同时，使用 Azure 安全中心自适应网络强化建议进行 NSG 配置，以根据实际流量和威胁智能限制端口和源 Ip。

* [如何配置 DDoS 防护](../ddos-protection/manage-ddos-protection.md)

* [如何部署 Azure 防火墙](../firewall/tutorial-firewall-deploy-portal.md)

* [了解 Azure 安全中心集成的威胁情报](../security-center/azure-defender.md)

* [了解 Azure 安全中心自适应网络强化](../security-center/security-center-adaptive-network-hardening.md)

* [了解 Azure 安全中心实时网络访问控制](../security-center/security-center-just-in-time.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="15-record-network-packets"></a>1.5：记录网络数据包

**指导**：除了支持混合 Runbook 辅助角色之外，Azure 自动化目前不具备专用网络的虚拟网络集成。如果你使用的是无混合辅助角色的现成服务，则此控制不适用。

如果你使用的是由 Azure 虚拟机提供支持的混合 Runbook 辅助角色，则可以将 NSG 流日志记录到存储帐户中，以便为充当 Runbook 辅助角色的 Azure 虚拟机生成流记录。 调查异常活动时，可以启用网络观察程序数据包捕获，以便可以检查网络流量中是否存在异常活动和意外活动。

* [如何启用 NSG 流日志](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [如何启用网络观察程序](../network-watcher/network-watcher-create.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6：部署基于网络的入侵检测/入侵防护系统 (IDS/IPS)

**指导**：除了支持混合 Runbook 辅助角色之外，Azure 自动化目前不具备专用网络的虚拟网络集成。 如果你使用的是无混合 Runbook 辅助角色的现成服务，则此控制不适用。

如果你使用在 Azure 虚拟机上承载的混合 Runbook 辅助角色，则可以将网络观察程序提供的数据包捕获信息与开源 IDS 工具组合使用，以针对大范围的威胁为这些辅助角色计算机执行网络入侵检测。 此外，还可以根据情况将 Azure 防火墙部署到虚拟网络段，启用威胁情报并将其配置为针对恶意网络流量执行“发出警报并拒绝”操作。

* [使用网络观察程序和开源工具执行网络入侵检测](../network-watcher/network-watcher-intrusion-detection-open-source-tools.md)

* [如何部署 Azure 防火墙](../firewall/tutorial-firewall-deploy-portal.md)

* [如何配置 Azure 防火墙警报](../firewall/threat-intel.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="17-manage-traffic-to-web-applications"></a>1.7：管理发往 Web 应用程序的流量

**指导**：不适用；此建议适用于 Azure 应用服务或计算资源上运行的 Web 应用程序。

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8：最大程度地降低网络安全规则的复杂性和管理开销

**指导**：使用虚拟网络服务标记在 Azure 中配置的网络安全组或 Azure 防火墙上定义网络访问控制，这需要访问自动化资源。 创建安全规则时，可以使用服务标记代替特定的 IP 地址。 在规则的相应源或目标字段中指定服务标记名称（例如 GuestAndHybridManagement），你可以允许或拒绝相应服务的流量。 Microsoft 会管理服务标记包含的地址前缀，并会在地址发生更改时自动更新服务标记。

* [了解并使用服务标记](../virtual-network/service-tags-overview.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9：维护网络设备的标准安全配置

**指南**：定义和实现 azure 自动化与 azure 策略一起使用的网络资源的标准安全配置。

还可以使用 Azure 蓝图，通过在单个蓝图定义中打包关键环境项目（例如 Azure 资源管理器模板、Azure RBAC 控制措施和策略），来简化大规模的 Azure 部署。 可将蓝图应用到新的订阅，并通过版本控制来微调控制措施和管理。

* [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [用于网络的 Azure Policy 示例](../governance/policy/samples/built-in-policies.md#network)

* [如何创建 Azure 蓝图](../governance/blueprints/create-blueprint-portal.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="110-document-traffic-configuration-rules"></a>1.10：阐述流量配置规则

**指导**：将标记用于 NSG 以及其他与网络安全和流量流有关的资源。 对于单个 NSG 规则，请使用“说明”字段针对允许流量传入/传出网络的任何规则指定业务需求和/或持续时间等。

使用标记相关的任何内置 Azure Policy 定义（例如“需要标记及其值”）来确保使用标记创建所有资源，并在有现有资源不带标记时发出通知。

可以使用 Azure PowerShell 或 Azure CLI 根据资源的标记查找资源或对其执行操作。

* [如何创建和使用标记](../azure-resource-manager/management/tag-resources.md)

* [如何创建虚拟网络](../virtual-network/quick-create-portal.md)

* [如何创建采用安全配置的 NSG](../virtual-network/tutorial-filter-network-traffic.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11：使用自动化工具来监视网络资源配置和检测更改

**指导**：使用 Azure 活动日志监视资源配置，并检测网络资源的更改。 在 Azure Monitor 中创建当关键资源发生更改时触发的警报。

* [如何查看和检索 Azure 活动日志事件](../azure-monitor/platform/activity-log.md#view-the-activity-log)

* [如何在 Azure Monitor 中创建警报](../azure-monitor/platform/alerts-activity-log.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

## <a name="logging-and-monitoring"></a>日志记录和监视

有关详细信息，请参阅[安全控制：日志记录和监视](../security/benchmarks/security-control-logging-monitoring.md)。

### <a name="21-use-approved-time-synchronization-sources"></a>2.1：使用批准的时间同步源

**指导**：由 Microsoft 维护 Azure 资源的时间源。 但是，你可以选择管理在 Windows 虚拟机上运行的任何混合 Runbook 辅助角色的时间同步设置。

* [如何为 Azure 计算资源配置时间同步](../virtual-machines/windows/time-sync.md)

**Azure 安全中心监视**：不适用

**责任**：Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2：配置中心安全日志管理

**指导**：将日志数据转发到 Azure Monitor 日志以聚合由 Azure 自动化资源生成的安全数据。 在 Azure Monitor 中，使用日志查询来搜索和执行分析，并使用 Azure 存储帐户进行长期/存档存储。 Azure 自动化可以将 runbook 作业状态、作业流、自动化状态配置数据、更新管理和更改跟踪或清单日志发送到 Log Analytics 工作区。 此信息可从 Azure 门户、Azure PowerShell 和 Azure Monitor 日志 API 中查看，因此你可以进行简单的调查。

或者，可以启用将数据加入 Azure Sentinel 或第三方 SIEM 的功能。

* [如何加入 Azure Sentinel](../sentinel/quickstart-onboard.md)

* [如何使用 Azure Monitor 收集平台日志和指标](../azure-monitor/platform/diagnostic-settings.md)

* [如何开始使用 Azure Monitor 和第三方 SIEM 集成](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

* [将 Azure 自动化作业数据转发到 Azure Monitor 日志](./automation-manage-send-joblogs-log-analytics.md)

* [将 DSC 与 Azure Monitor 日志集成](./automation-dsc-diagnostics.md)

* [链接的 Log Analytics 工作区支持的区域](./how-to/region-mappings.md)

* [查询更新管理日志](./update-management/query-logs.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：为 Azure 资源启用审核日志记录

**指导**：使 Azure Monitor 可以访问审核和活动日志，其中包括事件源、日期、用户、时间戳、源地址、目标地址和其他有用元素。

* [如何使用 Azure Monitor 收集平台日志和指标](../azure-monitor/platform/diagnostic-settings.md)

* [查看和检索 Azure 活动日志事件](../azure-monitor/platform/activity-log.md#view-the-activity-log)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4：从操作系统收集安全日志

**指导**：将 Azure 自动化与多租户 runbook 辅助角色一起使用时，此控制不适用，将由平台处理底层虚拟机。

当使用混合 Runbook 辅助角色功能时，Azure 安全中心为 Windows 虚拟机提供安全事件日志监视。 如果你的组织想要保留安全事件日志数据，则可将其存储在某个数据集合层中，然后即可在 Log Analytics 中对其进行查询。 存在不同的层：“最小”、“常用”和“全部”，详见以下链接。

* [在 Azure 安全中心内配置数据收集层](../security-center/security-center-enable-data-collection.md#data-collection-tier)

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="25-configure-security-log-storage-retention"></a>2.5：配置安全日志存储保留期

**指导**：在 Azure Monitor 中，根据组织的合规性规章设置 Log Analytics 工作区保留期。 使用 Azure 存储帐户进行长期/存档存储。

* [更改 Log Analytics 中的数据保留期](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

* [自动化帐户的数据保留详细信息](./automation-managing-data.md#data-retention)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="26-monitor-and-review-logs"></a>2.6：监视和审查日志

**指导**：分析和监视日志中的异常行为，并定期查看结果。 使用 Azure Monitor 日志查询来查看日志并对日志数据执行查询。

或者，可以启用将数据加入 Azure Sentinel 或第三方 SIEM 的功能。

* [如何加入 Azure Sentinel](../sentinel/quickstart-onboard.md)

* [了解 Azure Monitor 中的日志查询](../azure-monitor/log-query/log-analytics-tutorial.md)

* [如何在 Azure Monitor 中执行自定义查询](../azure-monitor/log-query/get-started-queries.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7：针对异常活动启用警报

**指导**：将 Azure 安全中心与 Azure Monitor 配合使用来监视安全日志和事件中的异常活动并发出警报。

或者，可以启用数据并将其载入 Azure Sentinel。

* [如何加入 Azure Sentinel](../sentinel/quickstart-onboard.md)

* [如何在 Azure 安全中心管理警报](../security-center/security-center-managing-and-responding-alerts.md)

* [如何针对 Azure Monitor 日志数据发出警报](../azure-monitor/learn/tutorial-response.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="28-centralize-anti-malware-logging"></a>2.8：集中管理反恶意软件日志记录

**指导**：将 Azure 自动化与多租户 runbook 辅助角色一起使用时，此控制不适用，将由平台处理底层虚拟机。

但是，在使用混合 Runbook 辅助角色功能时，可以将 Microsoft Antimalware 用于 Azure 云服务和虚拟机。 将虚拟机配置为将事件记录到 Azure 存储帐户。 配置 Log Analytics 工作区以从存储帐户中引入事件，并视具体情况创建警报。 请遵循 Azure 安全中心中的建议：“计算和应用”。&amp;

* [如何为云服务和虚拟机配置 Microsoft Anti-malware](../security/fundamentals/antimalware.md)

* [如何为虚拟机启用来宾级别监视](../cost-management-billing/cloudyn/azure-vm-extended-metrics.md)

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="29-enable-dns-query-logging"></a>2.9：启用 DNS 查询日志记录

**指导**：根据组织的需求，从 Azure 市场实现 DNS 日志记录解决方案的第三方解决方案。

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="210-enable-command-line-audit-logging"></a>2.10：启用命令行审核日志记录

**指导**：将 Azure 自动化与多租户 runbook 辅助角色一起使用时，此控制不适用，将由平台处理底层虚拟机。

但是，当使用混合 Runbook 辅助角色功能时，Azure 安全中心为 Azure 虚拟机提供安全事件日志监视。 如果启用了自动预配，则安全中心会在所有受支持的 Azure VM 以及任何新建的 Azure VM 中预配 Log Analytics 代理。 你也可以手动安装代理。 该代理可启用进程创建事件 4688 和事件 4688 内的 CommandLine 字段。 VM 上创建的新进程由事件日志记录，由安全中心的检测服务监视。

* [Azure 安全中心中的数据收集](../security-center/security-center-enable-data-collection.md#data-collection-tier)

**Azure 安全中心监视**：不适用

**责任**：不适用

## <a name="identity-and-access-control"></a>标识和访问控制

有关详细信息，请参阅[安全控制：标识和访问控制](../security/benchmarks/security-control-identity-access-control.md)。

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1：维护管理帐户的清单

**指导**：使用可显式分配并可查询的 Azure Active Directory 内置管理员角色。 使用 Azure AD PowerShell 模块执行即席查询，以发现属于管理组的成员的帐户。 每次将自动化帐户运行方式帐户用于 runbook 时，请确保还会在你的清单中跟踪这些服务主体，因为它们常常具有提升的权限。 删除任何未使用的运行方式帐户，以最大程度地降低暴露的受攻击面。

* [如何使用 PowerShell 获取 Azure AD 中的目录角色](/powershell/module/azuread/get-azureaddirectoryrole)

* [如何使用 PowerShell 获取 Azure AD 中目录角色的成员](/powershell/module/azuread/get-azureaddirectoryrolemember)

* [删除运行方式帐户或经典运行方式帐户](./delete-run-as-account.md)

* [管理 Azure 自动化运行方式帐户](./manage-runas-account.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="32-change-default-passwords-where-applicable"></a>3.2：在适用的情况下更改默认密码

**指导**：Azure 自动化帐户没有默认密码的概念。 客户负责第三方应用程序和市场服务，该服务可以使用在服务或其混合 Runbook 辅助角色上运行的默认密码。

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="33-use-dedicated-administrative-accounts"></a>3.3：使用专用管理帐户

**指南**：围绕专用管理帐户的使用创建标准操作程序。 使用 Azure 安全中心标识和访问管理来监视管理帐户的数量。 每次将自动化帐户运行方式帐户用于 runbook 时，请确保还会在你的清单中跟踪这些服务主体，因为它们常常具有提升的权限。 将这些标识的权限范围限定为它们让你的 runbook 成功执行其自动化过程所需的最低特权权限。 删除任何未使用的运行方式帐户，以最大程度地降低暴露的受攻击面。

还可以通过使用 Microsoft 服务的 Azure AD Privileged Identity Management 特权角色和 Azure 资源管理器来启用实时/足够访问权限。

* [详细了解 Privileged Identity Management](../active-directory/privileged-identity-management/index.yml)

* [删除运行方式帐户或经典运行方式帐户](./delete-run-as-account.md)

* [管理 Azure 自动化运行方式帐户](./manage-runas-account.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4：将单一登录 (SSO) 与 Azure Active Directory 配合使用

**指南**：尽可能将 SSO 与 Azure Active Directory 结合使用，而不是为每个服务配置单独的单独凭据。 请使用 Azure 安全中心标识和访问管理建议。

* [单一登录到 Azure Active Directory 中的应用程序](../active-directory/manage-apps/what-is-single-sign-on.md)

* [如何在 Azure 安全中心监视标识和访问](../security-center/security-center-identity-access.md)

* [使用 Azure AD 向 Azure 进行身份验证](./automation-use-azure-ad.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5：对所有基于 Azure Active Directory 的访问使用多重身份验证

**指导**：启用 Azure AD 多重身份验证 (MFA)，并遵循 Azure 安全中心标识和访问管理建议。

* [如何在 Azure 中启用 MFA](../active-directory/authentication/howto-mfa-getstarted.md)

* [如何在 Azure 安全中心监视标识和访问](../security-center/security-center-identity-access.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="36-use-dedicated-machines-for-all-administrative-tasks"></a>3.6：为所有管理任务使用专用计算机

**指导**：使用配置了多重身份验证的 PAW 来登录并配置生产环境中的 Azure 自动化帐户资源。

* [了解特权访问工作站](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

* [如何在 Azure 中启用 MFA](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7：记录来自管理帐户的可疑活动并对其发出警报

**指南**：利用 Azure AD 风险检测来查看警报和报告有风险的用户行为。 客户可以选择将 Azure 安全中心风险检测警报转发到 Azure Monitor 并使用操作组配置自定义警报/通知。

* [了解 Azure 安全中心风险检测 (可疑活动) ](../active-directory/identity-protection/overview-identity-protection.md)

* [如何将 Azure 活动日志集成到 Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

* [如何为自定义警报和通知配置操作组](../azure-monitor/platform/action-groups.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8：仅从批准的位置管理 Azure 资源

**指导**：建议使用条件访问命名位置，仅允许从 IP 地址范围或国家/地区的特定逻辑分组进行访问。

* [如何在 Azure 中配置命名位置](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="39-use-azure-active-directory"></a>3.9：使用 Azure Active Directory

**指导**：请使用 Azure AD 作为中心身份验证和授权系统。 Azure AD 通过对静态数据和传输中数据使用强加密来保护数据。 Azure AD 还会对用户凭据进行加盐、哈希处理和安全存储操作。 如果你使用混合 Runbook 辅助角色，则可以利用托管标识（而非运行方式帐户）来实现更无缝的安全权限。

* [如何创建和配置 Azure AD 实例](../active-directory-domain-services/tutorial-create-instance.md)

* [结合托管标识使用 Runbook 身份验证](./automation-hrw-run-runbooks.md#runbook-auth-managed-identities)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10：定期审查和协调用户访问

**指南**：Azure AD 提供有助于发现陈旧帐户的日志。 此外，使用 Azure 标识访问评审还可有效管理组成员身份、对企业应用程序的访问权限以及角色分配。 可以定期评审用户的访问权限，确保只有适当的用户才持续拥有访问权限。 每次将自动化帐户运行方式帐户用于 runbook 时，请确保还会在你的清单中跟踪这些服务主体，因为它们常常具有提升的权限。 删除任何未使用的运行方式帐户，以最大程度地降低暴露的受攻击面。

* [了解 Azure AD 报告](../active-directory/reports-monitoring/index.yml)

* [如何使用 Azure 标识访问评审](../active-directory/governance/access-reviews-overview.md)

* [删除运行方式帐户或经典运行方式帐户](./delete-run-as-account.md)

* [管理 Azure 自动化运行方式帐户](./manage-runas-account.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11：监视尝试访问已停用凭据的行为

**指导**：你有权访问 Azure AD 登录活动、审核和风险事件日志源，以便与任何 SIEM/监视工具集成。

可以通过为 Azure Active Directory 用户帐户创建诊断设置，并将审核日志和登录日志发送到 Log Analytics 工作区，来简化此过程。 你可以在 Log Analytics 工作区中配置所需的警报。

* [如何将 Azure 活动日志集成到 Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12：针对帐户登录行为偏差发出警报

**指南**：使用 Azure AD 风险和标识保护功能来配置对检测到的与网络资源的用户标识相关的可疑操作的自动响应。 还可以将数据引入 Azure Sentinel 中以便进一步调查。

* [如何查看 Azure AD 风险登录](../active-directory/identity-protection/overview-identity-protection.md)

* [如何配置和启用标识保护风险策略](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

* [如何加入 Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13：在支持场合下为 Microsoft 提供对相关客户数据的访问权限

**指南**：对于 Azure Automation 帐户，Microsoft 支持人员可以在开放支持案例中访问平台资源元数据，而无需使用其他工具。

但是，当使用由 Azure 虚拟机支持的混合 Runbook 辅助角色，而第三方需要访问客户数据时 (如) ，请使用 Azure 虚拟机的客户密码箱 (预览) 来查看和批准或拒绝客户数据访问请求。

* [了解客户密码箱](../security/fundamentals/customer-lockbox-overview.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

## <a name="data-protection"></a>数据保护

有关详细信息，请参阅[安全控制：数据保护](../security/benchmarks/security-control-data-protection.md)。

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1：维护敏感信息的清单

**指导**：使用标记有助于跟踪存储或处理敏感信息的 Azure 自动化资源。

* [如何创建和使用标记](../azure-resource-manager/management/tag-resources.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2：隔离存储或处理敏感信息的系统

**指导**：为开发、测试和生产实施单独的订阅和/或管理组。 使用独立的自动化帐户资源来隔离环境。 混合 Runbook 辅助角色之类的资源应当按虚拟网络/子网进行分隔，相应地进行标记，并在网络安全组 (NSG) 或 Azure 防火墙中进行保护。 对于存储或处理敏感数据的虚拟机，请实施相应的策略和过程，以便在不使用的时候将其关闭。

* [如何创建其他 Azure 订阅](../cost-management-billing/manage/create-subscription.md)

* [如何创建管理组](../governance/management-groups/create-management-group-portal.md)

* [如何创建和使用标记](../azure-resource-manager/management/tag-resources.md)

* [如何创建虚拟网络](../virtual-network/quick-create-portal.md)

* [如何创建采用安全配置的 NSG](../virtual-network/tutorial-filter-network-traffic.md)

* [如何部署 Azure 防火墙](../firewall/tutorial-firewall-deploy-portal.md)

* [如何通过 Azure 防火墙配置“警报”或“发出警报并拒绝”](../firewall/threat-intel.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3：监视和阻止未经授权的敏感信息传输

**指导**：使用混合 Runbook 辅助角色功能时，请利用 Azure 市场中有关网络外围的第三方解决方案，该解决方案可监视并阻止敏感信息的未授权传输，同时提醒信息安全专业人员。

对于 Microsoft 管理的基础平台，Microsoft 会将所有客户内容视为敏感数据，并全方位防范客户数据丢失和泄露。 为了确保 Azure 中的客户数据保持安全，Microsoft 已实施并维护一套可靠的数据保护控制机制和功能。

* [了解 Azure 中的客户数据保护](../security/fundamentals/protection-customer-data.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4：加密传输中的所有敏感信息

**指导**：加密传输中的所有敏感信息。 确保连接到 Azure 虚拟网络中的 Azure 资源的任何客户端能够协商 TLS 1.2 或更高版本。 对于所有外部 HTTPS 终结点，Azure 自动化完全支持并强制实施传输层 (TLS) 1.2 或更高版本和所有客户端调用（通过 Webhook、DSC 节点、混合 Runbook 辅助角色来进行）。

请按照 Azure 安全中心的建议，了解静态加密和传输中的加密（如果适用）。

* [了解 Azure 传输中的加密](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

* [Azure 自动化 TLS 1.2 强制实施](../active-directory/hybrid/reference-connect-tls-enforcement.md)

**Azure 安全中心监视**：是

**责任**：共享

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5：使用有效的发现工具识别敏感数据

**指导**：使用第三方主动发现工具来确定组织的技术系统（包括现场或远程服务提供商处的技术系统）存储、处理或传输的所有敏感信息，并更新组织的敏感信息清单。

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6：使用 Azure RBAC 控制对资源的访问

**指导**：使用 Azure 基于角色的访问控制 (Azure RBAC) 通过内置角色定义来控制对 Azure 自动化资源的访问，并遵循最小权限访问模型或“权限刚好足够”访问模型为访问自动化资源的用户分配访问权限。 使用混合 Runbook 辅助角色时，请利用这些虚拟机的托管标识以避免使用服务主体。在同时使用多租户或混合 Runbook 辅助角色时，请确保对 Runbook 辅助角色的标识应用适当范围的 Azure RBAC 权限。

* [如何配置 Azure RBAC](../role-based-access-control/role-assignments-portal.md)

* [混合 Runbook 辅助角色的 Runbook 权限](./automation-hybrid-runbook-worker.md#runbook-permissions-for-a-hybrid-runbook-worker)

* [管理角色权限和安全性](./automation-role-based-access-control.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7：使用基于主机的数据丢失防护来强制实施访问控制

**指导**：Azure 自动化目前没有公开基础多租户 runbook 辅助角色的虚拟机，这由平台进行处理。 如果你使用的是无混合 Runbook 辅助角色的现成服务，则此控制不适用。

如果你使用由 Azure 虚拟机提供支持的混合 Runbook 辅助角色，则需要使用第三方基于主机的数据丢失防护解决方案来对托管的混合 Runbook 辅助角色虚拟机强制实施访问控制。

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8：静态加密敏感信息

**指导**：为 Azure 自动化使用客户管理的密钥。 Azure 自动化支持使用客户管理的密钥来加密使用的所有“安全资产”，例如凭据、证书、连接和加密的变量。 将加密的变量用于 runbook 来满足所有持久变量查找需求，以防止意外暴露。

使用混合 Runbook 辅助角色时，将使用服务器端加密或 Azure 磁盘加密 (ADE) 对虚拟机上的虚拟磁盘进行静态加密。 Azure 磁盘加密利用 Windows 的 BitLocker 功能，通过来宾 VM 中的客户管理的密钥来加密托管磁盘。 使用客户托管密钥的服务器端加密改进了 ADE，它通过加密存储服务中的数据使你可以为 VM 使用任何 OS 类型和映像。

* [Azure 托管磁盘的服务器端加密](../virtual-machines/disk-encryption.md)

* [适用于 Windows VM 的 Azure 磁盘加密](../virtual-machines/windows/disk-encryption-overview.md)

* [为自动化帐户使用客户管理的密钥](./automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)

* [Azure 自动化中的托管变量](./shared-resources/variables.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9：记录对关键 Azure 资源的更改并对此类更改发出警报

**指导**：当关键 Azure 资源（例如网络组件、Azure 自动化帐户和 runbook）发生更改时，请将 Azure Monitor 与 Azure 活动日志结合使用来创建警报。

* [网络安全组的诊断日志记录](../private-link/private-link-overview.md#logging-and-monitoring)

* [如何针对 Azure 活动日志事件创建警报](../azure-monitor/platform/alerts-activity-log.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

## <a name="vulnerability-management"></a>漏洞管理

有关详细信息，请参阅[安全控制：漏洞管理](../security/benchmarks/security-control-vulnerability-management.md)。

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1：运行自动漏洞扫描工具

**指导**：遵循 Azure 安全中心内关于在 Azure 资源上执行漏洞评估的建议。

* [Azure 安全中心的安全建议](../security-center/security-center-recommendations.md)

* [安全中心建议参考](../security-center/recommendations-reference.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2：部署自动操作系统修补管理解决方案

**指导**：Azure 自动化目前没有公开基础多租户 runbook 辅助角色的虚拟机，这由平台进行处理。 如果你使用的是无混合 Runbook 辅助角色的现成服务，则此控制不适用。

如果你使用由 Azure 虚拟机提供支持的混合 Runbook 辅助角色，请使用 Azure 更新管理来管理虚拟机的更新和修补程序。 更新管理依赖于本地配置的更新存储库来修补受支持的 Windows 系统。 可以使用 System Center Updates Publisher (Updates Publisher) 之类的工具将自定义更新发布到 Windows Server Update Services (WSUS) 中。 在这种情况下，允许更新管理使用第三方软件来修补使用 Configuration Manager 作为其更新存储库的计算机。

* [Azure 中的更新管理](./update-management/overview.md)

* [管理 VM 的更新和修补程序](./update-management/manage-updates-for-vm.md)

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5.3：为第三方软件部署自动修补程序管理解决方案

**指导**：Azure 自动化目前没有公开基础多租户 runbook 辅助角色的虚拟机，这由平台进行处理。 如果你使用的是无混合 Runbook 辅助角色的现成服务，则此控制不适用。

如果你使用由 Azure 虚拟机提供支持的混合 Runbook 辅助角色，则可以使用 Azure 更新管理来管理虚拟机的更新和修补程序。 更新管理依赖于本地配置的更新存储库来修补受支持的 Windows 系统。 可以使用 System Center Updates Publisher (Updates Publisher) 之类的工具将自定义更新发布到 Windows Server Update Services (WSUS) 中。 这种情况使得更新管理可以通过第三方软件来修补使用 Configuration Manager 作为其更新存储库的计算机。

* [Azure 中的更新管理解决方案](./update-management/overview.md)

* [管理 Azure VM 的更新和修补程序](./update-management/manage-updates-for-vm.md)

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4：比较连续进行的漏洞扫描

**指导**：以一致的间隔导出扫描结果，并比较结果以验证漏洞是否已修复。 使用 Azure 安全中心建议的漏洞管理建议时，客户可以转到所选解决方案的门户查看历史扫描数据。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5：使用风险评级过程来确定已发现漏洞的修正措施的优先级

**指导**：使用 Azure 安全中心提供的默认风险等级（安全分数）来帮助确定发现的漏洞的修正优先级。

* [了解 Azure 安全中心安全功能分数](../security-center/secure-score-security-controls.md)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="inventory-and-asset-management"></a>库存和资产管理

有关详细信息，请参阅[安全控制：清单和资产管理](../security/benchmarks/security-control-inventory-asset-management.md)。

### <a name="61-use-automated-asset-discovery-solution"></a>6.1：使用自动化资产发现解决方案

**指导**：使用 Azure Resource Graph 查询并发现订阅中的所有 Azure 自动化资源。 确保你在租户中拥有适当的（读取）权限，并且可以枚举所有 Azure 订阅，以及订阅中的资源。

* [如何使用 Azure Resource Graph 创建查询](../governance/resource-graph/first-query-portal.md)

* [如何查看 Azure 订阅](/powershell/module/az.accounts/get-azsubscription)

* [了解 Azure RBAC](../role-based-access-control/overview.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="62-maintain-asset-metadata"></a>6.2：维护资产元数据

**指导**：将标记应用到 Azure资源，以便有条理地将元数据组织成某种分类。

* [如何创建和使用标记](../azure-resource-manager/management/tag-resources.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="63-delete-unauthorized-azure-resources"></a>6.3：删除未经授权的 Azure 资源

**指导**：在适用的情况下，请使用标记、管理组和单独的订阅来组织和跟踪 Azure 自动化资源。 定期核对清单，确保及时地从订阅中删除未经授权的资源。 删除任何未使用的运行方式帐户，以最大程度地降低暴露的受攻击面。

* [如何创建其他 Azure 订阅](../cost-management-billing/manage/create-subscription.md)

* [如何创建管理组](../governance/management-groups/create-management-group-portal.md)

* [如何创建和使用标记](../azure-resource-manager/management/tag-resources.md)

* [删除运行方式帐户或经典运行方式帐户](./delete-run-as-account.md)

* [管理 Azure 自动化运行方式帐户](./manage-runas-account.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4：定义并维护已获批 Azure 资源的清单

**指导**：你将需要根据组织需求，创建已获批 Azure 资源以及已获批用于计算资源的软件的清单。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5：监视未批准的 Azure 资源

**指导**：在 Azure Policy 中使用以下内置策略定义，对可以在客户订阅中创建的资源类型施加限制：
- 不允许的资源类型
- 允许的资源类型

此外，请使用 Azure Resource Graph 来查询/发现订阅中的资源。 这可以在基于高安全性的环境（例如具有存储帐户的环境）中提供帮助。

* [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [如何使用 Azure Resource Graph 创建查询](../governance/resource-graph/first-query-portal.md)

* [适用于 Azure 自动化的 Azure Policy 内置项示例](./policy-reference.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6：监视计算资源中未批准的软件应用程序

**指导**：Azure 自动化产品/服务目前没有公开基础多租户 runbook 辅助角色的虚拟机，这由平台进行处理。 如果你使用的是无混合辅助角色的现成服务，则此控制不适用。 但是，可以安装、删除和管理允许 runbook 通过门户或 cmdlet 访问的 PowerShell 或 Python 模块。 应当为 runbook 删除或更新未批准的或旧的模块。

如果你使用的是由 Azure 虚拟机支持的混合 Runbook 辅助角色，则 Azure Automation 会在部署、操作和停止工作负荷和资源的过程中提供完全控制。 利用 Azure 虚拟机清单自动收集有关虚拟机上的所有软件的信息。 可从 Azure 门户获取软件名称、版本、发布者和刷新时间。 若要获得安装日期和其他信息的访问权限，客户需要启用来宾级诊断并将 Windows 事件日志置于 Log Analytics 工作区中。

* [Azure 自动化简介](./automation-intro.md)

* [如何启用 Azure VM 清单](./automation-tutorial-installed-software.md)

* [在 Azure 自动化中管理模块](./shared-resources/modules.md)

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7：删除未批准的 Azure 资源和软件应用程序

**指导**：根据客户的公司准则，客户可以阻止通过 Azure Policy 来创建或使用资源的操作。 你可以实施自己的流程来删除未经授权的资源。 在 Azure 自动化产品/服务中，可以安装、删除和管理允许 runbook 通过门户或 cmdlet 访问的 PowerShell 或 Python 模块。 应当为 runbook 删除或更新未批准的或旧的模块。

* [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [在 Azure 自动化中管理模块](./shared-resources/modules.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="68-use-only-approved-applications"></a>6.8：仅使用已批准的应用程序

**指导**：使用混合 Runbook 辅助角色功能时，你可以利用 Azure 安全中心自适应应用程序控制来确保仅执行已授权软件，并阻止所有未授权软件在 Azure 虚拟机上执行。

* [如何使用 Azure 安全中心自适应应用程序控制](../security-center/security-center-adaptive-application.md)

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="69-use-only-approved-azure-services"></a>6.9：仅使用已批准的 Azure 服务

**指导**：在 Azure Policy 中使用以下内置策略定义，对可以在客户订阅中创建的资源类型施加限制：
- 不允许的资源类型
- 允许的资源类型

* [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [如何使用 Azure Policy 拒绝特定的资源类型](../governance/policy/samples/index.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10：维护已获批软件的清单

**指导**：使用混合 Runbook 辅助角色功能时，可以将 Azure 安全中心自适应应用程序控制功能与混合辅助角色虚拟机一起使用。

自适应应用程序控制是 Azure 安全中心提供的智能、自动化、端到端的解决方案，有助于控制可在 Azure 和非 Azure 计算机（Windows 和 Linux）上运行的应用程序。 如果此方案不满足组织的要求，则实现第三方解决方案。

* [如何使用 Azure 安全中心自适应应用程序控制](../security-center/security-center-adaptive-application.md)

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11：限制用户与 Azure 资源管理器进行交互的能力

**指导**：通过将“Microsoft Azure 管理”应用配置为阻止来自不安全的或未经批准的位置或设备的访问，使用 Azure 条件访问策略来限制用户与 Azure 资源管理器交互的功能。

* [如何配置条件访问以阻止访问 Azure 资源管理器](../role-based-access-control/conditional-access-azure-management.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12：限制用户在计算资源中执行脚本的功能

**指导**：使用混合 Runbook 辅助角色功能时，根据脚本的类型，可以使用特定于操作系统的配置或第三方资源来限制用户在 Azure 计算资源中执行脚本的能力。 还可以利用 Azure 安全中心自适应应用程序控制来确保仅执行已授权软件，并阻止所有未授权软件在 Azure 虚拟机上执行。

* [如何在 Windows 环境中控制 PowerShell 脚本的执行](/powershell/module/microsoft.powershell.security/set-executionpolicy)

* [如何使用 Azure 安全中心自适应应用程序控制](../security-center/security-center-adaptive-application.md)

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13：以物理或逻辑方式隔离高风险应用程序

**指导**：可通过虚拟网络、子网、订阅、管理组等构造使用单独的网络和资源容器来隔离你的 Azure 环境中部署的高风险应用程序，并可使用 Azure 防火墙、Web 应用程序防火墙 (WAF) 或网络安全组 (NSG) 对其进行充分保护。

* [Azure 中的虚拟网络和虚拟机](../virtual-machines/network-overview.md)

* [Azure 防火墙概述](../firewall/overview.md)

* [Azure Web 应用程序防火墙概述](../web-application-firewall/overview.md)

* [网络安全组](../virtual-network/network-security-groups-overview.md)

* [Azure 虚拟网络概述](../virtual-network/virtual-networks-overview.md)

* [使用 Azure 管理组来组织资源](../governance/management-groups/overview.md)

* [订阅决策指南](/azure/cloud-adoption-framework/decision-guides/subscriptions/)

**Azure 安全中心监视**：不适用

**责任**：不适用

## <a name="secure-configuration"></a>安全配置

有关详细信息，请参阅[安全控制：安全配置](../security/benchmarks/security-control-secure-configuration.md)。

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1：为所有 Azure 资源建立安全配置

**指导**：使用 Azure Policy 别名创建自定义策略，以审核或强制实施 Azure 自动化和相关资源的配置。 你还可以使用内置的 Azure Policy 定义。

此外，Azure 资源管理器能够以 JavaScript 对象表示法 (JSON) 导出模板，应该对其进行检查，以确保配置满足/超过组织的安全要求。

还可以使用来自 Azure 安全中心的建议作为 Azure 资源的安全配置基线。

* [如何查看可用的 Azure Policy 别名](/powershell/module/az.resources/get-azpolicyalias)

* [教程：创建和管理策略以强制实施符合性](../governance/policy/tutorials/create-and-manage.md)

* [适用于 Azure 自动化的 Azure Policy 内置项示例](./policy-reference.md)

* [在 Azure 门户中将单资源和多资源导出到模板](../azure-resource-manager/templates/export-template-portal.md)

* [安全建议 - 参考指南](../security-center/recommendations-reference.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="72-establish-secure-operating-system-configurations"></a>7.2：建立安全的操作系统配置

**指导**：Azure 自动化目前没有公开基础多租户 runbook 辅助角色的虚拟机或 OS。 这由平台进行处理。 如果你使用的是无混合 Runbook 辅助角色的现成服务，则此控制不适用。

使用混合 Runbook 辅助角色功能时，请使用 Azure 安全中心建议[修复虚拟机上安全配置中的漏洞]来维护虚拟机上的安全配置。

* [如何监视 Azure 安全中心建议](../security-center/security-center-recommendations.md)

* [如何修正 Azure 安全中心建议](../security-center/security-center-remediate-recommendations.md)

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3：维护安全的 Azure 资源配置

**指导**：使用 Azure 资源管理器模板和 Azure Policy 安全地配置与 Azure 自动化关联的 Azure 资源。 Azure 资源管理器模板是基于 JSON 的文件，用来部署 Azure 资源。任何自定义模板都需要在代码存储库中安全地存储和维护。 使用源代码管理集成功能，可以通过源代码管理存储库中的脚本使自动化帐户中的 Runbook 保持最新。 使用 Azure Policy“[拒绝]”和“[不存在则部署]”对不同的 Azure 资源强制实施安全设置。

* [使用源代码管理集成](./source-control-integration.md)

* [有关创建 Azure 资源管理器模板的信息](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)

* [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [了解 Azure Policy 效果](../governance/policy/concepts/effects.md)

* [使用 Azure 资源管理器模板部署自动化帐户](./quickstart-create-automation-account-template.md#deploy-the-template)

* [适用于 Azure 自动化的 Azure Policy 内置项示例](./policy-reference.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4：维护安全的操作系统配置

**指导**：Azure 自动化目前没有公开基础多租户 runbook 辅助角色的虚拟机或 OS，这由平台进行处理。 如果你使用的是无混合 Runbook 辅助角色的现成服务，则此控制不适用。

使用混合 Runbook 辅助角色功能时，可通过多个选项维护用于部署的 Azure 虚拟机的安全配置：

- Azure 资源管理器模板：这些是基于 JSON 的文件，用于从 Azure 门户部署 VM。自定义模板将需要进行维护。 Microsoft 对基本模板进行维护。
- 自定义虚拟硬盘 (VHD)：在某些情况下，可能需要使用自定义 VHD 文件，例如在处理无法通过其他方式管理的复杂环境时。
- Azure Automation State Configuration：部署基本 OS 后，可以将其用于更精细的设置控制，并通过自动化框架强制执行。

对于大部分方案，Microsoft 基本 VM 模板与 Azure Automation State Configuration 相结合可以帮助满足和维护安全要求。

* [有关如何下载 VM 模板的信息](/previous-versions/azure/virtual-machines/windows/download-template)

* [有关创建 ARM 模板的信息](../virtual-machines/windows/ps-template.md)

* [如何将自定义 VM VHD 上传到 Azure](/azure-stack/operator/azure-stack-add-vm-image)

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5：安全存储 Azure 资源的配置

**指导**：使用 Azure DevOps 安全地存储和管理代码，如自定义 Azure 策略、Azure 资源管理器模板和 Desired State Configuration 脚本。 若要访问在 Azure DevOps 中管理的资源，可以向特定用户、内置安全组或 Azure Active Directory（如果与 Azure DevOps 集成）或 Active Directory（如果与 TFS 集成）中定义的组授予或拒绝授予权限。 使用源代码管理集成功能，可以通过源代码管理存储库中的脚本使自动化帐户中的 Runbook 保持最新。

* [如何在 Azure DevOps 中存储代码](/azure/devops/repos/git/gitworkflow)

* [关于 Azure DevOps 中的权限和组](/azure/devops/organizations/security/about-permissions)

* [使用源代码管理集成](./source-control-integration.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="76-securely-store-custom-operating-system-images"></a>7.6：安全存储自定义操作系统映像

**指导**：Azure 自动化目前没有公开基础多租户 runbook 辅助角色的虚拟机或 OS，这由平台进行处理。 如果你使用的是无混合 Runbook 辅助角色的现成服务，则此控制不适用。

使用混合 Runbook 辅助角色功能时，请确保正确地限制对存储帐户中的自定义 OS 映像的访问，以便只有经过授权的用户才能访问该映像。

* [了解 Azure RBAC](../role-based-access-control/rbac-and-directory-admin-roles.md)

* [如何配置 Azure RBAC](../role-based-access-control/quickstart-assign-role-user-portal.md)

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7：部署 Azure 资源的配置管理工具

**指导**：使用 Azure Policy 为 Azure 资源定义和实施标准安全配置。 使用 Azure Policy 别名创建自定义策略，审核或强制实施 Azure 资源的网络配置。 还可以使用与特定资源相关的内置策略定义。

* [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [如何使用别名](../governance/policy/concepts/definition-structure.md#aliases)

* [适用于 Azure 自动化的 Azure Policy 内置项示例](./policy-reference.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8：部署操作系统的配置管理工具

**指导**：Azure 自动化目前没有公开基础多租户 runbook 辅助角色的虚拟机或 OS，这由平台进行处理。 如果你使用的是无混合 Runbook 辅助角色的现成服务，则此控制不适用。

使用混合 Runbook 辅助角色功能时，请在 Runbook 辅助角色上使用 Azure Automation State Configuration，这是一项配置管理服务，适用于任何云或本地数据中心内的 Desired State Configuration (DSC) 节点。 它可让你从中心的安全位置快速轻松地扩展到数千台计算机。 可以轻松登记计算机、为其分配声明性配置并查看显示每台计算机是否符合指定的所需状态的报告。

* [加入 Azure Automation State Configuration 管理的计算机](./automation-dsc-onboarding.md)

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9：为 Azure 资源实施自动配置监视

**指导**：使用 Azure Policy 对 Azure 资源配置发出警报和进行审核。可以使用策略来检测未配置专用终结点的特定资源。

使用混合 Runbook 辅助角色功能时，利用 Azure 安全中心为 Azure 虚拟机执行基线扫描。 其他适用于自动化配置的方法包括 Azure Automation State Configuration。

* [如何在 Azure 安全中心修正建议](../security-center/security-center-remediate-recommendations.md)

* [Azure Automation State Configuration 入门](./automation-dsc-getting-started.md)

* [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [适用于 Azure 自动化的 Azure Policy 内置项示例](./policy-reference.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10：为操作系统实施自动配置监视

**指导**：Azure 自动化产品/服务目前没有公开基础多租户 runbook 辅助角色的虚拟机或 OS，这由平台进行处理。 如果你使用的是无混合辅助角色的现成服务，则此控制不适用。

使用混合 Runbook 辅助角色功能时，请为 Runbook 辅助角色使用 Azure Automation State Configuration，这是一项配置管理服务，适用于任何云或本地数据中心内的 Desired State Configuration (DSC) 节点。 它可让你从中心的安全位置快速轻松地扩展到数千台计算机。 可以轻松登记计算机、为其分配声明性配置并查看显示每台计算机是否符合指定的所需状态的报告。

* [加入 Azure Automation State Configuration 管理的计算机](./automation-dsc-onboarding.md)

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="711-manage-azure-secrets-securely"></a>7.11：安全管理 Azure 机密

**指导**：将托管服务标识与 Azure Key Vault 结合使用，以便简化和保护云应用程序的机密管理。

* [为自动化帐户使用客户管理的密钥](./automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)

* [结合托管标识使用 Runbook 身份验证](./automation-hrw-run-runbooks.md#runbook-auth-managed-identities)

* [如何创建 Key Vault](../key-vault/general/quick-create-portal.md)

* [如何向 Key Vault 进行身份验证](../key-vault/general/authentication.md)

* [如何分配 Key Vault 访问策略](../key-vault/general/assign-access-policy-portal.md)

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="712-manage-identities-securely-and-automatically"></a>7.12：安全自动管理标识

**指导**：使用托管标识在 Azure AD 中为 Azure 服务提供自动托管标识。 使用托管标识可以向支持 Azure AD 身份验证的任何服务（包括 Key Vault）进行身份验证，无需在代码中放入任何凭据。

* [如何配置托管标识](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

* [为自动化帐户使用客户管理的密钥](./automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13：消除意外的凭据透露

**指南**：实施凭据扫描程序来识别代码中的凭据。 凭据扫描程序还会建议将发现的凭据转移到更安全的位置，例如 Azure Key Vault。

* [如何设置凭据扫描程序](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure 安全中心监视**：目前不可用

**责任**：客户

## <a name="malware-defense"></a>恶意软件防护

有关详细信息，请参阅[安全控制：恶意软件防护](../security/benchmarks/security-control-malware-defense.md)。

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1：使用集中管理的反恶意软件

**指导**：Azure 自动化产品/服务目前没有公开基础多租户 runbook 辅助角色的虚拟机或 OS，这由平台进行处理。 如果你使用的是无混合 Runbook 辅助角色的现成服务，则此控制不适用。

使用混合 Runbook 辅助角色功能时，请为 Azure Windows 虚拟机使用 Microsoft Antimalware，以持续监视和保护 runbook 辅助角色资源。

* [如何为云服务和虚拟机配置 Microsoft Anti-malware](../security/fundamentals/antimalware.md)

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2：预先扫描要上传到非计算 Azure 资源的文件

**指导**：不适用；Azure 自动化即服务不存储文件。 在支持 Azure 服务（例如 Azure 自动化）的底层主机上已启用 Microsoft Antimalware，但该软件不会针对你的内容运行。

* [了解适用于 Azure 云服务和虚拟机的 Microsoft Antimalware](../security/fundamentals/antimalware.md)

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>步骤 8.3：确保反恶意软件和签名已更新

**指导**：Azure 自动化目前没有公开基础多租户 runbook 辅助角色的虚拟机或 OS，这由平台进行处理。 如果你使用的是无混合 Runbook 辅助角色的现成服务，则此控制不适用。

使用混合 Runbook 辅助角色功能时，请使用适用于 Azure 的 Microsoft Antimalware 自动将最新的签名、平台和引擎更新默认安装到 runbook 辅助角色。 请遵循 Azure 安全中心中的建议：“计算和应用”用于确保所有终结点都具有最新的签名。 通过与 Azure 安全中心集成的 Microsoft Defender 高级威胁防护服务，可以使用附加的安全性进一步保护 Windows OS，以限制基于病毒或恶意软件进行攻击的风险。

* [如何为 Azure 云服务和虚拟机部署 Microsoft Antimalware](../security/fundamentals/antimalware.md)

* [Microsoft Defender 高级威胁防护](/windows/security/threat-protection/microsoft-defender-atp/onboard-configure)

**Azure 安全中心监视**：不适用

**责任**：不适用

## <a name="data-recovery"></a>数据恢复

有关详细信息，请参阅[安全控制：数据恢复](../security/benchmarks/security-control-data-recovery.md)。

### <a name="91-ensure-regular-automated-back-ups"></a>9.1：确保定期执行自动备份

**指导**：使用 Azure 资源管理器部署 Azure 自动化帐户和相关资源。 Azure 资源管理器提供导出模板的功能，这些模板可用作还原 Azure 自动化帐户和相关资源的备份。 使用 Azure 自动化定期调用 Azure 资源管理器模板导出 API。

使用源代码管理集成功能，可以通过源代码管理存储库中的脚本使自动化帐户中的 Runbook 保持最新。

* [Azure 资源管理器概述](../azure-resource-manager/management/overview.md)

* [适用于 Azure 自动化资源的 Azure 资源管理器模板参考](/azure/templates/microsoft.automation/allversions)

* [使用 Azure 资源管理器模板创建自动化帐户](./quickstart-create-automation-account-template.md)

* [在 Azure 门户中将单资源和多资源导出到模板](../azure-resource-manager/templates/export-template-portal.md)

* [资源组 - 导出模板](/rest/api/resources/resourcegroups/exporttemplate)

* [Azure 自动化简介](./automation-intro.md)

* [如何在 Azure 中备份密钥保管库密钥](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

* [为自动化帐户使用客户管理的密钥](./automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)

* [使用源代码管理集成](./source-control-integration.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2：执行完整系统备份，并备份客户管理的所有密钥

**指导**：使用 Azure 资源管理器部署 Azure 自动化帐户和相关资源。 Azure 资源管理器提供导出模板的功能，这些模板可用作还原 Azure 自动化帐户和相关资源的备份。 使用 Azure 自动化定期调用 Azure 资源管理器模板导出 API。 在 Azure Key Vault 中备份客户管理的密钥。 你可以使用 Azure 门户或 PowerShell 将 runbook 导出到脚本文件。

* [Azure 资源管理器概述](../azure-resource-manager/management/overview.md)

* [适用于 Azure 自动化资源的 Azure 资源管理器模板参考](/azure/templates/microsoft.automation/allversions)

* [使用 Azure 资源管理器模板创建自动化帐户](./quickstart-create-automation-account-template.md)

* [在 Azure 门户中将单资源和多资源导出到模板](../azure-resource-manager/templates/export-template-portal.md)

* [资源组 - 导出模板](/rest/api/resources/resourcegroups/exporttemplate)

* [Azure 自动化简介](./automation-intro.md)

* [如何在 Azure 中备份密钥保管库密钥](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

* [为自动化帐户使用客户管理的密钥](./automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)

* [自动化帐户的 Azure 数据备份](./automation-managing-data.md#data-backup)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3：验证所有备份，包括客户管理的密钥

**指导**：确保能够将 Azure 资源管理器模板定期部署到隔离订阅（如果需要）。 测试对备份的客户管理的密钥进行还原。

* [使用 ARM 模板和 Azure 门户部署资源](../azure-resource-manager/templates/deploy-portal.md)

* [如何在 Azure 中还原密钥保管库密钥](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey)

* [为自动化帐户使用客户管理的密钥](./automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4：确保保护备份和客户管理的密钥

**指导**：使用 Azure DevOps 安全地存储和管理 Azure 资源管理器模板之类的代码。 若要保护在 Azure DevOps 中管理的资源，可以向特定用户、内置安全组或 Azure Active Directory（如果与 Azure DevOps 集成）或 Active Directory（如果与 TFS 集成）中定义的组授予或拒绝授予权限。

使用源代码管理集成功能，可以通过源代码管理存储库中的脚本使自动化帐户中的 Runbook 保持最新。

* [如何在 Azure DevOps 中存储代码](/azure/devops/repos/git/gitworkflow)

* [关于 Azure DevOps 中的权限和组](/azure/devops/organizations/security/about-permissions)

* [使用源代码管理集成](./source-control-integration.md)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="incident-response"></a>事件响应

有关详细信息，请参阅[安全控制：事件响应](../security/benchmarks/security-control-incident-response.md)。

### <a name="101-create-an-incident-response-guide"></a>10.1：创建事件响应指导

**指南**：为组织制定事件响应指南。 确保在书面的事件响应计划中定义人员职责，以及事件处理/管理从检测到事件后审查的各个阶段。

* [关于建立自己的安全事件响应流程的指南](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Microsoft 安全响应中心事件分析](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

* [客户还可以利用 NIST 的“计算机安全事件处理指南”来制定他们自己的事件响应计划](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2：创建事件评分和优先级设定过程

**指导**：安全中心为每条警报分配严重性，以帮助你优先处理应该最先调查的警报。 严重性取决于安全中心在发出警报时所依据的检测结果和分析结果的置信度，以及导致发出警报的活动的恶意企图的置信度。

此外，请明确标记订阅（例如 生产、非生产）并创建命名系统来对 Azure 资源进行明确标识和分类，特别是处理敏感数据的资源。 你的责任是根据发生事件的 Azure 资源和环境的关键性确定修正警报的优先级。

* [Azure 安全中心中的安全警报](../security-center/security-center-alerts-overview.md)

* [使用标记整理 Azure 资源](../azure-resource-manager/management/tag-resources.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="103-test-security-response-procedures"></a>10.3：测试安全响应过程

**指导**：定期执行演练来测试系统的事件响应功能，以帮助保护 Azure 资源。 识别弱点和差距，并根据需要修改计划。

* [NIST 发布 - IT 计划和功能的测试、训练和演练计划指南](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4：提供安全事件联系人详细信息，并针对安全事件配置警报通知

**指导**：如果 Microsoft 安全响应中心 (MSRC) 发现数据被某方非法访问或未经授权访问，Microsoft 会使用安全事件联系信息联系用户。 事后审查事件，确保问题得到解决。

* [如何设置 Azure 安全中心安全联系人](../security-center/security-center-provide-security-contact-details.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5：将安全警报整合到事件响应系统中

**指南**：使用连续导出功能导出 Azure 安全中心警报和建议，以帮助确定 Azure 资源的风险。 使用连续导出可以手动导出或者持续导出警报和建议。 可以使用 Azure 安全中心数据连接器将警报流式传输到 Azure Sentinel。

* [如何配置连续导出](../security-center/continuous-export.md)

* [如何将警报流式传输到 Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="106-automate-the-response-to-security-alerts"></a>10.6：自动响应安全警报

**指导**：使用 Azure 安全中心内的工作流自动化功能，通过“逻辑应用”针对安全警报和建议自动触发响应，以保护 Azure 资源。

* [如何配置工作流自动化和逻辑应用](../security-center/workflow-automation.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

## <a name="penetration-tests-and-red-team-exercises"></a>渗透测试和红队练习

有关详细信息，请参阅[安全控制：渗透测试和红队演练](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)。

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1：定期对 Azure 资源执行渗透测试，确保修正所有发现的关键安全问题

**指导**：请遵循 Microsoft 互动规则，确保你的渗透测试不违反 Microsoft 政策。 使用 Microsoft 红队演练策略和执行，以及针对 Microsoft 托管云基础结构、服务和应用程序执行现场渗透测试。

* [参与的渗透测试规则](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

* [Microsoft 云红色组合](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure 安全中心监视**：不适用

**责任**：共享

## <a name="next-steps"></a>后续步骤

- 请参阅 [Azure 安全基准](../security/benchmarks/overview.md)
- 详细了解 [Azure 安全基线](../security/benchmarks/security-baselines-overview.md)