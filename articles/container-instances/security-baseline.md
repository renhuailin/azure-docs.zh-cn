---
title: 容器实例的 Azure 安全基线
description: 容器实例安全基准为实现 Azure 安全基准中指定的安全建议提供过程指南和资源。
author: msmbaldwin
ms.service: container-instances
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 07eaa9fd9add14f136d68c50bca15807ef4037ed
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101738083"
---
# <a name="azure-security-baseline-for-container-instances"></a>容器实例的 Azure 安全基线

此安全基线将 [Azure 安全基准版本 1.0](../security/benchmarks/overview-v1.md) 中的指南应用到容器实例。 Azure 安全基准提供有关如何在 Azure 上保护云解决方案的建议。
内容由 Azure 安全基准定义的 **安全控制** 和适用于容器实例的相关指南进行分组。 排除了不适用于容器实例的 **控件**。

 
若要查看容器实例如何完全映射到 Azure 安全基准，请参阅 [完整容器实例安全基线映射文件](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)。

## <a name="network-security"></a>网络安全

[有关详细信息，请参阅 *Azure 安全基线：* 网络安全性](../security/benchmarks/security-control-network-security.md)。

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1：保护虚拟网络中的 Azure 资源

**指南**：将 Azure 容器实例中的容器组与 azure 虚拟网络集成。  使用 azure 虚拟网络，可以将多个 Azure 资源（例如容器组）置于非 internet 可路由网络中。

使用 Azure Firewall 控制委派给 Azure 容器实例的子网的出站网络访问。 

- [将容器实例部署到 Azure 虚拟网络](/azure/container-instances/container-instance-vnet)

- [如何部署和配置 Azure 防火墙](../firewall/tutorial-firewall-deploy-portal.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2：监视并记录虚拟网络、子网和网络接口的配置与流量

**指导**：使用 Azure 安全中心并遵循网络保护建议来帮助保护 Azure 中的网络资源。 启用 NSG 流日志，并将日志发送到存储帐户以进行流量审核。 还可以将 NSG 流日志发送到 Log Analytics 工作区，并使用流量分析来提供有关 Azure 云中流量流的见解。 流量分析的优势包括能够可视化网络活动、识别热点、识别安全威胁、了解流量流模式，以及查明网络不当配置。 

- [如何启用 NSG 流日志](../network-watcher/network-watcher-nsg-flow-logging-portal.md) 

- [如何启用和使用流量分析](../network-watcher/traffic-analytics.md) 

- [了解 Azure 安全中心提供的网络安全](../security-center/security-center-network-recommendations.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="13-protect-critical-web-applications"></a>1.3：保护关键 Web 应用程序

**指导**：不适用。 基准适用于 Azure 应用服务或托管 Web 应用程序的计算资源。

**责任**：客户

**Azure 安全中心监视**：无

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4：拒绝与已知恶意的 IP 地址进行通信

**指导**：在虚拟网络上启用 DDoS 标准保护，以防范 DDoS 攻击。 根据 Azure 安全中心集成的威胁情报进行判断，拒绝与已知恶意的或未使用过的 Internet IP 地址通信。  在组织的每个网络边界上部署 Azure 防火墙，启用威胁情报并将其配置为针对恶意网络流量执行“发出警报并拒绝”操作。

可以使用 Azure 安全中心实时网络访问，将 NSG 配置为只能在有限时间内将终结点公开给已批准的 IP 地址。 另请使用 Azure 安全中心自适应网络强化，推荐基于实际流量和威胁情报限制端口和源 IP 的 NSG 配置。

- [如何配置 DDoS 防护](/azure/virtual-network/manage-ddos-protection)

- [如何部署 Azure 防火墙](../firewall/tutorial-firewall-deploy-portal.md)

- [了解 Azure 安全中心集成的威胁情报](../security-center/azure-defender.md)

- [了解 Azure 安全中心自适应网络强化](../security-center/security-center-adaptive-network-hardening.md)

- [Azure 安全中心实时网络访问控制](../security-center/security-center-just-in-time.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="15-record-network-packets"></a>1.5：记录网络数据包

**指南**：如果使用基于云的专用注册表（如 azure 容器注册表和 Azure 容器实例），则可以为连接到用于保护 Azure 容器注册表的子网的 NSG 启用网络安全组 (NSG) 流日志。 你可以将 NSG 流日志记录到 Azure 存储帐户中，以生成流记录。 如果需要调查异常活动，请启用 Azure 网络观察程序数据包捕获。

- [如何启用 NSG 流日志](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [如何启用网络观察程序](../network-watcher/network-watcher-create.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6：部署基于网络的入侵检测/入侵防护系统 (IDS/IPS)

**指导**：从 Azure 市场中选择一种产品/服务，该产品/服务应支持包含有效负载检查功能的 ID/IPS 功能。 如果不需要基于有效负载检查的入侵检测和/或防护，则可以使用包含威胁情报功能的 Azure 防火墙。 基于 Azure 防火墙威胁情报的筛选功能可以发出警报，并拒绝传入和传出已知恶意 IP 地址和域的流量。 IP 地址和域源自 Microsoft 威胁智能源。

在组织的每个网络边界上部署所选的防火墙解决方案，以检测和/或拒绝恶意流量。

- [Azure 市场](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

- [如何部署 Azure 防火墙](../firewall/tutorial-firewall-deploy-portal.md)

- [如何配置 Azure 防火墙警报](../firewall/threat-intel.md)

- [在虚拟网络中部署 - Azure 容器实例](container-instances-vnet.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="17-manage-traffic-to-web-applications"></a>1.7：管理发往 Web 应用程序的流量

**指导**：不适用。 基准适用于在 Azure 应用服务或计算资源上运行的 Web 应用程序。

**责任**：客户

**Azure 安全中心监视**：无

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8：最大程度地降低网络安全规则的复杂性和管理开销

**指南**：如果使用基于云的专用注册表（如 azure 容器注册表和 Azure 容器实例），对于需要访问容器注册表的资源，请使用 Azure 容器注册表服务的虚拟网络服务标记来定义网络安全组或 Azure 防火墙上的网络访问控制。 创建安全规则时，可以使用服务标记代替特定的 IP 地址。 在规则的相应源或目标字段中指定服务标记名称“AzureContainerRegistry”，可以允许或拒绝相应服务的流量。 Microsoft 会管理服务标记包含的地址前缀，并会在地址发生更改时自动更新服务标记。

- [允许按服务标记访问](https://docs.microsoft.com/azure/container-registry/container-registry-firewall-access-rules#allow-access-by-service-tag)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9：维护网络设备的标准安全配置

**指南**：将 Azure 容器注册表与 Azure 容器实例一起使用时，我们建议为与 azure 容器注册表关联的网络资源定义和实施标准安全配置。

使用 **microsoft.containerregistry** 和 **microsoft 网络** 命名空间中的 Azure 策略别名创建自定义策略，以便审核或强制执行容器注册表的网络配置。

可以使用 Azure 蓝图，通过将关键环境项目（例如 Azure 资源管理器模板、Azure RBAC 控件和策略定义）打包到单个蓝图定义中来简化大规模的 Azure 部署。 轻松将蓝图应用到新的订阅，并通过版本控制来微调控制措施和管理。

- [使用 Azure Policy 审核 Azure 容器注册表的合规性](../container-registry/container-registry-azure-policy.md)

- [如何创建 Azure 蓝图](../governance/blueprints/create-blueprint-portal.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="110-document-traffic-configuration-rules"></a>1.10：阐述流量配置规则

**指导**：客户可以使用 Azure 蓝图，通过在单个蓝图定义中打包关键环境项目（例如 Azure 资源管理器模板、Azure RBAC 控制措施和策略），来简化大规模的 Azure 部署。 轻松将蓝图应用到新的订阅，并通过版本控制来微调控制措施和管理。

- [如何创建 Azure 蓝图](../governance/blueprints/create-blueprint-portal.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11：使用自动化工具来监视网络资源配置和检测更改

**指导**：使用 Azure 活动日志监视网络资源配置，并检测与容器注册表相关的网络资源的更改。 在 Azure Monitor 中创建当关键网络资源发生更改时触发的警报。

- [如何查看和检索 Azure 活动日志事件](/azure/azure-monitor/platform/activity-log#view-the-activity-log)

- [如何在 Azure Monitor 中创建警报](/azure/azure-monitor/platform/alerts-activity-log)

**责任**：客户

**Azure 安全中心监视**：无

## <a name="logging-and-monitoring"></a>日志记录和监视

[有关详细信息，请参阅 *Azure 安全基线：* 日志记录和监视](../security/benchmarks/security-control-logging-monitoring.md)。

### <a name="22-configure-central-security-log-management"></a>2.2：配置中心安全日志管理

**指南**：通过 Azure Monitor 引入日志来聚合由 Azure 容器实例生成的安全数据。 在 Azure Monitor 中，使用 Log Analytics 工作区查询和执行分析，并使用 Azure 存储帐户进行长期/存档存储。

- [使用 Azure Monitor 日志进行容器组和实例日志记录](container-instances-log-analytics.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：为 Azure 资源启用审核日志记录

**指南**： Azure Monitor 收集先前称为诊断) 日志的资源日志 (用户驱动的事件。 收集并使用这些数据来审核容器身份验证事件，并为项目（如请求和推送事件）提供完整的活动跟踪，以便可以诊断容器组的安全问题。

- [使用 Azure Monitor 日志进行容器组和实例日志记录](container-instances-log-analytics.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="25-configure-security-log-storage-retention"></a>2.5：配置安全日志存储保留期

**指导**：在 Azure Monitor 中，根据组织的合规性规章设置 Log Analytics 工作区保留期。 使用 Azure 存储帐户进行长期/存档存储。

- [如何为 Log Analytics 工作区设置日志保留参数](/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="26-monitor-and-review-logs"></a>2.6：监视和查看日志

**指南**：分析和监视 Azure 容器实例日志中的异常行为，并定期检查结果。 使用 Azure Monitor 的 Log Analytics 工作区查看日志并对日志数据执行查询。

- [了解 Log Analytics 工作区](/azure/azure-monitor/log-query/log-analytics-tutorial)

- [如何在 Azure Monitor 中执行自定义查询](/azure/azure-monitor/log-query/get-started-queries)

- [如何创建启用日志的容器组和查询日志](container-instances-log-analytics.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7：针对异常活动启用警报

**指南**：如果使用基于云的专用注册表（如 azure 容器注册表和 Azure 容器实例），请使用 azure Log Analytics 工作区监视和警报与 azure 容器注册表相关的安全日志和事件中的异常活动。

- [用于诊断评估和审核的 Azure 容器注册表日志](../container-registry/container-registry-diagnostics-audit-logs.md)

- [如何针对 Log Analytics 日志数据发出警报](/azure/azure-monitor/learn/tutorial-response)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="28-centralize-anti-malware-logging"></a>2.8：集中管理反恶意软件日志记录

**指导**：不适用。 如果使用基于云的专用注册表（如 Azure 容器注册表和 Azure 容器实例），则 Azure 容器注册表不会处理或产生与反恶意软件相关的日志。

**责任**：客户

**Azure 安全中心监视**：无

### <a name="29-enable-dns-query-logging"></a>2.9：启用 DNS 查询日志记录

**指导**：不适用。 如果使用基于云的专用注册表（如 Azure 容器注册表和 Azure 容器实例），则 Azure 容器注册表是一个终结点，并且不会启动通信，并且服务不会查询 DNS。

**责任**：客户

**Azure 安全中心监视**：无

## <a name="identity-and-access-control"></a>标识和访问控制

[有关详细信息，请参阅 *Azure 安全基线：* 标识和访问控制](../security/benchmarks/security-control-identity-access-control.md)。

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1：维护管理帐户的清单

**指导**：Azure Active Directory (Azure AD) 具有必须显式分配且可查询的内置角色。 使用 Azure AD PowerShell 模块执行即席查询，以发现属于管理组成员的帐户。

如果将基于云的专用注册表（如 Azure 容器注册表）与 Azure 容器实例一起使用，则对于每个 Azure 容器注册表，请跟踪内置管理员帐户是已启用还是已禁用。 不使用该帐户时请将其禁用。

- [如何使用 PowerShell 获取 Azure AD 中的目录角色](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0&amp;preserve-view=true)

- [如何使用 PowerShell 获取 Azure AD 中目录角色的成员](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0&amp;preserve-view=true)

- [Azure 容器注册表管理员帐户](https://docs.microsoft.com/azure/container-registry/container-registry-authentication#admin-account)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="32-change-default-passwords-where-applicable"></a>3.2：在适用的情况下更改默认密码

**指导**：Azure Active Directory (Azure AD) 没有默认密码的概念。 其他需要密码的 Azure 资源会强制创建具有复杂性要求和最小密码长度要求的密码，这些要求因服务而异。 你对可能使用默认密码的第三方应用程序和市场服务负责。

如果在 azure 容器实例中使用基于云的专用注册表（如 Azure 容器注册表），并且启用了 Azure 容器注册表的默认管理员帐户，则会自动创建复杂密码并进行轮换。 不使用该帐户时请将其禁用。

- [Azure 容器注册表管理员帐户](https://docs.microsoft.com/azure/container-registry/container-registry-authentication#admin-account)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="33-use-dedicated-administrative-accounts"></a>3.3：使用专用管理帐户

**指南**：围绕专用管理帐户的使用创建标准操作程序。 使用 Azure 安全中心标识和访问管理来监视管理帐户的数量。

如果使用基于云的专用注册表（如 Azure 容器注册表和 Azure 容器实例），请创建启用容器注册表内置管理员帐户的过程。 不使用该帐户时请将其禁用。

- [了解 Azure 安全中心标识和访问](../security-center/security-center-identity-access.md)

- [Azure 容器注册表管理员帐户](https://docs.microsoft.com/azure/container-registry/container-registry-authentication#admin-account)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4：使用 Azure Active Directory 单一登录 (SSO)

**指南**：尽可能使用 Azure Active Directory (AZURE AD) SSO，而不是为每个服务配置单独的独立凭据。 请使用 Azure 安全中心标识和访问管理建议。

如果使用基于云的专用注册表（如 Azure 容器注册表和 Azure 容器实例）来对容器注册表进行单独访问，请使用与 Azure AD 集成的单独登录。

- [了解 Azure AD 的 SSO](../active-directory/manage-apps/what-is-single-sign-on.md)

- [单个登录到容器注册表](https://docs.microsoft.com/azure/container-registry/container-registry-authentication#individual-login-with-azure-ad)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5：对所有基于 Azure Active Directory 的访问使用多重身份验证

**指南**：启用 Azure Active Directory (Azure AD) 多重身份验证，并遵循 Azure 安全中心的标识和访问管理建议。

- [如何在 Azure 中启用多重身份验证](../active-directory/authentication/howto-mfa-getstarted.md)

- [如何在 Azure 安全中心监视标识和访问](../security-center/security-center-identity-access.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6：对所有管理任务使用专用计算机（特权访问工作站）

**指南**：通过配置为登录和配置 Azure 资源的多重身份验证，使用 paw (特权访问工作站) 。

- [了解特权访问工作站](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [如何在 Azure 中启用多重身份验证](../active-directory/authentication/howto-mfa-getstarted.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7：记录来自管理帐户的可疑活动并对其发出警报

**指导**：当环境中出现可疑或不安全的活动时，请使用 Azure Active Directory (Azure AD) 安全报告来生成日志和警报。 使用 Azure 安全中心监视标识和访问活动。

- [如何确定标记为存在风险活动的 Azure AD 用户](../active-directory/identity-protection/overview-identity-protection.md)

- [如何在 Azure 安全中心内监视用户的标识和访问活动](../security-center/security-center-identity-access.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8：仅从批准的位置管理 Azure 资源

**指南**：使用条件访问命名位置，仅允许从 IP 地址范围或国家/地区的特定逻辑分组进行访问。

- [如何在 Azure 中配置命名位置](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="39-use-azure-active-directory"></a>3.9：使用 Azure Active Directory

**指导**：使用 Azure Active Directory (Azure AD) 作为中心身份验证和授权系统。 Azure AD 通过对静态数据和传输中数据使用强加密来保护数据。 Azure AD 还会对用户凭据进行加盐、哈希处理和安全存储操作。

- [如何创建和配置 Azure AD 实例](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10：定期审查和协调用户访问

**指导**：Azure Active Directory (Azure AD) 提供日志来帮助发现过时的帐户。 此外，请使用 Azure 标识访问评审来有效管理组成员身份、对企业应用程序的访问和角色分配。 可以定期评审用户的访问权限，确保只有适当的用户才持续拥有访问权限。

- [了解 Azure AD 报告](/azure/active-directory/reports-monitoring/)

- [如何使用 Azure 标识访问评审](../active-directory/governance/access-reviews-overview.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11：监视尝试访问已停用凭据的行为

**指南**：你有权访问 Azure Active Directory (Azure AD) 登录活动、审核和风险事件日志源，这允许你与任何安全信息和事件管理 (SIEM) /Monitoring 工具集成。

可以通过创建 Azure AD 用户帐户的诊断设置，并将审核日志和登录日志发送到 Log Analytics 工作区来简化此过程。 你可以在 Log Analytics 工作区中配置所需的警报。

- [如何将 Azure 活动日志集成到 Azure Monitor](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12：针对帐户登录行为偏差发出警报

**指导**：使用 Azure Active Directory (Azure AD) 风险和标识保护功能配置对检测到的与用户标识相关的可疑操作的自动响应。

- [如何查看 Azure AD 有风险的登录](../active-directory/identity-protection/overview-identity-protection.md)

- [如何配置和启用标识保护风险策略](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13：在支持场合下为 Microsoft 提供对相关客户数据的访问权限

**指南**：不可用;Azure 容器实例当前不支持客户密码箱。

- [支持客户密码箱的服务列表](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability)

**责任**：客户

**Azure 安全中心监视**：无

## <a name="data-protection"></a>数据保护

[有关详细信息，请参阅 *Azure 安全基线：* 数据保护](../security/benchmarks/security-control-data-protection.md)。

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1：维护敏感信息的清单

**指导**：使用资源标记可以帮助跟踪用于存储或处理敏感信息的 Azure 容器注册表。

在注册表中对容器映像或其他项目进行标记和版本控制并锁定映像或存储库，以便跟踪可存储或处理敏感信息的映像。

- [如何创建和使用标记](../azure-resource-manager/management/tag-resources.md)

- [有关对容器映像进行标记和版本控制的建议](../container-registry/container-registry-image-tag-version.md)

- [锁定 Azure 容器注册表中的容器映像](../container-registry/container-registry-image-lock.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2：隔离存储或处理敏感信息的系统

**指导**：为开发、测试和生产实现单独的容器注册表、订阅和/或管理组。 存储或处理敏感数据的资源应当充分隔离。

资源应当按虚拟网络或子网进行分隔，相应地进行标记，并由网络安全组 (NSG) 或 Azure 防火墙提供保护。

- [如何创建其他 Azure 订阅](../cost-management-billing/manage/create-subscription.md)

- [如何创建管理组](../governance/management-groups/create-management-group-portal.md)

- [如何创建和使用标记](../azure-resource-manager/management/tag-resources.md)

- [使用 Azure 虚拟网络或防火墙规则限制对 Azure 容器注册表的访问权限](../container-registry/container-registry-vnet.md)

- [如何创建采用安全配置的 NSG](../virtual-network/tutorial-filter-network-traffic.md)

- [如何部署 Azure 防火墙](../firewall/tutorial-firewall-deploy-portal.md)

- [如何通过 Azure 防火墙配置“警报”或“发出警报并拒绝”](../firewall/threat-intel.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3：监视和阻止未经授权的敏感信息传输

**指导**：在网络外围部署一个自动化工具，用于监视敏感信息的未授权传输，并阻止此类传输，同时提醒信息安全专业人员。

对于由 Microsoft 管理的基础平台，Microsoft 将所有客户数据视为敏感数据，并在很大程度上防范客户数据丢失和公开。 为了确保 Azure 中的客户数据保持安全，Microsoft 实施并维护了一套可靠的数据保护控制措施和功能。

- [了解 Azure 中的客户数据保护](../security/fundamentals/protection-customer-data.md)

**责任**：共享

**Azure 安全中心监视**：无

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4：加密传输中的所有敏感信息

**指导**：确保连接到 Azure 容器注册表的任何客户端能够协商 TLS 1.2 或更高版本。 默认情况下，Microsoft Azure 资源会协商 TLS 1.2。

请按照 Azure 安全中心的建议，了解静态加密和传输中加密（如果适用）。

- [了解 Azure 传输中的加密](https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit)

**责任**：共享

**Azure 安全中心监视**：无

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5：使用有效的发现工具识别敏感数据

**指南**：如果使用基于云的专用注册表（如 azure 容器注册表和 Azure 容器实例），则 Azure 容器注册表尚不支持数据标识、分类和丢失防护功能。 如果需要出于合规性目的使用这些功能，请实施第三方解决方案。

对于由 Microsoft 管理的基础平台，Microsoft 将所有客户数据视为敏感数据，并在很大程度上防范客户数据丢失和公开。 为了确保 Azure 中的客户数据保持安全，Microsoft 实施并维护了一套可靠的数据保护控制措施和功能。

- [使用 Azure 容器实例加密部署数据](container-instances-encrypt-data.md)

- [了解 Azure 中的客户数据保护](../security/fundamentals/protection-customer-data.md)

**责任**：共享

**Azure 安全中心监视**：无

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6：使用基于角色的访问控制来控制对资源的访问

**指南**：如果使用基于云的专用注册表（如 Azure 容器注册表和 Azure 容器实例），请使用 azure 基于角色的访问控制 (azure RBAC) 管理对 Azure 容器注册表中的数据和资源的访问权限。

- [如何在 Azure 中配置 Azure RBAC](../role-based-access-control/role-assignments-portal.md)

- [Azure 容器注册表角色和权限](../container-registry/container-registry-roles.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7：使用基于主机的数据丢失防护来强制实施访问控制

**指导**：如果需要在计算资源上确保符合性，则实施第三方工具（如基于主机的自动数据丢失防护解决方案），以便对数据强制实施访问控制，即使数据从系统复制也是如此。

对于由 Microsoft 管理的基础平台，Microsoft 将所有客户数据视为敏感数据，并在很大程度上防范客户数据丢失和公开。 为了确保 Azure 中的客户数据保持安全，Microsoft 实施并维护了一套可靠的数据保护控制措施和功能。

- [了解 Azure 中的客户数据保护](../security/fundamentals/protection-customer-data.md)

**责任**：共享

**Azure 安全中心监视**：无

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8：静态加密敏感信息

**指导**：在所有 Azure 资源上使用静态加密。 如果在 azure 容器实例中使用基于云的专用注册表（如 Azure 容器注册表），则默认情况下，Azure 容器注册表中的所有数据都将使用 Microsoft 托管密钥进行静态加密。

- [了解 Azure 中的静态加密](../security/fundamentals/encryption-atrest.md)

- [Azure 容器注册表中客户托管的密钥](https://aka.ms/acr/cmk)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9：记录对关键 Azure 资源的更改并对此类更改发出警报

**指南**： Log Analytics 工作区提供了一个集中的位置，用于存储和查询仅来自 Azure 资源的日志数据，以及其他云中的本地资源和资源。 Azure 容器实例提供内置支持，支持将日志和事件数据发送到 Azure Monitor 日志。

- [使用 Azure Monitor 日志进行容器组和实例日志记录](container-instances-log-analytics.md)

**责任**：客户

**Azure 安全中心监视**：无

## <a name="vulnerability-management"></a>漏洞管理

[有关详细信息，请参阅 *Azure 安全基线：* 漏洞管理。](../security/benchmarks/security-control-vulnerability-management.md)

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1：运行自动漏洞扫描工具

**指南**：利用解决方案来扫描专用注册表中的容器映像，并识别潜在的漏洞。 了解不同解决方案提供的威胁检测的深度很重要。 遵循 Azure 安全中心的建议，在容器映像上执行漏洞评估。 （可选）从 Azure 市场部署第三方解决方案，用于执行映像漏洞评估。

- [Azure 容器实例的容器监视和扫描安全建议](container-instances-image-security.md)

- [Azure 容器注册表与安全中心的集成](/azure/security-center/azure-container-registry-integration)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2：部署自动操作系统修补管理解决方案

**指南**： Microsoft 在支持运行容器的底层系统上执行修补程序管理。

当检测到来自操作系统和其他修补程序的基础映像更新时，将自动执行容器映像更新。

- [关于 Azure 容器注册表任务的基础映像更新](../container-registry/container-registry-tasks-base-images.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5.3：为第三方软件部署自动修补程序管理解决方案

**指南**：可以使用第三方解决方案修补应用程序映像。 此外，如果使用基于云的专用注册表（如 Azure 容器注册表和 Azure 容器实例），则可以运行 Azure 容器注册表任务，以根据基本映像中的安全修补程序或其他更新自动更新容器注册表中的应用程序映像。

- [关于 ACR 任务的基础映像更新](../container-registry/container-registry-tasks-base-images.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4：比较连续进行的漏洞扫描

**指南**：如果使用基于云的专用注册表（如 azure 容器注册表和 Azure 容器实例），请将 azure 容器注册表 (ACR) 与 Azure 安全中心集成，以便定期扫描容器映像中的漏洞。 （可选）从 Azure 市场部署第三方解决方案，用于执行定期的映像漏洞扫描。

- [Azure 容器注册表与安全中心的集成](../security-center/defender-for-container-registries-introduction.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5：使用风险评级过程来确定已发现漏洞的修正措施的优先级

**指南**：如果使用基于云的专用注册表（如 azure 容器注册表和 Azure 容器实例），请将 Azure 容器注册表 (ACR) 与 Azure 安全中心集成，以便定期扫描容器映像是否存在漏洞并为风险分类。 （可选）从 Azure 市场部署第三方解决方案，用于执行定期的映像漏洞扫描和风险分类。

- [Azure 容器注册表与安全中心的集成](../security-center/defender-for-container-registries-introduction.md)

**责任**：客户

**Azure 安全中心监视**：无

## <a name="inventory-and-asset-management"></a>清单和资产管理

[有关详细信息，请参阅 *Azure 安全基线：* 清单和资产管理](../security/benchmarks/security-control-inventory-asset-management.md)。

### <a name="61-use-automated-asset-discovery-solution"></a>6.1：使用自动化资产发现解决方案

**指导**：使用 Azure Resource Graph 查询/发现订阅中的所有资源（例如计算、存储、网络、端口和协议等）。 确保租户中具有适当的（读取）权限，并枚举所有 Azure 订阅以及订阅中的资源。

尽管可以通过 Resource Graph 发现经典 Azure 资源，但我们强烈建议你今后还是创建并使用 Azure 资源管理器资源。

- [如何使用 Azure Resource Graph 创建查询](../governance/resource-graph/first-query-portal.md)

- [如何查看 Azure 订阅](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-4.8.0&amp;preserve-view=true)

- [了解 Azure RBAC](../role-based-access-control/overview.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="62-maintain-asset-metadata"></a>6.2：维护资产元数据

**指南**：如果使用基于云的专用注册表（如 Azure 容器注册表） (ACR) 与 Azure 容器实例一起使用，则 acr 将保留元数据，包括注册表中的图像的标记和清单。 请遵循用于标记项目的建议做法。

- [关于注册表、存储库和映像](../container-registry/container-registry-concepts.md)

- [有关对容器映像进行标记和版本控制的建议](../container-registry/container-registry-image-tag-version.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="63-delete-unauthorized-azure-resources"></a>6.3：删除未经授权的 Azure 资源

**指南**：如果使用基于云的专用注册表（如 Azure 容器注册表） (ACR) 与 Azure 容器实例一起使用，则 acr 将保留元数据，包括注册表中的图像的标记和清单。 请遵循用于标记项目的建议做法。

- [关于注册表、存储库和映像](../container-registry/container-registry-concepts.md)

- [有关对容器映像进行标记和版本控制的建议](../container-registry/container-registry-image-tag-version.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4：定义并维护已批准的 Azure 资源的清单

**指导**：你需要根据组织需求创建已批准的 Azure 资源的清单。

**责任**：客户

**Azure 安全中心监视**：无

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5：监视未批准的 Azure 资源

**指导**：使用 Azure Policy 对可以在订阅中创建的资源类型施加限制。

使用 Azure Resource Graph 查询/发现订阅中的资源。 确保环境中的所有 Azure 资源均已获得批准。

- [使用 Azure Policy 审核 Azure 容器注册表的合规性](../container-registry/container-registry-azure-policy.md)

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [如何使用 Azure Graph 创建查询](../governance/resource-graph/first-query-portal.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6：监视计算资源中未批准的软件应用程序

**指南**：如果使用基于云的专用注册表（如 Azure 容器注册表） (ACR) 与 Azure 容器实例一起使用，请分析和监视 Azure 容器注册表日志中的异常行为，并定期检查结果。 使用 Azure Monitor 的 Log Analytics 工作区来查看日志并执行针对日志数据的查询。

- [用于诊断评估和审核的 Azure 容器注册表日志](../container-registry/container-registry-diagnostics-audit-logs.md)

- [了解 Log Analytics 工作区](/azure/azure-monitor/log-query/log-analytics-tutorial)

- [如何在 Azure Monitor 中执行自定义查询](/azure/azure-monitor/log-query/get-started-queries)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7：删除未批准的 Azure 资源和软件应用程序

**指导**：可以通过 Azure 自动化在工作负荷和资源的部署、操作和解除授权过程中进行完全的控制。 你可以实施自己的解决方案来删除未经授权的 Azure 资源。 

- [Azure 自动化简介](../automation/automation-intro.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="68-use-only-approved-applications"></a>6.8：仅使用已批准的应用程序

**指导**：不适用。 基准设计用于计算资源。

**责任**：客户

**Azure 安全中心监视**：无

### <a name="69-use-only-approved-azure-services"></a>6.9：仅使用已批准的 Azure 服务

**指导**：利用 Azure Policy 限制可在环境中预配的服务。

- [使用 Azure Policy 审核 Azure 容器注册表的合规性](../container-registry/container-registry-azure-policy.md)

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [如何使用 Azure Policy 拒绝特定的资源类型](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10：维护已获批软件的清单

**指导**：不适用。 基准设计用于计算资源。

**责任**：客户

**Azure 安全中心监视**：无

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11：限制用户与 Azure 资源管理器进行交互的能力

**指导**：使用特定于操作系统的配置或第三方资源来限制用户在 Azure 计算资源中执行脚本的能力。

- [如何配置条件访问来阻止对 Azure 资源管理器的访问](../role-based-access-control/conditional-access-azure-management.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12：限制用户在计算资源中执行脚本的功能

**指导**：使用特定于操作系统的配置或第三方资源来限制用户在 Azure 计算资源中执行脚本的能力。

- [例如，如何在 Windows 环境中控制 PowerShell 脚本执行](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-7&amp;preserve-view=true)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13：以物理或逻辑方式隔离高风险应用程序

**指导**：业务运营所需的软件可能会给组织带来更高的风险，应将其隔离在自己的虚拟机和/或虚拟网络中，并通过 Azure 防火墙或网络安全组进行充分的保护。

- [如何创建虚拟网络](../virtual-network/quick-create-portal.md)

- [如何创建采用安全配置的 NSG](../virtual-network/tutorial-filter-network-traffic.md)

**责任**：客户

**Azure 安全中心监视**：无

## <a name="secure-configuration"></a>安全配置

[有关详细信息，请参阅 *Azure 安全基线：* 安全配置](../security/benchmarks/security-control-secure-configuration.md)。

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1：为所有 Azure 资源建立安全配置

**指导**：使用 Azure Policy 或 Azure 安全中心来维护所有 Azure 资源的安全配置。

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [使用 Azure Policy 审核 Azure 容器注册表的合规性](../container-registry/container-registry-azure-policy.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="72-establish-secure-operating-system-configurations"></a>7.2：建立安全的操作系统配置

**指导**：利用 Azure 安全中心建议“修复虚拟机上安全配置中的漏洞”，维护所有计算资源上的安全配置。

- [如何监视 Azure 安全中心建议](../security-center/security-center-recommendations.md)

- [如何修正 Azure 安全中心建议](../security-center/security-center-remediate-recommendations.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3：维护安全的 Azure 资源配置

**指南**：使用 azure 策略 [拒绝] 和 [部署（如果不存在）影响跨 Azure 资源强制实施安全设置。

如果使用基于云的专用注册表（如 Azure 容器注册表） (ACR) 与 Azure 容器实例一起使用，请使用 Azure 策略审核 Azure 容器注册表的符合性：。

- [使用 Azure Policy 审核 Azure 容器注册表的合规性](../container-registry/container-registry-azure-policy.md)

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [了解 Azure Policy 效果](../governance/policy/concepts/effects.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4：维护安全的操作系统配置

**指南**：不适用;此控件用于计算资源。

**责任**：共享

**Azure 安全中心监视**：无

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5：安全存储 Azure 资源的配置

**指南**：如果使用自定义 Azure 策略定义，请使用 Azure Repos 安全地存储和管理你的代码。

- [如何在 Azure DevOps 中存储代码](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops&amp;preserve-view=true)

- [Azure Repos 文档](https://docs.microsoft.com/azure/devops/repos/?view=azure-devops&amp;preserve-view=true)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="76-securely-store-custom-operating-system-images"></a>7.6：安全存储自定义操作系统映像

**指南**：不适用;此控件仅适用于计算资源。

**责任**：客户

**Azure 安全中心监视**：无

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7：部署 Azure 资源的配置管理工具

**指导**：使用 Azure 策略针对系统配置发出警报，以及审核和强制实施系统配置。 另外，开发一个用于管理策略例外的流程和管道。

如果使用基于云的专用注册表（如 Azure 容器注册表） (ACR) 与 Azure 容器实例一起使用，请使用 Azure 策略审核 Azure 容器注册表的符合性：。

- [使用 Azure Policy 审核 Azure 容器注册表的合规性](../container-registry/container-registry-azure-policy.md)

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [了解 Azure Policy 效果](../governance/policy/concepts/effects.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8：部署操作系统的配置管理工具

**指导**：不适用。 基准适用于计算资源。

**责任**：客户

**Azure 安全中心监视**：无

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9：为 Azure 资源实施自动配置监视

**指导**：使用 Azure 安全中心对 Azure 资源执行基线扫描。 

应用 Azure 策略，对可在订阅中创建的资源类型施加限制。

如果使用基于云的专用注册表（如 Azure 容器注册表） (ACR) 与 Azure 容器实例一起使用，请使用 Azure 策略审核 Azure 容器注册表的符合性：。

- [如何在 Azure 安全中心修正建议](../security-center/security-center-remediate-recommendations.md)

- [使用 Azure Policy 审核 Azure 容器注册表的合规性](../container-registry/container-registry-azure-policy.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10：为操作系统实施自动配置监视

**指导**：使用 Azure 安全中心对 OS 和容器的 Docker 设置执行基线扫描。 

- [了解 Azure 安全中心容器建议](../security-center/container-security.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="711-manage-azure-secrets-securely"></a>7.11：安全管理 Azure 机密

**指导**：将托管服务标识与 Azure Key Vault 结合使用，以便简化和保护云应用程序的机密管理。

- [如何与 Azure 托管标识集成](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [如何创建 Key Vault](../key-vault/secrets/quick-create-portal.md)

- [如何向 Key Vault 进行身份验证](container-instances-managed-identity.md)

- [如何分配 Key Vault 访问策略](../key-vault/general/assign-access-policy-portal.md)

- [如何将托管标识与 Azure 容器实例结合使用](../container-registry/container-registry-tasks-authentication-managed-identity.md)

- [如何在容器实例中加密数据](container-instances-encrypt-data.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="712-manage-identities-securely-and-automatically"></a>7.12：安全自动管理标识

**指导**：使用托管标识在 Azure Active Directory (Azure AD) 中为 Azure 服务提供一个自动托管的标识。 通过托管标识，你可以对任何支持 Azure AD 身份验证的服务进行身份验证，包括 Azure Key Vault，而无需在代码中包含任何凭据。

如果使用基于云的专用注册表（如 Azure 容器注册表） (ACR) 与 Azure 容器实例一起使用，请使用 Azure 策略审核 Azure 容器注册表的符合性：。

- [使用 Azure Policy 审核 Azure 容器注册表的合规性](../container-registry/container-registry-azure-policy.md)

- [如何配置托管标识](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

- [如何将托管标识与 Azure 容器实例结合使用](container-instances-managed-identity.md)

- [使用 Azure 托管标识向 Azure 容器注册表验证身份](../container-registry/container-registry-authentication-managed-identity.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13：消除意外的凭据透露

**指南**：实施凭据扫描程序来识别代码中的凭据。 凭据扫描程序还会建议将发现的凭据转移到更安全的位置，例如 Azure Key Vault。

- [如何设置凭据扫描程序](https://secdevtools.azurewebsites.net/helpcredscan.html)

**责任**：客户

**Azure 安全中心监视**：无

## <a name="malware-defense"></a>恶意软件防护

[有关详细信息，请参阅 *Azure 安全基线：* 恶意软件防护](../security/benchmarks/security-control-malware-defense.md)。

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1：使用集中管理的反恶意软件

**指导**：使用适用于 Azure 云服务和虚拟机的 Microsoft Antimalware 来持续监视和保护资源。 对于 Linux，请使用第三方反恶意软件解决方案。

- [如何为云服务和虚拟机配置 Microsoft Antimalware](../security/fundamentals/antimalware.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2：预先扫描要上传到非计算 Azure 资源的文件

**指南**：在支持 azure 服务的基础主机上启用了 Microsoft 反恶意软件 (例如，Azure 容器实例) ，但是不会在客户数据上运行。

预扫描任何上传到非计算 Azure 资源（例如应用服务、Data Lake Storage、Blob 存储等）的文件。

**责任**：客户

**Azure 安全中心监视**：无

## <a name="data-recovery"></a>数据恢复

[有关详细信息，请参阅 *Azure 安全基线：* 数据恢复](../security/benchmarks/security-control-data-recovery.md)。

### <a name="91-ensure-regular-automated-back-ups"></a>9.1：确保定期执行自动备份

**指南**：如果使用基于云的专用注册表（如 Azure 容器注册表） (ACR) 与 Azure 容器实例一起使用，则将始终自动复制 Microsoft Azure 容器注册表中的数据以确保持久性和高可用性。 Azure 容器注册表将复制你的数据，使其免受计划内和计划外事件的保护。

（可选）异地复制容器注册表，以在多个 Azure 区域中维护注册表副本。

- [Azure 容器注册表中的异地复制](../container-registry/container-registry-geo-replication.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2：执行完整系统备份，并备份客户管理的所有密钥

**指导**：（可选）通过将容器映像从一个注册表导入到另一个注册表来备份它们。

使用 Azure 命令行工具或 SDK 在 Azure Key Vault 中备份客户管理的密钥。

- [将容器映像导入到容器注册表](../container-registry/container-registry-import-images.md)

- [如何在 Azure 中备份密钥保管库密钥](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey?view=azps-4.8.0&amp;preserve-view=true)

- [用容器实例加密部署数据](container-instances-encrypt-data.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3：验证所有备份，包括客户管理的密钥

**指南**：使用 Azure 命令行工具或 sdk 在 Azure Key Vault 中测试已备份客户托管密钥的还原。

- [如何在 Azure 中还原 Azure Key Vault 密钥](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultkey?view=azps-4.8.0&amp;preserve-view=true)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4：确保保护备份和客户管理的密钥

**指导**：你可以在 Azure Key Vault 中启用“软删除”，以防止意外删除或恶意删除密钥。

- [如何在 Key Vault 中启用“软删除”](../storage/blobs/soft-delete-blob-overview.md)

**责任**：客户

**Azure 安全中心监视**：无

## <a name="incident-response"></a>事件响应

[有关详细信息，请参阅 *Azure 安全基线：* 事件响应](../security/benchmarks/security-control-incident-response.md)。

### <a name="101-create-an-incident-response-guide"></a>10.1：创建事件响应指导

**指南**：为组织制定事件响应指南。 确保在书面的事件响应计划中定义人员职责，以及事件处理/管理从检测到事件后审查的各个阶段。

- [如何在 Azure 安全中心配置工作流自动化](../security-center/security-center-planning-and-operations-guide.md)

- [关于建立自己的安全事件响应流程的指南](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft 安全响应中心事件分析](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [NIST 的计算机安全事件处理指南](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2：创建事件评分和优先级设定过程

**指导**：Azure 安全中心为每条警报分配严重性，方便你根据优先级来确定应该最先调查的警报。 严重性取决于安全中心在发出警报时所依据的检测结果和分析结果的置信度，以及导致发出警报的活动的恶意企图的置信度。

此外，使用标记来标记订阅，并创建命名系统来对 Azure 资源进行标识和分类，特别是处理敏感数据的资源。  你的责任是根据发生事件的 Azure 资源和环境的关键性确定修正警报的优先级。 

- [Azure 安全中心中的安全警报](../security-center/security-center-alerts-overview.md) 

- [使用标记整理 Azure 资源](/azure/azure-resource-manager/resource-group-using-tags)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="103-test-security-response-procedures"></a>10.3：测试安全响应过程

**指导**：定期执行演练来测试系统的事件响应功能。 识别弱点和差距，并根据需要修改计划。

- [NIST 发布 - IT 计划和功能的测试、训练和演练计划指南](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4：提供安全事件联系人详细信息，并针对安全事件配置警报通知

**指南**：如果 Microsoft 安全响应中心 (MSRC) 发现非法或未经授权的某方访问了客户的数据，Microsoft 将使用安全事件联系人信息与你取得联系。 事后审查事件，确保问题得到解决。

- [如何设置 Azure 安全中心安全联系人](../security-center/security-center-provide-security-contact-details.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5：将安全警报整合到事件响应系统中

**指导**：使用连续导出功能导出 Azure 安全中心警报和建议。 使用连续导出可以手动导出或者持续导出警报和建议。 可以使用 Azure 安全中心数据连接器将警报流式传输到 Azure Sentinel。

- [如何配置连续导出](../security-center/continuous-export.md)

- [如何将警报流式传输到 Azure Sentinel](../sentinel/connect-azure-security-center.md)

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
