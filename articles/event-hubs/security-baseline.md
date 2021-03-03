---
title: 事件中心的 Azure 安全基线
description: 事件中心安全基线为实现 Azure 安全基准中指定的安全建议提供过程指南和资源。
author: msmbaldwin
ms.service: event-hubs
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 1e1a8cb24b47277b38bd2d64b733a6f28ee8acff
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101742481"
---
# <a name="azure-security-baseline-for-event-hubs"></a>事件中心的 Azure 安全基线

此安全基线将 [Azure 安全基准版本 1.0](../security/benchmarks/overview-v1.md) 中的指南应用到事件中心。 Azure 安全基准提供有关如何在 Azure 上保护云解决方案的建议。
内容由 Azure 安全基准定义的 **安全控制** 和适用于事件中心的相关指南进行分组。 排除了不适用于事件中心的 **控件**。

 
若要查看事件中心如何完全映射到 Azure 安全基准，请参阅 [完整事件中心安全基线映射文件](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)。

## <a name="network-security"></a>网络安全

[有关详细信息，请参阅 *Azure 安全基线：* 网络安全性](../security/benchmarks/security-control-network-security.md)。

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1：保护虚拟网络中的 Azure 资源

**指南**：通过将 Azure 事件中心与虚拟网络服务终结点集成，可以安全地访问工作负荷（如绑定到虚拟网络的虚拟机）中的消息传递功能，同时保护两端的网络流量路径。

绑定到至少一个虚拟网络子网服务终结点后，相应的事件中心命名空间将不再接受来自经授权的虚拟网络子网以外的任何位置的流量。 从虚拟网络的角度来看，通过将事件中心命名空间绑定到服务终结点，可配置从虚拟网络子网到消息传递服务的独立网络隧道。 

你还可以创建专用终结点，该终结点是一个网络接口，该终结点是一个网络接口，可以使用 Azure 专用链接服务将你私下和安全地连接到事件中心服务。 专用终结点使用虚拟网络中的专用 IP 地址将服务有效地接入虚拟网络中。 发往服务的所有流量都可以通过专用终结点路由，因此不需要网关、NAT 设备、ExpressRoute 或 VPN 连接或公共 IP 地址。 

还可以使用防火墙保护 Azure 事件中心命名空间。 事件中心支持针对入站防火墙支持的基于 IP 的访问控制。 可以通过 Azure 门户、Azure 资源管理器模板、Azure CLI 或 Azure PowerShell 设置防火墙规则。

- [如何将虚拟网络服务终结点用于 Azure 事件中心](event-hubs-service-endpoints.md)

- [将 Azure 事件中心与 Azure 专用链接集成](private-link-service.md)

- [如何为 Azure 事件中心命名空间配置 IP 防火墙规则](event-hubs-ip-filtering.md)

**责任**：客户

**Azure 安全中心监视**： [azure 安全基准](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) 是安全中心的默认策略计划，是 [安全中心建议](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md)的基础。 安全中心会自动启用与此控件相关的 Azure 策略定义。 与此控件相关的警报可能需要 [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) 计划相关服务。

**Azure 策略内置定义-Microsoft EventHub**：

[!INCLUDE [Resource Policy for Microsoft.EventHub 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.eventhub-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2：监视并记录虚拟网络、子网和网络接口的配置与流量

**指导**：使用 Azure 安全中心并遵循网络保护建议来帮助保护 Azure 中的事件中心资源。 如果使用 Azure 虚拟机访问事件中心，请启用网络安全组流日志，并将日志发送到存储帐户以进行流量审核。

- [如何启用 NSG 流日志](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [了解 Azure 安全中心提供的网络安全](../security-center/security-center-network-recommendations.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4：拒绝与已知恶意的 IP 地址进行通信

**指导**：为了防范分布式拒绝服务 (DDoS) 攻击，请在与事件中心关联的虚拟网络上启用 DDoS 保护标准。 根据 Azure 安全中心集成的威胁情报进行判断，拒绝与已知恶意的或未使用过的 Internet IP 地址通信。

- [如何配置 DDoS 防护](../ddos-protection/manage-ddos-protection.md)

- [有关 Azure 安全中心集成威胁情报的详细信息](/azure/security-center/security-center-alerts-service-layer)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="15-record-network-packets"></a>1.5：记录网络数据包

**指南**：如果使用 Azure 虚拟机访问事件中心，请启用网络安全组流日志，并将日志发送到存储帐户以进行流量审核。 还可以将网络安全组流日志发送到 Log Analytics 工作区，并使用流量分析来深入了解 Azure 云中的流量流。 流量分析的优势包括能够可视化网络活动、识别热点、识别安全威胁、了解流量流模式，以及查明网络不当配置。

启用网络观察程序数据包捕获（如果调查异常活动时有此需要）。

- [如何启用 NSG 流日志](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [如何启用和使用流量分析](../network-watcher/traffic-analytics.md)

- [如何启用网络观察程序](../network-watcher/network-watcher-create.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6：部署基于网络的入侵检测/入侵防护系统 (IDS/IPS)

**指导**：如果使用 Azure 虚拟机访问事件中心，请从 Azure 市场中选择一种产品/服务，该产品/服务应支持包含有效负载检查功能的 ID/IPS 功能。 如果你的组织不需要基于有效负载检查的入侵检测和/或防护，你可以使用 Azure 事件中心的内置防火墙功能。 可以使用防火墙规则，将对事件中心命名空间的访问限制为有限的 IP 地址范围或特定的 IP 地址。

- [Azure 市场](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

- [如何在事件中心为指定的 IP 地址添加防火墙规则](event-hubs-ip-filtering.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9：维护网络设备的标准安全配置

**指导**：使用 Azure Policy 为与 Azure 事件中心命名空间关联的网络资源定义和实施标准安全配置。 使用 **Microsoft EventHub** 和 **microsoft 网络** 命名空间中的 Azure 策略别名创建自定义策略定义，以审核或强制执行事件中心命名空间的网络配置。 你还可以使用与 Azure 事件中心相关的内置策略定义，例如：

- 事件中心应使用虚拟网络服务终结点。

访问所引用的链接可获得更多信息。

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [适用于事件中心命名空间的 Azure 内置策略](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#event-hub)

- [用于网络的 Azure Policy 示例](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network)

- [如何创建 Azure 蓝图](../governance/blueprints/create-blueprint-portal.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="110-document-traffic-configuration-rules"></a>1.10：阐述流量配置规则

**指导**：对与你的事件中心关联的、与网络安全和流量流相关的虚拟网络和其他资源使用标记。

- [如何创建和使用标记](/azure/azure-resource-manager/resource-group-using-tags)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11：使用自动化工具来监视网络资源配置和检测更改

**指导**：使用 Azure 活动日志监视网络资源配置，并检测与 Azure 事件中心相关的网络资源的更改。 在 Azure Monitor 中创建当关键网络资源发生更改时触发的警报。

- [如何查看和检索 Azure 活动日志事件](/azure/azure-monitor/platform/activity-log-view)

- [如何在 Azure Monitor 中创建警报](/azure/azure-monitor/platform/alerts-activity-log)

**责任**：客户

**Azure 安全中心监视**：无

## <a name="logging-and-monitoring"></a>日志记录和监视

[有关详细信息，请参阅 *Azure 安全基线：* 日志记录和监视](../security/benchmarks/security-control-logging-monitoring.md)。

### <a name="22-configure-central-security-log-management"></a>2.2：配置中心安全日志管理

**指导**：在 Azure Monitor 中，配置与活动日志和事件中心诊断设置中的事件中心相关的日志，以将日志发送到要查询的 Log Analytics 工作区或要进行长期存档存储的存储帐户。

- [如何配置 Azure 事件中心的诊断设置](event-hubs-diagnostic-logs.md)

- [了解 Azure 活动日志](/azure/azure-monitor/platform/platform-logs-overview)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：为 Azure 资源启用审核日志记录

**指导**：启用 Azure 事件中心命名空间的诊断设置。 有三种类别的 Azure 事件中心诊断设置：存档日志、操作日志和自动缩放日志。 启用操作日志可捕获事件中心操作期间发生的事件的相关信息，具体而言，就是操作类型（包括事件中心创建操作）、所使用的资源和操作状态。

此外，你可以启用 Azure 活动日志诊断设置并将其发送到 Azure 存储帐户、事件中心或 Log Analytics 工作区。 可以通过活动日志了解在 Azure 事件中心和其他资源上执行的操作。 可以通过活动日志确定对 Azure 事件中心命名空间执行的任何写入操作（PUT、POST、DELETE）的“操作内容、操作人员和操作时间”。

- [如何为 Azure 事件中心启用诊断设置](event-hubs-diagnostic-logs.md)

- [如何启用 Azure 活动日志的诊断设置](/azure/azure-monitor/platform/diagnostic-settings-legacy)

**责任**：客户

**Azure 安全中心监视**： [azure 安全基准](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) 是安全中心的默认策略计划，是 [安全中心建议](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md)的基础。 安全中心会自动启用与此控件相关的 Azure 策略定义。 与此控件相关的警报可能需要 [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) 计划相关服务。

**Azure 策略内置定义-Microsoft EventHub**：

[!INCLUDE [Resource Policy for Microsoft.EventHub 2.3](../../includes/policy/standards/asb/rp-controls/microsoft.eventhub-2-3.md)]

### <a name="25-configure-security-log-storage-retention"></a>2.5：配置安全日志存储保留期

**指导**：在 Azure Monitor 中，根据组织的合规性规章设置 Log Analytics 工作区保留期，以捕获和查看与事件中心相关的事件。

- [如何为 Log Analytics 工作区设置日志保留参数](/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="26-monitor-and-review-logs"></a>2.6：监视和查看日志

**指导**：分析和监视日志中的异常行为，并定期查看与事件中心相关的结果。 使用 Azure Monitor 的 Log Analytics 检查日志并对日志数据执行查询。 或者，你可以将和机载数据启用到 Azure Sentinel 或第三方系统信息和事件管理解决方案。

- [有关 Log Analytics 工作区的详细信息](/azure/azure-monitor/log-query/get-started-portal)

- [如何在 Azure Monitor 中执行自定义查询](/azure/azure-monitor/log-query/get-started-queries)

- [如何加入 Azure Sentinel](../sentinel/quickstart-onboard.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7：针对异常活动启用警报

**指导**：在 Azure Monitor 中，配置与活动日志和事件中心诊断设置中的 Azure 事件中心相关的日志，以将日志发送到要查询的 Log Analytics 工作区或要进行长期存档存储的存储帐户。 使用 Log Analytics 工作区针对安全日志和事件中的异常活动创建警报。

或者，可以启用数据并将其载入 Azure Sentinel。 

- [了解 Azure 活动日志](/azure/azure-monitor/platform/platform-logs-overview)

- [如何配置 Azure 事件中心的诊断设置](event-hubs-diagnostic-logs.md)

- [如何针对 Log Analytics 工作区日志数据发出警报](/azure/azure-monitor/learn/tutorial-response)

- [如何加入 Azure Sentinel](../sentinel/quickstart-onboard.md)

**责任**：客户

**Azure 安全中心监视**：无

## <a name="identity-and-access-control"></a>标识和访问控制

[有关详细信息，请参阅 *Azure 安全基线：* 标识和访问控制](../security/benchmarks/security-control-identity-access-control.md)。

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1：维护管理帐户的清单

**指导**：Azure Active Directory (Azure AD) 具有必须显式分配且可查询的内置角色。 使用 Azure AD PowerShell 模块执行即席查询，以发现属于管理组成员的帐户。

- [如何使用 PowerShell 获取 Azure AD 中的目录角色](/powershell/module/azuread/get-azureaddirectoryrole)

- [如何使用 PowerShell 获取 Azure AD 中目录角色的成员](/powershell/module/azuread/get-azureaddirectoryrolemember)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="32-change-default-passwords-where-applicable"></a>3.2：在适用的情况下更改默认密码

**指南**：可以通过 Azure Active Directory (Azure AD) 来控制控制平面对事件中心的访问。 Azure AD 没有默认密码。

可以使用托管标识或应用注册和共享访问签名通过 Azure AD 控制数据平面对事件中心的访问。 共享访问签名由连接到事件中心的客户端使用，可以随时重新生成。

- [了解事件中心的共享访问签名](authenticate-shared-access-signature.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="33-use-dedicated-administrative-accounts"></a>3.3：使用专用管理帐户

**指南**：围绕专用管理帐户的使用创建标准操作程序。 使用 Azure 安全中心标识和访问管理来监视管理帐户的数量。

此外，为了帮助你跟踪专用管理帐户，你可以使用 Azure 安全中心或内置的 Azure 策略提供的建议，例如：

- 应该为你的订阅分配了多个所有者

- 应从订阅中删除拥有所有者权限的已弃用帐户

- 应从订阅中删除拥有所有者权限的外部帐户

访问所引用的链接可获得更多信息。

- [如何使用 Azure 安全中心监视标识和访问（预览）](../security-center/security-center-identity-access.md)

- [如何使用 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4：使用 Azure Active Directory 单一登录 (SSO)

**指南**： Microsoft Azure 为基于 Azure Active Directory (Azure AD) 的资源和应用程序提供集成的访问控制管理。 将 Azure AD 与 Azure 事件中心配合使用的主要优势在于，不再需要将凭据存储在代码中。 可以从 Microsoft 标识平台请求 OAuth 2.0 访问令牌。 用于请求令牌的资源名称为 \//eventhubs.azure.net/。 Azure AD 对运行应用程序的安全主体（用户、组或服务主体）进行身份验证。 如果身份验证成功，Azure AD 会将访问令牌返回应用程序，应用程序可随之使用访问令牌对 Azure 事件中心资源请求授权。

- [如何使用 Azure AD 对应用程序进行身份验证以访问事件中心资源](authenticate-application.md)

- [了解 Azure AD 的 SSO](../active-directory/manage-apps/what-is-single-sign-on.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5：对所有基于 Azure Active Directory 的访问使用多重身份验证

**指南**：启用 Azure Active Directory (Azure AD) 多重身份验证，并遵循 Azure 安全中心的标识和访问管理建议来帮助保护已启用事件中心的资源。

- [如何在 Azure 中启用多重身份验证](../active-directory/authentication/howto-mfa-getstarted.md)

- [如何在 Azure 安全中心监视标识和访问](../security-center/security-center-identity-access.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6：对所有管理任务使用专用计算机（特权访问工作站）

**指南**：使用特权访问工作站 (PAW) 配置为登录和配置支持事件中心的资源的多重身份验证。

- [了解特权访问工作站](/security/compass/privileged-access-devices)

- [如何在 Azure 中启用多重身份验证](../active-directory/authentication/howto-mfa-getstarted.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7：记录来自管理帐户的可疑活动并对其发出警报

**指南**：使用 Azure Active Directory (Azure AD) Privileged Identity Management 在环境中发生可疑或不安全活动时生成日志和警报。 使用 Azure AD 风险检测查看有关风险用户行为的警报和报告。 如需其他日志记录，请将 Azure 安全中心风险检测警报发送到 Azure Monitor 中，并使用操作组配置自定义警报/通知。

- [了解 Azure AD 风险检测](/azure/active-directory/reports-monitoring/concept-risk-events)

- [如何为自定义警报和通知配置操作组](/azure/azure-monitor/platform/action-groups)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8：仅从批准的位置管理 Azure 资源

**指南**：使用条件访问命名位置，仅允许从 IP 地址范围或国家/地区的特定逻辑分组进行访问。

- [如何在 Azure 中配置命名位置](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="39-use-azure-active-directory"></a>3.9：使用 Azure Active Directory

**指南**：使用 Azure Active Directory (Azure AD) 作为 Azure 资源（例如事件中心）的中央身份验证和授权系统。 这样就可以对用于管理的敏感资源进行 Azure 基于角色的访问控制 (Azure RBAC)。

- [ 如何创建和配置 Azure AD 实例](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [使用 Azure AD 授权访问事件中心资源](authorize-access-azure-active-directory.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10：定期审查和协调用户访问

**指导**：Azure Active Directory (Azure AD) 提供了日志来帮助你发现过时的帐户。 此外，请使用 Azure 标识访问评审来有效管理组成员身份、对企业应用程序的访问和角色分配。 可以定期评审用户的访问权限，确保只有适当的用户才持续拥有访问权限。

此外，请定期旋转事件中心的共享访问签名。

- [了解 Azure AD 报告](/azure/active-directory/reports-monitoring/)

- [如何使用 Azure 标识访问评审](../active-directory/governance/access-reviews-overview.md)

- [了解事件中心的共享访问签名](authenticate-shared-access-signature.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11：监视尝试访问已停用凭据的行为

**指南**：你有权访问 Azure Active Directory (Azure AD) 登录活动、审核和风险事件日志源，这允许你与任何第三方系统信息、事件管理解决方案或监视工具集成。

可以通过为 Azure AD 用户帐户创建诊断设置，并将审核日志和登录日志发送到 Log Analytics 工作区，来简化此过程。 可以在 Log Analytics 中配置所需的日志警报。

- [如何将 Azure 活动日志集成到 Azure Monitor](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

- [使用 Azure AD 授权访问事件中心资源](authorize-access-azure-active-directory.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12：针对帐户登录行为偏差发出警报

**指南**：使用 Azure Active Directory (Azure AD) 中的标识保护和风险检测功能，配置对检测到的与已启用事件中心相关的可疑操作的自动响应。 应通过 Azure Sentinel 启用自动响应，以实现组织的安全响应。

- [如何查看 Azure AD 风险登录](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [如何配置和启用标识保护风险策略](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [如何加入 Azure Sentinel](../sentinel/quickstart-onboard.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13：在支持场合下为 Microsoft 提供对相关客户数据的访问权限

**指南**：当前不可用;事件中心尚不支持客户密码箱。

- [支持客户密码箱的服务列表](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability)

**责任**：客户

**Azure 安全中心监视**：无

## <a name="data-protection"></a>数据保护

[有关详细信息，请参阅 *Azure 安全基线：* 数据保护](../security/benchmarks/security-control-data-protection.md)。

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1：维护敏感信息的清单

**指导**：对与事件中心相关的资源使用标记，以便跟踪那些存储或处理敏感信息的 Azure 资源。

- [如何创建和使用标记](/azure/azure-resource-manager/resource-group-using-tags)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2：隔离存储或处理敏感信息的系统

**指南**：实施单独的订阅，还可以选择通过管理组进行开发、测试和生产。 事件中心命名空间应由启用了服务终结点的虚拟网络分隔并进行相应的标记。

还可以使用防火墙保护 Azure 事件中心命名空间。 Azure 事件中心支持使用基于 IP 的访问控制来提供入站防火墙支持。 可以通过 Azure 门户、Azure 资源管理器模板、Azure CLI 或 Azure PowerShell 设置防火墙规则。

- [如何创建其他 Azure 订阅](/azure/billing/billing-create-subscription)

- [如何创建管理组](/azure/governance/management-groups/create)

- [为 Azure 事件中心命名空间配置 IP 防火墙规则](event-hubs-ip-filtering.md)

- [如何创建和使用标记](/azure/azure-resource-manager/resource-group-using-tags)

- [如何创建虚拟网络](../virtual-network/quick-create-portal.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3：监视和阻止未经授权的敏感信息传输

**指导**：使用虚拟机访问事件中心时，请利用虚拟网络、服务终结点、事件中心防火墙、网络安全组和服务标记来降低数据外泄的可能性。

Microsoft 会管理 Azure 事件中心的底层基础结构，并实施严格的控制措施来防止客户数据丢失或泄露。

- [为 Azure 事件中心命名空间配置 IP 防火墙规则](event-hubs-ip-filtering.md)

- [了解 Azure 事件中心的虚拟网络服务终结点](event-hubs-service-endpoints.md)

- [将 Azure 事件中心与 Azure 专用链接集成](private-link-service.md)

- [了解网络安全组和服务标记](/azure/virtual-network/security-overview)

- [了解 Azure 中的客户数据保护](../security/fundamentals/protection-customer-data.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5：使用有效的发现工具识别敏感数据

**指导**：数据标识、分类和丢失防护功能尚不适用于 Azure 事件中心。 如果需要出于合规性目的使用这些功能，请实施第三方解决方案。

对于 Microsoft 管理的底层平台，Microsoft 会将所有客户内容视为敏感数据，并会全方位地防范客户数据丢失和遭到透露。 为了确保 Azure 中的客户数据保持安全，Microsoft 实施并维护了一套可靠的数据保护控制措施和功能。

- [了解 Azure 中的客户数据保护](../security/fundamentals/protection-customer-data.md)

**责任**：共享

**Azure 安全中心监视**：无

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6：使用基于角色的访问控制来控制对资源的访问

**指南**： Azure 事件中心支持使用 Azure Active Directory (Azure AD) 向事件中心资源的请求授权。 可以通过 Azure AD 使用 Azure 基于角色的访问控制 (Azure RBAC) 向安全主体授予权限，该安全主体可能是用户，也可能是应用程序服务主体。

- [了解 Azure RBAC 和 Azure 事件中心的可用角色](authorize-access-azure-active-directory.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8：静态加密敏感信息

**指南**： Azure 事件中心支持通过 Microsoft 管理的密钥或客户托管的密钥来加密静态数据。 此功能使你能够创建、轮换、禁用和撤消对用于静态 Azure 事件中心数据加密的客户托管密钥的访问权限。

- [如何配置用于加密 Azure 事件中心的客户托管密钥](configure-customer-managed-key.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9：记录对关键 Azure 资源的更改并对此类更改发出警报

**指导**：将 Azure Monitor 与 Azure 活动日志结合使用，以创建在 Azure 事件中心的生产实例和其他关键或相关资源发生更改时发出的警报。

- [如何针对 Azure 活动日志事件创建警报](/azure/azure-monitor/platform/alerts-activity-log)

**责任**：客户

**Azure 安全中心监视**：无

## <a name="inventory-and-asset-management"></a>清单和资产管理

[有关详细信息，请参阅 *Azure 安全基线：* 清单和资产管理](../security/benchmarks/security-control-inventory-asset-management.md)。

### <a name="61-use-automated-asset-discovery-solution"></a>6.1：使用自动化资产发现解决方案

**指南**：使用 Azure 资源关系图可查询和发现所有资源 (包括订阅中) Azure 事件中心命名空间。 确保你在租户中拥有适当的（读取）权限，并且可以枚举所有 Azure 订阅，以及订阅中的资源。

- [如何使用 Azure Resource Graph 创建查询](../governance/resource-graph/first-query-portal.md)

- [如何查看 Azure 订阅](/powershell/module/az.accounts/get-azsubscription)

- [了解 Azure RBAC](../role-based-access-control/overview.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="62-maintain-asset-metadata"></a>6.2：维护资产元数据

**指导**：将标记应用到 Azure资源，以便有条理地将元数据组织成某种分类。

- [如何创建和使用标记](/azure/azure-resource-manager/resource-group-using-tags)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="63-delete-unauthorized-azure-resources"></a>6.3：删除未经授权的 Azure 资源

**指导**：使用标记、管理组和单独订阅（如果适用）来组织和跟踪 Azure 事件中心命名空间和相关资源。 定期核对清单，确保及时地从订阅中删除未经授权的资源。

- [如何创建其他 Azure 订阅](/azure/billing/billing-create-subscription)

- [如何创建管理组](/azure/governance/management-groups/create)

- [如何创建和使用标记](/azure/azure-resource-manager/resource-group-using-tags)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5：监视未批准的 Azure 资源

**指导**：在 Azure Policy 中使用以下内置策略定义，对可以在客户订阅中创建的资源类型施加限制：

- 不允许的资源类型

- 允许的资源类型

此外，使用 Azure 资源关系图查询/发现订阅中的资源。

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [如何使用 Azure Graph 创建查询](../governance/resource-graph/first-query-portal.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="69-use-only-approved-azure-services"></a>6.9：仅使用已批准的 Azure 服务

**指导**：在 Azure Policy 中使用以下内置策略定义，对可以在客户订阅中创建的资源类型施加限制：

- 不允许的资源类型

- 允许的资源类型

访问所引用的链接可获得更多信息。

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

**指导**：定义和实施适用于 Azure 事件中心部署的标准安全配置。 使用 **Microsoft EventHub** 命名空间中的 Azure 策略别名创建自定义策略，以审核或强制实施配置。 还可以为 Azure 事件中心利用内置策略定义，例如：

- 应启用事件中心内的诊断日志

- 事件中心应使用虚拟网络服务终结点

访问所引用的链接可获得更多信息。

- [适用于事件中心命名空间的 Azure 内置策略](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#event-hub)

- [如何查看可用的 Azure Policy 别名](/powershell/module/az.resources/get-azpolicyalias)

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3：维护安全的 Azure 资源配置

**指南**：使用 Azure 策略 [拒绝] 和 [部署（如果不存在）影响跨已启用事件中心的资源强制实施安全设置。 

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

 
- [有关 Azure Policy 效果的详细信息](../governance/policy/concepts/effects.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7：部署 Azure 资源的配置管理工具

**指导**：在“Microsoft.EventHub”命名空间中使用 Azure Policy 别名创建自定义策略，以审核、强制实施系统配置并对其发出警报。 另外，开发一个用于管理策略例外的流程和管道。

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9：为 Azure 资源实施自动配置监视

**指导**：在“Microsoft.EventHub”命名空间中使用 Azure Policy 别名创建自定义策略，以审核、强制实施系统配置并对其发出警报。 使用 Azure 策略 [audit]、[拒绝] 和 [部署（如果不存在]）效果自动强制执行 Azure 事件中心部署和相关资源的配置。

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="711-manage-azure-secrets-securely"></a>7.11：安全管理 Azure 机密

**指导**：对于在 Azure 应用服务上运行的用于访问事件中心的 Azure 虚拟机或 Web 应用程序，请将托管服务标识与 Azure Key Vault 结合使用，以简化和保护 Azure 事件中心部署的共享访问签名管理。 确保为启用了软删除的配置 Key Vault。

- [使用 Azure Active Directory (Azure AD) 访问事件中心资源来验证托管标识](https://docs.microsoft.com/azure/event-hubs/authenticate-managed-identity?tabs=latest)

- [为事件中心配置客户托管的密钥](configure-customer-managed-key.md)

- [如何与 Azure 托管标识集成](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [如何创建 Key Vault](/azure/key-vault/quick-create-portal)

- [如何使用托管标识提供 Key Vault 身份验证](/azure/key-vault/managed-identity)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="712-manage-identities-securely-and-automatically"></a>7.12：安全自动管理标识

**指南**：对于正在用于访问事件中心 Azure App Service 上运行的 Azure 虚拟机或 web 应用程序，请将托管服务标识与 Azure Key Vault 结合使用，以简化和保护 Azure 事件中心。 确保为启用了软删除的配置 Key Vault。

使用托管标识在 Azure Active Directory (Azure AD) 中为 Azure 服务提供一个自动托管的标识。 使用托管标识可以向支持 Azure AD 身份验证的任何服务（包括 Azure Key Vault）进行身份验证，无需在代码中放入任何凭据。

- [使用 Azure AD 验证托管标识，以访问事件中心资源](https://docs.microsoft.com/azure/event-hubs/authenticate-managed-identity?tabs=latest)

- [为事件中心配置客户托管的密钥](configure-customer-managed-key.md)

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

**指导**：预扫描要上传到非计算 Azure 资源的任何内容，例如 Azure 事件中心、应用服务、Data Lake Storage、Blob 存储、Azure Database for PostgreSQL 等。Microsoft 无法访问这些实例中的数据。

Microsoft 反恶意软件已在支持 Azure 服务（例如 Azure Cache for Redis）的基础主机上启用，但它不会针对客户内容运行。

**责任**：客户

**Azure 安全中心监视**：无

## <a name="data-recovery"></a>数据恢复

[有关详细信息，请参阅 *Azure 安全基线：* 数据恢复](../security/benchmarks/security-control-data-recovery.md)。

### <a name="91-ensure-regular-automated-back-ups"></a>9.1：确保定期执行自动备份

**指南**：配置 Azure 事件中心的异地灾难恢复。 当整个 Azure 区域或数据中心（如果未使用可用性区域）遭遇停机时，在不同区域或数据中心中继续进行数据处理就显得至关重要。 在这种情况下，异地灾难恢复和异地复制对于任何企业而言都是至关重要的功能。 Azure 事件中心支持命名空间级别的异地灾难恢复和异地复制。 

- [了解 Azure 事件中心的异地灾难恢复](https://docs.microsoft.com/azure/event-hubs/event-hubs-geo-dr#availability-zones)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2：执行完整系统备份，并备份客户管理的所有密钥

**指南**： Azure 事件中心通过 azure 存储服务加密 (azure SSE) 提供静态数据的加密。 事件中心依赖于 Azure 存储来存储数据，默认情况下，使用 Microsoft 托管密钥对存储在 Azure 存储中的所有数据进行加密。 如果使用 Azure Key Vault 来存储客户管理的密钥，请确保定期自动备份密钥。

请确保通过以下 PowerShell 命令定期自动备份你的 Key Vault 机密：Backup-AzKeyVaultSecret

- [如何配置客户托管的密钥以加密静态 Azure 事件中心数据](configure-customer-managed-key.md)

- [如何备份 Key Vault 机密](/powershell/module/azurerm.keyvault/backup-azurekeyvaultsecret)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3：验证所有备份，包括客户管理的密钥

**指导**：测试对备份的客户管理的密钥进行还原。

- [如何在 Azure 中还原密钥保管库密钥](/powershell/module/az.keyvault/restore-azkeyvaultkey)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4：确保保护备份和客户管理的密钥

**指南**：在 Key Vault 中启用软删除，以防止意外或恶意删除密钥。 Azure 事件中心需要客户管理的密钥才能进行软删除，并且不会清除已配置的密钥。

为用于捕获事件中心数据的 Azure 存储帐户配置软删除。 请注意，Azure Data Lake Storage 第2代尚不支持此功能。

- [如何在 Key Vault 中启用软删除](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

- [设置密钥保管库与密钥](configure-customer-managed-key.md)

- [Azure 存储 Blob 的软删除](https://docs.microsoft.com//azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

**责任**：客户

**Azure 安全中心监视**：无

## <a name="incident-response"></a>事件响应

[有关详细信息，请参阅 *Azure 安全基线：* 事件响应](../security/benchmarks/security-control-incident-response.md)。

### <a name="101-create-an-incident-response-guide"></a>10.1：创建事件响应指导

**指导**：确保在书面的事件响应计划中定义人员职责，以及事件处理/管理的各个阶段。

- [如何在 Azure 安全中心配置工作流自动化](../security-center/security-center-planning-and-operations-guide.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2：创建事件评分和优先级设定过程

**指导**：安全中心将为警报分配严重性来帮助你确定每条警报的处理优先顺序，以便在资源泄密时可以立即采取措施。 严重性取决于安全中心对调查结果或用于发出警报的分析的确信程度，以及对导致警报的活动背后存在恶意意图的确信程度。

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

**指导**：请遵循 Microsoft 云渗透测试互动规则，确保你的渗透测试不违反 Microsoft 政策。 使用 Microsoft 红队演练策略和执行，以及针对 Microsoft 托管云基础结构、服务和应用程序执行的现场渗透测试。 

- [参与的渗透测试规则](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft 云红色组合](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**责任**：客户

**Azure 安全中心监视**：无

## <a name="next-steps"></a>后续步骤

- 参阅 [Azure 安全基准 V2 概述](/azure/security/benchmarks/overview)
- 详细了解 [Azure 安全基线](/azure/security/benchmarks/security-baselines-overview)
