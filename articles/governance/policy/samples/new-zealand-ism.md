---
title: 限制性的新西兰 ISM 法规合规性详细信息
description: 限制性的新西兰 ISM 法规合规性内置计划的详细信息。 每个控制措施都映射到一个或多个协助评估的 Azure Policy 定义。
ms.date: 04/28/2021
ms.topic: sample
ms.custom: generated
ms.openlocfilehash: f7572f59ead4c14c3bb64da4f77fc7faa35947b0
ms.sourcegitcommit: 516eb79d62b8dbb2c324dff2048d01ea50715aa1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2021
ms.locfileid: "108179899"
---
# <a name="details-of-the-new-zealand-ism-restricted-regulatory-compliance-built-in-initiative"></a>限制性的新西兰 ISM 法规合规性内置计划的详细信息

下文详细说明 Azure Policy 法规合规性内置计划定义与限制性的新西兰 ISM 的合规性领域和控制措施的对应关系 。
有关此合规性标准的详细信息，请参阅[限制性的新西兰 ISM](https://www.nzism.gcsb.govt.nz/)。 如需了解所有权，请参阅 [Azure Policy 策略定义](../concepts/definition-structure.md#type)和[云中责任分担](../../../security/fundamentals/shared-responsibility.md)。

以下对应关系适用于限制性的新西兰 ISM 控制措施。 使用右侧的导航栏可直接跳转到特定的符合性域。 许多控制措施都是使用 [Azure Policy](../overview.md) 计划定义实现的。 若要查看完整计划定义，请在 Azure 门户中打开“策略”，并选择“定义”页 。
然后，找到并选择“受限于新西兰 ISM”的监管合规性内置计划定义。

此内置计划将部署为[限制性的新西兰 ISM 蓝图示例](../../blueprints/samples/new-zealand-ism.md)的一部分。

> [!IMPORTANT]
> 下面的每个控件都与一个或多个 [Azure Policy](../overview.md) 定义关联。
> 这些策略有助于[评估控制的合规性](../how-to/get-compliance-data.md)；但是，控制与一个或多个策略之间通常不是一对一或完全匹配。 因此，Azure Policy 中的符合性仅引用策略定义本身；这并不能确保你完全符合某个控制措施的所有要求。 此外，符合性标准包含目前未由任何 Azure Policy 定义处理的控件。 因此，Azure Policy 中的符合性只是整体符合性状态的部分视图。 此符合性标准的符合性域、控制措施和 Azure Policy 定义之间的关联可能会随着时间的推移而发生变化。 若要查看更改历史记录，请参阅 [GitHub 提交历史记录](https://github.com/Azure/azure-policy/commits/master/built-in-policies/policySetDefinitions/Regulatory%20Compliance/nz_ism.json)。

## <a name="information-security-monitoring"></a>信息安全监视

### <a name="625-conducting-vulnerability-assessments"></a>6.2.5 执行漏洞评估

**ID**：NZISM 安全基准 ISM-3 所有权：客户

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[应在虚拟机上启用漏洞评估解决方案](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F501541f7-f7e7-4cd6-868c-4190fdad3ac9) |审核虚拟机以检测其是否正在运行受支持的漏洞评估解决方案。 每个网络风险和安全计划的核心部分都是识别和分析漏洞。 Azure 安全中心的标准定价层包括对虚拟机进行漏洞扫描，无需额外付费。 此外，安全中心可以自动为你部署此工具。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ServerVulnerabilityAssessment_Audit.json) |
|[应从订阅中删除拥有所有者权限的已弃用帐户](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Febb62a0c-3560-49e1-89ed-27e074e9f8ad) |应从订阅中删除拥有所有者权限的已弃用帐户。  已弃用帐户是已阻止登录的帐户。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveDeprecatedAccountsWithOwnerPermissions_Audit.json) |
|[SQL 数据库应已解决漏洞发现](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffeedbf84-6b99-488c-acc2-71c829aa5ffc) |监视漏洞评估扫描结果，并提供有关如何消除数据库漏洞的建议。 |AuditIfNotExists、Disabled |[4.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_SQLDbVulnerabilities_Audit.json) |
|[应修正容器安全配置中的漏洞](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8cbc669-f12d-49eb-93e7-9273119e9933) |在安装了 Docker 的计算机上审核安全配置中的漏洞，并在 Azure 安全中心显示为建议。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ContainerBenchmark_Audit.json) |
|[应修复计算机上安全配置中的漏洞](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe1e5fd5d-3e4c-4ce1-8661-7d1873ae6b15) |建议通过 Azure 安全中心监视不满足配置的基线的服务器 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OSVulnerabilities_Audit.json) |
|[应修复虚拟机规模集上安全配置中的漏洞](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3c735d8a-a4ba-4a3a-b7cf-db7754cf57f4) |审核虚拟机规模集上的 OS 漏洞，以保护其免受攻击。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssOSVulnerabilities_Audit.json) |
|[SQL 服务器的漏洞评估设置应包含用来接收扫描报告的电子邮件地址](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F057d6cfe-9c4f-4a6d-bc60-14420ea1f1a9) |确保为漏洞评估设置中的“将扫描报告发送到”字段提供电子邮件地址。 在 SQL 服务器上运行定期扫描后，此电子邮件地址将收到扫描结果摘要。 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_VulnerabilityAssessmentEmails_Audit.json) |
|[应在 SQL 托管实例上启用漏洞评估](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1b7aa243-30e4-4c9e-bca8-d0d3022b634a) |审核未启用定期漏洞评估扫描的每个 SQL 托管实例。 漏洞评估可发现、跟踪和帮助你修正潜在数据库漏洞。 |AuditIfNotExists、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/VulnerabilityAssessmentOnManagedInstance_Audit.json) |
|[应对 SQL 服务器启用漏洞评估](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fef2a8f2a-b3d9-49cd-a8a8-9a3aaaf647d9) |审核未启用定期漏洞评估扫描的 Azure SQL 服务器。 漏洞评估可发现、跟踪和帮助你修正潜在数据库漏洞。 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/VulnerabilityAssessmentOnServer_Audit.json) |

## <a name="product-security"></a>产品安全性

### <a name="1244-patching-vulnerabilities-in-products"></a>12.4.4 修补产品中的漏洞

**ID**：NZISM 安全基准 PRS-5 所有权：客户

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[应从订阅中删除弃用的帐户](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6b1cbf55-e8b6-442f-ba4c-7246b6381474) |应从订阅中删除弃用的帐户。  已弃用帐户是已阻止登录的帐户。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveDeprecatedAccounts_Audit.json) |
|[应从订阅中删除拥有所有者权限的已弃用帐户](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Febb62a0c-3560-49e1-89ed-27e074e9f8ad) |应从订阅中删除拥有所有者权限的已弃用帐户。  已弃用帐户是已阻止登录的帐户。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveDeprecatedAccountsWithOwnerPermissions_Audit.json) |
|[应从订阅中删除拥有所有者权限的外部帐户](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8456c1c-aa66-4dfb-861a-25d127b775c9) |为了防止发生未受监视的访问，应从订阅中删除拥有所有者权限的外部帐户。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsWithOwnerPermissions_Audit.json) |
|[应从订阅中删除具有写入权限的外部帐户](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5c607a2e-c700-4744-8254-d77e7c9eb5e4) |应从订阅中删除拥有写入特权的外部帐户，以防发生未受监视的访问。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsWritePermissions_Audit.json) |
|[应在虚拟机规模集上安装系统更新](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc3f317a7-a95c-4547-b7e7-11017ebdf2fe) |审核是否缺少系统安全更新和关键更新，为了确保 Windows 和 Linux 虚拟机规模集的安全，应安装这些更新。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssMissingSystemUpdates_Audit.json) |
|[应在计算机上安装系统更新](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F86b3d65f-7626-441e-b690-81a8b71cff60) |建议通过 Azure 安全中心监视服务器上缺失的安全系统更新 |AuditIfNotExists、Disabled |[4.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingSystemUpdates_Audit.json) |

## <a name="software-security"></a>软件安全性

### <a name="1419-maintaining-hardened-soes"></a>14.1.9 维护强化的 SOE

**ID**：NZISM 安全基准 SS-2 所有权：客户

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[部署 - 将 Dependency Agent 配置为在 Windows 虚拟机规模集上启用](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3be22e3b-d919-47aa-805e-8985dbeb0ad9) |如果虚拟机映像在定义的列表中，并且未安装 Dependency Agent，则为 Windows 虚拟机规模集部署该代理。 如果规模集 upgradePolicy 设置为 Manual，你需要通过更新该规模集中的所有虚拟机将扩展应用到这些虚拟机。 |DeployIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/DependencyAgentExtension_Windows_VMSS_Deploy.json) |
|[应在虚拟机规模集上安装终结点保护解决方案](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F26a828e1-e88f-464e-bbb3-c134a282b9de) |审核终结点保护解决方案在虚拟机规模集上的存在性和运行状况 ，以保护其免受威胁和漏洞的侵害。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssMissingEndpointProtection_Audit.json) |
|[应在 Windows Server 上部署 Microsoft IaaSAntimalware 扩展](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9b597639-28e4-48eb-b506-56b05d366257) |此策略会审核所有未部署 Microsoft IaaSAntimalware 扩展的 Windows Server VM。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/WindowsServers_AntiMalware_AuditIfNotExists.json) |
|[监视 Azure 安全中心 Endpoint Protection 的缺失情况](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faf6cd1bd-1635-48cb-bde7-5b15693900b9) |建议通过 Azure 安全中心监视未安装 Endpoint Protection 代理的服务器 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingEndpointProtection_Audit.json) |

### <a name="1424-application-whitelisting"></a>14.2.4 应用程序允许列表

**ID**：NZISM 安全基准 SS-4 所有权：客户

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[应在计算机中启用自适应应用程序控制以定义安全应用程序](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47a6b606-51aa-4496-8bb7-64b11cf66adc) |启用应用程序控制，以定义计算机中正在运行的已知安全应用程序列表，并在其他应用程序运行时向你发出警报。 这有助于强化计算机免受恶意软件的侵害。 为了简化配置和维护规则的过程，安全中心使用机器学习来分析在每台计算机上运行的应用程序，并建议已知安全应用程序的列表。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveApplicationControls_Audit.json) |

### <a name="1458-web-applications"></a>14.5.8 Web 应用程序

**ID**：NZISM 安全基准 SS-8 所有权：客户

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[只能通过 HTTPS 访问 API 应用](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb7ddfbdc-1260-477d-91fd-98bd9be789a6) |使用 HTTPS 可确保执行服务器/服务身份验证服务，并保护传输中的数据不受网络层窃听攻击威胁。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceApiApp_AuditHTTP_Audit.json) |
|[CORS 不应允许所有资源都能访问你的 Web 应用程序](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5744710e-cc2f-4ee8-8809-3b11e89f4bc9) |跨源资源共享 (CORS) 不应允许所有域都能访问你的 Web 应用程序。 仅允许所需的域与 Web 应用交互。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RestrictCORSAccess_WebApp_Audit.json) |
|[应该只能通过 HTTPS 访问函数应用](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d555dd1-86f2-4f1c-8ed7-5abae7c6cbab) |使用 HTTPS 可确保执行服务器/服务身份验证服务，并保护传输中的数据不受网络层窃听攻击威胁。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceFunctionApp_AuditHTTP_Audit.json) |
|[只能与 Azure Cache for Redis 建立安全连接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |审核是否仅启用通过 SSL 来与 Azure Redis 缓存建立连接。 使用安全连接可确保服务器和服务之间的身份验证并保护传输中的数据免受中间人攻击、窃听攻击和会话劫持等网络层攻击 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|[只能通过 HTTPS 访问 Web 应用程序](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |使用 HTTPS 可确保执行服务器/服务身份验证服务，并保护传输中的数据不受网络层窃听攻击威胁。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |

## <a name="access-control-and-passwords"></a>访问控制和密码

### <a name="16132-system-user-identification"></a>16.1.32 系统用户标识

**ID**：NZISM 安全基准 AC-2 所有权：客户

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[只多只为订阅指定 3 个所有者](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f11b553-d42e-4e3a-89be-32ca364cad4c) |建议最多指定 3 个订阅所有者，以减少可能出现的已遭入侵的所有者做出的违规行为。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateLessThanXOwners_Audit.json) |
|[审核允许通过没有密码的帐户进行远程连接的 Linux 计算机](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fea53dbee-c6c9-4f0e-9f9e-de0039b78023) |要求将先决条件部署到策略分配范围。 有关详细信息，请访问 [https://aka.ms/gcpol](https://aka.ms/gcpol)。 如果 Linux 计算机允许通过没有密码的帐户进行远程连接，则计算机不合规 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_LinuxPassword110_AINE.json) |
|[审核具有不使用密码的帐户的 Linux 计算机](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6ec09a3-78bf-4f8f-99dc-6c77182d0f99) |要求将先决条件部署到策略分配范围。 有关详细信息，请访问 [https://aka.ms/gcpol](https://aka.ms/gcpol)。 如果 Linux 计算机具有不使用密码的帐户，则计算机不合规 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_LinuxPassword232_AINE.json) |
|[审核缺少管理员组中任何指定成员的 Windows 计算机](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F30f71ea1-ac77-4f26-9fc5-2d926bbd4ba7) |要求将先决条件部署到策略分配范围。 有关详细信息，请访问 [https://aka.ms/gcpol](https://aka.ms/gcpol)。 如果本地管理员组不包含策略参数中列出的一个或多个成员，则计算机不合规。 |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembersToInclude_AINE.json) |
|[审核管理员组中具有额外帐户的 Windows 计算机](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3d2a3320-2a72-4c67-ac5f-caa40fbee2b2) |要求将先决条件部署到策略分配范围。 有关详细信息，请访问 [https://aka.ms/gcpol](https://aka.ms/gcpol)。 如果本地管理员组包含策略参数中未列出的成员，则计算机不合规。 |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembers_AINE.json) |
|[审核具有管理员组中指定成员的 Windows 计算机](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F69bf4abd-ca1e-4cf6-8b5a-762d42e61d4f) |要求将先决条件部署到策略分配范围。 有关详细信息，请访问 [https://aka.ms/gcpol](https://aka.ms/gcpol)。 如果本地管理员组包含策略参数中列出的一个或多个成员，则计算机不合规。 |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembersToExclude_AINE.json) |
|[部署 - 将 Dependency Agent 配置为在 Windows 虚拟机上启用](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1c210e94-a481-4beb-95fa-1571b434fb04) |如果虚拟机映像在定义的列表中，并且未安装 Dependency Agent，则为 Windows 虚拟机部署该代理。 |DeployIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/DependencyAgentExtension_Windows_VM_Deploy.json) |
|[部署 Linux 来宾配置扩展以在 Linux VM 上启用来宾配置分配](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F331e8ea8-378a-410f-a2e5-ae22f38bb0da) |此策略将 Linux 来宾配置扩展部署到托管在 Azure 中受来宾配置支持的 Linux 虚拟机。 Linux 来宾配置扩展是所有 Linux 来宾配置分配的先决条件，在使用任何 Linux 来宾配置策略定义之前必须被部署到计算机。 有关来宾配置的详细信息，请访问 [https://aka.ms/gcpol](https://aka.ms/gcpol)。 |deployIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_DeployExtensionLinux_Prerequisite.json) |
|[部署 Windows 来宾配置扩展以在 Windows VM 上启用来宾配置分配](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F385f5831-96d4-41db-9a3c-cd3af78aaae6) |此策略将 Windows 来宾配置扩展部署到托管在 Azure 中受来宾配置支持的 Windows 虚拟机。 Windows 来宾配置扩展是所有 Windows 来宾配置分配的先决条件，在使用任何 Windows 来宾配置策略定义之前必须被部署到计算机。 有关来宾配置的详细信息，请访问 [https://aka.ms/gcpol](https://aka.ms/gcpol)。 |deployIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_DeployExtensionWindows_Prerequisite.json) |
|[Service Fabric 群集应仅使用 Azure Active Directory 进行客户端身份验证](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb54ed75b-3e1a-44ac-a333-05ba39b99ff0) |审核 Service Fabric 中仅通过 Azure Active Directory 进行客户端身份验证 |Audit、Deny、Disabled |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditADAuth_Audit.json) |
|[应为订阅分配了多个所有者](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F09024ccc-0c5f-475e-9457-b7c0d9ed487b) |建议指定多个订阅所有者，这样才会有管理员访问冗余。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateMoreThanOneOwner_Audit.json) |

### <a name="16135-methods-for-system-user-identification-and-authentication"></a>16.1.35 用于系统用户标识和身份验证的方法

**ID**：NZISM 安全基准 AC-3 所有权：客户

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[应对订阅中拥有写入权限的帐户启用 MFA](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9297c21d-2ed6-4474-b48f-163f75654ce3) |为了防止帐户或资源出现违规问题，应为所有拥有写入特权的订阅帐户启用多重身份验证 (MFA)。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForWritePermissions_Audit.json) |
|[应在对订阅拥有所有者权限的帐户上启用 MFA](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faa633080-8b72-40c4-a2d7-d00c03e80bed) |为了防止帐户或资源出现违规问题，应为所有拥有所有者权限的订阅帐户启用多重身份验证 (MFA)。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForOwnerPermissions_Audit.json) |
|[应在对订阅拥有读取权限的帐户上启用 MFA](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe3576e28-8b17-4677-84c3-db2990658d64) |为了防止帐户或资源出现违规问题，应为所有拥有读取特权的订阅帐户启用多重身份验证 (MFA)。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForReadPermissions_Audit.json) |

### <a name="16140-password-selection-policy"></a>16.1.40 密码选择策略

**ID**：NZISM 安全基准 AC-4 所有权：客户

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[部署 - 将 Dependency Agent 配置为在 Windows 虚拟机上启用](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1c210e94-a481-4beb-95fa-1571b434fb04) |如果虚拟机映像在定义的列表中，并且未安装 Dependency Agent，则为 Windows 虚拟机部署该代理。 |DeployIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/DependencyAgentExtension_Windows_VM_Deploy.json) |
|[Windows 计算机应符合“安全设置 - 帐户策略”的要求](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff2143251-70de-4e81-87a8-36cee5a2f29d) |对于密码历史记录、使用期限、长度、复杂性以及使用可还原加密存储密码，Windows 计算机应在“安全设置 - 帐户策略”类别中具有指定的组策略设置。 此策略要求来宾配置先决条件已部署到策略分配范围。 有关详细信息，请访问 [https://aka.ms/gcpol](https://aka.ms/gcpol)。 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SecuritySettingsAccountPolicies_AINE.json) |

### <a name="16146-suspension-of-access"></a>16.1.46 暂停访问

**ID**：NZISM 安全基准 AC-5 所有权：客户

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[部署 - 将 Dependency Agent 配置为在 Windows 虚拟机上启用](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1c210e94-a481-4beb-95fa-1571b434fb04) |如果虚拟机映像在定义的列表中，并且未安装 Dependency Agent，则为 Windows 虚拟机部署该代理。 |DeployIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/DependencyAgentExtension_Windows_VM_Deploy.json) |
|[应从订阅中删除弃用的帐户](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6b1cbf55-e8b6-442f-ba4c-7246b6381474) |应从订阅中删除弃用的帐户。  已弃用帐户是已阻止登录的帐户。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveDeprecatedAccounts_Audit.json) |
|[应从订阅中删除拥有所有者权限的已弃用帐户](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Febb62a0c-3560-49e1-89ed-27e074e9f8ad) |应从订阅中删除拥有所有者权限的已弃用帐户。  已弃用帐户是已阻止登录的帐户。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveDeprecatedAccountsWithOwnerPermissions_Audit.json) |

### <a name="1625-protecting-compartmented-information-on-systems"></a>16.2.5 保护系统上设置了隔离舱的信息

**ID**：NZISM 安全基准 AC-7 所有权：客户

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[应通过即时网络访问控制来保护虚拟机的管理端口](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |建议通过 Azure 安全中心监视可能的网络适时 (JIT) 访问 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |
|[应为 API 应用禁用远程调试](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe9c8d085-d9cc-4b17-9cdc-059f1f01f19e) |远程调试需要在 API 应用上打开入站端口。 应禁用远程调试。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_ApiApp_Audit.json) |
|[应对函数应用禁用远程调试](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e60b895-3786-45da-8377-9c6b4b6ac5f9) |远程调试需要在函数应用上打开入站端口。 应禁用远程调试。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_FunctionApp_Audit.json) |
|[应禁用 Web 应用程序的远程调试](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcb510bfd-1cba-4d9f-a230-cb0976f4bb71) |远程调试需要在 Web 应用程序上打开入站端口。 应禁用远程调试。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_WebApp_Audit.json) |

### <a name="1635-use-of-privileged-accounts"></a>16.3.5 使用特权帐户

**ID**：NZISM 安全基准 AC-9 所有权：客户

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[审核缺少管理员组中任何指定成员的 Windows 计算机](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F30f71ea1-ac77-4f26-9fc5-2d926bbd4ba7) |要求将先决条件部署到策略分配范围。 有关详细信息，请访问 [https://aka.ms/gcpol](https://aka.ms/gcpol)。 如果本地管理员组不包含策略参数中列出的一个或多个成员，则计算机不合规。 |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembersToInclude_AINE.json) |
|[审核管理员组中具有额外帐户的 Windows 计算机](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3d2a3320-2a72-4c67-ac5f-caa40fbee2b2) |要求将先决条件部署到策略分配范围。 有关详细信息，请访问 [https://aka.ms/gcpol](https://aka.ms/gcpol)。 如果本地管理员组包含策略参数中未列出的成员，则计算机不合规。 |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembers_AINE.json) |
|[审核具有管理员组中指定成员的 Windows 计算机](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F69bf4abd-ca1e-4cf6-8b5a-762d42e61d4f) |要求将先决条件部署到策略分配范围。 有关详细信息，请访问 [https://aka.ms/gcpol](https://aka.ms/gcpol)。 如果本地管理员组包含策略参数中列出的一个或多个成员，则计算机不合规。 |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembersToExclude_AINE.json) |
|[部署 - 将 Dependency Agent 配置为在 Windows 虚拟机上启用](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1c210e94-a481-4beb-95fa-1571b434fb04) |如果虚拟机映像在定义的列表中，并且未安装 Dependency Agent，则为 Windows 虚拟机部署该代理。 |DeployIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/DependencyAgentExtension_Windows_VM_Deploy.json) |
|[部署 Windows 来宾配置扩展以在 Windows VM 上启用来宾配置分配](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F385f5831-96d4-41db-9a3c-cd3af78aaae6) |此策略将 Windows 来宾配置扩展部署到托管在 Azure 中受来宾配置支持的 Windows 虚拟机。 Windows 来宾配置扩展是所有 Windows 来宾配置分配的先决条件，在使用任何 Windows 来宾配置策略定义之前必须被部署到计算机。 有关来宾配置的详细信息，请访问 [https://aka.ms/gcpol](https://aka.ms/gcpol)。 |deployIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_DeployExtensionWindows_Prerequisite.json) |

### <a name="1666-maintaining-system-management-logs"></a>16.6.6 维护系统管理日志

**ID**：NZISM 安全基准 AC-13 所有权：客户

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure 订阅应有用于活动日志的日志配置文件](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7796937f-307b-4598-941c-67d3a05ebfe7) |此策略确保启用一个日志配置文件来导出活动日志。 它会审核是否未创建日志配置文件将日志导出到存储帐户或事件中心。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/Logprofile_activityLogs_Audit.json) |

### <a name="1667-content-of-system-management-logs"></a>16.6.7 系统管理日志的内容

**ID**：NZISM 安全基准 AC-14 所有权：客户

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[审核诊断设置](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7f89b1eb-583c-429a-8828-af049802c1d9) |审核所选资源类型的诊断设置。 |AuditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/DiagnosticSettingsForTypes_Audit.json) |
|[审核 VM 的 Log Analytics 工作区 — 报告不匹配](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff47b5582-33ec-4c5c-87c0-b010a6b2e917) |如果 VM 未记录到策略/计划分配中指定的 Log Analytics 工作区，则将 VM 报告为“不合规”。 |审核 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/LogAnalytics_WorkspaceMismatch_VM_Audit.json) |
|[应为列出的虚拟机映像启用 Log Analytics 代理](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F32133ab0-ee4b-4b44-98d6-042180979d50) |如果虚拟机映像不在定义的列表中且代理未安装，则将虚拟机报告为“不合规”。 |AuditIfNotExists、Disabled |[2.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/LogAnalytics_OSImage_Audit.json) |
|[应在虚拟机规模集中为列出的虚拟机映像启用 Log Analytics 代理](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5c3bc7b8-a64c-4e08-a9cd-7ff0f31e1138) |如果虚拟机映像不在定义的列表中，并且未安装该代理，则会将虚拟机规模集报告为不合规。 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/LogAnalytics_OSImage_VMSS_Audit.json) |

### <a name="16613-event-log-archives"></a>16.6.13 事件日志存档

**ID**：NZISM 安全基准 AC-15 所有权：客户

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[活动日志至少应保留一年](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb02aacc0-b073-424e-8298-42b22829ee0a) |此策略审核活动日志的保留期是否未设置为365 天或永久（保留天数设置为 0）。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLogRetention_365orGreater.json) |

### <a name="16734-system-architecture-and-security-controls"></a>16.7.34 系统体系结构和安全控制

**ID**：NZISM 安全基准 AC-17 所有权：客户

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[应对订阅中拥有写入权限的帐户启用 MFA](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9297c21d-2ed6-4474-b48f-163f75654ce3) |为了防止帐户或资源出现违规问题，应为所有拥有写入特权的订阅帐户启用多重身份验证 (MFA)。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForWritePermissions_Audit.json) |
|[应在对订阅拥有所有者权限的帐户上启用 MFA](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faa633080-8b72-40c4-a2d7-d00c03e80bed) |为了防止帐户或资源出现违规问题，应为所有拥有所有者权限的订阅帐户启用多重身份验证 (MFA)。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForOwnerPermissions_Audit.json) |
|[应在对订阅拥有读取权限的帐户上启用 MFA](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe3576e28-8b17-4677-84c3-db2990658d64) |为了防止帐户或资源出现违规问题，应为所有拥有读取特权的订阅帐户启用多重身份验证 (MFA)。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForReadPermissions_Audit.json) |

## <a name="cryptography"></a>密码

### <a name="17146-reducing-storage-and-physical-transfer-requirements"></a>17.1.46 降低存储和物理传输要求

**ID**：NZISM 安全基准 CR-2 所有权：客户

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[应在虚拟机上应用磁盘加密](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0961003e-5a0a-4549-abde-af6a37f2724d) |Azure 安全中心建议对未启用磁盘加密的虚拟机进行监视。 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UnencryptedVMDisks_Audit.json) |

### <a name="17416-using-tls"></a>17.4.16 使用 TLS

**ID**：NZISM 安全基准 CR-6 所有权：客户

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[部署 Windows 来宾配置扩展以在 Windows VM 上启用来宾配置分配](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F385f5831-96d4-41db-9a3c-cd3af78aaae6) |此策略将 Windows 来宾配置扩展部署到托管在 Azure 中受来宾配置支持的 Windows 虚拟机。 Windows 来宾配置扩展是所有 Windows 来宾配置分配的先决条件，在使用任何 Windows 来宾配置策略定义之前必须被部署到计算机。 有关来宾配置的详细信息，请访问 [https://aka.ms/gcpol](https://aka.ms/gcpol)。 |deployIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_DeployExtensionWindows_Prerequisite.json) |
|[应在 API 应用中使用最新的 TLS 版本](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8cb6aa8b-9e41-4f4e-aa25-089a7ac2581e) |升级到最新的 TLS 版本 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_ApiApp_Audit.json) |
|[应在函数应用中使用最新的 TLS 版本](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9d614c5-c173-4d56-95a7-b4437057d193) |升级到最新的 TLS 版本 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_FunctionApp_Audit.json) |
|[应在 Web 应用中使用最新的 TLS 版本](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff0e6e85b-9b9f-4a4b-b67b-f730d42f1b0b) |升级到最新的 TLS 版本 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_WebApp_Audit.json) |
|[应将 Windows Web 服务器配置为使用安全通信协议](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5752e6d6-1206-46d8-8ab1-ecc2f71a8112) |为了保护通过 Internet 进行通信的信息的隐私，Web 服务器应使用最新版本的行业标准加密协议，即传输层安全性 (TLS)。 TLS 使用安全证书对计算机之间的连接进行加密来保护网络上的通信。 |AuditIfNotExists、Disabled |[2.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SecureWebProtocol_AINE.json) |

## <a name="network-security"></a>网络安全性

### <a name="18113-limiting-network-access"></a>18.1.13 限制网络访问

**ID**：NZISM 安全基准 NS-2 所有权：客户

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[应在面向 Internet 的虚拟机上应用自适应网络强化建议](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08e6af2d-db70-460a-bfe9-d5bd474ba9d6) |Azure 安全中心会分析面向虚拟机的 Internet 的流量模式，并提供可减小潜在攻击面的网络安全组规则建议 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveNetworkHardenings_Audit.json) |
|[面向 Internet 的虚拟机应使用网络安全组进行保护](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |使用网络安全组 (NSG) 限制对 VM 的访问，以此防范 VM 遭受潜在威胁。 如需详细了解如何使用 NSG 控制流量，请访问 [https://aka.ms/nsg-doc](https://aka.ms/nsg-doc) |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[应禁止存储帐户公共访问](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4fa4b6c0-31ca-4c0d-b10d-24b96f62a751) |对 Azure 存储中的容器和 blob 进行匿名公共读取访问虽然是共享数据的一种简便方法，但可能会带来安全风险。 为了防止不希望的匿名访问导致数据泄露，Microsoft 建议禁止对存储帐户的公共访问，除非你的方案需要。 |审核、拒绝、已禁用 |[2.0.1-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/ASC_Storage_DisallowPublicBlobAccess_Audit.json) |
|[应限制对存储帐户的网络访问](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34c877ad-507e-4c82-993e-3452a6e0ad3c) |应限制对存储帐户的网络访问。 配置网络规则，以便只允许来自允许的网络的应用程序访问存储帐户。 若要允许来自特定 Internet 或本地客户端的连接，可以向来自特定 Azure 虚拟网络的流量或公共 Internet IP 地址范围授予访问权限 |Audit、Deny、Disabled |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_NetworkAcls_Audit.json) |

### <a name="18319-content-of-a-denial-of-service-dos-response-plan"></a>18.3.19 拒绝服务 (DoS) 响应计划的内容

**ID**：NZISM 安全基准 NS-5 所有权：客户

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[应启用 Azure DDoS 防护标准](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa7aca53f-2ed4-4466-a25e-0b45ade68efd) |应为属于应用程序网关且具有公共 IP 子网的所有虚拟网络启用 DDoS 保护标准。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableDDoSProtection_Audit.json) |

### <a name="18410-malicious-code-counter-measures"></a>18.4.10 恶意代码的应对措施

**ID**：NZISM 安全基准 NS-7 所有权：客户

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[所有 Internet 流量都应通过所部署的 Azure 防火墙进行路由](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffc5e4038-4584-4632-8c85-c0448d374b2c) |Azure 安全中心已确认，你的某些子网未使用下一代防火墙进行保护。 通过使用 Azure 防火墙或受支持的下一代防火墙限制对子网的访问，保护子网免受潜在威胁的危害 |AuditIfNotExists、Disabled |[3.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ASC_All_Internet_traffic_should_be_routed_via_Azure_Firewall.json) |
|[应为应用程序网关启用 Web 应用程序防火墙 (WAF)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F564feb30-bf6a-4854-b4bb-0d2d2d1e6c66) |将 Azure Web 应用程序防火墙 (WAF) 部署在面向公众的 Web 应用程序的前面，以便对传入流量进行额外检查。 Web 应用程序防火墙 (WAF) 为 Web 应用程序提供集中保护，使其免受常见攻击和漏洞的侵害，例如 SQL 注入、跨站脚本以及本地和远程文件执行。 还可以通过自定义规则，按国家/地区、IP 地址范围和其他 http(s) 参数限制对 Web 应用程序的访问。 |Audit、Deny、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/WAF_AppGatewayEnabled_Audit.json) |
|[应为 Azure Front Door 服务启用 Web 应用程序防火墙 (WAF)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F055aa869-bc98-4af8-bafc-23f1ab6ffe2c) |将 Azure Web 应用程序防火墙 (WAF) 部署在面向公众的 Web 应用程序的前面，以便对传入流量进行额外检查。 Web 应用程序防火墙 (WAF) 为 Web 应用程序提供集中保护，使其免受常见攻击和漏洞的侵害，例如 SQL 注入、跨站脚本以及本地和远程文件执行。 还可以通过自定义规则，按国家/地区、IP 地址范围和其他 http(s) 参数限制对 Web 应用程序的访问。 |Audit、Deny、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/WAF_AFD_Enabled_Audit.json) |
|[Web 应用程序防火墙 (WAF) 应对应用程序网关使用指定模式](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F12430be1-6cc8-4527-a9a8-e3d38f250096) |要求对应用程序网关的所有 Web 应用程序防火墙策略启用“检测”或“防护”模式。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/WAF_AppGatewayMode_Audit.json) |
|[Web 应用程序防火墙 (WAF) 应对 Azure Front Door 服务使用指定模式](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F425bea59-a659-4cbb-8d31-34499bd030b8) |要求对 Azure Front Door 服务的所有 Web 应用程序防火墙策略启用“检测”或“防护”模式。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/WAF_AFD_Mode_Audit.json) |

## <a name="data-management"></a>数据管理

### <a name="20310-antivirus-scans"></a>20.3.10 防病毒扫描

**ID**：NZISM 安全基准 DM-4 所有权：客户

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[部署 - 将 Dependency Agent 配置为在 Windows 虚拟机规模集上启用](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3be22e3b-d919-47aa-805e-8985dbeb0ad9) |如果虚拟机映像在定义的列表中，并且未安装 Dependency Agent，则为 Windows 虚拟机规模集部署该代理。 如果规模集 upgradePolicy 设置为 Manual，你需要通过更新该规模集中的所有虚拟机将扩展应用到这些虚拟机。 |DeployIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/DependencyAgentExtension_Windows_VMSS_Deploy.json) |
|[应在虚拟机规模集上安装终结点保护解决方案](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F26a828e1-e88f-464e-bbb3-c134a282b9de) |审核终结点保护解决方案在虚拟机规模集上的存在性和运行状况 ，以保护其免受威胁和漏洞的侵害。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssMissingEndpointProtection_Audit.json) |
|[监视 Azure 安全中心 Endpoint Protection 的缺失情况](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faf6cd1bd-1635-48cb-bde7-5b15693900b9) |建议通过 Azure 安全中心监视未安装 Endpoint Protection 代理的服务器 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingEndpointProtection_Audit.json) |
|[应在计算机上启用 Windows Defender 攻击防护](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbed48b13-6647-468e-aa2f-1af1d3f4dd40) |Windows Defender 攻击防护使用 Azure Policy 来宾配置代理。 攻击防护服务具有 4 个组件，旨在锁定设备来阻隔各种攻击途径，并阻止恶意软件攻击中常用的行为，同时让企业能够平衡其安全风险和生产力要求（仅限 Windows）。 |AuditIfNotExists、Disabled |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsDefenderExploitGuard_AINE.json) |

### <a name="2044-database-files"></a>20.4.4 数据库文件

**ID**：NZISM 安全基准 DM-6 所有权：客户

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[应在 SQL 托管实例上启用高级数据安全](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb7388-5bf4-4ad7-ba99-2cd2f41cebb9) |审核所有未启用高级数据安全的 SQL 托管实例。 |AuditIfNotExists、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_AdvancedDataSecurity_Audit.json) |
|[应在 SQL 服务器上启用高级数据安全性](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb4388-5bf4-4ad7-ba82-2cd2f41ceae9) |审核没有高级数据安全的 SQL 服务器 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_AdvancedDataSecurity_Audit.json) |
|[应该为 SQL 服务器预配 Azure Active Directory 管理员](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1f314764-cb73-4fc9-b863-8eca98ac36e9) |审核确认已为 SQL Server 预配了 Azure Active Directory 管理员以启用 Azure AD 身份验证。 使用 Azure AD 身份验证可以简化权限管理，以及集中化数据库用户和其他 Microsoft 服务的标识管理 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SQL_DB_AuditServerADAdmins_Audit.json) |
|[审核诊断设置](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7f89b1eb-583c-429a-8828-af049802c1d9) |审核所选资源类型的诊断设置。 |AuditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/DiagnosticSettingsForTypes_Audit.json) |
|[应在虚拟机上应用磁盘加密](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0961003e-5a0a-4549-abde-af6a37f2724d) |Azure 安全中心建议对未启用磁盘加密的虚拟机进行监视。 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UnencryptedVMDisks_Audit.json) |
|[只能与 Azure Cache for Redis 建立安全连接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |审核是否仅启用通过 SSL 来与 Azure Redis 缓存建立连接。 使用安全连接可确保服务器和服务之间的身份验证并保护传输中的数据免受中间人攻击、窃听攻击和会话劫持等网络层攻击 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|[应启用安全传输到存储帐户](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |审核存储帐户中安全传输的要求。 安全传输选项会强制存储帐户仅接受来自安全连接 (HTTPS) 的请求。 使用 HTTPS 可确保服务器和服务之间的身份验证并保护传输中的数据免受中间人攻击、窃听和会话劫持等网络层攻击 |Audit、Deny、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |
|[应在 SQL 数据库上启用透明数据加密](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F17k78e20-9358-41c9-923c-fb736d382a12) |应启用透明数据加密以保护静态数据并满足符合性要求 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlDBEncryption_Audit.json) |
|[应将 Windows Web 服务器配置为使用安全通信协议](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5752e6d6-1206-46d8-8ab1-ecc2f71a8112) |为了保护通过 Internet 进行通信的信息的隐私，Web 服务器应使用最新版本的行业标准加密协议，即传输层安全性 (TLS)。 TLS 使用安全证书对计算机之间的连接进行加密来保护网络上的通信。 |AuditIfNotExists、Disabled |[2.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SecureWebProtocol_AINE.json) |

## <a name="enterprise-systems-security"></a>企业系统安全性

### <a name="22126-backup-recovery-archiving-and-data-remanence"></a>22.1.26 备份、恢复存档和数据残留

**ID**：NZISM 安全基准 ESS-3 所有权：客户

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[审核未配置灾难恢复的虚拟机](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0015ea4d-51ff-4ce3-8d8c-f3f8f0179a56) |审核未配置灾难恢复的虚拟机。 若要详细了解灾难恢复，请访问 [https://aka.ms/asr-doc](https://aka.ms/asr-doc)。 |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/RecoveryServices_DisasterRecovery_Audit.json) |

> [!NOTE]
> 特定 Azure Policy 定义的可用性在 Azure 政府和其他国家云中可能会有所不同。

## <a name="next-steps"></a>后续步骤

有关 Azure Policy 的其他文章：

- [法规符合性](../concepts/regulatory-compliance.md)概述。
- 请参阅[计划定义结构](../concepts/initiative-definition-structure.md)。
- 在 [Azure Policy 示例](./index.md)中查看其他示例。
- 查看[了解策略效果](../concepts/effects.md)。
- 了解如何[修正不符合的资源](../how-to/remediate-resources.md)。
