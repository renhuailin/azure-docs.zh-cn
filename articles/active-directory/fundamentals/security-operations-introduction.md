---
title: Azure Active Directory 安全操作指南
description: 了解如何监视和识别帐户、应用程序、设备和基础结构的安全问题并发出警报
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
ms.openlocfilehash: c219ab9c1503fdd3bbd85bf704786ef2c097d52a
ms.sourcegitcommit: 54e7b2e036f4732276adcace73e6261b02f96343
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/12/2021
ms.locfileid: "129808660"
---
# <a name="azure-active-directory-security-operations-guide"></a>Azure Active Directory 安全操作指南

Microsoft 提出一种成功且经过验证的[零信任安全](https://aka.ms/Zero-Trust)方法，该方法使用将标识用作控制平面的[纵深防御](https://us-cert.cisa.gov/bsi/articles/knowledge/principles/defense-in-depth)原则。 随着组织不断采用混合工作负载来实现规模、节省成本和提高安全性，Azure Active Directory (Azure AD) 在标识管理策略中发挥关键作用。 最近，关于标识和安全威胁的消息不断促使企业 IT 将其标识安全状况视为防御性安全成功的衡量标准。

组织必须越来越多地同时采用本地和云应用程序，用户可使用本地帐户和仅限云的帐户访问这些应用程序。 同时在本地和云中管理用户、应用程序和设备会带来挑战。

Azure Active Directory 创建的通用用户标识可用于针对任何位置的所有资源进行身份验证和授权。 我们称此为混合标识。 

若要通过 Azure AD 实现混合标识，可以根据你的具体方案使用三种身份验证方法之一。 这三种方法是：

* [密码哈希同步 (PHS)](../hybrid/whatis-phs.md)

* [直通身份验证 (PTA)](../hybrid/how-to-connect-pta.md)

* [联合 (AD FS)](../hybrid/whatis-fed.md)

审核当前安全操作或为 Azure 环境确定安全操作时，建议执行下列步骤：

* 阅读 Microsoft 安全指南的特定部分，力求对保护基于云或混合 Azure 环境有一定的了解。

* 审核自己的帐户和密码策略以及身份验证方法，以帮助阻止最常见的攻击途径。

* 针对可能表明存在安全威胁的活动制定持续监视和警报策略。

## <a name="audience"></a>目标受众

Azure AD SecOps 指南适用于企业 IT 标识和安全操作团队，以及需要通过更好的标识安全配置和监视配置文件应对威胁的托管服务提供商。 本指南特别适用于 IT 管理员和标识架构师，他们为安全操作中心 (SOC) 防御和渗透测试团队提供建议以改善和维护其标识安全状况。 

## <a name="scope"></a>范围

本简介针对预读、密码审核和策略提供了相关建议。 本文还概述了适用于混合 Azure 环境以及完全基于云的 Azure 环境的工具。 最后，本文提供了一份数据源列表，可用于监视、警报并配置安全信息和事件管理 (SIEM) 策略和环境。 本指南其余部分介绍了以下几个方面的监视和警报策略：

* [用户帐户](security-operations-user-accounts.md) – 针对没有管理权限的非特权用户帐户的指导，包括异常帐户创建和使用以及异常登录。

* [特权帐户](security-operations-privileged-accounts.md) – 针对具有较高权限以执行管理任务的特权用户帐户的指导，包括 Azure AD 角色分配、Azure 资源角色分配，以及 Azure 资源和订阅的访问管理。

* [Privileged Identity Management (PIM)](security-operations-privileged-identity-management.md) – 针对使用 PIM 管理、控制和监视资源访问权限的指导。 

* [应用程序](security-operations-applications.md) – 针对用于为应用程序提供身份验证的帐户的指导。 

* [设备](security-operations-devices.md) – 针对在策略之外注册或联接的设备进行监视和警报、不合规使用情况、管理设备的管理角色和登录虚拟机的指导。

*  [基础结构](security-operations-infrastructure.md) – 针对混合环境和完全基于云的环境的威胁进行监视和警报的指导。

## <a name="important-reference-content"></a>重要参考内容

Microsoft 提供了许多产品和服务，可自定义 IT 环境以满足你的需求。 建议在监视和警报策略中，查看以下与操作环境相关的指导：

* Windows 操作系统

   * [Windows 10 和 Windows Server 2016 安全审核和监视参考](https://www.microsoft.com/download/details.aspx?id=52630)

   * [Windows 10 v1909 和 Windows Server v1909 安全基线（最终版）](https://techcommunity.microsoft.com/t5/microsoft-security-baselines/security-baseline-final-for-windows-10-v1909-and-windows-server/ba-p/1023093)

   * [Windows 11 的安全基线](https://techcommunity.microsoft.com/t5/microsoft-security-baselines/windows-11-security-baseline/ba-p/2810772)
   
   * [Windows Server 2022 的安全基线](https://techcommunity.microsoft.com/t5/microsoft-security-baselines/windows-server-2022-security-baseline/ba-p/2724685)
   
* 本地环境

   * [Microsoft Defender for Identity 体系结构](/defender-for-identity/architecture)

   * [将 Microsoft Defender for Identity 连接到 Active Directory 快速入门](/defender-for-identity/install-step2)

   * [适用于 Microsoft Defender for Identity 的 Azure 安全基线](/defender-for-identity/security-baseline)

   * [监视 Active Directory 遭到破坏的迹象](/windows-server/identity/ad-ds/plan/security-best-practices/monitoring-active-directory-for-signs-of-compromise)

* 基于云的 Azure 环境


   * [使用 Azure AD 登录日志监视登录](../reports-monitoring/concept-all-sign-ins.md)

   * [Azure Active Directory 门户中的“审核活动”报表](../reports-monitoring/concept-audit-logs.md)

   * [使用 Azure Active Directory 标识保护来调查风险](../identity-protection/howto-identity-protection-investigate-risk.md) 

   * [将 Azure AD 标识保护数据连接到 Azure Sentinel](../../sentinel/data-connectors-reference.md#azure-active-directory-identity-protection)

* Active Directory 域服务 (AD DS)

   * [审核策略建议](/windows-server/identity/ad-ds/plan/security-best-practices/audit-policy-recommendations)

* Active Directory 联合身份验证服务 (AD FS)

   * [AD FS 故障排除 - 审核事件和日志记录](/windows-server/identity/ad-fs/troubleshooting/ad-fs-tshoot-logging)

## <a name="data-sources"></a>数据源 

用于调查和监视的日志文件包括：

* [Azure AD 审核日志](../reports-monitoring/concept-audit-logs.md)

* [登录日志](../reports-monitoring/concept-all-sign-ins.md)

* [Microsoft 365 审核日志](/microsoft-365/compliance/auditing-solutions-overview)

* [Azure Key Vault 日志](../../key-vault/general/logging.md?tabs=Vault)

在 Azure 门户中，可以查看 Azure AD 审核日志，并将其作为逗号分隔值 (CSV) 或 JavaScript 对象表示法 (JSON) 文件下载。 Azure 门户提供多种方法,将 Azure AD 日志与其他工具相集成，以便更好地自动执行监视和警报：

* [Azure Sentinel](../../sentinel/overview.md) - 通过提供安全信息和事件管理 (SIEM) 功能，实现企业级智能安全分析。 

* [Azure Monitor](../../azure-monitor/overview.md) – 实现对各种情况的自动监视和警报。 可以创建或使用工作簿来合并来自不同源的数据。

* 与 SIEM 集成的 [Azure 事件中心](../../event-hubs/event-hubs-about.md)- 通过 Azure 事件中心集成[可将 Azure AD 日志集成到其他 SIEM](../reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md)，例如 Splunk、ArcSight、QRadar 和 Sumo Logic。

* [Microsoft Cloud App Security](/cloud-app-security/what-is-cloud-app-security) (MCAS) – 支持发现和管理应用、跨应用和资源进行治理以及检查云应用合规性。

大部分你将监视和发出警报的内容由条件访问策略决定。 可以使用[条件访问见解和报告工作簿](../conditional-access/howto-conditional-access-insights-reporting.md)来检查一个或多个条件访问策略对登录的影响以及包括设备状态在内的策略结果。 可通过此工作簿查看影响摘要并确定特定时间段的影响。 还可以使用此工作簿来调查特定用户的登录情况。 

本文接下来将按威胁类型介绍应该监视和发出相关警报的对象。 如果有特定预生成解决方案，我们会链接到这些解决方案，或在表格之后提供示例。 除此之外，你可以使用前述工具生成警报。 

* [标识保护](../identity-protection/overview-identity-protection.md) – 生成有助于进行调查的三份关键报告：

   * 风险用户 - 包含有关哪些用户面临风险的信息、有关检测的详细信息、所有风险登录的历史记录，以及风险历史记录。

   * 风险登录 – 包含有关可能表明有可疑情况的登录信息。 有关根据此报告中的信息进行调查的详细信息，请访问[操作指南：调查风险](../identity-protection/howto-identity-protection-investigate-risk.md)。 

   *  风险检测 - 包含有关 Azure AD 标识保护检测到的风险信号的信息，此信息可用于通知登录和用户风险。 有关详细信息，请参阅[用户帐户的 Azure AD 安全操作指南](security-operations-user-accounts.md)。

### <a name="data-sources-for-domain-controller-monitoring"></a>用于域控制器监视的数据源

为获得最佳结果，我们建议使用 Microsoft Defender for Identity 监视域控制器。 你可因此获得最佳检测和自动化功能。 请遵循以下指南：

* [Microsoft Defender for Identity 体系结构](/defender-for-identity/architecture)

* [将 Microsoft Defender for Identity 连接到 Active Directory 快速入门](/defender-for-identity/install-step2)

如果不打算使用 Microsoft Defender 进行标识，则可以[通过事件日志消息](/windows-server/identity/ad-ds/plan/security-best-practices/monitoring-active-directory-for-signs-of-compromise)或[运行 PowerShell cmdlet](/windows-server/identity/ad-ds/deploy/troubleshooting-domain-controller-deployment)来监视域控制器。 

## <a name="components-of-hybrid-authentication"></a>混合身份验证组件

在 Azure 混合环境中，应将以下各项作为基准并纳入监视和警报策略中。 

* PTA 代理 – 直通身份验证代理用于启用直通身份验证，并安装在本地。 有关验证代理版本和后续步骤的信息，请参阅 [Azure AD 直通身份验证代理：版本发布历史记录](../hybrid/reference-connect-pta-version-history.md)。 

* AD FS/WAP – Azure Active Directory 联合身份验证服务 (Azure AD FS) 和 Web 应用程序代理 (WAP) 支持跨安全性和企业边界安全共享数字标识和权利权限。 有关安全最佳做法的信息，请参阅[保护 Active Directory 联合身份验证服务的最佳做法]/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs)。 

* Azure AD Connect Health 代理 – 用于为 Azure AD Connect Health 提供通信链接的代理。 有关安装代理的信息，请参阅 [Azure Active Directory Connect Health 代理安装](../hybrid/how-to-connect-health-agent-install.md)。 

* Azure AD Connect 同步引擎 - 本地组件，也称为同步引擎。 有关该功能的信息，请参阅 [Azure AD Connect 同步服务功能](../hybrid/how-to-connect-syncservice-features.md)。

* 密码保护 DC 代理 – Azure 密码保护 DC 代理用于帮助监视和报告事件日志消息。 有关信息，请参阅[为 Active Directory 域服务强制实施本地 Azure AD 密码保护](../authentication/concept-password-ban-bad-on-premises.md)。 

* 密码筛选器 DLL – DC 代理的密码筛选器 DLL 接收来自操作系统的用户密码验证请求。 筛选器将这些请求转发到在 DC 上本地运行的 DC 代理服务。 有关使用 DLL 的信息，请参阅[为 Active Directory 域服务强制实施本地 Azure AD 密码保护](../authentication/concept-password-ban-bad-on-premises.md)。 

* 密码写回代理 – 密码写回是使用 [Azure AD Connect](../hybrid/whatis-hybrid-identity.md) 启用的功能，可将云中的密码更改实时写回到现有的本地目录。 有关此功能的详细信息，请参阅[自助式密码重置写回在 Azure Active Directory 中的工作原理](../authentication/concept-sspr-writeback.md)。

* Azure AD 应用程序代理连接器 – 位于本地的轻型代理，能够帮助与应用程序代理服务建立出站连接。 有关详细信息，请参阅[了解 Azure AD 应用程序代理连接器](../app-proxy/application-proxy-connectors.md)。 

## <a name="components-of-cloud-based-authentication"></a>基于云的身份验证的组件

在 Azure 基于云的环境中，应将以下各项作为基准并纳入监视和警报策略中。

* Azure AD 应用程序代理 – 此云服务提供对本地 Web 应用程序的安全远程访问。 有关详细信息，请参阅[通过 Azure AD 应用程序代理远程访问本地应用程序](../app-proxy/application-proxy-connectors.md)。 

* Azure AD Connect – 用于 Azure AD Connect 解决方案的服务。 有关详细信息，请参阅 [Azure AD Connect 是什么](../hybrid/whatis-azure-ad-connect.md)。

* Azure AD Connect Health – 服务运行状况提供可定制的仪表板，用于跟踪 Azure 服务在使用区域中的运行状况。 有关详细信息，请参阅 [Azure AD Connect Health](../hybrid/whatis-azure-ad-connect.md)。

* Azure MFA – Azure AD 多重身份验证要求用户提供多种形式的身份验证证明。 此为第一步，可主动确保环境安全。 有关详细信息，请参阅 [Azure AD 多重身份验证工作原理](../authentication/concept-mfa-howitworks.md)。

* 动态组 – 动态配置 Azure Active Directory (Azure AD) 安全组成员身份的管理员可以设置规则，以根据用户属性填充在 Azure AD 中创建的组。 有关详细信息，请参阅[动态组和 Azure Active Directory B2B 协作](../external-identities/use-dynamic-groups.md)。

* 条件访问 – 条件访问是 Azure Active Directory 用来统合信号、做出决策以及强制执行组织策略的工具。 条件访问是新的标识驱动控制平面的核心。 有关详细信息，请参阅[条件访问是什么](../conditional-access/overview.md)。

* 标识保护 – 借助该工具，组织可以自动检测和修复基于标识的风险，使用门户中的数据调查风险，并将风险检测数据导出到 SIEM。 有关详细信息，请参阅[标识保护是什么](../identity-protection/overview-identity-protection.md)？

* 基于组的许可 – 可以将许可证分配给组，而不是直接分配给用户。 Azure AD 存储有关用户许可证分配状态的信息。 

* 预配服务 – 预配是指在用户需要访问的云应用程序中创建用户标识和角色。 除了创建用户标识外，自动预配还包括在状态或角色发生更改时维护和删除用户标识。 有关详细信息，请参阅[应用程序预配在 Azure Active Directory 中的工作方式](../app-provisioning/how-provisioning-works.md)。

* 图形 API – Microsoft Graph API 是一种 RESTful Web API，可用于访问 Microsoft 云服务资源。 注册应用并获取用户或服务的身份验证令牌后，可以向 Microsoft Graph API 发出请求。 有关详细信息，请参阅 [Microsoft Graph 概述](/graph/overview)。

* 域服务 – Azure Active Directory 域服务 (AD DS) 提供托管域服务，例如域加入、组策略等。 有关详细信息，请参阅 [Azure Active Directory 域服务是什么？](../../active-directory-domain-services/overview.md)

* Azure 资源管理器 – Azure 资源管理器是 Azure 的部署和管理服务。 它提供了一个管理层，用于在 Azure 帐户中创建、更新和删除资源。 有关详细信息，请参阅 [Azure 资源管理器是什么？](../../azure-resource-manager/management/overview.md)

* 托管标识 – 托管标识使开发人员无需管理凭据。 托管标识为应用程序提供一个标识，可以在连接到支持 Azure Active Directory (Azure AD) 身份验证的资源时使用。 有关详细信息，请参阅[什么是 Azure 资源的托管标识？](../managed-identities-azure-resources/overview.md)

* Privileged Identity Management – Privileged Identity Management (PIM) 是 Azure Active Directory (Azure AD) 中用于管理、控制和监视对组织中重要资源的访问的服务。 有关详细信息，请参阅[什么是 Azure AD Privileged Identity Management](../privileged-identity-management/pim-configure.md)。

* 访问评审 – 借助 Azure Active Directory (Azure AD) 访问评审，组织可有效地管理组成员身份、对企业应用程序的访问权限，以及角色分配。 可以定期评审用户的访问权限，确保相应人员持续拥有访问权限。 有关详细信息，请参阅 [Azure AD 访问评审是什么？](../governance/access-reviews-overview.md)

* 权利管理 – Azure Active Directory (Azure AD) 权利管理是一种[标识治理](../governance/identity-governance-overview.md)功能，通过自动执行访问请求工作流、访问分配、评审和过期，使组织能够大规模管理标识和访问生命周期。 有关详细信息，请参阅[什么是 Azure AD 权利管理？](../governance/entitlement-management-overview.md)

* 活动日志 – 活动日志是 Azure 中的一种[平台日志](../../azure-monitor/essentials/platform-logs-overview.md)，可用于深入了解订阅级别事件。 这包括何时修改了资源或何时启动了虚拟机等信息。 有关详细信息，请参阅 [Azure 活动日志](../../azure-monitor/essentials/activity-log.md)。

* 自助式密码重置服务 – 借助 Azure Active Directory (Azure AD) 自助式密码重置 (SSPR)，用户无需管理员或支持人员的介入即能够更改或重置其密码。 有关详细信息，请参阅[工作原理：Azure AD 自助式密码重置](../authentication/concept-sspr-howitworks.md)。

* 设备服务 – 设备标识管理是[基于设备的条件访问](../conditional-access/require-managed-devices.md)的基础。 通过基于设备的条件访问策略，可确保只有受管理设备才能访问环境中的资源。 有关详细信息，请参阅[设备标识是什么？](../devices/overview.md)

* 自助服务组管理 – 可以在 Azure Active Directory (Azure AD) 中允许用户创建和管理他们自己的安全组或 Microsoft 365 组。 该组的所有者可以批准或拒绝成员身份请求，并可以委托对组成员身份的控制。 自助服务组管理功能不可用于启用了邮件的安全组或通讯组列表。 有关详细信息，请参阅[在 Azure Active Directory 中设置自助服务组管理](../enterprise-users/groups-self-service-management.md)。

* 风险检测 – 包含在检测到风险时触发的其他风险的信息，以及其他相关信息，例如登录位置和 Microsoft Cloud App Security (MCAS) 的任何详细信息。

## <a name="next-steps"></a>后续步骤

请参阅以下安全操作指南文章：

[Azure AD 安全操作概述](security-operations-introduction.md)

[用户帐户的安全操作](security-operations-user-accounts.md)

[特权帐户的安全操作](security-operations-privileged-accounts.md)

[Privileged Identity Management 的安全操作](security-operations-privileged-identity-management.md)

[应用程序的安全操作](security-operations-applications.md)

[设备的安全操作](security-operations-devices.md)

 
[基础结构的安全操作](security-operations-infrastructure.md)
