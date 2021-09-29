---
title: Azure Active Directory 应用程序安全操作
description: 了解如何监视应用程序并向其发出警报来识别安全威胁。
services: active-directory
author: BarbaraSelden
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 07/15/2021
ms.author: baselden
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1a03b30c1e2628342246430089e3c6fbb47c98a7
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124754342"
---
# <a name="azure-active-directory-security-operations-guide-for-applications"></a>Azure Active Directory 应用程序安全操作指南

应用程序会受到安全漏洞攻击，因此必须对应用程序进行监视。 虽然没有像用户账户那样经常成为攻击目标，但也会发生违规情况。 由于应用程序常在无人工干预的情况下运行，因此可能更难检测攻击行为。

本文将指导如何监视应用程序事件并对其发出警报。 它会定期更新，以帮助确保：

* 防止恶意应用程序在未经授权的情况下访问数据。

* 防止现有应用程序遭不良执行组件入侵。

* 收集见解，使你能够更安全地构建和配置新的应用程序。

如果不熟悉应用程序在 Azure Active Directory (Azure AD) 中的工作原理，请参阅 [Azure AD 中的应用和服务主体](../develop/app-objects-and-service-principals.md)。

> [!NOTE]
> 如果尚未查看 [Azure Active Directory 安全操作概述](security-operations-introduction.md)，可以考虑现在前往查看。

## <a name="what-to-look-for"></a>要查找什么

监视应用程序日志中的安全事件时，请查看以下各项以帮助区分正常活动和恶意活动。 以下事件可能表明存在安全问题，本文接下来将逐一介绍。

* 在正常业务流程和计划之外发生的任何更改。

* 应用程序凭据更改

* 应用程序权限

   * 分配到 Azure AD 或 Azure RBAC 角色的服务主体。

   * 被授予高特权权限的应用程序。

   * Azure Key Vault 更改。

   * 最终用户授予应用程序许可。

   * 根据风险级别阻止最终用户同意。

* 应用程序配置更改

   * 统一资源标识符 (URI) 更改或非标准。

   * 应用程序所有者更改。

   * 注销 URL 已修改。

## <a name="where-to-look"></a>要查阅的内容

用于调查和监视的日志文件包括：

* [Azure AD 审核日志](../reports-monitoring/concept-audit-logs.md)

* [登录日志](../reports-monitoring/concept-all-sign-ins.md)

* [Microsoft 365 审核日志](/microsoft-365/compliance/auditing-solutions-overview)

* [Azure Key Vault 日志](../../key-vault/general/logging.md)

在 Azure 门户中，你可以查看 Azure AD 审核日志，并以逗号分隔值 (CSV) 或 JavaScript 对象表示法 (JSON) 文件格式下载。 Azure 门户提供多种方法将 Azure AD 日志与其他便于更好地自动执行监视和警报的工具集成：

* [Azure Sentinel](../../sentinel/overview.md) - 通过提供安全信息和事件管理 (SIEM) 功能，实现企业级智能安全分析。 

* [Azure Monitor](../../azure-monitor/overview.md) – 实现对各种情况的自动监视和警报。 可以创建或使用工作簿来合并不同源的数据。

* **与 SIEM 集成的 [Azure 事件中心](../../event-hubs/event-hubs-about.md)** -  通过 Azure 事件中心集成 [可将 Azure AD 日志集成到其他 SIEM](../reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md)，例如 Splunk、ArcSight、QRadar 和 Sumo Logic。

* **[Microsoft Cloud App Security](/cloud-app-security/what-is-cloud-app-security) (MCAS)** – 支持发现和管理应用、跨应用和资源进行治理以及检查云应用合规性。

大部分你将监视和发出警报的内容由条件访问策略决定。 可以使用[条件访问见解和报告工作簿](../conditional-access/howto-conditional-access-insights-reporting.md)来检查一个或多个条件访问策略对登录的影响以及包括设备状态在内的策略结果。 可通过此工作簿查看影响摘要并确定特定时间段的影响。 还可以使用此工作簿来调查特定用户的登录情况。 

 本文接下来将按威胁类型介绍应该监视和发出相关警报的对象。 如果有特定预生成解决方案，我们会链接到这些解决方案，或在表格之后提供示例。 否则，可以使用前面的工具来生成警报。 

## <a name="application-credentials"></a>应用程序凭据

许多应用程序使用凭据在 Azure AD 中进行身份验证。 在预期进程外额外添加的任何凭据都可能是使用这些凭据的恶意执行组件。 强烈建议使用受信任的颁发机构或托管标识颁发的 X509 证书，而不是使用客户端密码。 但是，如果需要使用客户端密码，请遵循良好的安全做法，以确保应用程序安全。 请注意，应用程序和服务主体更新会在审核日志中记录为两个条目。 

* 监视应用程序以识别凭据早已过期的应用程序。

* 将长期凭据替换为短期凭据。 采取措施以确保不向代码存储库提交凭据并将其安全存储。


| 要监视的内容| 风险级别| Where| 筛选器/子筛选器| 说明 |
| -|-|-|-|-|
| 向现有应用程序添加的凭据| 高| Azure AD 审核日志| Service-Core Directory、Category-ApplicationManagement <br>活动：更新应用程序证书和密码管理<br>－和－<br>活动：更新服务主体/更新应用程序| 当凭据为以下情况时发出警报：<li> 在正常工作时间或工作流之外添加。<li> 并非环境中使用的类型。<li> 添加到支持服务主体的非 SAML 流中。 |
| 生存期超出策略允许时间的凭据。| 中等| Microsoft Graph| 应用程序密钥凭据的状态和结束日期<br>－和－<br>应用程序密码凭据| 可以使用 MS Graph API 来查找凭据的开始和结束日期，并评估比允许的生存期还久的凭据。 请参阅此表后面的 PowerShell 脚本。 |

 提供以下预生成的监视和警报。

* Azure Sentinel – [添加新应用或服务主体凭据时发出警报](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/AuditLogs/NewAppOrServicePrincipalCredential.yaml) 

* Azure Monitor – [Azure AD 工作簿帮助评估 Solorigate 风险 - Microsoft 技术社区](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/azure-ad-workbook-to-help-you-assess-solorigate-risk/ba-p/2010718)

* MCAS – [云应用安全异常情况检测警报调查指南](/cloud-app-security/investigate-anomaly-alerts)

* PowerShell - [用于查找凭据生存期的 Powershell 脚本示例](https://github.com/madansr7/appCredAge)。

## <a name="application-permissions"></a>应用程序权限

与管理员帐户一样，应用程序可以分配有特权角色。 可向应用分配 Azure AD 角色（如全局管理员）或 Azure RBAC 角色（如订阅所有者）。 由于应用程序可在用户不在时作为后台服务运行，因此无论何时向应用程序授予了高特权角色或权限时，都需对其密切监视。 

### <a name="service-principal-assigned-to-a-role"></a>分配到角色的服务主体


| 要监视的内容| 风险级别| Where| 筛选器/子筛选器| 说明 |
|-|-|-|-|-|
| 分配到 Azure RBAC 角色或 Azure AD 角色的应用| 高到中| Azure AD 审核日志| 类型：服务主体<br>活动：“将成员添加到角色”或“将符合条件的成员添加到角色”<br>-或-<br>“将作用域内的成员添加到角色”。| 对于高特权角色（如全局管理员），为高风险。 对于低特权角色，为中风险。 在正常更改管理或配置过程以外将应用程序分配到 Azure 角色或 Azure AD 角色时，都会发出警报。 |

### <a name="application-granted-highly-privileged-permissions"></a>被授予高特权权限的应用程序

应用程序还应遵循最小特权原则。 调查应用程序权限，确保真正需要这些权限。 可以创建[应用同意授予报告](https://aka.ms/getazureadpermissions)以帮助识别现有应用程序并突出显示特权权限。

| 要监视的内容|风险级别|Where| 筛选器/子筛选器| 说明|
|-|-|-|-|-|
| 授予了高特权权限的应用，例如“ *.All”权限 (Directory.ReadWrite.All)，或广泛权限 (Mail.* )| 高 |Azure AD 审核日志| “向服务主体添加应用角色分配”， <br>-其中-<br> 目标识别包含敏感数据的 API（例如 Microsoft Graph） <br>－和－<br>AppRole.Value 识别高特权应用程序权限（应用角色）。| 授予了全部权限的应用，例如“ *.All”(Directory.ReadWrite.All) 或广泛权限 (Mail.* )。 |
| 授予应用程序权限（应用角色）或高特权委托权限的管理员 |高| Microsoft 365 门户| “向服务主体添加应用角色分配”， <br>-其中-<br>目标识别包含敏感数据的 API（例如 Microsoft Graph）<br>“添加委托的权限授予”， <br>-其中-<br>目标识别包含敏感数据的 API（例如 Microsoft Graph） <br>－和－<br>DelegatedPermissionGrant.Scope 包括高特权权限。| 全局管理员、应用程序管理员或云应用程序管理员同意使用应用程序时发出警报。 尤其查找正常活动和更改过程以外的同意。 |
| 应用程序获得授权使用 Microsoft Graph、Exchange、SharePoint 或 Azure AD。 |高| Azure AD 审核日志| “添加委托的权限授予” <br>-或-<br>“向服务主体添加应用角色分配”， <br>-其中-<br>目标识别包含敏感数据的 API（例如 Microsoft Graph、Exchange Online 等）| 如上一行所示警报。 |
| 授予应用程序访问其他 API 的权限（应用角色） |中等| Azure AD 审核日志| “向服务主体添加应用角色分配”， <br>-其中-<br>目标识别任何其他 API。| 如上一行所示警报。 |
| 代表所有用户授予高特权委托权限 |高| Azure AD 审核日志| “添加委托的权限授予”，其中目标标识包含敏感数据的 API（例如 Microsoft Graph）， <br> DelegatedPermissionGrant.Scope 包括高特权权限， <br>－和－<br>DelegatedPermissionGrant.ConsentType 为“AllPrincipals”。| 如上一行所示警报。 |

有关监视应用权限的详细信息，请参阅本教程：[调查和修正有风险的 OAuth 应用](/cloud-app-security/investigate-risky-oauth)。

### <a name="azure-key-vault"></a>Azure Key Vault

Azure Key Vault 可用于存储租户密钥。 建议特别注意对 Key Vault 配置和活动的任何更改。 

| 要监视的内容| 风险级别| Where| 筛选器/子筛选器| 说明 |
|-|-|-|-|-|
| 访问 Key Vault 的方式和时间以及由谁访问| 中等| [Azure Key Vault 日志](../../key-vault/general/logging.md?tabs=Vault)| 资源类型：Key Vault| 期望 <li> 常规进程和时间之外对 Key Vault 的任何访问。 <li> 对 Key Vault ACL 的任何更改。 |

设置 Azure Key Vault 后，请确保[启用日志记录](../../key-vault/general/howto-logging.md?tabs=azure-cli)，其用于显示[访问 Key Vault 的方式和时间](../../key-vault/general/logging.md?tabs=Vault)，然后在 Key Vault 上[配置警报](../../key-vault/general/alert.md)，以在运行状况受到影响时通过电子邮件、电话、短信或[事件网格](../../key-vault/general/event-grid-overview.md)通知向分配的用户或通讯组发出通知。 此外，使用 Key Vault 见解设置[监视](../../key-vault/general/alert.md)后，你会获得 Key Vault 请求、性能、故障和延迟的快照。 [日志分析](../../azure-monitor/logs/log-analytics-overview.md)还包括一些针对 Azure Key Vault 的[示例查询](../../azure-monitor/logs/queries.md)，通过选择 Key Vault 并选择“监视”下的“日志”可以访问这些示例。

### <a name="end-user-consent"></a>最终用户同意

| 要监视的内容| 风险级别| Where| 筛选器/子筛选器| 说明 |
|-|-|-|-|-|
| 最终用户同意使用应用程序| 低| Azure AD 审核日志| 活动：同意使用应用程序/ConsentContext.IsAdminConsent = false| 查找: <li>高配置文件或高特权帐户。<li> 应用请求高风险权限<li>具有可疑名称的应用，例如泛型、拼写错误等。 |


同意使用应用程序的行为本身并无恶意。 但是，调查新的最终用户同意授权时会查找可疑应用程序。 你可以[限制用户同意操作](../../security/fundamentals/steps-secure-identity.md)。

有关同意操作的详细信息，请参阅以下资源：

* [在 Azure Active Directory 中管理应用程序的同意和评估同意请求](../manage-apps/manage-consent-requests.md)

* [检测并修正非法同意授权 - Office 365](/microsoft-365/security/office-365-security/detect-and-remediate-illicit-consent-grants)

* [事件响应 playbook - 应用同意授权调查](/security/compass/incident-response-playbook-app-consent)

### <a name="end-user-stopped-due-to-risk-based-consent"></a>因同意存在风险而阻止最终用户 

| 要监视的内容| 风险级别| Where| 筛选器/子筛选器| 说明 |
|-|-|-|-|-|
| 因同意存在风险而阻止最终用户同意| 中等| Azure AD 审核日志| 核心目录/ApplicationManagement/同意使用应用程序<br> 失败状态原因 = Microsoft.online.Security.userConsent<br>BlockedForRiskyAppsExceptions| 因风险而阻止同意时进行监视和分析。 查找:<li>高配置文件或高特权帐户。<li> 应用请求高风险权限<li>具有可疑名称的应用，例如泛型、拼写错误等。 |

## <a name="application-configuration-changes"></a>应用程序配置更改

监视任何应用程序配置的更改。 具体而言，即对统一资源标识符 (URI)、所有权和注销 URL 的配置更改。

### <a name="dangling-uri-and-redirect-uri-changes"></a>无关联的 URI 和重定向 URI 更改 

| 要监视的内容| 风险级别| Where| 筛选器/子筛选器| 说明 |
|-|-|-|-|-|
| 无关联的 URI| 高| Azure AD 日志和应用程序注册| Service-Core Directory、Category-ApplicationManagement<br>活动：更新应用程序<br>成功 - 属性名称 AppAddress| 查找无关联的 URI，例如，指向不再存在或未显式拥有的域名。 |
| 重定向 URI 配置更改| 高| Azure AD 日志| Service-Core Directory、Category-ApplicationManagement<br>活动：更新应用程序<br>成功 - 属性名称 AppAddress| 查找未使用 HTTPS* 的 URI、通配符在末尾或包含 URL 域的 URI、对应用程序不唯一的 URI、指向非所控域的 URI。 |

检测到这些更改时发出警报。

### <a name="appid-uri-added-modified-or-removed"></a>添加、修改或删除的 AppID URI


| 要监视的内容| 风险级别| Where| 筛选器/子筛选器| 说明 |
|-|-|-|-|-|
| AppID URI 的更改| 高| Azure AD 日志| Service-Core Directory、Category-ApplicationManagement<br>活动：更新<br>应用程序<br>活动：更新服务主体| 查找任何 AppID URI 修改，例如添加、修改或删除 URI。 |


在已获得审批的更改管理过程之外检测到这些更改时发出警报。

### <a name="new-owner"></a>新所有者


| 要监视的内容| 风险级别| Where| 筛选器/子筛选器| 说明 |
|-|-|-|-|-|
| 应用程序所有权的更改| 中等| Azure AD 日志| Service-Core Directory、Category-ApplicationManagement<br>活动：将所有者添加到应用程序| 查找在正常更改管理活动之外添加为应用程序所有者的用户的任何实例。 |

### <a name="logout-url-modified-or-removed"></a>修改或删除的注销 URL

| 要监视的内容| 风险级别| Where| 筛选器/子筛选器| 说明 |
|-|-|-|-|-|
| 注销 URL 的更改| 低| Azure AD 日志| Service-Core Directory、Category-ApplicationManagement<br>活动：更新应用程序<br>－和－<br>活动：更新服务主体| 查找对注销 URL 的任何修改。 空白条目或位置不存在的条目会阻止用户终止会话。 |

## <a name="additional-resources"></a>其他资源

以下是有用资源的链接：

* Github Azure AD 工具包 - [https://github.com/microsoft/AzureADToolkit](https://github.com/microsoft/AzureADToolkit)

* Azure Key Vault 安全概述和安全指南 - [Azure Key Vault 安全概述](../../key-vault/general/security-features.md)

* Solorgate 风险信息和工具 - [Azure AD 工作簿帮助评估 Solorigate 风险](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/azure-ad-workbook-to-help-you-assess-solorigate-risk/ba-p/2010718)

* OAuth 攻击检测指南 - [向 OAuth 应用异常添加凭据](/cloud-app-security/investigate-anomaly-alerts)

Azure AD SIEM 监视配置信息 - [集成 Azure Monitor 的合作伙伴工具](../..//azure-monitor/essentials/stream-monitoring-data-event-hubs.md)

 ## <a name="next-steps"></a>后续步骤

请参阅以下安全操作指南文章：

[Azure AD 安全操作概述](security-operations-introduction.md)

[用户帐户的安全操作](security-operations-user-accounts.md)

[特权帐户的安全操作](security-operations-privileged-accounts.md)

[Privileged Identity Management 的安全操作](security-operations-privileged-identity-management.md)

[应用程序的安全操作](security-operations-applications.md)

[设备的安全操作](security-operations-devices.md)

 
[基础结构的安全操作](security-operations-infrastructure.md)