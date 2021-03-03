---
title: 适用于 Redis 的 Azure 缓存的 azure 安全基线
description: Azure Cache for Redis 安全基线为实现 Azure 安全基准中指定的安全建议提供过程指南和资源。
author: msmbaldwin
ms.service: cache
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 516a66ef64512cbe4fa4c3589e651c14ddf18550
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101647619"
---
# <a name="azure-security-baseline-for-azure-cache-for-redis"></a>适用于 Redis 的 Azure 缓存的 azure 安全基线

此安全基线将 [Azure 安全性基准1.0 版](../security/benchmarks/overview-v1.md) 中的指南应用于 Redis 的 azure Cache。 Azure 安全基准提供有关如何在 Azure 上保护云解决方案的建议。
内容按照 Azure 安全基准定义的 **安全控件** 进行分组，适用于适用于 Redis 的 azure 缓存的相关指南。 排除了不适用于 Redis 的 Azure 缓存的 **控件**。

 
若要查看 Redis 的 Azure 缓存如何完全映射到 Azure 安全基准，请参阅 [完整的 Azure cache For Redis Security 基准映射文件](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)。

## <a name="network-security"></a>网络安全

[有关详细信息，请参阅 *Azure 安全基线：* 网络安全性](../security/benchmarks/security-control-network-security.md)。

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1：保护虚拟网络中的 Azure 资源

**指南**：在虚拟网络 (VNet) 中部署 Azure Cache for Redis 实例。 VNet 是云中的专用网络。 为 Azure Redis 缓存实例配置了 VNet 后，该实例不可公开寻址，而只能从 VNet 中的虚拟机和应用程序进行访问。

你还可以使用开始和结束 IP 地址范围指定防火墙规则。 配置防火墙规则时，仅指定 IP 地址范围内的客户端连接可以连接到缓存。

- [如何为高级 Azure Redis 缓存配置虚拟网络支持](cache-how-to-premium-vnet.md)

- [如何为 Redis 防火墙规则配置 Azure 缓存](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-configure#firewall)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2：监视并记录虚拟网络、子网和网络接口的配置与流量

**指南**：当虚拟机部署在与 Azure Cache for Redis 实例相同的虚拟网络中时，可以使用网络安全组 (NSG) 以降低数据渗透的风险。 启用 NSG 流日志，并将日志发送到 Azure 存储帐户以进行流量审核。 还可以将 NSG 流日志发送到 Log Analytics 工作区，并使用流量分析来提供对 Azure 云中的流量流的见解。 流量分析的优势包括能够可视化网络活动、识别热点、识别安全威胁、了解流量流模式，以及查明网络不当配置。

- [如何启用 NSG 流日志](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [如何启用和使用流量分析](../network-watcher/traffic-analytics.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="13-protect-critical-web-applications"></a>1.3：保护关键 Web 应用程序

**指导**：不适用；此建议适用于 Azure 应用服务或计算资源上运行的 Web 应用程序。

**责任**：客户

**Azure 安全中心监视**：无

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4：拒绝与已知恶意的 IP 地址进行通信

**指南**： Azure 虚拟网络 (VNet) 部署为适用于 Redis 的 azure 缓存提供增强的安全性和隔离性，并提供子网、访问控制策略和其他功能，以进一步限制访问。 在 VNet 中部署时，适用于 Redis 的 Azure 缓存不能公开寻址，只能从 VNet 中的虚拟机和应用程序进行访问。

为 Redis 实例的 Azure 缓存相关联的 Vnet 启用 DDoS 保护标准，以防范分布式拒绝服务 (DDoS) 攻击。 根据 Azure 安全中心集成的威胁情报进行判断，拒绝与已知恶意的或未使用过的 Internet IP 地址通信。

- [如何为高级 Azure Redis 缓存配置虚拟网络支持](cache-how-to-premium-vnet.md)

- [使用 Azure 门户管理 Azure DDoS 防护标准](/azure/virtual-network/manage-ddos-protection)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="15-record-network-packets"></a>1.5：记录网络数据包

**指南**：当虚拟机部署在与 Azure Cache for Redis 实例相同的虚拟网络中时，可以使用网络安全组 (NSG) 以降低数据渗透的风险。 启用 NSG 流日志，并将日志发送到 Azure 存储帐户以进行流量审核。 还可以将 NSG 流日志发送到 Log Analytics 工作区，并使用流量分析来提供对 Azure 云中的流量流的见解。 流量分析的优势包括能够可视化网络活动、识别热点、识别安全威胁、了解流量流模式，以及查明网络不当配置。

- [如何启用 NSG 流日志](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [如何启用和使用流量分析](../network-watcher/traffic-analytics.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6：部署基于网络的入侵检测/入侵防护系统 (IDS/IPS)

**指南**：将适用于 Redis 的 azure Cache 用于在 Azure App Service 或计算实例上运行的 web 应用程序时，将 Azure 虚拟网络中的所有资源部署 (VNet) ，并使用 Azure Web 应用程序防火墙 (WAF) 在 Web 应用程序网关上使用。 将 WAF 配置为在 "保护模式" 下运行。 防护模式阻止规则检测到的入侵和攻击。 攻击者会收到“403 未授权访问”异常，且连接会结束。 阻止模式会在 WAF 日志中记录此类攻击。

或者，你可以从 Azure Marketplace 中选择一个产品/服务，其中支持具有负载检查和/或异常检测功能的 ID/IPS 功能。

- [了解 Azure WAF 功能](../web-application-firewall/ag/ag-overview.md)

- [如何部署 Azure WAF](../web-application-firewall/ag/create-waf-policy-ag.md)

- [Azure 市场](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="17-manage-traffic-to-web-applications"></a>1.7：管理发往 Web 应用程序的流量

**指导**：不适用；此建议适用于 Azure 应用服务或计算资源上运行的 Web 应用程序。

**责任**：客户

**Azure 安全中心监视**：无

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8：最大程度地降低网络安全规则的复杂性和管理开销

**指导**：在网络安全组 (NSG) 或 Azure 防火墙中使用虚拟网络服务标记来定义网络访问控制。 创建安全规则时，可以使用服务标记代替特定的 IP 地址。 在规则的相应源或目标字段中指定服务标记名称（例如 ApiManagement），可以允许或拒绝相应服务的流量。 Microsoft 会管理服务标记包含的地址前缀，并会在地址发生更改时自动更新服务标记。

还可使用应用程序安全组 (ASG) 来帮助简化复杂的安全配置。 借助 ASG，可将网络安全性配置为应用程序结构的固有扩展，从而可基于这些组对虚拟机进行分组并定义网络安全策略。

- [虚拟网络服务标记](../virtual-network/service-tags-overview.md)

- [应用程序安全组](/azure/virtual-network/security-overview#application-security-groups)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9：维护网络设备的标准安全配置

**指南**：定义和实现与 azure Cache for Redis 实例和 azure 策略相关的网络资源的标准安全配置。 使用 "Microsoft Cache" 和 "Microsoft 网络" 命名空间中的 Azure 策略别名创建自定义策略，以便为 Redis 实例审核或强制执行 Azure 缓存的网络配置。 还可以利用内置策略定义，例如：
- 应该启用只能通过安全方式连接到 Redis 缓存

- 应启用 DDoS 防护标准版

还可以使用 Azure 蓝图，通过在单个蓝图定义中打包关键环境项目（例如 Azure 资源管理器模板、Azure 基于角色的访问控制 (Azure RBAC) 和策略）来简化大规模的 Azure 部署。 轻松将蓝图应用到新的订阅和环境，并通过版本控制来微调控制措施和管理。

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [如何创建 Azure 蓝图](../governance/blueprints/create-blueprint-portal.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="110-document-traffic-configuration-rules"></a>1.10：阐述流量配置规则

**指南**：对与用于 Redis 部署的 Azure 缓存相关联的网络资源使用标记，以便以逻辑方式将它们组织到分类。

- [如何创建和使用标记](/azure/azure-resource-manager/resource-group-using-tags)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11：使用自动化工具来监视网络资源配置和检测更改

**指南**：使用 azure 活动日志监视网络资源配置，并检测与 Azure Cache for Redis 实例相关的网络资源的更改。 在 Azure Monitor 中创建当关键网络资源发生更改时触发的警报。

- [如何查看和检索 Azure 活动日志事件](/azure/azure-monitor/platform/activity-log-view)

- [如何在 Azure Monitor 中创建警报](/azure/azure-monitor/platform/alerts-activity-log)

**责任**：客户

**Azure 安全中心监视**：无

## <a name="logging-and-monitoring"></a>日志记录和监视

[有关详细信息，请参阅 *Azure 安全基线：* 日志记录和监视](../security/benchmarks/security-control-logging-monitoring.md)。

### <a name="22-configure-central-security-log-management"></a>2.2：配置中心安全日志管理

**指南**：启用 Azure 活动日志诊断设置，并将日志发送到 Log Aalytics 工作区、Azure 事件中心或 Azure 存储帐户进行存档。 活动日志可让你深入了解在 Azure Cache for Redis 实例在控制平面级别上执行的操作。 通过 Azure 活动日志数据，可确定在控制平面级别针对 Azure Cache for Redis 实例执行的任何写入操作（PUT、POST、DELETE）的“操作内容、操作人员和操作时间”。

- [如何启用 Azure 活动日志的诊断设置](/azure/azure-monitor/platform/diagnostic-settings-legacy)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：为 Azure 资源启用审核日志记录

**指南**：启用 Azure 活动日志诊断设置，并将日志发送到 Log Aalytics 工作区、Azure 事件中心或 Azure 存储帐户进行存档。 活动日志可让你深入了解在 Azure Cache for Redis 实例在控制平面级别上执行的操作。 通过 Azure 活动日志数据，可确定在控制平面级别针对 Azure Cache for Redis 实例执行的任何写入操作（PUT、POST、DELETE）的“操作内容、操作人员和操作时间”。

尽管可通过启用诊断设置来使用度量值，但对于 Redis 的 Azure 缓存，数据平面上的审核日志记录不可用。

- [如何启用 Azure 活动日志的诊断设置](/azure/azure-monitor/platform/diagnostic-settings-legacy)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="25-configure-security-log-storage-retention"></a>2.5：配置安全日志存储保留期

**指南**：在 Azure Monitor 中，根据组织的符合性规定，为与 Azure Cache for Redis 实例关联的 Log Analytics 工作区设置日志保持期。

请注意，数据平面上的审核日志功能尚不可用于 Redis 的 Azure Cache。

- [如何设置日志保留参数](/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="26-monitor-and-review-logs"></a>2.6：监视和查看日志

**指导**：启用 Azure 活动日志诊断设置，并将日志发送到 Log Analytics 工作区。 在 Log Analytics 中执行查询，以搜索术语，确定趋势，分析模式，并根据可能已为 Redis 的 Azure 缓存收集的活动日志数据提供许多其他见解。

请注意，数据平面上的审核日志功能尚不可用于 Redis 的 Azure Cache。

- [如何启用 Azure 活动日志的诊断设置](/azure/azure-monitor/platform/diagnostic-settings-legacy)

- [如何收集和分析 Azure Monitor 的 Log Analytics 工作区中的 Azure 活动日志](/azure/azure-monitor/platform/activity-log-collect)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7：针对异常活动启用警报

**指南**：可以将配置为根据与 Azure Cache for Redis 实例相关的指标和活动日志接收警报。 通过使用 Azure Monitor，你可以配置警报以发送电子邮件通知、调用 Webhook 或调用 Azure 逻辑应用。

尽管可通过启用诊断设置来使用度量值，但对于 Redis 的 Azure 缓存，数据平面上的审核日志记录不可用。

- [如何为 Redis 的 Azure 缓存配置警报](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-monitor#alerts)

**责任**：客户

**Azure 安全中心监视**：无

## <a name="identity-and-access-control"></a>标识和访问控制

[有关详细信息，请参阅 *Azure 安全基线：* 标识和访问控制](../security/benchmarks/security-control-identity-access-control.md)。

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1：维护管理帐户的清单

**指导**：Azure Active Directory (Azure AD) 具有必须显式分配且可查询的内置角色。 使用 Azure AD PowerShell 模块执行即席查询，以发现属于管理组成员的帐户。

- [如何使用 PowerShell 获取 Azure AD 中的目录角色](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0&amp;preserve-view=true)

- [如何使用 PowerShell 获取 Azure AD 中目录角色的成员](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0&amp;preserve-view=true)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="32-change-default-passwords-where-applicable"></a>3.2：在适用的情况下更改默认密码

**指南**：通过 Azure Active Directory (Azure AD) 控制对用于 Redis 的 Azure 缓存的控制平面访问。 Azure AD 没有默认密码。

通过访问密钥控制对 Redis 的 Azure Cache 的数据平面访问。 这些密钥由连接到缓存的客户端使用，可随时重新生成。

建议不要将默认密码构建到应用程序中。 相反，你可以将你的密码存储在 Azure Key Vault 中，然后使用 Azure AD 来检索它们。

- [如何重新生成 Azure Cache for Redis 访问密钥](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-configure#settings)

**责任**：共享

**Azure 安全中心监视**：无

### <a name="33-use-dedicated-administrative-accounts"></a>3.3：使用专用管理帐户

**指南**：围绕专用管理帐户的使用创建标准操作程序。 使用 Azure 安全中心标识和访问管理来监视管理帐户的数量。

此外，为了帮助你跟踪专用管理帐户，你可以使用 Azure 安全中心或内置的 Azure 策略提供的建议，例如：

- 应该为你的订阅分配了多个所有者

- 应从订阅中删除拥有所有者权限的已弃用帐户

- 应从订阅中删除拥有所有者权限的外部帐户

有关详细信息，请参阅以下资源：

- [如何使用 Azure 安全中心监视标识和访问（预览）](../security-center/security-center-identity-access.md)

- [如何使用 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4：使用 Azure Active Directory 单一登录 (SSO)

**指南**：用于 Redis 的 Azure 缓存使用访问密钥对用户进行身份验证，并且不支持数据平面级别的单一登录 (SSO) 。 可以通过 REST API 获取用于 Redis 的 Azure 缓存的控制平面，并支持 SSO。 若要进行身份验证，请将请求的授权标头设置为从 Azure Active Directory (Azure AD) 获取的 JSON Web 令牌。

- [了解适用于 Redis 的 Azure 缓存 REST API](/rest/api/redis/)

- [了解 Azure AD 的 SSO](../active-directory/manage-apps/what-is-single-sign-on.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5：对所有基于 Azure Active Directory 的访问使用多重身份验证

**指南**：启用 Azure Active Directory (Azure AD) 多重身份验证，并遵循 Azure 安全中心的标识和访问管理建议。

- [如何在 Azure 中启用多重身份验证](../active-directory/authentication/howto-mfa-getstarted.md)

- [如何在 Azure 安全中心监视标识和访问](../security-center/security-center-identity-access.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6：对所有管理任务使用专用计算机（特权访问工作站）

**指南**：通过配置为登录和配置 Azure 资源的多重身份验证，使用特权访问工作站 (PAW) 。 

- [了解特权访问工作站](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [如何在 Azure 中启用多重身份验证](../active-directory/authentication/howto-mfa-getstarted.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7：记录来自管理帐户的可疑活动并对其发出警报

**指导**：当环境中出现可疑或不安全的活动时，可使用 Azure Active Directory (Azure AD) Privileged Identity Management (PIM) 生成日志和警报。

此外，还可使用 Azure AD 风险检测来查看警报和报告有风险的用户行为。

- [如何部署 Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [了解 Azure AD 风险检测](/azure/active-directory/reports-monitoring/concept-risk-events)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8：仅从批准的位置管理 Azure 资源

**指南**：在 Azure Active Directory (中配置命名位置 Azure AD) 条件访问，以便仅允许从 IP 地址范围或国家/地区的特定逻辑分组访问。

- [如何在 Azure 中配置命名位置](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="39-use-azure-active-directory"></a>3.9：使用 Azure Active Directory

**指导**：使用 Azure Active Directory (Azure AD) 作为中心身份验证和授权系统。 Azure AD 通过对静态数据和传输中数据使用强加密来保护数据。 Azure AD 还会对用户凭据进行加盐、哈希处理和安全存储操作。

Azure AD 身份验证不能用于直接访问 Redis "数据平面的 Azure 缓存，但 Azure AD 凭据可用于在控制平面级别进行管理 (即，Azure 门户) 控制 Redis 访问密钥的 Azure 缓存。

**责任**：客户

**Azure 安全中心监视**：无

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10：定期审查和协调用户访问

**指导**：Azure Active Directory (Azure AD) 提供了日志来帮助你发现过时的帐户。 此外，请使用 Azure 标识访问评审来有效管理组成员身份、对企业应用程序的访问和角色分配。 可以定期评审用户的访问权限，确保只有适当的用户才持续拥有访问权限。

- [了解 Azure AD 报告](/azure/active-directory/reports-monitoring/)

- [如何使用 Azure 标识访问评审](../active-directory/governance/access-reviews-overview.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11：监视尝试访问已停用凭据的行为

**指南**：你有权访问 Azure Active Directory (Azure AD) 登录活动、审核和风险事件日志源，这允许你与 Azure Sentinel 或第三方 SIEM 集成。

可以通过为 Azure AD 用户帐户创建诊断设置，并将审核日志和登录日志发送到 Log Analytics 工作区，来简化此过程。 可以在 Log Analytics 中配置所需的日志警报。

- [如何将 Azure 活动日志集成到 Azure Monitor](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

- [如何加入 Azure Sentinel](../sentinel/quickstart-onboard.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12：针对帐户登录行为偏差发出警报

**指南**：对于控制平面上的帐户登录行为偏差，使用 Azure Active Directory (Azure AD) 的标识保护和风险检测功能，以配置对检测到的与用户标识相关的可疑操作的自动响应。 还可以将数据引入 Azure Sentinel 中以便进一步调查。

- [如何查看 Azure AD 风险登录](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [如何配置和启用标识保护风险策略](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [如何加入 Azure Sentinel](../sentinel/quickstart-onboard.md)

**责任**：客户

**Azure 安全中心监视**：无

## <a name="data-protection"></a>数据保护

[有关详细信息，请参阅 *Azure 安全基线：* 数据保护](../security/benchmarks/security-control-data-protection.md)。

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1：维护敏感信息的清单

**指导**：使用标记可以帮助跟踪存储或处理敏感信息的 Azure 资源。

- [如何创建和使用标记](/azure/azure-resource-manager/resource-group-using-tags)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2：隔离存储或处理敏感信息的系统

**指导**：为开发、测试和生产实现单独的订阅和/或管理组。 Azure Cache for Redis 实例应由虚拟网络/子网分隔并正确标记。 （可选）使用适用于 Redis 防火墙的 Azure Cache 定义规则，以便只有来自指定 IP 地址范围的客户端连接可以连接到缓存。

- [如何创建其他 Azure 订阅](/azure/billing/billing-create-subscription)

- [如何创建管理组](/azure/governance/management-groups/create)

- [如何将用于 Redis 的 Azure 缓存部署到 Vnet](cache-how-to-premium-vnet.md)

- [如何为 Redis 防火墙规则配置 Azure 缓存](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-configure#firewall)

- [如何创建和使用标记](/azure/azure-resource-manager/resource-group-using-tags)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3：监视和阻止未经授权的敏感信息传输

**指南**：尚不可用;对于 Redis 的 Azure 缓存，数据识别、分类和丢失防护功能尚不可用。

Microsoft 为 Redis 管理 Azure 缓存的底层基础结构，并实施了严格控制来防止客户数据丢失或泄露。

- [了解 Azure 中的客户数据保护](../security/fundamentals/protection-customer-data.md)

**责任**：共享

**Azure 安全中心监视**：无

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4：加密传输中的所有敏感信息

**指南**：默认情况下，用于 Redis 的 Azure 缓存要求使用 TLS 加密通信。 目前支持 TLS 版本 1.0、1.1 和 1.2。 但是，TLS 1.0 和 TLS 1.1 即将在全行业范围内弃用，因此，请尽可能使用 TLS 1.2。 如果客户端库或工具不支持 TLS，则可以通过 Azure 门户或管理 API 来启用未加密的连接。 在无法进行加密连接的情况下，建议将缓存和客户端应用程序放入虚拟网络中。

- [了解 Azure Cache for Redis 传输中的加密](cache-best-practices.md)

- [了解 Vnet 缓存方案中使用的所需端口](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-premium-vnet#outbound-port-requirements)

**责任**：共享

**Azure 安全中心监视**： [azure 安全基准](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) 是安全中心的默认策略计划，是 [安全中心建议](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md)的基础。 安全中心会自动启用与此控件相关的 Azure 策略定义。 与此控件相关的警报可能需要 [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) 计划相关服务。

**Azure 策略内置定义-Microsoft. 缓存**：

[!INCLUDE [Resource Policy for Microsoft.Cache 4.4](../../includes/policy/standards/asb/rp-controls/microsoft.cache-4-4.md)]

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5：使用有效的发现工具识别敏感数据

**指南**：对于 Redis 的 Azure 缓存，数据标识、分类和损失防护功能尚不可用。 同样地，标记包含敏感信息的实例；如果出于合规性目的需要，请实施第三方解决方案。

对于 Microsoft 管理的底层平台，Microsoft 会将所有客户内容视为敏感数据，并会全方位地防范客户数据丢失和遭到透露。 为了确保 Azure 中的客户数据保持安全，Microsoft 实施并维护了一套可靠的数据保护控制措施和功能。

- [了解 Azure 中的客户数据保护](../security/fundamentals/protection-customer-data.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6：使用基于角色的访问控制来控制对资源的访问

**指南**：使用 azure RBAC)  (azure 基于角色的访问控制来控制对 azure Cache for Redis 控制平面的访问 (即 Azure 门户) 。

- [如何配置 Azure RBAC](../role-based-access-control/role-assignments-portal.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8：静态加密敏感信息

**指南**：用于 Redis 的 Azure 缓存将客户数据存储在内存中，并由 Microsoft 实现的许多控件进行强保护，默认情况下不加密内存。 如果你的组织需要，请在将内容存储到用于 Redis 的 Azure 缓存之前对内容进行加密。

如果使用 Azure Cache for Redis 功能 "Redis 数据暂留"，则会将数据发送到你拥有和管理的 Azure 存储帐户。 你可以在创建缓存期间，在现有高级缓存的 "资源" 菜单中，配置 "用于 Redis 的新 Azure 缓存" 边栏选项卡。

Azure 存储中的数据将使用 256 位 AES 加密法（可用的最强大块加密法之一）以透明方式进行加密和解密，并符合 FIPS 140-2 规范。 无法禁用 Azure 存储加密。 可以依赖于使用 Microsoft 托管的密钥来加密存储帐户，或者，可以使用自己的密钥来管理加密。

- [如何在 Azure Cache for Redis 中配置持久性](cache-how-to-premium-persistence.md)

- [了解 Azure 存储帐户的加密](../storage/common/storage-service-encryption.md)

- [了解 Azure 客户数据保护](../security/fundamentals/protection-customer-data.md)

**责任**：共享

**Azure 安全中心监视**：无

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9：记录对关键 Azure 资源的更改并对此类更改发出警报

**指南**：将 Azure Monitor 与 azure 活动日志结合使用，以便为 Redis 的生产实例和其他关键或相关资源创建发生更改的警报。

- [如何针对 Azure 活动日志事件创建警报](/azure/azure-monitor/platform/alerts-activity-log)

**责任**：客户

**Azure 安全中心监视**：无

## <a name="vulnerability-management"></a>漏洞管理

[有关详细信息，请参阅 *Azure 安全基线：* 漏洞管理。](../security/benchmarks/security-control-vulnerability-management.md)

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1：运行自动漏洞扫描工具

**指南**：按照 Azure 安全中心提供的建议，确保 Redis 实例和相关资源的 Azure 缓存安全。

Microsoft 对支持 Redis 的 Azure 缓存的基础系统执行漏洞管理。

- [了解 Azure 安全中心建议](../security-center/recommendations-reference.md)

**责任**：共享

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

**指导**：将标记应用到 Azure资源，以便有条理地将元数据组织成某种分类。

- [如何创建和使用标记](/azure/azure-resource-manager/resource-group-using-tags)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="63-delete-unauthorized-azure-resources"></a>6.3：删除未经授权的 Azure 资源

**指导**：使用标记、管理组和单独订阅（若适用）来整理和跟踪 Azure Cache for Redis 实例及相关资源。 定期核对清单，确保及时地从订阅中删除未经授权的资源。

此外，使用 Azure Policy 对可使用以下内置策略定义在客户订阅中创建的资源类型施加限制：

- 不允许的资源类型

- 允许的资源类型

有关详细信息，请参阅以下资源：

- [如何创建其他 Azure 订阅](/azure/billing/billing-create-subscription)

- [如何创建管理组](/azure/governance/management-groups/create)

- [如何创建和使用资源标记](/azure/azure-resource-manager/resource-group-using-tags)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5：监视未批准的 Azure 资源

**指导**：在 Azure Policy 中使用以下内置策略定义，对可以在客户订阅中创建的资源类型施加限制：

- 不允许的资源类型
- 允许的资源类型

此外，使用 Azure 资源关系图可查询和发现订阅中的资源。

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [如何使用 Azure Graph 创建查询](../governance/resource-graph/first-query-portal.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="69-use-only-approved-azure-services"></a>6.9：仅使用已批准的 Azure 服务

**指南**：在 Azure Policy 中使用以下内置策略定义，对可以在客户订阅中创建的资源类型施加限制：

- 不允许的资源类型

- 允许的资源类型

有关详细信息，请参阅以下资源：

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

**指南**：通过 azure 策略为 Redis 实例定义和实现 azure 缓存的标准安全配置。 使用 "Microsoft Cache" 命名空间中的 Azure 策略别名创建自定义策略，以便为 Redis 实例审核或强制执行 Azure 缓存配置。 你还可以使用与 Azure Cache for Redis 实例相关的内置策略定义，例如：

- 应该启用只能通过安全方式连接到 Redis 缓存

有关详细信息，请参阅以下资源：

- [如何查看可用的 Azure Policy 别名](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&amp;preserve-view=true)

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3：维护安全的 Azure 资源配置

**指南**：使用 Azure Policy“[拒绝]”和“[不存在则部署]”对不同的 Azure 资源强制实施安全设置。

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [了解 Azure Policy 效果](../governance/policy/concepts/effects.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5：安全存储 Azure 资源的配置

**指南**：如果使用 azure Cache 的自定义 azure 策略定义或 azure 资源管理器模板来 Redis 实例和相关资源，请使用 Azure Repos 安全地存储和管理你的代码。

- [如何在 Azure DevOps 中存储代码](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops&amp;preserve-view=true)

- [Azure Repos 文档](https://docs.microsoft.com/azure/devops/repos/?view=azure-devops&amp;preserve-view=true)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7：部署 Azure 资源的配置管理工具

**指导**：使用“Microsoft.Cache”命名空间中的 Azure Policy 别名创建自定义策略，以审核、强制实施系统配置并设置相关警报。 另外，开发一个用于管理策略例外的流程和管道。

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9：为 Azure 资源实施自动配置监视

**指导**：使用“Microsoft.Cache”命名空间中的 Azure Policy 别名创建自定义策略，以审核、强制实施系统配置并设置相关警报。 使用 Azure Policy [审核]、[拒绝] 和 [不存在时部署] 为 Azure Cache for Redis 实例及相关资源自动强制实施配置。

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="711-manage-azure-secrets-securely"></a>7.11：安全管理 Azure 机密

**指南**：对于在 Azure App Service 上运行的 azure 虚拟机或 web 应用程序，用于访问用于 Redis 实例的 azure 缓存，请将托管服务标识与 Azure Key Vault 结合使用，以便为 Redis 密钥管理简化和保护 azure 缓存。 请确保启用 Key Vault 软删除。

- [如何与 Azure 托管标识集成](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [如何创建 Key Vault](../key-vault/general/quick-create-portal.md)

- [如何向 Key Vault 进行身份验证](/azure/key-vault/managed-identity)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="712-manage-identities-securely-and-automatically"></a>7.12：安全自动管理标识

**指南**：对于在 Azure App Service 上运行的 azure 虚拟机或 web 应用程序，用于访问用于 Redis 实例的 azure 缓存，请将托管服务标识与 Azure Key Vault 结合使用，以便为 Redis 密钥管理简化和保护 azure 缓存。 确保启用 Key Vault 软删除。

使用托管标识在 Azure Active Directory (Azure AD) 中为 Azure 服务提供一个自动托管的标识。 使用托管标识可以向支持 Azure AD 身份验证的任何服务（包括 Azure Key Vault）进行身份验证，无需在代码中放入任何凭据。

- [如何配置托管标识](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

- [如何与 Azure 托管标识集成](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

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

**指导**：Microsoft 反恶意软件已在支持 Azure 服务（例如 Azure Cache for Redis）的基础主机上启用，但它不会针对客户内容运行。

预扫描要上传到非计算 Azure 资源的任何内容，例如应用服务、Data Lake Storage、Blob 存储、Azure Database for PostgreSQL 等。Microsoft 无法访问这些实例中的数据。

**责任**：客户

**Azure 安全中心监视**：无

## <a name="data-recovery"></a>数据恢复

[有关详细信息，请参阅 *Azure 安全基线：* 数据恢复](../security/benchmarks/security-control-data-recovery.md)。

### <a name="91-ensure-regular-automated-back-ups"></a>9.1：确保定期执行自动备份

**指南**：启用 Redis 暂留。 使用 Redis 持久性可以在 Redis 中持久存储数据。 还可以获取快照并备份数据，以便在出现硬件故障时进行加载。 这相对于基本级别或标准级别是一项巨大优势，因为基本级别或标准级别将所有数据存储在内存中，在出现故障的情况下，如果缓存节点停机，则可能导致数据丢失。

你还可以使用 Azure Cache 进行 Redis 导出。 使用导出可以将 Azure Redis 缓存中存储的数据导出到与 Redis 兼容的 RDB 文件。 可以使用此功能将一个 Azure Redis 缓存实例中的数据移到另一个 Azure Redis 缓存实例或另一个 Redis 服务器。 在导出过程中，将在承载 Redis server 实例的 Azure 缓存的虚拟机上创建临时文件，并将文件上传到指定的存储帐户。 导出操作完成后，无论状态为成功还是失败，都会删除临时文件。

- [如何启用 Redis 暂留](cache-how-to-premium-persistence.md)

- [如何使用 Azure Cache 进行 Redis 导出](cache-how-to-import-export-data.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2：执行完整系统备份，并备份客户管理的所有密钥

**指南**：启用 Redis 暂留。 使用 Redis 持久性可以在 Redis 中持久存储数据。 还可以获取快照并备份数据，以便在出现硬件故障时进行加载。 这相对于基本级别或标准级别是一项巨大优势，因为基本级别或标准级别将所有数据存储在内存中，在出现故障的情况下，如果缓存节点停机，则可能导致数据丢失。

你还可以使用 Azure Cache 进行 Redis 导出。 使用导出可以将 Azure Redis 缓存中存储的数据导出到与 Redis 兼容的 RDB 文件。 可以使用此功能将一个 Azure Redis 缓存实例中的数据移到另一个 Azure Redis 缓存实例或另一个 Redis 服务器。 在导出过程中，将在承载 Redis server 实例的 Azure 缓存的虚拟机上创建临时文件，并将文件上传到指定的存储帐户。 导出操作完成后，无论状态为成功还是失败，都会删除临时文件。

如果使用 Azure Key Vault 为 Redis 实例存储 Azure 缓存的凭据，请确保密钥的定期自动备份。

- [如何启用 Redis 暂留](cache-how-to-premium-persistence.md)

- [如何使用 Azure Cache 进行 Redis 导出](cache-how-to-import-export-data.md)

- [如何备份 Key Vault 密钥](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3：验证所有备份，包括客户管理的密钥

**指南**：使用 Azure Cache 进行 Redis 导入。 导入可用于从任何云或环境中运行的任何 Redis 服务器引入与 Redis 兼容的 RDB 文件，包括在 Linux、Windows 上运行的 Redis 或任何云提供程序（如 Amazon Web Services 和其他云提供程序）。 导入数据是使用预先填充的数据创建缓存的简单方式。 在导入过程中，Azure Redis 缓存从 Azure 存储将 RDB 文件加载到内存中，再将密钥插入到缓存中。

定期测试 Azure Key Vault 机密的数据还原。

- [如何使用 Azure Cache 进行 Redis 导入](cache-how-to-import-export-data.md)

- [如何还原 Key Vault 机密](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultsecret?view=azps-4.8.0&amp;preserve-view=true)

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
