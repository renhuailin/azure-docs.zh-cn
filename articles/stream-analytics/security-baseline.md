---
title: 适用于流分析的 Azure 安全基线
description: 流分析安全基线为实现 Azure 安全基准中指定的安全建议提供过程指导和资源。
author: msmbaldwin
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/08/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 88e27b839e3f8baf9a1df8a6a6d936aecd602b23
ms.sourcegitcommit: c6a2d9a44a5a2c13abddab932d16c295a7207d6a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/09/2021
ms.locfileid: "107285101"
---
# <a name="azure-security-baseline-for-stream-analytics"></a>适用于流分析的 Azure 安全基线

此安全基线将 [Azure 安全基准版本 1.0](../security/benchmarks/overview-v1.md) 中的指导应用于流分析。 Azure 安全基准提供有关如何在 Azure 上保护云解决方案的建议。 内容按“安全控制”分组，这些控制根据适用于流分析的 Azure 安全基准和相关指导定义。 

> [!NOTE]
> 排除了不适用于流分析或 Microsoft 为其责任方的控件。 若要查看流分析如何完全映射到 Azure 安全基准，请参阅[完整的流分析安全基线映射文件](https://github.com/MicrosoftDocs/SecurityBenchmarks/raw/master/Azure%20Offer%20Security%20Baselines/1.1/stream-analytics-security-baseline-v1.1.xlsx)。

## <a name="network-security"></a>网络安全

[有关详细信息，请参阅 *Azure 安全基线：* 网络安全性](../security/benchmarks/security-control-network-security.md)。

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1：保护虚拟网络中的 Azure 资源

**指导**：Azure 流分析不支持使用网络安全组 (NSG) 和 Azure 防火墙。

**责任**：客户

Azure 安全中心监视：无

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2：监视并记录虚拟网络、子网和网络接口的配置与流量

**指导**：Azure 流分析不支持使用虚拟网络和子网。

**责任**：客户

Azure 安全中心监视：无

### <a name="13-protect-critical-web-applications"></a>1.3：保护关键 Web 应用程序 

**指导**：不适用；此建议适用于 Azure 应用服务或计算资源上运行的 Web 应用程序。

**责任**：客户

Azure 安全中心监视：无

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4：拒绝与已知恶意的 IP 地址进行通信

**指导**：使用 Azure 安全中心威胁防护检测和警报已知恶意的或未使用过的 Internet IP 地址通信。

- [Azure 安全中心内 Azure 服务层的威胁防护](../security-center/azure-defender.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="15-record-network-packets"></a>1.5：记录网络数据包

**指导**：Azure 流分析不使用网络安全组，也不会捕获 Azure Key Vault 的流日志。

**责任**：客户

Azure 安全中心监视：无

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6：部署基于网络的入侵检测/入侵防护系统 (IDS/IPS)

**指导**：使用 Azure 安全中心威胁防护检测 Azure 订阅环境中的异常或潜在有害操作。

- [Azure 安全中心内 Azure 服务层的威胁防护](../security-center/azure-defender.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="17-manage-traffic-to-web-applications"></a>1.7：管理发往 Web 应用程序的流量

**指导**：不适用；此建议适用于 Azure 应用服务或计算资源上运行的 Web 应用程序。

**责任**：客户

Azure 安全中心监视：无

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8：最大程度地降低网络安全规则的复杂性和管理开销

**指导**：Azure 流分析不支持使用虚拟网络和网络规则。

**责任**：客户

Azure 安全中心监视：无

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9：维护网络设备的标准安全配置 

**指导**：Azure 流分析不支持使用虚拟网络和网络设备。

**责任**：客户

Azure 安全中心监视：无

### <a name="110-document-traffic-configuration-rules"></a>1.10：阐述流量配置规则 

**指导**：Azure 流分析不支持使用虚拟网络和流量配置规则。

**责任**：客户

Azure 安全中心监视：无

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11：使用自动化工具来监视网络资源配置和检测更改 

**指导**：使用 Azure 活动日志监视网络资源配置，并检测流分析资源的变化。 在 Azure Monitor 中创建当关键资源发生更改时触发的警报。

- [如何查看和检索 Azure 活动日志事件](https://docs.microsoft.com/azure/azure-monitor/essentials/activity-log#view-the-activity-log)

- [如何在 Azure Monitor 中创建警报](../azure-monitor/alerts/alerts-activity-log.md)

**责任**：客户

Azure 安全中心监视：无

## <a name="logging-and-monitoring"></a>日志记录和监视

[有关详细信息，请参阅 *Azure 安全基线：* 日志记录和监视](../security/benchmarks/security-control-logging-monitoring.md)。

### <a name="22-configure-central-security-log-management"></a>2.2：配置中心安全日志管理 

**指导**：通过 Azure Monitor 引入日志来聚合安全数据，如审核事件和请求。 在 Azure Monitor 中，使用 Log Analytics 工作区查询和执行分析，并使用 Azure 存储帐户进行长期/存档存储，可以选择使用不可变存储和强制保留等安全功能。 

- [如何使用 Azure Monitor 收集平台日志和指标](../azure-monitor/essentials/diagnostic-settings.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：为 Azure 资源启用审核日志记录

**指导**：在 Azure 流分析上启用诊断设置，以访问管理、安全性和诊断日志。 还可以启用 Azure 活动日志诊断设置，并将日志发送到同一 Log Analytics 工作区或存储帐户。

- [Azure 流分析提供诊断日志和活动数据供审查](stream-analytics-job-diagnostic-logs.md)

**责任**：客户

**Azure 安全中心监视**：[Azure 安全基准](/azure/governance/policy/samples/azure-security-benchmark)是安全中心的默认策略计划，是[安全中心建议](/azure/security-center/security-center-recommendations)的基础。 安全中心会自动启用与此控件相关的 Azure Policy 定义。 与此控件相关的警报可能需要相关服务的 [Azure Defender](/azure/security-center/azure-defender) 计划。

Azure Policy 内置定义 - Microsoft.StreamAnalytics：

[!INCLUDE [Resource Policy for Microsoft.StreamAnalytics 2.3](../../includes/policy/standards/asb/rp-controls/microsoft.streamanalytics-2-3.md)]

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4：从操作系统收集安全日志

**指导**：不适用；此建议适用于计算资源。

**责任**：客户

Azure 安全中心监视：无

### <a name="25-configure-security-log-storage-retention"></a>2.5：配置安全日志存储保留期 

**指导**：在 Azure 存储帐户或 Log Analytics 工作区中存储安全事件日志时，可以根据组织的要求设置保留策略。 

- [Azure 流分析提供诊断日志和活动数据供审查](stream-analytics-job-diagnostic-logs.md)

- [如何为 Azure 存储帐户日志配置保留策略](https://docs.microsoft.com/azure/storage/common/manage-storage-analytics-logs#configure-logging) 

- [更改 Log Analytics 中的数据保留期](https://docs.microsoft.com/azure/azure-monitor/logs/manage-cost-storage#change-the-data-retention-period)

**责任**：客户

Azure 安全中心监视：无

### <a name="26-monitor-and-review-logs"></a>2.6：监视和查看日志

**指导**：分析和监视日志中的异常行为，并定期审查流分析资源的结果。 使用 Azure Monitor 的 Log Analytics 工作区查看日志并对日志数据执行查询。 或者，可以启用将数据加入 Azure Sentinel 或第三方 SIEM 的功能。 

- [如何加入 Azure Sentinel](../sentinel/quickstart-onboard.md) 

- [有关 Log Analytics 工作区的详细信息](../azure-monitor/logs/log-analytics-tutorial.md)

- [如何在 Azure Monitor 中执行自定义查询](../azure-monitor/logs/get-started-queries.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7：针对异常活动启用警报

**指导**：启用流分析的诊断设置，并将日志发送到 Log Analytics 工作区。 将 Log Analytics 工作区加入 Azure Sentinel，因为它提供了安全业务流程自动化响应 (SOAR) 解决方案。 这样便可以创建 playbook（自动化解决方案）并将其用于修正安全问题。 

- [如何加入 Azure Sentinel](../sentinel/quickstart-onboard.md) 

- [如何针对 Log Analytics 日志数据发出警报](../azure-monitor/alerts/tutorial-response.md)  

- [Azure 流分析提供诊断日志和活动数据供审查](stream-analytics-job-diagnostic-logs.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="28-centralize-anti-malware-logging"></a>2.8：集中管理反恶意软件日志记录 

**指导**：不适用；流分析不会处理或生成与反恶意软件相关的日志。

**责任**：客户

Azure 安全中心监视：无

### <a name="29-enable-dns-query-logging"></a>2.9：启用 DNS 查询日志记录 

**指导**：Azure Monitor 中的 Azure DNS Analytics（预览）解决方案收集有关 DNS 基础结构安全性、性能和操作的见解。 目前，该解决方案不支持 Azure 流分析，但你可以使用第三方 DNS 日志记录解决方案。 

- [使用 DNS Analytics 预览解决方案收集有关 DNS 基础结构的见解](../azure-monitor/insights/dns-analytics.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="210-enable-command-line-audit-logging"></a>2.10：启用命令行审核日志记录 

**指导**：不适用；此建议适用于计算资源。

**责任**：客户

Azure 安全中心监视：无

## <a name="identity-and-access-control"></a>标识和访问控制

[有关详细信息，请参阅 *Azure 安全基线：* 标识和访问控制](../security/benchmarks/security-control-identity-access-control.md)。

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1：维护管理帐户的清单 

指导：Azure Active Directory (Azure AD) 具有内置角色，这些角色必须显式分配。 可以查询角色以了解成员资格。 使用 Azure AD PowerShell 模块执行即席查询，以发现属于管理组成员的帐户。

- [如何使用 PowerShell 获取 Azure AD 中的目录角色](/powershell/module/azuread/get-azureaddirectoryrole)

- [如何使用 PowerShell 获取 Azure AD 中目录角色的成员](/powershell/module/azuread/get-azureaddirectoryrolemember)

**责任**：客户

Azure 安全中心监视：无

### <a name="32-change-default-passwords-where-applicable"></a>3.2：在适用的情况下更改默认密码 

指导：流分析没有默认密码的概念，因为身份验证随 Azure Active Directory (Azure AD) 提供，并且由用于管理服务的 Azure 基于角色的访问控制 (Azure RBAC) 进行保护。 根据注入流服务和输出服务，你需要轮换在作业中配置的凭据。

- [轮转流分析作业的输入和输出的登录凭据](stream-analytics-login-credentials-inputs-outputs.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="33-use-dedicated-administrative-accounts"></a>3.3：使用专用管理帐户 

**指导**：创建标识管理和角色安全性计划，遵循最佳做法，包括管理员角色的最小特权访问原则。 使用 Azure Privileged Identity Management (PIM) 提供对 Azure Active Directory (Azure AD) 和 Azure 资源的实时特权访问权限。 使用 Azure PIM 警报和审核历史记录监视管理帐户的活动。 使用 Azure AD 安全报告帮助标识可能已泄露的管理帐户。

- [了解详细信息](/azure/active-directory/privileged-identity-management/)

**责任**：客户

Azure 安全中心监视：无

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4：使用 Azure Active Directory 单一登录 (SSO)

指导：请尽可能使用 Azure Active Directory (Azure AD) SSO，而不是为每个服务配置独立凭据。 实现 Azure 安全中心标识 &amp; 访问建议。

- [了解 Azure AD 的 SSO](../active-directory/manage-apps/what-is-single-sign-on.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5：对所有基于 Azure Active Directory 的访问使用多重身份验证

指导：启用 Azure Active Directory (Azure AD) 多重身份验证，并遵循 Azure 安全中心标识和访问管理建议，以帮助保护流分析资源。

- [如何在 Azure 中启用多重身份验证](../active-directory/authentication/howto-mfa-getstarted.md)

- [如何在 Azure 安全中心监视标识和访问](../security-center/security-center-identity-access.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3.6：使用由 Azure 管理的安全工作站执行管理任务

指导：使用配置了多重身份验证的 PAW（特权访问工作站）登录和配置流分析资源。

- [了解特权访问工作站](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [如何在 Azure 中启用多重身份验证](../active-directory/authentication/howto-mfa-getstarted.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7：记录来自管理帐户的可疑活动并对其发出警报

**指导**：当环境中出现可疑或不安全的活动时，请使用 Azure Active Directory (Azure AD) 安全报告来生成日志和警报。 使用 Azure 安全中心监视标识和访问活动。

- [如何确定标记为存在风险活动的 Azure AD 用户](../active-directory/identity-protection/overview-identity-protection.md)

- [如何在 Azure 安全中心内监视用户的标识和访问活动](../security-center/security-center-identity-access.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8：仅从批准的位置管理 Azure 资源

**指导**：使用条件访问命名位置，仅允许从 IP 地址范围或国家/地区的特定逻辑分组进行访问。 

- [如何在 Azure 中配置命名位置](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="39-use-azure-active-directory"></a>3.9：使用 Azure Active Directory

**指导**：使用 Azure Active Directory (Azure AD) 作为中心身份验证和授权系统。 Azure AD 提供基于角色的访问控制 (Azure RBAC)，用于精细地控制客户端对流分析资源的访问权限。

- [如何创建和配置 Azure AD 实例](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10：定期审查和协调用户访问

指导：检查 Azure Active Directory (Azure AD) 日志，以帮助发现可能包含具有存储帐户管理角色的陈旧帐户。 此外，请使用 Azure 标识访问评审来有效管理组成员身份、对企业应用程序的访问和角色分配。 应定期评审用户的访问权限，确保只有适当的用户才持续拥有访问权限。

- [了解 Azure AD 报告](/azure/active-directory/reports-monitoring/)

- [如何使用 Azure 标识访问评审](../active-directory/governance/access-reviews-overview.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11：监视尝试访问已停用凭据的行为

指导：为 Azure 流分析和 Azure Active Directory (Azure AD) 启用诊断设置，将所有日志都发送到 Log Analytics 工作区。 在 Log Analytics 中配置所需警报（例如尝试访问禁用的机密）。

- [将 Azure AD 日志与 Azure Monitor 日志集成](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**责任**：客户

Azure 安全中心监视：无

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12：针对帐户登录行为偏差发出警报

指导：使用 Azure Active Directory (Azure AD) 的风险和标识保护功能，对检测到的与流分析资源相关的可疑操作配置自动响应。 应通过 Azure Sentinel 启用自动响应，以实现组织的安全响应。

- [如何查看 Azure AD 风险登录](../active-directory/identity-protection/overview-identity-protection.md)

- [如何配置和启用标识保护风险策略](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [如何加入 Azure Sentinel](../sentinel/quickstart-onboard.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13：在支持场合下为 Microsoft 提供对相关客户数据的访问权限

**指导**：不适用；Azure 流分析不支持客户密码箱。

- [正式版中支持的服务和方案](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability)

**责任**：客户

Azure 安全中心监视：无

## <a name="data-protection"></a>数据保护

[有关详细信息，请参阅 *Azure 安全基线：* 数据保护](../security/benchmarks/security-control-data-protection.md)。

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1：维护敏感信息的清单

**指导**：使用标记可以帮助跟踪存储或处理敏感信息的流分析资源。 

- [如何创建和使用标记](../azure-resource-manager/management/tag-resources.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2：隔离存储或处理敏感信息的系统

**指导**：通过将输入、输出和存储帐户放在同一订阅中来隔离流分析作业。 你可以限制流分析，以控制对应用程序和企业环境所需流分析资源的访问级别。 可以通过 Azure Active Directory (Azure AD) RBAC 控制对 Azure 流分析的访问。

- [如何创建其他 Azure 订阅](../cost-management-billing/manage/create-subscription.md)

- [如何创建管理组](../governance/management-groups/create-management-group-portal.md)

- [如何创建和使用标记](../azure-resource-manager/management/tag-resources.md)

- [理解 Azure 流分析的输入](stream-analytics-add-inputs.md)

- [了解 Azure 流分析的输出](stream-analytics-define-outputs.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3：监视和阻止未经授权的敏感信息传输 

**指导**：数据丢失防护功能尚不可用于 Azure 流分析资源。 如果需要出于合规性目的使用这些功能，请实施第三方解决方案。

对于 Microsoft 管理的基础平台，Microsoft 会将所有客户内容视为敏感数据，并全方位防范客户数据丢失和泄露。 为了确保 Azure 中的客户数据保持安全，Microsoft 已实施并维护一套可靠的数据保护控制机制和功能。

- [了解 Azure 中的客户数据保护](../security/fundamentals/protection-customer-data.md)

- [如何保护 Azure 存储帐户](../storage/blobs/security-recommendations.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5：使用有效的发现工具识别敏感数据

**指导**：数据标识功能尚不可用于 Azure 流分析资源。 如果需要出于合规性目的使用这些功能，请实施第三方解决方案。 

- [了解 Azure 中的客户数据保护](../security/fundamentals/protection-customer-data.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6：使用基于角色的访问控制来控制对资源的访问

**指导**：使用 Azure 基于角色的访问控制 (Azure RBAC) 来控制用户与服务交互的方式。

- [如何配置 Azure RBAC](../role-based-access-control/role-assignments-portal.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7：使用基于主机的数据丢失防护来强制实施访问控制

**指导**：不适用；此建议适用于计算资源。

**责任**：客户

Azure 安全中心监视：无

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8：静态加密敏感信息

**指导**：流分析不存储传入数据，因为所有处理都在内存中完成。  任何私有数据（包括流分析需要保留的查询和功能）都存储在配置的存储帐户中。  使用客户管理的密钥 (CMK) 对存储帐户中的输出静态数据进行加密。 即使没有 CMK，流分析也会自动在其基础架构中采用一流的加密标准来加密和保护数据。

- [Azure 流分析中的数据保护](data-protection.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9：记录对关键 Azure 资源的更改并对此类更改发出警报

**指导**：将 Azure Monitor 与 Azure 活动日志结合使用，以创建在 Azure 流分析资源生产实例发生更改时发出的警报。

- [如何针对 Azure 活动日志事件创建警报](../azure-monitor/alerts/alerts-activity-log.md)

**责任**：客户

Azure 安全中心监视：无

## <a name="vulnerability-management"></a>漏洞管理

[有关详细信息，请参阅 *Azure 安全基线：* 漏洞管理。](../security/benchmarks/security-control-vulnerability-management.md)

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1：运行自动漏洞扫描工具

**指导**：请遵循 Azure 安全中心关于保护 Azure 流分析资源的建议。

Microsoft 对支持 Azure 流分析的基础系统执行漏洞管理。

- [了解 Azure 安全中心建议](../security-center/recommendations-reference.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2：部署自动操作系统修补管理解决方案

**指导**：不适用；此建议适用于计算资源。

**责任**：客户

Azure 安全中心监视：无

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5.3：为第三方软件部署自动修补程序管理解决方案

**指导**：不适用；此建议适用于计算资源。

**责任**：客户

Azure 安全中心监视：无

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4：比较连续进行的漏洞扫描

**指导**：不适用；此建议适用于计算资源。

**责任**：客户

Azure 安全中心监视：无

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5：使用风险评级过程来确定已发现漏洞的修正措施的优先级

**指导**：使用 Azure 安全中心提供的默认风险评级（安全功能分数）。 

- [了解 Azure 安全中心安全功能分数](../security-center/secure-score-security-controls.md)

**责任**：客户

Azure 安全中心监视：无

## <a name="inventory-and-asset-management"></a>清单和资产管理

[有关详细信息，请参阅 *Azure 安全基线：* 清单和资产管理](../security/benchmarks/security-control-inventory-asset-management.md)。

### <a name="61-use-automated-asset-discovery-solution"></a>6.1：使用自动化资产发现解决方案

**指导**：使用 Azure Resource Graph 查询/发现订阅中的所有资源（例如计算、存储、网络、端口和协议等）。 确保租户中具有适当的（读取）权限，并枚举所有 Azure 订阅以及订阅中的资源。

尽管可以通过 Resource Graph 发现经典 Azure 资源，但我们强烈建议你今后还是创建并使用 Azure 资源管理器资源。

- [如何使用 Azure Resource Graph 创建查询](../governance/resource-graph/first-query-portal.md)

- [如何查看 Azure 订阅](/powershell/module/az.accounts/get-azsubscription)

- [了解 Azure RBAC](../role-based-access-control/overview.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="62-maintain-asset-metadata"></a>6.2：维护资产元数据

**指导**：将标记应用到 Azure资源，以便有条理地将元数据组织成某种分类。

- [如何创建和使用标记](../azure-resource-manager/management/tag-resources.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="63-delete-unauthorized-azure-resources"></a>6.3：删除未经授权的 Azure 资源

**指导**：在适用的情况下，请使用标记、管理组和单独的订阅来组织和跟踪 Azure 流分析资产。 定期核对清单，确保及时地从订阅中删除未经授权的资源。

此外，在 Azure Policy 中使用以下内置策略定义，对可以在客户订阅中创建的资源类型施加限制：

- 不允许的资源类型

- 允许的资源类型

- [如何创建其他 Azure 订阅](../cost-management-billing/manage/create-subscription.md)

- [如何创建管理组](../governance/management-groups/create-management-group-portal.md)

- [如何创建和使用标记](../azure-resource-manager/management/tag-resources.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4：定义并维护已批准的 Azure 资源的清单

**指南**：不适用；此建议适用于计算资源和整个 Azure。

**责任**：客户

Azure 安全中心监视：无

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5：监视未批准的 Azure 资源

**指南**：在 Azure Policy 中使用以下内置策略定义，对可以在客户订阅中创建的资源类型施加限制：

- 不允许的资源类型

- 允许的资源类型

此外，请使用 Azure Resource Graph 来查询/发现订阅中的资源。

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [如何使用 Azure Graph 创建查询](../governance/resource-graph/first-query-portal.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6：监视计算资源中未批准的软件应用程序

**指导**：不适用；此建议适用于计算资源。

**责任**：客户

Azure 安全中心监视：无

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7：删除未批准的 Azure 资源和软件应用程序

**指南**：不适用；此建议适用于计算资源和整个 Azure。

**责任**：客户

Azure 安全中心监视：无

### <a name="68-use-only-approved-applications"></a>6.8：仅使用已批准的应用程序

**指导**：不适用；此建议适用于计算资源。

**责任**：客户

Azure 安全中心监视：无

### <a name="69-use-only-approved-azure-services"></a>6.9：仅使用已批准的 Azure 服务

**指南**：在 Azure Policy 中使用以下内置策略定义，对可以在客户订阅中创建的资源类型施加限制：
- 不允许的资源类型
- 允许的资源类型

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [如何使用 Azure Policy 拒绝特定的资源类型](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**责任**：客户

Azure 安全中心监视：无

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10：维护已获批软件的清单

**指导**：不适用；此建议适用于计算资源。

**责任**：客户

Azure 安全中心监视：无

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11：限制用户与 Azure 资源管理器进行交互的能力

**指南**：配置 Azure 条件访问，使其通过为“Microsoft Azure 管理”应用配置“阻止访问”，来限制用户与 Azure 资源管理器进行交互的能力。

- [如何配置条件访问以阻止访问 Azure 资源管理器](../role-based-access-control/conditional-access-azure-management.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12：限制用户在计算资源中执行脚本的功能

**指导**：不适用；此建议适用于计算资源。

**责任**：客户

Azure 安全中心监视：无

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13：以物理或逻辑方式隔离高风险应用程序

**指导**：不适用；此建议适用于 Azure 应用服务或计算资源上运行的 Web 应用程序。

**责任**：客户

Azure 安全中心监视：无

## <a name="secure-configuration"></a>安全配置

[有关详细信息，请参阅 *Azure 安全基线：* 安全配置](../security/benchmarks/security-control-secure-configuration.md)。

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1：为所有 Azure 资源建立安全配置

**指导**：使用“Microsoft.StreamAnalytics”命名空间中的 Azure Policy 别名创建自定义策略，以审核或强制实施 Azure 流分析的配置。 还可以使用与 Azure 流分析相关的内置策略定义，例如：

-应启用 Azure 流分析的诊断日志

- [如何查看可用的 Azure Policy 别名](/powershell/module/az.resources/get-azpolicyalias)

- [Azure Policy 内置策略定义](../governance/policy/samples/built-in-policies.md)

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="72-establish-secure-operating-system-configurations"></a>7.2：建立安全的操作系统配置

**指导**：不适用；此建议适用于计算资源。

**责任**：客户

Azure 安全中心监视：无

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3：维护安全的 Azure 资源配置

**指南**：使用 Azure Policy“[拒绝]”和“[不存在则部署]”对不同的 Azure 资源强制实施安全设置。

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [了解 Azure Policy 效果](../governance/policy/concepts/effects.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4：维护安全的操作系统配置

**指导**：不适用；此项指导适用于计算资源。

**责任**：客户

Azure 安全中心监视：无

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5：安全存储 Azure 资源的配置

指导：使用 Azure Repos 安全地存储和管理代码，包括自定义 Azure 策略、Azure 资源管理器模板、Desired State Configuration 脚本、用户定义的函数、查询。 若要访问在 Azure DevOps 中管理的资源，可以向特定用户、内置安全组或 Azure Active Directory (Azure AD)（如果与 Azure DevOps 集成）中定义的组或 Azure AD（如果与 TFS 集成）授予或拒绝授予权限。

- [如何在 Azure DevOps 中存储代码](/azure/devops/repos/git/gitworkflow)

- [关于 Azure DevOps 中的权限和组](/azure/devops/organizations/security/about-permissions)

**责任**：客户

Azure 安全中心监视：无

### <a name="76-securely-store-custom-operating-system-images"></a>7.6：安全存储自定义操作系统映像

**指导**：不适用；此建议适用于计算资源。

**责任**：客户

Azure 安全中心监视：无

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7：部署 Azure 资源的配置管理工具

**指导**：使用“Microsoft.StreamAnalytics”命名空间中的 Azure Policy 别名创建自定义策略，以审核、强制实施系统配置并为其发出警报。 另外，开发一个用于管理策略例外的流程和管道。

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8：部署操作系统的配置管理工具

**指导**：不适用；此建议适用于计算资源。

**责任**：客户

Azure 安全中心监视：无

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9：为 Azure 资源实施自动配置监视

**指导**：使用“Microsoft.StreamAnalytics”命名空间中的 Azure Policy 别名创建自定义策略，以审核、强制实施系统配置并为其发出警报。 使用 Azure Policy“[审核]”、“[拒绝]”和“[不存在则部署]”自动强制实施 Azure 流分析资源的配置。

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10：为操作系统实施自动配置监视

**指导**：不适用；此建议适用于计算资源。

**责任**：客户

Azure 安全中心监视：无

### <a name="711-manage-azure-secrets-securely"></a>7.11：安全管理 Azure 机密

**指导**：流分析作业使用的输入或输出资源的连接详细信息存储在配置的存储帐户中。 加密存储帐户以保护你的所有数据。  此外，定期轮换流分析作业的输入或输出凭据。

- [Azure 流分析中的数据保护](data-protection.md)

- [轮转流分析作业的输入和输出的登录凭据](stream-analytics-login-credentials-inputs-outputs.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="712-manage-identities-securely-and-automatically"></a>7.12：安全自动管理标识

**指导**：用于输出的托管标识身份验证允许流分析作业直接访问服务，包括 Power BI、存储帐户，而不是使用连接字符串。

- [使用托管标识在 Azure Data Lake Storage Gen1 中对流分析进行身份验证](stream-analytics-managed-identities-adls.md)

- [使用托管标识在 Azure Blob 存储输出中对 Azure 流分析作业进行身份验证](blob-output-managed-identity.md)

- [使用托管标识在 Power BI 中对 Azure 流分析作业进行身份验证](powerbi-output-managed-identity.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13：消除意外的凭据透露

**指南**：实施凭据扫描程序来识别代码中的凭据。 凭据扫描程序还会建议将发现的凭据转移到更安全的位置，例如 Azure Key Vault。

- [如何设置凭据扫描程序](https://secdevtools.azurewebsites.net/helpcredscan.html)

**责任**：客户

Azure 安全中心监视：无

## <a name="malware-defense"></a>恶意软件防护

[有关详细信息，请参阅 *Azure 安全基线：* 恶意软件防护](../security/benchmarks/security-control-malware-defense.md)。

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1：使用集中管理的反恶意软件

**指南**：不适用；此建议适用于计算资源。

**责任**：客户

Azure 安全中心监视：无

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2：预先扫描要上传到非计算 Azure 资源的文件

**指导**：在支持 Azure 服务（例如 Azure 流分析）的底层主机上已启用 Microsoft Antimalware，但是，该软件不会针对客户内容运行。

预扫描上传到 Azure 资源的任何内容，如应用服务、流分析、Blob 存储等。Microsoft 无法访问这些实例中的数据。

**责任**：客户

Azure 安全中心监视：无

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>步骤 8.3：确保反恶意软件和签名已更新

**指南**：不适用；此建议适用于计算资源。

**责任**：客户

Azure 安全中心监视：无

## <a name="data-recovery"></a>数据恢复

[有关详细信息，请参阅 *Azure 安全基线：* 数据恢复](../security/benchmarks/security-control-data-recovery.md)。

### <a name="91-ensure-regular-automated-back-ups"></a>9.1：确保定期执行自动备份

**指导**：基于所选的输出服务的类型。 可以根据输出服务的建议指南对输出数据执行自动备份。  内部数据（包括用户定义的函数、查询、数据快照）存储在配置的存储帐户中，你可以定期备份该帐户。

系统始终会自动复制 Microsoft Azure 存储帐户中的数据，以确保持久性和高可用性。 Azure 存储功能会复制数据，以防范各种计划内和计划外的事件，包括暂时性的硬件故障、网络中断或断电、大范围自然灾害等。 可以选择在同一数据中心中、跨同一区域中的局域数据中心或跨地理上隔离的区域复制数据。 

还可以使用生命周期管理功能将数据备份到存档层。  此外，为存储在存储帐户中的备份启用软删除。

- [Azure 流分析中的数据保护](https://docs.microsoft.com/azure/stream-analytics/data-protection#private-data-assets-that-are-stored)

- [了解 Azure 存储冗余和服务级别协议](../storage/common/storage-redundancy.md)

- [管理 Azure Blob 存储生命周期](../storage/blobs/storage-lifecycle-management-concepts.md)

- [Azure 存储 Blob 的软删除](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

**责任**：客户

Azure 安全中心监视：无

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2：执行完整系统备份，并备份客户管理的所有密钥

指导：内部数据（包括用户定义的函数、查询、数据快照）存储在配置的存储帐户中，你可以定期备份该帐户。

从存储帐户支持的服务备份数据，有多种可用方法，包括使用 azcopy 或第三方工具。 Azure Blob 存储的不可变存储可让用户以 WORM（一次写入，多次读取）状态存储业务关键型数据对象。 此状态可以根据用户指定的时间间隔使数据保持不可擦除且不可修改的状态。

- [Azure 流分析中的数据保护](https://docs.microsoft.com/azure/stream-analytics/data-protection#private-data-assets-that-are-stored)

- [AzCopy 入门](../storage/common/storage-use-azcopy-v10.md)

- [为 Blob 存储设置和管理不可变性策略](../storage/blobs/storage-blob-immutability-policies-manage.md)

可以使用 Azure CLI 或 PowerShell 在 Azure Key Vault 中备份客户管理/提供的密钥。

- [如何在 Azure 中备份密钥保管库密钥](/powershell/module/az.keyvault/backup-azkeyvaultkey)

**责任**：客户

Azure 安全中心监视：无

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3：验证所有备份，包括客户管理的密钥

**指导**：定期对备份数据执行数据恢复，以测试数据的完整性。

**责任**：客户

Azure 安全中心监视：无

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4：确保保护备份和客户管理的密钥

指导：默认情况下，存储在 Azure 存储中的流分析备份支持加密，并且无法关闭。  你应将备份视为敏感数据，并应用相关的访问和数据保护控制作为此基线的一部分。  

- [Azure 流分析中的数据保护](https://docs.microsoft.com/azure/stream-analytics/data-protection#private-data-assets-that-are-stored)

- [授予访问 Azure 存储中的数据的权限](../storage/common/storage-auth.md)

**责任**：客户

Azure 安全中心监视：无

## <a name="incident-response"></a>事件响应

[有关详细信息，请参阅 *Azure 安全基线：* 事件响应](../security/benchmarks/security-control-incident-response.md)。

### <a name="101-create-an-incident-response-guide"></a>10.1：创建事件响应指导

**指南**：为组织制定事件响应指南。 确保在书面的事件响应计划中定义人员职责，以及事件处理/管理从检测到事件后审查的各个阶段。

- [关于建立自己的安全事件响应流程的指南](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft 安全响应中心事件分析](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [客户还可以利用 NIST 的“计算机安全事件处理指南”来制定他们自己的事件响应计划](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**责任**：客户

Azure 安全中心监视：无

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2：创建事件评分和优先级设定过程

**指南**：安全中心向每个警报分配一个严重性，帮助你优先处理应首先调查的警报。 严重性取决于安全中心对调查结果或用于发出警报的指标的置信度，以及对导致警报的活动背后存在恶意意图的置信度级别。 

此外，请明确标记订阅（例如 生产、非生产）并创建命名系统来对 Azure 资源进行明确标识和分类，特别是处理敏感数据的资源。  你的责任是根据发生事件的 Azure 资源和环境的关键性确定修正警报的优先级。

- [Azure 安全中心中的安全警报](../security-center/security-center-alerts-overview.md)

- [使用标记整理 Azure 资源](../azure-resource-manager/management/tag-resources.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="103-test-security-response-procedures"></a>10.3：测试安全响应过程

**指导**：定期执行演练来测试系统的事件响应功能，以帮助保护 Azure 资源。 识别弱点和差距，并根据需要修改计划。

- [请参阅 NIST 的刊物：Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)（IT 规划和功能的测试、培训与演练计划指南）

**责任**：客户

Azure 安全中心监视：无

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4：提供安全事件联系人详细信息，并针对安全事件配置警报通知

**指导**：如果 Microsoft 安全响应中心 (MSRC) 发现数据被某方非法访问或未经授权访问，Microsoft 会使用安全事件联系信息联系用户。 事后审查事件，确保问题得到解决。

- [如何设置 Azure 安全中心安全联系人](../security-center/security-center-provide-security-contact-details.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5：将安全警报整合到事件响应系统中

**指南**：使用连续导出功能导出 Azure 安全中心警报和建议，以帮助确定 Azure 资源的风险。 使用连续导出可以手动导出或者持续导出警报和建议。 可以使用 Azure 安全中心数据连接器将警报流式传输到 Azure Sentinel。

- [如何配置连续导出](../security-center/continuous-export.md)

- [如何将警报流式传输到 Azure Sentinel](../sentinel/connect-azure-security-center.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="106-automate-the-response-to-security-alerts"></a>10.6：自动响应安全警报

**指导**：使用 Azure 安全中心内的工作流自动化功能，通过“逻辑应用”针对安全警报和建议自动触发响应，以保护 Azure 资源。

- [如何配置工作流自动化和逻辑应用](../security-center/workflow-automation.md)

**责任**：客户

Azure 安全中心监视：无

## <a name="penetration-tests-and-red-team-exercises"></a>渗透测试和红队练习

[有关详细信息，请参阅 *Azure 安全基线：* 渗透测试和红队演练](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)。

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1：定期对 Azure 资源执行渗透测试，确保修正所有发现的关键安全问题

指南：遵循 Microsoft Rules of Engagement 以确保渗透测试不违反 Microsoft 政策： https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1 

- [对于 Microsoft 红队演练策略和执行，以及针对 Microsoft 托管云基础结构、服务和应用程序的实时站点渗透测试，可在此处找到详细信息](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**责任**：共享

Azure 安全中心监视：无

## <a name="next-steps"></a>后续步骤

- 参阅 [Azure 安全基准 V2 概述](/azure/security/benchmarks/overview)
- 详细了解 [Azure 安全基线](/azure/security/benchmarks/security-baselines-overview)
