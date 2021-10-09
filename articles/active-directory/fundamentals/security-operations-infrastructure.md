---
title: Azure Active Directory 基础结构安全操作
description: 了解如何监视基础结构组件以识别安全威胁并针对其发出警报。
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
ms.openlocfilehash: e0a3c2b380160ef68655f0727f4d47280cd5cd02
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124754289"
---
# <a name="security-operations-for-infrastructure"></a>基础结构安全操作

基础结构有许多组件，如果配置不当，可能会出现漏洞。 作为基础结构监视和警报策略的一部分，应该监视以下方面的事件并发出相关警报：

* 身份验证和授权

* 混合身份验证组件（包括联合服务器）

* 策略 

* 订阅

监视身份验证基础结构的组件并发出相关警报至关重要。 任何入侵都可能导致整个环境遭到全面入侵。 许多使用 Azure AD 的企业都是在混合身份验证环境中操作。 这意味着，应将云和本地组件包含在监视和警报策略中。 使用混合身份验证环境还会将另一种攻击途径引入到你的环境。

建议将所有组件以及用于管理这些组件的帐户视为控制平面/第 0 层资产。 有关设计和实施环境的指南，请参阅[保护特权资产](/security/compass/overview) (SPA)。 此指南包含有可能用于 Azure AD 租户的每个混合身份验证组件的建议。

要想检测意外事件和潜在攻击，第一步是建立基线。 对于本文中列出的所有本地组件，请参阅“保护特权资产”(SPA) 指南中的[特权访问部署](/security/compass/privileged-access-deployment)部分。

## <a name="where-to-look"></a>要查看的地方

用于调查和监视的日志文件包括： 

* [Azure AD 审核日志](../reports-monitoring/concept-audit-logs.md)

* [登录日志](../reports-monitoring/concept-all-sign-ins.md)

* [Microsoft 365 审核日志](/microsoft-365/compliance/auditing-solutions-overview) 

* [Azure Key Vault 日志](../../key-vault/general/logging.md?tabs=Vault)

在 Azure 门户中，可以查看 Azure AD 审核日志，并将其作为逗号分隔值 (CSV) 或 JavaScript 对象表示法 (JSON) 文件下载。 Azure 门户提供多种方法用于将 Azure AD 日志与其他便于更好地自动执行监视和警报的工具相集成：

* [Azure Sentinel](../../sentinel/overview.md) – 提供安全信息和事件管理 (SIEM) 功能，实现企业级智能安全分析。 

* [Azure Monitor](../../azure-monitor/overview.md) – 实现对各种情况的自动监视和警报。 可以创建或使用工作簿来合并来自不同源的数据。

* 与 SIEM 集成的 [Azure 事件中心](../../event-hubs/event-hubs-about.md) - 通过 Azure 事件中心集成[可将 Azure AD 日志集成到其他 SIEM](../reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md)，例如 Splunk、ArcSight、QRadar 和 Sumo Logic。

* [Microsoft Cloud App Security](/cloud-app-security/what-is-cloud-app-security) (MCAS) – 支持发现和管理应用、跨应用和资源进行治理以及检查云应用合规性。 

本文接下来将按威胁类型介绍应该监视和发出相关警报的对象。 如果有具体的预生成解决方案，表格后面会提供这些解决方案的链接。 否则，可以使用前面的工具来生成警报。

## <a name="authentication-infrastructure"></a>身份验证基础结构

在包含本地和基于云的资源和帐户的混合环境中，Active Directory 基础结构是身份验证堆栈的关键组成部分。 该堆栈也是攻击目标，因此必须对其进行配置以维持安全的环境，并适当地对其进行监视。 当前针对身份验证基础结构使用的攻击类型示例包含使用密码喷射和 Solorigate 技术。 下面是建议阅读的文章的链接：

* [保护特权访问概述](/security/compass/overview) — 此文概述了使用零信任技术创建和维护安全特权访问的当前技术。

* [Microsoft Defender for Identity 受监视域活动](/defender-for-identity/monitored-activities) - 此文提供了要监视并为其设置警报的活动的综合列表。 

* [Microsoft Defender for Identity 安全警报教程](/defender-for-identity/understanding-security-alerts) - 此文提供了有关创建和实施安全警报策略的指导。

下面是重点介绍如何监视身份验证基础结构并发出相关警报的具体文章的链接：

* [了解和使用 Microsoft Defender for Identity 中的横向移动路径](/defender-for-identity/use-case-lateral-movement-path) - 此文介绍了一些检测技术，这些技术可帮助识别在你的整个网络中何时使用了非敏感帐户获取对敏感帐户的访问权限。

* [在 Microsoft Defender for Identity 中处理安全警报](/defender-for-identity/working-with-suspicious-activities) - 此文介绍了在记录警报后如何查看和管理这些警报。 

 下面是需要检查的具体内容：

| 要监视的内容| 风险级别| Where| 说明 |
| - | - | - | - |
| Extranet 锁定趋势| 高| Azure AD Connect Health| 参考[使用 Azure AD Connect Health 监视 AD FS](../hybrid/how-to-connect-health-adfs.md) 中的信息获取用于帮助检测 Extranet 锁定趋势的工具和技术。 |
| 失败的登录|高 | Connect Health 门户| 导出或下载有风险的 IP 报告，并遵循[有风险的 IP 报告（公共预览版）](../hybrid/how-to-connect-health-adfs-risky-ip.md)中的指导执行后续步骤。 |
| 符合隐私标准| 低| Azure AD Connect Health| 参考[用户隐私和 Azure AD Connect Health](../hybrid/reference-connect-health-user-privacy.md) 一文将 Azure AD Connect Health 配置为禁用数据收集和监视。 |
| 针对 LDAP 的潜在暴力攻击| 中等| Microsoft Defender for Identity| 使用传感器来帮助检测针对 LDAP 的潜在暴力攻击。 |
| 帐户枚举侦测| 中等| Microsoft Defender for Identity| 使用传感器来帮助执行帐户枚举侦查。 |
| Azure AD 与 Azure AD FS 之间的常规关联|中等 | Microsoft Defender for Identity| 使用功能来关联 Azure AD 与 Azure AD FS 环境之间的活动。 |


 

### <a name="pass-through-authentication-monitoring"></a>直通身份验证监视 

Azure Active Directory (Azure AD) 直通身份验证通过直接针对本地 Active Directory 验证用户密码来将用户登录。 

下面是需要检查的具体内容：

| 要监视的内容| 风险级别| Where| 筛选器/子筛选器| 说明 |
| - | - | - | - | - |
| Azure AD 直通身份验证错误|中等 | Application and ‎Service Logs\Microsoft\A‎zureAdConnec‎t\Authenticatio‎nAgent\Admin| AADSTS80001 – 无法连接到 Active Directory| 确保代理服务器是需要验证其密码的用户所在 AD 林的成员，并且能够连接到 Active Directory。 |
| Azure AD 直通身份验证错误| 中等| Application and ‎Service Logs\Microsoft\A‎zureAdConnec‎t\Authenticatio‎nAgent\Admin| AADSTS8002 - 连接到 Active Directory 时超时| 检查以确保 Active Directory 可用，并且可以响应代理的请求。 |
| Azure AD 直通身份验证错误|中等 | Application and ‎Service Logs\Microsoft\A‎zureAdConnec‎t\Authenticatio‎nAgent\Admin| AADSTS80004 - 传递给代理的用户名无效| 确保用户尝试使用正确的用户名登录。 |
| Azure AD 直通身份验证错误|中等 | Application and ‎Service Logs\Microsoft\A‎zureAdConnec‎t\Authenticatio‎nAgent\Admin| AADSTS80005 - 验证遇到了不可预知的 WebException| 暂时性的错误。 重试请求。 如果持续失败，请与 Microsoft 支持人员联系。 |
| Azure AD 直通身份验证错误| 中等| Application and ‎Service Logs\Microsoft\A‎zureAdConnec‎t\Authenticatio‎nAgent\Admin| AADSTS80007 - 与 Active Directory 通信时出错| 查看代理日志以了解更多信息，并验证 Active Directory 是否按预期方式运行。 |
| Azure AD 直通身份验证错误|高 | Win32 LogonUserA 函数 API| 登录事件 4624(s)：帐户已成功登录<br>- 关联 –<br>4625(F)：帐户无法登录| 与对请求进行身份验证的域控制器上的可疑用户名配合使用。 [LogonUserA 函数 (winbase.h)](/windows/win32/api/winbase/nf-winbase-logonusera) 中提供了指导 |
| Azure AD 直通身份验证错误| 中等| 域控制器的 PowerShell 脚本| 参阅表格后面的查询。 | 参考 [Azure AD Connect：排查直通身份验证问题](../hybrid/tshoot-connect-pass-through-authentication.md)中的信息获取更多指导。 |

```Kusto

<QueryList>

<Query Id="0" Path="Security">

<Select Path="Security">*[EventData[Data[@Name='ProcessName'] and (Data='C:\Program Files\Microsoft Azure AD Connect Authentication Agent\AzureADConnectAuthenticationAgentService.exe')]]</Select>

</Query>

</QueryList>
```

### <a name="appproxy-connector"></a>AppProxy 连接器

Azure AD 和 Azure AD 应用程序网关为远程用户提供单一登录 (SSO) 体验。 用户在不使用虚拟专用网 (VPN) 或双重宿主服务器和防火墙规则的情况下安全连接到本地应用。 如果 Azure AD 应用程序代理服务器遭到入侵，攻击者可能会改变 SSO 体验或更改对已发布的应用程序的访问权限。 

若要为应用程序代理配置监视，请参阅[排查应用程序代理问题和错误消息](../app-proxy/application-proxy-troubleshoot.md)。 可以在 Applications and Services Logs\Microsoft\AadApplicationProxy\Connector\Admin 中找到记录信息的数据文件。有关审核活动的完整参考指南，请参阅 [Azure AD 审核活动参考](../reports-monitoring/reference-audit-activities.md)。 要监视的具体内容：

| 要监视的内容| 风险级别| Where| 筛选器/子筛选器| 说明 |
| - | - | - | - | - |
| Kerberos 错误| 中等 | 各种工具| 中等 | [排查应用程序代理问题和错误消息](../app-proxy/application-proxy-troubleshoot.md)中的“Kerberos 错误”下提供了 Kerberos 身份验证错误处理指导。 |
| DC 安全问题| 高| DC 安全审核日志| 事件 ID 4742(S)：计算机帐户已更改<br>－和－<br>标志 – 受信任，可委派<br>-或-<br>标志 - 受信任，可执行身份验证以便委派| 调查任何标志更改。 |
| 类似于传递票证的攻击| 高| | | 遵循以下文章中的指导：<li>[安全主体侦查 (LDAP)（外部 ID 2038）](/defender-for-identity/reconnaissance-alerts)<li>[教程：泄露凭据警报](/defender-for-identity/compromised-credentials-alerts)<li> [了解和使用 Microsoft Defender for Identity 的横向移动路径](/defender-for-identity/use-case-lateral-movement-path)<li> [了解实体配置文件](/defender-for-identity/entity-profiles) |


### <a name="legacy-authentication-settings"></a>旧式身份验证设置

要使多重身份验证 (MFA) 生效，还需要阻止旧式身份验证。 然后，需要监视环境并对任何使用旧式身份验证的情况发出警报。 这是因为，POP、SMTP、IMAP 和 MAPI 等旧式身份验证协议无法强制执行 MFA。 这使得这些协议成了组织攻击者的首选入口点。 有关可用于阻止旧式身份验证的工具的详细信息，请参阅[用于在组织中阻止旧式身份验证的新工具](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/new-tools-to-block-legacy-authentication-in-your-organization/ba-p/1225302)。 

旧式身份验证作为事件详细信息的一部分捕获到 Azure AD 登录日志中。 可以使用 Azure Monitor 工作簿来帮助识别旧式身份验证的使用。 有关详细信息，请参阅[如何将 Azure Monitor 工作簿用于 Azure Active Directory 报告](../reports-monitoring/howto-use-azure-monitor-workbooks.md)中的[使用旧式身份验证登录](../reports-monitoring/howto-use-azure-monitor-workbooks.md)。 还可以使用 Azure Sentinel 的不安全协议工作簿。 有关详细信息，请参阅 [Azure Sentinel 不安全协议工作簿实施指南](https://techcommunity.microsoft.com/t5/azure-sentinel/azure-sentinel-insecure-protocols-workbook-implementation-guide/ba-p/1197564)。 要监测的具体活动包括：

| 要监视的内容| 风险级别| Where| 筛选器/子筛选器| 说明 |
| - | - | - | - | - |
| 旧式身份验证|高 | Azure AD 登录日志| ClientApp : POP<br>ClientApp : IMAP<br>ClientApp : MAPI<br>ClientApp: SMTP<br>ClientApp : ActiveSync go to EXO<br>其他客户端 = SharePoint 和 EWS| 在联合域环境中，失败的身份验证不被记录，因此不会显示在日志中。 |


## <a name="azure-ad-connect"></a>Azure AD Connect

Azure AD 连接提供一个集中位置，用于在本地与基于云的 Azure AD 环境之间实现帐户和属性同步。 Azure AD Connect 专用于满足和完成混合标识目标的 Microsoft 工具。 它提供以下功能：

* [密码哈希同步](../hybrid/whatis-phs.md) - 一种登录方法，它将用户的本地 AD 密码与 Azure AD 进行同步。

* [同步](../hybrid/how-to-connect-sync-whatis.md) - 负责创建用户、组和其他对象。 另外，它还负责确保本地用户和组的标识信息与云匹配。 此同步还包括密码哈希。

* [运行状况监视](../hybrid/whatis-azure-ad-connect.md) - Azure AD Connect Health 提供可靠监视，并在 Azure 门户中提供一个中心位置，用于查看此活动。

同步本地环境与云环境之间的标识会在本地和基于云的环境中造成新的攻击面。 我们建议：

* 将 Azure AD Connect 主服务器和暂存服务器视为控制平面中的第 0 层系统。 

* 遵循一组标准策略来控制每种类型的帐户及其在环境中的用法。

*  安装 Azure AD Connect 和 Connect Health。 这些组件主要用于提供环境的操作数据。 

以不同的方式记录发生的 Azure AD Connect 操作：

* Azure AD Connect 向导将数据记录到 \ProgramData\AADConnect。 每次调用该向导时，都会创建一个带时间戳的跟踪日志文件。 可将跟踪日志导入到 Sentinel 或其他第三方安全信息和事件管理 (SIEM) 工具进行分析。<sup data-htmlnode=""></sup>

* 某些操作会启动 PowerShell 脚本来捕获日志记录信息。 若要收集此数据，必须确保已启用脚本块日志记录。

### <a name="monitoring-configuration-changes"></a>监视配置更改

Azure AD 使用 Microsoft SQL Server 数据引擎或 SQL 来存储 Azure AD Connect 配置信息。 因此应在监视和审核策略中，包括与配置关联的日志文件的监视和审核。 具体而言，应在监视和警报策略中包括以下表。

| 要监视的内容| Where| 说明 |
| - | - | - |
| mms_management_agent| SQL 服务审核记录| 参阅 [SQL Server 审核记录](/sql/relational-databases/security/auditing/sql-server-audit-records) |
| mms_partition| SQL 服务审核记录| 参阅 [SQL Server 审核记录](/sql/relational-databases/security/auditing/sql-server-audit-records) |
| mms_run_profile| SQL 服务审核记录| 参阅 [SQL Server 审核记录](/sql/relational-databases/security/auditing/sql-server-audit-records) |
| mms_server_configuration| SQL 服务审核记录| 参阅 [SQL Server 审核记录](/sql/relational-databases/security/auditing/sql-server-audit-records) |
| mms_synchronization_rule| SQL 服务审核记录| 参阅 [SQL Server 审核记录](/sql/relational-databases/security/auditing/sql-server-audit-records) |


有关要监视的配置信息内容和监视方法的信息，请参阅：

* 对于 SQL Server，请参阅 [SQL Server 审核记录](/sql/relational-databases/security/auditing/sql-server-audit-records)。

* 对于 Azure Sentinel，请参阅[连接到 Windows 服务器以收集安全事件](/sql/relational-databases/security/auditing/sql-server-audit-records)。 

* 有关配置和使用 Azure AD Connect 的信息，请参阅[什么是 Azure AD Connect？](../hybrid/whatis-azure-ad-connect.md)

### <a name="monitoring-and-troubleshooting-synchronization"></a>监视和排查同步问题

 Azure AD Connect 的一项功能是在用户本地密码与 Azure AD 之间进行哈希同步。 如果密码不按预期方式同步，则同步可能会影响一部分用户或所有用户。 参考以下文章来帮助验证操作是否正常或排查问题：

* 有关检查和排查哈希同步问题的信息，请参阅[排查使用 Azure AD Connect 同步进行密码哈希同步的问题](../hybrid/tshoot-connect-password-hash-synchronization.md)。 

* 有关对连接器空间的修改，请参阅[排查 Azure AD Connect 对象和属性问题](/troubleshoot/azure/active-directory/troubleshoot-aad-connect-objects-attributes)。 

**有关监测的重要资源**

| 要监视的内容 | 资源 |
| - | - |
| 哈希同步验证|参阅[排查使用 Azure AD Connect 同步进行密码哈希同步的问题](../hybrid/tshoot-connect-password-hash-synchronization.md) |
 对连接器空间的修改|[排查 Azure AD Connect 对象和属性问题](/troubleshoot/azure/active-directory/troubleshoot-aad-connect-objects-attributes) |
| 对配置的规则的修改| 具体而言，这包括监视筛选更改、域和 OU 更改、属性更改和基于组的更改 |
| SQL 和 MSDE 更改 | 更改日志记录参数和添加自定义函数 |

监视以下各项： 

| 要监视的内容| 风险级别| Where| 筛选器/子筛选器| 说明 |
| - | - | - | - | - |
| 计划程序更改|高 | PowerShell| Set-ADSyncScheduler| 查看对计划的修改 |
| 对计划任务的更改| 高 | Azure AD 审核日志| 活动 = 4699(S)：已删除计划任务<br>-或-<br>活动 = 4701(S)：已禁用计划任务<br>-或-<br>活动 = 4701(S)：已更新计划任务| 全部监视 |



* 有关记录 PowerShell 脚本操作的详细信息，请参阅 PowerShell 参考文档中的[启用脚本块日志记录](/powershell/module/microsoft.powershell.core/about/about_logging_windows)。

* 有关配置 PowerShell 日志记录以通过 Splunk 进行分析的详细信息，请参阅[将数据引入 Splunk User Behavior Analytics](https://docs.splunk.com/Documentation/UBA/5.0.4.1/GetDataIn/AddPowerShell)。

### <a name="monitoring-seamless-single-sign-on"></a>监视无缝单一登录

Azure Active Directory (Azure AD) 无缝单一登录（无缝 SSO）可使登录连接到企业网络的企业台式机的用户自动登录。 无缝 SSO 可让用户轻松访问基于云的应用程序，而无需使用其他任何本地组件。 SSO 使用 Azure AD Connect 提供的直通身份验证和密码哈希同步功能。

监视单一登录和 Kerberos 活动可帮助检测一般性的凭据盗窃攻击模式。 使用以下信息进行监视：

| 要监视的内容| 风险级别| Where| 筛选器/子筛选器| 说明 |
| - | - | - | - | - |
| 与 SSO 和 Kerberos 验证失败关联的错误|中等 | Azure AD 登录日志| | [单一登录](../hybrid/tshoot-connect-sso.md)中提供了单一登录错误代码列表。 |
| 用于排查错误的查询|中等 | PowerShell| 参阅表格后面的查询。 检查每个启用了 SSO 的林。| 检查每个启用了 SSO 的林。 |
| 与 Kerberos 相关的事件|高 | Microsoft Defender for Identity 监视| | 查看 [Microsoft Defender for Identity 横向移动路径 (LMP)](/defender-for-identity/use-case-lateral-movement-path) 中提供的指导 |

```kusto
<QueryList>

<Query Id="0" Path="Security">

<Select Path="Security">*[EventData[Data[@Name='ServiceName'] and (Data='AZUREADSSOACC$')]]</Select>

</Query>

</QueryList>
```
## <a name="password-protection-policies"></a>密码保护策略

如果部署了 Azure AD 密码保护，则监视和报告是至关重要的任务。 以下链接提供的详细信息可帮助你了解各种监视技术，包括每个服务在哪里记录信息，以及如何报告 Azure AD 密码保护的使用情况。 

域控制器 (DC) 代理和代理服务都记录事件日志消息。 下面所述的所有 PowerShell cmdlet 仅可用于代理服务器（请参阅 AzureADPasswordProtection PowerShell 模块）。 DC 代理软件不安装 PowerShell 模块。

[规划和部署本地 Azure Active Directory 密码保护](../authentication/howto-password-ban-bad-on-premises-deploy.md)中提供了有关规划和实施本地密码保护的详细信息。 有关监视详细信息，请参阅[监视本地 Azure AD 密码保护](../authentication/howto-password-ban-bad-on-premises-monitor.md)。 在每个域控制器上，DC 代理服务软件将每个密码验证操作的结果（和其他状态）写入以下本地事件日志：

* \Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Admin

* \Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Operational

* \Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Trace

DC 代理管理日志是软件行为方式信息的主要来源。 跟踪日志默认已关闭，在记录数据之前必须启用它。 若要排查应用程序代理问题和错误消息，请参阅[排查 Azure Active Directory 应用程序代理问题](../app-proxy/application-proxy-troubleshoot.md)中的详细信息。 这些事件的信息记录在以下位置：

* Applications and Services Logs\Microsoft\AadApplicationProxy\Connector\Admin

* Azure AD 审核日志，类别为“应用程序代理”

[Azure Active Directory (Azure AD) 审核活动参考](../reports-monitoring/reference-audit-activities.md)中提供了 Azure AD 审核活动的完整参考。 

## <a name="next-steps"></a>后续步骤


参阅以下其他安全操作指南文章：

[Azure AD 安全操作概述](security-operations-introduction.md)

[用户帐户的安全操作](security-operations-user-accounts.md)

[特权帐户的安全操作](security-operations-privileged-accounts.md)

[Privileged Identity Management 的安全操作](security-operations-privileged-identity-management.md)

[应用程序的安全操作](security-operations-applications.md)

[设备的安全操作](security-operations-devices.md)
 
[基础结构安全操作](security-operations-infrastructure.md)


 

  
‎
