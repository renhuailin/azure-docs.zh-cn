---
title: 计划单一登录部署
description: 在 Azure Active Directory 中规划单一登录的部署。
titleSuffix: Azure AD
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 09/22/2021
ms.author: davidmu
ms.reviewer: ergreenl
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: d6daa398c560bf7112539291f76fe4188abb76a1
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129231608"
---
# <a name="plan-a-single-sign-on-deployment-in-azure-active-directory"></a>在 Azure Active Directory 中规划单一登录部署

可以使用本文提供的信息在 Azure Active Directory (Azure AD) 中规划[单一登录 (SSO)](what-is-single-sign-on.md) 部署。 为 Azure AD 中的应用程序规划 SSO 部署时，需要考虑以下问题：

- 需要使用哪些管理角色来管理应用程序？
- 是否需要续订证书？
- 需要向哪些人通知与 SSO 实现相关的更改？
- 需要使用哪些许可证来确保有效管理应用程序？
- 是否使用共享用户帐户来访问应用程序？
- 我是否了解 SSO 部署选项？

## <a name="administrative-roles"></a>管理角色

始终使用具有可供完成 Azure AD 中所需任务的最低权限的角色。 查看可用的不同角色，并且选择正确的角色 (role) 来满足你对应用程序的每个角色 (persona) 的需求。 可能需要暂时应用某些角色，在部署完成后再将其删除。

| 角色 | 角色 | Azure AD 角色（如有必要） |
| ------- | ----- | --------------------------- |
| 咨询台管理员 | 第 1 层支持 | 无 |
| 标识管理员 | 在问题涉及 Azure AD 时进行配置和调试 | 全局管理员 |
| 应用程序管理员 | 在应用程序中进行用户证明，为用户配置权限 | 无 |
| 基础结构管理员 | 证书滚动更新所有者 | 全局管理员 |
| 企业主/利益干系人 | 在应用程序中进行用户证明，为用户配置权限 | 无 |

要详细了解 Azure AD 管理角色，请参阅 [Azure AD 内置角色](../users-groups-roles/directory-assign-admin-roles.md)。

## <a name="certificates"></a>证书

当你为应用程序启用联合 SSO 时，Azure AD 会创建默认情况下有效期为三年的证书。 可以根据需要自定义该证书的到期日期。 请确保已做好相关流程的准备，以便在证书到期之前续订证书。 

可以在 Azure 门户中更改该证书持续时间。 请确保记录过期情况并了解如何管理证书续订。 确定与签名证书生命周期管理相关的适当角色和电子邮件通讯组非常重要。 建议分配以下角色：

- 在应用程序中更新用户属性的所有者
- 提供应用程序故障排除支持的待命所有者
- 受到密切监视的电子邮件通讯组列表（用于接收与证书相关的更改通知）

可以使用以下方法来管理证书：

- 证书自动滚动更新 - Azure AD 支持签名密钥滚动更新。 虽然签名密钥滚动更新是管理证书的首选方法，但并非所有应用程序都支持此方案。

- 手动更新 - 每个应用程序都有自己的可根据定义方式过期的证书。 在应用程序的证书过期之前，请创建新的证书并将其发送到应用程序提供商。 此信息可从联合元数据拉取。 有关详细信息，请参阅[联合元数据](../azuread-dev/azure-ad-federation-metadata.md)。 

制定一个关于如何处理 Azure AD 与应用程序之间的证书更改的流程。 实施此流程有助于防止或最大程度地减少由于证书过期或强制证书滚动更新而导致的服务中断。 有关详细信息，请参阅[在 Azure Active Directory 中管理用于联合单一登录的证书](manage-certificates-for-federated-single-sign-on.md)。

## <a name="communications"></a>通信

沟通对于任何新服务的成功都至关重要。 主动向用户传达有关其体验将发生变化的情况。 传达体验何时会发生变化，以及他们在遇到问题时如何获得支持。 查看有关用户将如何访问已启用 SSO 的应用程序的选项，并根据你的选择编写传达内容。

实现通信计划。 确保让用户知道即将进行更改、何时收到该更改，以及现在该做什么。 此外，确保提供有关如何寻求帮助的信息。

## <a name="licensing"></a>许可

确保以下许可要求涵盖了该应用程序：

- Azure AD 许可 - 适用于预先集成的企业应用程序的 SSO 是免费的。 但是，你可能需要根据目录中的对象数和要部署的功能购买其他许可证。 有关许可证要求的完整列表，请参阅 [Azure Active Directory 定价](https://www.microsoft.com/security/business/identity-access-management/azure-ad-pricing)。

- 应用程序许可 - 需要根据业务需求购买适用于你的应用程序的许可证。 与应用程序所有者合作，确定分配到应用程序的用户是否具有与其在应用程序中的角色相对应的许可证。 如果 Azure AD 基于角色来管理自动预配，则在 Azure AD 中分配的角色必须与在应用程序中拥有的许可证数量一致。 如果在应用程序中拥有的许可证数量不正确，则可能会导致在用户帐户的预配或更新过程中出现错误。

## <a name="shared-accounts"></a>共享的帐户

从登录的角度来看，使用共享帐户的应用程序并未不同于对单个用户使用密码 SSO 的企业应用程序。 但是，在规划和配置要使用共享帐户的应用程序时，还需要执行其他步骤。
- 与用户合作以记录以下信息：
    - 组织中要使用应用程序的用户集。
    - 应用程序中与用户集关联的现有凭据集。
- 对于用户集和凭据的每个组合，请根据你的需求在云中或本地创建安全组。
- 重置共享凭据。 将应用程序部署到 Azure AD 中以后，个人不需要共享帐户的密码。 Azure AD 会存储密码，因此请考虑设置较长且复杂的密码。
- 在应用程序支持的情况下配置密码的自动滚动更新。 这样，即使是进行了初始设置的管理员也不会知道共享帐户的密码。

## <a name="single-sign-on-options"></a>单一登录选项

可通过多种方法配置应用程序以实现 SSO。 选择哪种 SSO 方法取决于如何为应用程序配置身份验证。
- 云应用程序可以使用 OpenID Connect、OAuth、SAML、基于密码的方法或链接方法来实现 SSO。 还可以禁用单一登录。
- 本地应用程序可以使用基于密码的方法、集成 Windows 身份验证方法、基于头的方法或链接方法来实现 SSO。 为应用程序配置了[应用程序代理](../app-proxy/what-is-application-proxy.md)时，本地选项适用。

此流程图可帮助你确定哪种 SSO 方法最适合你的情况。

![单一登录方法的决策流程图](./media/plan-sso-deployment/single-sign-on-options.png)
 
可以使用以下 SSO 协议：

- OpenID Connect 和 OAuth - 如果你要连接到的应用程序支持 OpenID Connect 和 OAuth 2.0，请选择这两种协议。 有关详细信息，请参阅 [Microsoft 标识平台上的 OAuth 2.0 和 OpenID Connect 协议](../develop/active-directory-v2-protocols.md)。 有关实现 OpenID Connect SSO 的步骤，请参阅[为 Azure Active Directory 中的应用程序设置基于 OIDC 的单一登录](add-application-portal-setup-oidc-sso.md)。

- SAML - 尽可能为不使用 OpenID Connect 或 OAuth 的现有应用程序选择 SAML。 有关详细信息，请参阅[单一登录 SAML 协议](../develop/single-sign-on-saml-protocol.md)。 有关实现 SAML SSO 的简介，请参阅[快速入门：为 Azure Active Directory 中的应用程序设置基于 SAML 的单一登录](add-application-portal-setup-sso.md)。 

- 基于密码 - 如果应用程序具有 HTML 登录页，请选择基于密码的方法。 基于密码的 SSO 也称为密码保管。 基于密码的 SSO，可用于管理不支持标识联合的 Web 应用程序中的用户访问权限和密码。 如果有多个用户需要共享单个帐户（例如共享组织的社交媒体应用帐户），此功能也很有用。 

    基于密码的 SSO 支持需要多个登录字段的应用程序，这适用于不只需要用户名和密码字段才能登录的应用程序。 可以自定义用户在输入其凭据时在“我的应用”中看到的用户名和密码字段的标签。 有关实现基于密码的 SSO 的步骤，请参阅[基于密码的单一登录](configure-password-single-sign-on-non-gallery-applications.md)。

- 链接 - 如果在其他标识提供者服务中为应用程序配置了 SSO，请选择链接方法。 使用链接选项，可以配置当用户在门户中选择你组织中的应用程序时的目标位置。 可以添加一个链接，该链接需指向当前使用联合身份验证（如 Active Directory 联合身份验证服务 (AD FS)）的自定义 Web 应用程序。 

    还可以添加指向你要在用户访问面板上显示的特定网页以及指向不需要身份验证的应用的链接。 “链接”选项不会通过 Azure AD 凭据提供登录功能。 有关实现链接 SSO 的步骤，请参阅[链接单一登录](configure-linked-sign-on.md)。

- 已禁用 - 如果尚未准备好为应用程序配置 SSO，请选择“已禁用 SSO”。

- **集成 Windows 身份验证 (IWA)** - 对于使用 IWA 的应用程序或声明感知应用程序，请选择 IWA 单一登录。 有关详细信息，请参阅[用于通过应用程序代理单一登录到应用程序的 Kerberos 约束委派](../app-proxy/application-proxy-configure-single-sign-on-with-kcd.md)。

- 基于头 - 如果应用程序使用头进行身份验证，请选择基于头的单一登录。 有关详细信息，请参阅[基于头的 SSO](../app-proxy/application-proxy-configure-single-sign-on-with-headers.md)。

## <a name="next-steps"></a>后续步骤
- [管理对应用的访问权限](what-is-access-management.md)
