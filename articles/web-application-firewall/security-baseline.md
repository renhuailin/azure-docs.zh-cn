---
title: Azure Web 应用程序防火墙的 Azure 安全基线
description: Azure Web 应用程序防火墙安全基线为实现 Azure 安全基准中指定的安全建议提供过程指导和资源。
author: msmbaldwin
ms.service: web-application-firewall
ms.topic: conceptual
ms.date: 04/08/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 6aa3e2b84c4349e2134ddeb2a60fd193f56f84e8
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/22/2021
ms.locfileid: "107875948"
---
# <a name="azure-security-baseline-for-azure-web-application-firewall"></a>Azure Web 应用程序防火墙的 Azure 安全基线

此安全基线将 [Azure 安全基准 1.0 版](../security/benchmarks/overview-v1.md)中的指南应用于 Azure Web 应用程序防火墙。 Azure 安全基准提供有关如何在 Azure 上保护云解决方案的建议。 内容按照 Azure 安全基准和适用于 Azure Web 应用程序防火墙的相关指南所定义的安全控制进行分组。 

> [!NOTE]
> 排除了不适用于 Azure Web 应用程序防火墙或 Microsoft 为其责任方的控件。 若要了解 Azure Web 应用程序防火墙如何完全映射到 Azure 安全基准，请参阅[完整的 Azure Web 应用程序防火墙安全基线映射文件](https://github.com/MicrosoftDocs/SecurityBenchmarks/raw/master/Azure%20Offer%20Security%20Baselines/1.1/azure-web-application-firewall-security-baseline-v1.1.xlsx)。

## <a name="network-security"></a>网络安全

[有关详细信息，请参阅 *Azure 安全基线：* 网络安全性](../security/benchmarks/security-control-network-security.md)。

### <a name="13-protect-critical-web-applications"></a>1.3：保护关键 Web 应用程序

**指导**：使用 Microsoft Azure Web 应用程序防火墙 (WAF) 为 Web 应用程序提供集中式保护，使其免受 SQL 注入、跨站脚本等常见攻击和漏洞的影响。 

- 检测模式：使用此模式来获知网络流量信息，并了解和查看误报。 它监视和记录所有威胁警报。 请确保已选择诊断和 WAF 日志并将其打开。 请注意，当 WAF 在检测模式下运行时，它不会阻止传入请求。
- 防护模式：阻止规则检测到的入侵和攻击。 攻击者会收到“403 未授权访问”异常，且连接会终止。 阻止模式会在 WAF 日志中记录此类攻击。

使用 WAF 检测模式设置网络流量基线。 确定流量需求后，将 WAF 配置为防护模式来阻止不需要的流量。

针对任何不受 WAF 保护的启用了 Web 的资源跟进安全中心的高严重性建议。  

- [Web 应用程序防火墙 CRS 规则组和规则](ag/application-gateway-crs-rulegroups-rules.md) 

- [应用程序网关上的 WAF 模式](https://docs.microsoft.com/azure/web-application-firewall/ag/ag-overview#waf-modes)

- [Front Door 上的 WAF 模式](https://docs.microsoft.com/azure/web-application-firewall/afds/afds-overview#waf-modes)

**责任**：客户

Azure 安全中心监视：无

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4：拒绝与已知恶意的 IP 地址进行通信

**指导**：将自定义规则用于 Azure Web 应用程序防火墙 (WAF) 以允许和阻止流量。 例如，可以阻止来自一系列 IP 地址的所有流量。 将 Azure WAF 配置为在防护模式下运行，这会阻止规则检测到的入侵和攻击。 攻击者会收到“403 未授权访问”异常，且连接会终止。 阻止模式会在 WAF 日志中记录此类攻击。

- [应用程序网关上的 WAF 模式](https://docs.microsoft.com/azure/web-application-firewall/ag/ag-overview#waf-modes)

- [Front Door 上的 WAF 模式](https://docs.microsoft.com/azure/web-application-firewall/afds/afds-overview#waf-modes)

**责任**：客户

**Azure 安全中心监视**：[Azure 安全基准](/azure/governance/policy/samples/azure-security-benchmark)是安全中心的默认策略计划，是 [安全中心建议](/azure/security-center/security-center-recommendations)的基础。 安全中心会自动启用与此控件相关的 Azure Policy 定义。 与此控件相关的警报可能需要相关服务的 [Azure Defender](/azure/security-center/azure-defender) 计划。

Azure Policy 内置定义 - Microsoft.Network：

[!INCLUDE [Resource Policy for Microsoft.Network 1.4](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-4.md)]

### <a name="17-manage-traffic-to-web-applications"></a>1.7：管理发往 Web 应用程序的流量

**指导**：Azure Web 应用程序防火墙 (WAF) 是 Azure Web 应用程序保护的核心组件。 使用 Azure WAF 为 Web 应用程序提供集中式保护，通过针对 OWASP 前 10 大类中已知攻击特征而预先配置的托管规则集来使其免受常见攻击和漏洞的影响。

使用规则和规则组来自定义 Azure WAF 以满足 Web 应用程序要求并消除误报。 为 WAF 后面的每个站点关联 WAF 策略，以允许进行特定于站点的配置。 Azure WAF 支持异地筛选、速率限制、Azure 托管的默认规则集规则。 可以创建自定义规则来满足应用程序的需求。 

将 Azure WAF 配置为在检测模式下为网络流量设置基线后在防护模式下运行确定的时间段。 防护模式下，Azure WAF 阻止规则检测到的入侵和攻击。 攻击者会收到“403 未授权访问”异常，且连接会终止。 阻止模式会在 WAF 日志中记录此类攻击。

- [应用程序网关上的 WAF 模式](https://docs.microsoft.com/azure/web-application-firewall/ag/ag-overview#waf-modes)

- [Front Door 上的 WAF 模式](https://docs.microsoft.com/azure/web-application-firewall/afds/afds-overview#waf-modes)

- [Web 应用程序防火墙 CRS 规则组和规则](https://docs.microsoft.com/azure/web-application-firewall/ag/application-gateway-crs-rulegroups-rules?tabs=owasp31)

**责任**：客户

Azure 安全中心监视：无

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8：最大程度地降低网络安全规则的复杂性和管理开销

**指导**：创建和关联 Azure 订阅中的资源，并使用标记来有逻辑地整理它们，从而检测常见的应用程序配置错误（例如 APACHE 和 IIS）。 

基于应用的标记元数据将规则和规则组应用于 Azure Web 应用程序防火墙 (WAF) 策略。

- [应用程序网关 WAF 策略](/cli/azure/network/application-gateway/waf-policy)

- [Front Door WAF 策略](/cli/azure/network/front-door/waf-policy)

**责任**：客户

Azure 安全中心监视：无

### <a name="110-document-traffic-configuration-rules"></a>1.10：阐述流量配置规则

**指导**：对与 Azure 应用程序网关子网中 Azure Web 应用程序防火墙 (WAF) 关联的网络安全组以及与网络安全和流量流相关的任何其他资源使用标记。 针对各个网络安全组规则，请使用“说明”字段为允许流量传入/传出网络的任何规则指定业务需要、持续时间等。

使用标记相关的任何内置 Azure Policy 定义（例如“需要标记及其值”）来确保使用标记创建所有资源，并在有现有资源不带标记时发出通知。

选择 Azure PowerShell 或 Azure CLI，基于资源的标记查找资源或对其执行操作。

- [如何创建和使用标记](../azure-resource-manager/management/tag-resources.md)

- [如何创建虚拟网络](../virtual-network/quick-create-portal.md)

- [如何创建采用安全配置的 NSG](../virtual-network/tutorial-filter-network-traffic.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11：使用自动化工具来监视网络资源配置和检测更改

**指导**：使用 Azure 活动日志监视网络资源配置，并检测与 Azure Web 应用程序防火墙 (WAF) 部署相关的网络设置和资源的更改。 在 Azure Monitor 中创建警报，这些警报将在对关键网络设置或资源进行更改时触发。

- [如何查看和检索 Azure 活动日志事件](https://docs.microsoft.com/azure/azure-monitor/essentials/activity-log#view-the-activity-log)

- [如何在 Azure Monitor 中创建警报](../azure-monitor/alerts/alerts-activity-log.md)

**责任**：客户

Azure 安全中心监视：无

## <a name="logging-and-monitoring"></a>日志记录和监视

[有关详细信息，请参阅 *Azure 安全基线：* 日志记录和监视](../security/benchmarks/security-control-logging-monitoring.md)。

### <a name="21-use-approved-time-synchronization-sources"></a>2.1：使用批准的时间同步源

**指导**：为 Azure Web 应用程序防火墙 (WAF) 创建网络规则，以允许使用适当的端口和协议（如端口 123 和 UDP）访问 NTP 服务器。

**责任**：共享

Azure 安全中心监视：无

### <a name="22-configure-central-security-log-management"></a>2.2：配置中心安全日志管理

**指导**：配置要发送到中央安全日志管理解决方案（如 Azure Sentinel 或第三方 SIEM）的 Azure Web 应用程序防火墙 (WAF) 日志。 这些日志包括 Azure 活动、诊断和实时 WAF 日志，然后可以在不同的工具（例如 Azure Monitor、Excel 和 Power BI）中查看这些日志。 通过 Azure Web 应用程序防火墙日志可深入了解 Azure WAF 所评估、匹配和阻止的数据。

Azure Sentinel 包含一个内置的 Azure WAF 工作簿，该工作簿提供 Azure WAF 上安全事件的概述。 此工作簿包括事件、匹配和阻止的规则，以及在防火墙日志中记录的其他所有内容。 此遥测数据可用于启动 playbook 自动化，以根据 Azure Sentinel 收集的 WAF 事件来通知或采取更正措施。

- [查看活动日志](../azure-resource-manager/management/view-activity-logs.md)

- [应用程序网关诊断日志](../application-gateway/application-gateway-diagnostics.md)

- [将数据从 Microsoft Web 应用程序防火墙连接到 Azure Sentinel](../sentinel/connect-azure-waf.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：为 Azure 资源启用审核日志记录

**指导**：对 Azure Web 应用程序防火墙 (WAF) 资源启用日志记录以访问审核、安全和诊断日志。 Azure Web 应用程序防火墙提供已配置诊断日志中可用的每个检测到的威胁的详细报告。 自动可用的活动日志包括事件源、日期、用户、时间戳、源地址、目标地址和其他有用元素。

- [日志记录概述](https://docs.microsoft.com/azure/web-application-firewall/ag/ag-overview#logging)

- [Azure Monitor 日志查询概述](../azure-monitor/logs/log-query-overview.md)

- [Azure 平台日志概述](../azure-monitor/essentials/platform-logs-overview.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="25-configure-security-log-storage-retention"></a>2.5：配置安全日志存储保留期

**指导**：将 Azure Web 应用程序防火墙 (WAF) 日志发送到自定义存储帐户，并定义保留策略。 使用 Azure Monitor 根据组织的合规性要求设置 Log Analytics 工作区保持期。
- [为存储帐户配置监视](https://docs.microsoft.com/azure/storage/common/manage-storage-analytics-logs#configure-logging)

**责任**：客户

Azure 安全中心监视：无

### <a name="26-monitor-and-review-logs"></a>2.6：监视和查看日志

**指导**：Azure Web 应用程序防火墙 (WAF) 提供有关检测到的每个威胁的详细报告。 日志记录与 Azure 诊断日志集成，后者提供了有关操作和错误的丰富信息，对审核和故障排除非常重要。 

Azure WAF 实例与安全中心集成以发送警报和运行状况信息进行报告。 使用安全中心的建议来检测未受保护的 Web 应用程序并保护这些易受攻击的资源。 

Azure Sentinel 包含一个内置的 WAF - 防火墙事件工作簿，该工作簿提供 WAF 上安全事件的概述。 这些包括事件、匹配和阻止的规则，以及在防火墙日志中记录的其他所有内容。

- [如何启用 Azure 活动日志的诊断设置](/azure/azure-monitor/platform/activity-log)

- [如何 Azure 应用程序网关启用诊断设置](../application-gateway/application-gateway-diagnostics.md)

- [在 Azure Front Door 中监视指标和日志](../frontdoor/front-door-diagnostics.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7：针对异常活动启用警报

**指导**：启用 Azure 活动日志诊断设置以及 Azure WAF 的诊断设置，并将日志发送到 Log Analytics 工作区。 在 Log Analytics 中执行查询，以搜索字词、识别趋势、分析模式，并根据收集的数据提供许多其他见解。 基于 WAF 指标为异常活动创建警报。 例如，如果阻止的请求数超过“X”，则执行“Y”。

- [如何启用 Azure 活动日志的诊断设置](/azure/azure-monitor/essentials/diagnostic-settings-legacy)

- [如何在 Azure 中创建警报](../azure-monitor/alerts/tutorial-response.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="28-centralize-anti-malware-logging"></a>2.8：集中管理反恶意软件日志记录

**指导**：在关键 Web 应用程序前部署 Azure Web 应用程序防火墙 (WAF)，以对传入的流量进行额外的检查。 

Azure WAF 为 Web 应用程序提供集中式保护，使其免受常见攻击和漏洞的影响，并且通过检查入站 Web 流量来阻止 SQL 注入、跨站脚本、恶意软件上传和 DDoS 攻击等攻击，从而为应用程序提供保护。

- [如何部署 Azure WAF](ag/create-waf-policy-ag.md)

**责任**：客户

Azure 安全中心监视：无

## <a name="identity-and-access-control"></a>标识和访问控制

[有关详细信息，请参阅 *Azure 安全基线：* 标识和访问控制](../security/benchmarks/security-control-identity-access-control.md)。

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1：维护管理帐户的清单

**指导**：Azure Active Directory (Azure AD) 具有必须显式分配且可进行查询的内置角色。 使用 Azure AD PowerShell 模块执行即席查询，以发现属于管理组成员的帐户。

- [如何使用 PowerShell 获取 Azure AD 中的目录角色](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0&amp;preserve-view=true)

- [如何使用 PowerShell 获取 Azure AD 中目录角色的成员](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0&amp;preserve-view=true)

**责任**：客户

Azure 安全中心监视：无

### <a name="33-use-dedicated-administrative-accounts"></a>3.3：使用专用管理帐户

**指导**：WAF 中不可进行本地管理员分配。 但是，如果环境可以允许对 Azure WAF 资源进行管理控制，则该环境中可以有 Azure Active Directory (Azure AD) 管理员角色。
使用可以访问 Azure Web 应用程序防火墙 (WAF) 实例的专用管理帐户时，最好创建标准操作过程。 使用安全中心的标识和访问管理功能来监视管理帐户的数量。

- [了解 Azure 安全中心标识和访问](../security-center/security-center-identity-access.md)

- [了解如何在 Azure Database for PostgreSQL 中创建管理员用户](https://docs.microsoft.com/azure/postgresql/howto-create-users#the-server-admin-account)

- [如何使用 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5：对所有基于 Azure Active Directory 的访问使用多重身份验证

指导：启用 Azure Active Directory (Azure AD) 多重身份验证，并遵循安全中心身份和访问管理的建议。

- [如何在 Azure 中启用多重身份验证](../active-directory/authentication/howto-mfa-getstarted.md)

- [如何在 Azure 安全中心监视标识和访问](../security-center/security-center-identity-access.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6：对所有管理任务使用专用计算机（特权访问工作站）

指导：使用配置了多重身份验证的特权访问工作站 (PAW) 来登录和配置 Azure Web 应用程序防火墙 (WAF) 和相关资源。

- [了解特权访问工作站](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [如何在 Azure 中启用多重身份验证](../active-directory/authentication/howto-mfa-getstarted.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7：记录来自管理帐户的可疑活动并对其发出警报

**指导**：当环境中出现可疑或不安全的活动时，请使用 Azure Active Directory (Azure AD) 安全报告来生成日志和警报。 使用安全中心监视标识和访问活动。

- [如何确定标记为存在风险活动的 Azure AD 用户](../active-directory/identity-protection/overview-identity-protection.md)

- [如何在 Azure 安全中心内监视用户的标识和访问活动](../security-center/security-center-identity-access.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8：仅从批准的位置管理 Azure 资源

**指导**：配置条件访问策略的位置条件，并管理你的命名位置。

使用命名位置创建 IP 地址范围、国家和地区的逻辑分组。 限制仅限你配置的命名位置可访问 Azure Key Vault 机密等敏感资源。

- [Azure Active Directory (Azure AD) 条件访问中的位置条件是什么](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="39-use-azure-active-directory"></a>3.9：使用 Azure Active Directory

**指导**：使用 Azure Active Directory (Azure AD) 作为中心身份验证和授权系统。 Azure AD 通过对静态数据和传输中数据使用强加密以及加盐、哈希处理和安全存储用户凭据来保护数据。
- [如何创建和配置 Azure AD 实例](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10：定期审查和协调用户访问

**指导**：Azure Active Directory (Azure AD) 提供日志来帮助发现过时的帐户。 使用 Azure 标识访问评审来高效地管理组成员身份、对企业应用程序的访问和角色分配。 定期查看用户的访问权限，确保只有活动用户持续拥有访问权限。

- [了解 Azure AD 报告](/azure/active-directory/reports-monitoring)

- [如何使用 Azure 标识访问评审](../active-directory/governance/access-reviews-overview.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11：监视尝试访问已停用凭据的行为

**指导**：将 Azure Active Directory (Azure AD) 登录活动、审核和风险事件日志源与任何 SIEM 或 Azure Sentinel 等监视工具集成。

可通过为 Azure AD 用户帐户创建诊断设置，并将审核日志和登录日志发送到 Log Analytics 工作区，来简化此过程。 在 Log Analytics 工作区中配置所需的警报。

- [如何将 Azure 活动日志集成到 Azure Monitor](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**责任**：客户

Azure 安全中心监视：无

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12：针对帐户登录行为偏差发出警报

指导：使用 Azure Active Directory (Azure AD) 的风险和标识保护功能配置对检测到的与用户标识相关的可疑操作的自动响应。 将数据引入 Azure Sentinel 以便进一步调查。

- [如何查看 Azure AD 风险登录](../active-directory/identity-protection/overview-identity-protection.md)

- [如何配置和启用标识保护风险策略](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [如何加入 Azure Sentinel](../sentinel/quickstart-onboard.md)

**责任**：客户

Azure 安全中心监视：无

## <a name="data-protection"></a>数据保护

[有关详细信息，请参阅 *Azure 安全基线：* 数据保护](../security/benchmarks/security-control-data-protection.md)。

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1：维护敏感信息的清单

**指导**：使用标记可帮助跟踪存储或处理敏感信息的 Azure Web 应用程序防火墙 (WAF) 和相关资源。

- [如何创建和使用标记](../azure-resource-manager/management/tag-resources.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2：隔离存储或处理敏感信息的系统

**指导**：使用单独的订阅和管理组对各个安全域（例如，开发、测试和生产环境等环境类型和数据敏感度级别）实施隔离。 

通过 Azure Active Directory (Azure AD) 基于角色的访问控制 (Azure RBAC) 控制对 Azure 资源的访问。

- [如何创建其他 Azure 订阅](../cost-management-billing/manage/create-subscription.md)

- [如何创建管理组](../governance/management-groups/create-management-group-portal.md)

- [如何创建和使用标记](../azure-resource-manager/management/tag-resources.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4：加密传输中的所有敏感信息

**指导**：加密传输中的所有敏感信息。 确保连接到 Azure Web 应用程序防火墙 (WAF) 实例和相关资源的任何客户端能够协商 TLS 1.2 或更高版本。

请按照安全中心的建议进行静态加密和传输中加密（如果适用）。

- [了解 Azure 传输中的加密](https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit)

**责任**：共享

Azure 安全中心监视：无

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4.6：使用 Azure RBAC 管理对资源的访问 

指导：使用 Azure 基于角色的访问控制 (Azure RBAC) 控制对 Azure 资源（如 Web 应用程序防火墙）的访问。

- [如何在 Azure 中配置 Azure RBAC](../role-based-access-control/role-assignments-portal.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8：加密静态的敏感信息

**指导**：对 Azure Web 应用程序防火墙 (WAF) 及相关资源在内的所有 Azure 资源使用静态加密。 Microsoft 建议允许 Azure 管理加密密钥，但在某些情况下，你可以选择管理自己的密钥。

- [了解 Azure 中的静态加密](../security/fundamentals/encryption-atrest.md)

- [如何配置客户管理的加密密钥](https://docs.microsoft.com/azure/storage/common/customer-managed-keys-configure-key-vault?tabs=portal)

**责任**：客户

Azure 安全中心监视：无

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9：记录对关键 Azure 资源的更改并对此类更改发出警报

**指导**：将 Azure Web 应用程序防火墙 (WAF) 配置为在检测模式下为网络流量设置基线后在防护模式下运行预先确定的时间量。 

防护模式下，Azure WAF 阻止规则检测到的入侵和攻击。 攻击者会收到“403 未授权访问”异常，且连接会结束。 阻止模式会在 WAF 日志中记录此类攻击。

- [应用程序网关和 Azure 安全中心之间的集成概述](https://docs.microsoft.com/azure/security-center/security-center-partner-integration#overview)

- [应用程序网关上的 WAF 模式](https://docs.microsoft.com/azure/web-application-firewall/ag/ag-overview#waf-modes)

- [Front Door 上的 WAF 模式](https://docs.microsoft.com/azure/web-application-firewall/afds/afds-overview#waf-modes)

**责任**：客户

Azure 安全中心监视：无

## <a name="inventory-and-asset-management"></a>清单和资产管理

[有关详细信息，请参阅 *Azure 安全基线：* 清单和资产管理](../security/benchmarks/security-control-inventory-asset-management.md)。

### <a name="61-use-automated-asset-discovery-solution"></a>6.1：使用自动化资产发现解决方案

**指导**：使用 Azure Resource Graph 来查询或发现订阅中的所有资源（例如计算、存储、网络、端口和协议等）。

确保你在租户中拥有适当（读取）权限，并且能够枚举所有 Azure 订阅以及订阅中的资源。 尽管可以通过 Resource Graph 发现经典 Azure 资源，但我们强烈建议你今后还是创建并使用 Azure 资源管理器资源。

- [如何使用 Azure Resource Graph 创建查询](../governance/resource-graph/first-query-portal.md)

- [如何查看 Azure 订阅](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-4.8.0&amp;preserve-view=true)

- [了解 Azure RBAC](../role-based-access-control/overview.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="62-maintain-asset-metadata"></a>6.2：维护资产元数据

**指导**：对 Azure Web 应用程序防火墙 (WAF) 策略使用标记。 标记可以与资源相关联，并且有逻辑地用于在客户订阅中整理对这些资源的访问。 

- [如何创建和使用标记](../azure-resource-manager/management/tag-resources.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="63-delete-unauthorized-azure-resources"></a>6.3：删除未经授权的 Azure 资源

**指导**：使用标记、管理组和单独订阅（如果适用）来整理和跟踪 Azure WAF 及相关资源。 定期核对清单，确保及时地从订阅中删除未经授权的资源。

- [如何创建其他 Azure 订阅](../cost-management-billing/manage/create-subscription.md)

- [如何创建管理组](../governance/management-groups/create-management-group-portal.md)

- [如何创建和使用标记](../azure-resource-manager/management/tag-resources.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4：定义并维护已批准的 Azure 资源的清单

**指导**：根据组织需求创建已批准资源（包括配置）的清单。

使用 Azure Policy 对可以在订阅中创建的资源类型施加限制。 使用 Azure Resource Graph 查询和发现订阅中的资源。 确保环境中存在的所有 Azure 资源已获得批准。

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [如何使用 Azure Resource Graph 创建查询](../governance/resource-graph/first-query-portal.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5：监视未批准的 Azure 资源

**指导**：使用 Azure Policy 对可以在订阅中创建的资源类型施加限制。
使用 Azure Resource Graph 来查询或发现其订阅中 Azure Web 应用程序防火墙 (WAF) 资源。 确保环境中的所有 Azure WAF 和相关资源均已获得批准。

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [如何使用 Azure Graph 创建查询](../governance/resource-graph/first-query-portal.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7：删除未批准的 Azure 资源和软件应用程序

**指导**：使用 Azure Policy 来监视和删除未经批准的 Azure WAF 资源以拒绝部署 Azure WAF 或特定类型的 WAF，例如 Azure WAF v1 vs V2。

**责任**：客户

Azure 安全中心监视：无

### <a name="69-use-only-approved-azure-services"></a>6.9：仅使用已批准的 Azure 服务

**指导**：使用 Azure Policy 限制可在环境中预配的服务。

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [如何使用 Azure Policy 拒绝特定的资源类型](/azure/governance/policy/samples/built-in-policies#general)

**责任**：客户

Azure 安全中心监视：无

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11：限制用户与 Azure 资源管理器进行交互的能力

**指导**：通过为“Microsoft Azure 管理”应用配置“阻止访问”，使用 Azure 条件访问来限制用户与 Azure 资源管理器交互的能力。

- [如何配置条件访问来阻止对 Azure 资源管理器的访问](../role-based-access-control/conditional-access-azure-management.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13：以物理或逻辑方式隔离高风险应用程序

**指导**：Azure Web 应用程序防火墙 (WAF) 可以通过网络、资源组或订阅与不同环境相关联，以便将高风险应用程序隔离开来。

- [Azure 虚拟网络概述](../virtual-network/virtual-networks-overview.md)

- [使用 Azure 管理组来组织资源](../governance/management-groups/overview.md)

- [订阅决策指南](/azure/cloud-adoption-framework/decision-guides/subscriptions/)

**责任**：客户

Azure 安全中心监视：无

## <a name="secure-configuration"></a>安全配置

[有关详细信息，请参阅 *Azure 安全基线：* 安全配置](../security/benchmarks/security-control-secure-configuration.md)。

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1：为所有 Azure 资源建立安全配置

**指导**：定义和实现与 Azure Web 应用程序防火墙 (WAF) 部署相关的网络设置的标准安全配置。

在“Microsoft.Network”命名空间中使用 Azure Policy 别名创建自定义策略，以审核或强制实施 Azure 应用程序网关、虚拟网络和网络安全组的网络配置并使用内置策略定义。

- [如何查看可用的 Azure Policy 别名](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&amp;preserve-view=true)

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3：维护安全的 Azure 资源配置

**指导**：使用 Azure Policy 来拒绝效果（如果效果不存在则部署效果），从而在 Azure Web 应用程序防火墙 (WAF) 和相关资源中强制实施安全设置。 

使用 Azure 资源管理器模板来维护组织所需的 Azure WAF 和相关资源的安全配置。

- [了解 Azure Policy 效果](../governance/policy/concepts/effects.md)

- [创建和管理策略以强制实施符合性](../governance/policy/tutorials/create-and-manage.md)

- [Azure 资源管理器模板概述](../azure-resource-manager/templates/overview.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5：安全存储 Azure 资源的配置

**指导**：使用 Azure DevOps 安全地存储和管理代码，如自定义 Azure 策略和 Azure 资源管理器模板。

向特定用户、内置安全组或者 Azure Active Directory (Azure AD)（如果与 Azure DevOps 集成）或 Azure AD（如果与 Team Foundation Server (TFS) 集成）中定义的组授予权限，或拒绝向其授予权限。

- [如何在 Azure DevOps 中存储代码](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops&amp;preserve-view=true)

- [关于 Azure DevOps 中的权限和组](/azure/devops/organizations/security/about-permissions)

**责任**：客户

Azure 安全中心监视：无

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7：部署 Azure 资源的配置管理工具

**指导**：在“Microsoft.Network”命名空间中使用内置的 Azure Policy 定义和 Azure Policy 别名创建自定义策略，以审核、强制实施系统配置并为其发出警报。 开发一个用于管理策略例外的流程和管道。

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy 文档](/azure/governance/policy)

**责任**：客户

Azure 安全中心监视：无

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9：为 Azure 资源实施自动配置监视

**指导**：在“Microsoft.Network”命名空间中使用内置的 Azure Policy 定义和 Azure Policy 别名创建自定义策略，以审核、强制实施系统配置并为其发出警报。 

使用 Azure Policy 的“[审核]”、“[拒绝]”和“[不存在则部署]”效果自动强制实施 Azure 资源的配置。

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy 文档](/azure/governance/policy)

**责任**：客户

Azure 安全中心监视：无

### <a name="711-manage-azure-secrets-securely"></a>7.11：安全管理 Azure 机密

**指导**：使用 Azure Key Vault 安全地存储证书。 Azure Key Vault 是由平台管理的机密存储，可以用来保证机密、密钥和 SSL 证书的安全。 

Azure 应用程序网关支持与密钥保管库集成，以存储附加到支持 HTTPS 的侦听器的服务器证书。 

- [如何使用 Azure PowerShell 通过 Key Vault 证书配置 SSL 终止](../application-gateway/configure-keyvault-ps.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13：消除意外的凭据透露

**指导**：实现凭据扫描程序来识别代码中的凭据，这还会鼓励将发现的凭据迁移到更安全的位置，例如 Azure Key Vault。
- [如何设置凭据扫描程序](https://secdevtools.azurewebsites.net/helpcredscan.html)

**责任**：客户

Azure 安全中心监视：无

## <a name="data-recovery"></a>数据恢复

[有关详细信息，请参阅 *Azure 安全基线：* 数据恢复](../security/benchmarks/security-control-data-recovery.md)。

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4：确保保护备份和客户管理的密钥

**指导**：请确保为 Azure Key Vault 启用软删除。 软删除允许恢复已删除的密钥保管库和保管库对象，例如密钥、机密和证书。

- [如何使用 Azure Key Vault 软删除](https://docs.microsoft.com/azure/key-vault/general/key-vault-recovery?tabs=azure-powershell&amp;preserve-view=true)

**责任**：客户

Azure 安全中心监视：无

## <a name="incident-response"></a>事件响应

[有关详细信息，请参阅 *Azure 安全基线：* 事件响应](../security/benchmarks/security-control-incident-response.md)。

### <a name="101-create-an-incident-response-guide"></a>10.1：创建事件响应指导

**指导**：为组织制定事件响应指南。 确保在书面的事件响应计划中定义人员职责，以及事件处理和管理从检测到事件后审查的各个阶段。 

- [关于建立自己的安全事件响应流程的指南](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/) 

- [Microsoft 安全响应中心事件分析](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/) 

- [使用 NIST 的“计算机安全事件处理指南”，帮助制定自己的事件响应计划](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**责任**：客户

Azure 安全中心监视：无

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2：创建事件评分和优先级设定过程

**指导**：安全中心向每个警报分配严重性，帮助优先处理应首先调查的警报。 严重性取决于安全中心对调查结果或用于发出警报的指标的置信度，以及对导致警报的活动背后存在恶意意图的置信度级别。

清楚地标记订阅（例如生产、非生产），并创建命名系统来对 Azure 资源进行明确标识和分类。

**责任**：客户

Azure 安全中心监视：无

### <a name="103-test-security-response-procedures"></a>10.3：测试安全响应过程

**指导**：定期执行演练来测试系统的事件响应功能。 识别弱点和差距，并根据需要修改计划。
- [请参阅 NIST 的刊物：IT 规划和功能的测试、培训与演练计划指南](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**责任**：客户

Azure 安全中心监视：无

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4：提供安全事件联系人详细信息，并针对安全事件配置警报通知

**指南**：如果 Microsoft 安全响应中心 (MSRC) 发现非法或未经授权的某方访问了客户的数据，Microsoft 将使用安全事件联系人信息与你取得联系。 事后审查事件，确保问题得到解决。
- [如何设置 Azure 安全中心安全联系人](../security-center/security-center-provide-security-contact-details.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5：将安全警报整合到事件响应系统中

**指导**：使用连续导出功能导出安全中心警报和建议。 使用连续导出可以手动导出或者持续导出警报和建议。 根据组织要求使用 Azure 安全中心数据连接器将警报流式传输到 Azure Sentinel。

- [如何配置连续导出](../security-center/continuous-export.md)

- [如何将警报流式传输到 Azure Sentinel](../sentinel/connect-azure-security-center.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="106-automate-the-response-to-security-alerts"></a>10.6：自动响应安全警报

**指导**：使用安全中心内的工作流自动化功能可以通过“逻辑应用”针对安全警报和建议自动触发响应。
- [如何配置工作流自动化和逻辑应用](../security-center/workflow-automation.md)

**责任**：客户

Azure 安全中心监视：无

## <a name="penetration-tests-and-red-team-exercises"></a>渗透测试和红队练习

[有关详细信息，请参阅 *Azure 安全基线：* 渗透测试和红队演练](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)。

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1：定期对 Azure 资源执行渗透测试，确保修正所有发现的关键安全问题

**指导**：请遵循 Microsoft 互动规则，确保你的渗透测试不违反 Microsoft 政策。 使用 Microsoft 红队演练策略和执行，以及针对 Microsoft 托管云基础结构、服务和应用程序执行的现场渗透测试。 

- [参与的渗透测试规则](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft 云红色组合](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**责任**：共享

Azure 安全中心监视：无

## <a name="next-steps"></a>后续步骤

- 参阅 [Azure 安全基准 V2 概述](/azure/security/benchmarks/overview)
- 详细了解 [Azure 安全基线](/azure/security/benchmarks/security-baselines-overview)
