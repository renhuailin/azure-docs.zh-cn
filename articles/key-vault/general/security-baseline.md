---
title: Key Vault 的 Azure 安全基线
description: Key Vault 的 Azure 安全基线
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/10/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: ad47ac7d51de6ab497d7061961c14b5324fab931
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/14/2021
ms.locfileid: "98202447"
---
# <a name="azure-security-baseline-for-key-vault"></a>Key Vault 的 Azure 安全基线

Key Vault 的 Azure 安全基线包含可帮助你改善部署安全态势的建议。

此服务的基线摘自 [Azure 安全基准版本 1.0](../../security/benchmarks/overview.md)，其中提供了有关如何根据我们的最佳做法指导保护 Azure 上的云解决方案的建议。

有关详细信息，请参阅 [Azure 安全基线概述](../../security/benchmarks/security-baselines-overview.md)。

## <a name="network-security"></a>网络安全

有关详细信息，请参阅[安全控制：网络安全性](../../security/benchmarks/security-control-network-security.md)。

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1：在虚拟网络中使用网络安全组或 Azure 防火墙保护资源

**指导**：将 Azure Key Vault 与 Azure 专用链接集成。 

使用 Azure 专用链接服务，可通过虚拟网络中的专用终结点访问 Azure 服务（例如 Azure Key Vault）和 Azure 托管的客户服务/合作伙伴服务。

Azure 专用终结点是一个网络接口，可以通过私密且安全的方式将你连接到 Azure 专用链接支持的服务。 专用终结点使用 VNet 中的专用 IP 地址将服务有效接入 VNet 中。 发往服务的所有流量都可以通过专用终结点路由，因此不需要网关、NAT 设备、ExpressRoute 或 VPN 连接或公共 IP 地址。 虚拟网络与服务之间的流量将通过 Microsoft 主干网络，因此不会从公共 Internet 泄露。 可以连接到 Azure 资源的实例，从而获得最高级别的访问控制粒度。

如何将 Key Vault 与 Azure 专用链接集成：

https://docs.microsoft.com/azure/key-vault/private-link-service


**Azure 安全中心监视**：是

**责任**：客户

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2：监视和记录 VNet、子网和 NIC 的配置与流量

**指导**：在 Azure 安全中心，按照网络保护建议来帮助保护 Azure 中配置了 Key Vault 的资源。 

若要详细了解 Azure 安全中心提供的网络安全性： 

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

**Azure 安全中心监视**：是

**责任**：客户

### <a name="13-protect-critical-web-applications"></a>1.3：保护关键 Web 应用程序

**指导**：不适用；此建议适用于 Azure 应用服务或计算资源上运行的 Web 应用程序。


**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4：拒绝与已知恶意的 IP 地址进行通信

**指南**：在与 Key Vault 实例关联的 Azure 虚拟网络上启用 Azure DDoS 保护，以防范分布式拒绝服务攻击。 根据 Azure 安全中心集成的威胁情报进行判断，拒绝与已知恶意的或未使用过的 Internet IP 地址通信。

 
使用 Azure 门户管理 Azure DDoS 保护标准： https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Azure 安全中心内 Azure 服务层的威胁检测： https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer


**Azure 安全中心监视**：是

**责任**：客户

### <a name="15-record-network-packets-and-flow-logs"></a>1.5：记录网络数据包和流日志

**指导**：Azure Key Vault 不使用网络安全组 (NSG)，也不会捕获 Azure Key Vault 的流日志。 相反，使用 Azure 专用链接来保护 Azure Key Vault 资源，并启用诊断设置来记录指标和审核事件。

将 Key Vault 与 Azure Private Link 集成：

https://docs.microsoft.com/azure/key-vault/private-link-service

Azure Key Vault 日志记录： https://docs.microsoft.com/azure/key-vault/key-vault-logging



**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6：部署基于网络的入侵检测/入侵防护系统 (IDS/IPS)

**指导**：可通过为 Azure Key Vault 配置高级威胁防护来满足此要求。 ATP 提供额外一层安全情报。 此工具可检测访问或利用 Azure Key Vault 帐户的潜在有害的尝试。

Azure 安全中心检测到异常活动时会显示警报。 它还会向订阅管理员发送电子邮件，提供可疑活动的详细信息，以及如何调查和修正已识别威胁的建议。

为 Azure Key Vault 设置高级威胁防护：

https://docs.microsoft.com/azure/security-center/advanced-threat-protection-key-vault



**Azure 安全中心监视**：是

**责任**：客户

### <a name="17-manage-traffic-to-web-applications"></a>1.7：管理发往 Web 应用程序的流量

**指导**：不适用；此建议适用于 Azure 应用服务或计算资源上运行的 Web 应用程序。


**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8：最大程度地降低网络安全规则的复杂性和管理开销

**指导**：对于需要访问 Azure Key Vault 实例的资源，请对 Azure Key Vault 使用 Azure 服务标记来定义网络安全组或 Azure 防火墙上的网络访问控制。 创建安全规则时，可以使用服务标记代替特定的 IP 地址。 在规则的相应源或目标字段中指定服务标记名称（例如 ApiManagement），可以允许或拒绝相应服务的流量。 Microsoft 会管理服务标记包含的地址前缀，并会在地址发生更改时自动更新服务标记。

Azure 服务标记概述： https://docs.microsoft.com/azure/virtual-network/service-tags-overview


**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9：维护网络设备的标准安全配置

**指导**：使用 Azure Policy 为与 Azure Key Vault 实例关联的网络资源定义和实施标准安全配置。 在“Microsoft.KeyVault”和“Microsoft.Network”命名空间中使用 Azure Policy 别名创建自定义策略，以审核或强制实施 Azure Key Vault 实例的网络配置。 还可使用与 Azure Key Vault 相关的内置策略定义，例如：

Key Vault 应使用虚拟网络服务终结点

教程：创建和管理策略以强制实施合规性：

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure Policy 示例： 

https://docs.microsoft.com/azure/governance/policy/samples/#networ

快速入门：在门户中定义和分配蓝图：

https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal


**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="110-document-traffic-configuration-rules"></a>1.10：阐述流量配置规则

**指导**：对与 Azure Key Vault 实例的网络安全性和流量流相关的资源使用标记，以提供元数据和逻辑组织。

使用标记相关的任何内置 Azure Policy 定义（例如“需要标记及其值”）来确保使用标记创建所有资源，并在有现有资源不带标记时发出通知。

可以使用 Azure PowerShell 或 Azure CLI 根据资源的标记查找资源或对其执行操作。

使用标记来整理 Azure 资源：

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags


**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11：使用自动化工具来监视网络资源配置和检测更改

**指导**：使用 Azure 活动日志监视网络资源配置，并检测与 Azure Key Vault 实例相关的网络资源的更改。 在 Azure Monitor 中创建当关键网络资源发生更改时触发的警报。

查看和检索 Azure 活动日志事件：

https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

使用 Azure Monitor 创建、查看和管理活动日志警报：

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log


**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="logging-and-monitoring"></a>日志记录和监视

有关详细信息，请参阅[安全控制：日志记录和监视](../../security/benchmarks/security-control-logging-monitoring.md)。

### <a name="21-use-approved-time-synchronization-sources"></a>2.1：使用批准的时间同步源

**指导**：不适用；Microsoft 会维护 Azure Key Vault 等 Azure 资源的时间源用于日志中的时间戳。


**Azure 安全中心监视**：不适用

**责任**：Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2：配置中心安全日志管理

**指导**：通过 Azure Monitor 引入日志来聚合 Azure Key Vault 生成的安全数据。 在 Azure Monitor 中，使用 Azure Log Analytics 工作区来查询和执行分析，并使用 Azure 存储帐户进行长期/存档存储。 或者，可以启用将数据加入 Azure Sentinel 或第三方 SIEM 的功能。 

Azure Key Vault 日志记录：

https://docs.microsoft.com/azure/key-vault/key-vault-logging

快速入门：如何板载 Azure Sentinel：

https://docs.microsoft.com/azure/sentinel/quickstart-onboard


**Azure 安全中心监视**：是

**责任**：客户

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：为 Azure 资源启用审核日志记录

**指导**：在 Azure Key Vault 实例上启用诊断设置，来访问审核、安全和诊断日志。 活动日志自动可用，包括事件源、日期、用户、时间戳、源地址、目标地址和其他有用元素。

Azure Key Vault 日志记录：

https://docs.microsoft.com/azure/key-vault/key-vault-logging


**Azure 安全中心监视**：是

**责任**：客户

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4：从操作系统收集安全日志

**指南**：不适用；此建议适用于计算资源。


**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="25-configure-security-log-storage-retention"></a>2.5：配置安全日志存储保留期

**指导**：在 Azure Monitor 中，对于用于保存 Azure Key Vault 日志的 Log Analytics 工作区，根据组织的合规性法规设置保持期。 使用 Azure 存储帐户进行长期/存档存储。

更改数据保持期： https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period


**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="26-monitor-and-review-logs"></a>2.6：监视和审查日志

**指导**：分析和监视日志中的异常行为，定期审查 Azure Key Vault 保护的资源的结果。 使用 Azure Monitor 的 Log Analytics 工作区查看日志并对日志数据执行查询。 或者，可以启用数据并将其加入 Azure Sentinel 或第三方 SIEM。 

快速入门：板载 Azure Sentinel：

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Azure Monitor 中的 Log Analytics 入门：

https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Azure Monitor 中的日志查询入门：

https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries


**Azure 安全中心监视**：是

**责任**：客户

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7：针对异常活动启用警报

**指导**：在 Azure 安全中心，为 Key Vault 启用高级威胁防护 (ATP)。 在 Azure Key Vault 中启用诊断设置，并将日志发送到 Log Analytics 工作区。 将 Log Analytics 工作区加入 Azure Sentinel，因为它提供了安全业务流程自动化响应 (SOAR) 解决方案。 这样便可以创建 playbook（自动解决方案）并用于修正安全问题。

快速入门：板载 Azure Sentinel：

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

在 Azure 安全中心内管理和响应安全警报：

https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts

借助 Azure Monitor 警报对事件做出响应：

https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response


**Azure 安全中心监视**：是

**责任**：客户

### <a name="28-centralize-anti-malware-logging"></a>2.8：集中管理反恶意软件日志记录

**指导**：不适用；Azure Key Vault 既不处理也不生成与反恶意软件相关的日志。


**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="29-enable-dns-query-logging"></a>2.9：启用 DNS 查询日志记录

**指导**：不适用；Azure Key Vault 既不处理也不生成与 DNS 相关的日志。


**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="210-enable-command-line-audit-logging"></a>2.10：启用命令行审核日志记录

**指南**：不适用；此建议适用于计算资源。


**Azure 安全中心监视**：不适用

**责任**：不适用

## <a name="identity-and-access-control"></a>标识和访问控制

有关详细信息，请参阅[安全控制：标识和访问控制](../../security/benchmarks/security-control-identity-access-control.md)。

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1：维护管理帐户的清单

**指导**：维护注册 Azure Active Directory 的应用程序的清单，以及有权访问你的 Azure Key Vault 密钥、机密和证书的所有用户帐户。 可使用 Azure 门户或 PowerShell 来查询和协调 Key Vault 访问。 若要在 PowerShell中查看访问权限，请使用以下命令：

(Get-AzResource -ResourceId [KeyVaultResourceID]).Properties.AccessPolicies

将应用程序注册到 Azure Active Directory：

https://docs.microsoft.com/azure/key-vault/key-vault-manage-with-cli2#registering-an-application-with-azure-active-directory

保护对密钥保管库的访问：

https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault


**Azure 安全中心监视**：是

**责任**：客户

### <a name="32-change-default-passwords-where-applicable"></a>3.2：在适用的情况下更改默认密码

**指导**：不适用；Azure Key Vault 没有默认密码的概念，因为身份验证由 Active Directory 提供，由 Azure 基于角色的访问控制 (Azure RBAC) 进行保护。


**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="33-use-dedicated-administrative-accounts"></a>3.3：使用专用管理帐户

**指导**：围绕可访问 Azure Key Vault 实例的专用管理帐户的使用，创建标准操作过程。 使用 Azure 安全中心标识和访问管理（当前为预览版）监视活动管理帐户的数量。

监视标识和访问（预览）：

https://docs.microsoft.com/azure/security-center/security-center-identity-access


**Azure 安全中心监视**：是

**责任**：客户

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4：将单一登录 (SSO) 与 Azure Active Directory 配合使用

**指导**：将 Azure 服务主体与 AppId、TenantID 和 ClientSecret 一起使用，从而无缝验证你的身份验证，并检索将用于访问你的 Azure Key Vault 机密的令牌。

使用 .NET 向 Azure Key Vault 进行服务到服务身份验证：

https://docs.microsoft.com/azure/key-vault/service-to-service-authentication



**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5：对所有基于 Azure Active Directory 的访问使用多重身份验证

**指导**：启用 Azure Active Directory 多重身份验证，并遵循 Azure 安全中心标识和访问管理（当前为预览版）建议，来保护已启用事件中心的资源。

规划基于云的 Azure AD 多重身份验证部署：

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

监视标识和访问（预览）：

https://docs.microsoft.com/azure/security-center/security-center-identity-access


**Azure 安全中心监视**：是

**责任**：客户

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6：对所有管理任务使用专用计算机（特权访问工作站）

**指导**：使用配置了 Azure AD 多重身份验证 (MFA) 的特权访问工作站 (PAW) 来登录和配置已启用 Key Vault 的资源。 

特权访问工作站： https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/ 

规划基于云的 Azure AD 多重身份验证部署： https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted



**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7：记录来自管理帐户的可疑活动并对其发出警报

**指导**：当环境中出现可疑或不安全的活动时，可使用 Azure Active Directory (Azure AD) Privileged Identity Management (PIM) 生成日志和警报。 使用 Azure AD 风险检测查看有关风险用户行为的警报和报告。 如需其他日志记录，请将 Azure 安全中心风险检测警报发送到 Azure Monitor 中，并使用操作组配置自定义警报/通知。

为 Azure Key Vault 启用高级威胁防护 (ATP)，来针对可疑活动生成警报。

部署 Azure AD Privileged Identity Management (PIM)： https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan

为 Azure Key Vault 设置高级威胁防护（预览）： https://docs.microsoft.com/azure/security-center/advanced-threat-protection-key-vault

Azure Key Vault 的警报（预览）： https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-azurekv

Azure Active Directory 风险检测： https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

在 Azure 门户中创建和管理操作组： https://docs.microsoft.com/azure/azure-monitor/platform/action-groups



**Azure 安全中心监视**：是

**责任**：客户

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8：仅从批准的位置管理 Azure 资源

**指导**：配置条件访问策略的位置条件，并管理你的命名位置。 使用命名位置可以创建 IP 地址范围或者国家和地区的逻辑分组。 可限制仅限你配置的命名位置可访问 Key Vault 机密等敏感资源。

Azure Active Directory 条件访问中的位置条件是什么？： https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations



**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="39-use-azure-active-directory"></a>3.9：使用 Azure Active Directory

**指导**：使用 Azure Active Directory (Azure AD) 作为 Key Vault 等 Azure 资源的中心身份验证和授权系统。 这样，就可使用 Azure 基于角色的访问控制 (Azure RBAC) 来管理敏感资源。

 

快速入门：在 Azure Active Directory 中创建新租户：

https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant


**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10：定期审查和协调用户访问

**指导**：查看 Azure Active Directory (Azure AD) 日志，帮助发现具有 Azure Key Vault 管理角色的过时帐户。 此外，使用 Azure AD 访问评审来高效地管理组成员身份、角色分配，以及对可用于访问 Azure Key Vault 的企业应用程序的访问权限。 应定期（例如每 90 天一次）评审用户访问权限，以确保正确用户持续拥有访问权限。

“Azure Active Directory 报表和监视”文档：

https://docs.microsoft.com/azure/active-directory/reports-monitoring/

什么是 Azure AD 访问评审？：

https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview


**Azure 安全中心监视**：是

**责任**：客户

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11：监视尝试访问已停用帐户的行为

**指导**：为 Azure Key Vault 和 Azure Active Directory 启用诊断设置，将所有日志都发送到 Log Analytics 工作区。 在 Log Analytics 中配置所需警报（例如尝试访问禁用的机密）。

将 Azure AD 日志与 Azure Monitor 日志集成： https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

从旧 Key Vault 解决方案迁移： https://docs.microsoft.com/azure/azure-monitor/insights/azure-key-vault#migrating-from-the-old-key-vault-solution



**Azure 安全中心监视**：是

**责任**：客户

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12：针对帐户登录行为偏差发出警报

**指南**：使用 Azure Active Directory 的标识保护和风险检测功能来配置对检测到的与 Azure Key Vault 受保护资源相关的可疑操作的自动响应。 应通过 Azure Sentinel 启用自动响应，以实现组织的安全响应。 

Azure Active Directory 门户中的 "有风险的登录" 报表： https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins 

如何：配置和启用风险策略： https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

如何载入 Azure Sentinel：  https://docs.microsoft.com/azure/sentinel/quickstart-onboard


**Azure 安全中心监视**：是

**责任**：客户

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13：在支持场合下为 Microsoft 提供对相关客户数据的访问权限

**指导**：不适用；Azure Key Vault 不支持客户密码箱。

正式版中支持的服务和方案： https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability



**Azure 安全中心监视**：不适用

**责任**：不适用

## <a name="data-protection"></a>数据保护

有关详细信息，请参阅[安全控制：数据保护](../../security/benchmarks/security-control-data-protection.md)。

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1：维护敏感信息的清单

**指导**：有些资源用于存储或处理已启用 Azure Key Vault 的资源上的敏感信息，可使用标记来跟踪这些资源。 

使用标记整理 Azure 资源： https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags



**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2：隔离存储或处理敏感信息的系统

**指导**：可使用虚拟网络服务终结点安全地访问 Azure Key Vault，其中这些终结点根据配置会限制对特定你子网的访问。

防火墙规则生效后，只有当你的请求来自允许的子网或 IP 地址范围时，你才能执行 Azure Key Vault 数据平面操作。 在 Azure 门户中访问 Azure Key Vault 时，这同样适用。 虽然可从 Azure 门户浏览到 Key Vault，但如果你的客户端计算机不在允许列表中，那么你可能无法列出密钥、机密或证书。 这也会影响其他 Azure Key Vault 选取器和其他 Azure 服务。 如果防火墙规则阻止你的客户端计算机这样做，那么你可能能够查看 Key Vault 列表，但不能查看列表密钥。

配置 Azure Key Vault 防火墙和虚拟网络： https://docs.microsoft.com/azure/key-vault/key-vault-network-security

Azure Key Vault 的虚拟网络服务终结点： https://docs.microsoft.com/azure/key-vault/key-vault-overview-vnet-service-endpoints



**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3：监视和阻止未经授权的敏感信息传输

**指导**：Azure Key Vault 中存储的所有数据都被视为敏感数据。 使用 Azure Key Vault 数据平面访问控制来控制对 Azure Key Vault 机密的访问。 你还可使用 Key Vault 的内置防火墙，在网络层控制访问。 若要监视对 Azure Key Vault 的访问，请启用 Key Vault 诊断设置，并将日志发送到 Azure 存储帐户或 Log Analytics 工作区。

保护对密钥保管库的访问： https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault

配置 Azure Key Vault 防火墙和虚拟网络： https://docs.microsoft.com/azure/key-vault/key-vault-network-security

Azure Key Vault 日志记录： https://docs.microsoft.com/azure/key-vault/key-vault-logging



**Azure 安全中心监视**：是

**责任**：客户

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4：加密传输中的所有敏感信息

**指导**：所有流向 Azure Key Vault 来进行身份验证、管理和数据平面访问的流量都会被加密并通过 HTTPS（端口 443）传递。 （但是对于 CRL，偶尔会有 HTTP [端口 80] 流量。）Azure Key Vault 继续允许引入 TLS 1.1 和 TLS 1.0 数据。 通过在客户端配置，可将数据限制为 TLS 1.2。



访问防火墙后面的 Azure Key Vault： https://docs.microsoft.com/azure/key-vault/key-vault-access-behind-firewall



**Azure 安全中心监视**：不适用

**责任**：Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5：使用有效的发现工具识别敏感数据

**指导**：不适用；Azure Key Vault （机密、密钥和证书）中的所有数据都被视为敏感数据。


**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6：使用 Azure RBAC 控制对资源的访问

**指导**：保护对 Azure Key Vault 实例的管理和数据平面的访问。

保护对密钥保管库的访问：

https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault


**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7：使用基于主机的数据丢失防护来强制实施访问控制

**指导**：Microsoft 管理 Azure Key Vault 的底层基础结构，并实施了严格的控制措施来防止客户数据丢失或泄露。

什么是 Azure 密钥保管库？

https://docs.microsoft.com/azure/key-vault/key-vault-overview

Azure 客户数据保护：

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Azure 安全中心监视**：不适用

**责任**：Microsoft

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8：静态加密敏感信息

**指导**：所有托管对象（密钥、证书和机密）都在 Azure Key Vault 中静态加密。

支持性文档：

- [加密模型和密钥管理表](../../security/fundamentals/encryption-atrest.md)


**Azure 安全中心监视**：不适用

**责任**：Microsoft

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9：记录对关键 Azure 资源的更改并对此类更改发出警报

**指导**：在 Azure Monitor 中使用 Azure Key Vault 分析解决方案来查看 Azure Key Vault 审核事件日志。

Azure Monitor 中的 Azure Key Vault 分析解决方案：

https://docs.microsoft.com/azure/azure-monitor/insights/azure-key-vault



**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="vulnerability-management"></a>漏洞管理

有关详细信息，请参阅[安全控制：漏洞管理。](../../security/benchmarks/security-control-vulnerability-management.md)

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1：运行自动漏洞扫描工具

**指导**：Microsoft 对支持 Azure Key Vault 的基础系统执行漏洞管理。


**Azure 安全中心监视**：不适用

**责任**：Microsoft

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2：部署自动操作系统修补管理解决方案

**指导**：不适用；Microsoft 对支持 Key Vault 的基础系统执行补丁管理。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3：部署第三方自动软件修补管理解决方案

**指南**：不适用；此建议适用于计算资源。


**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4：比较连续进行的漏洞扫描

**指导**：Microsoft 对支持 Key Vault 的基础系统执行漏洞管理。

**Azure 安全中心监视**：不适用

**责任**：Microsoft

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5：使用风险评级过程来确定已发现漏洞的修正措施的优先级

**指导**：使用 Azure 安全中心提供的默认风险评级（安全功能分数）。

提高 Azure 安全中心内的安全功能分数：

https://docs.microsoft.com/azure/security-center/security-center-secure-score


**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="inventory-and-asset-management"></a>清单和资产管理

有关详细信息，请参阅[安全控制：清单和资产管理](../../security/benchmarks/security-control-inventory-asset-management.md)。

### <a name="61-use-azure-asset-discovery"></a>6.1：使用 Azure 资产发现

**指导**：使用 Azure Resource Graph 可查询和发现订阅中的所有资源（包括 Azure Key Vault 实例）。 确保你在租户中拥有适当（读取）权限，并且能够枚举所有 Azure 订阅以及订阅中的资源。

快速入门：使用 Azure Resource Graph 资源管理器运行你的第一个 Resource Graph 查询：

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

获取当前帐户可访问的订阅：

https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

什么是 Azure 基于角色的访问控制 (Azure RBAC)？

https://docs.microsoft.com/azure/role-based-access-control/overview

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="62-maintain-asset-metadata"></a>6.2：维护资产元数据

**指导**：将标记应用于 Azure Key Vault 资源，从而将元数据按逻辑组织到分类中。

如何创建和使用标记：

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="63-delete-unauthorized-azure-resources"></a>6.3：删除未经授权的 Azure 资源

**指导**：使用标记、管理组和单独订阅（若适用）来整理和跟踪 Azure Key Vault 实例及相关资源。 定期核对清单，确保及时地从订阅中删除未经授权的资源。

创建另外的 Azure 订阅：

https://docs.microsoft.com/azure/billing/billing-create-subscription

创建用来组织和管理资源的管理组：

https://docs.microsoft.com/azure/governance/management-groups/create

使用标记整理 Azure 资源： https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags


**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4：维护已批准的 Azure 资源和软件标题的清单

**指导**：定义已批准的 Azure 资源以及计算资源的已批准软件的列表

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5：监视未批准的 Azure 资源

**指导**：在 Azure Policy 中使用以下内置策略定义，对可在客户订阅中创建的资源类型施加限制：

- 不允许的资源类型

- 允许的资源类型

此外，请使用 Azure Resource Graph 来查询/发现订阅中的资源。

教程：创建和管理策略以强制实施符合性： https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

快速入门：使用 Azure Resource Graph Explorer 运行第一个 Resource Graph 查询： https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal



**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6：监视计算资源中未批准的软件应用程序

**指导**：不适用；此建议适用于计算资源。


**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7：删除未批准的 Azure 资源和软件应用程序

**指导**：不适用；此建议适用于 Azure 整体和计算资源。


**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="68-use-only-approved-applications"></a>6.8：仅使用已批准的应用程序

**指导**：不适用；此建议适用于计算资源。


**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="69-use-only-approved-azure-services"></a>6.9：仅使用已批准的 Azure 服务

**指导**：在 Azure Policy 中使用以下内置策略定义，对可在客户订阅中创建的资源类型施加限制：

- 不允许的资源类型

- 允许的资源类型

教程：创建和管理策略以强制实施符合性： https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure Policy 示例： https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types



**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="610-implement-approved-application-list"></a>6.10：实施已批准的应用程序列表

**指南**：不适用；此建议适用于计算资源。


**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="611-limit-users-ability-to-interact-with-azureresources-manager-via-scripts"></a>6.11：限制用户通过脚本与 Azure 资源管理器进行交互的功能

**指导**：使用 Azure 条件访问，通过为“Microsoft Azure 管理”应用配置“阻止访问”，限制用户与 Azure 资源管理器 (ARM) 进行交互的能力。 这可防止在高度安全的环境（例如使用 Key Vault 配置的环境）中创建和更改资源。

使用条件访问管理对 Azure 管理的访问权限： 

https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management


**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12：限制用户在计算资源中执行脚本的功能

**指导**：不适用；此建议适用于计算资源。


**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13：以物理或逻辑方式隔离高风险应用程序

**指导**：不适用；此建议适用于 Azure 应用服务或计算资源上运行的 Web 应用程序。


**Azure 安全中心监视**：不适用

**责任**：不适用

## <a name="secure-configuration"></a>安全配置

有关详细信息，请参阅[安全控制：安全配置](../../security/benchmarks/security-control-secure-configuration.md)。

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1：为所有 Azure 资源建立安全配置

**指导**：使用“Microsoft.KeyVault”命名空间中的 Azure Policy 别名创建自定义策略，以审核或强制实施 Azure Key Vault 实例的配置。 你还可使用内置的用于 Azure Key Vault 的 Azure Policy 定义，例如：

应可恢复 Key Vault 对象

将 Key Vault 的诊断设置部署到 Log Analytics 工作区

应启用 Key Vault 中的诊断日志

Key Vault 应使用虚拟网络服务终结点

将 Key Vault 的诊断设置部署到事件中心

使用来自 Azure 安全中心的建议作为 Azure Key Vault 资源的安全配置基线。

如何查看可用的 Azure Policy 别名：

https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

教程：创建和管理策略以强制实施合规性：

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage


**Azure 安全中心监视**：是

**责任**：客户

### <a name="72-establish-secure-operating-system-configurations"></a>7.2：建立安全的操作系统配置

**指南**：不适用；此建议适用于计算资源。


**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3：维护安全的 Azure 资源配置

**指导**：使用 Azure Policy“[拒绝]”和“[不存在则部署]”对已启用 Azure Key Vault 的资源强制实施安全设置。 

教程：创建和管理策略以强制实施合规性：

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage 

  
了解 Azure Policy 效果： 

https://docs.microsoft.com/azure/governance/policy/concepts/effects


**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4：维护安全的操作系统配置

**指南**：不适用；此建议适用于计算资源。


**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5：安全存储 Azure 资源的配置

**指南**：如果为已启用 Azure Key Vault 的资源使用自定义 Azure 策略定义，请使用 Azure Repos 安全地存储和管理你的代码。

如何在 Azure DevOps 中存储代码： 

https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops 

Azure Repos 文档： 

https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="76-securely-store-custom-operating-system-images"></a>7.6：安全存储自定义操作系统映像

**指南**：不适用；此建议适用于计算资源。


**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="77-deploy-system-configuration-management-tools"></a>7.7：部署系统配置管理工具

**指导**：使用“Microsoft.KeyVault”命名空间中的 Azure Policy 别名创建自定义策略，以审核、强制实施系统配置并设置相关警报。 另外，开发一个用于管理策略例外的流程和管道。

如何配置和管理 Azure Policy：

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8：为操作系统部署系统配置管理工具

**指南**：不适用；此建议适用于计算资源。


**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9：为 Azure 服务实施自动配置监视

**指导**：使用 Azure 安全中心对 Azure Key Vault 保护的资源执行基线扫描 

  

如何在 Azure 安全中心修正建议： 

https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations

**Azure 安全中心监视**：是

**责任**：客户

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10：为操作系统实施自动配置监视

**指南**：不适用；此基准适用于计算资源。


**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="711-manage-azure-secrets-securely"></a>7.11：安全管理 Azure 机密

**指导**：将托管服务标识与 Azure Key Vault 结合使用，以便简化和保护云应用程序的机密管理。 请确保已启用 Azure Key Vault 软删除。

如何与 Azure 托管标识集成： 

https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

如何创建 Key Vault： 

https://docs.microsoft.com/azure/key-vault/general/quick-create-portal

如何向 Key Vault 进行身份验证：

https://docs.microsoft.com/azure/key-vault/general/authentication

如何分配 Key Vault 访问策略： 

https://docs.microsoft.com/azure/key-vault/general/assign-access-policy-portal

**Azure 安全中心监视**：是

**责任**：客户

### <a name="712-manage-identities-securely-and-automatically"></a>7.12：安全自动管理标识

**指导**：将托管服务标识与 Azure Key Vault 结合使用，以便简化和保护云应用程序的机密管理。 

  

* [如何与 Azure 托管标识集成](../../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

* [如何创建 Key Vault](quick-create-portal.md)

* [如何向 Key Vault 进行身份验证](authentication.md)

* [如何分配 Key Vault 访问策略](assign-access-policy-portal.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13：消除意外的凭据透露

**指南**：实施凭据扫描程序来识别代码中的凭据。 凭据扫描程序还会建议将发现的凭据转移到更安全的位置，例如 Azure Key Vault。  
  
 如何设置凭据扫描器： https://secdevtools.azurewebsites.net/helpcredscan.html

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="malware-defense"></a>恶意软件防护

有关详细信息，请参阅[安全控制：恶意软件防护](../../security/benchmarks/security-control-malware-defense.md)。

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1：使用集中管理的反恶意软件

**指南**：不适用；此建议适用于计算资源。 Microsoft 会处理基础平台的反恶意软件。


**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2：预先扫描要上传到非计算 Azure 资源的文件

**指导**：Microsoft 反恶意软件已在支持 Azure 服务（例如 Azure Key Vault）的基础主机上启用，但它不会针对客户内容运行。

请预先扫描要上传或发送到非计算 Azure 资源（如 Azure Key Vault）的任何内容。 Microsoft 无法访问这些实例中的数据。

了解适用于 Azure 云服务和虚拟机的 Microsoft Antimalware： https://docs.microsoft.com/azure/security/fundamentals/antimalware

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>步骤 8.3：确保反恶意软件和签名已更新

**指南**：不适用；此建议适用于计算资源。 Microsoft 会处理基础平台的反恶意软件。


**Azure 安全中心监视**：不适用

**责任**：不适用

## <a name="data-recovery"></a>数据恢复

有关详细信息，请参阅[安全控制：数据恢复](../../security/benchmarks/security-control-data-recovery.md)。

### <a name="91-ensure-regular-automated-back-ups"></a>9.1：确保定期执行自动备份

**指导**：使用以下 PowerShell 命令确保定期自动备份 Key Vault 证书、密钥、托管存储帐户和机密：

- Backup-AzKeyVaultCertificate

- Backup-AzKeyVaultKey

- Backup-AzKeyVaultManagedStorageAccount

- Backup-AzKeyVaultSecret

或者，可将 Key Vault 备份存储在 Azure 备份中。

如何备份 Key Vault 证书： https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate

如何备份 Key Vault 密钥： https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey

如何备份 Key Vault 托管存储帐户： https://docs.microsoft.com/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount

如何备份 Key Vault 机密： https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultsecret

如何启用 Azure 备份： https://docs.microsoft.com/azure/backup



**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2：执行完整系统备份，并备份客户管理的所有密钥

**指导**：使用以下 PowerShell 命令备份 Key Vault 证书、密钥、托管存储帐户和机密：

- Backup-AzKeyVaultCertificate

- Backup-AzKeyVaultKey

- Backup-AzKeyVaultManagedStorageAccount

- Backup-AzKeyVaultSecret

或者，可将 Key Vault 备份存储在 Azure 备份中。

如何备份 Key Vault 证书： https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate

如何备份 Key Vault 密钥： https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey

如何备份 Key Vault 托管存储帐户： https://docs.microsoft.com/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount

如何备份 Key Vault 机密： https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultsecret

如何启用 Azure 备份： https://docs.microsoft.com/azure/backup



**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3：验证所有备份，包括客户管理的密钥

**指导**：使用以下 PowerShell 命令定期执行 Key Vault 证书、密钥、托管存储帐户和机密的数据还原：

- Restore-AzKeyVaultCertificate

- Restore-AzKeyVaultKey

- Restore-AzKeyVaultManagedStorageAccount

- Restore-AzKeyVaultSecret

如何还原 Key Vault 证书： https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultcertificate?view=azurermps-6.13.0

如何还原 Key Vault 密钥： https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0 

如何还原 Key Vault 托管存储帐户： https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultmanagedstorageaccount

如何还原 Key Vault 机密： https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultsecret?view=azurermps-6.13.0


**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4：确保保护备份和客户管理的密钥

**指导**：请确保为 Azure Key Vault 启用软删除。 软删除允许恢复已删除的密钥保管库和保管库对象，例如密钥、机密和证书。 

如何使用 Azure Key Vault 软删除： 

https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell

**Azure 安全中心监视**：目前不可用

**责任**：客户

## <a name="incident-response"></a>事件响应

有关详细信息，请参阅[安全控制：事件响应](../../security/benchmarks/security-control-incident-response.md)。

### <a name="101-create-an-incident-response-guide"></a>10.1：创建事件响应指导

**指南**：为组织制定事件响应指南。 确保在书面的事件响应计划中定义人员职责，以及事件处理/管理从检测到事件后审查的各个阶段。 这些流程应着重于保护敏感系统，例如使用 Key Vault 机密的系统。

如何在 Azure 安全中心配置工作流自动化： 

https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide   

关于自行建立安全事件响应流程的指南：  

https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Microsoft 安全响应中心的事件剖析：   

https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process   

客户还可利用 NIST 的“计算机安全事件处理指南”来制定自己的事件响应计划： 

https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2：创建事件评分和优先级设定过程

**指导**：安全中心为每条警报分配严重性，以帮助你优先处理应该最先调查的警报。 严重性取决于安全中心在发出警报时所依据的检测结果和分析结果的置信度，以及导致发出警报的活动的恶意企图的置信度。 此外，请用标记明确标记订阅（例如 生产、非生产）并创建命名系统来对 Azure 资源进行明确标识和分类，特别是处理敏感数据的资源。


**Azure 安全中心监视**：是

**责任**：客户

### <a name="103-test-security-response-procedures"></a>10.3：测试安全响应过程

**指导**：定期执行演练来测试系统的事件响应功能，以帮助保护 Azure Key Vault 实例及相关资源。 识别弱点和差距，并根据需要修改计划。

请参阅 NIST 的刊物：Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities（IT 规划和功能的测试、培训与演练计划指南）：  

https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4：提供安全事件联系人详细信息，并针对安全事件配置警报通知

**指导**：如果 Microsoft 安全响应中心 (MSRC) 发现数据被某方非法访问或未经授权访问，Microsoft 会使用安全事件联系信息联系用户。  事后审查事件，确保问题得到解决。

如何设置 Azure 安全中心安全联系人：

https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Azure 安全中心监视**：是

**责任**：客户

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5：将安全警报整合到事件响应系统中

**指导**：使用连续导出功能导出 Azure 安全中心警报和建议，以便确定已启用 Azure Key Vault 的资源的风险。 使用连续导出可以手动导出或者持续导出警报和建议。  可以使用 Azure 安全中心数据连接器将警报流式传输到 Azure Sentinel。 

 

如何配置连续导出： 

https://docs.microsoft.com/azure/security-center/continuous-export 

  

如何将警报流式传输到 Azure Sentinel： 

https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="106-automate-the-response-to-security-alerts"></a>10.6：自动响应安全警报

**指导**：使用 Azure 安全中心内的工作流自动化功能，通过“逻辑应用”针对安全警报和建议自动触发响应，来保护已启用 Azure Key Vault 的资源。 

 

如何配置工作流自动化和逻辑应用： 

https://docs.microsoft.com/azure/security-center/workflow-automation

**Azure 安全中心监视**：目前不可用

**责任**：客户

## <a name="penetration-tests-and-red-team-exercises"></a>渗透测试和红队练习

有关详细信息，请参阅[安全控制：渗透测试和红队演练](../../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)。

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1：定期对 Azure 资源执行渗透测试，确保在 60 天内修正所有发现的关键安全问题

**指导**：不要直接对 Azure Key Vault 服务执行渗透测试，而是建议测试使用 Key Vault 的 Azure 资源，以确保机密的安全性。

你将需要 Microsoft 互动规则，确保你的渗透测试不违反 Microsoft 政策：

https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

对于 Microsoft 红队演练策略和执行，以及针对 Microsoft 管理的云基础结构、服务和应用程序的实时站点渗透测试，可在此处找到详细信息： 

https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Azure 安全中心监视**：不适用

**责任**：共享

## <a name="next-steps"></a>后续步骤

- 参阅 [Azure 安全基准](../../security/benchmarks/overview.md)
- 详细了解 [Azure 安全基线](../../security/benchmarks/security-baselines-overview.md)