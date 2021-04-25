---
title: 适用于 Microsoft Azure 的客户密码箱的 Azure 安全基线
description: Microsoft Azure 客户密码箱安全基线为实现 Azure 安全基准中指定的安全建议提供过程指南和资源。
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/31/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 1b387cd92d94727b57af22675672fdb08b4cf5cb
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2021
ms.locfileid: "106449692"
---
# <a name="azure-security-baseline-for-customer-lockbox-for-microsoft-azure"></a>适用于 Microsoft Azure 的客户密码箱的 Azure 安全基线

此安全基线将 [Azure 安全基准版本 1.0](../benchmarks/overview-v1.md) 中的指导应用于客户密码箱。 Azure 安全基准提供有关如何在 Azure 上保护云解决方案的建议。 内容分为 Azure 安全基准定义的安全控制和适用于客户密码箱的相关指南。 

>[!NOTE]
>排除了不适用于客户密码箱或 Microsoft 为其责任方的控件。 若要了解客户密码箱如何完全映射到 Azure 安全基准，请参阅[完整的客户密码箱安全基线映射文件](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)。

## <a name="logging-and-monitoring"></a>日志记录和监视

[有关详细信息，请参阅 *Azure 安全基线：* 日志记录和监视](../benchmarks/security-control-logging-monitoring.md)。

### <a name="22-configure-central-security-log-management"></a>2.2：配置中心安全日志管理

**指导**：在 Azure 活动日志中会自动启用和维护客户密码箱的审核日志。 可以通过将此数据从 Azure 活动日志流式传输到 Log Analytic 工作区来查看它，然后即可在该工作区中对其进行研究和分析。

将客户密码箱生成的活动日志加入 Azure Sentinel 或其他 SIEM，以集中进行日志聚合和管理。

- [客户密码箱的审核日志](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#auditing-logs)

- [如何加入 Azure Sentinel](../../sentinel/quickstart-onboard.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：为 Azure 资源启用审核日志记录

**指导**：在 Azure 活动日志中会自动启用和维护客户密码箱的审核日志。 可以通过将此数据从 Azure 活动日志流式传输到 Log Analytic 工作区来查看它，然后即可在该工作区中对其进行研究和分析。

- [客户密码箱的审核日志](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#auditing-logs)

- [如何加入 Azure Sentinel](../../sentinel/quickstart-onboard.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="25-configure-security-log-storage-retention"></a>2.5：配置安全日志存储保留期

**指导**：在 Azure Monitor 中，根据组织的合规性规定，为与客户密码箱关联的 Log Analytics 工作区设置日志保留期。

- [如何设置日志保留参数](https://docs.microsoft.com/azure/azure-monitor/logs/manage-cost-storage#change-the-data-retention-period)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="26-monitor-and-review-logs"></a>2.6：监视和查看日志

**指导**：在 Azure 活动日志中会自动启用和维护客户密码箱的审核日志。 可以通过将此数据从 Azure 活动日志流式传输到 Log Analytic 工作区来查看它，然后即可在该工作区中对其进行研究和分析。 分析和监视客户密码箱请求的日志中是否存在异常行为。 使用 Azure Sentinel 工作区中的“日志”部分来执行查询，或根据客户密码箱日志创建警报。

- [客户密码箱中的审核日志](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#auditing-logs)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7：针对异常活动启用警报

**指导**：在 Azure 活动日志中会自动启用和维护客户密码箱的审核日志。 可以通过将此数据从 Azure 活动日志流式传输到 Log Analytic 工作区来查看它，然后即可在该工作区中对其进行研究和分析。 分析和监视客户密码箱请求的日志中是否存在异常行为。 使用 Azure Sentinel 工作区中的“日志”部分来执行查询，或根据客户密码箱日志创建警报。

- [客户密码箱中的审核日志](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#auditing-logs)

- [如何针对 Log Analytics 日志数据发出警报](../../azure-monitor/alerts/tutorial-response.md)

**责任**：客户

**Azure 安全中心监视**：无

## <a name="identity-and-access-control"></a>标识和访问控制

[有关详细信息，请参阅 *Azure 安全基线：* 标识和访问控制](../benchmarks/security-control-identity-access-control.md)。

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1：维护管理帐户的清单

**指导**：维护对客户密码箱请求拥有管理访问权限的用户帐户的清单。 可以在 Azure 门户中为你的订阅使用“标识和访问控制(IAM)”窗格来配置 Azure 基于角色的访问控制 (Azure RBAC)。 角色将应用到 Azure Active Directory (Azure AD) 中的用户、组、服务主体和托管标识。

在客户组织中，具有 Azure 订阅的“所有者”角色的用户会收到来自 Microsoft 的电子邮件，该邮件会告知是否有任何挂起的访问请求。 对于客户密码箱请求，此人为指定的审批者。

- [了解自定义角色](../../role-based-access-control/custom-roles.md)

- [如何为工作簿配置 Azure RBAC](../../sentinel/quickstart-get-visibility.md)

- [了解客户密码箱中的访问请求权限](customer-lockbox-overview.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="32-change-default-passwords-where-applicable"></a>3.2：在适用的情况下更改默认密码

**指导**：Azure Active Directory (Azure AD) 没有默认密码的概念。 其他需要密码的 Azure 资源会强制创建具有复杂性要求和最小密码长度的密码，该长度因服务而异。 你对可能使用默认密码的第三方应用程序和市场服务负责。

**责任**：客户

**Azure 安全中心监视**：无

### <a name="33-use-dedicated-administrative-accounts"></a>3.3：使用专用管理帐户

**指南**：围绕专用管理帐户的使用创建标准操作程序。 使用 Azure 安全中心标识和访问管理来监视管理帐户的数量。

此外，为了跟踪专用管理帐户，你可以使用 Azure 安全中心或内置的 Azure 策略提供的建议，例如：

- 应该为你的订阅分配了多个所有者
- 应从订阅中删除拥有所有者权限的已弃用帐户
- 应从订阅中删除拥有所有者权限的外部帐户

- [如何使用 Azure 安全中心监视标识和访问（预览）](../../security-center/security-center-identity-access.md)

- [如何使用 Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4：使用 Azure Active Directory 单一登录 (SSO)

**指导**：不适用；对客户密码箱的访问是通过 Azure 门户进行的，专为具有“所有者”租户角色的帐户保留。 不支持单一登录。

**责任**：客户

**Azure 安全中心监视**：无

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5：对所有基于 Azure Active Directory 的访问使用多重身份验证

指导：启用 Azure AD 多重身份验证，并遵循 Azure 安全中心标识和访问管理建议。

- [如何启用 Azure AD 多重身份验证](../../active-directory/authentication/howto-mfa-getstarted.md)

- [如何在 Azure 安全中心监视标识和访问](../../security-center/security-center-identity-access.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3.6：使用由 Azure 管理的安全工作站执行管理任务

指导：使用启用了 Azure AD 多重身份验证的特权访问工作站 (PAW) 来登录并配置客户密码箱请求。 

- [特权访问工作站](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [规划基于云的 Azure AD 多重身份验证部署](../../active-directory/authentication/howto-mfa-getstarted.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7：记录来自管理帐户的可疑活动并对其发出警报

**指导**：当环境中出现可疑或不安全的活动时，可使用 Azure Active Directory (Azure AD) Privileged Identity Management (PIM) 生成日志和警报。

此外，还可使用 Azure AD 风险检测来查看警报和报告有风险的用户行为。

- [如何部署 Privileged Identity Management (PIM)](../../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [了解 Azure AD 风险检测](../../active-directory/identity-protection/overview-identity-protection.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8：仅从批准的位置管理 Azure 资源

**指导**：使用“条件访问”中的“命名位置”，以仅允许从 IP 地址范围或国家/地区的特定逻辑分组访问 Azure 门户。

- [如何在 Azure 中配置命名位置](../../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="39-use-azure-active-directory"></a>3.9：使用 Azure Active Directory

指导：在适用的情况下使用 Azure Active Directory (Azure AD) 作为中央身份验证和授权系统。 Azure AD 通过对静态数据和传输中数据使用强加密来保护数据。 Azure AD 还会对用户凭据进行加盐、哈希处理和安全存储操作。

- [如何创建和配置 Azure AD 实例](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10：定期审查和协调用户访问

**指导**：Azure Active Directory (Azure AD) 提供了日志来帮助你发现过时的帐户。 此外，使用 Azure AD 访问评审还可有效管理组成员身份、对企业应用程序的访问权限以及角色分配。 可以定期评审用户的访问权限，确保只有适当的用户才持续拥有访问权限。

- [了解 Azure AD 报告](/azure/active-directory/reports-monitoring/)

- [如何使用 Azure AD 访问评审](../../active-directory/governance/access-reviews-overview.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11：监视尝试访问已停用凭据的行为

指导：在适用的情况下使用 Azure Active Directory (Azure AD) 作为中央身份验证和授权系统。 Azure AD 通过对静态数据和传输中数据使用强加密来保护数据。 Azure AD 还会对用户凭据进行加盐、哈希处理和安全存储操作。

你可以访问 Azure AD 登录活动、审核和风险事件日志源，以便与 Azure Sentinel 或第三方 SIEM 集成。

可以通过为 Azure AD 用户帐户创建诊断设置，并将审核日志和登录日志发送到 Log Analytics 工作区，来简化此过程。 可以在 Log Analytics 中配置所需的日志警报。

- [如何将 Azure 活动日志集成到 Azure Monitor 中](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

- [如何加入 Azure Sentinel](../../sentinel/quickstart-onboard.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12：针对帐户登录行为偏差发出警报

指导：对于控制平面（例如 Azure 门户）中帐户登录行为的偏差，请使用 Azure Active Directory (Azure AD) 标识保护和风险检测功能进行配置，实现在检测到与用户标识相关的可疑操作时自动进行响应。 还可将数据引入 Azure Sentinel 以做进一步调查。

- [如何查看 Azure AD 风险登录](../../active-directory/identity-protection/overview-identity-protection.md)

- [如何配置和启用标识保护风险策略](../../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [如何加入 Azure Sentinel](../../sentinel/quickstart-onboard.md)

**责任**：客户

**Azure 安全中心监视**：无

## <a name="data-protection"></a>数据保护

[有关详细信息，请参阅 *Azure 安全基线：* 数据保护](../benchmarks/security-control-data-protection.md)。

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6：使用基于角色的访问控制来控制对资源的访问

**指导**：向在租户级别具有“所有者”角色的用户授予客户密码箱请求审批权限。

- [了解客户密码箱工作流](customer-lockbox-overview.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9：记录对关键 Azure 资源的更改并对此类更改发出警报

**指导**：在 Azure 活动日志中会自动启用和维护客户密码箱的审核日志。 使用 Azure 活动日志监视并检测对 Azure 客户密码箱资源的更改。 在 Azure Monitor 中创建当关键资源发生更改时触发的警报。

- [如何在客户密码箱中启用审核](customer-lockbox-overview.md)

- [如何查看和检索 Azure 活动日志事件](https://docs.microsoft.com/azure/azure-monitor/essentials/activity-log#view-the-activity-log)

- [如何在 Azure Monitor 中创建警报](../../azure-monitor/alerts/alerts-activity-log.md)

**责任**：客户

**Azure 安全中心监视**：无

## <a name="inventory-and-asset-management"></a>清单和资产管理

[有关详细信息，请参阅 *Azure 安全基线：* 清单和资产管理](../benchmarks/security-control-inventory-asset-management.md)。

### <a name="61-use-automated-asset-discovery-solution"></a>6.1：使用自动化资产发现解决方案

**指导**：使用 Azure Resource Graph 查询/发现订阅中的所有资源（例如计算、存储、网络、端口和协议等）。 确保租户中具有适当的（读取）权限，并枚举所有 Azure 订阅以及订阅中的资源。

尽管可以通过 Azure Resource Graph 发现经典 Azure 资源，但我们强烈建议你创建并使用 Azure 资源管理器资源。

- [如何使用 Azure Resource Graph 创建查询](../../governance/resource-graph/first-query-portal.md)

- [如何查看 Azure 订阅](/powershell/module/az.accounts/get-azsubscription)

- [了解 Azure 基于角色的访问控制 (Azure RBAC)](../../role-based-access-control/overview.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="63-delete-unauthorized-azure-resources"></a>6.3：删除未经授权的 Azure 资源

**指南**：在适用的情况下，请使用标记、管理组和单独的订阅来组织和跟踪 Azure 资产。 定期核对清单，确保及时地从订阅中删除未经授权的资源。

此外，在 Azure Policy 中使用以下内置策略定义，对可以在客户订阅中创建的资源类型施加限制：

- 不允许的资源类型
- 允许的资源类型

有关详细信息，请参阅以下资源：

- [如何创建其他 Azure 订阅](../../cost-management-billing/manage/create-subscription.md)

- [如何创建管理组](../../governance/management-groups/create-management-group-portal.md)

- [如何创建和使用标记](../../azure-resource-manager/management/tag-resources.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5：监视未批准的 Azure 资源

**指导**：使用 Azure Policy 对可以在订阅中创建的资源类型施加限制。 

使用 Azure Resource Graph 查询/发现订阅中的资源。 确保环境中的所有 Azure 资源均已获得批准。

- [如何配置和管理 Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

- [如何使用 Azure Resource Graph 创建查询](../../governance/resource-graph/first-query-portal.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="69-use-only-approved-azure-services"></a>6.9：仅使用已批准的 Azure 服务

**指南**：在 Azure Policy 中使用以下内置策略定义，对可以在你的订阅中创建的资源类型施加限制：
- 不允许的资源类型
- 允许的资源类型

有关详细信息，请参阅以下资源：

- [如何配置和管理 Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

- [如何使用 Azure Policy 拒绝特定的资源类型](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11：限制用户与 Azure 资源管理器进行交互的能力

**指南**：配置 Azure 条件访问，使其通过为“Microsoft Azure 管理”应用配置“阻止访问”，来限制用户与 Azure 资源管理器进行交互的能力。

- [如何配置条件访问以阻止访问 Azure 资源管理器](../../role-based-access-control/conditional-access-azure-management.md)

**责任**：客户

**Azure 安全中心监视**：无

## <a name="secure-configuration"></a>安全配置

[有关详细信息，请参阅 *Azure 安全基线：* 安全配置](../benchmarks/security-control-secure-configuration.md)。

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13：消除意外的凭据透露

**指南**：实施凭据扫描程序来识别代码中的凭据。 凭据扫描程序还会建议将发现的凭据转移到更安全的位置，例如 Azure Key Vault。

- [如何设置凭据扫描程序](https://secdevtools.azurewebsites.net/helpcredscan.html)

**责任**：客户

**Azure 安全中心监视**：无

## <a name="malware-defense"></a>恶意软件防护

[有关详细信息，请参阅 *Azure 安全基线：* 恶意软件防护](../benchmarks/security-control-malware-defense.md)。

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2：预先扫描要上传到非计算 Azure 资源的文件

**指导**：Microsoft Antimalware 在支持 Azure 服务（例如，客户密码箱）的基础主机上处于启用状态。

你需要负责预先扫描要上传到非计算 Azure 资源的任何内容。 Microsoft 无法访问客户数据，因此无法代表你对客户内容执行反恶意软件扫描。

**责任**：客户

**Azure 安全中心监视**：无

## <a name="incident-response"></a>事件响应

[有关详细信息，请参阅 *Azure 安全基线：* 事件响应](../benchmarks/security-control-incident-response.md)。

### <a name="101-create-an-incident-response-guide"></a>10.1：创建事件响应指导

**指南**：为组织制定事件响应指南。 确保在书面的事件响应计划中定义人员职责，以及事件处理/管理从检测到事件后审查的各个阶段。

- [如何在 Azure 安全中心配置工作流自动化](../../security-center/security-center-planning-and-operations-guide.md)

- [关于建立自己的安全事件响应流程的指南](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft 安全响应中心事件分析](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [客户还可以利用 NIST 的“计算机安全事件处理指南”来制定他们自己的事件响应计划](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2：创建事件评分和优先级设定过程

**指南**：安全中心向每个警报分配一个严重性，帮助你优先处理应首先调查的警报。 严重性取决于安全中心对调查结果或用于发出警报的指标的置信度，以及对导致警报的活动背后存在恶意意图的置信度级别。

此外，请明确标记订阅（例如 生产、非生产），并创建命名系统来对 Azure 资源进行明确标识和分类。

**责任**：客户

**Azure 安全中心监视**：无

### <a name="103-test-security-response-procedures"></a>10.3：测试安全响应过程

**指导**：定期执行演练来测试系统的事件响应功能。 识别弱点和差距，并根据需要修改计划。

- [请参阅 NIST 的刊物：Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)（IT 规划和功能的测试、培训与演练计划指南）

**责任**：客户

**Azure 安全中心监视**：无

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4：提供安全事件联系人详细信息，并针对安全事件配置警报通知

**指南**：如果 Microsoft 安全响应中心 (MSRC) 发现非法或未经授权的某方访问了客户的数据，Microsoft 将使用安全事件联系人信息与你取得联系。 事后审查事件，确保问题得到解决。

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

[有关详细信息，请参阅 *Azure 安全基线：* 渗透测试和红队演练](../benchmarks/security-control-penetration-tests-red-team-exercises.md)。

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1：定期对 Azure 资源执行渗透测试，确保修正所有发现的关键安全问题

**指导**：请遵循 Microsoft 云渗透测试互动规则，确保你的渗透测试不违反 Microsoft 政策。 使用 Microsoft 红队演练策略和执行，以及针对 Microsoft 托管云基础结构、服务和应用程序执行现场渗透测试。

- [参与的渗透测试规则](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Microsoft 云红色组合](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**责任**：共享

**Azure 安全中心监视**：无

## <a name="next-steps"></a>后续步骤

- 参阅 [Azure 安全基准 V2 概述](/azure/security/benchmarks/overview)
- 详细了解 [Azure 安全基线](/azure/security/benchmarks/security-baselines-overview)
