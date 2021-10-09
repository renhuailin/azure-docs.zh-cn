---
title: 主领域发现策略
titleSuffix: Azure AD
description: 了解如何使用主领域发现策略控制应用程序的自动加速。
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 08/20/2021
ms.author: davidmu
ms.reviewer: hirsin
ms.openlocfilehash: ce71512e46f1b78ece832ec1fb1bad938edfcbf8
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/26/2021
ms.locfileid: "129058835"
---
# <a name="home-realm-discovery-for-an-application-in-azure-active-directory"></a>Azure Active Directory 中的应用程序的主领域发现

主领域发现 (HRD) 是一个过程，便于 Azure AD 确定用户在登录时需要使用哪个标识提供者（“IdP”）进行身份验证。  登录 Azure AD 租户访问资源或 Azure AD 公共登录页时，用户需键入用户名 (UPN)。 Azure AD 以此来发现用户需要在何处登录。

用户将被转到以下标识提供程序之一进行身份验证：

- 用户的主租户（可能是与用户尝试访问的资源相同的租户）。

- Microsoft 帐户。  用户是资源租户中的来宾，使用使用者帐户进行身份验证。

- 本地标识提供者，例如 Active Directory 联合身份验证服务 (AD FS)。

- 与 Azure AD 租户联合的其他标识提供者。

## <a name="auto-acceleration"></a>自动加速

某些组织在其 Azure AD 租户中配置域，以便与其他 IdP（例如 AD FS）联合进行用户身份验证。

当用户登录到应用程序时，首先会看到 Azure AD 登录页。 键入 UPN 后，如果用户位于联合域中，则会转到为该域提供服务的 IdP 的登录页。 某些情况下，当用户尝试登录特定应用程序时，管理员可能希望将其定向到登录页。

因此，用户可以跳过 Azure Active Directory 初始页。 这个过程称为“登录自动加速”。

在租户被联合到另一个 IdP 以进行登录的情况下，自动加速可进一步简化用户登录。  可以为单个应用程序配置自动加速。

> [!NOTE]
> 如果你为应用程序配置自动加速，那么用户就不能使用托管凭据（如 FIDO），来宾用户也不能登录。 如果将用户直接转到联合 IdP 进行身份验证，他们将无法返回到 Azure Active Directory 登录页。 来宾用户可能需要进入其他租户或外部 IdP（如 Microsoft 帐户），而不能登录该应用程序，因为他们会跳过主领域发现步骤。

有三种方法可以控制自动加速到联合 IdP：

- 在应用程序的身份验证请求中使用域提示。
- 将主领域发现策略配置为[强制自动加速]()(configure-authentication-for-federated-users-portal.md)
- 将主页领域发现策略配置为[忽略来自特定应用程序或特定域的域提示](prevent-domain-hints-with-home-realm-discovery.md)。

## <a name="domain-hints"></a>域提示

域提示是应用程序的身份验证请求中包含的指令。 它们可用于加速将用户转到其联合的 IdP 登录页。 或者可由多租户应用程序用来帮助用户更快地直接转到其租户的品牌 Azure AD 登录页。

例如，应用程序“largeapp.com”可使其客户通过 URL“contoso.largeapp.com”访问应用程序。 该应用可能在身份验证请求中包含了 Contoso.com 的域提示。

域提示语法因所用协议而异，通常按以下方式在应用程序中配置：

- 对于使用 WS-Federation 的应用程序：在查询字符串中使用 whr=contoso.com。

- 对于使用 SAML 的应用程序：使用包含域提示的 SAML 身份验证请求，或使用查询字符串 whr=contoso.com。

- 对于使用 Open ID Connect 的应用程序：使用查询字符串 domain_hint=contoso.com。

默认情况下，如果满足以下两个条件，则 Azure AD 会尝试将登录重定向到为域配置的 IdP：

- 域提示包含在应用程序的身份验证请求中；且
- 租户与相应域联合。

如果域提示不引用已验证的联合域，则忽略它。

> [!NOTE]
> 如果域提示包含在身份验证请求中并且[应生效](#home-realm-discovery-policy-to-prevent-auto-acceleration)，那么它会替代在 HRD 策略中为应用程序设置的自动加速。

### <a name="home-realm-discovery-policy-for-auto-acceleration"></a>自动加速的主领域发现策略

某些应用程序不提供配置其发出的身份验证请求的方法。 在此情况下，域提示不能用来控制自动加速。 自动加速可以[通过主领域发现策略进行配置](configure-authentication-for-federated-users-portal.md)，以实现相同的行为。

### <a name="home-realm-discovery-policy-to-prevent-auto-acceleration"></a>用于阻止自动加速的主页领域发现策略

一些 Microsoft 和 SaaS 应用程序自动包含 domain_hint（例如，`https://outlook.com/contoso.com` 会生成追加了 `&domain_hint=contoso.com` 的登录请求），这可能会中断 FIDO 等托管凭据的推出。  在推出托管凭据期间，可以使用[主领域发现策略](prevent-domain-hints-with-home-realm-discovery.md)来忽略来自特定应用或特定域的域提示。

## <a name="enable-direct-ropc-authentication-of-federated-users-for-legacy-applications"></a>为旧的应用程序启用对联合用户进行直接 ROPC 身份验证

最佳做法是让应用程序使用 Azure AD 库和交互式登录对用户进行身份验证。 库会处理联合用户流。  有时，旧的应用程序（尤其是使用资源所有者密码凭据 (ROPC) 授权的应用程序）会直接将用户名和密码提交到 Azure AD，它们无法识别联合。 它们不会执行 HRD，并且不会通过与正确的联合终结点交互来对用户进行身份验证。 如果选择[使用主领域发现策略来启用特定的旧版应用程序](configure-authentication-for-federated-users-portal.md)，使之使用 ROPC 授权来提交用户名/密码凭据，以便直接向 Azure Active Directory 进行身份验证，则必须启用密码哈希同步。

> [!IMPORTANT]
> 仅当已启用密码哈希同步，并且知道可对此应用程序进行身份验证，且本地 IdP 未实施任何策略时，才启用直接身份验证。 如果禁用密码哈希同步，或出于任何原因禁用使用 AD Connect 的目录同步，则应删除此策略，以防止使用过期的密码哈希进行直接身份验证。

## <a name="set-hrd-policy"></a>设置 HRD 策略

需要执行三个步骤在应用程序中设置 HRD 策略，以启用联合登录自动加速或基于云的直接应用程序：

1. 创建 HRD 策略

2. 找到要将策略附加到的服务主体。

3. 将策略附加到服务主体。

只有在将策略附加到服务主体后，策略才会对特定的应用程序生效。

服务主体中每次只有一个 HRD 策略处于活动状态。

可以使用 Azure Active Directory PowerShell cmdlet 来创建和管理 HRD 策略。

以下是 HRD 策略定义示例：

```json
{  
  "HomeRealmDiscoveryPolicy":
  {  
    "AccelerateToFederatedDomain":true,
    "PreferredDomain":"federated.example.edu",
    "AllowCloudPasswordValidation":false,    
  }
}
```

策略类型为“[HomeRealmDiscoveryPolicy](/graph/api/resources/homeRealmDiscoveryPolicy)”。

**AccelerateToFederatedDomain** 是可选的。 如果 **AccelerateToFederatedDomain** 为 false，则策略对自动加速不起作用。 如果 **AccelerateToFederatedDomain** 为 true 并且租户中只有一个已验证的联合域，则用户将直接转到联合 IdP 进行登录。 如果此属性为 true 并且租户中有多个已验证的域，则必须指定 **PreferredDomain**。

**PreferredDomain** 是可选的。 “PreferredDomain”应指示需要加速的域。 如果租户仅有一个联合域，可省略此项。  如果在有多个已验证的联合域的情况下省略，则策略无效。

 如果指定“PreferredDomain”，必须与租户的某个已验证的联合域匹配。 应用程序的所有用户都必须能够登录此域 - 无法在联合域登录的用户将被困住，无法完成登录。

**AllowCloudPasswordValidation** 是可选的。 如果 **AllowCloudPasswordValidation** 为 true，则允许应用程序通过直接向 Azure Active Directory 令牌终结点提供用户名/密码凭据，来对联合用户进行身份验证。 仅当已启用密码哈希同步时，才可以做到这一点。

另外，还存在两个租户级别 HRD 选项（上面没有显示）：

- “AlternateIdLogin”是可选的。  如果启用，[用户可以在 Azure AD 登录页上使用电子邮件地址（而不是 UPN）登录](../authentication/howto-authentication-use-email-signin.md)。  备用 ID 依赖于不自动加速到联合 IDP 的用户。

- DomainHintPolicy 是可选的复杂对象，它[阻止域提示将用户自动加速到联合域](prevent-domain-hints-with-home-realm-discovery.md)。 此租户范围内的设置用于确保发送域提示的应用程序不会阻止用户使用云管理的凭据登录。

### <a name="priority-and-evaluation-of-hrd-policies"></a>HRD 策略的优先级和评估

可以创建 HRD 策略，并将其分配到特定组织和服务主体。 这意味着，可以将多个策略应用到一个特定的应用程序，因此 Azure AD 必须决定哪个策略优先。 一组规则决定（应用的许多策略中的）哪个 HRD 策略生效：

- 如果身份验证请求中有域提示，则会检查租户的 HRD 策略（策略被设置为租户默认值），以确定是否应忽略域提示。 如果允许域提示，则使用域提示指定的行为。

- 或者，如果向服务主体显式分配了某个策略，则强制实施该策略。

- 如果没有域提示，也未向服务主体显式分配策略，则强制实施向服务主体的父组织显式分配的策略。

- 如果没有域提示，也未向服务主体或组织显式分配策略，则使用默认 HRD 行为。

## <a name="next-steps"></a>后续步骤

- [使用主领域发现策略为应用程序配置登录行为](configure-authentication-for-federated-users-portal.md)
- [在用户使用主领域发现策略登录过程中禁用到联合 IDP 的自动加速](prevent-domain-hints-with-home-realm-discovery.md)
- 有关 Azure AD 中的身份验证工作原理的详细信息，请参阅 [Azure AD 的身份验证方案](../develop/authentication-vs-authorization.md)。
