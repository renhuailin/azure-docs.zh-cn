---
title: Azure 备份的 azure 安全基准
description: Azure 备份安全基线为实现 Azure 安全基准中指定的安全建议提供过程指南和资源。
author: msmbaldwin
ms.service: backup
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 33408c65fdc09972807aaa8afdf123f1c39cfff5
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101700248"
---
# <a name="azure-security-baseline-for-azure-backup"></a>Azure 备份的 azure 安全基准

此安全基线将 [Azure 安全基准版本 1.0](../security/benchmarks/overview-v1.md) 中的指南应用到 azure 备份。 Azure 安全基准提供有关如何在 Azure 上保护云解决方案的建议。
内容按照 Azure 安全基准定义的 **安全控制措施** 进行分组，以及适用于 azure 备份的相关指南。 排除了不适用于 Azure 备份的 **控件**。

 
若要查看 Azure 备份如何完全映射到 Azure 安全基准，请参阅 [完整的 Azure 备份安全基线映射文件](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)。

## <a name="network-security"></a>网络安全

[有关详细信息，请参阅 *Azure 安全基线：* 网络安全性](../security/benchmarks/security-control-network-security.md)。

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6：部署基于网络的入侵检测/入侵防护系统 (IDS/IPS)

**指南**： Azure 备份所使用的终结点 (包括 Microsoft Azure 恢复服务代理) 都是由 Microsoft 管理的。 你负责管理要部署到本地系统的其他所有控件。

- [了解对 MARS 代理的网络和访问支持](https://docs.microsoft.com/azure/backup/backup-support-matrix-mars-agent#networking-and-access-support)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8：最大程度地降低网络安全规则的复杂性和管理开销

**指南**：如果在 Azure 虚拟机上使用 MARS 代理，请使用网络安全组或 Azure 防火墙上的 AzureBackup 服务标记，以允许对 azure 备份进行出站访问。

- [备份 Azure VM 中的 SQL Server 数据库](backup-sql-server-database-azure-vms.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="110-document-traffic-configuration-rules"></a>1.10：阐述流量配置规则

**指导**：如果在 Azure 虚拟机上使用 MARS 代理，请将该 VM 与网络安全组相关联，并使用描述来指定该规则的业务需求

**责任**：客户

**Azure 安全中心监视**：无

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11：使用自动化工具来监视网络资源配置和检测更改

**指南**：如果在受网络安全组或 azure 防火墙保护的 Azure 虚拟机上使用 MARS 代理，请使用 Azure 活动日志监视 NSG 或防火墙的配置。 可以在 Azure Monitor 中创建当这些资源发生更改时触发的警报。

- [查看和检索 Azure 活动日志事件](/azure/azure-monitor/platform/activity-log#view-the-activity-log)

- [使用 Azure Monitor 创建、查看和管理活动日志警报](/azure/azure-monitor/platform/alerts-activity-log)

**责任**：客户

**Azure 安全中心监视**：无

## <a name="logging-and-monitoring"></a>日志记录和监视

[有关详细信息，请参阅 *Azure 安全基线：* 日志记录和监视](../security/benchmarks/security-control-logging-monitoring.md)。

### <a name="22-configure-central-security-log-management"></a>2.2：配置中心安全日志管理

**指导**：对于控制平面审核日志记录，请启用 Azure 活动日志诊断设置，并将日志发送到 Log Aalytics 工作区、Azure 事件中心或 Azure 存储帐户进行存档。 使用 Azure 活动日志数据，可以确定在控制平面级别针对 Azure 资源执行的任何写入操作（PUT、POST、DELETE）的“操作内容、操作人员和操作时间”。

此外，通过 Azure Monitor 引入日志来聚合 Azure 备份生成的安全数据。 在 Azure Monitor 中，使用 Log Analytics 工作区来查询和执行分析，并使用存储帐户进行长期/存档存储。 或者，你可以将数据 SIEM 到 Azure Sentinel 或第三方安全事件和事件管理 (的) 。

- [如何启用 Azure 活动日志的诊断设置](/azure/azure-monitor/platform/activity-log)

- [使用恢复服务保管库的诊断设置](backup-azure-diagnostic-events.md)

- [如何加入 Azure Sentinel](../sentinel/quickstart-onboard.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：为 Azure 资源启用审核日志记录

**指导**：对于控制平面审核日志记录，请启用 Azure 活动日志诊断设置，并将日志发送到 Log Aalytics 工作区、Azure 事件中心或 Azure 存储帐户进行存档。 使用 Azure 活动日志数据，可以确定在控制平面级别针对 Azure 资源执行的任何写入操作（PUT、POST、DELETE）的“操作内容、操作人员和操作时间”。

此外，Azure 备份还发送诊断事件，可以收集这些事件并使用它们来实现分析、警报和报告目的。 可以通过 Azure 门户配置恢复服务保管库的诊断设置。 可以将一个或多个诊断事件发送到存储帐户、事件中心或 Log Analytics 工作区。

- [如何启用 Azure 活动日志的诊断设置](/azure/azure-monitor/platform/activity-log)

- [使用恢复服务保管库的诊断设置](backup-azure-diagnostic-events.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="25-configure-security-log-storage-retention"></a>2.5：配置安全日志存储保留期

**指导**：在 Azure Monitor 中，根据组织的符合性规定，为与 Azure 恢复服务保管库关联的 Log Analytics 工作区设置日志保留期。

- [如何设置日志保留参数](/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="26-monitor-and-review-logs"></a>2.6：监视和查看日志

**指导**：Azure 备份在恢复服务保管库中提供内置的监视和警报功能。 无需配置任何附加的管理基础结构即可使用这些功能。 还可以使用 Azure Monitor 提高监视和报告的规模。

启用 Azure 活动日志诊断设置，并将日志发送到 Log Analytics 工作区。 在 Log Analytics 中执行查询，以搜索字词、识别趋势、分析模式，并根据可能已为恢复服务保管库收集的活动日志数据提供许多其他见解。

- [监视 Azure 备份工作负荷](backup-azure-monitoring-built-in-monitor.md)

- [如何启用 Azure 活动日志的诊断设置](/azure/azure-monitor/platform/activity-log)

- [如何收集和分析 Azure Monitor 的 Log Analytics 工作区中的 Azure 活动日志](/azure/azure-monitor/platform/activity-log)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7：针对异常活动启用警报

**指导**：Azure 备份在恢复服务保管库中提供内置的监视和警报功能。 无需配置任何附加的管理基础结构即可使用这些功能。 还可以使用 Azure Monitor 提高监视和报告的规模。

警报主要用于通知用户，让他们采取相关的措施。 “备份警报”部分显示 Azure 备份服务生成的警报。 这些警报由服务定义，你无法自定义创建任何警报。

还可以将 Log Analytics 工作区加入 Azure Sentinel，因为它提供了安全业务流程自动化响应 (SOAR) 解决方案。 这样便可以创建 playbook（自动化解决方案）并将其用于修正安全问题。 此外，还可以使用 Azure Monitor 在 Log Analytics 工作区中创建自定义日志警报。

- [监视 Azure 备份工作负荷](backup-azure-monitoring-built-in-monitor.md)

- [如何加入 Azure Sentinel](../sentinel/quickstart-onboard.md)

- [使用 Azure Monitor 创建、查看和管理日志警报](/azure/azure-monitor/platform/alerts-log)

**责任**：客户

**Azure 安全中心监视**：无

## <a name="identity-and-access-control"></a>标识和访问控制

[有关详细信息，请参阅 *Azure 安全基线：* 标识和访问控制](../security/benchmarks/security-control-identity-access-control.md)。

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1：维护管理帐户的清单

**指南**： Azure Active Directory (Azure AD) 具有必须显式分配的内置角色，并且可以通过查询来确定哪些帐户是这些角色的成员。 使用 Azure AD PowerShell 模块执行即席查询，以发现属于管理组成员的帐户。

- [如何使用 PowerShell 获取 Azure AD 中的目录角色](/powershell/module/azuread/get-azureaddirectoryrole)

- [如何使用 PowerShell 获取 Azure AD 中目录角色的成员](/powershell/module/azuread/get-azureaddirectoryrolemember)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="32-change-default-passwords-where-applicable"></a>3.2：在适用的情况下更改默认密码

**指导**：Azure Active Directory (Azure AD) 没有默认密码的概念。 其他需要密码的 Azure 资源会强制创建具有复杂性要求和最小密码长度的密码，该长度因服务而异。 你对可能使用默认密码的第三方应用程序和市场服务负责。

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

**指导**：使用 Azure 应用注册（服务主体）来检索令牌，该令牌可用于通过 API 调用与恢复服务保管库进行交互。

- [如何调用 Azure REST API](/rest/api/azure/#how-to-call-azure-rest-apis-with-postman)

- [如何将客户端应用程序 (服务主体) 注册 Azure Active Directory (Azure AD) ](/rest/api/azure/#register-your-client-application-with-azure-ad)

- [Azure 恢复服务 API 信息](/rest/api/recoveryservices/)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5：对所有基于 Azure Active Directory 的访问使用多重身份验证

**指导**：在 Azure 备份中执行关键操作时，必须输入 Azure 门户中提供的安全 PIN。 启用 Azure Active Directory (Azure AD) 多重身份验证添加一个安全层。 只有获得授权、具有有效 Azure 凭据且通过第二台设备进行身份验证的用户能够访问 Azure 门户。

- [Azure 备份中的 azure 多重身份验证](backup-azure-security-feature.md)

- [规划基于云的 Azure AD 多重身份验证部署](../active-directory/authentication/howto-mfa-getstarted.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6：对所有管理任务使用专用计算机（特权访问工作站）

**指南**：使用 azure 托管的工作站 Azure AD 多因素身份验证 (MFA) 配置为登录和配置启用了 Azure 备份的资源。

- [特权访问工作站](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [规划基于云的 Azure AD 多重身份验证部署](../active-directory/authentication/howto-mfa-getstarted.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7：记录来自管理帐户的可疑活动并对其发出警报

**指导**：当环境中出现可疑或不安全的活动时，可使用 Azure Active Directory (Azure AD) Privileged Identity Management (PIM) 生成日志和警报。

此外，还可使用 Azure AD 风险检测来查看警报和报告有风险的用户行为。

- [如何部署 Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [了解 Azure AD 风险检测](../active-directory/identity-protection/overview-identity-protection.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8：仅从批准的位置管理 Azure 资源

**指南**：使用条件访问命名位置，仅允许从 IP 地址范围或国家/地区的特定逻辑分组访问 Azure 门户。

- [如何在 Azure 中配置命名位置](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="39-use-azure-active-directory"></a>3.9：使用 Azure Active Directory

**指南**：使用 Azure Active Directory (Azure AD) 作为 Azure 备份实例的中央身份验证和授权系统。 Azure AD 通过对静态数据和传输中数据使用强加密来保护数据。 Azure AD 还会对用户凭据进行加盐、哈希处理和安全存储操作。

- [如何配置 Azure 备份以使用 Azure AD 登录](../app-service/configure-authentication-provider-aad.md)

- [如何创建和配置 Azure AD 实例](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10：定期审查和协调用户访问

**指导**：Azure Active Directory (Azure AD) 提供了日志来帮助你发现过时的帐户。 此外，还可以使用 Azure AD 访问评审来有效地管理组成员身份、访问企业应用程序和角色分配。 应定期评审用户的访问权限，确保只有适当的用户才持续拥有访问权限。

- [了解 Azure AD 报告](/azure/active-directory/reports-monitoring/)

- [如何使用 Azure AD 访问评审](../active-directory/governance/access-reviews-overview.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11：监视尝试访问已停用凭据的行为

**指南**：使用 Azure Active Directory (Azure AD) 作为 Azure 备份实例的中央身份验证和授权系统。 Azure AD 通过对静态数据和传输中数据使用强加密来保护数据。 Azure AD 还会对用户凭据进行加盐、哈希处理和安全存储操作。

你可以访问 Azure AD 登录活动、审核和风险事件日志源，以便与 Azure Sentinel 或第三方 SIEM 集成。

可以通过为 Azure AD 用户帐户创建诊断设置，并将审核日志和登录日志发送到 Log Analytics 工作区，来简化此过程。 可以在 Log Analytics 中配置所需的日志警报。

- [如何将 Azure 活动日志集成到 Azure Monitor](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

- [如何加入 Azure Sentinel](../sentinel/quickstart-onboard.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12：针对帐户登录行为偏差发出警报

**指南**：使用 Azure Active Directory (Azure AD) 作为恢复服务保管库的中央身份验证和授权系统。 对于控制平面（Azure 门户）中帐户登录行为的偏差，可使用 Azure AD 标识保护和风险检测功能进行配置，使其在检测到与用户标识相关的可疑操作时自动进行响应。 还可将数据引入 Azure Sentinel 以做进一步调查。

- [如何查看 Azure AD 风险登录](../active-directory/identity-protection/overview-identity-protection.md)

- [如何配置和启用标识保护风险策略](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [如何加入 Azure Sentinel](../sentinel/quickstart-onboard.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13：在支持场合下为 Microsoft 提供对相关客户数据的访问权限

**指导**：目前不适用；Azure 备份尚不支持客户密码箱。

- [支持客户密码箱的服务列表](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability)

**责任**：客户

**Azure 安全中心监视**：无

## <a name="data-protection"></a>数据保护

[有关详细信息，请参阅 *Azure 安全基线：* 数据保护](../security/benchmarks/security-control-data-protection.md)。

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1：维护敏感信息的清单

**指导**：使用标记可以帮助跟踪存储或处理敏感信息的 Azure 资源。

- [如何创建和使用标记](../azure-resource-manager/management/tag-resources.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2：隔离存储或处理敏感信息的系统

**指南**：将 Azure 基础结构即服务备份 (IaaS) 虚拟机时，Azure 备份提供独立且独立的备份，以防止意外销毁原始数据。 备份存储在提供恢复点内置管理的恢复服务保管库中。

为开发、测试和生产恢复服务保管库实施单独的订阅和可选的管理组。 资源应由虚拟网络或子网进行分隔，进行适当标记，并受网络安全组或 Azure 防火墙的保护。 存储或处理敏感数据的资源应当充分隔离。 对于存储或处理敏感数据的虚拟机，请实施相应的策略和过程，以在不使用这些虚拟机时将其关闭。

- [Azure 备份概述](backup-overview.md)

- [如何创建其他 Azure 订阅](../cost-management-billing/manage/create-subscription.md)

- [如何创建管理组](../governance/management-groups/create-management-group-portal.md)

- [如何创建和使用标记](../azure-resource-manager/management/tag-resources.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3：监视和阻止未经授权的敏感信息传输

**指导**：目前不适用；数据标识、分类和丢失防护功能尚不适用于 Azure 备份。

Microsoft 管理 Azure 备份的底层基础结构，并实施了严格的控制措施来防止客户数据丢失或泄露。

- [了解 Azure 中的客户数据保护](../security/fundamentals/protection-customer-data.md)

**责任**：共享

**Azure 安全中心监视**：无

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5：使用有效的发现工具识别敏感数据

**指导**：目前不适用；数据标识、分类和丢失防护功能尚不适用于 Azure 备份。

Microsoft 管理 Azure 备份的底层基础结构，并实施了严格的控制措施来防止客户数据丢失或泄露。

- [了解 Azure 中的客户数据保护](../security/fundamentals/protection-customer-data.md)

**责任**：共享

**Azure 安全中心监视**：无

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6：使用基于角色的访问控制来控制对资源的访问

**指南**： azure RBAC) 的 azure 基于角色的访问控制支持对 azure 进行细致的访问管理 (。 使用 Azure RBAC，可以在团队中实现职责分离，仅向用户授予他们执行作业所需的访问权限。

Azure 备份提供三个用于控制备份管理操作的内置角色：备份参与者、备份操作员和备份读取者。 可以将备份内置角色映射到各种备份管理操作。

- [如何配置 Azure RBAC](../role-based-access-control/role-assignments-portal.md)

- [使用 Azure 基于角色的访问控制管理 Azure 备份恢复点](backup-rbac-rs-vault.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8：静态加密敏感信息

**指导**：Azure 备份支持加密静态数据。 对于本地备份，使用在备份到 Azure 时提供的密码提供静态加密。 对于云工作负荷，使用存储服务加密 (SSE) 对数据进行静态加密。 Microsoft 不会解密任何位置的备份数据。

使用 MARS 代理进行备份或使用通过客户托管密钥加密的恢复服务保管库时，只有你才能访问加密密钥。 Microsoft 不保留副本，且没有访问该密钥的权限。 如果客户丢失了密钥，Microsoft 无法恢复备份数据。

- [了解 Azure 备份的静态加密](backup-encryption.md)

**责任**：共享

**Azure 安全中心监视**：无

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9：记录对关键 Azure 资源的更改并对此类更改发出警报

**指导**：将 Azure Monitor 与 Azure 活动日志结合使用，以创建当生产 Azure 恢复服务保管库以及其他关键或相关资源发生更改时触发的警报。

- [如何针对 Azure 活动日志事件创建警报](/azure/azure-monitor/platform/alerts-activity-log)

**责任**：客户

**Azure 安全中心监视**：无

## <a name="vulnerability-management"></a>漏洞管理

[有关详细信息，请参阅 *Azure 安全基线：* 漏洞管理。](../security/benchmarks/security-control-vulnerability-management.md)

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1：运行自动漏洞扫描工具

**指导**：暂不适用；Azure 安全中心的漏洞评估暂不适用于 Azure 备份。

Microsoft 对基础平台进行了扫描和修补。 查看 Microsoft Azure 备份可用的安全控制，以减少与服务配置相关的漏洞。

- [了解 Azure 备份可用的安全控制](/azure/backup/backup-security-controls)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5：使用风险评级过程来确定已发现漏洞的修正措施的优先级

**指导**：目前不适用；Azure 安全中心尚不支持 Azure 备份的安全配置。

- [Azure 安全中心支持的 PaaS 服务列表](../security-center/features-paas.md)

**责任**：客户

**Azure 安全中心监视**：无

## <a name="inventory-and-asset-management"></a>清单和资产管理

[有关详细信息，请参阅 *Azure 安全基线：* 清单和资产管理](../security/benchmarks/security-control-inventory-asset-management.md)。

### <a name="61-use-automated-asset-discovery-solution"></a>6.1：使用自动化资产发现解决方案

**指南**：使用 Azure 资源关系图可查询/发现订阅中的所有资源 (如计算、存储、网络、端口和协议) 。 确保租户中具有适当的（读取）权限，并枚举所有 Azure 订阅以及订阅中的资源。

尽管可以通过 Resource Graph 发现经典 Azure 资源，但我们强烈建议你今后还是创建并使用 Azure 资源管理器资源。

- [如何使用 Azure Resource Graph 创建查询](../governance/resource-graph/first-query-portal.md)

- [如何查看 Azure 订阅](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-4.8.0&amp;preserve-view=true)

- [了解 Azure RBAC](../role-based-access-control/overview.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="62-maintain-asset-metadata"></a>6.2：维护资产元数据

**指导**：将标记应用到 Azure资源，以便有条理地将元数据组织成某种分类。

- [如何创建和使用标记](../azure-resource-manager/management/tag-resources.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="63-delete-unauthorized-azure-resources"></a>6.3：删除未经授权的 Azure 资源

**指南**：在适用的情况下，请使用标记、管理组和单独的订阅来组织和跟踪 Azure 资产。 定期核对清单，确保及时地从订阅中删除未经授权的资源。

此外，使用 Azure Policy 对可使用以下内置策略定义在客户订阅中创建的资源类型施加限制：

- 不允许的资源类型

- 允许的资源类型

访问所引用的链接可获得更多信息。

- [如何创建其他 Azure 订阅](../cost-management-billing/manage/create-subscription.md)

- [如何创建管理组](../governance/management-groups/create-management-group-portal.md)

- [如何创建和使用标记](../azure-resource-manager/management/tag-resources.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4：定义并维护已批准的 Azure 资源的清单

**指导**：为计算资源定义已批准的 Azure 资源和软件。

**责任**：客户

**Azure 安全中心监视**：无

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5：监视未批准的 Azure 资源

**指导**：使用 Azure Policy 对可以在订阅中创建的资源类型施加限制。

使用 Azure Resource Graph 查询和发现订阅中的资源。  确保环境中的所有 Azure 资源均已获得批准。

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [如何使用 Azure Graph 创建查询](../governance/resource-graph/first-query-portal.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="69-use-only-approved-azure-services"></a>6.9：仅使用已批准的 Azure 服务

**指南**：在 Azure Policy 中使用以下内置策略定义，对可以在客户订阅中创建的资源类型施加限制：

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

**指导**：使用 Azure Policy 为恢复服务保管库定义和实施标准安全配置。 在“Microsoft.RecoveryServices”命名空间中使用 Azure Policy 别名创建自定义策略，以审核或强制实施恢复服务保管库的配置。

- [如何查看可用的 Azure Policy 别名](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&amp;preserve-view=true)

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3：维护安全的 Azure 资源配置

**指南**：使用 azure 策略 [拒绝] 和 [部署（如果不存在）影响跨 Azure 资源强制实施安全设置。

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [了解 Azure Policy 效果](../governance/policy/concepts/effects.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5：安全存储 Azure 资源的配置

**指导**：如果使用自定义的 Azure Policy 定义，请使用 Azure DevOps 或 Azure Repos 安全地存储和管理代码。

- [如何在 Azure DevOps 中存储代码](/azure/devops/repos/git/gitworkflow)

- [Azure Repos 文档](/azure/devops/repos)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7：部署 Azure 资源的配置管理工具

**指导**：在“Microsoft.RecoveryServices”命名空间中使用内置的 Azure Policy 定义和 Azure Policy 别名创建自定义策略，以审核、强制实施系统配置并为其发出警报。 另外，开发一个用于管理策略例外的流程和管道。

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9：为 Azure 资源实施自动配置监视

**指南**：使用内置的 azure 策略定义以及 **microsoft.recoveryservices** 命名空间中的 azure 策略别名创建自定义策略，以对系统配置进行警报、审核和强制执行。 使用 Azure Policy 的“[审核]”、“[拒绝]”和“[不存在则部署]”效果自动强制实施 Azure 资源的配置。

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="711-manage-azure-secrets-securely"></a>7.11：安全管理 Azure 机密

**指导**：设置 MARS 代理时，将加密密码存储在 Azure 密钥保管库中。

- [如何向 Key Vault 进行身份验证](../key-vault/general/authentication.md)

- [如何分配 Key Vault 访问策略](../key-vault/general/assign-access-policy-portal.md)

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

**指南**： azure 的 Microsoft 反恶意软件已在支持 azure 服务的基础主机上启用 (例如，azure 备份) ，但它不会在你的内容上运行。

预先扫描正在上载到非计算 Azure 资源的文件，如应用服务、Data Lake Storage 和 Blob 存储。

使用适用于数据服务的 Azure 安全中心威胁防护来检测已上传到存储帐户的恶意软件。

- [了解适用于 Azure 云服务和虚拟机的 Microsoft Antimalware](../security/fundamentals/antimalware.md)

- [了解适用于数据服务的 Azure 安全中心威胁防护](../security-center/azure-defender.md)

**责任**：客户

**Azure 安全中心监视**：无

## <a name="data-recovery"></a>数据恢复

[有关详细信息，请参阅 *Azure 安全基线：* 数据恢复](../security/benchmarks/security-control-data-recovery.md)。

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2：执行完整系统备份，并备份客户管理的所有密钥

**指导**：本地冗余存储 (LRS) 将数据中心的存储缩放单元中的数据复制三次（创建三个数据副本）。 数据的所有副本存在于同一区域。 LRS 是一种低成本选项，用于保护数据免受本地硬件故障的影响。 异地冗余存储 (GRS) 是默认的和推荐的复制选项。 GRS 将数据复制到次要区域，远离源数据的主要位置。 GRS 的成本高于 LRS，但 GRS 为你的数据提供更高的持久性，即使出现区域性中断也是如此。

在 Azure Key Vault 中备份客户管理的密钥。

- [Azure 备份概述](backup-overview.md)

- [如何在 Azure 中备份密钥保管库密钥](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

- [了解 Azure 备份中的加密](backup-encryption.md)

**责任**：客户

**Azure 安全中心监视**： [azure 安全基准](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) 是安全中心的默认策略计划，是 [安全中心建议](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md)的基础。 安全中心会自动启用与此控件相关的 Azure 策略定义。 与此控件相关的警报可能需要 [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) 计划相关服务。

**Azure 策略内置定义-microsoft.recoveryservices**：

[!INCLUDE [Resource Policy for Microsoft.RecoveryServices 9.2](../../includes/policy/standards/asb/rp-controls/microsoft.recoveryservices-9-2.md)]

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3：验证所有备份，包括客户管理的密钥

**指导**：测试对备份的客户管理的密钥进行还原。

- [如何在 Azure 中还原密钥保管库密钥](/powershell/module/az.keyvault/restore-azkeyvaultkey)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4：确保保护备份和客户管理的密钥

**指导**：对于本地备份，使用在备份到 Azure 时提供的密码提供静态加密。 对于 Azure VM，使用存储服务加密 (SSE) 对数据进行静态加密。 可以在密钥保管库中启用软删除，以防止意外删除或恶意删除密钥。

- [如何在密钥保管库中启用软删除](https://docs.microsoft.com/azure/storage/blobs/soft-delete-blob-overview?tabs=azure-portal)

**责任**：客户

**Azure 安全中心监视**：无

## <a name="incident-response"></a>事件响应

[有关详细信息，请参阅 *Azure 安全基线：* 事件响应](../security/benchmarks/security-control-incident-response.md)。

### <a name="101-create-an-incident-response-guide"></a>10.1：创建事件响应指导

**指南**：为组织制定事件响应指南。 确保在书面的事件响应计划中定义人员职责，以及事件处理/管理从检测到事件后审查的各个阶段。

- [如何在 Azure 安全中心配置工作流自动化](../security-center/security-center-planning-and-operations-guide.md)

- [关于建立自己的安全事件响应流程的指南](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process)

- [Microsoft 安全响应中心事件分析](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process)

- [NIST 的计算机安全事件处理指南](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2：创建事件评分和优先级设定过程

**指导**：安全中心为每条警报分配严重性，以帮助你优先处理应该最先调查的警报。 严重性取决于安全中心在发出警报时所依据的检测结果和分析结果的置信度，以及导致发出警报的活动的恶意企图的置信度。

此外，还可以清楚地标记订阅并创建命名系统，以便清晰地识别和分类 Azure 资源。

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

**指导**：请遵循 Microsoft 互动规则，确保你的渗透测试不违反 Microsoft 政策。 你可以在引用的链接中找到有关 Microsoft 托管的云基础结构、服务和应用程序的 Microsoft 策略和执行情况的详细信息。

- [参与的渗透测试规则](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Microsoft 云红色组合](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**责任**：共享

**Azure 安全中心监视**：无

## <a name="next-steps"></a>后续步骤

- 参阅 [Azure 安全基准 V2 概述](/azure/security/benchmarks/overview)
- 详细了解 [Azure 安全基线](/azure/security/benchmarks/security-baselines-overview)
