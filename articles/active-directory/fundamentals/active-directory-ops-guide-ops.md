---
title: Azure Active Directory 常规操作参考指南
description: 本操作参考指南介绍了为保护常规操作而要执行的检查和措施
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: fundamentals
ms.date: 10/31/2019
ms.author: martinco
ms.openlocfilehash: 74410025e148eb60438a020164837dac486d2ad3
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/10/2021
ms.locfileid: "111954994"
---
# <a name="azure-active-directory-general-operations-guide-reference"></a>Azure Active Directory 常规操作参考指南

[Azure AD 操作参考指南](active-directory-ops-guide-intro.md)的本部分介绍了为优化 Azure Active Directory (Azure AD) 的常规操作而要执行的检查和措施。

> [!NOTE]
> 这些建议截至发布之日为最新，但可能会随着时间而改变。 组织应持续评估其操作实践，因为 Microsoft 产品和服务会不断发展。

## <a name="key-operational-processes"></a>关键操作过程

### <a name="assign-owners-to-key-tasks"></a>将所有者分配到关键任务

管理 Azure Active Directory 需要持续执行关键操作任务和流程，这可能并不完全属于某一个推出式项目。 设置这些任务对于优化环境仍非常重要。 关键任务及其建议所有者包括：

| 任务 | 所有者 |
| :- | :- |
| 促成标识安全分数的提高 | InfoSec 运营团队 |
| 维护 Azure AD Connect 服务器 | IAM 运营团队 |
| 定期执行和会审 IdFix 报告 | IAM 运营团队 |
| 会审 Sync 和 AD FS 的 Azure AD Connect Health 警报 | IAM 运营团队 |
| 如果不使用 Azure AD Connect Health，则客户应有可用于监视自定义基础结构的等效过程和工具 | IAM 运营团队 |
| 如果不使用 AD FS，则客户应有可用于监视自定义基础结构的等效过程和工具 | IAM 运营团队 |
| 监视混合日志：Azure AD 应用代理连接器 | IAM 运营团队 |
| 监视混合日志：直通身份验证代理 | IAM 运营团队 |
| 监视混合日志：密码写回服务 | IAM 运营团队 |
| 监视混合日志：本地密码保护网关 | IAM 运营团队 |
| 监视混合日志：Azure AD MFA NPS 扩展（如果适用） | IAM 运营团队 |

查看列表时，你可能会发现你需要为缺少所有者的任务分配所有者，或调整其所有者与上述建议不符的任务的所有权。

#### <a name="owners-recommended-reading"></a>有关所有者的推荐阅读文档

- [在 Azure Active Directory 中分配管理员角色](../roles/permissions-reference.md)
- [Azure 中的监管](../../governance/index.yml)

## <a name="hybrid-management"></a>混合管理

### <a name="recent-versions-of-on-premises-components"></a>本地组件的最新版本

本地组件的最新版本可为客户提供所有最新安全更新、性能改进，以及有助于进一步简化环境的功能。 大多数组件都有一个自动升级设置，该设置可自动完成升级过程。

这些组件包括：

- Azure AD Connect
- Azure AD 应用程序代理连接器
- Azure AD 直通身份验证代理
- Azure AD Connect Health 代理

除非已建立一个过程，否则应定义一个过程来升级这些组件，并尽可能地依赖自动升级功能。 如果你发现组件有半年或更长时间未升级，应尽快升级。

#### <a name="hybrid-management-recommended-reading"></a>有关混合管理的推荐阅读文档

- [Azure AD Connect：自动升级](../hybrid/how-to-connect-install-automatic-upgrade.md)
- [了解 Azure AD 应用程序代理连接器 | 自动更新](../app-proxy/application-proxy-connectors.md#automatic-updates)

### <a name="azure-ad-connect-health-alert-baseline"></a>Azure AD Connect Health 警报基线

组织应部署 [Azure AD Connect Health](../hybrid/whatis-azure-ad-connect.md#what-is-azure-ad-connect-health) 以用于监视 Azure AD Connect 和 AD FS 并报告其状态。 Azure AD Connect 和 AD FS 属于关键组件，它们可能会干扰生命周期管理和身份验证，从而导致服务中断。 Azure AD Connect Health 可帮助你监视和深入了解本地标识基础结构，从而确保环境的可靠性。

![Azure AD Connect Heath 体系结构](./media/active-directory-ops-guide/active-directory-ops-img16.png)

监视环境的运行状况时，必须立即处理所有高严重性警报，然后再处理较低严重性的警报。

#### <a name="azure-ad-connect-health-recommended-reading"></a>有关 Azure AD Connect Health 的推荐阅读文档

- [Azure AD Connect Health 代理安装](../hybrid/how-to-connect-health-agent-install.md)

### <a name="on-premises-agents-logs"></a>本地代理日志

某些标识和访问管理服务需要通过本地代理来启用混合方案。 示例服务包括密码重置、直通身份验证 (PTA)、Azure AD 应用程序代理和 Azure AD MFA NPS 扩展。 运营团队必须使用 System Center Operations Manager 或 SIEM 等解决方案来存档和分析组件代理日志，依此建立这些组件的运行状况基线并监视其运行状况，这一点非常关键。 同样重要的是，Infosec 运营团队或支持人员应了解如何排查错误模式。

#### <a name="on-premises-agents-logs-recommended-reading"></a>有关本地代理日志的推荐阅读文档

- [应用程序代理故障排除](../app-proxy/application-proxy-troubleshoot.md)
- [自助密码重置疑难解答 - Azure Active Directory](../authentication/troubleshoot-sspr.md)
- [了解 Azure AD 应用程序代理连接器](../app-proxy/application-proxy-connectors.md)
- [Azure AD Connect：排查直通身份验证的问题](../hybrid/tshoot-connect-pass-through-authentication.md#collecting-pass-through-authentication-agent-logs)
- [根据 Azure AD MFA NPS 扩展的错误代码排查错误](../authentication/howto-mfa-nps-extension-errors.md)

### <a name="on-premises-agents-management"></a>本地代理管理

采用最佳做法有助于本地代理保持最佳运行状态。 请考虑采用以下最佳做法：

- 建议在每个连接器组中包含多个 Azure AD 应用程序代理连接器，以避免在访问代理应用程序时出现单一故障点，从而提供无缝的负载均衡和高可用性。 如果用于处理生产环境中应用程序的连接器组中当前只有一个连接器，则应至少部署两个连接器以实现冗余。
- 出于调试目的创建并使用应用程序代理连接器组对于故障排除方案以及加入新本地应用程序时可能很有用。 我们还建议在连接器计算机中安装 Message Analyzer 和 Fiddler 等网络工具。
- 建议使用多个直通身份验证代理，以避免在运行身份验证流期间出现单一故障点，从而提供无缝的负载均衡和高可用性。 确保至少部署两个直通身份验证代理以实现冗余。

#### <a name="on-premises-agents-management-recommended-reading"></a>有关本地代理管理的推荐阅读文档

- [了解 Azure AD 应用程序代理连接器](../app-proxy/application-proxy-connectors.md)
- [Azure AD 直通身份验证 - 快速入门](../hybrid/how-to-connect-pta-quick-start.md#step-4-ensure-high-availability)

## <a name="management-at-scale"></a>大规模管理

### <a name="identity-secure-score"></a>标识安全分数

[标识安全分数](./identity-secure-score.md)为组织的安全局势提供可量化的度量。 持续评审并解决报告的问题并努力达到最高分数至关重要。 评分有助于：

- 客观衡量标识安全状况
- 规划标识安全改进
- 审查改进措施的成败

![安全功能分数](./media/active-directory-ops-guide/active-directory-ops-img17.png)

如果你的组织目前未制定任何计划来监视标识安全分数的变化，我们建议实施一个计划，并指派所有者来监视和推动改进措施。 组织应尽快修正分数影响力高于 30 的改进措施。

### <a name="notifications"></a>通知

Microsoft 将向管理员发送电子邮件，通知服务发生的各种更改、所需的配置更新，以及需要管理员干预的错误。 客户必须设置通知电子邮件地址，以便将通知发送到可以确认所有通知并对其采取措施的适当团队成员。 建议将多个收件人添加到[消息中心](/office365/admin/manage/message-center)，并请求将通知（包括 Azure AD Connect Health 通知）发送到通讯组列表或共享的邮箱。 如果你只有一个附带电子邮件地址的全局管理员帐户，请确保至少配置两个支持电子邮件的帐户。

Azure AD 使用两个“发件人”地址：<o365mc@email2.microsoft.com>，用于发送消息中心通知的；<azure-noreply@microsoft.com>，用于发送与以下各项相关的通知：

- [Azure AD 访问评审](../governance/access-reviews-overview.md)
- [Azure AD Connect Health](../hybrid/how-to-connect-health-operations.md#enable-email-notifications)
- [Azure AD 标识保护](../identity-protection/howto-identity-protection-configure-notifications.md)
- [Azure AD Privileged Identity Management](../privileged-identity-management/pim-email-notifications.md)
- [企业应用到期证书通知](../manage-apps/manage-certificates-for-federated-single-sign-on.md#add-email-notification-addresses-for-certificate-expiration)
- 企业应用预配服务通知

请参阅下表了解发送的通知类型以及通知的查看位置：

| 通知源 | 发送的内容 | 检查位置 |
|:-|:-|:-|
| 技术联系人 | 同步错误 | Azure 门户 -“属性”边栏选项卡 |
| 消息中心 | 标识服务和 Microsoft 365 后端服务的事件与降级通知 | Office 门户 |
| 标识保护每周摘要 | 标识保护摘要 | “Azure AD 标识保护”边栏选项卡 |
| Azure AD Connect Health | 警报通知 | Azure 门户 -“Azure AD Connect Health”边栏选项卡 |
| 企业应用程序通知 | 证书即将过期和发生预配错误时的通知 | Azure 门户 -“企业应用程序”边栏选项卡（每个应用具有自身的电子邮件地址设置） |

#### <a name="notifications-recommended-reading"></a>有关通知的推荐阅读文档

- [更改组织的地址、技术联系人等设置](/office365/admin/manage/change-address-contact-and-more)

## <a name="operational-surface-area"></a>操作外围应用

### <a name="ad-fs-lockdown"></a>AD FS 锁定

将应用程序配置为直接向 Azure AD 进行身份验证的组织可以受益于 [Azure AD 智能锁定](../authentication/concept-sspr-howitworks.md)。 如果你在 Windows Server 2012 R2 中使用 AD FS，请实施 AD FS [Extranet 锁定保护](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-soft-lockout-protection)。 如果你在 Windows Server 2016 或更高版本中使用 AD FS，请实施 [Extranet 智能锁定](https://support.microsoft.com/help/4096478/extranet-smart-lockout-feature-in-windows-server-2016)。 我们建议至少启用 Extranet 锁定，以遏制本地 Active Directory 遭受暴力攻击的风险。 但是，如果你在 Windows 2016 或更高版本中使用 AD FS，则还应启用 Extranet 智能锁定，以帮助缓解[密码喷洒](https://www.microsoft.com/microsoft-365/blog/2018/03/05/azure-ad-and-adfs-best-practices-defending-against-password-spray-attacks/)攻击。

如果 AD FS 仅用于 Azure AD 联合身份验证，则可以禁用某些终结点以最大程度地减少受攻击面。 例如，如果 AD FS 仅用于 Azure AD，则除了为 usernamemixed 和 windowstransport 启用的终结点之外，应禁用其他 WS-Trust 终结点 。

### <a name="access-to-machines-with-on-premises-identity-components"></a>访问具有本地标识组件的计算机

组织应该像锁定对本地域的访问那样，锁定对具有本地混合组件的计算机的访问。 例如，备份操作员或 Hyper-V 管理员应该无法登录到 Azure AD Connect 服务器来更改规则。

Active Directory 管理层模型用于在环境（第 0 层）完全控制和高风险工作站资产（经常受到攻击者侵害）之间使用一组缓冲区来保护标识系统。 ![层模型的三个层图示](./media/active-directory-ops-guide/active-directory-ops-img18.png)

[层模型](/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material)由三个级别组成，只包含管理帐户而不包含标准用户帐户。

- **第 0 层** - 直接控制环境中的企业标识。 第 0 层包括帐户、组以及对 Active Directory 林、域或域控制器及其中的所有资产具有直接或间接管理控制权的其他资产。 第 0 层中所有资产的安全敏感性是等同的，因为它们均可以有效地相互控制。
- **第 1 层** - 控制企业服务器和应用程序。 第 1 层资产包括服务器操作系统、云服务和企业应用程序。 第 1 层的管理员帐户能够管理控制这些资产上托管的大量业务价值。 常见的示例角色是服务器管理员，此角色可维护这些操作系统并能够影响所有企业服务。
- **第 2 层** - 控制用户工作站和设备。 第 2 层的管理员帐户对用户工作站和设备上托管的大量业务价值具有管理控制权。 示例包括技术支持和计算机支持管理员，因为它们可以影响几乎任何用户数据的完整性。

像锁定对域控制器的访问那样，锁定对 Azure AD Connect、AD FS 和 SQL 服务等本地标识组件的访问。

## <a name="summary"></a>摘要

安全的标识基础结构体现在七个方面。 以下列表可帮助你确定在优化 Azure Active Directory (Azure AD) 的操作时应采取的措施。

- 将所有者分配到关键任务。
- 自动完成本地混合组件的升级过程。
- 部署 Azure AD Connect Health 用于监视 Azure AD Connect 和 AD FS 并报告其状态。
- 使用 System Center Operations Manager 或 SIEM 解决方案存档和分析组件代理日志，依此监视本地混合组件的运行状况。
- 根据标识安全分数衡量安全局势，依此实施安全改进措施。
- 锁定 AD FS。
- 锁定对具有本地标识组件的计算机的访问。

## <a name="next-steps"></a>后续步骤

有关未部署的任何功能的实施详细信息，请参阅 [Azure AD 部署计划](active-directory-deployment-plans.md)。