---
title: Azure 负载均衡器的 Azure 安全基线
description: Azure 负载均衡器安全基线为实现 Azure 安全基准中指定的安全建议提供过程指南和资源。
author: msmbaldwin
ms.service: load-balancer
ms.topic: conceptual
ms.date: 09/28/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 1e20ed632ee5b100098f7f35bcca16d157668cad
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101721355"
---
# <a name="azure-security-baseline-for-azure-load-balancer"></a>Azure 负载均衡器的 Azure 安全基线

Microsoft Azure 负载均衡器的 Azure 安全基线包含的建议可帮助你提高部署的安全状况。 此服务的基线摘自 [Azure 安全基准版本 1.0](../security/benchmarks/overview.md)，其中提供了有关如何根据我们的最佳做法指导保护 Azure 上的云解决方案的建议。 有关详细信息，请参阅 [Azure 安全基线概述](../security/benchmarks/security-baselines-overview.md)。

## <a name="network-security"></a>网络安全性

[有关详细信息，请参阅 *Azure 安全基线：* 网络安全](../security/benchmarks/security-control-network-security.md)。

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1：保护虚拟网络中的 Azure 资源

**指导**：使用内部 Azure 负载均衡器，仅允许从特定虚拟网络或对等互连虚拟网络内部到后端资源的流量，不会将内容暴露到 Internet。 实现采用源网络地址转换 (SNAT) 的外部负载均衡器，以伪装后端资源的 IP 地址，防止直接暴露到 Internet。

Azure 提供了两种类型的负载均衡器产品/服务：“标准”类型和“基本”类型。 请为所有生产工作负荷使用标准负载均衡器。 实现网络安全组，只允许访问应用程序的受信任端口和 IP 地址范围。 如果没有为后端虚拟机的后端子网或 NIC 分配网络安全组，则不允许流量从负载均衡器发送到这些资源。 使用标准负载均衡器，提供出站规则以使用网络安全组定义出站 NAT。 查看这些出站规则以优化出站连接的行为。 

建议为生产工作负荷使用标准负载均衡器。通常，基本负载均衡器仅用于测试，因为基本类型在默认情况下对来自 Internet 的连接开放，不需要网络安全组便可进行操作。 

- [Azure 中的出站连接](load-balancer-outbound-connections.md)

- [升级 Azure 公共负载均衡器](./upgrade-basic-standard.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2：监视和记录虚拟网络、子网和 NIC 的配置与流量

**指导**：负载均衡器是一项直通服务，因为它依赖于应用于后端资源的网络安全组规则和所配置的出站规则来控制 Internet 访问。

通过负载均衡器的“出站规则”边栏选项卡和“负载均衡规则”边栏选项卡（你可能已在其中启用了隐式出站规则），查看为标准负载均衡器配置的出站规则。

监视出站连接数，以跟踪资源到达 Internet 的频率。 

使用安全中心并遵循网络保护建议来帮助保护 Azure 网络资源。

遵循针对后端资源的安全建议，启用网络安全组流日志，并将日志发送到 Azure 存储帐户进行审核。

另外，请将流日志发送到 Log Analytics 工作区，然后使用流量分析来提供有关 Azure 云中流量模式的见解。 流量分析的优势包括能够可视化网络活动、识别热点和安全威胁、了解通信流模式，以及查明网络不当配置。

- [如何启用网络安全组流日志](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [如何启用和使用流量分析](../network-watcher/traffic-analytics.md)

- [了解 Azure 安全中心提供的网络安全性](../security-center/security-center-network-recommendations.md)

- [如何检查出站连接统计信息](./load-balancer-standard-diagnostics.md#how-do-i-check-my-outbound-connection-statistics)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="13-protect-critical-web-applications"></a>1.3：保护关键 Web 应用程序

**指南**：使用负载均衡器，通过出站规则和网络安全组显式定义 internet 连接和有效源 ip，以使用 Microsoft 的威胁情报来保护 web 应用程序。

- [集成 Azure 防火墙](../firewall/integrate-lb.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4：拒绝与已知恶意的 IP 地址进行通信

**指南**：在 Azure 虚拟网络上启用 Azure 分布式拒绝服务 (DDoS) 标准保护，防止 DDoS 攻击。 

在组织的每个网络边界上部署 Azure 防火墙，启用基于威胁情报的筛选并将其配置为针对恶意网络流量执行“发出警报并拒绝”操作。

 

使用安全中心威胁防护来检测与已知的恶意 IP 地址的通信。 

默认情况下，标准负载均衡器是安全的，并且是专用和隔离的虚拟网络的一部分。 除非由网络安全组打开以明确允许允许的流量，并且不允许已知的恶意 IP 地址，否则会关闭入站流。 除非虚拟机资源的子网或 NIC 上的网络安全组位于负载均衡器后面，否则不允许流量到达此资源。 

在组织的每个网络边界上部署 Azure 防火墙，并为恶意网络流量启用基于威胁智能的筛选并将其配置为 "警报并拒绝"，以防止恶意 IP 地址遭受攻击。 

实现将 Azure 防火墙与负载均衡器集成的指导。

使用安全中心威胁防护功能检测与已知的恶意 IP 地址的通信。 

安全中心 (标准层) 提供实时虚拟机访问，并将允许的源 IP 地址配置为仅允许来自已批准的 IP 地址/范围的访问。
 

使用安全中心的自适应网络强化功能推荐网络安全组配置，这些配置基于实际流量和威胁智能限制端口和源 Ip。
 

- [使用 Azure 门户管理 Azure DDoS 防护标准](../ddos-protection/manage-ddos-protection.md)

- [基于 Azure 防火墙威胁智能的筛选](../firewall/threat-intel.md)

- [Azure 安全中心的威胁防护](../security-center/azure-defender.md)

- [使用实时访问保护管理端口](../security-center/security-center-just-in-time.md)

- [Azure 安全中心内的自适应网络强化](../security-center/security-center-adaptive-network-hardening.md)

- [将 Azure 防火墙与负载均衡器集成](../firewall/overview.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="15-record-network-packets"></a>1.5：记录网络数据包

**指导**：启用网络观察程序数据包捕获来调查异常活动。

- [如何创建网络观察程序实例](../network-watcher/network-watcher-create.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6：部署基于网络的入侵检测/入侵防护系统 (IDS/IPS)

**指导**：实现 Azure 市场中的一种产品/服务，该产品/服务应支持包含有效负载检查功能的 ID/IPS 功能，以支持负载均衡器的环境。 

如果不需要进行有效负载检查，请使用 Azure 防火墙威胁情报。 使用基于 Azure 防火墙威胁情报的筛选功能，针对进出已知恶意 IP 地址和域的流量发出警报并/或阻止该流量。 IP 地址和域源自 Microsoft 威胁智能源。

在组织的每个网络边界上部署所选的防火墙解决方案，以检测并/或阻止恶意流量。

- [Azure 市场](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

- [如何部署 Azure 防火墙](../firewall/tutorial-firewall-deploy-portal.md)

- [如何配置 Azure 防火墙警报](../firewall/threat-intel.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="17-manage-traffic-to-web-applications"></a>1.7：管理发往 Web 应用程序的流量

**指导**：通过出站规则和网络安全组以及负载均衡器显式定义 Internet 连接和有效的源 IP，以使用 Microsoft 的威胁情报功能来保护你的 Web 应用程序。

- [集成 Azure 防火墙](../firewall/integrate-lb.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8：最大程度地降低网络安全规则的复杂性和管理开销

**指导**：创建安全规则时，使用服务标记代替特定的 IP 地址。 在规则的源或目标字段中指定服务标记名称，以允许或拒绝相应服务的流量。 

Microsoft 会管理服务标记包含的地址前缀，并会在地址发生更改时自动更新服务标记。 

默认情况下，每个网络安全组都包含服务标记 AzureLoadBalancer，以允许运行状况探测流量。 

有关可在网络安全组规则中使用的所有服务标记，请参阅 Azure 文档。

- [可用服务标记](../virtual-network/service-tags-overview.md#available-service-tags)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9：维护网络设备的标准安全配置

**指导**：使用 Azure Policy 为网络资源定义并实施标准安全配置。

使用 Azure 蓝图可以通过在单个蓝图定义中打包关键环境项目（例如 Azure 资源管理器模板、Azure RBAC 控件和策略）来简化大规模的 Azure 部署。 

将蓝图应用到新的订阅，并通过版本控制来微调控制和管理。

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [用于网络的 Azure Policy 示例](../governance/policy/samples/built-in-policies.md#network)

- [如何创建 Azure 蓝图](../governance/blueprints/create-blueprint-portal.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="110-document-traffic-configuration-rules"></a>1.10：阐述流量配置规则

**指导**：为网络安全组以及与网络安全和通信流相关的其他资源使用资源标记。 

对于单独的网络安全组规则，请使用“说明”字段来记录允许流入/流出网络的流量的规则。

实施与标记相关的任何内置 Azure Policy 定义（例如“需要标记及其值”），这将确保使用标记创建所有资源，并在现有资源不带标记时发出通知。

使用 Azure PowerShell 或 Azure CLI，基于资源的标记查找资源或对其执行操作。

- [如何创建和使用标记](../azure-resource-manager/management/tag-resources.md)

- [如何创建 Azure 虚拟网络](../virtual-network/quick-create-portal.md)

- [如何使用网络安全组规则筛选网络流量](../virtual-network/tutorial-filter-network-traffic.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11：使用自动化工具来监视网络资源配置和检测更改

**指导**：使用 Azure 活动日志监视网络资源配置，并检测 Azure 资源的更改。 

在 Azure Monitor 中创建警报，以便在关键资源发生更改时通知你。

- [如何查看和检索 Azure 活动日志事件](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [如何在 Azure Monitor 中创建警报](../azure-monitor/alerts/alerts-activity-log.md)

**Azure 安全中心监视**：是

**责任**：客户

## <a name="logging-and-monitoring"></a>日志记录和监视

[有关详细信息，请参阅 *Azure 安全基线：* 日志记录和监视](../security/benchmarks/security-control-logging-monitoring.md)。

### <a name="22-configure-central-security-log-management"></a>2.2：配置中心安全日志管理

**指导**：通过查看订阅中的活动日志，查看对负载均衡器的出站规则和网络安全组的更改。 

查看内部主机日志，以确保后端资源是安全的。

将这些日志导出到 Log Analytics 或其他存储平台。 在 Azure Monitor 中，使用 Log Analytics 工作区来查询和执行分析，并使用 Azure 存储帐户进行长期存档存储。

根据组织业务需求，启用此数据并将其上传到 Azure Sentinel 或第三方 SIEM。

- [如何加入 Azure Sentinel](../sentinel/quickstart-onboard.md)

- [如何使用 Azure Monitor 收集平台日志和指标](../azure-monitor/essentials/diagnostic-settings.md)

- [如何使用 Azure Monitor 收集 Azure 虚拟机内部主机日志](../azure-monitor/vm/quick-collect-azurevm.md)

- [如何开始使用 Azure Monitor 和第三方 SIEM 集成](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

- [平台活动日志](../azure-monitor/essentials/activity-log.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：为 Azure 资源启用审核日志记录

**指导**：查看在基本负载均衡器的活动日志中捕获的控制和管理平面日志记录和审核信息。 这些捕获设置是默认启用的。 

使用活动日志监视资源上的操作，以查看所有活动及其状态。 

使用活动日志确定对订阅中的资源执行的操作： 

- 谁启动了该操作
- 操作何时发生
- 操作的状态

- 其他可能有助于研究操作的属性的值

通过 Azure PowerShell、Azure 命令行接口 (CLI)、Azure REST API 或 Azure 门户检索活动日志中的信息。 

通过 Azure Monitor 针对标准负载均衡器配置功能实施多维诊断。  这包括适用于安全性的指标，包括有关源网络地址转换 (SNAT) 连接、端口的信息。 同时还提供了有关 SYN（同步）数据包和数据包计数器的其他指标。 

通过 API 以编程方式检索多维指标，并通过“所有指标”选项将它们写入到存储帐户。

将 Azure 审核日志内容包与 Microsoft Power BI 配合使用以借助预配置的仪表板分析你的数据，或者根据你的要求来自定义视图。

将日志流式传输到事件中心或 Log Analytics 工作区。 还可以从 Azure Blob 存储中提取它们并在 Excel 和 Power BI 等各种工具中查看。 

根据你的业务需求，将和机载数据启用到 Azure Sentinel 或第三方 SIEM。

- [查看包含分步说明的此文章，了解使用资源管理器进行的审核操作中详细介绍的每种方法](../azure-resource-manager/management/view-activity-logs.md)

- [公共基本负载均衡器的 Azure Monitor 日志](./load-balancer-monitor-log.md)

- [查看活动日志以监视对资源的操作](../azure-resource-manager/management/view-activity-logs.md)

- [通过 API 以编程方式检索多维指标](./load-balancer-standard-diagnostics.md#retrieve-multi-dimensional-metrics-programmatically-via-apis)

- [如何开始将 Azure Monitor 与第三方 SIEM 集成](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="25-configure-security-log-storage-retention"></a>2.5：配置安全日志存储保留期

**指导**：活动日志是默认启用的，在 Azure 的事件日志存储中保留 90 天。 在 Azure Monitor 中，根据组织的合规性规则设置 Log Analytics 工作区保留期。 将 Azure 存储帐户用于长期存储和存档存储。

- [“查看活动日志以监视对资源的操作”一文](../azure-resource-manager/management/view-activity-logs.md)

- [更改 Log Analytics 中的数据保留期](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

- [如何为 Azure 存储帐户日志配置保留策略](../storage/common/manage-storage-analytics-logs.md#configure-logging)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="26-monitor-and-review-logs"></a>2.6：监视和审查日志

**指导**：使用 Azure 门户中的“负载均衡器”页面和 Azure Monitor 下的“资源运行状况”页面监视、管理标准负载均衡器资源并对其进行故障排除。 适用于安全性的指标包括有关源网络地址转换 (SNAT) 连接、端口的信息。 同时还提供了有关 SYN（同步）数据包和数据包计数器的其他指标。 

使用 Azure Monitor 通过标准、外部和内部负载均衡器的多维指标来查看终结点运行状况探测状态。 通过 API 以编程方式收集这些指标，并通过“所有指标”选项将它们写入到存储帐户。

Azure Monitor 日志不适用于内部基本负载均衡器。 

使用 Azure Monitor 查看基本外部负载均衡器的每个后端池的汇总运行状况探测状态，例如，后端池中由于运行状况探测失败而未收到负载均衡器发出的请求的实例数。 

通过 Azure Operational Insights 实施日志记录，以提供有关负载均衡器运行状况的统计信息。 

使用活动日志查看有关 Azure 订阅中的资源及其状态的警报并监视针对它们的操作。 活动日志默认情况下启用，并且可以在 Azure 门户中查看。 

将 Microsoft Power BI 与 Azure 审核日志内容包一起使用，并使用预配置的仪表板来分析数据。 根据业务需求自定义视图，使之满足你的要求。 

将日志流式传输到事件中心或 Log Analytics 工作区。 还可以从 Azure Blob 存储中提取它们并在 Excel 和 Power BI 等各种工具中查看。 可以将和机载数据启用到 Azure Sentinel 或第三方 SIEM。

- [负载均衡器运行状况探测](./load-balancer-custom-probe-overview.md)

- [Azure 监视器 REST API](/rest/api/monitor)

- [如何通过 REST API 检索指标](/rest/api/monitor/metrics/list)

- [通过指标、警报和资源运行状况进行标准负载均衡器诊断](./load-balancer-standard-diagnostics.md)

- [公共基本负载均衡器的 Azure Monitor 日志](./load-balancer-monitor-log.md)

- [在 Azure 门户中查看负载均衡器指标](./load-balancer-standard-diagnostics.md#view-your-load-balancer-metrics-in-the-azure-portal)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7：针对异常活动启用警报

**指导**：使用安全中心和 Log Analytics 工作区监视安全日志和事件中与负载均衡器相关的异常活动并对其发出警报。

启用和板数据到 Azure Sentinel 或第三方 SIEM 工具。

- [如何加入 Azure Sentinel](../sentinel/quickstart-onboard.md)

- [如何在 Azure 安全中心管理警报](../security-center/security-center-managing-and-responding-alerts.md)

- [如何针对 Log Analytics 日志数据发出警报](../azure-monitor/alerts/tutorial-response.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="28-centralize-anti-malware-logging"></a>2.8：集中管理反恶意软件日志记录

**指导**：不适用于 Azure 负载均衡器。 此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="29-enable-dns-query-logging"></a>2.9：启用 DNS 查询日志记录

**指导**：不适用，因为 Azure 负载均衡器是一种核心网络服务，不会进行 DNS 查询。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="210-enable-command-line-audit-logging"></a>2.10：启用命令行审核日志记录

**指导**：不适用于 Azure 负载均衡器，因为此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="identity-and-access-control"></a>标识和访问控制

[有关详细信息，请参阅 *Azure 安全基线：* 标识和访问控制](../security/benchmarks/security-control-identity-access-control.md)。

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1：维护管理帐户的清单

**指导**：借助 Azure 基于角色的访问控制 (Azure RBAC)，可以通过角色分配管理对 Azure 资源（例如负载均衡器）的访问。 可将这些角色分配给用户、组、服务主体和托管标识。 

某些资源具有预定义的内置角色，可以通过工具（例如 Azure CLI、Azure PowerShell 或 Azure 门户）来清点这些角色。

- [如何使用 PowerShell 获取 Azure AD 中的目录角色](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [如何使用 PowerShell 获取 Azure AD 中目录角色的成员](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Azure 安全中心监视**：是

**责任**：客户

## <a name="data-protection"></a>数据保护

[有关详细信息，请参阅 *Azure 安全基线：* 数据保护](../security/benchmarks/security-control-data-protection.md)。

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4.6：使用 Azure RBAC 管理对资源的访问

**指导**：使用 Azure RBAC 控制对负载均衡器资源的访问。

- [如何配置 Azure RBAC](../role-based-access-control/role-assignments-portal.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7：使用基于主机的数据丢失防护来强制实施访问控制

**指导**：负载均衡器是一种直通服务，不会存储客户数据。 它是由 Microsoft 管理的底层平台的一部分。 

Microsoft 将所有客户内容视为敏感内容，并在很大程度上防范客户数据丢失和公开。 

为了确保 Azure 中的客户数据始终安全，Microsoft 实施并维护了一套可靠的数据保护控制机制和功能。 

- [了解 Azure 中的客户数据保护](../security/fundamentals/protection-customer-data.md)

**Azure 安全中心监视**：不适用

**责任**：共享

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9：记录对关键 Azure 资源的更改并对此类更改发出警报

**指导**：将 Azure Monitor 与 Azure 活动日志结合使用，以创建在关键 Azure 资源（例如，用于重要生产工作负荷的负载均衡器）发生更改时发出的警报。

- [如何针对 Azure 活动日志事件创建警报](../azure-monitor/alerts/alerts-activity-log.md)

**Azure 安全中心监视**：是

**责任**：客户

## <a name="inventory-and-asset-management"></a>库存和资产管理

[有关详细信息，请参阅 *Azure 安全基线：* 清单和资产管理](../security/benchmarks/security-control-inventory-asset-management.md)。

### <a name="61-use-automated-asset-discovery-solution"></a>6.1：使用自动化资产发现解决方案

**指导**：使用 Azure Resource Graph 来查询和发现订阅中的所有资源（例如计算、存储、网络、端口、协议等）。 建议使用 Azure 资源管理器来创建和使用当前资源。 

确保租户中具有适当的（读取）权限，并枚举所有 Azure 订阅以及订阅中的资源。

- [如何使用 Azure Resource Graph 浏览器创建查询](../governance/resource-graph/first-query-portal.md)

- [如何查看 Azure 订阅](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

- [了解 Azure RBAC](../role-based-access-control/overview.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="62-maintain-asset-metadata"></a>6.2：维护资产元数据

**指导**：将标记应用于 Azure资源，以便有条理地将元数据组织成某个分类。

- [如何创建和使用标记](../azure-resource-manager/management/tag-resources.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="63-delete-unauthorized-azure-resources"></a>6.3：删除未经授权的 Azure 资源

**指导**：在适用的情况下，请使用标记、管理组和单独的订阅来组织和跟踪资产。 

定期核对清单，确保及时地从订阅中删除未经授权的资源。

- [如何创建其他 Azure 订阅](../cost-management-billing/manage/create-subscription.md)

- [如何创建管理组](../governance/management-groups/create-management-group-portal.md)

- [如何创建和使用标记](../azure-resource-manager/management/tag-resources.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4：定义并维护已批准 Azure 资源的清单

**指导**：根据你的组织需求创建已批准的 Azure 资源的列表，你可以将该列表用作允许列表机制。 这样便可以在任何新提供的 Azure 服务经过组织的典型安全评估流程的正式审核和批准后将其载入。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5：监视未批准的 Azure 资源

**指导**：使用 Azure Policy 对可以在订阅中创建的资源类型施加限制。

使用 Azure Resource Graph 在所拥有的订阅中查询和发现资源。 

确保环境中存在的所有 Azure 资源已获得批准。

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [如何使用 Azure Resource Graph 浏览器创建查询](../governance/resource-graph/first-query-portal.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11：限制用户与 Azure 资源管理器进行交互的能力

**指南**：通过为“Microsoft Azure 管理”应用配置“阻止访问”，使用 Azure AD 条件访问来限制用户与 Azure 资源管理器交互的能力。

- [如何配置条件访问来阻止对 Azure 资源管理器的访问](../role-based-access-control/conditional-access-azure-management.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13：以物理或逻辑方式隔离高风险应用程序

**指导**：对于业务运营所需的但可能会给组织带来更高风险的软件，应将其隔离在其自己的虚拟机和/或虚拟网络中，并通过 Azure 防火墙或网络安全组进行充分的保护。

- [如何创建虚拟网络](../virtual-network/quick-create-portal.md)

- [如何创建具有安全配置的网络安全组](../virtual-network/tutorial-filter-network-traffic.md)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="secure-configuration"></a>安全配置

[有关详细信息，请参阅 *Azure 安全基线：* 安全配置](../security/benchmarks/security-control-secure-configuration.md)。

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1：为所有 Azure 资源建立安全配置

**指导**：使用 Azure Policy 别名创建自定义策略，审核或强制实施 Azure 资源的配置。 使用内置的 Azure Policy 定义。

Azure 资源管理器能够以 JavaScript 对象表示法 (JSON) 导出模板，应该对其进行检查，以确保配置满足组织的安全要求。

将 Azure 资源管理器模板导出为 JavaScript 对象表示法 (JSON) 格式，并定期对其进行检查，以确保这些配置符合组织的安全要求。 

将安全中心的建议作为 Azure 资源的安全配置基线来实施。 

- [如何查看可用的 Azure Policy 别名](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [教程：创建和管理策略以强制实施符合性](../governance/policy/tutorials/create-and-manage.md)

- [在 Azure 门户中将单资源和多资源导出到模板](../azure-resource-manager/templates/export-template-portal.md)

- [安全建议 - 参考指南](../security-center/recommendations-reference.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3：维护安全的 Azure 资源配置

**指南**：使用 Azure Policy“[拒绝]”和“[不存在则部署]”对不同的 Azure 资源强制实施安全设置。  此外，你还可以使用 Azure 资源管理器模板来维护组织所需的 Azure 资源的安全配置。 

- [了解 Azure Policy 效果](../governance/policy/concepts/effects.md)

- [创建和管理策略以强制实施符合性](../governance/policy/tutorials/create-and-manage.md)

- [Azure 资源管理器模板概述](../azure-resource-manager/templates/overview.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5：安全存储 Azure 资源的配置

**指导**：使用 Azure DevOps 安全地存储和管理代码，例如自定义 Azure Policy 定义、Azure 资源管理器模板和 Desired State Configuration 脚本。 

向特定用户、内置安全组或者在 Azure Active Directory (Azure AD)（如果与 Azure DevOps 集成）或 Active Directory（如果与 TFS 集成）中定义的组授予权限，或拒绝向其授予权限。

- [如何在 Azure DevOps 中存储代码](/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [关于 Azure DevOps 中的权限和组](/azure/devops/organizations/security/about-permissions)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7：部署 Azure 资源的配置管理工具

**指导**：使用 Azure Policy 为 Azure 资源定义和实施标准安全配置。  使用 Azure Policy 别名创建自定义策略，审核或强制实施 Azure 资源的网络配置。 实现与特定的 Azure 负载均衡器资源相关的内置策略定义。  此外，请使用 Azure 自动化将配置更改 部署到 Azure 负载均衡器资源。

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [如何使用别名](../governance/policy/concepts/definition-structure.md#aliases)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9：为 Azure 资源实施自动配置监视

**指导**：使用安全中心对 Azure 资源和 Azure Policy 执行基线扫描，以发出警报并审核资源配置。

- [如何在 Azure 安全中心修正建议](../security-center/security-center-remediate-recommendations.md)

**Azure 安全中心监视**：是

**责任**：客户

## <a name="incident-response"></a>事件响应

[有关详细信息，请参阅 *Azure 安全基线：* 事件响应](../security/benchmarks/security-control-incident-response.md)。

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2：创建事件评分和优先级设定过程

**指南**：安全中心向每个警报分配一个严重性，帮助你优先处理应首先调查的警报。 

严重性取决于安全中心对调查结果或用于发出警报的分析的确信程度，以及对导致警报的活动背后存在恶意意图的确信程度。

使用标记来标记订阅，并创建命名系统来对 Azure 资源进行标识和分类，特别是处理敏感数据的资源。  

你的责任是根据发生事件的 Azure 资源和环境的关键性确定修正警报的优先级。

- [Azure 安全中心中的安全警报](../security-center/security-center-alerts-overview.md)

- [使用标记整理 Azure 资源](../azure-resource-manager/management/tag-resources.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5：将安全警报整合到事件响应系统中

**指导**：使用连续导出功能导出安全中心警报和建议，以便确定给 Azure 资源带来的风险。 

使用安全中心的连续导出功能手动导出或者持续导出警报和建议。 

利用安全中心数据连接器将警报流式传输到 Azure Sentinel。

- [如何配置连续导出](../security-center/continuous-export.md)

- [如何将警报流式传输到 Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="106-automate-the-response-to-security-alerts"></a>10.6：自动响应安全警报

**指导**：使用安全中心的工作流自动化功能，针对安全警报和建议自动触发响应，以保护 Azure 资源。

- [如何在安全中心配置工作流自动化](../security-center/workflow-automation.md)

**Azure 安全中心监视**：是

**责任**：客户

## <a name="penetration-tests-and-red-team-exercises"></a>渗透测试和红队练习

[有关详细信息，请参阅 *Azure 安全基线：* 渗透测试和红队演练](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)。

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1：定期对 Azure 资源执行渗透测试，确保修正所有发现的关键安全问题

**指导**：请遵循 Microsoft 云渗透测试互动规则，确保你的渗透测试不违反 Microsoft 政策。 使用 Microsoft 红队演练策略和执行，以及针对 Microsoft 托管云基础结构、服务和应用程序执行现场渗透测试。 

- [参与的渗透测试规则](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft 云红色组合](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure 安全中心监视**：不适用

**责任**：共享

## <a name="next-steps"></a>后续步骤

- 请参阅 [Azure 安全基准](../security/benchmarks/overview.md)
- 详细了解 [Azure 安全基线](../security/benchmarks/security-baselines-overview.md)