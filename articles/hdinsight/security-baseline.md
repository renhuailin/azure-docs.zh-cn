---
title: 适用于 HDInsight 的 Azure 安全基线
description: HDInsight 安全基线为实现 Azure 安全基准中指定的安全建议提供过程指南和资源。
author: msmbaldwin
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: bf2360bda55735aa8ef4258da5ae47f673f4d71b
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101738916"
---
# <a name="azure-security-baseline-for-hdinsight"></a>适用于 HDInsight 的 Azure 安全基线

此安全基线将 [Azure 安全基准版本 1.0](../security/benchmarks/overview-v1.md) 中的指南应用到 HDInsight。 Azure 安全基准提供有关如何在 Azure 上保护云解决方案的建议。
内容由 Azure 安全基准定义的 **安全控制** 和适用于 HDInsight 的相关指南进行分组。 排除了不适用于 HDInsight 的 **控件**。

 
若要查看 HDInsight 如何完全映射到 Azure 安全基准，请参阅 [完整的 hdinsight 安全基线映射文件](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)。

## <a name="network-security"></a>网络安全

[有关详细信息，请参阅 *Azure 安全基线：* 网络安全性](../security/benchmarks/security-control-network-security.md)。

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1：保护虚拟网络中的 Azure 资源

**指导**：Azure HDInsight 中的外围安全性是通过虚拟网络实现的。 企业管理员可在虚拟网络中创建群集，并使用网络安全组 (NSG) 限制对虚拟网络的访问。 只有入站网络安全组规则中允许的 IP 地址才能与 Azure HDInsight 群集通信。 此配置可实现外围安全性。 部署在虚拟网络中的所有群集还有一个专用终结点，该终结点解析为虚拟网络中的专用 IP 地址，可对群集网关进行专用 HTTP 访问。

为了减少通过渗透丢失数据的风险，请使用 Azure 防火墙限制 Azure HDInsight 群集的出站网络流量。

- [如何在虚拟网络中部署 Azure HDInsight 并使用网络安全组进行保护](hdinsight-create-virtual-network.md)

- [如何通过 Azure 防火墙限制 Azure HDInsight 群集的出站流量](hdinsight-restrict-outbound-traffic.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2：监视并记录虚拟网络、子网和网络接口的配置与流量

**指导**：使用 Azure 安全中心，并修正有关虚拟网络、子网以及用于保护 Azure HDInsight 群集的网络安全组的网络保护建议。 启用网络安全组 (NSG) 流日志，并将日志发送到 Azure 存储帐户以进行流量审核。 还可以将 NSG 流日志发送到 Azure Log Analytics 工作区，并使用 Azure 流量分析来洞察 Azure 云中的通信流。 Azure 流量分析的优势包括能够可视化网络活动、识别热点、识别安全威胁、了解流量流模式，以及查明网络不当配置。

- [如何启用 NSG 流日志](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [如何启用和使用 Azure 流量分析](../network-watcher/traffic-analytics.md)

- [了解 Azure 安全中心提供的网络安全](../security-center/security-center-network-recommendations.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4：拒绝与已知恶意的 IP 地址进行通信

**指导**：为了防范 DDoS 攻击，请在部署 Azure HDInsight 的虚拟网络中启用 Azure DDoS 标准防护。 使用 Azure 安全中心集成的威胁情报来拒绝与已知恶意的或未使用过的 Internet IP 地址进行通信。

- [如何配置 DDoS 防护](/azure/virtual-network/manage-ddos-protection)

- [了解 Azure 安全中心集成的威胁情报](/azure/security-center/security-center-alerts-service-layer)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="15-record-network-packets"></a>1.5：记录网络数据包

**指南**：对于连接到用于保护 Azure HDInsight 群集的子网的 NSG，启用网络安全组 (NSG) 流日志。 将 NSG 流日志记录到 Azure 存储帐户中，以生成流记录。 如果需要调查异常活动，请启用 Azure 网络观察程序数据包捕获。

- [如何启用 NSG 流日志](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [如何启用网络观察程序](../network-watcher/network-watcher-create.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6：部署基于网络的入侵检测/入侵防护系统 (IDS/IPS)

**指导**：可以满足 Azure 安全控制 ID 1.1 的要求；将 Azure HDInsight 群集部署到虚拟网络中，并使用网络安全组 (NSG) 进行保护。

Azure HDInsight 有多个依赖项需要入站流量。 不能通过防火墙设备发送入站管理流量。 所需管理流量的源地址是已知且已发布的。 使用此信息创建网络安全组规则，以便仅允许来自受信任位置的流量，从而保护群集的入站流量。

为了减少通过渗透丢失数据的风险，请使用 Azure 防火墙限制 Azure HDInsight 群集的出站网络流量。

- [如何在虚拟网络中部署 HDInsight 并使用网络安全组进行保护](hdinsight-create-virtual-network.md)

- [了解 HDInsight 依赖关系和防火墙使用情况](hdinsight-restrict-outbound-traffic.md)

- [HDInsight 管理 IP 地址](hdinsight-management-ip-addresses.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8：最大程度地降低网络安全规则的复杂性和管理开销

**指导**：使用虚拟网络服务标记，在附加到部署 Azure HDInsight 群集的子网的网络安全组 (NSG) 中定义网络访问控制。 创建安全规则时，可以使用服务标记代替特定的 IP 地址。 在规则的相应源或目标字段中指定服务标记名称（例如 ApiManagement），可以允许或拒绝相应服务的流量。 Microsoft 会管理服务标记包含的地址前缀，并会在地址发生更改时自动更新服务标记。

- [了解和使用 Azure HDInsight 的服务标记](/azure/virtual-network/security-overview#service-tags)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9：维护网络设备的标准安全配置

**指导**：定义并实施与 Azure HDInsight 群集相关的网络资源的标准安全配置。 在“Microsoft.HDInsight”和“Microsoft.Network”命名空间中使用 Azure Policy 别名创建自定义策略，以审核或强制实施 Azure HDInsight 群集的网络配置。

你还可以使用 Azure 蓝图通过在单个蓝图定义中打包关键环境项目（例如 Azure 资源管理器模板、Azure RBAC 控件和策略）来简化大规模的 Azure 部署。 轻松将蓝图应用到新的订阅和环境，并通过版本控制来微调控制措施和管理。

- [如何查看可用的 Azure Policy 别名](/powershell/module/az.resources/get-azpolicyalias)

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [如何创建 Azure 蓝图](../governance/blueprints/create-blueprint-portal.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="110-document-traffic-configuration-rules"></a>1.10：阐述流量配置规则

**指导**：对与 Azure HDInsight 群集关联的网络安全性和流量流相关的网络安全组 (NSG) 及其他资源使用标记。 对于单个 NSG 规则，请使用“说明”字段针对允许流量传入/传出网络的任何规则指定业务需求和/或持续时间等。

使用标记相关的任何内置 Azure Policy 定义（例如“需要标记及其值”）来确保使用标记创建所有资源，并在有现有资源不带标记时发出通知。

可以使用 Azure PowerShell 或 Azure 命令行接口 (CLI) 根据资源的标记查找资源或对其执行操作。

- [如何创建和使用标记](/azure/azure-resource-manager/resource-group-using-tags)

- [如何创建虚拟网络](../virtual-network/quick-create-portal.md)

- [如何创建采用安全配置的 NSG](../virtual-network/tutorial-filter-network-traffic.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11：使用自动化工具来监视网络资源配置和检测更改

**指导**：使用 Azure 活动日志监视网络资源配置，并检测与 Azure HDInsight 部署相关的网络资源的更改。 在 Azure Monitor 中创建当关键网络资源发生更改时触发的警报。

- [如何查看和检索 Azure 活动日志事件](/azure/azure-monitor/platform/activity-log-view)

- [如何在 Azure Monitor 中创建警报](/azure/azure-monitor/platform/alerts-activity-log)

**责任**：客户

**Azure 安全中心监视**：无

## <a name="logging-and-monitoring"></a>日志记录和监视

[有关详细信息，请参阅 *Azure 安全基线：* 日志记录和监视](../security/benchmarks/security-control-logging-monitoring.md)。

### <a name="22-configure-central-security-log-management"></a>2.2：配置中心安全日志管理

**指导**：可将 Azure HDInsight 群集加入 Azure Monitor，以聚合群集生成的安全数据。 利用自定义查询来检测和应对环境中的威胁。 

- [如何将 Azure HDInsight 群集加入到 Azure Monitor](hdinsight-hadoop-oms-log-analytics-tutorial.md)

- [如何为 Azure HDInsight 群集创建自定义查询](hdinsight-hadoop-oms-log-analytics-use-queries.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：为 Azure 资源启用审核日志记录

**指导**：为 HDInsight 群集启用 Azure Monitor，并将日志定向到 Log Analytics 工作区。 这会记录所有 Azure HDInsight 群集节点的相关群集信息和 OS 指标。

- [如何为 HDInsight 群集启用日志记录](hdinsight-hadoop-oms-log-analytics-tutorial.md)

- [如何查询 HDInsight 日志](hdinsight-hadoop-oms-log-analytics-use-queries.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4：从操作系统收集安全日志

**指导**：将 Azure HDInsight 群集加入 Azure Monitor。 确保根据组织的合规性规章为使用的 Log Analytics 工作区设置日志保留期。

- [如何将 Azure HDInsight 群集加入到 Azure Monitor](hdinsight-hadoop-oms-log-analytics-tutorial.md)

- [如何配置 Log Analytics 工作区保留期](/azure/azure-monitor/platform/manage-cost-storage)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="25-configure-security-log-storage-retention"></a>2.5：配置安全日志存储保留期

**指导**：将 Azure HDInsight 群集加入 Azure Monitor。 确保根据组织的合规性规章为使用的 Azure Log Analytics 工作区设置日志保留期。

- [如何将 Azure HDInsight 群集加入到 Azure Monitor](hdinsight-hadoop-oms-log-analytics-tutorial.md)

- [如何配置 Log Analytics 工作区保留期](/azure/azure-monitor/platform/manage-cost-storage)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="26-monitor-and-review-logs"></a>2.6：监视和查看日志

**指导**：使用 Azure Log Analytics 工作区查询来查询 Azure HDInsight 日志：

- [如何为 Azure HDInsight 群集创建自定义查询](hdinsight-hadoop-oms-log-analytics-use-queries.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7：针对异常活动启用警报

**指导**：使用 Azure Log Analytics 工作区在与 Azure HDInsight 群集相关的安全日志和事件中监视异常活动并对其发出警报。

- [如何在 Azure 安全中心管理警报](../security-center/security-center-managing-and-responding-alerts.md)

- [如何针对 Log Analytics 日志数据发出警报](/azure/azure-monitor/learn/tutorial-response)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="28-centralize-anti-malware-logging"></a>2.8：集中管理反恶意软件日志记录

**指导**：Azure HDInsight 中预装了 Clamscan，并且已针对群集节点映像启用了 Clamscan，但是，必须管理 Clamscan，并手动聚合/监视该软件生成的任何日志。

- [了解 Clamscan](https://docs.microsoft.com/azure/hdinsight/hdinsight-faq#security-and-certificates)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="29-enable-dns-query-logging"></a>2.9：启用 DNS 查询日志记录

**指导**：实施用于 DNS 日志记录的第三方解决方案。

**责任**：客户

**Azure 安全中心监视**：无

### <a name="210-enable-command-line-audit-logging"></a>2.10：启用命令行审核日志记录

**指导**：为每个节点手动配置控制台日志记录。

**责任**：客户

**Azure 安全中心监视**：无

## <a name="identity-and-access-control"></a>标识和访问控制

[有关详细信息，请参阅 *Azure 安全基线：* 标识和访问控制](../security/benchmarks/security-control-identity-access-control.md)。

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1：维护管理帐户的清单

**指导**：维护预配 Azure HDInsight 群集期间创建的本地管理帐户以及创建的任何其他帐户的记录。 此外，如果使用 Azure Active Directory (Azure AD) 集成，Azure AD 具有必须显式分配的内置角色，因此可查询。 使用 Azure AD PowerShell 模块执行即席查询，以发现属于管理组的成员的帐户。

此外，可以使用 Azure 安全中心标识和访问管理建议。

- [如何使用 PowerShell 获取 Azure AD 中的目录角色](/powershell/module/azuread/get-azureaddirectoryrole)

- [如何使用 PowerShell 获取 Azure AD 中目录角色的成员](/powershell/module/azuread/get-azureaddirectoryrolemember)

- [如何使用 Azure 安全中心监视标识和访问](../security-center/security-center-identity-access.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="32-change-default-passwords-where-applicable"></a>3.2：在适用的情况下更改默认密码

**指导**：预配群集时，Azure 会要求创建新的密码用于访问 Web 门户和安全外壳 (SSH)。 没有要更改的默认密码，但是，可以指定不同的密码分别用于访问 SSH 和 Web 门户。

- [如何在预配 Azure HDInsight 群集时设置密码](hdinsight-hadoop-linux-use-ssh-unix.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="33-use-dedicated-administrative-accounts"></a>3.3：使用专用管理帐户

**指南**：将 Azure HDInsight 群集的身份验证与 Azure Active Directory (Azure AD) 集成。 围绕专用管理帐户的使用创建策略和过程。

此外，可以使用 Azure 安全中心标识和访问管理建议。

- [如何将 Azure HDInsight 身份验证与 Azure AD 集成](domain-joined/apache-domain-joined-configure-using-azure-adds.md)

- [如何使用 Azure 安全中心监视标识和访问](../security-center/security-center-identity-access.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4：使用 Azure Active Directory 单一登录 (SSO)

**指南**：通过使用多重身份验证，使用 AZURE HDInsight ID 代理登录到企业安全性套餐 (ESP) 群集，而无需提供任何密码。 如果已登录到其他 Azure 服务（例如 Azure 门户），可以使用单一登录 (SSO) 体验登录到 Azure HDInsight 群集。

- [如何启用 Azure HDInsight ID 代理](https://docs.microsoft.com/azure/hdinsight/domain-joined/identity-broker#enable-hdinsight-id-broker)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5：对所有基于 Azure Active Directory 的访问使用多重身份验证

**指南**：启用 Azure Active Directory (Azure AD) 多重身份验证，并遵循 Azure 安全中心的标识和访问管理建议。 配置有企业安全性套餐的 Azure HDInsight 群集可以连接到域，使域用户能够使用其域凭据对群集进行身份验证和运行大数据作业。 当使用多重身份验证进行身份验证时，用户将面临第二个身份验证因素的挑战。

- [如何在 Azure 中启用多重身份验证](../active-directory/authentication/howto-mfa-getstarted.md)

- [如何在 Azure 安全中心监视标识和访问](../security-center/security-center-identity-access.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6：对所有管理任务使用专用计算机（特权访问工作站）

**指南**：通过配置为登录并配置 Azure HDInsight 群集和相关资源的多重身份验证，使用 paw (特权访问工作站) 。

- [了解特权访问工作站](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [如何在 Azure 中启用多重身份验证](../active-directory/authentication/howto-mfa-getstarted.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7：记录来自管理帐户的可疑活动并对其发出警报

**指导**：配置有企业安全性套餐的 Azure HDInsight 群集可以连接到域，使域用户能够使用其域凭据进行身份验证。 在 Azure AD 环境中发生可疑或不安全活动时，可以使用 Azure Active Directory (Azure AD) 安全报告来生成日志和警报。 使用 Azure 安全中心监视标识和访问活动。

- [如何确定标记为存在风险活动的 Azure AD 用户](/azure/active-directory/reports-monitoring/concept-user-at-risk)

- [如何在 Azure 安全中心监视用户的标识和访问活动](../security-center/security-center-identity-access.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8：仅从批准的位置管理 Azure 资源

**指导**：配置有企业安全性套餐的 Azure HDInsight 群集可以连接到域，使域用户能够使用其域凭据进行身份验证。 使用条件访问命名位置，仅允许从 IP 地址范围或国家/地区的特定逻辑分组进行访问。

- [如何在 Azure 中配置命名位置](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="39-use-azure-active-directory"></a>3.9：使用 Azure Active Directory

**指导**：使用 Azure Active Directory (Azure AD) 作为中心身份验证和授权系统。 Azure AD 通过对静态数据和传输中数据使用强加密来保护数据。 Azure AD 还会对用户凭据进行加盐、哈希处理和安全存储操作。

配置有企业安全性套餐 (ESP) 的 Azure HDInsight 群集可以连接到域，使域用户能够使用其域凭据对群集进行身份验证。

- [如何创建和配置 Azure AD 实例](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [如何在 Azure HDInsight 中通过 Azure AD 域服务配置企业安全性套餐](domain-joined/apache-domain-joined-configure-using-azure-adds.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10：定期审查和协调用户访问

**指南**：使用 Azure Active Directory (Azure AD) 使用 Azure HDInsight 群集进行身份验证。 Azure AD 提供日志来帮助发现过时的帐户。 此外，请使用 Azure 标识访问评审来有效管理组成员身份、对企业应用程序的访问和角色分配。 可以定期评审用户的访问权限，确保只有适当的用户才持续拥有访问权限。

- [如何使用 Azure 标识访问评审](../active-directory/governance/access-reviews-overview.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11：监视尝试访问已停用凭据的行为

**指南**：使用 Azure Active Directory (Azure AD) 登录和审核日志来监视是否尝试访问停用的帐户;这些日志可集成到任何第三方 SIEM/监视工具中。

可以通过创建 Azure AD 用户帐户的诊断设置，将审核日志和登录日志发送到 Azure Log Analytics 工作区来简化此过程。 在 Azure Log Analytics 工作区中配置所需的警报。

- [如何将 Azure 活动日志集成到 Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12：针对帐户登录行为偏差发出警报

**指导**：配置有企业安全性套餐 (ESP) 的 Azure HDInsight 群集可以连接到域，使域用户能够使用其域凭据对群集进行身份验证。 使用 Azure Active Directory (Azure AD) 风险检测和标识保护功能，配置对检测到的与用户标识相关的可疑操作的自动响应。 此外，可将数据引入 Azure Sentinel 以做进一步调查。

- [如何查看 Azure AD 风险登录](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [如何配置和启用标识保护风险策略](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13：在支持场合下为 Microsoft 提供对相关客户数据的访问权限

**指导**：不可用；Azure HDInsight 尚不支持客户密码箱。

- [支持客户密码箱的服务列表](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability)

**责任**：客户

**Azure 安全中心监视**：无

## <a name="data-protection"></a>数据保护

[有关详细信息，请参阅 *Azure 安全基线：* 数据保护](../security/benchmarks/security-control-data-protection.md)。

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1：维护敏感信息的清单

**指导**：对与 Azure HDInsight 部署相关的资源使用标记，以帮助跟踪存储或处理敏感信息的 Azure 资源。

- [如何创建和使用标记](/azure/azure-resource-manager/resource-group-using-tags)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2：隔离存储或处理敏感信息的系统

**指导**：为开发、测试和生产实现单独的订阅和/或管理组。 Azure HDInsight 群集和任何关联的存储帐户都应由虚拟网络/子网分开、正确地标记并在网络安全组 (NSG) 或 Azure 防火墙中得到保护。 群集数据应包含在受保护的 Azure 存储帐户或 Azure Data Lake Storage（Gen1 或 Gen2）中。

- [选择 Azure HDInsight 群集的存储选项](hdinsight-hadoop-compare-storage-options.md)

- [如何保护 Azure Data Lake Storage](../data-lake-store/data-lake-store-security-overview.md)

- [如何保护 Azure 存储帐户](/azure/storage/common/storage-security-guide)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3：监视和阻止未经授权的敏感信息传输

**指导**：对于存储或处理敏感信息的 Azure HDInsight 群集，请使用标记将该群集和相关资源标记为敏感。 为了减少通过渗透丢失数据的风险，请使用 Azure 防火墙限制 Azure HDInsight 群集的出站网络流量。

对于 Microsoft 管理的底层平台，Microsoft 会将所有客户内容视为敏感数据，并会全方位地防范客户数据丢失和遭到透露。 为了确保 Azure 中的客户数据保持安全，Microsoft 实施并维护了一套可靠的数据保护控制措施和功能。

- [如何通过 Azure 防火墙限制 Azure HDInsight 群集的出站流量](hdinsight-restrict-outbound-traffic.md)

- [了解 Azure 中的客户数据保护](../security/fundamentals/protection-customer-data.md)

**责任**：共享

**Azure 安全中心监视**：无

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4：加密传输中的所有敏感信息

**指导**：加密传输中的所有敏感信息。 确保连接到 Azure HDInsight 群集或群集数据存储（Azure 存储帐户或 Azure Data Lake Storage Gen1/Gen2）的任何客户端能够协商 TLS 1.2 或更高版本。 默认情况下，Microsoft Azure 资源将协商 TLS 1.2。 

- [了解传输中 Azure Data Lake Storage 加密](../data-lake-store/data-lake-store-security-overview.md)

- [了解传输中的 Azure 存储帐户加密](../storage/blobs/security-recommendations.md)

**责任**：共享

**Azure 安全中心监视**：无

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5：使用有效的发现工具识别敏感数据

**指导**：数据标识、分类和丢失防护功能尚不适用于 Azure 存储或计算资源。 如果需要出于合规性目的使用这些功能，请实施第三方解决方案。

对于 Microsoft 管理的底层平台，Microsoft 会将所有客户内容视为敏感数据，并会全方位地防范客户数据丢失和遭到透露。 为了确保 Azure 中的客户数据保持安全，Microsoft 实施并维护了一套可靠的数据保护控制措施和功能。

- [了解 Azure 中的客户数据保护](../security/fundamentals/protection-customer-data.md)

**责任**：共享

**Azure 安全中心监视**：无

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6：使用 Azure RBAC 控制对资源的访问 

**指导**：借助 Azure HDInsight 企业安全性套餐 (ESP)，可以使用 Apache Ranger 针对文件、文件夹、数据库、表和行/列中存储的数据创建和管理精细访问控制与数据模糊化策略。 Hadoop 管理员可以使用 Apache Ranger 中的这些插件配置基于角色的访问控制 (RBAC) 来保护 Apache Hive、HBase、Kafka 和 Spark。

使用 Apache Ranger 配置 RBAC 策略可将权限与组织中的角色相关联。 此层抽象可以更轻松地确保用户仅仅有履行工作责任所需的权限。

- [企业安全性套餐在 HDInsight 中 Azure Active Directory (Azure AD) 域服务的配置](domain-joined/apache-domain-joined-configure-using-azure-adds.md)

- [Azure HDInsight 中的企业安全性概述](domain-joined/hdinsight-security-overview.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7：使用基于主机的数据丢失防护来强制实施访问控制

**指导**：对于存储或处理敏感信息的 Azure HDInsight 群集，请使用标记将该群集和相关资源标记为敏感。 数据标识、分类和丢失防护功能尚不适用于 Azure 存储或计算资源。 如果需要出于合规性目的使用这些功能，请实施第三方解决方案。

对于 Microsoft 管理的底层平台，Microsoft 会将所有客户内容视为敏感数据，并会全方位地防范客户数据丢失和遭到透露。 为了确保 Azure 中的客户数据保持安全，Microsoft 实施并维护了一套可靠的数据保护控制措施和功能。

- [了解 Azure 中的客户数据保护](../security/fundamentals/protection-customer-data.md)

**责任**：共享

**Azure 安全中心监视**：无

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8：静态加密敏感信息

**指导**：如果使用 Azure SQL 数据库来存储 Apache Hive 和 Apache Oozie 元数据，请确保 SQL 数据始终保持加密状态。 对于 Azure 存储帐户和 Data Lake Storage（Gen1 或 Gen2），建议允许 Microsoft 管理你的加密密钥，但是，你可以选择管理自己的密钥。

- [如何管理 Azure 存储帐户的加密密钥](/azure/storage/common/storage-encryption-keys-portal)

- [如何使用客户管理的加密密钥创建 Azure Data Lake Storage](../data-lake-store/data-lake-store-get-started-portal.md)

- [了解 Azure SQL 数据库的加密](/azure/sql-database/sql-database-technical-overview#data-encryption)

- [如何使用客户管理的密钥为 SQL 数据库配置透明数据加密](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql?tabs=azure-portal)

**责任**：共享

**Azure 安全中心监视**：无

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9：记录对关键 Azure 资源的更改并对此类更改发出警报

**指导**：为关联到 Azure HDInsight 群集的 Azure 存储帐户配置诊断设置，以监视并记录针对群集数据执行的所有 CRUD 操作。 为关联到 Azure HDInsight 群集的任何存储帐户或 Data Lake Store 启用审核。

- [如何为 Azure 存储帐户启用其他日志记录/审核](/azure/storage/common/storage-monitor-storage-account)

- [如何为 Azure Data Lake Storage 启用其他日志记录/审核](../data-lake-analytics/data-lake-analytics-diagnostic-logs.md)

**责任**：客户

**Azure 安全中心监视**：无

## <a name="vulnerability-management"></a>漏洞管理

[有关详细信息，请参阅 *Azure 安全基线：* 漏洞管理。](../security/benchmarks/security-control-vulnerability-management.md)

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1：运行自动漏洞扫描工具

**指导**：实施第三方漏洞管理解决方案。

（可选）如果你有 Rapid7、Qualys 或任何其他漏洞管理平台订阅，可以使用脚本操作在 Azure HDInsight 群集节点上安装漏洞评估代理，并通过相应的门户管理这些节点。

- [如何手动安装 Rapid7 代理](https://insightvm.help.rapid7.com/docs/install)

- [如何手动安装 Qualys 代理](https://www.qualys.com/docs/qualys-cloud-agent-linux-install-guide.pdf)

- [如何使用脚本操作](hdinsight-hadoop-customize-cluster-linux.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2：部署自动操作系统修补管理解决方案

**指导**：系统已经为群集节点映像启用了自动系统更新，但是，必须定期重新启动群集节点才能确保应用更新。

Microsoft 将维护并更新 Azure HDInsight 的基础节点映像。

- [如何为 HDInsight 群集配置 OS 修补计划](hdinsight-os-patching.md)

**责任**：共享

**Azure 安全中心监视**：无

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5.3：为第三方软件部署自动修补程序管理解决方案

**指导**：使用脚本操作或其他机制修补 Azure HDInsight 群集。 新创建的群集将始终包含最新的可用更新，其中包括最新的安全修补程序。

- [如何为基于 Linux 的 Azure HDInsight 群集配置 OS 修补计划](hdinsight-os-patching.md)

- [如何使用脚本操作](hdinsight-hadoop-customize-cluster-linux.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4：比较连续进行的漏洞扫描

**指导**：实施能够比较一段时间内的漏洞扫描的第三方漏洞管理解决方案。 如果你有 Rapid7 或 Qualys 订阅，可以使用该供应商的门户来查看和比较连续进行的漏洞扫描。

**责任**：客户

**Azure 安全中心监视**：无

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5：使用风险评级过程来确定已发现漏洞的修正措施的优先级

**指南**：使用常见的风险分级程序（例如“常见漏洞评分系统”）或第三方扫描工具提供的默认风险分级功能。

**责任**：客户

**Azure 安全中心监视**：无

## <a name="inventory-and-asset-management"></a>清单和资产管理

[有关详细信息，请参阅 *Azure 安全基线：* 清单和资产管理](../security/benchmarks/security-control-inventory-asset-management.md)。

### <a name="61-use-automated-asset-discovery-solution"></a>6.1：使用自动化资产发现解决方案

**指南**：使用 Azure 资源关系图可以在订阅中查询和发现所有资源 (如计算、存储、网络、端口和协议等 ) ，包括 Azure HDInsight 群集。 确保你在租户中拥有适当的（读取）权限，并且可以枚举所有 Azure 订阅，以及订阅中的资源。

尽管可以通过 Azure Resource Graph 发现经典 Azure 资源，但我们强烈建议今后创建并使用 Azure 资源管理器资源。

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

**指导**：在适用的情况下，请使用标记、管理组和单独的订阅来组织和跟踪资产。 定期核对清单，确保及时地从订阅中删除未经授权的资源。

- [如何创建其他 Azure 订阅](/azure/billing/billing-create-subscription)

- [如何创建管理组](/azure/governance/management-groups/create)

- [如何创建和使用标记](/azure/azure-resource-manager/resource-group-using-tags)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4：定义并维护已批准的 Azure 资源的清单

**指导**：定义已批准的 Azure 资源以及计算资源的已批准软件的列表

**责任**：客户

**Azure 安全中心监视**：无

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5：监视未批准的 Azure 资源

**指导**：在 Azure Policy 中使用以下内置策略定义，对可以在客户订阅中创建的资源类型施加限制：

- 不允许的资源类型

- 允许的资源类型

使用 Azure 资源关系图可查询和发现订阅中的资源。 确保环境中存在的所有 Azure 资源已获得批准。

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [如何使用 Azure Resource Graph 创建查询](../governance/resource-graph/first-query-portal.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6：监视计算资源中未批准的软件应用程序

**指导**：实施第三方解决方案以监视群集节点中未批准的软件应用程序。

**责任**：客户

**Azure 安全中心监视**：无

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7：删除未批准的 Azure 资源和软件应用程序

**指南**：使用 Azure 资源关系图可以在订阅中查询和发现所有资源 (如计算、存储、网络、端口和协议等 ) ，包括 Azure HDInsight 群集。  删除发现的任何未批准 Azure 资源。 对于 Azure HDInsight 群集节点，请实施第三方解决方案来删除未批准的软件或对其发出警报。

- [如何使用 Azure Resource Graph 创建查询](../governance/resource-graph/first-query-portal.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="68-use-only-approved-applications"></a>6.8：仅使用已批准的应用程序

**指导**：对于 Azure HDInsight 群集节点，请实施第三方解决方案，以防止执行未经授权的软件。

**责任**：客户

**Azure 安全中心监视**：无

### <a name="69-use-only-approved-azure-services"></a>6.9：仅使用已批准的 Azure 服务

**指导**：在 Azure Policy 中使用以下内置策略定义，对可以在客户订阅中创建的资源类型施加限制：
- 不允许的资源类型

- 允许的资源类型

有关详细信息，请参阅以下资源：

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [如何使用 Azure Policy 拒绝特定的资源类型](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10：维护已获批软件的清单

**指导**：对于 Azure HDInsight 群集节点，请实施第三方解决方案，以防止执行未经授权的文件类型。

**责任**：客户

**Azure 安全中心监视**：无

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11：限制用户与 Azure 资源管理器进行交互的能力

**指南**：使用 Azure 条件访问可通过为“Microsoft Azure 管理”应用配置“阻止访问”，限制用户与 Azure 资源管理器进行交互的能力。

- [如何配置条件访问以阻止访问 Azure 资源管理器](../role-based-access-control/conditional-access-azure-management.md)

**责任**：客户

**Azure 安全中心监视**：无

## <a name="secure-configuration"></a>安全配置

[有关详细信息，请参阅 *Azure 安全基线：* 安全配置](../security/benchmarks/security-control-secure-configuration.md)。

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1：为所有 Azure 资源建立安全配置

**指导**：在“Microsoft.HDInsight”命名空间中使用 Azure Policy 别名创建自定义策略，以审核或强制实施 HDInsight 群集的网络配置。

- [如何查看可用的 Azure Policy 别名](/powershell/module/az.resources/get-azpolicyalias)

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="72-establish-secure-operating-system-configurations"></a>7.2：建立安全的操作系统配置

**指导**：Microsoft 管理和维护的 Azure HDInsight 操作系统映像。 客户负责为群集节点的操作系统实施安全配置。

**责任**：客户

**Azure 安全中心监视**：无

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3：维护安全的 Azure 资源配置

**指南**：使用 azure 策略 [拒绝] 和 [部署（如果不存在]）来强制执行 Azure HDInsight 群集和相关资源的安全设置。

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [了解 Azure Policy 效果](../governance/policy/concepts/effects.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4：维护安全的操作系统配置

**指导**：Microsoft 管理和维护的 Azure HDInsight 操作系统映像。 客户负责实施 OS 级别的状态配置。

**责任**：共享

**Azure 安全中心监视**：无

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5：安全存储 Azure 资源的配置

**指导**：如果使用自定义的 Azure Policy 定义，请使用 Azure DevOps 或 Azure Repos 安全地存储和管理代码。

- [Azure Repos Git 教程](/azure/devops/repos/git/gitworkflow)

- [Azure Repos 文档](/azure/devops/repos/index)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7：部署 Azure 资源的配置管理工具

**指导**：在“Microsoft.HDInsight”命名空间中使用 Azure Policy 别名创建自定义策略，以审核、强制实施系统配置并对其发出警报。 另外，开发一个用于管理策略例外的流程和管道。

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8：部署操作系统的配置管理工具

**指导**：实施第三方解决方案，以维护群集节点操作系统的所需状态。

**责任**：客户

**Azure 安全中心监视**：无

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9：为 Azure 资源实施自动配置监视

**指南**：使用 "Microsoft hdinsight" 命名空间中的 Azure 策略别名创建自定义策略，以审核或强制执行 HDInsight 群集配置。

- [如何查看可用的 Azure Policy 别名](/powershell/module/az.resources/get-azpolicyalias)

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10：为操作系统实施自动配置监视

**指导**：实施第三方解决方案，以监视群集节点操作系统的状态。

**责任**：客户

**Azure 安全中心监视**：无

### <a name="711-manage-azure-secrets-securely"></a>7.11：安全管理 Azure 机密

**指导**：Azure HDInsight 包括为 Apache Kafka 提供创建自己的密钥 (BYOK) 支持。 借助此功能，你可以拥有和管理用于加密静态数据的密钥。

使用 Azure 存储服务加密 (SSE) 保护 Azure HDInsight 中的所有托管磁盘。 默认情况下，这些磁盘上的数据使用 Microsoft 托管密钥进行加密。 如果启用 BYOK，请提供 Azure HDInsight 要使用的加密密钥，并通过 Azure Key Vault 管理该密钥。

还可将 Key Vault 用于 Azure HDInsight 部署，以管理群集存储（Azure 存储帐户和 Azure Data Lake Storage）的密钥

- [如何在 Azure HDInsight 上为 Apache Kafka 自带密钥](/azure/hdinsight/kafka/apache-kafka-byok)

- [如何管理 Azure 存储帐户的加密密钥](/azure/storage/common/storage-encryption-keys-portal)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="712-manage-identities-securely-and-automatically"></a>7.12：安全自动管理标识

**指南**：可在 Azure HDInsight 中使用托管标识，以便群集能够访问 Azure Active Directory (Azure AD) 域服务、访问 Azure Key Vault 或访问 Azure Data Lake Storage Gen2 中的文件。

- [了解 Azure HDInsight 的托管标识](hdinsight-managed-identities.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13：消除意外的凭据透露

**指导**：如果使用与 Azure HDInsight 部署相关的任何代码，可以实施凭据扫描程序来识别代码中的凭据。 凭据扫描程序还会建议将发现的凭据转移到更安全的位置，例如 Azure Key Vault。 

- [如何设置凭据扫描程序](https://secdevtools.azurewebsites.net/helpcredscan.html)

**责任**：客户

**Azure 安全中心监视**：无

## <a name="malware-defense"></a>恶意软件防护

[有关详细信息，请参阅 *Azure 安全基线：* 恶意软件防护](../security/benchmarks/security-control-malware-defense.md)。

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1：使用集中管理的反恶意软件

**指导**：Azure HDInsight 中预装了 Clamscan，并且已针对群集节点映像启用了 Clamscan，但是，必须管理 Clamscan，并手动聚合/监视该软件生成的任何日志。

- [了解 Azure HDInsight 的 Clamscan](https://docs.microsoft.com/azure/hdinsight/hdinsight-faq#security-and-certificates)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2：预先扫描要上传到非计算 Azure 资源的文件

**指导**：在支持 Azure 服务的底层主机上已启用 Microsoft Antimalware，但是，该软件不会针对客户内容运行。

请预先扫描要上传到与 Azure HDInsight 群集部署相关的 Azure 资源（例如 Data Lake Storage、Blob 存储等）的任何文件。在这种情况下，Microsoft 无法访问客户数据。

- [了解适用于 Azure 云服务和虚拟机的 Microsoft Antimalware](../security/fundamentals/antimalware.md)

**责任**：共享

**Azure 安全中心监视**：无

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>步骤 8.3：确保反恶意软件和签名已更新

**指导**：Azure HDInsight 中预装了 Clamscan，并且为群集节点映像启用了 Clamscan。 Clamscan 将自动执行引擎和定义更新，但是，日志的聚合和管理需要手动执行。

- [了解 Azure Azure HDInsight 的 Clamscan](https://docs.microsoft.com/azure/hdinsight/hdinsight-faq#security-and-certificates)

**责任**：客户

**Azure 安全中心监视**：无

## <a name="data-recovery"></a>数据恢复

[有关详细信息，请参阅 *Azure 安全基线：* 数据恢复](../security/benchmarks/security-control-data-recovery.md)。

### <a name="91-ensure-regular-automated-back-ups"></a>9.1：确保定期执行自动备份

**指南**：将 Azure 存储帐户用于 HDInsight 群集数据存储时，请选择相应的冗余选项 (LRS、ZRS、GRS、RA-GRS) 。  将 Azure SQL 数据库用于 Azure HDInsight 群集数据存储时，请配置活动异地复制。

- [如何为 Azure 存储帐户配置存储冗余](../storage/common/storage-redundancy.md)

- [如何配置 Azure SQL 数据库的冗余](/azure/sql-database/sql-database-active-geo-replication)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2：执行完整系统备份，并备份客户管理的所有密钥

**指南**：使用 azure HDInsight 群集数据存储的 Azure 存储帐户时，请选择相应的冗余选项 (LRS、ZRS、GRS、RA-GRS) 。 如果在任何 Azure HDInsight 部署环节中使用 Azure Key Vault，请确保备份密钥。

- [选择 Azure HDInsight 群集的存储选项](hdinsight-hadoop-compare-storage-options.md)

- [如何为 Azure 存储帐户配置存储冗余](../storage/common/storage-redundancy.md)

- [如何在 Azure 中备份 Key Vault 密钥](/powershell/module/az.keyvault/backup-azkeyvaultkey)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3：验证所有备份，包括客户管理的密钥

**指南**：如果 Azure Key Vault 与 Azure HDInsight 部署一起使用，请测试已备份客户托管密钥的还原。

- [如何在 Azure HDInsight 上为 Apache Kafka 自带密钥](/azure/hdinsight/kafka/apache-kafka-byok)

- [如何在 Azure 中还原密钥保管库密钥](/powershell/module/az.keyvault/restore-azkeyvaultkey)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4：确保保护备份和客户管理的密钥

**指南**：如果 Azure Key Vault 与 Azure HDInsight 部署一起使用，请在 Key Vault 中启用软删除，以防止意外或恶意删除密钥。

- [如何启用软删除 Azure Key Vault](/azure/key-vault/key-vault-ovw-soft-delete)

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

**指导**：安全中心将为警报分配严重性来帮助你确定每条警报的处理优先顺序，以便在资源泄密时可以立即采取措施。 严重性取决于安全中心对调查结果或用于发出警报的分析的确信程度，以及对导致警报的活动背后存在恶意意图的确信程度。

**责任**：客户

**Azure 安全中心监视**：无

### <a name="103-test-security-response-procedures"></a>10.3：测试安全响应过程

**指导**：定期执行演练来测试系统的事件响应功能。 识别弱点和差距，并根据需要修改计划。

- [请参阅 NIST 的刊物：Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)（IT 规划和功能的测试、培训与演练计划指南）

**责任**：客户

**Azure 安全中心监视**：无

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4：提供安全事件联系人详细信息，并针对安全事件配置警报通知

**指导**：如果 Microsoft 安全响应中心 (MSRC) 发现数据被某方非法访问或未经授权访问，Microsoft 会使用安全事件联系信息联系用户。

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
