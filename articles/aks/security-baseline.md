---
title: 适用于 Azure Kubernetes 服务的 Azure 安全基线
description: Azure Kubernetes 服务安全基线为实现 Azure 安全基准中指定的安全建议提供过程指南和资源。
author: msmbaldwin
ms.service: container-service
ms.topic: conceptual
ms.date: 10/01/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 6612e6982ae4826869134dfa46b35a3f202d4bbb
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/26/2020
ms.locfileid: "96183237"
---
# <a name="azure-security-baseline-for-azure-kubernetes-service"></a>适用于 Azure Kubernetes 服务的 Azure 安全基线

Azure Kubernetes 服务的 Azure 安全基线包含有助于你改进部署安全状况的建议。

此服务的基线摘自 [Azure 安全基准版本 1.0](../security/benchmarks/overview.md)，其中提供了有关如何根据我们的最佳做法指导保护 Azure 上的云解决方案的建议。

有关详细信息，请参阅 [Azure 安全基线概述](../security/benchmarks/security-baselines-overview.md)。

## <a name="network-security"></a>网络安全性

[有关详细信息，请参阅 *Azure 安全基线：* 网络安全](../security/benchmarks/security-control-network-security.md)。

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1：保护虚拟网络中的 Azure 资源

**指导**：默认情况下，随着 Microsoft Azure Kubernetes 服务 (AKS) 群集的创建，会自动创建一个网络安全组和路由表。 使用负载均衡器、端口映射或入口路由创建服务时，AKS 会自动修改网络安全组以便流量流向正确的方向。 网络安全组自动与客户节点上的虚拟 NIC 关联，并将路由表与虚拟网络上的子网关联。 

使用 AKS 网络策略来限制网络流量，方法是根据所选的命名空间和标签选择器为群集中 Linux pod 间的入口和出口流量定义规则。 使用网络策略时要求 Azure CNI 插件具有已定义的虚拟网络和子网，并且只能在群集创建时启用。 不能将它们部署到现有的 AKS 群集。

可以实现专用 AKS 群集，以确保 AKS-API 服务器和节点池之间的网络通信仅保留在专用网络上。 控制平面或 API 服务器驻留在 AKS 管理的 Azure 订阅中，并使用内部 (RFC1918) IP 地址，而客户的群集或节点池则位于其自己的订阅中。 服务器与群集或节点池可以使用 API 服务器虚拟网络中的 Azure 专用链接服务以及在客户 AKS 群集的子网中公开的专用终结点相互通信。  或者，使用 AKS-API 服务器的公共终结点，但使用 AKS-API 服务器的授权 IP 范围功能限制访问。 

- [Azure Kubernetes Service (AKS) 中的应用程序和群集的安全性概念](concepts-security.md)

- [在 Azure Kubernetes 服务 (AKS) 中使用网络策略保护 Pod 之间的流量](use-network-policies.md)

- [创建专用 Azure Kubernetes 服务群集](private-clusters.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2：监视和记录虚拟网络、子网和 NIC 的配置与流量

**指导**：使用安全中心并遵循其网络保护建议来保护 Azure Kubernetes 服务 (AKS) 群集正在使用的网络资源。 

启用网络安全组流日志，并将日志发送到 Azure 存储帐户进行审核。 还可以将流日志发送到 Log Analytics 工作区，然后使用流量分析来深入了解 Azure 云中的流量模式，以可视化网络活动、识别热点和安全威胁、了解流量流模式并查明网络错误配置。

- [了解 Azure 安全中心提供的网络安全](../security-center/security-center-network-recommendations.md)

- [如何启用网络安全流日志](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [如何启用和使用流量分析](../network-watcher/traffic-analytics.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="13-protect-critical-web-applications"></a>1.3：保护关键 Web 应用程序

**指导**：在 AKS 群集前面使用启用了 Web 应用程序防火墙 (WAF) 的 Azure 应用程序网关，通过筛选到 Web 应用的传入流量，来提供额外的安全层。 Azure WAF 使用一组由开放式 Web 应用程序安全项目 (OWASP) 提供的规则来监视攻击，例如针对该流量的跨网站脚本攻击或 cookie 篡改。 

使用 API 网关对 AKS 环境中使用的 API 进行身份验证、授权、限制、缓存、转换和监视。 API 网关充当微服务的前门、将客户端与微服务分离，并通过消除处理横切关注点的负担来降低微服务的复杂性。

- [了解 AKS 中的网络连接和安全的最佳做法](operator-best-practices-network.md)

- [应用程序网关入口控制器](../application-gateway/ingress-controller-overview.md)

- [将 Azure API 管理与 Azure Kubernetes 服务中部署的微服务结合使用](../api-management/api-management-kubernetes.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4：拒绝与已知恶意的 IP 地址进行通信

**指南**：在虚拟网络上启用 Microsoft 分布式拒绝服务 (DDoS) 标准保护，其中部署了 Azure Kubernetes SERVICE (AKS) 组件来防范 DDoS 攻击。
安装网络策略引擎，并创建 Kubernetes 网络策略来控制 AKS 中 pod 之间的流量，默认情况下，在这些 pod 之间允许所有流量。 应该只对 AKS 中基于 Linux 的节点和 Pod 使用网络策略。 定义对 Pod 通信进行限制的规则以提高安全性。 

选择基于分配的标签、命名空间或流量端口等设置来允许或拒绝流量。 在 AKS 群集中动态创建 Pod 时，可以自动应用所需的网络策略。 

- [在 Azure Kubernetes 服务 (AKS) 中使用网络策略保护 Pod 之间的流量](use-network-policies.md)

- [如何配置 DDoS 防护](../ddos-protection/manage-ddos-protection.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="15-record-network-packets"></a>1.5：记录网络数据包

**指导**：根据需要使用网络观察程序数据包捕获来调查异常活动。 

在订阅中创建或更新虚拟网络时，网络观察程序将在虚拟网络的区域中自动启用。 还可以使用 PowerShell、Azure CLI、REST API 或 ARMClient 方法创建新的网络观察程序实例

- [如何启用网络观察程序](../network-watcher/network-watcher-create.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6：部署基于网络的入侵检测/入侵防护系统 (IDS/IPS)

**指导**：使用启用了 Web 应用程序防火墙 (WAF) 的 Azure 应用程序网关来保护 Azure Kubernetes 服务 (AKS) 群集。 

如果不需要基于有效负载检查或行为分析的入侵检测和/或预防，则可以在“检测模式”下使用并配置具有 WAF 的 Azure 应用程序网关，以记录警报和威胁，或使用“预防模式”主动阻止检测到的入侵和攻击。

- [了解使用 WAF 保护 AKS 群集的最佳做法](operator-best-practices-network.md#secure-traffic-with-a-web-application-firewall-waf)

- [如何部署 Azure 应用程序网关 (Azure WAF)](../web-application-firewall/ag/application-gateway-web-application-firewall-portal.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="17-manage-traffic-to-web-applications"></a>1.7：管理发往 Web 应用程序的流量

**指导**：在 AKS 群集前面使用启用了 Web 应用程序防火墙 (WAF) 的 Azure 应用程序网关来筛选传入的流量。 开放式 Web 应用程序安全项目 (OWASP) 提供了一套规则，这些规则在 Azure WAF 中用于监视跨网站脚本或 cookie 篡改之类的攻击。

将完全限定的域名 (FQDN) 标记应用于应用程序，以便在网络安全组内设置应用程序规则。 设置网络规则后。 使用 FQDN 标记添加应用程序规则，例如 AzureKubernetesService，它包括通过 TCP 端口 443 和端口 80 访问的所有必需 FQDN。 

- [了解 AKS 中的网络连接和安全的最佳做法](operator-best-practices-network.md)

- [在 Azure Kubernetes 服务 (AKS) 中使用网络策略保护 Pod 之间的流量](use-network-policies.md)

- [如何部署 Azure 应用程序网关 (Azure WAF)](../web-application-firewall/ag/application-gateway-web-application-firewall-portal.md)

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8：最大程度地降低网络安全规则的复杂性和管理开销

**指导**：使用虚拟网络服务标记定义与 Azure Kubernetes 服务 (AKS) 实例关联的网络安全组上的网络访问控制。 当创建通过指定服务标记名称来允许或拒绝相应服务的流量的安全规则时，可以使用服务标签代替特定的 IP 地址。 

Microsoft 会管理服务标记包含的地址前缀，并会在地址发生更改时自动更新服务标记。

将 Azure 标记应用到 AKS 群集中的节点池。 它们不同于虚拟网络服务标记，并应用于节点池中的每个节点，在升级过程中持久保留。 

- [了解并使用服务标记](../virtual-network/service-tags-overview.md)

- [了解适用于 AKS 的 NSG](support-policies.md)

- [控制 Azure Kubernetes 服务 (AKS) 中群集节点的出口流量](limit-egress-traffic.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9：维护网络设备的标准安全配置

**指导**：使用 Azure Policy 为与 Azure Kubernetes 服务 (AKS) 群集关联的网络资源定义并实现标准安全配置。 在“Microsoft.ContainerService”和“Microsoft.Network”命名空间中使用 Azure Policy 别名创建自定义策略，以审核或强制实施 AKS 群集的网络配置。 

另外，请使用与 AKS 相关的内置策略定义，例如：

• 应在 Kubernetes 服务上定义经授权的 IP 范围

• 在 Kubernetes 群集中强制实施 HTTPS 入口

• 确保服务只在 Kubernetes 群集中侦听允许使用的端口

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [用于网络的 Azure Policy 示例](../governance/policy/samples/built-in-policies.md#network)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="110-document-traffic-configuration-rules"></a>1.10：阐述流量配置规则

**指导**：为网络安全组和其他资源使用标记，以用于流入和流出 Azure Kubernetes 服务 (AKS) 群集的流量。 使用各个网络安全组规则的“说明”字段为允许流量传入/传出网络的任何规则指定业务需要和/或持续时间等。
使用标记相关的任何内置 Azure Policy 定义（例如“需要标记及其值”），以确保使用标记创建所有资源，并接收现有未标记资源的通知。

使用网络策略，可以选择基于命名空间和标签选择器来允许或拒绝群集中的特定网络路径。 使用这些命名空间和标记作为流量配置规则的描述符。 使用 Azure PowerShell 或 Azure 命令行接口 (CLI) 根据资源的标记查找资源或对其执行操作。

- [具有 CLI 的 Azure Policy](/cli/azure/policy?view=azure-cli-latest)

- [如何创建和使用标记](../azure-resource-manager/management/tag-resources.md)

- [如何创建采用安全配置的 NSG](../virtual-network/tutorial-filter-network-traffic.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11：使用自动化工具来监视网络资源配置和检测更改

**指导**：使用 Azure 活动日志监视网络资源配置，并检测与 Azure Kubernetes 服务 (AKS) 群集相关的网络资源的更改。 

在 Azure Monitor 中创建当关键网络资源发生更改时触发的警报。 活动日志中来自 AzureContainerService 用户的任何条目都将作为平台操作记录。 

使用 Azure Monitor 日志从 AKS 主组件、kube-apiserver 和 kube-controller-manager 启用和查询日志。 使用容器运行时创建和管理运行 kubelet 的节点，并通过托管 Kubernetes API 服务器部署其应用程序。 

- [如何查看和检索 Azure 活动日志事件](../azure-monitor/platform/activity-log.md#view-the-activity-log)

- [如何在 Azure Monitor 中创建警报](../azure-monitor/platform/alerts-activity-log.md)

- [启用和查看 Azure Kubernetes 服务 (AKS) 中 Kubernetes 主节点的日志](view-master-logs.md)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="logging-and-monitoring"></a>日志记录和监视

[有关详细信息，请参阅 *Azure 安全基线：* 日志记录和监视](../security/benchmarks/security-control-logging-monitoring.md)。

### <a name="21-use-approved-time-synchronization-sources"></a>2.1：使用批准的时间同步源

**指导**：除了 UDP 端口 123 和网络时间协议 (NTP)，Azure Kubernetes 服务 (AKS) 节点还使用 ntp.ubuntu.com 进行时间同步。 

如果使用自定义 DNS 服务器，请确保群集节点可以访问 NTP 服务器。 

- [了解 AKS 群集节点的 NTP 域和端口要求](limit-egress-traffic.md)

**Azure 安全中心监视**：不适用

**责任**：共享

### <a name="22-configure-central-security-log-management"></a>2.2：配置中心安全日志管理

**指导**：从 Azure Kubernetes 服务 (AKS) 主组件、kube-apiserver 和 kube-controller-manager 启用审核日志，这些组件作为托管服务提供。 

• kube-auditaksService：审核日志中控制平面操作的显示名称（来自 hcpService） 

• masterclient：审核日志中 MasterClientCertificate（通过 az aks get-credentials 获得的证书）的显示名称 

• nodeclient：代理节点使用的 ClientCertificate 的显示名称

还可以启用其他审核日志（例如 kube-audit）。 

将这些日志导出到 Log Analytics 或其他存储平台。 在 Azure Monitor 中，使用 Log Analytics 工作区来查询和执行分析，并使用 Azure 存储帐户进行长期存档存储。

根据组织业务需求，启用此数据并将其上传到 Azure Sentinel 或第三方 SIEM。

- [在此处查看包括日志角色的日志架构](view-master-logs.md)

- [了解用于容器的 Azure Monitor](../azure-monitor/insights/container-insights-overview.md)

- [如何为容器启用 Azure Monitor](../azure-monitor/insights/container-insights-onboard.md)

- [启用和查看 Azure Kubernetes 服务 (AKS) 中 Kubernetes 主节点的日志](view-master-logs.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：为 Azure 资源启用审核日志记录

**指导**：使用活动日志监视 Azure Kubernetes 服务 (AKS) 资源上的操作，查看所有活动及其状态。 使用活动日志确定对订阅中的资源执行的操作：谁启动的操作

操作何时发生

操作的状态

其他可能有助于研究操作的属性的值

通过 Azure PowerShell、Azure 命令行接口 (CLI)、Azure REST API 或 Azure 门户检索活动日志中的信息。 

在 AKS 主组件上启用审核日志，例如： 

• kube-auditaksService：审核日志中控制平面操作的显示名称（来自 hcpService） 

• masterclient：审核日志中 MasterClientCertificate（通过 az aks get-credentials 获得的证书）的显示名称 

• nodeclient：代理节点使用的 ClientCertificate 的显示名称

还可以启用其他审核日志（例如 kube-audit）。 

- [如何启用和查看 AKS 中 Kubernetes 主节点的日志](view-master-logs.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4：从操作系统收集安全日志

**指导**：启用 Log Analytics 代理的自动安装，以从 AKS 群集节点收集数据。 此外，从 Azure 安全中心启用 Azure Log Analytics 监视代理的自动预配，自动预配默认处于关闭状态。 也可以手动安装该代理。 启用自动预配后，安全中心可在所有受支持的 Azure VM 以及任何新建的 Azure VM 中部署 Log Analytics 代理。 安全中心从 Azure 虚拟机 (VM)、虚拟机规模集和 IaaS 容器（如 Kubernetes 群集节点）收集数据，以监视安全漏洞和威胁。 数据是使用 Azure Log Analytics 代理收集的，该代理从计算机中读取各种与安全相关的配置和事件日志，然后将数据复制到工作区进行分析。 

必须收集数据才能深入了解缺少的更新、配置不当的 OS 安全设置、Endpoint Protection 状态情况，以及运行状况和威胁检测。

- [如何启用对 Log Analytics 代理的自动预配](../security-center/security-center-enable-data-collection.md)

**Azure 安全中心监视**：是

**责任**：共享

### <a name="25-configure-security-log-storage-retention"></a>2.5：配置安全日志存储保留期

**指导**：将 Azure Kubernetes 服务 (AKS) 实例载入 Azure Monitor，并根据组织的合规性要求设置相应的 Azure Log Analytics 工作区保持期。 

- [如何为 Log Analytics 工作区设置日志保留参数](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="26-monitor-and-review-logs"></a>2.6：监视和审查日志

**指导**：将 Azure Kubernetes 服务 (AKS) 实例载入 Azure Monitor 并为群集配置诊断设置。 

使用 Azure Monitor 的 Log Analytics 工作区查看日志并对日志数据执行查询。 Azure Monitor 日志在 Azure 门户中启用和管理，或通过 CLI 进行管理，并使用基于角色的访问控制 (Kubernetes RBAC) 、Azure RBAC 和非 RBAC 启用 AKS 群集。

查看由 AKS 主组件（kube-apiserver 和 kube-controllermanager）生成的日志，以便对应用程序和服务进行故障排除。 启用数据并将其载入 Azure Sentinel 或第三方 SIEM，以进行集中的日志管理和监视。

- [如何启用和查看 AKS 中 Kubernetes 主节点的日志](view-master-logs.md)

- [如何加入 Azure Sentinel](../sentinel/quickstart-onboard.md)

- [如何在 Azure Monitor 中执行自定义查询](../azure-monitor/log-query/get-started-queries.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7：针对异常活动启用警报

**指导**：将 Azure Kubernetes 服务 (AKS) 与安全中心一起使用，可以更深入地了解 AKS 节点。 查看安全中心在主机和群集级别检测到的威胁和恶意活动警报。 安全中心实现对 AKS 群集中发生的原始安全事件的持续分析，例如网络数据、进程创建和 Kubernetes 审核日志。 确定此活动是否为预期行为，或者应用程序是否行为异常。 使用 Azure Monitor 中的指标和日志来证实发现结果。 

- [了解 Azure Kubernetes 服务与安全中心的集成](../security-center/defender-for-kubernetes-introduction.md)

- [如何启用 Azure 安全中心标准层](../security-center/security-center-get-started.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="28-centralize-anti-malware-logging"></a>2.8：集中管理反恶意软件日志记录

**指导**：安装并启用针对 Azure 到 Azure Kubernetes 服务 (AKS) 虚拟机和虚拟机规模集节点的 Microsoft 反恶意软件。 查看安全中心中的警报以进行修正。

- [适用于 Azure 云服务和虚拟机的 Microsoft 反恶意软件](../security/fundamentals/antimalware.md)

- [安全警报参考指南](../security-center/alerts-reference.md)

- [容器的警报 - Azure Kubernetes 服务群集](../security-center/alerts-reference.md#alerts-akscluster)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="29-enable-dns-query-logging"></a>2.9：启用 DNS 查询日志记录

**指导**：Azure Kubernetes 服务 (AKS) 使用 CoreDNS 项目来管理和解析群集 DNS。

通过在 coredns-custom ConfigMap 中应用文档化配置来启用 DNS 查询日志记录。 

- [使用 Azure Kubernetes 服务自定义 CoreDNS](coredns-custom.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="210-enable-command-line-audit-logging"></a>2.10：启用命令行审核日志记录

**指导**：在 Azure Kubernetes 服务 (AKS) 中使用 kubectl 命令行客户端管理 Kubernetes 群集，并从 AKS 节点获取其日志以进行故障排除。 如果使用 Azure Cloud Shell，则已安装 Kubectl。 若要在本地安装 kubectl，请使用“Install-AzAksKubectl”cmdlet。

- [快速入门 - 使用 PowerShell 部署 Azure Kubernetes 服务群集](kubernetes-walkthrough-powershell.md)

- [从 Azure Kubernetes 服务 (AKS) 群集节点获取 kubelet 日志](kubelet-logs.md)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="identity-and-access-control"></a>标识和访问控制

[有关详细信息，请参阅 *Azure 安全基线：* 标识和访问控制](../security/benchmarks/security-control-identity-access-control.md)。

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1：维护管理帐户的清单

**指导**：Azure Kubernetes 服务 (AKS) 本身不提供存储常规用户帐户和密码的标识管理解决方案。 借助 Azure Active Directory (Azure AD) 集成，可授权用户或组访问一个命名空间或多个群集内的 Kubernetes 资源。 

使用 Azure AD PowerShell 模块执行即席查询，以发现属于 AKS 管理组的成员的帐户

将 Azure CLI 用于 "获取托管 Kubernetes 群集的访问凭据" 等操作，有助于定期协调访问。 实现此过程以保持服务帐户的更新清单，这是 AKS 中的另一个主要用户类型。 强制执行安全中心的标识和访问管理建议。

- [如何将 AKS 与 Azure AD 集成](./azure-ad-integration-cli.md)

- [如何使用 PowerShell 获取 Azure AD 中目录角色的成员](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

- [如何使用 Azure 安全中心监视标识和访问](../security-center/security-center-identity-access.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="32-change-default-passwords-where-applicable"></a>3.2：在适用的情况下更改默认密码

**指导**：Azure Kubernetes 服务 (AKS) 没有通用默认密码的概念，也没有提供可以存储常规用户帐户和密码的标识管理解决方案。 借助 Azure Active Directory (Azure AD) 集成，可以在命名空间内或跨群集授予对 AKS 资源的基于角色的访问权限。 

使用 Azure AD PowerShell 模块执行即席查询，以发现属于 AKS 管理组的成员的帐户

- [了解 AKS 的访问和标识选项](concepts-identity.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="33-use-dedicated-administrative-accounts"></a>3.3：使用专用管理帐户

**指导**：将 Azure Kubernetes 服务 (AKS) 群集的用户身份验证与 Azure Active Directory (Azure AD) 集成。 可以使用 Azure AD 身份验证令牌登录到 AKS 群集。 配置 Kubernetes 基于角色的访问控制 (Kubernetes RBAC) ，用于对 Kubernetes 配置 (kubeconfig) 信息和权限、命名空间和群集资源的管理访问权限。 

围绕专用管理帐户的使用创建策略和过程。 实现安全中心标识和访问管理建议。

- [如何使用 Azure 安全中心监视标识和访问](../security-center/security-center-identity-access.md)

- [控制对群集资源的访问](azure-ad-rbac.md)

- [使用 Azure 基于角色的访问控制](control-kubeconfig-access.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4：将单一登录 (SSO) 与 Azure Active Directory 配合使用

**指导**：对 AKS 群集使用带有 Azure Active Directory (Azure AD) 集成身份验证的 Azure Kubernetes 服务 (AKS) 的单一登录。

- [如何实时查看 Kubernetes 日志、事件和 Pod 指标](../azure-monitor/insights/container-insights-livedata-overview.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5：对所有基于 Azure Active Directory 的访问使用多重身份验证

**指导**：将 Azure Kubernetes 服务 (AKS) 的身份验证与 Azure Active Directory (Azure AD) 集成。 

启用 Azure AD 多重身份验证 (MFA)，并遵循安全中心标识和访问管理建议。

- [如何在 Azure 中启用 MFA](../active-directory/authentication/howto-mfa-getstarted.md)

- [如何在 Azure 安全中心监视标识和访问](../security-center/security-center-identity-access.md) 

**Azure 安全中心监视**：是

**责任**：客户

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6：对所有管理任务使用专用计算机（特权访问工作站）

**指导**：使用配置了多重身份验证 (MFA) 的特权访问工作站 (PAW) 登录到指定的 Azure Kubernetes 服务 (AKS) 群集和相关资源。
- [了解特权访问工作站](/windows-server/identity/securing-privileged-access/privileged-access-workstations)

- [如何在 Azure 中启用 MFA](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7：记录来自管理帐户的可疑活动并对其发出警报

**指导**：对 Azure Kubernetes 服务 (AKS) 使用带有 Azure AD 集成身份验证的 Azure Active Directory (Azure AD) 安全报告。 当环境中发生可疑或不安全活动时，可以生成警报。 使用安全中心监视标识和访问活动。

- [如何确定标记为存在风险活动的 Azure AD 用户](../active-directory/identity-protection/overview-identity-protection.md)

- [如何在 Azure 安全中心监视用户的标识和访问活动](../security-center/security-center-identity-access.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="38-manage-azure-resources-only-from-approved-locations"></a>3.8：仅从批准的位置管理 Azure 资源

**指导**：使用条件访问命名位置，仅允许从 IP 地址范围或国家/地区的特定逻辑分组访问 Azure Kubernetes 服务 (AKS) 群集。 这要求将 AKS 的身份验证与 Azure Active Directory (Azure AD) 集成。

限制从一组有限的 IP 地址范围访问 AKS API 服务器，因为它接收在群集中执行操作以创建资源或缩放节点数量的请求。 

- [使用 Azure Kubernetes 服务 (AKS) 中的已授权 IP 地址范围保护对 API 服务器的访问](api-server-authorized-ip-ranges.md)

- [如何在 Azure 中配置命名位置](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="39-use-azure-active-directory"></a>3.9：使用 Azure Active Directory

**指导**：使用 Azure Active Directory (Azure AD) 作为 Azure Kubernetes 服务 (AKS) 的中心身份验证和授权系统。 Azure AD 通过对静态数据和传输中数据使用强加密以及加盐、哈希处理和安全存储用户凭据来保护数据。

将 AKS 内置角色与 Azure 基于角色的访问控制 (Azure RBAC) - 资源策略参与者和所有者一起使用，以便对 Kubernetes 群集执行策略分配操作

- [Azure Policy 概述](../governance/policy/overview.md)

- [如何将 Azure AD 与 AKS 集成](./azure-ad-integration-cli.md) 

- [集成 AKS 托管 Azure AD](managed-aad.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10：定期审查和协调用户访问

**指导**：对 Azure Kubernetes 服务 (AKS) 使用带有 Azure AD 集成身份验证的 Azure Active Directory (Azure AD) 安全报告。 搜索 Azure AD 日志，以帮助发现过时的帐户。 

执行 Azure 标识访问评审来高效地管理组成员身份、对企业应用程序的访问和角色分配。 修正 Azure 安全中心提供的标识和访问管理建议。

了解用于支持或故障排除目的的角色。 例如，Microsoft 支持)  (执行的任何群集操作都是在名称 aks-rolebinding 的内置 Kubernetes "编辑" 角色下进行的。 AKS 支持使用此角色来编辑群集配置和资源，以便对群集问题进行故障排除和诊断。 但是，此角色无法修改权限，也无法创建角色或角色绑定。 仅在具有实时 (JIT) 访问权限的活动支持工单下启用角色访问。
 
- [Azure Kubernetes 服务 (AKS) 的访问和标识选项](concepts-identity.md)

- [如何使用 Azure 标识访问评审](../active-directory/governance/access-reviews-overview.md)

- [如何在 Azure 安全中心监视用户的标识和访问活动](../security-center/security-center-identity-access.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11：监视尝试访问已停用凭据的行为

**指导**：将 Azure Kubernetes 服务 (AKS) 的用户身份验证与 Azure Active Directory (Azure AD) 集成。 为 Azure AD 创建诊断设置，将审核和登录日志发送到 Azure Log Analytics 工作区。 在 Azure log Analytics 工作区中配置所需的警报（例如，当停用的帐户尝试登录时）。
- [如何将 Azure 活动日志集成到 Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [如何使用 Azure Monitor 创建、查看和管理日志警报](../azure-monitor/platform/alerts-log.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12：针对帐户登录行为偏差发出警报

**指导**：将 Azure Kubernetes 服务 (AKS) 的用户身份验证与 Azure Active Directory (Azure AD) 集成。 使用 Azure AD 的风险检测和标识保护功能，配置对检测到的与用户标识相关的可疑操作的自动响应。 根据业务需要，将数据引入 Azure Sentinel 以便进一步调查。

- [如何查看 Azure AD 风险登录](../active-directory/identity-protection/overview-identity-protection.md)

- [如何配置和启用标识保护风险策略](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [如何加入 Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13：在支持场合下为 Microsoft 提供对相关客户数据的访问权限

**指导**：不适用于 Azure Kubernetes 服务 (AKS)，因为它不受客户密码箱支持。
- [支持客户密码箱的服务列表](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Azure 安全中心监视**：不适用

**责任**：不适用

## <a name="data-protection"></a>数据保护

[有关详细信息，请参阅 *Azure 安全基线：* 数据保护](../security/benchmarks/security-control-data-protection.md)。

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1：维护敏感信息的清单

**指导**：对与 Azure Kubernetes 服务 (AKS) 部署相关的资源使用标记，以帮助跟踪存储或处理敏感信息的 Azure 资源。

- [如何创建和使用标记](../azure-resource-manager/management/tag-resources.md)

- [更新托管群集的标记](/rest/api/aks/managedclusters/updatetags)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2：隔离存储或处理敏感信息的系统

**指导**：使用 Azure Kubernetes 服务 (AKS) 以逻辑方式隔离同一群集中的团队和工作负载，以提供最少数量的特权，将范围限定为每个团队所需的资源。 

使用 Kubernetes 中的命名空间创建逻辑隔离边界。 考虑为隔离和多租户实现附加的 Kubernetes 功能，例如计划、网络、身份验证/授权和容器。

为开发、测试和生产环境采用单独的订阅和/或管理组。 通过将 AKS 群集部署到不同的虚拟网络中，并对其进行适当标记，从而将 AKS 群集与网络隔离。

- [了解 AKS 中群集隔离的最佳做法](operator-best-practices-cluster-isolation.md)

- [如何创建其他 Azure 订阅](../cost-management-billing/manage/create-subscription.md)

- [了解 AKS 中的网络连接和安全的最佳做法](operator-best-practices-network.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3：监视和阻止未经授权的敏感信息传输

**指导**：使用 Azure 市场中有关网络外围的第三方解决方案，监视并阻止敏感信息的未授权传输，同时提醒信息安全专业人员。

Microsoft 管理底层平台，并将所有客户内容视为敏感内容，并在很大程度上防范客户数据丢失和公开。 为了确保 Azure 中的客户数据保持安全，Microsoft 已实施并维护一套可靠的数据保护控制机制和功能。

- [AKS 功能所需端口、地址和域名的列表](limit-egress-traffic.md)

- [如何配置 Azure 防火墙的诊断设置](../firewall/firewall-diagnostics.md)

- [了解 Azure 中的客户数据保护](../security/fundamentals/protection-customer-data.md)

**Azure 安全中心监视**：不适用

**责任**：共享

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4：加密传输中的所有敏感信息

**指导**：创建 HTTPS 入口控制器并为 Azure Kubernetes 服务 (AKS) 部署使用自己的 TLS 证书（或者选择使用 Let's Encrypt）。 

默认情况下，Kubernetes 出口流量通过 HTTPS/TLS 加密。 查看来自 AKS 实例的任何可能未加密的出口流量，以进行额外的监视。 这可能包括 NTP 流量、DNS 流量、某些情况下用于检索更新的 HTTP 流量。 

- [如何在 AKS 上创建 HTTPS 入口控制器并使用自己的 TLS 证书](ingress-own-tls.md)

- [如何使用 Let's Encrypt 在 AKS 上创建 HTTPS 入口控制器](ingress-tls.md)

- [AKS 使用的潜在输出端口和协议列表](limit-egress-traffic.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5：使用有效的发现工具识别敏感数据

**指导**：数据标识、分类和丢失防护功能尚不适用于 Azure 存储或计算资源。 如果需要出于合规性目的使用这些功能，请实施第三方解决方案。
Microsoft 管理底层平台，并将所有客户内容视为敏感内容，并在很大程度上防范客户数据丢失和公开。 

为了确保 Azure 中的客户数据保持安全，Microsoft 已实施并维护一套可靠的数据保护控制机制和功能。

- [了解 Azure 中的客户数据保护](../security/fundamentals/protection-customer-data.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4.6：使用 Azure RBAC 管理对资源的访问

**指导**：使用构建在 Azure 资源管理器上的 Azure 基于角色的访问控制 (Azure RBAC) 授权系统来提供对 Azure 资源的细粒度访问权限管理。

配置 Azure Kubernetes 服务 (AKS) 以使用 Azure Active Directory (Azure AD) 进行用户身份验证。 使用此配置使用 Azure AD 身份验证令牌登录到 AKS 群集。 

将 AKS 内置角色与 Azure RBAC - 资源策略参与者和所有者一起使用，以便对 AKS 群集执行策略分配操作

- [如何在 AKS 中使用 Azure RBAC 和 Azure AD 标识控制对群集资源的访问](azure-ad-rbac.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7：使用基于主机的数据丢失防护来强制实施访问控制

**指导**：数据标识、分类和丢失防护功能尚不适用于 Azure 存储或计算资源。 如果需要出于合规性目的使用这些功能，请实施第三方解决方案。
Microsoft 管理底层平台，并将所有客户内容视为敏感内容，并在很大程度上防范客户数据丢失和公开。 为了确保 Azure 中的客户数据保持安全，Microsoft 已实施并维护一套可靠的数据保护控制机制和功能。

- [了解 Azure 中的客户数据保护](../security/fundamentals/protection-customer-data.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8：静态加密敏感信息

**指导**：为 Azure Kubernetes 服务 (AKS) 中的卷提供的两种主要存储类型由 Azure 磁盘或 Azure 文件支持。 这两种类型的存储都使用 Azure 存储服务加密 (SSE)，它对静态数据进行加密以提高安全性。 默认情况下，数据使用 Microsoft 管理的密钥进行加密。

使用客户管理的密钥进行静态加密可同时用于对 AKS 群集上的 OS 和数据磁盘进行加密，以进一步控制加密密钥。 客户负责关键管理活动，如密钥备份和轮换。 目前无法使用 AKS 节点级别的 Azure 磁盘加密对磁盘进行加密。

- [有关 Azure Kubernetes 服务 (AKS) 中的存储和备份的最佳做法](operator-best-practices-storage.md)

- [对 Azure Kubernetes Service (AKS) 中的 Azure 磁盘使用自带密钥 (BYOK)](azure-disk-customer-managed-keys.md)

**Azure 安全中心监视**：不适用

**责任**：共享

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9：记录对关键 Azure 资源的更改并对此类更改发出警报

**指导**：使用用于容器的 Azure Monitor 来监视部署到 Azure Kubernetes 服务 (AKS) 上托管的托管 Kubernetes 群集的容器工作负载的性能。 

配置警报，以便在节点或容器上的 CPU 和内存使用率超过定义的阈值时，或者在基础结构中的群集或节点运行状况汇总中发生运行状况状态更改时，发出主动通知或创建日志。 

使用 Azure 活动日志在较高级别上监视 AKS 群集和相关资源。 与 Prometheus 集成，以使用查询查看从节点和 Kubernetes 收集的应用程序和工作负荷指标，以创建自定义警报、仪表板和详细的执行详细分析。

- [了解用于容器的 Azure Monitor](../azure-monitor/insights/container-insights-overview.md)

- [如何为容器启用 Azure Monitor](../azure-monitor/insights/container-insights-onboard.md)

- [如何查看和检索 Azure 活动日志事件](../azure-monitor/platform/activity-log.md#view-the-activity-log)

**Azure 安全中心监视**：是

**责任**：客户

## <a name="vulnerability-management"></a>漏洞管理

[有关详细信息，请参阅 *Azure 安全基线：* 漏洞管理](../security/benchmarks/security-control-vulnerability-management.md)。

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1：运行自动漏洞扫描工具

**指导**：使用安全中心监视 Azure 容器注册表（包括 Azure Kubernetes 服务 (AKS) 实例）是否存在漏洞。 启用安全中心中的容器注册表捆绑包，以确保安全中心已准备好扫描推送到注册表的映像。

在安全中心使用 Qualys 扫描图像后，如果发现问题，你会在安全中心仪表板中收到通知。 使用容器注册表捆绑功能，可以进一步了解基于 Azure 资源管理器的注册表中使用的映像的漏洞。 

使用安全中心为每个漏洞提供可操作的建议。 这些建议包括严重程度分类和补救指南。 

- [Azure Kubernetes 服务 (AKS) 中容器映像管理和安全性的最佳做法](../security-center/defender-for-container-registries-introduction.md)

- [了解 AKS 中容器映像管理和安全性的最佳做法](operator-best-practices-container-image-management.md)

- [了解容器注册表与 Azure 安全中心的集成](../security-center/defender-for-container-registries-introduction.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2：部署自动操作系统修补管理解决方案

**指导**：安全更新会自动应用到 Linux 节点，以保护客户的 Azure Kubernetes 服务 (AKS) 群集。 这些更新包括 OS 安全修复项或内核更新。 

请注意，使 Windows 服务器节点保持最新状态的过程与运行 Linux 的节点不同，因为 Windows Server 节点不接收每日更新。 相反，客户需要在其 AKS 群集中的 Windows Server 节点池上执行升级，后者使用 Azure 控制面板或 Azure CLI 部署具有最新基本 Windows Server 映像和修补程序的新节点。 这些更新可能包含 AKS 的安全或功能改进。

- [了解如何将更新应用于运行 Linux 的 AKS 群集节点](node-updates-kured.md)

- [如何为使用 Windows Server 节点的 AKS 群集升级 AKS 节点池](use-multiple-node-pools.md#upgrade-a-node-pool)

- [Azure Kubernetes 服务 (AKS) 节点映像升级](node-image-upgrade.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="53-deploy-an-automated-patch-management-solution-for-third-party-software-titles"></a>5.3：为第三方软件部署自动化补丁管理解决方案

**指导**：实现手动过程，确保 Azure Kubernetes 服务 (AKS) 群集节点的第三方应用程序在群集生存期内保持修补状态。 这可能需要启用自动更新、监视节点或定期重新启动。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4：比较连续进行的漏洞扫描

**指导**：以一致的间隔导出安全中心扫描结果，并比较结果以验证漏洞是否已修复。 

使用 PowerShell cmdlet“Get-AzSecurityTask”自动检索安全中心建议执行的安全任务，以加强安全状况并修复漏洞扫描结果。

- [如何使用 PowerShell 查看 Azure 安全中心发现的漏洞](/powershell/module/az.security/get-azsecuritytask?view=azps-3.3.0)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5：使用风险评级过程来确定已发现漏洞的修正措施的优先级

**指导**：使用安全中心提供的严重性等级来确定漏洞修复的优先级。 

如果使用内置漏洞评估工具（如 Azure 提供的 Qualys 或 Rapid7），请使用通用漏洞评分系统 (CVSS) 或扫描工具提供的其他评分系统。

**Azure 安全中心监视**：是

**责任**：客户

## <a name="inventory-and-asset-management"></a>库存和资产管理

[有关详细信息，请参阅 *Azure 安全基线：* 清单和资产管理](../security/benchmarks/security-control-inventory-asset-management.md)。

### <a name="61-use-automated-asset-discovery-solution"></a>6.1：使用自动化资产发现解决方案

**指导**：使用 Azure Resource Graph 查询/发现订阅中的所有资源（例如计算、存储、网络等）。 确保你在租户中拥有适当的（读取）权限，并且可以枚举所有 Azure 订阅以及订阅中的资源。

尽管可以通过 Resource Graph 发现经典 Azure 资源，但我们强烈建议你今后还是创建并使用基于 Azure 资源管理器的资源。

- [如何使用 Azure Graph 创建查询](../governance/resource-graph/first-query-portal.md)

- [如何查看 Azure 订阅](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

- [了解 Azure RBAC](../role-based-access-control/overview.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="62-maintain-asset-metadata"></a>6.2：维护资产元数据

**指导**：将标记应用到 Azure资源，以便有条理地将元数据组织成某种分类。

- [如何创建和使用标记](../azure-resource-manager/management/tag-resources.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="63-delete-unauthorized-azure-resources"></a>6.3：删除未经授权的 Azure 资源

**指导**：在适用的情况下，请使用标记、管理组和单独的订阅来组织和跟踪资产。 

创建 Azure Kubernetes 服务 (AKS) 节点池时应用排斥、标签或标记。 该节点池中的所有节点也将继承该排斥、标签或标记。

排斥、标签或标记可用于定期核对清单，并确保及时从订阅中删除未经授权的资源。

- [如何创建其他 Azure 订阅](../cost-management-billing/manage/create-subscription.md)

- [如何创建管理组](../governance/management-groups/create-management-group-portal.md)

- [如何创建和使用标记](../azure-resource-manager/management/tag-resources.md)

- [托管群集 - 更新标记](/rest/api/aks/managedclusters/updatetags)

- [指定节点池的排斥、标签或标记](use-multiple-node-pools.md#specify-a-taint-label-or-tag-for-a-node-pool)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4：定义并维护已批准 Azure 资源的清单

**指导**：根据组织的业务需求，为计算资源定义已批准的 Azure 资源和软件的列表。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5：监视未批准的 Azure 资源

**指导**：在 Azure Policy 中使用以下内置策略定义，对可以在客户订阅中创建的资源类型施加限制：
-   不允许的资源类型 

-   允许的资源类型

使用 Azure Resource Graph 查询/发现订阅中的资源。 确保环境中存在的所有 Azure 资源都是根据组织业务需求批准的。

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [如何使用 Azure Graph 创建查询](../governance/resource-graph/first-query-portal.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6：监视计算资源中未批准的软件应用程序

**指导**：使用 Azure 自动化更改跟踪和清单功能查找环境中安装的软件。 

收集并查看计算机上的软件、文件、Linux 守护程序、Windows 服务和 Windows 注册表项的清单，并监视未经批准的软件应用程序。 

跟踪计算机的配置，以帮助查明整个环境中的操作问题，并更好地了解计算机的状态。

- [如何启用 Azure 虚拟机清单](../automation/automation-tutorial-installed-software.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7：删除未批准的 Azure 资源和软件应用程序

**指导**：使用 Azure 自动化更改跟踪和清单功能查找环境中安装的软件。 

收集并查看计算机上的软件、文件、Linux 守护程序、Windows 服务和 Windows 注册表项的清单，并监视未经批准的软件应用程序。 

跟踪计算机的配置，以帮助查明整个环境中的操作问题，并更好地了解计算机的状态。

- [如何启用 Azure 虚拟机清单](../automation/automation-tutorial-installed-software.md)

- [如何使用文件完整性监视](../security-center/security-center-file-integrity-monitoring.md)

- [了解 Azure 更改跟踪](../automation/change-tracking/overview.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="68-use-only-approved-applications"></a>6.8：仅使用已批准的应用程序

**指导**：使用 Azure 自动化更改跟踪和清单功能查找环境中安装的软件。 

收集并查看计算机上的软件、文件、Linux 守护程序、Windows 服务和 Windows 注册表项的清单，并监视未经批准的软件应用程序。 

跟踪计算机的配置，以帮助查明整个环境中的操作问题，并更好地了解计算机的状态。

在安全中心中为环境中存在的应用程序启用自适应应用程序分析。

- [如何启用 Azure 虚拟机清单](../automation/automation-tutorial-installed-software.md)

 
如何使用 Azure 安全中心自适应应用程序
- [控件](../security-center/security-center-adaptive-application.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="69-use-only-approved-azure-services"></a>6.9：仅使用已批准的 Azure 服务

**指导**：在 Azure Policy 中使用以下内置策略定义，对可以在客户订阅中创建的资源类型施加限制：

- 不允许的资源类型

- 允许的资源类型

使用 Azure Resource Graph 查询/发现订阅中的资源。 确保环境中存在的所有 Azure 资源已获得批准。

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [如何使用 Azure Policy 拒绝特定的资源类型](../governance/policy/samples/index.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10：维护已获批软件的清单

**指导**：使用 Azure Policy 对资源类型设置限制，可使用内置策略定义在订阅中创建这些资源。

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11：限制用户与 Azure 资源管理器进行交互的能力

**指南**：使用 Azure 条件访问可通过为“Microsoft Azure 管理”应用配置“阻止访问”，限制用户与 Azure 资源管理器进行交互的能力。
- [如何配置条件访问以阻止访问 Azure 资源管理器](../role-based-access-control/conditional-access-azure-management.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="612-limit-users-ability-to-execute-scripts-in-compute-resources"></a>6.12：限制用户在计算资源中执行脚本的能力

**指导**：Azure Kubernetes 服务 (AKS) 本身不提供存储常规用户帐户和密码的标识管理解决方案。 相反，请使用 Azure Active Directory (Azure AD) 作为 AKS 群集的集成标识解决方案。 

使用 Azure AD 集成，可授权用户或组访问一个命名空间或多个群集内的 Kubernetes 资源。 

使用 Azure AD PowerShell 模块执行即席查询，以发现作为 AKS 管理组成员的帐户，并使用它来定期协调访问权限。 使用 Azure CLI 来执行 "获取托管 Kubernetes 群集的访问凭据" 等操作。 实现安全中心标识和访问管理建议。

- [使用 Azure CLI 管理 AKS](/cli/azure/aks?view=azure-cli-latest)

- [了解 AKS 与 Azure AD 的集成](concepts-identity.md)

- [如何将 AKS 与 Azure AD 集成](./azure-ad-integration-cli.md)

- [如何使用 PowerShell 获取 Azure AD 中的目录角色](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [如何使用 PowerShell 获取 Azure AD 中目录角色的成员](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

- [如何使用 Azure 安全中心监视标识和访问](../security-center/security-center-identity-access.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13：以物理或逻辑方式隔离高风险应用程序

**指导**：使用 Azure Kubernetes 服务 (AKS) 以逻辑方式隔离同一群集中的团队和工作负载，以获取最少数量的特权，将范围限定为每个团队所需的资源。 

在 Kubernetes 中实现命名空间以创建逻辑隔离边界。 使用“Microsoft.ContainerService”命名空间中的 Azure Policy 别名创建自定义策略，以审核或强制实施 Azure Kubernetes 服务 (AKS) 实例的配置。 

查看并实现用于隔离和多租户的其他 Kubernetes 功能和注意事项，包括以下内容：计划、网络、身份验证/授权和容器。 还可以使用单独的订阅和/或管理组进行开发、测试和生产。 将 AKS 群集与虚拟网络、适当标记的子网分开，并使用 Web 应用程序防火墙 (WAF) 进行保护。

- [了解 AKS 中群集隔离的最佳做法](operator-best-practices-cluster-isolation.md)

- [如何创建其他 Azure 订阅](../cost-management-billing/manage/create-subscription.md)

- [如何创建管理组](../governance/management-groups/create-management-group-portal.md)

- [了解 AKS 中的网络连接和安全的最佳做法](operator-best-practices-network.md)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="secure-configuration"></a>安全配置

[有关详细信息，请参阅 *Azure 安全基线：* 安全配置](../security/benchmarks/security-control-secure-configuration.md)。

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1：为所有 Azure 资源建立安全配置

**指导**：使用“Microsoft.ContainerService”命名空间中的 Azure Policy 别名创建自定义策略，以审核或强制实施 Azure Kubernetes 服务 (AKS) 实例的配置。 使用内置 Azure Policy 定义。

AKS 的内置策略定义示例包括：

• 在 Kubernetes 群集中强制实施 HTTPS 入口

• 应在 Kubernetes 服务上定义经授权的 IP 范围

•基于角色的访问控制 (RBAC) 应在 Kubernetes Services 上使用

• 确保只有允许使用的容器映像才在 Kubernetes 群集中运行

使用 Azure 资源管理器以 JavaScript 对象表示法 (JSON) 导出 AKS 配置的模板。 定期检查以确保这些配置符合组织的安全要求。 使用来自 Azure 安全中心的建议作为 Azure 资源的安全配置基线。 

- [如何配置和管理 AKS pod 安全策略](use-pod-security-policies.md)

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="72-establish-secure-operating-system-configurations"></a>7.2：建立安全的操作系统配置

**指导**：Azure Kubernetes 服务 (AKS) 群集部署在具有安全优化 OS 的虚拟主机上。 主机 OS 还包含了附加的安全强化步骤，以减少攻击面，并允许以安全的方式部署容器。 

Azure 对 AKS 虚拟机主机应用每日修补程序（包括安全修补程序），其中一些修补程序需要重新启动。 客户负责根据需要安排 AKS 虚拟机主机重新启动。 

- [AKS 代理节点主机 OS 的安全强化](security-hardened-vm-host-image.md)

- [了解 AKS 虚拟主机中的安全强化](security-hardened-vm-host-image.md)

**Azure 安全中心监视**：不适用

**责任**：共享

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3：维护安全的 Azure 资源配置

**指导**：使用 pod 安全策略保护 Azure Kubernetes 服务 (AKS) 群集。  限制可以计划哪些 pod 来提高群集的安全性。 

不允许请求资源的 Pod 不能在 AKS 群集中运行。 

还可以使用 Azure Policy [拒绝] 和 [不存在则部署] 效果对与 AKS 部署相关的 Azure 资源（如虚拟网络、子网、Azure 防火墙、存储帐户等）强制实施安全设置。 

使用以下命名空间中的别名创建自定义 Azure Policy 定义： 

•   Microsoft.ContainerService

•   Microsoft.Network

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [了解 Azure Policy 效果](../governance/policy/concepts/effects.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4：维护安全的操作系统配置

**指导**：Azure Kubernetes 服务 (AKS) 群集部署在具有安全优化 OS 的虚拟主机上。 主机 OS 还包含了附加的安全强化步骤，以减少攻击面，并允许以安全的方式部署容器。 

请参阅主机操作系统中内置的 Internet 安全中心 (CIS) 控制列表。  

- [AKS 代理节点主机 OS 的安全强化](security-hardened-vm-host-image.md)

- [了解 AKS 群集的状态配置](concepts-clusters-workloads.md#control-plane)

- [了解 AKS 虚拟主机中的安全强化](security-hardened-vm-host-image.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5：安全存储 Azure 资源的配置

**指导**：如果使用自定义 Azure Policy 定义，请使用 Azure Repos 安全地存储和管理配置。 使用 Azure 资源管理器以 JavaScript 对象表示法 (JSON) 导出 Azure Kubernetes 服务 (AKS) 配置的模板。 定期检查以确保这些配置符合组织的安全要求。 

实现第三方解决方案（如 Terraform）创建一个配置文件，该文件用于声明 Kubernetes 群集的资源。 可以通过实现安全性最佳做法来加强 AKS 部署，并将配置作为代码存储在安全的位置。

- [定义 Kubernetes 群集](/azure/developer/terraform/create-k8s-cluster-with-tf-and-aks#define-a-kubernetes-cluster)

AKS 代理节点主机 OS 的安全强化

security-hardened-vm-host-image.md

- [如何在 Azure DevOps 中存储代码](/azure/devops/repos/git/gitworkflow?view=azure-devops)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="76-securely-store-custom-operating-system-images"></a>7.6：安全存储自定义操作系统映像

**指导**：不适用于 Azure Kubernetes 服务 (AKS)。 默认情况下，AKS 提供安全优化主机操作系统 (OS)。 当前没有用于选择备用或自定义操作系统的选项。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7：部署 Azure 资源的配置管理工具

**指导**：使用 Azure Policy 对可以使用内置策略定义以及“Microsoft.ContainerService”命名空间中的 Azure Policy 别名在订阅中创建的资源类型设置限制。 

创建自定义策略以审核和强制执行系统配置。 开发一个用于管理策略例外的流程和管道。

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [如何使用别名](../governance/policy/concepts/definition-structure.md#aliases)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8：部署操作系统的配置管理工具

**指导**：Azure Kubernetes 服务 (AKS) 群集部署在具有安全优化 OS 的虚拟主机上。 主机 OS 还包含了附加的安全强化步骤，以减少攻击面，并允许以安全的方式部署容器。 

请参阅 AKS 主机中内置的 Internet 安全中心 (CIS) 控制列表。  

- [AKS 代理节点主机 OS 的安全强化](security-hardened-vm-host-image.md)

- [了解 AKS 虚拟主机中的安全强化](security-hardened-vm-host-image.md)

- [了解 AKS 群集的状态配置](concepts-clusters-workloads.md#control-plane)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9：为 Azure 资源实施自动配置监视

**指导**：使用安全中心对与 Azure Kubernetes 服务 (AKS) 部署相关的资源执行基线扫描。 示例资源包括但不限于 AKS 群集本身、部署 AKS 群集的虚拟网络、用于跟踪 Terraform 状态的 Azure 存储帐户，或用于 AKS 群集 OS 和数据磁盘的加密密钥的 Azure Key Vault 实例。

- [如何在 Azure 安全中心修正建议](../security-center/security-center-remediate-recommendations.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10：为操作系统实施自动配置监视

**指导**：使用“计算 &amp; 应用”部分下的安全中心容器建议，为 Azure Kubernetes 服务 (AKS) 群集执行基线扫描。 当发现配置问题或漏洞时，安全中心仪表板中将显示通知。 这需要启用可选的容器注册表捆绑包，该捆绑包允许安全中心扫描映像。  

- [了解 Azure 安全中心容器建议](../security-center/container-security.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="711-manage-azure-secrets-securely"></a>7.11：安全管理 Azure 机密

**指导**：使用 FlexVolume 驱动程序将 Azure Key Vault 与 Azure Kubernetes 服务 (AKS) 群集集成。 使用 Azure Key Vault，可存储并定期轮换凭据、存储帐户密钥或证书等机密。 FlexVolume 驱动程序允许 AKS 群集以本机方式检索 Key Vault 中的凭据，并仅将其安全地提供给发出请求的 Pod。 可使用 Pod 托管标识来请求访问 Key Vault，并通过 FlexVolume 驱动程序检索所需凭据。 确保启用 Key Vault 软删除。 

可通过不在应用程序代码中定义凭据来限制凭据暴露。 

避免使用固定或共享凭据。 

- [Azure Kubernetes Service (AKS) 中的应用程序和群集的安全性概念](concepts-security.md)

- [如何对 AKS 群集使用 Key Vault](developer-best-practices-pod-security.md#limit-credential-exposure)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="712-manage-identities-securely-and-automatically"></a>7.12：安全自动管理标识

**指导**：请勿将应用程序代码中的凭据定义为安全性最佳做法。 使用 Azure 资源的托管标识，允许 pod 针对 Azure 中支持它的任何服务（包括 Azure Key Vault）进行身份验证。 为 pod 分配 Azure 标识以对 Azure Active Directory (Azure AD) 进行身份验证，并接收一个数字令牌，该令牌可以呈现给其他 Azure 服务，这些服务检查 pod 是否被授权访问该服务并执行所需的操作。 

请注意，Pod 托管标识仅适用于 Linux Pod 和容器映像。 设置 Azure Key Vault 以存储和检索数字密钥和凭据。 可以在 Azure Key Vault 中存储密钥（如用于加密 OS 磁盘的密钥）和 AKS 群集数据。

服务主体也可以在 AKS 群集中使用。 但是，使用服务主体的群集最终可能会达到这样一种状态，即，必须续订服务主体才能让群集保持正常运行。 管理服务主体会增加复杂性，这也是托管标识使用起来更简单的原因。 服务主体和托管标识适用相同的权限要求。

- [了解 Azure Kubernetes Service (AKS) 的托管标识和 Key Vault](developer-best-practices-pod-security.md#limit-credential-exposure)

- [Azure Active Directory Pod 标识](https://github.com/Azure/aad-pod-identity)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13：消除意外的凭据透露

**指南**：实施凭据扫描程序来识别代码中的凭据。 凭据扫描程序还会建议你将发现的凭据转移到更安全的位置，例如包含建议的 Azure Key Vault。

可通过不在应用程序代码中定义凭据来限制凭据暴露。 并避免使用共享凭据。 Azure Key Vault 应用于存储和检索数字密钥和凭据。 使用 Azure 资源的托管标识，让 Pod 请求访问其他资源。 

- [如何设置凭据扫描程序](https://secdevtools.azurewebsites.net/helpcredscan.html)

- [Pod 安全性的开发人员最佳做法](developer-best-practices-pod-security.md)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="malware-defense"></a>恶意软件防护

[有关详细信息，请参阅 *Azure 安全基线：* 恶意软件防护](../security/benchmarks/security-control-malware-defense.md)。

### <a name="81-use-centrally-managed-antimalware-software"></a>8.1：使用集中管理的反恶意软件

**指导**：AKS 代表你管理代理节点的生命周期和操作 - 不支持修改与该代理节点关联的 IaaS 资源。 但是，对于 Linux 节点，你可以使用守护程序集来安装自定义软件，例如反恶意软件解决方案。

- [安全警报参考指南](../security-center/alerts-reference.md)

- [容器的警报 - Azure Kubernetes 服务群集](../security-center/alerts-reference.md#alerts-akscluster)

- [AKS 共担责任和守护程序集](support-policies.md#shared-responsibility)

**Azure 安全中心监视**：不适用

**责任**：共享

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2：预先扫描要上传到非计算 Azure 资源的文件

**指南**：预扫描正在上传到 AKS 资源的任何文件。 如果使用 Azure 存储帐户作为数据存储或跟踪 AKS 群集的 Terraform 状态，请使用安全中心的数据服务威胁检测来检测已上传到存储帐户的恶意软件。 

- [了解 Azure 安全中心的数据服务威胁检测](../security-center/azure-defender.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="83-ensure-antimalware-software-and-signatures-are-updated"></a>步骤 8.3：确保反恶意软件和签名已更新

**指导**：AKS 代表你管理代理节点的生命周期和操作 - 不支持修改与该代理节点关联的 IaaS 资源。 但是，对于 Linux 节点，你可以使用守护程序集来安装自定义软件，例如反恶意软件解决方案。

- [安全警报参考指南](../security-center/alerts-reference.md)

- [容器的警报 - Azure Kubernetes 服务群集](../security-center/alerts-reference.md#alerts-akscluster)

- [AKS 共担责任和守护程序集](support-policies.md#shared-responsibility)

**Azure 安全中心监视**：不适用

**责任**：共享

## <a name="data-recovery"></a>数据恢复

[有关详细信息，请参阅 *Azure 安全基线：* 数据恢复](../security/benchmarks/security-control-data-recovery.md)。

### <a name="91-ensure-regular-automated-back-ups"></a>9.1：确保定期执行自动备份

**指导**：使用适合存储类型的适当工具（如 Velero）备份数据，它可以备份持久卷以及其他群集资源和配置。 定期验证这些备份的完整性和安全性。 

备份前从应用程序中删除状态。 在无法执行此操作的情况下，请从永久卷备份数据，并定期测试还原操作，以验证数据完整性和所需的过程。

- [AKS 中存储和备份的最佳做法](operator-best-practices-storage.md)

- [AKS 中业务连续性和灾难恢复的最佳做法](operator-best-practices-multi-region.md)

- [了解 Azure Site Recovery](../site-recovery/site-recovery-overview.md)

- [如何在 Azure 上设置 Velero](https://github.com/vmware-tanzu/velero-plugin-for-microsoft-azure/blob/master/README.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2：执行完整系统备份，并备份客户管理的所有密钥

**指导**：使用适合存储类型的适当工具（如 Velero）备份数据，它可以备份持久卷以及其他群集资源和配置。 

使用 PowerShell 命令定期自动备份 Key Vault 证书、密钥、托管存储帐户和机密。 

例如： 。

Backup-AzKeyVaultCertificate Backup-AzKeyVaultKey Backup-AzKeyVaultManagedStorageAccount Backup-AzKeyVaultSecret

- [如何备份 Key Vault 证书](/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate)

- [如何备份 Key Vault 密钥](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

- [如何备份 Key Vault 托管存储帐户](/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount)

- [如何备份 Key Vault 机密](/powershell/module/azurerm.keyvault/backup-azurekeyvaultsecret)

- [如何启用 Azure 备份](../backup/index.yml)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3：验证所有备份，包括客户管理的密钥

**指导**：定期在 Velero 备份中执行内容的数据还原。 如果需要，对还原到隔离的虚拟网络进行测试。

使用 PowerShell 命令定期执行 Key Vault 证书、密钥、托管存储帐户和机密的数据还原。 

例如： 。

Restore-AzKeyVaultCertificate Restore-AzKeyVaultKey Restore-AzKeyVaultManagedStorageAccount Restore-AzKeyVaultSecret

- [如何还原 Key Vault 证书](/powershell/module/azurerm.keyvault/restore-azurekeyvaultcertificate?view=azurermps-6.13.0)

- [如何还原 Key Vault 密钥](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

- [如何还原 Key Vault 托管存储帐户](/powershell/module/az.keyvault/backup-azkeyvaultmanagedstorageaccount)

- [如何还原 Key Vault 机密](/powershell/module/azurerm.keyvault/restore-azurekeyvaultsecret?view=azurermps-6.13.0)

- [如何从 Azure 虚拟机备份恢复文件](../backup/backup-azure-restore-files-from-vm.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4：确保保护备份和客户管理的密钥

**指导**：使用适合存储类型的适当工具（如 Velero）备份数据，它可以备份持久卷以及其他群集资源和配置。 

如果 Azure Key Vault 正在用于 Azure Kubernetes 服务 (AKS) 部署，请在 Key Vault 中启用软删除，以防止意外删除或恶意删除密钥。

- [了解 Azure 存储服务加密](../storage/common/storage-service-encryption.md)

- [如何在 Key Vault 中启用“软删除”](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="incident-response"></a>事件响应

[有关详细信息，请参阅 *Azure 安全基线：* 事件响应](../security/benchmarks/security-control-incident-response.md)。

### <a name="101-create-an-incident-response-guide"></a>10.1：创建事件响应指导

**指导**：为组织制定事件响应指南。 确保在书面的事件响应计划中定义人员职责，以及事件处理/管理从检测到事件后审查的各个阶段。

- [如何在 Azure 安全中心配置工作流自动化](../security-center/security-center-planning-and-operations-guide.md)

- [关于建立自己的安全事件响应流程的指南](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft 安全响应中心事件分析](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [客户还可以利用 NIST 的“计算机安全事件处理指南”来制定他们自己的事件响应计划](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2：创建事件评分和优先级设定过程

**指导**：在安全中心为警报分配严重性的情况下，确定必须首先调查哪些警报的优先级。 严重性取决于安全中心在发出警报时所依据的检测结果和分析结果的置信度，以及导致发出警报的活动的恶意企图的置信度。
清楚地标记订阅（例如生产、非生产），并创建命名系统来对 Azure 资源进行明确标识和分类。

**Azure 安全中心监视**：是

**责任**：客户

### <a name="103-test-security-response-procedures"></a>10.3：测试安全响应过程

**指导**：定期执行演练来测试系统的事件响应功能。 请参阅 NIST 的发布，确定薄弱点和缺口，并根据需要修改计划。

- [适用于 IT 计划和功能的测试、培训和试验计划指南](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4：提供安全事件联系人详细信息，并针对安全事件配置警报通知

**指南**：如果 Microsoft 安全响应中心 (MSRC) 发现非法或未经授权的某方访问了客户的数据，Microsoft 将使用安全事件联系人信息与你取得联系。 事后审查事件，确保问题得到解决。

- [如何设置 Azure 安全中心安全联系人](../security-center/security-center-provide-security-contact-details.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5：将安全警报整合到事件响应系统中

**指导**：使用连续导出功能导出安全中心警报和建议。 使用连续导出可以手动导出或者持续导出警报和建议。 还可以选择安全中心数据连接器，以便根据组织业务需求将警报流式传输到 Azure Sentinel。

- [如何配置连续导出](../security-center/continuous-export.md)

- [如何将警报流式传输到 Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="106-automate-the-response-to-security-alerts"></a>10.6：自动响应安全警报

**指导**：使用 Azure 安全中心内的工作流自动化功能可以通过“逻辑应用”针对安全警报和建议自动触发响应。

- [如何配置工作流自动化和逻辑应用](../security-center/workflow-automation.md)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="penetration-tests-and-red-team-exercises"></a>渗透测试和红队练习

[有关详细信息，请参阅 *Azure 安全基线：* 渗透测试和红队演练](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)。

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1：定期对 Azure 资源执行渗透测试，确保修正所有发现的关键安全问题

指南：遵循 Microsoft Rules of Engagement 以确保渗透测试不违反 Microsoft 政策： https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

- [可在此处详细了解如何针对 Microsoft 托管云基础结构、服务和应用程序执行红队测试和实时站点渗透测试，以及 Microsoft 的相关策略](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure 安全中心监视**：不适用

**责任**：共享

## <a name="next-steps"></a>后续步骤

- 请参阅 [Azure 安全基准](../security/benchmarks/overview.md)
- 详细了解 [Azure 安全基线](../security/benchmarks/security-baselines-overview.md)