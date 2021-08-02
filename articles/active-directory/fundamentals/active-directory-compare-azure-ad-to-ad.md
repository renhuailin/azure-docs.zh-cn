---
title: 将 Active Directory 与 Azure Active Directory 进行比较
description: 本文档将 Active Directory 域服务 (ADDS) 与 Azure Active Directory (AD) 进行了比较。 它概述了这两个标识解决方案中的关键概念，并解释了它们的不同或相似之处。
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: fundamentals
ms.date: 02/26/2020
ms.author: martinco
ms.openlocfilehash: bca37a0e30240732443bcc08ca76dc9b875af37d
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/10/2021
ms.locfileid: "111955021"
---
# <a name="compare-active-directory-to-azure-active-directory"></a>将 Active Directory 与 Azure Active Directory 进行比较

Azure Active Directory 是适用于云的标识和访问管理解决方案的下一次革命。 Microsoft 在 Windows 2000 中引入了 Active Directory 域服务，使组织能够使用每个用户的单一标识管理多个本地基础结构组件和系统。

通过为组织提供一种适用于其云中和本地所有应用的标识即服务 (IDaaS) 解决方案，Azure AD 将此方法提升到了一个新层次。

大多数 IT 管理员都熟悉 Active Directory 域服务概念。 下表概述了 Active Directory 概念与 Azure Active Directory 之间的差异和相似之处。

|概念|Active Directory (AD)|Azure Active Directory |
|:-|:-|:-|
|**用户**|||
|预配：用户 | 组织手动创建内部用户，或者使用内部或自动化预配系统（例如 Microsoft Identity Manager）来与 HR 系统集成。|现有 AD 组织使用 [Azure AD Connect](../hybrid/how-to-connect-sync-whatis.md) 将标识同步到云。</br> Azure AD 现支持从[云 HR 系统](../saas-apps/workday-tutorial.md)自动创建用户。 </br>Azure AD 可在[已启用 SCIM](../app-provisioning/use-scim-to-provision-users-and-groups.md) 的 SaaS 应用中预配标识来自动为应用提供必要的详细信息，从而允许用户访问。 |
|预配：外部标识| 组织在专用的外部 AD 林中作为一般用户手动创建外部用户，导致产生管理外部标识（来宾用户）生命周期的管理开销| Azure AD 提供了特殊的标识类来支持外部标识。 [Azure AD B2B](/azure/active-directory/b2b/) 会管理指向外部用户标识的链接，以确保它们是有效的。 |
| 权利管理和组| 管理员使用户成为组的成员。 然后，应用和资源所有者向组授予对应用或资源的访问权限。| Azure AD 中也提供了[组](./active-directory-groups-create-azure-portal.md)，管理员也可以使用组来授予对资源的权限。 在 Azure AD 中，管理员可以手动将成员身份分配到组，也可以使用查询动态地将用户包括到组中。 </br> 管理员可以使用 Azure AD 中的[权利管理](../governance/entitlement-management-overview.md)，以使用户能够使用工作流以及基于时间的条件（如有必要）访问应用和资源的集合。 |
| 管理员管理|组织将在 AD 中使用域、组织单位和组的组合来委派管理权限，以管理其控制的目录和资源。| Azure AD 为[内置角色](./active-directory-users-assign-role-azure-portal.md)提供了 Azure AD 基于角色的访问控制 (Azure AD RBAC) 系统，带有对[创建自定义角色](../roles/custom-overview.md)的有限支持，以用于委派对它控制的标识系统、应用和资源的特权访问。</br>可以通过 [Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md) 增强对角色的管理，以提供对特权角色的实时、有时间限制或基于工作流的访问。 |
| 凭据管理| Active Directory 中的凭据基于密码、证书身份验证和智能卡身份验证。 密码是使用基于密码长度、有效期和复杂性的密码策略管理的。|Azure AD 对云和本地都使用智能[密码保护](../authentication/concept-password-ban-bad.md)。 保护包括智能锁定，以及阻止通用和自定义密码短语和替换。 </br>Azure AD [通过多重身份验证](../authentication/concept-mfa-howitworks.md)和[无密码](../authentication/concept-authentication-passwordless.md)技术（例如 FIDO2）显著提高了安全性。 </br>Azure AD 通过向用户提供[自助式密码重置](../authentication/concept-sspr-howitworks.md)系统来降低支持成本。 |
| 应用|||
| 基础结构应用|Active Directory 构成了许多基础结构本地组件的基础，例如 DNS、DHCP、IPSec、WiFi、NPS 和 VPN 访问|在新的云环境中，Azure AD 是用于访问应用的新控制平面，而不是依赖于网络控制。 当用户进行身份验证时，[条件访问 (CA)](../conditional-access/overview.md) 将控制在所需条件下哪些用户可以访问哪些应用。|
| 传统应用和旧式应用| 大多数本地应用都使用 LDAP、Windows 集成身份验证（NTLM 和 Kerberos）或基于标头的身份验证来控制对用户的访问。| Azure AD 可使用在本地运行的 [Azure AD 应用程序代理](../app-proxy/application-proxy.md)程序来访问这些类型的本地应用。 通过此方法，Azure AD 可在你进行迁移或需要与旧式应用共存时，在本地使用 Kerberos 对 Active Directory 用户进行身份验证。 |
| SaaS 应用|Active Directory 不提供对 SaaS 应用的本机支持，而且需要联合系统（例如 AD FS）。|可集成支持 OAuth2、SAML 和 WS 身份验证的 SaaS 应用来使用 Azure AD 进行身份验证。 |
| 采用新式身份验证的业务线 (LOB) 应用|组织可以将 AD FS 与 Active Directory 配合使用来支持需要新式身份验证的 LOB 应用。| 需要新式身份验证的 LOB 应用可以配置为使用 Azure AD 进行身份验证。 |
| 中间层级/守护程序服务|在本地环境中运行的服务通常使用 AD 服务帐户或组托管服务帐户 (gMSA) 来运行。 然后，这些应用将继承服务帐户的权限。| Azure AD 提供[托管标识](../managed-identities-azure-resources/index.yml)，以在云中运行其他工作负荷。 这些标识的生命周期由 Azure AD 管理，并绑定到资源提供程序，无法用于其他目的以获得后门访问。|
| **设备**|||
| 移动型|在没有第三方解决方案的情况下，Active Directory 本身不支持移动设备。| Microsoft 的移动设备管理解决方案 Microsoft Intune 集成了 Azure AD。 Microsoft Intune 向标识系统提供在身份验证期间要评估的设备状态信息。 |
| Windows 桌面|通过 Active Directory，可在域中加入 Windows 设备来使用组策略、System Center Configuration Manager 或其他第三方解决方案管理这些设备。|Windows 设备可[加入到 Azure AD](../devices/index.yml)。 条件访问可检查设备是否是在身份验证过程中加入 Azure AD 的。 还可通过 [Microsoft Intune](/intune/what-is-intune) 管理 Windows 设备。 在这种情况下，条件访问将考虑设备是否合规（例如最新的安全修补程序和防病毒签名），然后才允许访问应用。|
| Windows 服务器| Active Directory 为使用组策略或其他管理解决方案的本地 Windows 服务器提供了强大的管理功能。| 可以通过 [Azure AD 域服务](../../active-directory-domain-services/index.yml)来管理 Azure 中的 Windows Server 虚拟机。 当 VM 需要访问标识系统目录或资源时，可以使用[托管标识](../managed-identities-azure-resources/index.yml)。|
| Linux/Unix 工作负荷|尽管 Linux 计算机可以配置为向 Active Directory（作为 Kerberos 领域）进行身份验证，但 Active Directory 在没有第三方解决方案的情况下本身不支持非 Windows。|Linux/Unix VM 可以使用[托管标识](../managed-identities-azure-resources/index.yml)来访问标识系统或资源。 某些组织将这些工作负荷迁移到云容器技术，这些技术也可以使用托管标识。|

## <a name="next-steps"></a>后续步骤

- [什么是 Azure Active Directory？](./active-directory-whatis.md)
- [自我管理型 Azure Active Directory 域服务、Azure Active Directory 和托管型 Azure Active Directory 域服务的比较](../../active-directory-domain-services/compare-identity-solutions.md)
- [有关 Azure Active Directory 的常见问题](./active-directory-faq.yml)
- [Azure Active Directory 中的新增功能](./whats-new.md)