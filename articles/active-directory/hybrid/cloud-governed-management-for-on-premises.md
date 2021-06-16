---
title: 针对本地工作负载的 Azure AD 云调控管理 - Azure
description: 本主题介绍针对本地工作负载的云调控管理。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: ea87a6729e6f8339ad92bc9d57e7152199349ac5
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/10/2021
ms.locfileid: "111965935"
---
# <a name="how-azure-ad-delivers-cloud-governed-management-for-on-premises-workloads"></a>Azure AD 如何针对本地工作负载提供云调控管理

Azure Active Directory (Azure AD) 是综合性的标识即服务 (IDaaS) 解决方案，已由涉及到标识、访问管理和安全性等方方面面的数百万家组织采用。 Azure AD 包含超过十亿的用户标识，可帮助用户登录并安全访问以下两种资源：

* 外部资源，例如 Microsoft 365、Azure 门户，以及成千上万的其他软件即服务 (SaaS) 应用程序。
* 内部资源，例如组织的企业网络和 Intranet 上的应用程序，以及该组织开发的任何云应用程序。

如果组织的工作负载是“纯云”工作负载，则它们可以使用 Azure AD；如果组织的工作负载位于本地，则可以采用“混合”部署。 可将 Azure AD 的混合部署加入到组织的策略中，以将其 IT 资产迁移到云，或者继续连同新云服务一起集成现有的本地基础结构。

在过去，“混合”组织一直将 Azure AD 视作其现有本地基础结构的延伸。 在这些部署中，本地标识调控管理、Windows Server Active Directory 或其他内部目录系统都是控制点，用户和组将从这些系统同步到 Azure AD 等云目录。 一旦这些标识位于云中，即可将其用于 Microsoft 365、Azure 和其他应用程序。

![标识生命周期](media/cloud-governed-management-for-on-premises//image1.png)

随着组织将更多的 IT 基础结构连同其应用程序一起迁移到云中，有许多组织都在寻求方法来提高安全性并简化服务形式的标识管理的管理功能。 Azure AD 的云交付 IDaaS 功能加速了组织过渡到云调控管理，因为它提供的解决方案和功能可使组织快速采用更多的标识管理，并将其从传统的本地系统迁移到 Azure AD，同时还能继续支持现有应用程序和新的应用程序。

本文将会概述 Microsoft 为混合 IDaaS 提供的策略，并介绍组织如何为其现有应用程序使用 Azure AD。

## <a name="the-azure-ad-approach-to-cloud-governed-identity-management"></a>Azure AD 的云调控标识管理方法

在组织过渡到云的过程中，它们需要确保能够控制其整个环境 - 拥有更高的安全性、更高的活动洞察力、受自动化的支持，并且能够获得前瞻性的见解。 “云调控管理”描述组织如何从云管理与调控其用户、应用程序、组和设备。

在这个现代化的世界中，随着 SaaS 应用程序的激增以及协作职能和外部标识的不断增多，组织需要具备大规模有效管理的能力。 云的新风险形势意味着组织必须提高响应能力 - 入侵了云用户的恶意行动者可能会影响云中和本地的应用程序。

具体而言，混合组织需要能够委托和自动化任务，而在过去，这些任务需要由 IT 工作人员手动完成。 若要将任务自动化，他们需要使用 API 和流程来协调标识相关的各个资源（用户、组、应用程序、设备）的生命周期，以便可以将这些资源的日常管理委托给核心 IT 工作人员以外的其他个人。 Azure AD 无需本地标识基础结构，即可通过用户帐户管理和用户本机身份验证解决这些要求。 不构建本地基础结构可能有利于具有新用户社区的组织，例如业务合作伙伴，这些组织不是来源于其本地目录，但其访问权限管理对于实现业务成果至关重要。

此外，没有调控的管理算不上完整 --- 在这个新时代，调控是标识系统不可或缺的组成部分，而不是一个添头。 标识调控使组织能够管理所有员工、业务合作伙伴与供应商，以及服务和应用程序的标识与访问权限生命周期。

整合标识调控使组织能够更轻松地过渡到云调控管理，使 IT 部门能够调整规模，解决来宾方面的新挑战，并为客户提供比本地基础结构更深入的见解和自动化。 在这个新时代，调控意味着组织能够获得对组织中资源的访问活动的透明性、可见性和适当控制。 借助 Azure AD，安全运营和审核团队可以审视哪些用户拥有以及哪些用户应该拥有对组织中（哪些设备上的）哪些资源的访问权限、这些用户正在使用该访问权限执行哪些操作，以及组织是否具有并使用适当的控制措施来根据公司或法规策略删除或限制访问权限。

具有 SaaS 和业务线 (LOB) 应用程序的组织可以从新的管理模型中受益，因为它们能够更轻松地管理和保护对这些应用程序的访问。 通过将应用程序与 Azure AD 集成，组织能够以一致的方式针对源自云的标识与源自本地的标识使用和管理访问权限。 应用程序生命周期管理的自动化程度变得更高，并且 Azure AD 会提供对应用程序使用情况的丰富见解，而这些见解在本地标识管理中却不容易获得。 通过 Azure AD、Microsoft 365 组和 Teams 自助服务功能，组织可以轻松创建组以用于访问权限管理和协作，并在云中添加或删除用户，以实现协作和访问权限管理要求。

哪些 Azure AD 功能适合用于云调控管理取决于要使用的应用程序，以及这些应用程序与 Azure AD 集成的方式。 以下部分概述了要对 AD 集成的应用程序以及使用联合身份验证协议（例如 SAML、OAuth 或 OpenID Connect）的应用程序采用的方法。

## <a name="cloud-governed-management-for-ad-integrated-applications"></a>适用于 AD 集成的应用程序的云调控管理

对于组织中本地 Active Directory 集成的应用程序，Azure AD 通过针对这些应用程序实施的安全远程访问和条件访问改进了其管理。 此外，对于用户的现有 AD 帐户，Azure AD 还提供帐户生命周期管理和凭据管理，包括：

* **针对本地应用程序的安全远程访问和条件访问**

对于许多组织而言，从云管理对本地 AD 集成的 Web 应用程序和基于远程桌面的应用程序的访问的第一步是在这些应用程序的前面部署[应用程序代理](../app-proxy/application-proxy.md)，以提供安全远程访问。

单一登录到 Azure AD 后，用户可以通过外部 URL 或内部应用程序门户访问云端和本地的应用程序。 例如，应用程序代理提供远程访问和单一登录到远程桌面、SharePoint 以及 Tableau 和 Qlik 等应用和业务线 (LOB) 应用程序的功能。 此外，条件访问策略可以包括显示[使用条款](../conditional-access/terms-of-use.md)并确保用户在访问应用程序之前[同意这些条款](../conditional-access/require-tou.md)。

![应用代理体系结构](media/cloud-governed-management-for-on-premises/image2.png)

* **Active Directory 帐户的自动生命周期管理**

标识调控可帮助组织在以下需求之间实现平衡：工作效率 --- 用户能够以多快的速度访问所需的资源（例如在刚入职时）？ 安全性 --- 用户的访问权限会随时间的推移发生怎样的变化（例如，该用户的雇佣状态发生变化时）？ 标识生命周期管理是标识监管的基石，大规模的有效监管需要将应用程序的标识生命周期管理基础结构现代化。

对于许多组织而言，员工的标识生命周期与该用户在人力资本管理 (HCM) 系统中的表示形式密切相关。 对于使用 Workday 作为 HCM 系统的组织，Azure AD 可确保为 [Workday 中的工作人员自动预配和取消预配](../saas-apps/workday-inbound-tutorial.md) AD 中的用户帐户。 这样就通过自动预配拥有本该拥有的权限的帐户来提高用户工作效率，同时，确保在用户的角色改变或用户离职时自动更新应用程序访问权限，从而可以控制风险。 Workday 驱动的用户预配[部署计划](https://aka.ms/WorkdayDeploymentPlan)是一个循序渐进的指南，可以引导组织通过包括五个步骤的过程，以最佳做法实现 Workday 到 Active Directory 的用户预配解决方案。

Azure AD Premium 还包含可从本地 HCM 系统（包括 SAP、Oracle eBusiness 和 Oracle PeopleSoft）导入记录的 Microsoft Identity Manager。

企业间的协作越来越多地要求向组织外部的人员授予访问权限。 组织可以使用 [Azure AD B2B](/azure/active-directory/b2b/) 协作安全地将其应用程序和服务与来宾用户和外部合作伙伴共享，同时保持对自己企业数据的控制。

Azure AD 可以根据需要[在 AD 中自动为来宾用户创建帐户](../external-identities/hybrid-cloud-to-on-premises.md)，使企业来宾无需输入其他密码即可访问本地 AD 集成的应用程序。 组织可[为来宾用户设置多重身份验证 (MFA) 策略](../external-identities/conditional-access.md)，以便在应用程序代理身份验证期间执行 MFA 检查。 另外，针对云 B2B 用户执行的任何[访问评审](../governance/manage-guest-access-with-access-reviews.md)也适用于本地用户。 例如，如果通过生命周期管理策略删除了云用户，则本地用户也会一并删除。

Active Directory 帐户的凭据管理：Azure AD 的自助式密码重置功能可让忘记了密码的用户重新完成身份验证并重置其密码，更改后的密码将[写入本地 Active Directory](../authentication/concept-sspr-writeback.md)。 密码重置过程还可使用本地 Active Directory 密码策略：如果用户重置密码，系统会检查此请求，以确保它符合本地 Active Directory 策略要求，然后再将请求提交到相应目录。 自助式密码重置[部署计划](../authentication/howto-sspr-deployment.md)概述了通过 Web 和 Windows 集成式体验向用户推出自助式密码重置的最佳做法。

![Azure AD SSPR 体系结构](media/cloud-governed-management-for-on-premises/image3.png)

最后，对于允许用户在 AD 中更改其密码的组织，可以通过 [Azure AD 密码保护功能](../authentication/concept-password-ban-bad-on-premises.md)（目前为公共预览版），将 AD 配置为使用组织在 Azure AD 中所用的相同密码策略。

当组织准备好将 AD 集成的应用程序迁移云时（可通过将托管应用程序的操作系统迁移到 Azure 来实现此目的），[Azure AD 域服务](../../active-directory-domain-services/overview.md)提供与 AD 兼容的域服务（例如域加入、组策略、LDAP 和 Kerberos/NTLM 身份验证）。 Azure AD 域服务与组织的现有 Azure AD 租户集成，因此可让用户使用其企业凭据登录。 此外，可以使用现有的组和用户帐户安全访问资源，确保更顺畅地将本地资源“直接迁移”到 Azure 基础结构服务。

![Azure AD 域服务](media/cloud-governed-management-for-on-premises/image4.png)

## <a name="cloud-governed-management-for-on-premises-federation-based-applications"></a>适用于基于联合身份验证的本地应用程序的云调控管理

对于已使用本地标识提供者的组织，将应用程序迁移到 Azure AD 可使访问变得更安全，并简化联合身份验证管理的管理体验。 Azure AD 支持使用 Azure AD 条件访问配置细致的按应用程序访问控制，包括 Azure AD 多重身份验证。 Azure AD 支持更多功能，包括特定于应用程序的令牌签名证书和可配置的证书到期日期。 拥有这些功能、工具和指导，组织就可以停用其本地标识提供者。 举个例子，Microsoft 自己的 IT 部门已将 17,987 个应用程序从 Microsoft 的内部 Active Directory 联合身份验证服务 (AD FS) 迁移到了 Azure AD。

![Azure AD 的演进](media/cloud-governed-management-for-on-premises/image5.png)

若要开始将联合应用程序迁移到 Azure AD 标识提供者，请参阅 https://aka.ms/migrateapps ，其中包含以下资源的链接：

* 白皮书[将应用程序迁移到 Azure Active Directory](https://aka.ms/migrateapps/whitepaper)，其中介绍了迁移的优势，以及如何规划通过四个明确概述的阶段完成迁移：发现、分类、迁移和持续管理。 此白皮书将引导你思考如何规划该过程，并将项目分解为易于使用的部分。 整个文档为指向重要资源的链接，可全程为你提供帮助。

* 解决方案指南[将应用程序身份验证从 Active Directory 联合身份验证服务迁移到 Azure Active Directory](../manage-apps/migrate-adfs-apps-to-azure.md) 更详细地探讨了规划和执行应用程序迁移项目的四个阶段。 此指南介绍了如何将这些阶段应用于将应用程序从 Active Directory 联合身份验证服务 (AD FS) 迁移到 Azure AD 的特定目标。

* 可以在现有的本地 Active Directory 联合身份验证服务 (AD FS) 服务器上运行 [Active Directory 联合身份验证服务迁移就绪状态脚本](https://aka.ms/migrateapps/adfstools)，以确定要迁移到 Azure AD 的应用程序的就绪状态。

## <a name="ongoing-access-management-across-cloud-and-on-premises-applications"></a>跨云和本地应用程序的持续访问管理

组织需要通过一个流程来管理可缩放的访问权限。 用户的访问权限会不断累积，最终超出了最初为他们预配的权限。 此外，企业组织需要能够有效缩放，以便持续制定和实施访问策略与控制措施。

通常，IT 部门会将访问权限审批决策委托给业务决策人。 此外，IT 部门可能涉及到用户本身的事务。 例如，访问公司位于欧洲的市场营销应用程序中的机密客户数据的用户需要知道该公司的策略。 此外，来宾用户可能不知道他们受邀访问的组织的数据处理要求。

组织可以通过[动态组](../enterprise-users/groups-dynamic-membership.md)等技术，并结合 [SaaS 应用程序](../saas-apps/tutorial-list.md)中的用户预配或者[使用跨域标识管理系统 (SCIM) 标准集成的应用程序](../app-provisioning/use-scim-to-provision-users-and-groups.md)，将访问生命周期过程自动化。 组织还可以控制哪些[来宾用户有权访问本地应用程序](../external-identities/hybrid-cloud-to-on-premises.md)。 然后，可以使用 [Azure AD 访问评审](../governance/access-reviews-overview.md)定期评审这些访问权限。

## <a name="future-directions"></a>未来发展方向

在混合环境中，Microsoft 的策略是实现如下所述的部署：让云充当标识的控制平面，而本地目录和其他标识系统（例如 Active Directory 和其他本地应用程序）充当预配拥有访问权限的用户的目标。 此策略可持续确保在应用程序以及依赖于它们的工作负载中提供正确的权限、标识和访问权限。 达到这种最终状态后，组织就能够彻底地通过云推动最终用户的生产力。

![Azure AD 体系结构](media/cloud-governed-management-for-on-premises/image6.png)

## <a name="next-steps"></a>后续步骤

有关如何开始此旅程的详细信息，请参阅 <https://aka.ms/deploymentplans> 上的 Azure AD 部署计划。 这些计划提供了有关如何部署 Azure Active Directory (Azure AD) 功能的端到端指导。 每个计划都解释了业务价值、规划注意事项、设计，以及成功推出常用 Azure AD 功能所需的操作过程。 新增了使用 Azure AD 从云进行管理的功能后，Microsoft 会根据我们从客户部署和其他反馈中学得的最佳做法持续更新部署计划。