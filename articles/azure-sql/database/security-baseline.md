---
title: 适用于 Azure SQL 数据库的 azure 安全基线
description: Azure SQL 数据库安全基准为实现 Azure 安全基准中指定的安全建议提供过程指南和资源。
author: msmbaldwin
ms.service: sql-database
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 87072ecc4bff054d64c3d8576f821e725959ea7e
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101657785"
---
# <a name="azure-security-baseline-for-azure-sql-database"></a>适用于 Azure SQL 数据库的 azure 安全基线

此安全基线将 [Azure 安全性基准1.0 版](../../security/benchmarks/overview.md) 中的指南应用于 Azure SQL 数据库。 Azure 安全基准提供有关如何在 Azure 上保护云解决方案的建议。 内容按照 Azure 安全基准和适用于 Azure SQL 数据库的相关指南 **的定义进行** 分组。 排除了不适用于 Azure SQL 数据库的 **控件**。

若要了解 Azure SQL 数据库如何完全映射到 Azure 安全基准，请参阅 [完整的 AZURE Sql 数据库安全基准映射文件](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)。

## <a name="network-security"></a>网络安全

[有关详细信息，请参阅 *Azure 安全基线：* 网络安全性](../../security/benchmarks/security-control-network-security.md)。

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1：保护虚拟网络中的 Azure 资源

**指导**：可以启用 Azure 专用链接，以允许通过虚拟网络中的专用终结点访问 Azure PaaS 服务（例如，SQL 数据库）和 Azure 托管的客户服务/合作伙伴服务。 虚拟网络与服务之间的流量将通过 Microsoft 主干网络，因此不会从公共 Internet 泄露。 

若要允许流量到达 Azure SQL 数据库，请使用 SQL 服务标记，以允许出站流量通过网络安全组。

虚拟网络规则使 Azure SQL 数据库仅接受从虚拟网络中的所选子网发送的通信。

- [如何设置 Azure SQL 数据库的专用链接](/azure/sql-database/sql-database-private-endpoint-overview#how-to-set-up-private-link-for-azure-sql-database)

- [如何使用数据库服务器的虚拟网络服务终结点和规则](/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview)

**责任**：客户

**Azure 安全中心监视**： [azure 安全基准](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) 是安全中心的默认策略计划，是 [安全中心建议](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md)的基础。 安全中心会自动启用与此控件相关的 Azure 策略定义。 与此控件相关的警报可能需要 [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) 计划相关服务。

**Azure 策略内置定义-Microsoft .sql**：

[!INCLUDE [Resource Policy for Microsoft.Sql 1.1](../../../includes/policy/standards/asb/rp-controls/microsoft.sql-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2：监视并记录虚拟网络、子网和网络接口的配置与流量

**指南**：对于 Azure SQL 数据库服务器部署到的子网，使用 Azure 安全中心并修正网络保护建议。 

对于将连接到 Azure SQL 数据库服务器实例 (VM) 的 Azure 虚拟机，请启用 "网络安全组" (NSG) 流日志来保护这些 Vm，并将日志发送到 Azure 存储帐户以进行流量审核。 

还可以将 NSG 流日志发送到 Log Analytics 工作区，并使用流量分析来提供对 Azure 云中的流量流的见解。 流量分析的优势包括能够可视化网络活动、识别热点、识别安全威胁、了解流量流模式，以及查明网络不当配置。

- [如何启用 NSG 流日志](../../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [了解 Azure 安全中心提供的网络安全](../../security-center/security-center-network-recommendations.md)

- [如何启用和使用流量分析](../../network-watcher/traffic-analytics.md)

- [了解 Azure 安全中心提供的网络安全](../../security-center/security-center-network-recommendations.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4：拒绝与已知恶意的 IP 地址进行通信

**指南**：在与 SQL Server 实例相关联的虚拟网络上启用 DDoS 保护，以防范分布式拒绝服务攻击。 根据 Azure 安全中心集成的威胁情报进行判断，拒绝与已知恶意的或未使用过的 Internet IP 地址通信。

- [如何配置 DDoS 防护](/azure/virtual-network/manage-ddos-protection)

- [了解 Azure 安全中心集成的威胁情报](/azure/security-center/security-center-alerts-data-services)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="15-record-network-packets"></a>1.5：记录网络数据包

**指导**：对于将要连接到 Azure SQL 数据库实例的 Azure 虚拟机 (VM)，为保护这些 VM 的网络安全组 (NSG) 启用 NSG 流日志，然后将日志发送到 Azure 存储帐户以进行流量审核。 启用网络观察程序数据包捕获（如果调查异常活动时有此需要）。

- [如何启用 NSG 流日志](../../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [如何启用网络观察程序](../../network-watcher/network-watcher-create.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6：部署基于网络的入侵检测/入侵防护系统 (IDS/IPS)

**指导**：为 Azure SQL 数据库启用高级威胁防护 (ATP)。  出现可疑数据库活动、潜在漏洞、SQL 注入攻击和异常数据库访问和查询模式时，用户将收到警报。 高级威胁防护也将警报与 Azure 安全中心集成。 

- [了解和使用适用于 Azure SQL 数据库的高级威胁防护](/azure/sql-database/sql-database-threat-detection-overview)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8：最大程度地降低网络安全规则的复杂性和管理开销

**指导**：使用虚拟网络服务标签来定义网络安全组或 Azure 防火墙上的网络访问控制。 创建安全规则时，可以使用服务标记代替特定的 IP 地址。 在规则的相应源或目标字段中指定服务标记名称（例如 ApiManagement），可以允许或拒绝相应服务的流量。 Microsoft 会管理服务标记包含的地址前缀，并会在地址发生更改时自动更新服务标记。

如果将服务终结点用于 Azure SQL 数据库，需要出站到 Azure SQL 数据库公共 IP 地址：必须为 Azure SQL 数据库 IP 启用网络安全组 (NSG) 才能进行连接。 可以使用 Azure SQL 数据库的 NSG 服务标记执行此操作。

- [了解 Azure SQL 数据库的服务终结点的服务标记](/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview#limitations)

- [了解并使用服务标记](../../virtual-network/service-tags-overview.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9：维护网络设备的标准安全配置

**指南**：通过 azure 策略为 Azure SQL 数据库服务器实例定义和实施网络安全配置。 你可以使用 "Sql-dmo" 命名空间来定义自定义策略定义，或者使用为 Azure SQL 数据库服务器网络保护设计的任何内置策略定义。 适用于 Azure SQL 数据库服务器的适用内置网络安全策略的一个示例是： "SQL Server 应使用虚拟网络服务终结点"。

 

使用 Azure 蓝图可以通过在单个蓝图定义中打包关键环境项目（例如 Azure 资源管理模板、基于 Azure 角色的访问控制 (Azure RBAC) 和策略）来简化大规模的 Azure 部署。 轻松将蓝图应用到新的订阅和环境，并通过版本控制来微调控制措施和管理。

- [如何配置和管理 Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

- [如何创建 Azure 蓝图](../../governance/blueprints/create-blueprint-portal.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="110-document-traffic-configuration-rules"></a>1.10：阐述流量配置规则

**指导**：将标记用于网络安全组 (NSG) 以及其他与网络安全和通信流有关的资源。 对于单个 NSG 规则，请使用“说明”字段针对允许流量传入/传出网络的任何规则指定业务需求和/或持续时间等。

使用标记相关的任何内置 Azure Policy 定义（例如“需要标记及其值”）来确保使用标记创建所有资源，并在有现有资源不带标记时发出通知。

可以使用 Azure PowerShell 或 Azure CLI 根据资源的标记查找资源或对其执行操作。

- [如何创建和使用标记](/azure/azure-resource-manager/resource-group-using-tags)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11：使用自动化工具来监视网络资源配置和检测更改

**指南**：使用 Azure 活动日志监视网络资源配置，并检测对与 Azure SQL 数据库服务器实例相关的网络资源所做的更改。 在 Azure Monitor 中创建当关键网络资源发生更改时触发的警报。

- [如何查看和检索 Azure 活动日志事件](/azure/azure-monitor/platform/activity-log-view)

- [如何在 Azure Monitor 中创建警报](/azure/azure-monitor/platform/alerts-activity-log)

**责任**：客户

**Azure 安全中心监视**：无

## <a name="logging-and-monitoring"></a>日志记录和监视

[有关详细信息，请参阅 *Azure 安全基线：* 日志记录和监视](../../security/benchmarks/security-control-logging-monitoring.md)。

### <a name="22-configure-central-security-log-management"></a>2.2：配置中心安全日志管理

**指导**：启用 Azure SQL 数据库审核以跟踪数据库事件，并将这些事件写入 Azure 存储帐户、Log Analytics 工作区或事件中心中的审核日志。

此外，你还可以将 Azure SQL 诊断遥测流式传输到 Azure SQL Analytics，这是一种云解决方案，可跨多个订阅大规模监视 Azure SQL 数据库和 Azure SQL 托管实例的性能。 它可以帮助你收集和可视化 Azure SQL 数据库性能指标，并提供内置智能进行性能故障排除。

- [如何设置 Azure SQL 数据库的审核](/azure/sql-database/sql-database-auditing)

- [如何使用 Azure Monitor 收集平台日志和指标](/azure/sql-database/sql-database-metrics-diag-logging)

- [如何将诊断流到 Azure SQL Analytics](/azure/sql-database/sql-database-metrics-diag-logging#stream-into-azure-sql-analytics)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：为 Azure 资源启用审核日志记录

**指南**：在 Azure SQL 数据库服务器实例上启用审核，并为 (azure 存储、Log Analytics 或事件中心) 的审核日志选择存储位置。

- [如何为 Azure SQL Server 启用审核](/azure/sql-database/sql-database-auditing)

**责任**：客户

**Azure 安全中心监视**： [azure 安全基准](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) 是安全中心的默认策略计划，是 [安全中心建议](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md)的基础。 安全中心会自动启用与此控件相关的 Azure 策略定义。 与此控件相关的警报可能需要 [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) 计划相关服务。

**Azure 策略内置定义-Microsoft .sql**：

[!INCLUDE [Resource Policy for Microsoft.Sql 2.3](../../../includes/policy/standards/asb/rp-controls/microsoft.sql-2-3.md)]

### <a name="25-configure-security-log-storage-retention"></a>2.5：配置安全日志存储保留期

**指南**：将 Azure SQL 数据库日志存储在 Log Analytics 工作区中时，请根据组织的符合性法规设置日志保持期。

- [如何设置日志保留参数](/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**责任**：客户

**Azure 安全中心监视**： [azure 安全基准](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) 是安全中心的默认策略计划，是 [安全中心建议](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md)的基础。 安全中心会自动启用与此控件相关的 Azure 策略定义。 与此控件相关的警报可能需要 [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) 计划相关服务。

**Azure 策略内置定义-Microsoft .sql**：

[!INCLUDE [Resource Policy for Microsoft.Sql 2.5](../../../includes/policy/standards/asb/rp-controls/microsoft.sql-2-5.md)]

### <a name="26-monitor-and-review-logs"></a>2.6：监视和查看日志

**指导**：分析和监视日志中的异常行为，并定期审查结果。 使用 Azure 安全中心的高级威胁防护来对与 Azure SQL 数据库实例相关的异常活动发出警报。 或者，基于与 Azure SQL 数据库实例相关的指标值或 Azure 活动日志条目配置警报。

- [了解 Azure SQL Server 的高级威胁防护和警报](/azure/sql-database/sql-database-threat-detection-overview)

- [如何配置 Azure SQL 数据库的自定义警报](alerts-insights-configure-portal.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7：针对异常活动启用警报

**指南**：对 Azure SQL 数据库使用 Azure 安全中心高级威胁防护，以便在异常活动上进行监视和警报。 为 SQL 数据库启用 Azure Defender for SQL。 适用于 SQL 的 Azure Defender 包含用于发现和分类敏感数据、呈现和缓解潜在的数据库漏洞以及检测可能指示数据库威胁的异常活动的功能。

- [了解 Azure SQL 数据库的高级威胁防护和警报](/azure/sql-database/sql-database-threat-detection-overview)

- [如何为 Azure SQL Database 启用适用于 SQL 的 Azure Defender](azure-defender-for-sql.md)

- [如何在 Azure 安全中心管理警报](../../security-center/security-center-managing-and-responding-alerts.md)

**责任**：客户

**Azure 安全中心监视**： [azure 安全基准](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) 是安全中心的默认策略计划，是 [安全中心建议](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md)的基础。 安全中心会自动启用与此控件相关的 Azure 策略定义。 与此控件相关的警报可能需要 [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) 计划相关服务。

**Azure 策略内置定义-Microsoft .sql**：

[!INCLUDE [Resource Policy for Microsoft.Sql 2.7](../../../includes/policy/standards/asb/rp-controls/microsoft.sql-2-7.md)]

## <a name="identity-and-access-control"></a>标识和访问控制

[有关详细信息，请参阅 *Azure 安全基线：* 标识和访问控制](../../security/benchmarks/security-control-identity-access-control.md)。

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1：维护管理帐户的清单

**指导**：Azure Active Directory (Azure AD) 具有必须显式分配且可查询的内置角色。 使用 Azure AD PowerShell 模块执行即席查询，以发现属于管理组成员的帐户。

- [如何使用 PowerShell 获取 Azure AD 中的目录角色](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0&amp;preserve-view=true)

- [如何使用 PowerShell 获取 Azure AD 中目录角色的成员](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0&amp;preserve-view=true)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="32-change-default-passwords-where-applicable"></a>3.2：在适用的情况下更改默认密码

**指导**：Azure Active Directory (Azure AD) 没有默认密码的概念。 预配 Azure SQL 数据库实例时，建议选择将身份验证与 Azure AD 集成。

- [如何配置和管理 Azure SQL Azure AD 身份验证](/azure/sql-database/azure-sql/database/authentication-aad-configure)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="33-use-dedicated-administrative-accounts"></a>3.3：使用专用管理帐户

**指导**：围绕专用管理帐户的使用创建策略和过程。 使用 Azure 安全中心标识和访问管理来监视管理帐户的数量。

- [了解 Azure 安全中心标识和访问](../../security-center/security-center-identity-access.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5：对所有基于 Azure Active Directory 的访问使用多重身份验证

**指南**：启用 Azure Active Directory (Azure AD) 多重身份验证，并遵循 Azure 安全中心的标识和访问管理建议。

- [如何在 Azure 中启用多重身份验证](../../active-directory/authentication/howto-mfa-getstarted.md)

- [如何在 Azure 安全中心监视标识和访问](../../security-center/security-center-identity-access.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3.6：使用由 Azure 管理的安全工作站执行管理任务

**指南**：通过配置为登录和配置 Azure 资源的多重身份验证，使用特权访问工作站 (PAW) 。

- [了解特权访问工作站](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [如何在 Azure 中启用多重身份验证](../../active-directory/authentication/howto-mfa-getstarted.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7：记录来自管理帐户的可疑活动并对其发出警报

**指导**：当环境中出现可疑或不安全的活动时，请使用 Azure Active Directory (Azure AD) 安全报告来生成日志和警报。

使用适用于 Azure SQL 数据库的高级威胁防护来检测异常活动，这些活动表明访问或利用数据库的异常和潜在有害尝试。

- [如何确定标记为存在风险活动的 Azure AD 用户](/azure/active-directory/reports-monitoring/concept-user-at-risk)

- [如何在 Azure 安全中心监视用户的标识和访问活动](../../security-center/security-center-identity-access.md)

- [查看高级威胁防护和潜在警报](https://docs.microsoft.com/azure/azure-sql/database/threat-detection-overview#alerts)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8：仅从批准的位置管理 Azure 资源

**指导**：使用条件访问命名位置，仅允许从 IP 地址范围或国家/地区的特定逻辑分组进行门户和 Azure 资源管理访问。

- [如何在 Azure 中配置命名位置](../../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="39-use-azure-active-directory"></a>3.9：使用 Azure Active Directory

**指南**：为 Azure SQL 数据库服务器实例创建 Azure Active Directory (Azure AD) 管理员。

- [如何配置和管理 Azure SQL Azure AD 身份验证](authentication-aad-configure.md)

- [如何创建和配置 Azure AD 实例](../../active-directory-domain-services/tutorial-create-instance.md)

**责任**：客户

**Azure 安全中心监视**： [azure 安全基准](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) 是安全中心的默认策略计划，是 [安全中心建议](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md)的基础。 安全中心会自动启用与此控件相关的 Azure 策略定义。 与此控件相关的警报可能需要 [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) 计划相关服务。

**Azure 策略内置定义-Microsoft .sql**：

[!INCLUDE [Resource Policy for Microsoft.Sql 3.9](../../../includes/policy/standards/asb/rp-controls/microsoft.sql-3-9.md)]

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10：定期审查和协调用户访问

**指导**：Azure Active Directory (Azure AD) 提供日志来帮助发现过时的帐户。 此外，请使用 Azure 标识访问评审来有效管理组成员身份、对企业应用程序的访问和角色分配。 可以定期评审用户的访问权限，确保只有适当的用户才能持续拥有访问权限。

- [如何使用 Azure 标识访问评审](../../active-directory/governance/access-reviews-overview.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11：监视尝试访问已停用凭据的行为

**指南**：配置 Azure Active Directory (Azure AD 使用 Azure SQL) 身份验证，并创建 Azure AD 用户帐户的诊断设置，并将审核日志和登录日志发送到 Log Analytics 工作区。 在 Log Analytics 工作区中配置所需的警报。

- [如何配置和管理 Azure SQL Azure AD 身份验证](authentication-aad-configure.md)

- [如何将 Azure 活动日志集成到 Azure Monitor](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12：针对帐户登录行为偏差发出警报

**指南**：使用 Azure Active Directory (Azure AD) 标识保护和风险检测来配置对检测到的与用户标识相关的可疑操作的自动响应。 此外，可将数据引入 Azure Sentinel 以做进一步调查。

- [如何查看 Azure AD 风险登录](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [如何配置和启用标识保护风险策略](../../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13：在支持场合下为 Microsoft 提供对相关客户数据的访问权限

**指南**：在 Microsoft 需要访问客户数据的支持方案中，Azure 客户密码箱提供了一个界面，供客户查看和批准或拒绝客户数据访问请求。

- [了解客户密码箱](../../security/fundamentals/customer-lockbox-overview.md)

**责任**：客户

**Azure 安全中心监视**：无

## <a name="data-protection"></a>数据保护

[有关详细信息，请参阅 *Azure 安全基线：* 数据保护](../../security/benchmarks/security-control-data-protection.md)。

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1：维护敏感信息的清单

**指导**：使用标记可以帮助跟踪存储或处理敏感信息的 Azure 资源。

- [如何创建和使用标记](/azure/azure-resource-manager/resource-group-using-tags)

**责任**：客户

**Azure 安全中心监视**： [azure 安全基准](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) 是安全中心的默认策略计划，是 [安全中心建议](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md)的基础。 安全中心会自动启用与此控件相关的 Azure 策略定义。 与此控件相关的警报可能需要 [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) 计划相关服务。

**Azure 策略内置定义-Microsoft .sql**：

[!INCLUDE [Resource Policy for Microsoft.Sql 4.1](../../../includes/policy/standards/asb/rp-controls/microsoft.sql-4-1.md)]

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2：隔离存储或处理敏感信息的系统

指南：为开发、测试和生产实现单独的订阅和/或管理组。 资源应当按 VNet/子网进行分隔，相应地进行标记，并在 NSG 或 Azure 防火墙中提供保护。 应当隔离用于存储或处理敏感数据的资源。 使用专用链接;在 Vnet 中部署 Azure SQL Server，并使用专用终结点进行私下连接。

- [如何创建其他 Azure 订阅](/azure/billing/billing-create-subscription)

- [如何创建管理组](/azure/governance/management-groups/create)

- [如何创建和使用标记](/azure/azure-resource-manager/resource-group-using-tags)

- [如何设置 Azure SQL 数据库的专用链接](/azure/sql-database/sql-database-private-endpoint-overview#how-to-set-up-private-link-for-azure-sql-database)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3：监视和阻止未经授权的敏感信息传输

**指导**：对于存储或处理敏感信息的 Azure SQL 数据库中的数据库，请使用标记将该数据库和相关资源标记为敏感。 结合 Azure SQL 数据库实例上的网络安全组服务标记配置专用链接，以防止敏感信息外泄。

对于 Microsoft 管理的底层平台，Microsoft 会将所有客户内容视为敏感数据，并会全方位地防范客户数据丢失和遭到透露。 为了确保 Azure 中的客户数据保持安全，Microsoft 实施并维护了一套可靠的数据保护控制措施和功能。

- [如何配置 Private Link 和 Nsg 以防止 Azure SQL 数据库实例上的数据渗透](/azure/sql-database/sql-database-private-endpoint-overview)

- [了解 Azure 中的客户数据保护](../../security/fundamentals/protection-customer-data.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5：使用有效的发现工具识别敏感数据

**指导**：使用 Azure SQL 数据库数据发现和分类功能。 数据发现和分类提供了内置于 Azure SQL 数据库的高级功能，可用于发现、分类、标记和保护数据库中的敏感数据。

- [如何使用 Azure SQL Server 的数据发现和分类功能](/azure/sql-database/sql-database-data-discovery-and-classification)

**责任**：客户

**Azure 安全中心监视**： [azure 安全基准](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) 是安全中心的默认策略计划，是 [安全中心建议](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md)的基础。 安全中心会自动启用与此控件相关的 Azure 策略定义。 与此控件相关的警报可能需要 [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) 计划相关服务。

**Azure 策略内置定义-Microsoft .sql**：

[!INCLUDE [Resource Policy for Microsoft.Sql 4.5](../../../includes/policy/standards/asb/rp-controls/microsoft.sql-4-5.md)]

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6：使用基于角色的访问控制来控制对资源的访问

**指导**：使用 Azure Active Directory (Azure AD) 来验证和控制对 Azure SQL 数据库实例的访问。

- [如何将 Azure SQL Server 与 Azure AD 进行身份验证](/azure/sql-database/sql-database-aad-authentication)

- [如何在 Azure SQL Server 中控制访问权限](/azure/sql-database/sql-database-control-access)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8：静态加密敏感信息

**指南**：透明数据加密 (TDE) 通过加密静态数据，帮助保护 Azure sql 数据库、azure sql 托管实例和 Azure 数据仓库免受恶意脱机活动的威胁。 它可执行静态数据库、关联备份和事务日志文件的实时加密和解密，无需更改应用程序。 默认情况下，在 SQL 数据库和 SQL 托管实例中会为所有新部署的数据库启用 TDE。 Microsoft 或客户可以管理 TDE 加密密钥。

- [如何管理透明数据加密和使用自己的加密密钥](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql?tabs=azure-portal#manage-transparent-data-encryption)

**责任**：客户

**Azure 安全中心监视**： [azure 安全基准](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) 是安全中心的默认策略计划，是 [安全中心建议](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md)的基础。 安全中心会自动启用与此控件相关的 Azure 策略定义。 与此控件相关的警报可能需要 [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) 计划相关服务。

**Azure 策略内置定义-Microsoft .sql**：

[!INCLUDE [Resource Policy for Microsoft.Sql 4.8](../../../includes/policy/standards/asb/rp-controls/microsoft.sql-4-8.md)]

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9：记录对关键 Azure 资源的更改并对此类更改发出警报

**指导**：将 Azure Monitor 与 Azure 活动日志结合使用，以创建在 Azure SQL 数据库的生产实例和其他关键资源或相关资源发生更改时发出的警报。

- [如何针对 Azure 活动日志事件创建警报](/azure/azure-monitor/platform/alerts-activity-log)

**责任**：客户

**Azure 安全中心监视**：无

## <a name="vulnerability-management"></a>漏洞管理

[有关详细信息，请参阅 *Azure 安全基线：* 漏洞管理。](../../security/benchmarks/security-control-vulnerability-management.md)

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1：运行自动漏洞扫描工具

**指南**：为 Azure sql 数据库启用 azure DEFENDER for sql，并遵循 Azure 安全中心的建议，了解如何在 Azure sql server 上执行漏洞评估。

- [如何对 Azure SQL 数据库运行漏洞评估](/azure/sql-database/sql-vulnerability-assessment)

- [如何启用适用于 SQL 的 Azure Defender](azure-defender-for-sql.md)

- [如何实现 Azure 安全中心漏洞评估建议](/azure/security-center/security-center-vulnerability-assessment-recommendations)

**责任**：客户

**Azure 安全中心监视**： [azure 安全基准](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) 是安全中心的默认策略计划，是 [安全中心建议](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md)的基础。 安全中心会自动启用与此控件相关的 Azure 策略定义。 与此控件相关的警报可能需要 [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) 计划相关服务。

**Azure 策略内置定义-Microsoft .sql**：

[!INCLUDE [Resource Policy for Microsoft.Sql 5.1](../../../includes/policy/standards/asb/rp-controls/microsoft.sql-5-1.md)]

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4：比较连续进行的漏洞扫描

**指导**：为 Azure SQL 数据库实例启用定期重复扫描；这会将漏洞评估配置为每周自动对数据库运行一次扫描。 扫描结果摘要会发送到你提供的电子邮件地址。 比较结果以验证漏洞已得到修复。

- [如何导出 Azure 安全中心的漏洞评估报告](/azure/sql-database/sql-vulnerability-assessment#implementing-vulnerability-assessment)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5：使用风险评级过程来确定已发现漏洞的修正措施的优先级

**指导**：使用 Azure 安全中心提供的默认风险评级（安全功能分数）。

- [了解 Azure 安全中心安全功能分数](/azure/security-center/security-center-secure-score)

**责任**：客户

**Azure 安全中心监视**： [azure 安全基准](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) 是安全中心的默认策略计划，是 [安全中心建议](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md)的基础。 安全中心会自动启用与此控件相关的 Azure 策略定义。 与此控件相关的警报可能需要 [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) 计划相关服务。

**Azure 策略内置定义-Microsoft .sql**：

[!INCLUDE [Resource Policy for Microsoft.Sql 5.5](../../../includes/policy/standards/asb/rp-controls/microsoft.sql-5-5.md)]

## <a name="inventory-and-asset-management"></a>清单和资产管理

[有关详细信息，请参阅 *Azure 安全基线：* 清单和资产管理](../../security/benchmarks/security-control-inventory-asset-management.md)。

### <a name="61-use-automated-asset-discovery-solution"></a>6.1：使用自动化资产发现解决方案

**指南**：使用 Azure 资源关系图可查询和发现所有资源 (包括订阅中)  () 的 azure SQL Server 实例。 确保你在租户中拥有适当的（读取）权限，并且可以枚举所有 Azure 订阅，以及订阅中的资源。

尽管可以通过 Resource Graph 发现经典 Azure 资源，但我们强烈建议你今后还是创建并使用 Azure 资源管理器资源。

- [如何使用 Azure Resource Graph 创建查询](../../governance/resource-graph/first-query-portal.md)

- [如何查看 Azure 订阅](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-4.8.0&amp;preserve-view=true)

- [了解 Azure RBAC](../../role-based-access-control/overview.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="62-maintain-asset-metadata"></a>6.2：维护资产元数据

**指导**：将标记应用于 Azure 资源，从而将元数据按逻辑组织到分类中。

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

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5：监视未批准的 Azure 资源

**指南**：在 Azure Policy 中使用以下内置策略定义，对可以在客户订阅中创建的资源类型施加限制：

- 不允许的资源类型

- 允许的资源类型

使用 Azure Resource Graph 查询/发现订阅中的资源。 确保环境中存在的所有 Azure 资源已获得批准。

- [如何配置和管理 Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

- [如何使用 Azure Resource Graph 创建查询](../../governance/resource-graph/first-query-portal.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="69-use-only-approved-azure-services"></a>6.9：仅使用已批准的 Azure 服务

**指导**：使用以下内置策略定义，通过 Azure Policy 对可以在客户订阅中创建的资源类型施加限制：
- 不允许的资源类型
- 允许的资源类型

使用 Azure Resource Graph 查询/发现订阅中的资源。 确保环境中存在的所有 Azure 资源已获得批准。

- [如何配置和管理 Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

- [如何使用 Azure Policy 拒绝特定的资源类型](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11：限制用户与 Azure 资源管理器进行交互的能力

**指南**：使用 Azure 条件访问可通过为“Microsoft Azure 管理”应用配置“阻止访问”，限制用户与 Azure 资源管理器进行交互的能力。

- [如何配置条件访问以阻止访问 Azure 资源管理器](../../role-based-access-control/conditional-access-azure-management.md)

**责任**：客户

**Azure 安全中心监视**：无

## <a name="secure-configuration"></a>安全配置

[有关详细信息，请参阅 *Azure 安全基线：* 安全配置](../../security/benchmarks/security-control-secure-configuration.md)。

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1：为所有 Azure 资源建立安全配置

**指南**：对 Azure SQL 服务器/数据库使用 azure 策略或 Azure 安全中心建议来维护所有 azure 资源的安全配置。

- [如何配置和管理 Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3：维护安全的 Azure 资源配置

**指南**：使用 Azure Policy“[拒绝]”和“[不存在则部署]”对不同的 Azure 资源强制实施安全设置。

- [如何配置和管理 Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

- [了解 Azure Policy 效果](../../governance/policy/concepts/effects.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5：安全存储 Azure 资源的配置

**指导**：如果使用自定义的 Azure Policy 定义，请使用 Azure DevOps 或 Azure Repos 安全地存储和管理代码。

- [如何在 Azure DevOps 中存储代码](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops&amp;preserve-view=true)

- [Azure Repos 文档](https://docs.microsoft.com/azure/devops/repos/?view=azure-devops&amp;preserve-view=true)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7：部署 Azure 资源的配置管理工具

**指导**：使用“Microsoft.SQL”命名空间中的 Azure Policy 别名创建自定义策略，以审核、强制实施系统配置并设置相关警报。 另外，开发一个用于管理策略例外的流程和管道。

- [如何配置和管理 Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9：为 Azure 资源实施自动配置监视

**指南**：利用 Azure 安全中心对 Azure SQL 服务器和数据库执行基线扫描。

- [如何在 Azure 安全中心修正建议](/azure/security-center/security-center-sql-service-recommendations)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="711-manage-azure-secrets-securely"></a>7.11：安全管理 Azure 机密

**指导**：使用 Azure Key Vault 存储 Azure SQL 数据库透明数据加密 (TDE) 的加密密钥。

- [如何保护 Azure 中存储的敏感数据 SQL Server 并将加密密钥存储在 Azure Key Vault](/azure/sql-database/sql-database-always-encrypted-azure-key-vault)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="712-manage-identities-securely-and-automatically"></a>7.12：安全自动管理标识

**指导**：使用托管标识在 Azure Active Directory (Azure AD) 中为 Azure 服务提供一个自动托管的标识。 使用托管标识可以向支持 Azure AD 身份验证的任何服务（包括 Azure Key Vault）进行身份验证，无需在代码中放入任何凭据。

- [教程：使用 Windows VM 系统分配的托管标识访问 Azure SQL](../../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-sql.md)

- [如何配置托管标识](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13：消除意外的凭据透露

**指导**：实施凭据扫描程序来识别代码中的凭据。 凭据扫描程序还会建议将发现的凭据转移到更安全的位置，例如 Azure Key Vault。 

- [如何设置凭据扫描程序](https://secdevtools.azurewebsites.net/helpcredscan.html)

**责任**：客户

**Azure 安全中心监视**：无

## <a name="malware-defense"></a>恶意软件防护

[有关详细信息，请参阅 *Azure 安全基线：* 恶意软件防护](../../security/benchmarks/security-control-malware-defense.md)。

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2：预先扫描要上传到非计算 Azure 资源的文件

**指导**：Microsoft 反恶意软件会在支持 Azure 服务（例如，Azure 应用服务）的基础主机上启用，但不会对客户内容运行。

预先扫描要上传到非计算 Azure 资源的任何内容，例如应用服务、Data Lake Storage、Blob 存储、Azure SQL Server 等。Microsoft 无法访问这些实例中的数据。

- [了解适用于 Azure 云服务和虚拟机的 Microsoft Antimalware](../../security/fundamentals/antimalware.md)

**责任**：客户

**Azure 安全中心监视**：无

## <a name="data-recovery"></a>数据恢复

[有关详细信息，请参阅 *Azure 安全基线：* 数据恢复](../../security/benchmarks/security-control-data-recovery.md)。

### <a name="91-ensure-regular-automated-back-ups"></a>9.1：确保定期执行自动备份

**指导**：为了防止企业丢失数据，Azure SQL 数据库每周自动创建完整数据库备份，每 12 小时自动创建差异数据库备份，每隔 5 - 10 分钟自动创建事务日志备份。 所有服务层级的备份在 RA-GRS 存储中存储至少 7 天。 对于时间点还原，所有服务层级（“基本”除外）都支持可配置的备份保留期，最长为 35 天。

为了满足不同的符合性要求，可为每周、每月和/或每年备份选择不同的保留期。 存储消耗量取决于所选的备份频率和保留期。

- [了解 Azure SQL Server 的备份和业务连续性](/azure/sql-database/sql-database-business-continuity)

**责任**：共享

**Azure 安全中心监视**： [azure 安全基准](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) 是安全中心的默认策略计划，是 [安全中心建议](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md)的基础。 安全中心会自动启用与此控件相关的 Azure 策略定义。 与此控件相关的警报可能需要 [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) 计划相关服务。

**Azure 策略内置定义-Microsoft .sql**：

[!INCLUDE [Resource Policy for Microsoft.Sql 9.1](../../../includes/policy/standards/asb/rp-controls/microsoft.sql-9-1.md)]

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2：执行完整系统备份，并备份客户管理的所有密钥

**指导**：Azure SQL 数据库自动创建数据库备份（保留 7 到 35 天），并使用 Azure 读取访问异地冗余存储 (RA-GRS)，确保即使数据中心不可用也会保留这些备份。 这些备份是自动创建的。 如果需要，为 Azure SQL 数据库启用长期冗余备份。

如果要将客户管理的密钥用于透明数据加密，请确保已备份你的密钥。

- [了解 Azure 中的备份 SQL Server](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups?tabs=single-database)

- [如何在 Azure 中备份密钥保管库密钥](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey?view=azps-4.8.0&amp;preserve-view=true)

**责任**：客户

**Azure 安全中心监视**： [azure 安全基准](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) 是安全中心的默认策略计划，是 [安全中心建议](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md)的基础。 安全中心会自动启用与此控件相关的 Azure 策略定义。 与此控件相关的警报可能需要 [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) 计划相关服务。

**Azure 策略内置定义-Microsoft .sql**：

[!INCLUDE [Resource Policy for Microsoft.Sql 9.2](../../../includes/policy/standards/asb/rp-controls/microsoft.sql-9-2.md)]

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3：验证所有备份，包括客户管理的密钥

**指导**：确保能够定期在 Azure 备份中执行内容数据还原。 如有必要，在隔离的 VLAN 中对还原内容进行测试。 测试对备份的客户管理的密钥进行还原。

- [如何在 Azure 中还原密钥保管库密钥](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultkey?view=azps-4.8.0&amp;preserve-view=true)

- [如何使用时间点还原恢复 Azure SQL 数据库备份](/azure/sql-database/sql-database-recovery-using-backups#point-in-time-restore)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4：确保保护备份和客户管理的密钥

**指导**：在 Azure Key Vault 中启用“软删除”，以防止意外删除或恶意删除密钥。

- [如何在 Key Vault 中启用软删除](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

**责任**：客户

**Azure 安全中心监视**：无

## <a name="incident-response"></a>事件响应

[有关详细信息，请参阅 *Azure 安全基线：* 事件响应](../../security/benchmarks/security-control-incident-response.md)。

### <a name="101-create-an-incident-response-guide"></a>10.1：创建事件响应指导

**指导**：确保在书面的事件响应计划中定义人员职责，以及事件处理/管理的各个阶段。

- [如何在 Azure 安全中心配置工作流自动化](../../security-center/security-center-planning-and-operations-guide.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2：创建事件评分和优先级设定过程

**指导**：安全中心将为警报分配严重性来帮助你确定每条警报的处理优先顺序，以便在资源泄密时可以立即采取措施。 严重性取决于安全中心对调查结果或用于发出警报的分析的确信程度，以及对导致警报的活动背后存在恶意意图的确信程度。

- [Azure 安全中心中的安全警报](../../security-center/security-center-alerts-overview.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="103-test-security-response-procedures"></a>10.3：测试安全响应过程

**指导**：定期执行演练来测试系统的事件响应功能。 识别弱点和差距，并根据需要修改计划。

- [可以参考 NIST 发布：针对 IT 计划和功能的测试、培训和运用计划指南](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4：提供安全事件联系人详细信息，并针对安全事件配置警报通知

**指南**：如果 Microsoft 安全响应中心 (MSRC) 发现你的数据被非法或未授权的一方访问，microsoft 将使用安全事件联系人信息与你联系。

- [如何设置 Azure 安全中心安全联系人](../../security-center/security-center-provide-security-contact-details.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5：将安全警报整合到事件响应系统中

**指导**：使用连续导出功能导出 Azure 安全中心警报和建议。 使用连续导出可以手动导出或者持续导出警报和建议。 可以使用 Azure 安全中心数据连接器将警报流式传输到 Azure Sentinel。

- [如何配置连续导出](../../security-center/continuous-export.md)

- [如何将警报流式传输到 Azure Sentinel](../../sentinel/connect-azure-security-center.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="106-automate-the-response-to-security-alerts"></a>10.6：自动响应安全警报

**指导**：使用 Azure 安全中心内的工作流自动化功能可以通过“逻辑应用”针对安全警报和建议自动触发响应。

- [如何配置工作流自动化和逻辑应用](../../security-center/workflow-automation.md)

**责任**：客户

**Azure 安全中心监视**：无

## <a name="penetration-tests-and-red-team-exercises"></a>渗透测试和红队练习

[有关详细信息，请参阅 *Azure 安全基线：* 渗透测试和红队演练](../../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)。

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1：定期对 Azure 资源执行渗透测试，确保修正所有发现的关键安全问题

**指导**：请遵循 Microsoft 云渗透测试互动规则，确保你的渗透测试不违反 Microsoft 政策。 使用 Microsoft 红队演练策略和执行，以及针对 Microsoft 托管云基础结构、服务和应用程序执行现场渗透测试。 

- [参与的渗透测试规则](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft 云红色组合](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**责任**：共享

**Azure 安全中心监视**：无

## <a name="next-steps"></a>后续步骤

- 参阅 [Azure 安全基准 V2 概述](/azure/security/benchmarks/overview)
- 详细了解 [Azure 安全基线](/azure/security/benchmarks/security-baselines-overview)
