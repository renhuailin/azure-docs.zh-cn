---
title: 技术和功能概述 - Azure Active Directory B2C
description: Azure Active Directory B2C 中的功能和技术的深入介绍。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 06/07/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 7fb3dcb07b8b868dda7bfef587b930765c4f661e
ms.sourcegitcommit: e1874bb73cb669ce1e5203ec0a3777024c23a486
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/16/2021
ms.locfileid: "112199231"
---
# <a name="technical-and-feature-overview-of-azure-active-directory-b2c"></a>Azure Active Directory B2C 的技术和功能概述

本文是[关于 Azure Active Directory B2C](overview.md) 一文的配套文档，其中更深入地介绍了该服务。 这里讨论了你在服务中使用的主要资源及其功能。 了解这些功能如何使你能够在应用程序中为客户提供完全定制的身份体验。

## <a name="azure-ad-b2c-tenant"></a>Azure AD B2C 租户

在 Azure Active Directory B2C (Azure AD B2C) 中，租户表示组织，也是用户的目录。 每个 Azure AD B2C 租户都是独特的，独立于其他 Azure AD B2C 租户。 Azure AD B2C 租户不同于你可能已有的 Azure Active Directory 租户。

在 Azure AD B2C 租户中使用的主要资源包括：

* **目录** - Azure AD B2C 在目录中存储用户的凭据、个人资料数据和应用程序注册。
* 应用程序注册 - 将 Web、移动和本机应用程序注册到 Azure AD B2C 以启用标识管理。 还可以注册你想要使用 Azure AD B2C 保护的任何 API。
* 用户流和自定义策略 - 使用内置的用户流和完全可配置的自定义策略为应用程序创建标识体验 ：
  * 用户流可帮助快速启用注册、登录和配置文件编辑等常见标识任务。
  * 自定义策略可让你构建对于组织、客户、员工、合作伙伴和公民独有的复杂标识工作流。
* 登录选项 - Azure AD B2C 为应用程序的用户提供各种[注册和登录选项](sign-in-options.md)：
  * 用户名、电子邮件和电话登录 - 配置 Azure AD B2C 本地帐户以允许使用用户名、电子邮件地址、电话号码或多种方法的组合进行注册和登录。
  * 社交标识提供程序 - 与社交提供程序（如 Facebook、LinkedIn或 Twitter）联合。
  * 外部标识提供者 - 与 OAuth 2.0、OpenID Connect 等标准标识协议联合。
* **密钥** - 添加和管理用于签名和验证令牌、客户端密码、证书和密码的加密密钥。

Azure AD B2C 租户是开始使用 Azure AD B2C 之前必须先创建的第一个资源。 了解如何：

* [创建 Azure Active Directory B2C 租户](tutorial-create-tenant.md)。
* [准备 Azure AD B2C 租户](tenant-management.md)

## <a name="accounts-in-azure-ad-b2c"></a>Azure AD B2C 中的帐户

Azure AD B2C 定义多种类型的用户帐户。 Azure Active Directory、Azure Active Directory B2B 和 Azure Active Directory B2C 共享这些帐户类型。

* **工作帐户** - 具有工作帐户的用户可以管理租户中的资源，而具有管理员角色的用户还可以管理租户。 具有工作帐户的用户可以创建新的使用者帐户、重置密码、阻止/解除阻止帐户，以及设置权限或将帐户分配到安全组。
* **来宾帐户** - 邀请你作为来宾加入其租户的外部用户。 邀请来宾用户访问 Azure AD B2C 租户的典型方案是共享管理职责。
* **使用者帐户** - 通过 Azure AD B2C 用户流和自定义策略管理的帐户。

![Azure 门户中的 Azure AD B2C 用户管理页](media/technical-overview/portal-01-users.png)<br/>*图：Azure 门户中 Azure AD B2C 租户内的用户目录*

### <a name="consumer-accounts"></a>使用者帐户

用户可以通过使用者帐户登录到通过 Azure AD B2C 保护的应用程序。 但是，具有使用者帐户的用户无法访问 Azure 资源（例如 Azure 门户）。

可将使用者帐户关联到以下标识类型：

* **本地** 标识：将用户名和密码存储在 Azure AD B2C 目录本地。 我们通常将此类标识称为“本地帐户”。
* 社交或企业标识，其中用户的标识由联合标识提供者进行管理 。 例如 Facebook、Microsoft、ADFS 或 Salesforce。

具有使用者帐户的用户可使用多个标识进行登录。 例如用户名、电子邮件、员工 ID 和政府 ID 等。 单个帐户可以有多个本地和社交标识。

![使用者帐户标识](media/technical-overview/identities.png)<br/>*图：在 Azure AD B2C 中具有多个标识的单个使用者帐户*

有关详细信息，请查看 [Azure Active Directory B2C 中的用户帐户概述](user-overview.md)。

## <a name="local-account-sign-in-options"></a>本地帐户登录选项

Azure AD B2C 提供了用户可以用于对用户进行身份验证的各种方式。 用户可以使用用户名和密码、电话验证（也称为无密码身份验证）登录本地帐户。 在默认情况下，本地帐户标识提供者设置中会启用电子邮件注册。

详细了解[登录选项](sign-in-options.md)或如何[设置本地帐户标识提供程序](identity-provider-local.md)。

## <a name="user-profile-attributes"></a>用户配置文件属性

通过 Azure AD B2C，可管理使用者帐户个人资料的通用属性。 例如显示名称、姓氏、名字、城市等信息。

还可以扩展 Azure AD 架构以存储有关用户的其他信息。 例如，用户居住国家/地区、首选语言和偏好（比如是否想要订阅新闻稿或启用多重身份验证）。 有关详细信息，请参阅：

* [用户配置文件属性](user-profile-attributes.md)
* [添加用户属性并自定义用户输入](configure-user-input.md)

## <a name="sign-in-with-external-identity-providers"></a>用外部标识提供者登录

可以配置 Azure AD B2C，以允许用户使用社交和企业标识提供者提供的凭据登录到你的应用程序。 Azure AD B2C 可以与支持 OAuth 1.0、OAuth 2.0、OpenID Connect和 SAML 协议的标识提供者联合。 例如 Facebook、Microsoft 帐户、Google、Twitter 和 AD-FS。

![外部标识提供者](media/technical-overview/external-idps.png)

使用外部标识提供者联合，可让使用者通过其现有的社交帐户或企业帐户登录，而不必仅仅出于访问你的应用程序的目的创建一个新帐户。

在注册或登录页上，Azure AD B2C 会提供外部标识提供者的列表，供用户选择用来登录。 用户选择一个外部标识提供者后，将会转到（重定向到）所选提供者的网站，以完成登录过程。 用户成功登录后，将返回到 Azure AD B2C，以便对应用程序中的帐户进行身份验证。

![使用社交帐户 (Facebook) 进行移动登录的示例](media/technical-overview/external-idp.png)

若要了解如何在 Azure AD B2C 中添加标识提供者，请参阅[将标识提供者添加到 Azure Active Directory B2C 中的应用程序](add-identity-provider.md)。

## <a name="identity-experiences-user-flows-or-custom-policies"></a>标识体验：用户流或自定义策略

在 Azure AD B2C 中，你可以定义用户在获取应用程序访问权限时遵循的业务逻辑。 例如，可以确定用户在登录、注册、编辑个人资料或重置密码时遵循的步骤顺序。 按顺序完成步骤后，用户会获得一个令牌，并获得对应用程序的访问权限。

在 Azure AD B2C 中，有两种方式可以提供标识用户体验：

* **用户流** 是我们提供的预定义的内置可配置策略，使你能够在几分钟内创建注册、登录和策略编辑体验。

* 使用 **自定义策略** 可为复杂的标识体验方案创建自己的用户旅程。

以下屏幕截图显示用户流设置 UI，而不是自定义策略配置文件。

![屏幕截图显示用户流设置 UI，而不是自定义策略配置文件。](media/technical-overview/user-flow-vs-custom-policy.png)

阅读[用户流和自定义策略概述](user-flow-overview.md)一文。 这篇文章概述了用户流和自定义策略，并帮助你确定哪种方法最适合你的业务需求。

## <a name="user-interface"></a>用户界面

在 Azure AD B2C 中，可创建用户的标识体验，使显示的页面能够与品牌形象无缝融合。 当用户完成应用程序的标识旅程时，你几乎可以获得向用户显示的 HTML 和 CSS 内容的完全控制权。 凭借这种灵活性，可以在应用程序与 Azure AD B2C 之间保持品牌和视觉一致性。

![按品牌自定义的注册/登录页的屏幕截图](media/technical-overview/seamless-ux.png)

有关 UI 自定义项的信息，请参见：

* [自定义用户界面](customize-ui.md)
* [利用 HTML 模板自定义用户界面](customize-ui-with-html.md)
* [启用 JavaScript 并选择页面布局版本](javascript-and-page-layout.md)

## <a name="custom-domain"></a>自定义域

可以在 Azure AD B2C 的重定向 URL 中自定义 Azure AD B2C 域。 自定义域允许你创建无缝体验，使页面显示与应用程序的域名无缝结合。

![Azure AD B2C 自定义域的屏幕截图](media/technical-overview/custom-domain.png)

从用户角度来看，他们会在登录过程中保留在域中，而不是重定向到 Azure AD B2C 默认域 .b2clogin.com。 有关详细信息，请参阅[启用自定义域](custom-domain.md)。
 
## <a name="localization"></a>本地化

借助 Azure AD B2C 中的语言自定义可以适应不同的语言以满足客户需求。 Microsoft 提供 36 种语言的翻译，但你也可以为任何语言提供自己的翻译。 即使体验是针对一种语言提供的，也可以自定义页面上的任何文本。

![以不同语言显示 UI 文本的三个注册/登录页](media/technical-overview/localization.png)

在 [Azure Active Directory B2C 中的语言自定义](language-customization.md)中了解本地化的工作原理。

## <a name="email-verification"></a>电子邮件验证

Azure AD B2C 通过要求客户在注册过程中和密码重置流程中验证电子邮件地址来确保电子邮件地址有效。 它还可防止恶意执行组件使用自动进程在应用程序中生成欺诈帐户。

![Azure AD B2C 电子邮件验证的屏幕截图](media/technical-overview/email-verification.png)

可以为注册使用你的应用程序的用户自定义电子邮件。 通过使用第三方电子邮件提供商，你可以使用自己的电子邮件模板和“发件人：”地址和主题，并支持本地化和自定义一次性密码 (OTP) 设置。 有关详细信息，请参阅：

* [使用 Mailjet 进行自定义电子邮件验证](custom-email-mailjet.md)
* [使用 SendGrid 进行自定义电子邮件验证](custom-email-sendgrid.md)

## <a name="add-your-own-business-logic"></a>添加自己的业务逻辑

如果你选择使用自定义策略，可与用户旅程中的 RESTful API 相集成，以将自己的业务逻辑添加到旅程中。 例如，Azure AD B2C 可与 RESTful 服务交换数据，以便：

* 显示用户友好的自定义错误消息。
* 验证用户输入，以防止在用户目录中保存格式不当的数据。 例如，可以修改用户输入的数据（例如，将用户以全小写形式输入的名字大写）。
* 通过进一步与企业业务线应用程序集成来扩充用户数据。
* 使用 RESTful 调用可以发送推送通知、更新企业数据库、运行用户迁移过程、管理权限、审核数据库，以及执行其他操作。

会员计划是 Azure AD B2C 为了支持调用 REST API 而实现的另一种方案。 例如，RESTful 服务可以接收用户的电子邮件地址、查询客户数据库，然后向 Azure AD B2C 返回用户的会员号。 

返回数据可存储在用户在 Azure AD B2C 中的目录帐户中。 随后，可在后续步骤中在策略中进一步评估此数据，或者将其包含在访问令牌中。

![移动应用程序中的业务线集成](media/technical-overview/lob-integration.png)

可在自定义策略定义的用户旅程中的任意步骤中添加 REST API 调用。 例如，可在以下时机调用 REST API：

* 登录期间在 Azure AD B2C 验证凭据之前的那一刻
* 登录后立即调用
* Azure AD B2C 在目录中创建新帐户之前
* Azure AD B2C 在目录中创建新帐户之后
* Azure AD B2C 颁发访问令牌之前

若要了解如何对 Azure AD B2C 中的 RESTful API 集成使用自定义策略，请参阅[在 Azure AD B2C 自定义策略中集成 REST API 声明交换](api-connectors-overview.md)。

## <a name="protocols-and-tokens"></a>协议和令牌

- 对于应用程序，Azure AD B2C 支持对用户旅程使用 [OAuth 2.0](protocols-overview.md)、[OpenID Connect](openid-connect.md) 和 [SAML 协议](saml-service-provider.md)。 应用程序通过向 Azure AD B2C 发出身份验证请求，来启动此用户旅程。 向 Azure AD B2C 发出请求后会获得一个安全令牌，例如 [ID 令牌、访问令牌](tokens-overview.md)或 SAML 令牌。 此安全令牌定义应用程序中用户的标识。

- 对于外部标识，Azure AD B2C 支持与任何 OAuth 1.0、OAuth 2.0、OpenID Connect、SAML 标识提供者联合。

下图显示了 Azure AD B2C 如何使用同一身份验证流中的各种协议进行通信：

![基于 OIDC 的客户端应用与基于 SAML 的 IdP 联合的示意图](media/technical-overview/protocols.png)

1. 信赖方应用程序使用 OpenID Connect 向 Azure AD B2C 发起授权请求。
1. 当应用程序的用户选择通过使用 SAML 协议的外部标识提供者登录时，Azure AD B2C 将调用 SAML 协议来与该标识提供者通信。
1. 用户使用外部标识提供者完成登录操作后，Azure AD B2C 会使用 OpenID Connect 将令牌返回给信赖方应用程序。

## <a name="application-integration"></a>应用程序集成

当用户想要登录你的应用程序时，应用程序就会向用户流或自定义策略提供的终结点发起授权请求。 用户流或自定义策略定义并控制用户的体验。 当用户完成用户流（例如注册或登录流）后，Azure AD B2C 会生成一个令牌，然后将用户重定向回到应用程序。

![移动应用，其中的箭头显示 Azure AD B2C 登录页之间的流](media/technical-overview/app-integration.png)

多个应用程序可以使用同一个用户流或自定义策略。 单个应用程序可以使用多个用户流或自定义策略。

例如，若要登录到某个应用程序，该应用程序将使用注册或登录用户流。 用户登录后，他们可能想要编辑其配置文件，在这种情况下，应用程序将发起另一个授权请求（这一次使用的是配置文件编辑用户流）。

## <a name="multi-factor-authentication-mfa"></a>多重身份验证 (MFA)

Azure AD B2C 多重身份验证 (MFA) 有助于保护对数据和应用程序的访问，同时满足用户对简单性的需求。 它通过要求另一种形式的身份验证来提供额外的安全性，并通过提供一系列易于使用的身份验证方法来实现强式身份验证。 

根据管理员做出的配置决策，用户可能会受到 MFA 的质询，也可能不会受到 MFA 的质询。

有关如何在用户流中启用 MFA，请参阅[在 Azure Active Directory B2C 中启用多重身份验证](multi-factor-authentication.md)。

## <a name="conditional-access"></a>条件性访问

Azure AD 标识保护风险检测功能会自动检测风险用户和风险登录并将其显示在 Azure AD B2C 租户中。 你可以创建使用这些风险检测功能的条件访问策略来确定修正操作并强制实施组织策略。 

![条件访问流](media/technical-overview/conditional-access-flow.png)

Azure AD B2C 会评估每个登录事件，并确保在满足所有策略要求后才向用户授予访问权限。 有风险的用户或登录名可能被阻止，或者需要完成特定修正操作，如多重身份验证 (MFA)。 有关详细信息，请参阅[标识保护和条件访问](conditional-access-identity-protection-overview.md)。

## <a name="password-complexity"></a>密码复杂性

在注册或密码重置期间，用户必须提供符合复杂性规则的密码。 默认情况下，Azure AD B2C 实施强密码策略。 Azure AD B2C 还提供相应的配置选项用于指定客户所用密码的复杂性要求。

![密码复杂性用户体验的屏幕截图](media/technical-overview/password-complexity.png)

有关详细信息，请参阅[在 Azure AD B2C 中配置密码的复杂性要求](password-complexity.md)。

## <a name="force-password-reset"></a>强制执行密码重置

如果用户忘记了密码，作为 Azure AD B2C 租户管理员，你可以[重置用户的密码](manage-users-portal.md#reset-a-users-password)。 你也可以强制他们定期重置密码。 有关详细信息，请参阅[设置强制密码重置流程](force-password-reset.md)。

![强制密码重置流](media/technical-overview/force-password-reset-flow.png)

## <a name="smart-account-lockout"></a>智能帐户锁定

为了防止密码猜测暴力破解攻击，Azure AD B2C 可使用一种复杂的策略，根据请求的 IP、输入的密码和其他多个因素来锁定帐户。 锁定持续时间根据风险和尝试次数自动延长。

![帐户智能锁定](media/technical-overview/smart-lockout1.png)

有关管理密码保护设置的详细信息，请参阅[在 Azure AD B2C 中缓解凭据攻击](threat-management.md)。

## <a name="protect-resources-and-customer-identities"></a>保护资源和客户标识

Azure AD B2C 符合 [Microsoft Azure 信任中心](https://www.microsoft.com/trustcenter/cloudservices/azure)内所述的安全性、隐私性和其他承诺。

会话将通过只有 Azure AD B2C 安全令牌服务知道的解密密钥建模为加密数据。 使用强加密算法 AES-192。 为实现保密性和完整性，所有通信路径将通过 TLS 进行保护。 我们的安全令牌服务对 TLS 使用扩展验证 (EV) 证书。 一般而言，安全令牌服务不会呈现不受信任的输入，因此可以缓解跨站点脚本 (XSS) 攻击。

![保护传输中数据和静态数据的示意图](media/technical-overview/user-data.png)

### <a name="access-to-user-data"></a>访问用户数据

Azure AD B2C 租户与用于员工和合作伙伴的企业 Azure Active Directory 租户共享多个特征。 共享的方面包括用于查看管理角色、分配角色和审核活动的机制。

可以分配角色来控制谁能够在 Azure AD B2C 中执行特定的管理操作，包括：

* 创建和管理用户流的所有方面
* 创建和管理可用于所有用户流的属性架构
* 配置要在直接联合中使用的标识提供者
* 在 Identity Experience Framework 中创建和管理信任框架策略（自定义策略）
* 在 Identity Experience Framework 中管理用于联合身份验证和加密的机密（自定义策略）

有关 Azure AD 角色的详细信息，包括 Azure AD B2C 管理角色支持，请参阅 [Azure Active Directory 中的管理员角色权限](../active-directory/roles/permissions-reference.md)。

## <a name="auditing-and-logs"></a>审核和日志

Azure AD B2C 发出审核日志，其中包含有关其资源、颁发的令牌和管理员访问权限的活动信息。 可以使用审核日志来了解平台活动和诊断问题。 在生成事件的活动发生后，会立即提供审核日志条目。

在适用于 Azure AD B2C 租户或特定用户的审核日志中，可以找到以下信息：

* 涉及授权用户访问 B2C 资源（例如，管理员访问 B2C 策略列表）的活动
* 与管理员使用 Azure 门户登录时检索到的目录属性相关的活动
* 对 B2C 应用程序执行的创建、读取、更新和删除 (CRUD) 操作
* 对 B2C 密钥容器中存储的密钥执行的 CRUD 操作
* 与 B2C 资源（如策略和标识提供者）相关的 CRUD 操作
* 用户凭据和令牌颁发的验证

![Azure 门户中显示的单个用户审核日志](media/technical-overview/audit-log.png)

有关审核日志的详细信息，请查看[访问 Azure AD B2C 审核日志](view-audit-logs.md)。

## <a name="usage-analytics"></a>使用情况分析

在 Azure AD B2C 中，可发现用户何时注册或登录你的应用、用户身在何处，还有他们在使用哪些浏览器和操作系统。 

通过将 Azure Application Insights 集成到 Azure AD B2C 自定义策略，可深入了解用户如何注册、登录、重置其密码或编辑其个人资料。 了解此类信息后，可以针对未来的开发周期做出数据驱动的决策。

有关详细信息，请查看[使用 Application Insights 在 Azure Active Directory B2C 中跟踪用户行为](analytics-with-application-insights.md)。

## <a name="next-steps"></a>后续步骤

现在你已更深入地了解 Azure Active Directory B2C 的功能和技术方面，可以开始使用[创建 Azure Active Directory B2C 租户教程](tutorial-create-tenant.md)了。
