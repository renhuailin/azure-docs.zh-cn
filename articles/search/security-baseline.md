---
title: Azure 认知搜索的 Azure 安全基线
description: Azure 认知搜索安全基线为实现 Azure 安全基准中指定的安全建议提供过程指南和资源。
author: msmbaldwin
ms.service: search
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 313ac05d8e4fdc19736d0c35285c2b854ec26968
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589187"
---
# <a name="azure-security-baseline-for-azure-cognitive-search"></a>Azure 认知搜索的 Azure 安全基线

此安全基线将 [Azure 安全基准 1.0 版](../security/benchmarks/overview-v1.md)中的指南应用到 Azure 认知搜索。 Azure 安全基准提供有关如何在 Azure 上保护云解决方案的建议。 内容按“安全控制”分组，这些控制根据适用于 Azure 认知搜索的 Azure 安全基准和相关指南定义。 控制措施不适用于 Azure 认知搜索，或已排除该客户。

若要查看 Azure 认知搜索到 Azure 安全基准的完整映射，请参阅[完整的 Azure 认知搜索安全基线映射文件](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)。

## <a name="network-security"></a>网络安全

[有关详细信息，请参阅 *Azure 安全基线：* 网络安全性](../security/benchmarks/security-control-network-security.md)。

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1：保护虚拟网络中的 Azure 资源

**指导**：确保通过规则向所有 Microsoft Azure 虚拟网络子网部署应用了网络安全组，以实现“最小特权”访问方案。 只允许访问你的应用程序的受信任端口和 IP 地址范围。 在可行的情况下，请为 Azure 认知搜索部署 Azure 专用终结点，以实现从你的虚拟网络对你的服务的专用访问。

认知搜索还支持通过其他网络安全功能来管理网络访问控制列表。 将搜索服务配置为仅允许与受信任的源通信，方法是使用其防火墙功能限制从特定公共 IP 地址范围进行访问。

- [如何配置 Azure 认知搜索的专用终结点](service-create-private-endpoint.md)

- [如何配置 Azure 认知搜索防火墙](service-configure-firewall.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2：监视和记录虚拟网络、子网和 NIC 的配置与流量

**指导**：不能将认知搜索直接部署到虚拟网络。 但是，如果你的客户端应用程序或数据源位于虚拟网络中，则可以监视和记录这些网络内组件的流量，包括发送到云中搜索服务的请求。 标准建议包括启用网络安全组流日志，以及将日志发送到 Azure 存储或 Log Analytics 工作区。 可以选择使用流量分析来深入了解流量模式。

- [如何启用网络安全组流日志](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [如何启用和使用流量分析](../network-watcher/traffic-analytics.md)

- [了解 Azure 安全中心提供的网络安全性](../security-center/security-center-network-recommendations.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4：拒绝与已知恶意的 IP 地址进行通信

**指导**：认知搜索未提供应对分布式拒绝服务攻击的特定功能，但你可以在与认知搜索服务相关联的虚拟网络上启用 DDoS 防护标准，以便进行常规保护。

- [如何配置 DDoS 防护](../ddos-protection/manage-ddos-protection.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="15-record-network-packets"></a>1.5：记录网络数据包

**指导**：为保护着要连接到你的认知搜索服务的 Azure 虚拟机 (VM) 的网络安全组启用网络安全组流日志。 将日志发送到 Azure 存储帐户以进行流量审核。 

如果需要启用网络观察程序数据包捕获才能调查异常活动，请启用它。

- [如何启用 NSG 流日志](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [如何启用网络观察程序](../network-watcher/network-watcher-create.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6：部署基于网络的入侵检测/入侵防护系统 (IDS/IPS)

**指导**：认知搜索不支持网络入侵检测，但你可以配置防火墙规则，指定认知搜索服务接受的 IP 地址，作为入侵缓解措施。 配置专用终结点，使搜索流量不流经公共 Internet。

- [如何配置客户管理的密钥以进行数据加密](search-security-manage-encryption-keys.md)

- [如何从索引和同义词映射获取客户管理的密钥信息](search-security-get-encryption-keys.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8：最大程度地降低网络安全规则的复杂性和管理开销

**指导**：如果在认知搜索中利用索引器和技能集，请使用服务标记来表示有权连接到外部资源的 IP 地址范围。 

在规则的相应源或目标字段中指定服务标记名称（例如 AzureCognitiveSearch），以允许或拒绝流向资源的流量。 

- [虚拟网络服务标记](../virtual-network/service-tags-overview.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="110-document-traffic-configuration-rules"></a>1.10：阐述流量配置规则

**指导**：可以为认知搜索配置 Azure 专用终结点，以便将搜索服务与虚拟网络集成。  为网络安全组以及与网络安全和通信流相关的其他资源使用资源标记。 对于单独的网络安全组规则，请使用“说明”字段来记录允许流入/流出网络的流量的规则。 
 

使用与标记相关的任何内置 Azure Policy 定义（例如“需要标记及其值”效果）来确保使用标记创建所有资源，并在现有资源不带标记时发出通知。 

可使用 Azure PowerShell 或 Azure CLI，基于资源的标记查找资源或对其执行操作。  

 
- [了解如何为认知搜索创建专用终结点](service-create-private-endpoint.md) 

 
 
- [如何创建和使用标记](../azure-resource-manager/management/tag-resources.md)

- [如何创建 Azure 虚拟网络](../virtual-network/quick-create-portal.md) 

 
- [如何使用网络安全组规则筛选网络流量](../virtual-network/tutorial-filter-network-traffic.md)

**责任**：客户

Azure 安全中心监视：无

## <a name="logging-and-monitoring"></a>日志记录和监视

[有关详细信息，请参阅 *Azure 安全基线：* 日志记录和监视](../security/benchmarks/security-control-logging-monitoring.md)。

### <a name="22-configure-central-security-log-management"></a>2.2：配置中心安全日志管理

**指导**：通过 Azure Monitor 引入与认知搜索相关的日志，以聚合终结点设备、网络资源和其他安全系统生成的安全数据。 在 Azure Monitor 中，使用 Log Analytics 工作区来查询和执行分析，并使用 Azure 存储帐户进行长期存档存储。 或者，可以启用此数据并将其加入 Azure Sentinel 或第三方 SIEM。
 

 
- [如何开始使用 Azure Monitor 和第三方 SIEM 集成](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)
 

 
- [如何使用 Azure Monitor 收集平台日志和指标](../azure-monitor/essentials/diagnostic-settings.md) 
 

 
- [如何加入 Azure Sentinel](../sentinel/quickstart-onboard.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：为 Azure 资源启用审核日志记录

**指导**：诊断和操作日志提供有关认知搜索详细操作的见解，对于监视服务以及访问你的服务的工作负载很有用。  若要捕获诊断数据，请指定存储日志记录信息的位置以启用日志记录。
 

 
- [如何收集和分析 Azure 认知搜索的日志数据](search-monitor-logs.md) 

 
- [如何使用 Azure Monitor 收集平台日志和指标](../azure-monitor/essentials/diagnostic-settings.md)

**责任**：客户

Azure 安全中心监视：[Azure 安全基准](/azure/governance/policy/samples/azure-security-benchmark)是安全中心的默认策略计划，并且是[安全中心的建议](/azure/security-center/security-center-recommendations)的基础。 安全中心会自动启用与此控件相关的 Azure Policy 定义。 与此控件相关的警报可能需要相关服务的 [Azure Defender](/azure/security-center/azure-defender) 计划。

Azure Policy 内置定义 - Microsoft.Search：

[!INCLUDE [Resource Policy for Microsoft.Search 2.3](../../includes/policy/standards/asb/rp-controls/microsoft.search-2-3.md)]

### <a name="25-configure-security-log-storage-retention"></a>2.5：配置安全日志存储保留期

**指导**：认知搜索默认将馈送到诊断指标的历史数据保留 30 天。 若要保留更长时间，请务必启用以下设置：指定用于保存所记录事件和指标的存储选项的设置。
 

 
在 Azure Monitor 中，根据组织的合规性规则设置 Log Analytics 工作区保持期。 将 Azure 存储帐户用于长期存储和存档存储。 
 

 
- [更改 Log Analytics 中的数据保留期](https://docs.microsoft.com/azure/azure-monitor/logs/manage-cost-storage#change-the-data-retention-period) 

 
- [如何为 Azure 存储帐户日志配置保留策略](https://docs.microsoft.com/azure/storage/common/manage-storage-analytics-logs#enable-logs)

**责任**：客户

Azure 安全中心监视：无

### <a name="26-monitor-and-review-logs"></a>2.6：监视和审查日志

**指导**：分析和监视认知搜索服务生成的日志中是否存在异常行为。 使用 Azure Monitor 的 Log Analytics 检查日志并对日志数据执行查询。 或者，可以启用将数据加入 Azure Sentinel 或第三方 SIEM 的功能。 

 
 
- [如何收集和分析认知搜索的日志数据](search-monitor-logs.md)
 
- [如何在 Power BI 中直观显示搜索日志数据](search-monitor-logs-powerbi.md)
 

 
- [如何加入 Azure Sentinel](../sentinel/quickstart-onboard.md)
 

 
- [了解 Log Analytics](../azure-monitor/logs/log-analytics-tutorial.md)
 

 
- [如何在 Azure Monitor 中执行自定义查询](../azure-monitor/logs/get-started-queries.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7：针对异常活动启用警报

**指导**：使用安全中心和 Log Analytics 工作区监视安全日志和事件中的异常活动并发出警报。 或者，可以启用数据并将其加入 Azure Sentinel。
 

 
- [如何加入 Azure Sentinel](../sentinel/quickstart-onboard.md)
 

 
- [如何在 Azure 安全中心管理警报](../security-center/security-center-managing-and-responding-alerts.md)
 

 
- [如何针对 Log Analytics 日志数据发出警报](../azure-monitor/alerts/tutorial-response.md)

**责任**：客户

Azure 安全中心监视：无

## <a name="identity-and-access-control"></a>标识和访问控制

[有关详细信息，请参阅 *Azure 安全基线：* 标识和访问控制](../security/benchmarks/security-control-identity-access-control.md)。

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1：维护管理帐户的清单

**指南**：借助基于 Azure 角色的访问控制 (Azure RBAC)，可以通过角色分配管理对 Azure 资源的访问。 可以将这些角色分配给用户、组服务主体和托管标识。 某些资源具有预定义的内置角色，可以通过工具（例如 Azure CLI、Azure PowerShell 或 Azure 门户）来清点或查询这些角色。

认知搜索角色与支持服务级别管理任务的权限相关联。 这些角色不授予对服务终结点的访问权限。 访问对终结点执行的操作（例如索引管理、索引填充和搜索数据查询），使用 API 密钥对请求进行身份验证。

- [为针对 Azure 认知搜索的管理访问权限设置角色](search-security-rbac.md)

- [创建和管理 Azure 认知搜索服务的 API 密钥](search-security-api-keys.md)

- [如何使用 PowerShell 获取 Azure Active Directory (Azure AD) 中的目录角色](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0&amp;preserve-view=true)

- [如何使用 PowerShell 获取 Azure AD 中目录角色的成员](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0&amp;preserve-view=true)

**责任**：客户

Azure 安全中心监视：无

### <a name="33-use-dedicated-administrative-accounts"></a>3.3：使用专用管理帐户

**指导**：认知搜索没有可用于管理索引和操作的本地级别或 Azure Active Directory (Azure AD) 管理员帐户的概念。 

请使用 Azure AD 内置角色（必须显式分配）执行管理操作。 调用 Azure AD PowerShell 模块执行即席查询，以发现属于管理组成员的帐户。

- [如何使用角色控制认知搜索中的管理访问权限](search-security-rbac.md)

- [如何使用 PowerShell 获取 Azure AD 中的目录角色](/powershell/module/azuread/get-azureaddirectoryrole)

**责任**：客户

Azure 安全中心监视：无

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4：将单一登录 (SSO) 与 Azure Active Directory 配合使用

**指导**：将 SSO 身份验证用于 Azure Active Directory (Azure AD)，以便通过 Azure 资源管理器访问受支持管理操作的搜索服务信息。 

建立一个流程，使用组织中预先存在的标识为服务启用 SSO，从而减少标识和凭据的数量。

- [了解 Azure AD 的 SSO](../active-directory/manage-apps/what-is-single-sign-on.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5：对所有基于 Azure Active Directory 的访问使用多重身份验证

**指导**：启用 Azure Active Directory (Azure AD) 多重身份验证功能，并遵循安全中心的标识和访问建议。

- [如何在 Azure 中启用多重身份验证](../active-directory/authentication/howto-mfa-getstarted.md) 

- [如何在 Azure 安全中心监视标识和访问](../security-center/security-center-identity-access.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6：对所有管理任务使用专用计算机（特权访问工作站）

**指导**：使用配置了多重身份验证的特权访问工作站 (PAW) 来登录并访问 Azure 资源。
 

 
- [了解安全的 Azure 托管工作站](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)
 

 
- [如何启用 Azure Active Directory (Azure AD) 多重身份验证](../active-directory/authentication/howto-mfa-getstarted.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7：记录来自管理帐户的可疑活动并对其发出警报

**指导**：使用 Azure Active Directory (Azure AD) 安全报告和监视，来检测环境中何时发生可疑或不安全的活动。 使用安全中心监视标识和访问活动。

- [如何确定标记为存在风险活动的 Azure AD 用户](../active-directory/identity-protection/overview-identity-protection.md)

- [如何在 Azure 安全中心内监视用户的标识和访问活动](../security-center/security-center-identity-access.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="39-use-azure-active-directory"></a>3.9：使用 Azure Active Directory

**指导**：对于 Azure 认知搜索中的服务级别管理任务，使用 Azure Active Directory (Azure AD) 作为中心身份验证和授权系统。 Azure AD 标识不授予对搜索服务终结点的访问权限。  可通过 API 密钥获得索引管理、索引填充和搜索数据查询等操作的访问权限。

- [如何创建和配置 Azure AD 实例](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [创建和管理 Azure 认知搜索服务的 API 密钥](search-security-api-keys.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10：定期审查和协调用户访问

**指导**：Azure Active Directory (Azure AD) 提供日志来帮助发现过时的帐户。 使用 Azure AD 的标识和访问评审，有效管理组成员身份、对企业应用程序的访问以及角色分配。 可以定期评审用户的访问权限，确保只有适当的用户才持续拥有访问权限。 

查看认知搜索生成的诊断日志，以查找搜索服务终结点中的活动，例如索引管理、索引填充和查询。

- [了解 Azure AD 报告](/azure/active-directory/reports-monitoring/)

- [如何使用 Azure AD 标识和访问评审](../active-directory/governance/access-reviews-overview.md)

- [监视 Azure 认知搜索的操作和活动](search-monitor-usage.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11：监视尝试访问已停用凭据的行为

**指导**：通过访问 Azure Active Directory (AD) 登录活动、审核和风险事件日志源，可以与任何 SIEM 或监视工具集成。

可通过为 Azure AD 用户帐户创建诊断设置，并将审核日志和登录日志发送到 Log Analytics 工作区，来简化此过程。 在 Log Analytics 工作区中配置所需的警报。

- [如何将 Azure 活动日志与 Azure Monitor 集成](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**责任**：客户

Azure 安全中心监视：无

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12：针对帐户登录行为偏差发出警报

指导：使用 Azure Active Directory (Azure AD) 标识保护功能配置对检测到的与用户标识相关的可疑操作的自动响应。 根据需要将数据引入 Azure Sentinel 以便进一步调查。

- [如何查看 Azure AD 风险登录](../active-directory/identity-protection/overview-identity-protection.md)

- [如何配置和启用标识保护风险策略](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md) 

- [如何加入 Azure Sentinel](../sentinel/quickstart-onboard.md)

**责任**：客户

Azure 安全中心监视：无

## <a name="data-protection"></a>数据保护

[有关详细信息，请参阅 *Azure 安全基线：* 数据保护](../security/benchmarks/security-control-data-protection.md)。

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1：维护敏感信息的清单

**指导**：使用标记可以帮助跟踪存储或处理敏感信息的 Azure 资源。

**责任**：客户

Azure 安全中心监视：无

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2：隔离存储或处理敏感信息的系统

**指导**：为开发、测试和生产实施单独的订阅和/或管理组。 资源应当按虚拟网络/子网进行分隔，相应地进行标记，并由网络安全组或 Azure 防火墙提供保护。 应当隔离用于存储或处理敏感数据的资源。 使用专用链接，将专用终结点配置到认知搜索。

- [如何创建其他 Azure 订阅](../cost-management-billing/manage/create-subscription.md)

- [如何创建和使用标记](../azure-resource-manager/management/tag-resources.md)

- [了解如何为认知搜索创建专用终结点](service-create-private-endpoint.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3：监视和阻止未经授权的敏感信息传输

**指导**：利用 Azure 市场中有关网络外围的第三方解决方案，监视并阻止敏感信息的未授权传输，同时提醒信息安全专业人员。

Microsoft 管理基础平台，并将所有客户内容视为敏感数据，防范客户数据丢失和泄露。 为了确保 Azure 中的客户数据保持安全，Microsoft 已实施并维护一套可靠的数据保护控制机制和功能。

- [了解 Azure 中的客户数据保护](../security/fundamentals/protection-customer-data.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5：使用有效的发现工具识别敏感数据

**指导**：数据标识、分类和丢失防护功能尚不适用于认知搜索。 可以根据合规性需要实施第三方解决方案。 

Microsoft 管理基础平台，并将所有客户内容视为敏感数据，防范客户数据丢失和泄露。 为了确保 Azure 中的客户数据保持安全，Microsoft 已实施并维护一套可靠的数据保护控制机制和功能。

- [了解 Azure 中的客户数据保护](../security/fundamentals/protection-customer-data.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4.6：使用 Azure RBAC 管理对资源的访问

**指导**：对于服务管理，使用 Azure 基于角色的访问控制 (Azure RBAC) 来管理对密钥和配置的访问。 对于索引和查询等内容操作，认知搜索使用密钥，而不是基于标识的访问控制模型。 使用 Azure RBAC 控制对密钥的访问。 

 
- [如何配置 Azure RBAC](../role-based-access-control/role-assignments-portal.md)  
 
 
- [如何使用角色控制对认知搜索的管理访问权限](search-security-rbac.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8：静态加密敏感信息

**指导**：认知搜索会自动使用 Microsoft 管理的密钥对已编制索引的内容进行静态加密。 如果需要更多保护，可以使用在 Azure Key Vault 中创建和管理的密钥，用第二个加密层来补充默认加密。

- [在 Azure 认知服务中配置客户管理的密钥以进行数据加密](search-security-manage-encryption-keys.md)

- [了解 Azure 中的静态加密](../security/fundamentals/encryption-atrest.md)

**责任**：共享

Azure 安全中心监视：无

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9：记录对关键 Azure 资源的更改并对此类更改发出警报

**指导**：将 Azure Monitor 与 Azure 活动日志结合使用，以创建在认知搜索的生产实例和其他关键资源或相关资源发生更改时发出的警报。 

 
- [如何针对 Azure 活动日志事件创建警报](../azure-monitor/alerts/alerts-activity-log.md) 

 
- [如何为认知搜索活动创建警报](search-monitor-logs.md)

**责任**：客户

Azure 安全中心监视：无

## <a name="inventory-and-asset-management"></a>清单和资产管理

[有关详细信息，请参阅 *Azure 安全基线：* 清单和资产管理](../security/benchmarks/security-control-inventory-asset-management.md)。

### <a name="61-use-automated-asset-discovery-solution"></a>6.1：使用自动化资产发现解决方案

**指导**：使用 Azure Resource Graph 来查询和发现订阅中的所有资源（例如计算、存储、网络、端口、协议等）。

确保租户中具有适当的（读取）权限，并枚举所有 Azure 订阅以及订阅中的资源。

- [如何使用 Azure Resource Graph 浏览器创建查询](../governance/resource-graph/first-query-portal.md)

- [如何查看 Azure 订阅](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-4.8.0&amp;preserve-view=true)

- [了解 Azure RBAC](../role-based-access-control/overview.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="62-maintain-asset-metadata"></a>6.2：维护资产元数据

**指导**：将标记应用到 Azure资源，以便有条理地将元数据组织成某种分类。

- [如何创建和使用标记](../azure-resource-manager/management/tag-resources.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="63-delete-unauthorized-azure-resources"></a>6.3：删除未经授权的 Azure 资源

**指导**：在适用的情况下，请使用标记、管理组和单独的订阅来组织和跟踪资产。 定期核对清单，确保及时地从订阅中删除未经授权的资源。

- [如何创建其他 Azure 订阅](../cost-management-billing/manage/create-subscription.md)

- [如何创建管理组](../governance/management-groups/create-management-group-portal.md)

- [如何创建和使用标记](../azure-resource-manager/management/tag-resources.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4：定义并维护已批准 Azure 资源的清单

**指导**：定义与认知搜索中的索引编制和技能组合处理相关的经批准 Azure 资源的列表。

**责任**：客户

Azure 安全中心监视：无

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5：监视未批准的 Azure 资源

**指导**：建议先定义已根据你的组织策略和标准批准使用的 Azure 资源的清单，然后使用 Azure Policy 或 Azure Resource Graph 来监视未批准的 Azure 资源。

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md) 

- [如何使用 Azure Graph 创建查询](../governance/resource-graph/first-query-portal.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="69-use-only-approved-azure-services"></a>6.9：仅使用已批准的 Azure 服务

**指导**：使用以下内置策略定义，通过 Azure Policy 对可以在客户订阅中创建的资源类型施加限制：
- 不允许的资源类型
- 允许的资源类型

使用 Azure Resource Graph 查询或发现订阅中的资源。 确保环境中的所有 Azure 资源均已获得批准。

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [如何使用 Azure Policy 拒绝特定的资源类型](/azure/governance/policy/samples/)

**责任**：客户

Azure 安全中心监视：无

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11：限制用户与 Azure 资源管理器进行交互的能力

**指南**：使用 Azure 条件访问可通过为“Microsoft Azure 管理”应用配置“阻止访问”，限制用户与 Azure 资源管理器进行交互的能力。  

 
控制对具有以下功能的密钥的访问：对向所有其他操作（特别是与认知搜索相关的操作）发出的请求进行身份验证。

**责任**：客户

Azure 安全中心监视：无

## <a name="secure-configuration"></a>安全配置

[有关详细信息，请参阅 *Azure 安全基线：* 安全配置](../security/benchmarks/security-control-secure-configuration.md)。

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1：为所有 Azure 资源建立安全配置

**指导**：使用“Microsoft.Search”命名空间中的 Azure Policy 别名创建自定义策略，以审核或强制实施 Azure 认知搜索资源的配置。 还可以使用认知搜索服务的内置 Azure Policy 定义，例如：

为 Azure 资源启用审核日志记录

Azure 资源管理器能够以 JavaScript 对象表示法 (JSON) 导出模板，应该对其进行检查，以确保配置满足组织的安全要求。

还可以使用来自 Azure 安全中心的建议作为 Azure 资源的安全配置基线。

- [Azure 认知搜索的 Azure Policy 法规遵从性控制](security-controls-policy.md)

- [如何查看可用的 Azure Policy 别名](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&amp;preserve-view=true)

**责任**：客户

Azure 安全中心监视：无

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3：维护安全的 Azure 资源配置

**指导**：使用 Azure Policy“[拒绝]”和“[不存在则部署]”效果对认知搜索服务资源强制实施安全设置。 

还可以使用 Azure 资源管理器模板来维护组织所需的 Azure 资源的安全配置。 

- [了解 Azure Policy 效果](../governance/policy/concepts/effects.md)

- [Azure 认知搜索的 Azure Policy 法规遵从性控制](security-controls-policy.md)

- [创建和管理策略以强制实施符合性](../governance/policy/tutorials/create-and-manage.md)

- [Azure 资源管理器模板概述](../azure-resource-manager/templates/overview.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5：安全存储 Azure 资源的配置

**指导**：如果使用自定义的 Azure Policy 定义，请使用 Azure DevOps 或 Azure Repos 安全地存储和管理代码。

- [如何在 Azure DevOps 中存储代码](/azure/devops/repos/git/gitworkflow)

- [Azure Repos 文档](/azure/devops/repos/index)

**责任**：客户

Azure 安全中心监视：无

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7：部署 Azure 资源的配置管理工具

**指导**：使用 Azure Policy 为认知搜索服务定义和实施标准安全配置。 

使用别名创建用于审核或强制实施网络配置的自定义策略。 还可以使用与特定资源相关的内置策略定义。 

此外，也可以使用 Azure 自动化来部署配置更改和管理策略例外。 

- [Azure 认知搜索的 Azure Policy 法规遵从性控制](security-controls-policy.md)

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9：为 Azure 资源实施自动配置监视

**指导**：使用安全中心对认知搜索服务资源执行基线扫描。  此外，使用 Azure Policy 提醒和审核你的资源配置。 

- [如何在 Azure 安全中心修正建议](../security-center/security-center-remediate-recommendations.md)

- [Azure 认知搜索的 Azure Policy 法规遵从性控制](security-controls-policy.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="711-manage-azure-secrets-securely"></a>7.11：安全管理 Azure 机密

**指导**：将 Azure 托管标识与 Azure Key Vault 结合使用，以简化云应用程序的机密管理。

- [如何使用 Azure 资源的托管标识](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md) 

- [如何创建 Key Vault](../key-vault/general/quick-create-portal.md)

- [如何使用托管标识提供 Key Vault 身份验证](../key-vault/general/assign-access-policy-portal.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="712-manage-identities-securely-and-automatically"></a>7.12：安全自动管理标识

**指导**：使用 Azure 托管标识，使用 Azure Active Directory (Azure AD) 中的自动托管标识，向认知搜索授予对其他 Azure 服务（例如 Key Vault 和索引器数据源）的访问权限。 使用托管标识可以向支持 Azure AD 身份验证的任何服务（包括 Azure Key Vault）进行身份验证，无需在代码中放入任何凭据。 

- [使用托管标识设置到数据源的索引器连接](search-howto-managed-identities-data-sources.md)

- [使用托管标识配置客户管理的密钥以用于数据加密](search-security-manage-encryption-keys.md)

**责任**：客户

Azure 安全中心监视：无

## <a name="malware-defense"></a>恶意软件防护

[有关详细信息，请参阅 *Azure 安全基线：* 恶意软件防护](../security/benchmarks/security-control-malware-defense.md)。

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2：预先扫描要上传到非计算 Azure 资源的文件

**指导**：预扫描要上传到非计算 Azure 资源（例如认知搜索、Blob 存储、Azure SQL 数据库等）的任何内容。 

你需要负责预先扫描要上传到非计算 Azure 资源的任何内容。 Microsoft 无法访问客户数据，因此无法代表你对客户内容执行反恶意软件扫描。

**责任**：客户

**Azure 安全中心监视**：无

### <a name="83-ensure-antimalware-software-and-signatures-are-updated"></a>步骤 8.3：确保反恶意软件和签名已更新

**指导**：不适用于认知搜索。 它不允许在其资源上安装反恶意软件解决方案。 对于基础平台，Microsoft 将负责更新任何反恶意软件和签名。  

 
对于你的组织拥有的、你的搜索解决方案中使用的任何计算资源，请按照安全中心的“计算和应用”内的建议进行操作，以确保所有终结点都具有最新的签名。 对于 Linux，请使用第三方反恶意软件解决方案。

**责任**：共享

Azure 安全中心监视：无

## <a name="data-recovery"></a>数据恢复

[有关详细信息，请参阅 *Azure 安全基线：* 数据恢复](../security/benchmarks/security-control-data-recovery.md)。

### <a name="91-ensure-regular-automated-back-ups"></a>9.1：确保定期执行自动备份

**指导**：不能通过 Azure 备份或任何其他内置机制来备份搜索服务中存储的内容，但可以从应用程序源代码和主数据源重新生成索引，或者构建自定义工具来检索和存储已编制索引的内容。 

 
- [GitHub Index-backup-restore 示例](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/index-backup-restore)

**责任**：客户

Azure 安全中心监视：无

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2：执行完整系统备份，并备份客户管理的所有密钥

**指导**：认知搜索当前不支持自动备份搜索服务中的数据，必须通过手动过程进行备份。 你还可在 Azure 密钥保管库中备份客户管理的密钥。
 

- [备份和还原 Azure 认知搜索索引](/samples/azure-samples/azure-search-dotnet-samples/azure-search-backup-restore-index/)

- [如何在 Azure 中备份 Key Vault 密钥](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey?view=azps-4.8.0&amp;preserve-view=true)

**责任**：客户

Azure 安全中心监视：无

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3：验证所有备份，包括客户管理的密钥

**指导**：认知搜索当前不支持自动备份搜索服务中的数据，必须通过手动过程进行备份和还原。 请对手动备份的内容定期执行数据还原，以确保备份过程的端到端完整性。

- [备份和还原 Azure 认知搜索索引](/samples/azure-samples/azure-search-dotnet-samples/azure-search-backup-restore-index/)

- [如何在 Azure 中还原 Key Vault 密钥](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultkey?view=azps-4.8.0&amp;preserve-view=true)

**责任**：客户

Azure 安全中心监视：无

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4：确保保护备份和客户管理的密钥

**指导**：认知搜索当前不支持自动备份搜索服务中的数据，必须通过手动过程进行备份。  你还可在 Azure 密钥保管库中备份客户管理的密钥。  

 
在 Key Vault 中启用软删除和清除保护，以防止意外删除或恶意删除密钥。 如果将 Azure 存储用于存储手动备份，请启用软删除以在 blob 或 blob 快照被删除时保存和恢复数据。 
 

 
- [备份和还原 Azure 认知搜索索引](/samples/azure-samples/azure-search-dotnet-samples/azure-search-backup-restore-index/)
 

 
- [如何在 Key Vault 中启用软删除和清除保护](../storage/blobs/soft-delete-blob-overview.md) 

- [Azure Blob 存储的软删除](../storage/blobs/soft-delete-blob-overview.md)

**责任**：客户

Azure 安全中心监视：无

## <a name="incident-response"></a>事件响应

[有关详细信息，请参阅 *Azure 安全基线：* 事件响应](../security/benchmarks/security-control-incident-response.md)。

### <a name="101-create-an-incident-response-guide"></a>10.1：创建事件响应指导

**指导**：为组织制定事件响应指南。 确保在书面的事件响应计划中定义人员职责，以及事件处理和管理从检测到事件后审查的各个阶段。

- [关于建立自己的安全事件响应流程的指南](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft 安全响应中心事件分析](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [客户还可以利用 NIST 的“计算机安全事件处理指南”来制定他们自己的事件响应计划](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**责任**：客户

Azure 安全中心监视：无

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2：创建事件评分和优先级设定过程

**指导**：安全中心为每条警报分配严重性，以帮助你优先处理应该最先调查的警报。 严重性取决于安全中心在发出警报时所依据的检测结果或分析结果的置信度，以及导致发出警报的活动的恶意企图的置信度。

此外，使用标记来标记订阅，并创建命名系统来对 Azure 资源进行标识和分类，特别是处理敏感数据的资源。 你的责任是根据发生事件的 Azure 资源和环境的关键性确定修正警报的优先级。

- [使用标记整理 Azure 资源](../azure-resource-manager/management/tag-resources.md)

- [Azure 安全中心中的安全警报](../security-center/security-center-alerts-overview.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="103-test-security-response-procedures"></a>10.3：测试安全响应过程

**指导**：定期执行演练来测试系统的事件响应功能。 识别弱点和差距，并根据需要修改计划。

- [参阅 NIST 出版的《IT 计划和功能的测试、训练和演练计划指南》](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**责任**：客户

Azure 安全中心监视：无

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4：提供安全事件联系人详细信息，并针对安全事件配置警报通知

**指导**：如果 Microsoft 安全响应中心 (MSRC) 发现数据被某方非法访问或未经授权访问，Microsoft 会使用安全事件联系信息联系用户。 事后审查事件，确保问题得到解决。

- [如何设置 Azure 安全中心安全联系人](../security-center/security-center-provide-security-contact-details.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5：将安全警报整合到事件响应系统中

**指导**：使用连续导出功能导出安全中心警报和建议。 使用连续导出，可以手动导出或者持续导出警报和建议。 可使用安全中心数据连接器将警报流式传输到 Azure Sentinel。

- [如何配置连续导出](../security-center/continuous-export.md)

- [如何将警报流式传输到 Azure Sentinel](../sentinel/connect-azure-security-center.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="106-automate-the-response-to-security-alerts"></a>10.6：自动响应安全警报

**指导**：使用 Azure 安全中心内的工作流自动化功能可以通过“逻辑应用”针对安全警报和建议自动触发响应。

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
