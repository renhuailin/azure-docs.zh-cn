---
title: 将应用发布到 Azure Active Directory 应用库
description: 了解如何将支持单一登录的应用程序列入 Azure Active Directory 应用库中。 通过将应用发布到应用库，可使客户更轻松地查找应用并将应用添加到其租户。
services: active-directory
author: kenwith
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 06/23/2021
ms.author: kenwith
ms.reviewer: jeedes
ms.custom: aaddev, contperf-fy21q4
ms.openlocfilehash: 7938e8ffbaca3f069016a445775d9c669f1ad144
ms.sourcegitcommit: 92dd25772f209d7d3f34582ccb8985e1a099fe62
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/15/2021
ms.locfileid: "114228061"
---
# <a name="publish-your-app-to-the-azure-ad-app-gallery"></a>将应用发布到 Azure AD 应用库

你可以将应用发布到 Azure Active Directory (Azure AD) 应用库中。 应用发布后将显示为一个选项，客户在[将应用添加到其租户](../manage-apps/add-application-portal.md)时可以选择该选项。 

在 Azure AD 应用库中发布应用的步骤如下：
1. 先决条件
1. 为应用选择适当的单一登录标准。
1. 在应用中实现单一登录。
1. 在应用中实现 SCIM 用户预配（可选）
1. 创建 Azure 租户并测试应用。
1. 创建并发布文档。
1. 提交应用。
1. 加入 Microsoft 合作伙伴网络。

## <a name="what-is-the-azure-ad-application-gallery"></a>什么是 Azure AD 应用程序库？

[Azure AD 应用库](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps?page=1)是一个包含数千应用的目录，在库中可轻松部署和配置单一登录 (SSO) 并自动化用户预配。

将应用添加到 Azure AD 库可带来的一些好处包括：

- 让客户找到可能最适合你的应用的单一登录体验。
- 简化并最小化了应用程序的配置。
- 在库中快速搜索应用程序。
- 免费、基本和高级 Azure AD 客户都可以使用此集成。
- 共同客户可以获得分步配置教程。
- 使用跨域身份管理系统 ([SCIM](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/Provisioning-with-SCIM-getting-started/ba-p/880010)) 的客户可对同一应用使用预配。

此外，当客户使用 Azure AD 作为你的应用的标识提供者时也能获得许多好处。 其中包括：

- 为用户提供单一登录。 使用 SSO（单一登录）可为客户提供方便，从而降低支持成本。 如果启用了一键式 SSO，则客户的 IT 管理员就无需了解如何配置要在其组织中使用的应用程序。 若要详细了解单一登录，请参阅[什么是单一登录？](../manage-apps/what-is-single-sign-on.md)。
- 可以在 Microsoft 365 应用库、Microsoft 365 应用启动器和 Office.com 上的 Microsoft 搜索中发现你的应用。 
- 集成式应用管理。 若要详细了解 Azure AD 中的应用管理，请参阅[什么是应用程序管理？](../manage-apps/what-is-application-management.md)。
- 应用可以使用 [Graph API](/graph/) 来访问可在 Microsoft 生态系统中提高用户工作效率的数据。
- 与 Azure AD 团队共同制作的应用程序特定文档，有助于我们的共同客户采用该应用程序。
- 使客户能够全面管理其员工和来宾身份的身份验证和授权。
- 让客户的这些身份所有者承担所有帐户管理和合规性责任。
- 可以根据客户的业务需求，针对特定的标识提供者、组或用户启用或禁用 SSO。
- 提高适销性和易采用性。 许多大型组织要求（或者热切希望）其员工在所有应用程序中获得无缝的 SSO 体验。 简化 SSO 非常重要。
- 减少最终用户遇到的难题，这可以增加最终用户对应用程序的使用，并可提高收入。
- 使用跨域身份管理系统 ([SCIM](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/Provisioning-with-SCIM-getting-started/ba-p/880010)) 的客户可对同一应用使用预配。
- 通过使用 Azure AD SSO 而不需要单独的凭据，提高了用户登录应用程序时的安全性和便利性。

> [!TIP]
> 让其他公司通过购买或订阅使用你的应用程序时，你的应用程序可供这些公司自己的 Azure 租户内部的客户使用。 这称为创建多租户应用程序。 有关此概念的概述，请参阅 [Azure Active Directory 中的租户](single-and-multi-tenant-apps.md)。

## <a name="prerequisites"></a>先决条件
若要在 Azure AD 库中发布应用，必须首先阅读并同意具体的[条款和条件](https://azure.microsoft.com/support/legal/active-directory-app-gallery-terms/)。

需要一个用于测试的永久性帐户，其中至少注册了两个用户。

- 对于联合应用程序（Open ID 和 SAML/WS-Fed），应用程序必须支持软件即服务 (SaaS) 模型才能列入 Azure AD 应用库中。 企业库应用程序必须支持多个客户配置，而不能仅支持某个特定客户。
- 对于 Open ID Connect，应用程序必须是多租户的，并且必须为应用程序正确实现 [Azure AD 同意框架](../develop/consent-framework.md)。 用户可将登录请求发送到公用终结点，以便任何客户都可以向应用程序提供同意。 你可以根据在令牌中收到的租户 ID 和用户 UPN 来控制用户访问。
- 对于 SAML 2.0/WS-Fed，应用程序必须能够以 SP 或 IDP 模式执行 SAML/WS-Fed SSO 集成。 在提交请求之前，请确保此功能正常工作。
- 对于密码 SSO，请确保你的应用程序支持窗体身份验证，以便可以进行密码保管，从而让单一登录按预期方式工作。
- 需要一个用于测试的永久性帐户，其中至少注册了两个用户。

可以获取一个具有所有高级 Azure AD 功能的免费测试帐户 - 该帐户可免费使用 90 天，并且只要将其用于开发工作，就还能延长其免费使用期限：[加入 Microsoft 365 开发者计划](/office/developer-program/microsoft-365-developer-program)。

## <a name="step-1---choose-the-right-single-sign-on-standard-for-your-app"></a>步骤 1 - 为应用选择适当的单一登录标准

若要将某个应用程序列入 Azure AD 应用库中，请至少实现一个受支持的单一登录选项。 若要了解单一登录选项以及客户如何在 Azure AD 中配置这些选项，请参阅 [SSO 选项](../manage-apps/sso-options.md)。

下表比较了主要标准：使用 OpenID Connect (OIDC) 的 Open Authentication 2.0 (OAuth 2.0)、安全断言标记语言 (SAML) 和 Web Services 联合身份验证 (WS-Fed)。

| 功能| OAuth / OIDC| SAML / WS-Fed |
| - |-|-|
| 基于 Web 的单一登录| √| √ |
| 基于 Web 的单一注销| √| √ |
| 基于移动设备的单一登录| √| √* |
| 基于移动设备的单一注销| √| √* |
| 移动应用程序的条件访问策略| √| √* |
| 移动应用程序的无缝 MFA 体验| √| √* |
| SCIM 预配| √| √ |
| 访问 Microsoft Graph| √| X |

\* 可能支持，但 Microsoft 不提供示例或指导。

### <a name="oauth-20-and-openid-connect"></a>OAuth 2.0 和 OpenID Connect
OAuth 2.0 是用于授权的[行业标准](https://oauth.net/2/)协议。 OpenID Connect (OIDC) 是构建在 OAuth 2.0 协议基础之上的[行业标准](https://openid.net/connect/)标识身份验证层。 

**选择 OAuth/OIDC 的原因**
- 利用这些协议中固有的授权，应用程序可以通过 Microsoft Graph API 来访问和集成丰富的用户数据与组织数据。
- 简化了在为应用程序采用 SSO 时客户的最终用户体验。 可以轻松定义所需的权限集，然后，这些权限集将自动呈现给需要征得其同意的管理员或最终用户。
- 客户可以通过这些协议使用条件访问和多重身份验证 (MFA) 策略来控制对应用程序的访问。 
- Microsoft 提供[跨多个技术平台的库和代码示例](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Samples)来帮助你进行开发。  

**一些考虑因素**
- 如果你已经为应用程序实现了基于 SAML 的单一登录，那么你可能不希望实现新的标准以将应用添加到库中。

### <a name="saml-20-or-ws-fed"></a>选择 SAML 2.0 还是 WS-Fed

SAML 是适用于 Web 应用程序的成熟且广泛采用的[单一登录标准](https://www.oasis-open.org/standards#samlv2.0)。 若要详细了解 Azure 如何使用 SAML，请参阅 [Azure 如何使用 SAML 协议](active-directory-saml-protocol-reference.md)。 

Web Services 联合身份验证 (WS-Fed) 是一种[行业标准](https://docs.oasis-open.org/wsfed/federation/v1.2/ws-federation.html)，通常用于使用 .NET 平台开发的 Web 应用程序。

**选择 SAML 的原因**
- SAML 2.0 是成熟的标准，大多数技术平台都支持 SAML 2.0 的开源库。 
- 你可以为客户提供一个管理界面用于配置 SAML SSO。 他们可以配置 Microsoft Azure AD 的 SAML SSO，以及任何其他支持 SAML 的标识提供者。

**一些考虑因素**
- 将 SAML 2.0 或 WSFed 协议用于移动应用程序时，某些条件访问策略（包括多重身份验证 (MFA)）的使用体验将会下降。
- 如果你要访问 Microsoft Graph，则需要通过 OAuth 2.0 实现授权才能生成所需的令牌。 

### <a name="password-based"></a>基于密码
基于密码的 SSO 也称为密码保管，可用于管理不支持标识联合的 Web 应用程序中的用户访问权限和密码。 如果有多个用户需要共享单个帐户（例如共享组织的社交媒体应用帐户），此功能也很有用。


## <a name="step-2---implement-single-sign-on-in-your-app"></a>步骤 2 - 在应用中实现单一登录
库中的每个应用都必须实现一个受支持的单一登录选项。 若要详细了解受支持的选项，请参阅 [SSO 选项](../manage-apps/sso-options.md)。

对于 OAuth 和 OIDC，请参阅[有关身份验证模式的指导](v2-app-types.md)和 [Azure Active Directory 代码示例](sample-v2-code.md)。

对于 SAML 和 WS-Fed，应用程序必须能够以 SP 或 IDP 模式执行 SSO 集成。 在提交请求之前，请确保此功能正常工作。

若要详细了解身份验证，请参阅[什么是身份验证？](authentication-vs-authorization.md)。

> [!IMPORTANT]
> 对于联合应用程序（OpenID 和 SAML/WS-Fed），应用必须支持软件即服务 (SaaS) 模型。 Azure AD 库应用程序必须支持多个客户配置，且不应特定于任何单个客户。

### <a name="implement-oauth-20-and-openid-connect"></a>实现 OAuth 2.0 和 OpenID Connect

对于 OpenID Connect，应用程序必须是多租户的，并且必须为应用程序正确实现 [Azure AD 同意框架](consent-framework.md)。 用户可将登录请求发送到公用终结点，以便任何客户都可以向应用程序提供同意。 你可以根据在令牌中收到的租户 ID 和用户 UPN 来控制用户访问。

若要查看具体示例，请参阅 [Microsoft 标识平台代码示例](sample-v2-code.md)。 

若要查看特定于移动设备的示例，请参阅： 
* [Android](quickstart-v2-android.md)
* [iOS](quickstart-v2-ios.md)
* [通用 Windows 平台](quickstart-v2-uwp.md)

### <a name="implement-saml-20"></a>实现 SAML 2.0

如果你的应用支持 SAML 2.0，则可将它与 Azure AD 租户直接集成。 若要详细了解 Azure AD 中的 SAML 配置，请参阅[配置基于 SAML 的单一登录](../manage-apps/configure-saml-single-sign-on.md)。

Microsoft 不会提供或推荐用于 SAML 实现的库。 有许多可用的开源库。

### <a name="implement-ws-fed"></a>实现 WS-Fed
若要详细了解 ASP.NET Core 中的 WS-Fed，请参阅[通过 ASP.NET Core 中的 WS 联合身份验证对用户进行身份验证](/aspnet/core/security/authentication/ws-federation)。

### <a name="implement-password-vaulting"></a>实现密码保管

创建具有 HTML 登录页的 Web 应用程序。 确保你的应用程序支持窗体身份验证，以便可以进行密码保管，从而让单一登录按预期方式工作。

## <a name="step-3---implement-scim-user-provisioning-in-your-app"></a>步骤 3 - 在应用中实现 SCIM 用户预配
生成应用程序时，支持 [SCIM](https://aka.ms/scimoverview) 预配是可选步骤，但强烈建议使用此步骤。 对 SCIM 标准予以支持非常简单，并且这可以让客户在你的应用中自动创建和更新用户帐户，而无需依赖手动过程，例如上传 CSV 文件。 此外，客户还可以自动删除用户并使组成员身份保持同步，而使用 SAML JIT 等解决方案却做不到这一点。 

### <a name="learn-about-scim"></a>了解 SCIM
若要详细了解 SCIM 标准及其为客户带来的好处，请参阅[使用 SCIM 进行预配 - 入门](https://aka.ms/scimoverview)。

### <a name="understand-the-azure-ad-scim-implementation"></a>了解 Azure AD SCIM 实现
若要详细了解 Azure AD SCIM 实现，请参阅[生成 SCIM 终结点并使用 Azure AD 配置用户预配](../app-provisioning/use-scim-to-provision-users-and-groups.md)。

### <a name="implement-scim"></a>实现 SCIM
Azure AD 提供了[参考代码](https://aka.ms/scimoverview)来帮助你生成 SCIM 终结点。 在 GitHub 上还可以找到许多第三方库/参考。  

## <a name="step-4---create-your-azure-tenant-and-test-your-app"></a>步骤 4 - 创建 Azure 租户并测试应用

需有一个 Azure AD 租户才能测试应用。 若要设置开发环境，请参阅[快速入门：设置租户](quickstart-create-new-tenant.md)。

另外，每个 Microsoft 365 订阅都随附了一个 Azure AD 租户。 若要设置免费的 Microsoft 365 开发环境，请参阅[加入 Microsoft 365 开发人员计划](/office/developer-program/microsoft-365-developer-program)。

创建租户后，测试单一登录并进行[预配](../app-provisioning/use-scim-to-provision-users-and-groups.md#integrate-your-scim-endpoint-with-the-aad-scim-client)。 

对于 OIDC 或 Oath 应用程序，请将[应用程序注册](quickstart-register-app.md)为多租户应用程序。 ‎在“支持的帐户类型”中，选择“任何组织目录中的帐户和个人 Microsoft 帐户”选项。

对于基于 SAML 和基于 WS-Fed 的应用程序，请使用 Azure AD 中的通用 SAML 模板来[配置基于 SAML 的单一登录](../manage-apps/configure-saml-single-sign-on.md)应用程序。

如果需要，还可以[将单租户应用程序转换为多租户应用程序](howto-convert-app-to-be-multi-tenant.md)。


## <a name="step-5---create-and-publish-documentation"></a>步骤 5 - 创建并发布文档

### <a name="documentation-on-your-site"></a>站点上的文档

“易于采用”是企业软件决策中的一个重要因素。 清晰易懂的文档可以在客户的采用旅程中为他们提供帮助，并降低支持成本。 Microsoft 与数千家软件供应商合作，懂得哪些方法是行之有效的。

我们建议至少在站点上的文档中包含以下各项。

* SSO 功能简介
  * 支持的协议
  * 版本和 SKU
  * “支持的标识提供者”列表及文档链接
* 应用程序的许可信息
* 用于配置 SSO 的基于角色的访问控制
* SSO 配置步骤
  * SAML 的 UI 配置元素，以及提供商的预期值
  * 要传递给标识提供者的服务提供商信息
* 如果使用 OIDC/OAuth
  * 需要用户同意才可授予的权限的列表以及业务理由
* 试点用户的测试步骤
* 故障排除信息，包括错误代码和消息
* 客户支持机制
* 有关 SCIM 终结点的详细信息，包括支持的资源和特性

### <a name="documentation-on-the-microsoft-site"></a>Microsoft 站点上的文档

将你的应用程序列入 Azure Active Directory 应用程序库中（这也会在 Azure 市场中发布该应用程序）时，Microsoft 将为我们的共同客户生成用于解释分步过程的文档。 可在[此处](../saas-apps/tutorial-list.md)查看示例。 此文档是根据你在库中提交的内容创建的，如果你要对应用程序进行更改，可以使用 GitHub 帐户轻松更新此文档。


## <a name="step-6---submit-your-app"></a>步骤 6 - 提交应用

在进行了测试并确认你的应用程序集成可在 Azure AD 中正常工作后，请在 [Microsoft 应用程序网络门户](https://microsoft.sharepoint.com/teams/apponboarding/Apps)中提交应用程序请求。

首次尝试登录到该门户时，会看到以下两个屏幕之一。 

如果你收到“不起作用”消息，则需要联系 [Azure AD SSO 集成团队](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)。 请提供要用于提交该请求的电子邮件帐户。 最好是类似于 `name@yourbusiness.com` 的业务电子邮件地址。 Azure AD 团队将在 Microsoft 应用程序网络门户中添加该帐户。

如果你看到“请求访问权限”页，请填写业务理由，然后选择“请求访问权限”。

添加帐户后，可以登录到 Microsoft 应用程序网络门户，然后在主页上选择“提交请求(ISV)”磁贴来提交请求。

![主页上的“提交请求(ISV)”磁贴](./media/howto-app-gallery-listing/homepage.png)

### <a name="issues-on-logging-into-portal"></a>登录到门户时出现问题

如果在登录时看到此错误，请参阅下述有关问题的详细信息及其解决方法。

* 如果登录被阻止，如下所示：

  ![解析库中的应用程序时出现问题](./media/howto-app-gallery-listing/blocked.png)

**发生了什么情况：**

来宾用户已与某个主租户联合，而该租户也是 Azure AD。 来宾用户处于高风险状态。 Microsoft 不允许高风险用户访问其资源。 所有高风险用户（员工或来宾/供应商）必须修正/终止其风险才能访问 Microsoft 资源。 对于来宾用户，这种用户风险来自于主租户，而策略来自于资源租户（在这种情况下为 Microsoft）。
 
**安全的解决方法：**

* 已注册 MFA 的来宾用户修正其自己的用户风险。 可以由执行安全密码更改或重置 (https://aka.ms/sspr) 的来宾用户在其主租户中采取此措施（这需要在主租户中执行 MFA 和 SSPR）。 必须在 Azure AD 中而不是在本地启动安全密码更改或重置。

* 来宾用户请求其管理员修正其风险。 在这种情况下，管理员将执行密码重置（生成临时密码）。 这不需要使用标识保护。 来宾用户的管理员可以转到 https://aka.ms/RiskyUsers 并单击“重置密码”。

* 来宾用户请求其管理员终止/消除其风险。 同样，这不需要使用标识保护。 管理员可以转到 https://aka.ms/RiskyUsers 并单击“消除用户风险”。 但是，管理员应在终止用户风险之前进行尽职调查，确保这是一次误报的风险评估。 否则，如果在未经调查的情况下消除风险评估，会使客户自己以及 Microsoft 的资源面临风险。

> [!NOTE]
> 如果你在访问时遇到任何问题，请联系 [Azure AD SSO 集成团队](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)。

### <a name="implementation-specific-options"></a>特定于实现的选项
如果你希望使用 OpenID Connect 添加要列入库中的应用程序，请如下所示选择“OpenID Connect 和 OAuth 2.0”。

![将 OpenID Connect 应用程序列入库中](./media/howto-app-gallery-listing/openid.png)

如果你希望使用 SAML 2.0 或 WS-Fed 添加要列入库中的应用程序，请选择“SAML 2.0/WS-Fed”  ，如下所示。

![将 SAML 2.0 或 WS-Fed 应用程序列入库中](./media/howto-app-gallery-listing/saml.png)

如果你要使用密码 SSO 添加要列入库中的应用程序，请如下所示选择“密码 SSO (用户名和密码)”。

![将密码 SSO 应用程序列入库中](./media/howto-app-gallery-listing/passwordsso.png)

如果你要为用户预配实现 [SCIM](../app-provisioning/use-scim-to-provision-users-and-groups.md) 2.0 终结点，请如下所示选择相应的选项。 在加入请求中提供架构时，请按照[此处](../app-provisioning/export-import-provisioning-configuration.md)的说明下载架构。 我们将使用你在测试非库应用程序时配置的架构来生成库应用程序。 

   ![请求用户预配](./media/howto-app-gallery-listing/user-provisioning.png)

### <a name="update-or-remove-an-existing-listing"></a>更新或删除现有列表

可以在 [Microsoft 应用程序网络门户](https://microsoft.sharepoint.com/teams/apponboarding/Apps)中更新或删除现有的库应用。

![将 SAML 应用程序列入库中](./media/howto-app-gallery-listing/updateorremove.png)

> [!NOTE]
> 如果你在访问时遇到任何问题，请参阅前面有关创建帐户的部分。 如果这些方法不起作用，请联系 [Azure AD SSO 集成团队](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)。

### <a name="timelines"></a>时间线

将 SAML 2.0 或 WS-Fed 应用程序列入库中这一过程的时间线为 7 到 10 个工作日。

![将 SAML 应用程序列入库中的时间线](./media/howto-app-gallery-listing/timeline.png)

将 OpenID Connect 应用程序列入库中这一过程的时间线为 2 到 5 个工作日。

![将 OpenID Connect 应用程序列入库中的时间线](./media/howto-app-gallery-listing/timeline2.png)

将 SCIM 预配应用程序列入库中这一过程的时间线各不相同，取决于许多因素。 

### <a name="escalations"></a>升级

若要将处理升级，请向 [Azure AD SSO 集成团队](mailto:SaaSApplicationIntegrations@service.microsoft.com)发送电子邮件，我们会尽快予以回复。


## <a name="step-7---join-the-microsoft-partner-network"></a>步骤 7 - 加入 Microsoft 合作伙伴网络
在 Microsoft 合作伙伴网络中可以即时访问专用的资源、计划、工具和连接。 若要加入该网络并创建面市计划，请参阅[与商业客户取得联系](https://partner.microsoft.com/explore/commercial#gtm)。

## <a name="request-apps-by-sharing-isv-app-team-contact"></a>通过共享 ISV 应用团队联系人请求应用

客户可以通过在[此处](https://microsoft.sharepoint.com/teams/apponboarding/Apps/SitePages/AppRequestsByCustomers.aspx)共享应用程序和 ISV 联系人信息来请求应用程序。

![显示客户请求的应用磁贴](./media/howto-app-gallery-listing/customer-submit-request.png)

下面是客户请求的应用程序的流。

![显示客户请求的应用流](./media/howto-app-gallery-listing/customer-request-2.png)

> [!Note]
> 如果[访问时遇到任何问题](#issues-on-logging-into-portal)，请发送电子邮件给 [Azure AD 应用集成团队](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)。

## <a name="next-steps"></a>后续步骤

* [生成 SCIM 终结点并配置预配](../app-provisioning/use-scim-to-provision-users-and-groups.md)
* [Azure AD 的身份验证方案](authentication-flows-app-scenarios.md)