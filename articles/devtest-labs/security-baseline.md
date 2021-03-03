---
title: Azure 开发测试实验室的 azure 安全基准
description: Azure 开发测试实验室安全基线为实现 Azure 安全基准中指定的安全建议提供过程指南和资源。
author: msmbaldwin
ms.service: devtest-lab
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 13585173b54c79158161da54f968ba66eb018f6a
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101705732"
---
# <a name="azure-security-baseline-for-azure-devtest-labs"></a>Azure 开发测试实验室的 azure 安全基准

此安全基线将 [Azure 安全性基准1.0 版](../security/benchmarks/overview-v1.md) 中的指导应用于 Azure 开发测试实验室。 Azure 安全基准提供有关如何在 Azure 上保护云解决方案的建议。 内容按照 Azure 安全基准定义的 **安全控件** 进行分组，以及适用于 Azure 开发测试实验室的相关指南。 排除了不适用于 Azure 开发测试实验室的 **控件**。

若要查看 Azure 开发测试实验室如何完全映射到 Azure 安全基准，请参阅 [完整的 Azure 开发测试 labs security 基准映射文件](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)。

## <a name="network-security"></a>网络安全

[有关详细信息，请参阅 *Azure 安全基线：* 网络安全性](../security/benchmarks/security-control-network-security.md)。

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1：保护虚拟网络中的 Azure 资源

**指南**：部署 Azure 开发测试实验室资源时，必须创建或使用现有虚拟网络。 确保所选虚拟网络具有一个网络安全组，该网络安全组应用到其子网和配置了特定于应用程序的受信任端口和源的网络访问控制。 如果实验室资源是使用虚拟网络配置的，则它们不可公开寻址，只能从虚拟网络内部访问。 你还可以选择创建一个网络隔离实验室，其中与实验室环境一起，实验室资源（如存储帐户和密钥保管库）也将完全隔离，并且仅可通过指定的终结点进行访问。 

根据组织的需求，使用 Azure 防火墙服务跨订阅和虚拟网络集中创建、强制和记录应用程序和网络连接策略。

- [如何为 Azure 开发测试实验室配置虚拟网络](devtest-lab-configure-vnet.md)

- [如何创建网络隔离的开发测试实验室实例](network-isolation.md)

- [如何创建包含安全规则的网络安全组](../virtual-network/tutorial-filter-network-traffic.md)

- [如何部署和配置 Azure 防火墙](../firewall/tutorial-firewall-deploy-portal.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2：监视和记录虚拟网络、子网和 NIC 的配置与流量

**指南**：在部署了 Azure 开发测试实验室资源的网络上部署网络安全组。 为流量审核启用网络安全组流日志。

还可以将 NSG 流日志发送到 Log Analytics 工作区，并使用流量分析来提供有关 Azure 云中流量流的见解。 流量分析的优势包括能够可视化网络活动、识别热点、识别安全威胁、了解流量流模式，以及查明网络不当配置。

- [如何启用 NSG 流日志](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [如何启用和使用流量分析](../network-watcher/traffic-analytics.md)

- [了解 Azure 安全中心提供的网络安全](../security-center/security-center-network-recommendations.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="13-protect-critical-web-applications"></a>1.3：保护关键 Web 应用程序

**指南**：在使用 azure 资源管理器模板部署的关键 Web 应用程序之前部署 Azure Web 应用程序防火墙 (WAF) ，以便对传入流量进行额外检查。 启用 WAF 的诊断设置，并将日志引入存储帐户、事件中心或 Log Analytics 工作区。

- [如何部署 Azure WAF](../web-application-firewall/ag/create-waf-policy-ag.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4：拒绝与已知恶意的 IP 地址进行通信

**指南**：为实验室部署 Azure 虚拟网络 (VNet) 增强了实验室的安全性和隔离性。 子网、访问控制策略和其他功能也有助于限制访问。 在 VNet 中部署时，Azure 开发测试实验室不能公开寻址，只能从 VNet 中的虚拟机和应用程序进行访问。

在 Azure 虚拟网络上启用 DDoS 标准保护，防范 DDoS 攻击。 使用 Azure 安全中心的集成式威胁情报功能拒绝与已知的恶意 IP 地址通信。

在组织的每个网络边界上部署 Azure 防火墙，启用威胁情报并将其配置为针对恶意网络流量执行“发出警报并拒绝”操作。 使用 Azure 安全中心实时网络访问，将 NSG 配置为只能在有限时间内将终结点公开给已批准的 IP 地址。 使用 Azure 安全中心自适应网络强化，推荐基于实际流量和威胁情报限制端口和源 IP 的 NSG 配置。

- [如何为 Azure 开发测试实验室配置虚拟网络](devtest-lab-configure-vnet.md)

- [如何配置 DDoS 防护](/azure/virtual-network/manage-ddos-protection)

- [如何部署 Azure 防火墙](../firewall/tutorial-firewall-deploy-portal.md)

- [了解 Azure 安全中心集成的威胁情报](/azure/security-center/threat-protection)

- [了解 Azure 安全中心自适应网络强化](../security-center/security-center-adaptive-network-hardening.md)

- [了解 Azure 安全中心实时网络访问控制](../security-center/security-center-just-in-time.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="15-record-network-packets"></a>1.5：记录网络数据包

**指南**：在实验室虚拟网络上启用网络观察程序数据包捕获，调查异常活动。 

- [如何启用 NSG 流日志](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [如何启用和使用流量分析](../network-watcher/traffic-analytics.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6：部署基于网络的入侵检测/入侵防护系统 (IDS/IPS)

**指导**：使用部署在启用威胁情报的虚拟网络上的 Azure 防火墙。 基于 Azure 防火墙威胁情报的筛选功能可以发出警报，并拒绝传入和传出已知恶意 IP 地址和域的流量。 IP 地址和域源自 Microsoft 威胁智能源。 如果你的组织需要 Azure 防火墙提供的其他功能，请从 Azure Marketplace 中选择一个适用于具有负载检查功能的 ID/IPS 功能的适当产品/服务。

在组织的每个网络边界上部署所选的防火墙解决方案，以检测和/或拒绝恶意流量。

- [Azure 市场](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)
 

- [如何部署 Azure 防火墙](../firewall/tutorial-firewall-deploy-portal.md)
 

- [如何配置 Azure 防火墙警报](../firewall/threat-intel.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="17-manage-traffic-to-web-applications"></a>1.7：管理发往 Web 应用程序的流量

**指南**：在使用开发测试实验室基于 Azure 资源管理器的环境（包括 web 应用程序）时，请为受信任的证书部署启用了 HTTPS/TLS 的 Azure 应用程序网关。

- [如何部署应用程序网关](../application-gateway/quick-create-portal.md)

- [如何将应用程序网关配置为使用 HTTPS](../application-gateway/create-ssl-portal.md)

- [了解 Azure Web 应用程序网关的第七层负载均衡](../application-gateway/overview.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8：最大程度地降低网络安全规则的复杂性和管理开销

**指南**：使用虚拟网络服务标记定义针对开发测试实验室资源配置的网络安全组或 Azure 防火墙上的网络访问控制。 创建安全规则时，可以使用服务标记代替特定的 IP 地址。 在规则的相应源或目标字段中指定服务标记名称（例如 ApiManagement），可以允许或拒绝相应服务的流量。 Microsoft 会管理服务标记包含的地址前缀，并会在地址发生更改时自动更新服务标记。

还可使用应用程序安全组来帮助简化复杂的安全配置。 使用应用程序安全组可将网络安全性配置为应用程序结构的固有扩展，从而可以基于这些组将虚拟机分组以及定义网络安全策略。

- [了解并使用服务标记](../virtual-network/service-tags-overview.md)

- [了解并使用应用程序安全组](/azure/virtual-network/security-overview#application-security-groups)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9：维护网络设备的标准安全配置

**指导**：使用 Azure Policy 为网络资源定义并实施标准安全配置。

还可以使用 Azure 蓝图，通过在单个蓝图定义中打包关键环境项目（例如 Azure 资源管理器模板、RBAC 控制措施和策略），来简化大规模的 Azure 部署。 可将蓝图应用到新的订阅，并通过版本控制来微调控制措施和管理。

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [用于网络的 Azure Policy 示例](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network)

- [如何创建 Azure 蓝图](../governance/blueprints/create-blueprint-portal.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="110-document-traffic-configuration-rules"></a>1.10：阐述流量配置规则

**指南**：对与 Azure 开发测试实验室部署关联的网络资源使用标记，以便以逻辑方式将它们组织到分类。 对于单个 NSG 规则，请使用“说明”字段针对允许流量传入/传出网络的任何规则指定业务需求和/或持续时间等。

使用标记相关的任何内置 Azure Policy 定义（例如“需要标记及其值”）来确保使用标记创建所有资源，并在有现有资源不带标记时发出通知。

可以使用 Azure PowerShell 或 Azure CLI 根据资源的标记查找资源或对其执行操作。

- [如何创建和使用标记](../azure-resource-manager/management/tag-resources.md)

- [如何创建虚拟网络](../virtual-network/quick-create-portal.md)

- [如何创建采用安全配置的 NSG](../virtual-network/tutorial-filter-network-traffic.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11：使用自动化工具来监视网络资源配置和检测更改

**指南**：使用 Azure 活动日志监视资源配置，并检测对 Azure 资源所做的更改。 在 Azure Monitor 中创建当关键资源发生更改时触发的警报。

- [如何查看和检索 Azure 活动日志事件](/azure/azure-monitor/platform/activity-log#view-the-activity-log)

- [如何在 Azure Monitor 中创建警报](/azure/azure-monitor/platform/alerts-activity-log)

**责任**：客户

**Azure 安全中心监视**：无

## <a name="logging-and-monitoring"></a>日志记录和监视

[有关详细信息，请参阅 *Azure 安全基线：* 日志记录和监视](../security/benchmarks/security-control-logging-monitoring.md)。

### <a name="21-use-approved-time-synchronization-sources"></a>2.1：使用批准的时间同步源

**指导**：由 Microsoft 维护 Azure 资源的时间源。 不过，你可以管理计算资源的时间同步设置。

- [Azure 中 Windows VM 的时间同步](../virtual-machines/windows/time-sync.md)

**责任**：共享

**Azure 安全中心监视**：无

### <a name="22-configure-central-security-log-management"></a>2.2：配置中心安全日志管理

**指南**：启用 Azure 活动日志诊断设置，并将日志发送到 Log Aalytics 工作区、Azure 事件中心或 Azure 存储帐户进行存档。 活动日志可让你深入了解在 Azure 开发测试实验室实例的管理平面上完成的操作。 使用 Azure 活动日志数据，您可以确定任何写入操作的 "内容、人员和时间" (PUT、POST、DELETE) 在开发测试实验室实例的管理平面级别完成。

- [创建诊断设置以将平台日志和指标发送到不同目标](/azure/azure-monitor/platform/diagnostic-settings)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：为 Azure 资源启用审核日志记录

**指南**：启用 Azure 活动日志诊断设置，并将日志发送到 Log Aalytics 工作区、Azure 事件中心或 Azure 存储帐户进行存档。 活动日志可让你深入了解在 Azure 开发测试实验室实例的管理平面上完成的操作。 使用 Azure 活动日志数据，可以确定任何写入操作的 "操作内容、人员和时间" (PUT、POST、DELETE) 在开发测试实验室实例的管理平面级别完成。

- [创建诊断设置以将平台日志和指标发送到不同目标](/azure/azure-monitor/platform/diagnostic-settings)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4：从操作系统收集安全日志

**指南**： Azure 开发测试实验室虚拟机 (vm) 由客户创建和拥有。 因此，组织负责监视它。 可以使用 Azure 安全中心监视计算操作系统。 安全中心从操作系统收集的数据包括 OS 类型和版本、OS（Windows 事件日志）、正在运行的进程、计算机名称、IP 地址和登录用户。 Log Analytics 代理还会收集故障转储文件。

有关详细信息，请参阅以下文章：

- [如何使用 Azure Monitor 收集 Azure 虚拟机内部主机日志](/azure/azure-monitor/learn/quick-collect-azurevm)

- [了解 Azure 安全中心数据收集](../security-center/security-center-enable-data-collection.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="25-configure-security-log-storage-retention"></a>2.5：配置安全日志存储保留期

**指南**：在 Azure Monitor 中，根据组织的符合性规定，设置与 Azure 开发测试实验室实例关联的 Log Analytics 工作区的日志保持期。

- [有关详细信息，请参阅以下文章](/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="26-monitor-and-review-logs"></a>2.6：监视和审查日志

**指导**：启用 Azure 活动日志诊断设置，并将日志发送到 Log Analytics 工作区。 在 Log Analytics 中运行查询以搜索术语，确定趋势，分析模式，并根据可能已为 Azure 开发测试实验室收集的活动日志数据提供许多其他见解。

有关详细信息，请参阅以下文章：

- [如何启用 Azure 活动日志的诊断设置](/azure/azure-monitor/platform/diagnostic-settings)

- [如何收集和分析 Azure Monitor 的 Log Analytics 工作区中的 Azure 活动日志](/azure/azure-monitor/platform/activity-log)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7：针对异常活动启用警报

**指南**：使用 azure Log Analytics 工作区监视和警报与 Azure 开发测试实验室相关的安全日志和事件中的异常活动。

- [如何针对 Log Analytics 日志数据发出警报](/azure/azure-monitor/learn/tutorial-response)

**责任**：客户

**Azure 安全中心监视**：无

## <a name="identity-and-access-control"></a>标识和访问控制

[有关详细信息，请参阅 *Azure 安全基线：* 标识和访问控制](../security/benchmarks/security-control-identity-access-control.md)。

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1：维护管理帐户的清单

**指导**：Azure Active Directory (Azure AD) 具有必须显式分配且可查询的内置角色。 使用 Azure AD PowerShell 模块运行即席查询，以发现属于管理组成员的帐户。

- [如何使用 PowerShell 获取 Azure AD 中的目录角色](/powershell/module/azuread/get-azureaddirectoryrole)

- [如何使用 PowerShell 获取 Azure AD 中目录角色的成员](/powershell/module/azuread/get-azureaddirectoryrolemember)

- [Azure 开发测试实验室角色](devtest-lab-add-devtest-user.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="32-change-default-passwords-where-applicable"></a>3.2：在适用的情况下更改默认密码

**指南**： Azure Active Directory (Azure AD) 没有默认密码的概念。 其他需要密码的 Azure 资源会强制创建具有复杂性要求和最小密码长度要求的密码，这些要求因服务而异。 你负责第三方应用程序和可能使用默认密码的 Marketplace 服务。

开发测试实验室没有默认密码的概念。

**责任**：客户

**Azure 安全中心监视**：无

### <a name="33-use-dedicated-administrative-accounts"></a>3.3：使用专用管理帐户

**指南**：围绕专用管理帐户的使用创建标准操作程序。 使用 Azure 安全中心标识和访问管理来监视管理帐户的数量。

此外，为了帮助你跟踪专用管理帐户，你可以使用 Azure 安全中心或内置的 Azure 策略提供的建议，例如：

- 应该为你的订阅分配了多个所有者

- 应从订阅中删除拥有所有者权限的已弃用帐户
- 应从订阅中删除拥有所有者权限的外部帐户

- [如何使用 Azure 安全中心监视标识和访问（预览）](../security-center/security-center-identity-access.md)

- [如何使用 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Azure 开发测试实验室角色](devtest-lab-add-devtest-user.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4：将单一登录 (SSO) 与 Azure Active Directory 配合使用

**指南**：开发测试实验室使用 Azure Active Directory (Azure AD) 服务进行标识管理。 当你为用户授予基于开发测试实验室的环境的访问权限时，请考虑以下两个重要方面：
- 资源管理：它提供对 Azure 门户的访问，以管理 (创建 Vm、创建环境、启动、停止、重启、删除和应用) 项目等的资源。 使用 azure RBAC)  (azure 基于角色的访问控制在 Azure 中完成资源管理。 你将角色分配给用户，并设置资源和访问级别权限。
- 虚拟机 (网络级) ：在默认配置中，Vm 使用本地管理员帐户。 如果域服务 (Azure Active Directory 域服务 (Azure AD DS) 、本地域或基于云的域) ，则可以将计算机加入域。 然后，用户可以使用域加入项目将其基于域的标识连接到计算机。

- [开发测试实验室的参考体系结构](https://docs.microsoft.com/azure/devtest-labs/devtest-lab-reference-architecture#architecture)

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

**指南**：通过配置为登录和配置 Azure 资源的多重身份验证，使用特权访问工作站 (paw) 。 

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

### <a name="38-manage-azure-resources-only-from-approved-locations"></a>3.8：仅从批准的位置管理 Azure 资源

**指南**：使用条件访问命名位置，仅允许从 IP 地址范围或国家/地区的特定逻辑分组访问。

- [如何在 Azure 中配置命名位置](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="39-use-azure-active-directory"></a>3.9：使用 Azure Active Directory

**指导**：使用 Azure Active Directory (Azure AD) 作为中心身份验证和授权系统。 Azure AD 通过对静态数据和传输中数据使用强加密来保护数据。 Azure AD 还会对用户凭据进行加盐、哈希处理和安全存储操作。

- [如何创建和配置 Azure AD 实例](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10：定期审查和协调用户访问

**指导**：Azure Active Directory (Azure AD) 提供日志来帮助发现过时的帐户。 此外，使用 Azure 标识访问评审来有效地管理组成员身份、访问企业应用程序和角色分配。 可以定期评审用户的访问权限，确保只有适当的用户才持续拥有访问权限。

- [了解 Azure AD 报告](../active-directory/reports-monitoring/overview-reports.md)

- [如何使用 Azure 标识访问评审](../active-directory/governance/access-reviews-overview.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11：监视尝试访问已停用凭据的行为

**指南**：你有权访问 Azure Active Directory (Azure AD) 登录活动、审核和风险事件日志源，这允许你与任何安全信息和事件管理 (SIEM) /Monitoring 工具集成。

可以通过创建 Azure AD 用户帐户的诊断设置并将审核日志和登录日志发送到 Log Analytics 工作区来简化此过程。 你可以在 Log Analytics 工作区中配置警报。

- [如何将 Azure 活动日志集成到 Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12：针对帐户登录行为偏差发出警报

**指导**：使用 Azure Active Directory (Azure AD) 风险和标识保护功能配置对检测到的与用户标识相关的可疑操作的自动响应。

- [如何查看 Azure AD 风险登录](../active-directory/identity-protection/overview-identity-protection.md)

- [如何配置和启用标识保护风险策略](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

**责任**：客户

**Azure 安全中心监视**：无

## <a name="data-protection"></a>数据保护

[有关详细信息，请参阅 *Azure 安全基线：* 数据保护](../security/benchmarks/security-control-data-protection.md)。

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1：维护敏感信息的清单

**指导**：使用标记可以帮助跟踪存储或处理敏感信息的 Azure 资源。

- [如何创建和使用标记](../azure-resource-manager/management/tag-resources.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4.6：使用 Azure RBAC 管理对资源的访问

**指南**：使用 azure RBAC)  (azure 基于角色的访问控制来控制对 Azure 开发测试实验室中的实验室的访问。

- [如何配置 Azure RBAC](../role-based-access-control/role-assignments-portal.md)

- [了解开发测试实验室中的角色](devtest-lab-add-devtest-user.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9：记录对关键 Azure 资源的更改并对此类更改发出警报

**指南**：将 Azure Monitor 与 Azure 活动日志结合使用，以便为开发测试实验室实例和其他关键或相关资源的发生更改创建警报。

- [如何为 Azure 活动日志事件创建警报](/azure/azure-monitor/platform/alerts-activity-log)

- [如何创建开发测试 Labs 活动日志事件的警报](create-alerts.md)

**责任**：客户

**Azure 安全中心监视**：无

## <a name="inventory-and-asset-management"></a>清单和资产管理

[有关详细信息，请参阅 *Azure 安全基线：* 清单和资产管理](../security/benchmarks/security-control-inventory-asset-management.md)。

### <a name="61-use-automated-asset-discovery-solution"></a>6.1：使用自动化资产发现解决方案

**指南**：使用 Azure 资源关系图可查询和发现所有资源 (包括订阅中) 的开发测试实验室资源。 确保你具有适当的 (读取租户中的) 权限，并且可以枚举订阅中的所有 Azure 订阅和资源。

- [如何使用 Azure Graph 创建查询](../governance/resource-graph/first-query-portal.md)

- [如何查看 Azure 订阅](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-4.8.0&amp;preserve-view=true)

- [了解 Azure RBAC](../role-based-access-control/overview.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="62-maintain-asset-metadata"></a>6.2：维护资产元数据

**指导**：将标记应用到 Azure资源，以便有条理地根据分类组织元数据。

- [如何创建和使用标记](../azure-resource-manager/management/tag-resources.md)

- [配置开发测试实验室的标记](devtest-lab-add-tag.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="63-delete-unauthorized-azure-resources"></a>6.3：删除未经授权的 Azure 资源

**指南**：使用标记、管理组和单独的订阅，并根据需要单独的实验室来组织和跟踪实验室与实验室相关的资源。 定期协调清点，并确保从订阅快速删除未经授权的资源。

- [如何创建其他 Azure 订阅](../cost-management-billing/manage/create-subscription.md)

- [如何创建管理组](../governance/management-groups/create-management-group-portal.md)

- [如何使用开发测试实验室创建实验室](devtest-lab-create-lab.md)

- [如何创建和使用标记](devtest-lab-add-tag.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4：定义并维护已批准 Azure 资源的清单

**指南**：根据组织需求为计算资源创建已批准的 Azure 资源和批准的软件的清单。 作为订阅管理员，你还可以使用自适应应用程序控件（Azure 安全中心的一项功能）来帮助你定义一组允许在已配置的实验室计算机上运行的应用程序。 此功能适用于 Azure 和非 Azure Windows（所有版本，不管是经典部署模型还是 Azure 资源管理器部署模型）和 Linux 计算机。

- [如何启用自适应应用程序控制](../security-center/security-center-adaptive-application.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5：监视未批准的 Azure 资源

**指导**：在 Azure Policy 中使用以下内置策略定义，对可以在客户订阅中创建的资源类型施加限制：

- 不允许的资源类型

- 允许的资源类型

此外，还可使用 Azure 资源关系图查询/发现订阅中的资源。 它可以在基于高安全性的环境（例如，具有存储帐户的环境）中提供帮助。

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [如何使用 Azure Graph 创建查询](../governance/resource-graph/first-query-portal.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6：监视计算资源中未批准的软件应用程序

**指导**：可以通过 Azure 自动化在工作负荷和资源的部署、操作和解除授权过程中进行完全的控制。 作为订阅管理员，你可以使用 Azure 虚拟机库存自动收集有关你的订阅中的开发测试实验室 Vm 的所有软件的信息。 "软件名称"、"版本"、"发布者" 和 "刷新时间" 属性可从 Azure 门户中获取。 若要获得安装日期和其他信息的访问权限，客户需要启用来宾级诊断并将 Windows 事件日志置于 Log Analytics 工作区中。

除了使用更改跟踪来监视软件应用程序外，Azure 安全中心的自适应应用程序控制还可以使用机器学习来分析计算机上运行的应用程序，并通过此智能创建允许列表。 此功能极大地简化了配置和维护应用程序允许列表策略的过程，使你能够避免在你的环境中使用不需要的软件。 你可以配置审核模式或强制模式。 审核模式只审核受保护 VM 上的活动。 强制模式确实强制执行这些规则，并确保阻止不允许运行的应用程序。 

- [Azure 自动化简介](../automation/automation-intro.md)

- [如何启用 Azure VM 清单](../automation/automation-tutorial-installed-software.md)

- [了解自适应应用程序控制](../security-center/security-center-adaptive-application.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7：删除未批准的 Azure 资源和软件应用程序

**指导**：可以通过 Azure 自动化在工作负荷和资源的部署、操作和解除授权过程中进行完全的控制。 作为订阅管理员，你可以使用更改跟踪来确定在开发测试实验室中托管的 Vm 上安装的所有软件。 可以实现自己的过程，也可以使用 Azure Automation State Configuration 来删除未经授权的软件。

- [Azure 自动化简介](../automation/automation-intro.md)

- [使用更改跟踪解决方案跟踪环境中的更改](../automation/change-tracking/overview.md)

- [Azure 自动化状态配置概述](../automation/automation-dsc-overview.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="68-use-only-approved-applications"></a>6.8：仅使用已批准的应用程序

**指南**：作为订阅管理员，你可以使用 Azure 安全中心自适应应用程序控制来确保只执行已授权的软件，并且阻止所有未经授权的软件在开发测试实验室中托管的 Azure vm 上执行。

- [如何使用 Azure 安全中心自适应应用程序控制](../security-center/security-center-adaptive-application.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="69-use-only-approved-azure-services"></a>6.9：仅使用已批准的 Azure 服务

**指导**：在 Azure Policy 中使用以下内置策略定义，对可以在客户订阅中创建的资源类型施加限制：
- 不允许的资源类型
- 允许的资源类型

参考资料：

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [如何使用 Azure Policy 拒绝特定的资源类型](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10：维护已获批软件的清单

**指南**：自适应应用程序控制是 Azure 安全中心的一个智能、自动化的端到端解决方案，可帮助你控制哪些应用程序可在 azure 和非 Azure 计算机上运行 (Windows 和 Linux) ，开发测试实验室托管。 请注意，你必须是订阅管理员才能为开发测试实验室中托管的基础计算资源配置此设置。 如果此设置不符合组织的要求，请实现第三方解决方案。

- [如何使用 Azure 安全中心自适应应用程序控制](../security-center/security-center-adaptive-application.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11：限制用户与 Azure 资源管理器进行交互的能力

**指南**：使用 azure 条件性访问，通过为 **Microsoft Azure 管理** 应用配置 **阻止访问**，限制用户与 Azure 资源管理器的交互能力。

- [如何配置条件访问以阻止访问 Azure 资源管理器](../role-based-access-control/conditional-access-azure-management.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="612-limit-users-ability-to-execute-scripts-in-compute-resources"></a>6.12：限制用户在计算资源中执行脚本的能力

**指南**：根据脚本类型，可以使用特定于操作系统的配置或第三方资源来限制用户在开发测试实验室中托管的 vm 内执行脚本的能力。 你还可以使用 Azure 安全中心的自适应应用程序控制，以确保仅执行授权的软件，并且阻止所有未经授权的软件在底层 Azure Vm 上执行。

- [如何在 Windows 环境中控制 PowerShell 脚本的执行](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-7&amp;preserve-view=true)

- [如何使用 Azure 安全中心自适应应用程序控制](../security-center/security-center-adaptive-application.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13：以物理或逻辑方式隔离高风险应用程序

**指南**：你的 Azure 环境中部署的高风险应用程序可使用虚拟网络、子网、订阅、管理组等进行隔离。 使用 Azure 防火墙、Web 应用程序防火墙 (WAF) 或网络安全组 (NSG) 充分保护。

- [为开发测试实验室配置虚拟网络](devtest-lab-configure-vnet.md)

- [Azure 防火墙概述](../web-application-firewall/overview.md)

- [Web 应用程序防火墙概述](../virtual-network/network-security-groups-overview.md)

- [网络安全概述](security-baseline.md)

- [使用 Azure 管理组来组织资源](../governance/management-groups/overview.md)

- [订阅决策指南](/azure/cloud-adoption-framework/decision-guides/subscriptions/)

**责任**：客户

**Azure 安全中心监视**：无

## <a name="secure-configuration"></a>安全配置

[有关详细信息，请参阅 *Azure 安全基线：* 安全配置](../security/benchmarks/security-control-secure-configuration.md)。

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1：为所有 Azure 资源建立安全配置

**指南**：使用 Azure 策略别名创建自定义策略，以审核或强制执行在开发测试实验室中创建的 Azure 资源的配置。 你还可以使用内置的 Azure Policy 定义。

此外，Azure 资源管理器能够以 JavaScript 对象表示法 (JSON) 导出模板，应该对其进行检查，以确保配置满足/超过组织的安全要求。

还可以使用来自 Azure 安全中心的建议作为 Azure 资源的安全配置基线。

- [如何查看可用的 Azure Policy 别名](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&amp;preserve-view=true)

- [教程：创建和管理策略以强制实施符合性](../governance/policy/tutorials/create-and-manage.md)

- [在 Azure 门户中将单资源和多资源导出到模板](../azure-resource-manager/templates/export-template-portal.md)

- [安全建议 - 参考指南](../security-center/recommendations-reference.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="72-establish-secure-operating-system-configurations"></a>7.2：建立安全的操作系统配置

**指南**：使用 Azure 安全中心建议来维护在开发测试实验室中创建的所有基础计算资源的安全配置。 此外，你可以使用自定义的操作系统映像或 Azure 自动化状态配置或开发测试实验室项目来建立你的组织所需的操作系统的安全配置。

- [如何监视 Azure 安全中心建议](../security-center/security-center-recommendations.md)

- [安全建议 - 参考指南](../security-center/recommendations-reference.md)

- [Azure Automation State Configuration 概述](../automation/automation-dsc-overview.md)

- [在 Azure 中上传 VHD 并使用它创建新 Windows VM](../virtual-machines/windows/upload-generalized-managed.md)

- [使用 Azure CLI 从自定义磁盘创建 Linux VM](../virtual-machines/linux/upload-vhd.md)

- [创建自定义映像并将其分发到多个开发测试实验室](image-factory-save-distribute-custom-images.md)

**责任**：共享

**Azure 安全中心监视**：无

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3：维护安全的 Azure 资源配置

**指南**：使用 azure 策略 **deny** 并 **部署（如果不存在** ）规则，在作为开发测试实验室的一部分创建的 Azure 资源中强制执行安全设置。 此外，你可以使用 Azure 资源管理器模板来维护你的组织所需的 Azure 资源的安全配置。

- [了解 Azure Policy 效果](../governance/policy/concepts/effects.md)

- [创建和管理策略以强制实施符合性](../governance/policy/tutorials/create-and-manage.md)

- [Azure 资源管理器模板概述](../azure-resource-manager/templates/overview.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4：维护安全的操作系统配置

**指南**：根据 Azure 安全中心的建议，在作为实验室的一部分创建的底层 Azure 计算资源上执行漏洞评估。 此外，还可以使用 Azure 资源管理器模板、自定义操作系统映像或 Azure 自动化状态配置来维护组织所需的操作系统的安全配置。 你还可以使用 "映像工厂" 解决方案，它是一种配置即代码解决方案，它使用所有所需配置定期自动生成和分发映像。

此外，Microsoft 发布的 Azure Marketplace 虚拟机映像由 Microsoft 进行管理和维护。

- [如何实现 Azure 安全中心漏洞评估建议](../security-center/deploy-vulnerability-assessment-vm.md)

- [Azure Automation State Configuration 概述](../automation/automation-dsc-overview.md)

- [将 VHD 上传到 Azure 并创建新的 VM 的示例脚本](/azure/virtual-machines/scripts/virtual-machines-windows-powershell-upload-generalized-script)

- [如何在开发测试实验室中创建映像工厂](image-factory-create.md)

**责任**：共享

**Azure 安全中心监视**：无

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5：安全存储 Azure 资源的配置

**指南**：使用 Azure DevOps 来安全地存储和管理代码，如自定义 azure 策略、Azure 资源管理器模板和所需的状态配置脚本。 若要访问在 Azure DevOps 中管理的资源，可以授予或拒绝特定用户、内置安全组或 (Azure Active Directory 中定义的组的权限（如果与 Azure DevOps 集成） Azure AD) 。

- [Azure Repos Git 教程](/azure/devops/repos/git/gitworkflow)

- [关于权限和组](https://docs.microsoft.com/azure/devops/organizations/security/about-permissions?view=azure-devops&amp;tabs=preview-page&amp;preserve-view=true)

- [Azure 开发测试实验室与 Azure DevOps 工作流之间的集成](devtest-lab-dev-ops.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="76-securely-store-custom-operating-system-images"></a>7.6：安全存储自定义操作系统映像

**指导**：如果使用自定义映像，请使用 Azure 基于角色的访问控制 (Azure RBAC) 来确保只有授权用户才能访问映像。 使用共享映像库，你可以将映像共享到需要它的特定实验室。 对于容器映像，请将其存储在 Azure 容器注册表中，并使用 Azure RBAC 确保只有经过授权的用户才能访问这些映像。

- [了解 Azure RBAC](../role-based-access-control/rbac-and-directory-admin-roles.md)

- [如何配置 Azure RBAC](../role-based-access-control/quickstart-assign-role-user-portal.md)

- [为开发测试实验室配置共享映像库](configure-shared-image-gallery.md)

- [了解容器注册表的 Azure RBAC](../container-registry/container-registry-roles.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7：部署 Azure 资源的配置管理工具

**指导**：使用 Azure Policy 为 Azure 资源定义和实施标准安全配置。 使用 Azure 策略别名创建自定义策略，以审核或强制执行在开发测试实验室中创建的 Azure 资源的网络配置。 还可以使用与特定资源相关的内置策略定义。 此外，你也可以使用 Azure 自动化来部署配置更改。

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [如何使用别名](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8：部署操作系统的配置管理工具

**指导**：Azure Automation State Configuration 是一个配置管理服务，适用于任何云或本地数据中心内的 Desired State Configuration (DSC) 节点。 可以轻松登记计算机、为其分配声明性配置并查看显示每台计算机是否符合指定的所需状态的报告。 你还可以编写可在每个实验室计算机上安装的自定义项目，以确保它们遵循组织策略。 

- [加入 Azure Automation State Configuration 管理的计算机](../automation/automation-dsc-onboarding.md)

- [为开发测试实验室虚拟机创建自定义项目](devtest-lab-artifact-author.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9：为 Azure 资源实施自动配置监视

**指南**：使用 Azure 安全中心对在开发测试实验室中创建的 Azure 资源执行基线扫描。 此外，使用 Azure Policy 警告和审核 Azure 资源配置。

- [如何在 Azure 安全中心修正建议](../security-center/security-center-remediate-recommendations.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10：为操作系统实施自动配置监视

**指南**：使用 Azure 安全中心对实验室中运行的容器的 OS 和 Docker 设置执行基线扫描。

- [了解 Azure 安全中心容器建议](../security-center/container-security.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="711-manage-azure-secrets-securely"></a>7.11：安全管理 Azure 机密

**指导**：将托管服务标识与 Azure Key Vault 结合使用，以便简化和保护云应用程序的机密管理。

- [在开发测试实验室中配置托管标识以部署 Azure 资源管理器环境](use-managed-identities-environments.md)

- [在开发测试实验室中配置托管标识以部署虚拟机](enable-managed-identities-lab-vms.md)

- [如何创建密钥保管库](../key-vault/general/quick-create-portal.md)

- [如何使用托管标识提供 Key Vault 身份验证](../key-vault/general/authentication.md)

- [如何分配 Key Vault 访问策略](../key-vault/general/assign-access-policy-portal.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="712-manage-identities-securely-and-automatically"></a>7.12：安全自动管理标识

**指导**：使用托管标识在 Azure Active Directory (Azure AD) 中为 Azure 服务提供一个自动托管的标识。 使用托管标识可以向支持 Azure AD 身份验证的任何服务（包括 Key Vault）进行身份验证，无需在代码中放入任何凭据。

- [在开发测试实验室中配置托管标识以部署 Azure 资源管理器环境](use-managed-identities-environments.md)

- [在开发测试实验室中配置托管标识以部署虚拟机](enable-managed-identities-lab-vms.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13：消除意外的凭据透露

**指南**：实施凭据扫描程序来识别代码中的凭据。 凭据扫描程序还会建议将发现的凭据转移到更安全的位置，例如 Azure Key Vault。

- [如何设置凭据扫描程序](https://secdevtools.azurewebsites.net/helpcredscan.html)

**责任**：客户

**Azure 安全中心监视**：无

## <a name="malware-defense"></a>恶意软件防护

[有关详细信息，请参阅 *Azure 安全基线：* 恶意软件防护](../security/benchmarks/security-control-malware-defense.md)。

### <a name="81-use-centrally-managed-antimalware-software"></a>8.1：使用集中管理的反恶意软件

**指南**：使用适用于 Azure 的 Microsoft 反恶意软件持续监视和保护你的资源。 对于 Linux，请使用第三方反恶意软件解决方案。  另外，使用 Azure 安全中心的数据服务威胁检测来检测上传到存储帐户的恶意软件。 

- [如何为 Azure 配置 Microsoft Antimalware](../security/fundamentals/antimalware.md) 

- [Azure 安全中心的威胁防护](/azure/security-center/threat-protection)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2：预先扫描要上传到非计算 Azure 资源的文件

**指南**：在支持 Azure 服务的基础主机上启用了 Microsoft 反恶意软件 (例如，Azure App Service 托管在实验室) ，但是，它不会在你的内容上运行。 

预先扫描上传到非计算 Azure 资源的文件，如应用服务、Data Lake Storage、Blob 存储等。 

使用 Azure 安全中心的数据服务威胁检测来检测已上传到存储帐户的恶意软件。 

- [了解适用于 Azure 的 Microsoft 反恶意软件](../security/fundamentals/antimalware.md) 

- [了解 Azure 安全中心的数据服务威胁检测](/azure/security-center/security-center-alerts-data-services)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="83-ensure-antimalware-software-and-signatures-are-updated"></a>步骤 8.3：确保反恶意软件和签名已更新

**指导**：部署后，Microsoft Antimalware for Azure 会默认自动安装最新的签名、平台和引擎更新。 遵循 Azure 安全中心中的建议： "计算 &amp; 应用"，以确保开发测试实验室基础计算资源的所有终结点都是最新的，并且具有最新的签名。 通过与 Azure 安全中心集成的 Microsoft Defender 高级威胁防护服务，可以使用附加的安全性进一步保护 Windows OS，以降低基于病毒或恶意软件进行攻击的风险。

- [如何为 Azure 部署 Microsoft Antimalware](../security/fundamentals/antimalware.md)

**责任**：客户

**Azure 安全中心监视**：无

## <a name="data-recovery"></a>数据恢复

[有关详细信息，请参阅 *Azure 安全基线：* 数据恢复](../security/benchmarks/security-control-data-recovery.md)。

### <a name="91-ensure-regular-automated-back-ups"></a>9.1：确保定期执行自动备份

**指南**：目前，Azure 开发测试实验室不支持 VM 备份和快照。 但是，可以在开发测试实验室中托管的底层 Azure Vm 上启用和配置 Azure 备份。 而且，您还可以为自动备份配置所需的频率和保留期，前提是您对基础计算资源具有适当的访问权限。 

- [概要了解 Azure VM 备份](../backup/backup-azure-vms-introduction.md)

- [通过 VM 设置备份 Azure VM](../backup/backup-azure-vms-first-look-arm.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2：执行完整系统备份，并备份客户管理的所有密钥

**指南**：目前，Azure 开发测试实验室不支持 VM 备份和快照。 不过，你可以使用 PowerShell 或 REST Api 创建托管在开发测试实验室中的基础 Azure Vm 或附加到这些实例的托管磁盘的快照，前提是你有权访问基础计算资源。 你还可以在 Azure Key Vault 中备份任何客户管理的密钥。

在目标 Azure Vm 上启用 Azure 备份，以及所需的频率和保留期。 它包括完整的系统状态备份。 如果使用的是 Azure 磁盘加密，Azure VM 备份会自动处理客户托管密钥的备份。

- [在使用加密的 Azure Vm 上备份](../backup/backup-azure-vms-encryption.md)

- [Azure VM 备份概述](../backup/backup-azure-vms-introduction.md)

- [概要了解 Azure VM 备份](../backup/backup-azure-vms-introduction.md)

- [如何在 Azure 中备份 Key Vault 密钥](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey?view=azps-4.8.0&amp;preserve-view=true)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3：验证所有备份，包括客户管理的密钥

**指导**：确保能够定期在 Azure 备份中执行内容数据还原。 如有必要，请测试将内容还原到隔离的虚拟网络或订阅。 同时，测试已备份客户托管密钥的还原。

如果你使用的是 Azure 磁盘加密，则可以使用磁盘加密密钥还原 Azure VM。 使用磁盘加密时，可以使用磁盘加密密钥还原 Azure VM。

- [在使用加密的 Azure Vm 上备份](../backup/backup-azure-vms-encryption.md)

- [如何从 Azure VM 备份恢复文件](../backup/backup-azure-restore-files-from-vm.md)

- [如何在 Azure 中还原密钥保管库密钥](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultkey?view=azps-4.8.0&amp;preserve-view=true)

- [如何备份和还原加密的 VM](../backup/backup-azure-vms-encryption.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4：确保保护备份和客户管理的密钥

**指南**：在 Azure 备份中备份托管磁盘时，将静态加密 vm，并) 存储服务加密 (SSE。 Azure 备份还可以备份使用 Azure 磁盘加密进行加密的 Azure VM。 Azure 磁盘加密与在 Key Vault 中作为机密受到保护的 BitLocker 加密密钥 (BEK) 相集成。 Azure 磁盘加密还与 Azure Key Vault 密钥加密密钥 (KEK) 相集成。 在 Key Vault 中启用“软删除”，以防止意外删除或恶意删除密钥。

- [VM 的软删除](../backup/soft-delete-virtual-machines.md)

- [Azure Key Vault 软删除概述](../key-vault/general/soft-delete-overview.md)

**责任**：客户

**Azure 安全中心监视**：无

## <a name="incident-response"></a>事件响应

[有关详细信息，请参阅 *Azure 安全基线：* 事件响应](../security/benchmarks/security-control-incident-response.md)。

### <a name="101-create-an-incident-response-guide"></a>10.1：创建事件响应指导

**指导**：为组织制定事件响应指南。 确保在书面的事件响应计划中定义人员职责，以及事件处理和管理从检测到事件后审查的各个阶段。 

- [关于建立自己的安全事件响应流程的指南](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/) 

- [Microsoft 安全响应中心事件分析](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/) 

- [使用 NIST 的“计算机安全事件处理指南”，帮助制定自己的事件响应计划](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

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

**指导**：定期执行演练来测试系统的事件响应功能，以帮助保护 Azure 资源。 查明弱点和差距，并根据需要修改你的响应计划。 

- [NIST 发布 - IT 计划和功能的测试、训练和演练计划指南](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4：提供安全事件联系人详细信息，并针对安全事件配置警报通知

**指导**：如果 Microsoft 安全响应中心 (MSRC) 发现数据被某方非法访问或未经授权访问，Microsoft 会使用安全事件联系信息联系用户。 事后审查事件，确保问题得到解决。 

- [如何设置 Azure 安全中心安全联系人](../security-center/security-center-provide-security-contact-details.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5：将安全警报整合到事件响应系统中

**指导**：使用连续导出功能导出 Azure 安全中心警报和建议，以便确定 Azure 资源的风险。 使用连续导出可以手动导出或者持续导出警报和建议。 可以使用 Azure 安全中心数据连接器将警报流式传输到 Azure Sentinel。 

- [如何配置连续导出](../security-center/continuous-export.md) 

- [如何将警报流式传输到 Azure Sentinel](../sentinel/connect-azure-security-center.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="106-automate-the-response-to-security-alerts"></a>10.6：自动响应安全警报

**指导**：使用 Azure 安全中心的工作流自动化功能，针对安全警报和建议自动触发响应，以保护 Azure 资源。 

- [如何在安全中心配置工作流自动化](../security-center/workflow-automation.md)

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
