---
title: 使用 Azure Active Directory B2C 设置直接登录 | Microsoft Docs
description: 了解如何预填充登录名或直接重定向到社交标识提供者。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/14/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: b2342ed978204284bee4d2be0f1c983aa10ade36
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/16/2020
ms.locfileid: "97585031"
---
# <a name="set-up-direct-sign-in-using-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 设置直接登录

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

使用 Azure Active Directory (AD) B2C 为应用程序设置登录时，可以预填充登录名或直接登录到特定的社交标识提供者，例如 Facebook、LinkedIn 或 Microsoft 帐户。

## <a name="prepopulate-the-sign-in-name"></a>预填充登录名

在登录用户旅程中，信赖方应用程序可以针对特定用户或域名。 当针对用户时，应用程序可以在授权请求中使用用户登录名指定 `login_hint` 查询参数。 Azure AD B2C 自动填充登录名，而用户只需提供密码。

![URL 中突出显示了 login_hint 查询参数的注册登录页](./media/direct-signin/login-hint.png)

用户可以更改登录文本框中的值。

::: zone pivot="b2c-custom-policy"

若要支持登录提示参数，请替代 `SelfAsserted-LocalAccountSignin-Email` 技术配置文件。 在 `<InputClaims>` 节中，将 signInName 声明的 DefaultValue 设置为 `{OIDC:LoginHint}`。 `{OIDC:LoginHint}` 变量包含 `login_hint` 参数的值。 Azure AD B2C 将读取 signInName 声明的值并预填充 signInName 文本框。

```xml
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
      <InputClaims>
        <!-- Add the login hint value to the sign-in names claim type -->
        <InputClaim ClaimTypeReferenceId="signInName" DefaultValue="{OIDC:LoginHint}" />
      </InputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

::: zone-end

## <a name="redirect-sign-in-to-a-social-provider"></a>将登录重定向到社交提供者

如果已将应用程序的登录旅程配置为包括社交帐户（如 Facebook、LinkedIn 或 Google），则可以指定 `domain_hint` 参数。 此查询参数向 Azure AD B2C 提供有关应该用于登录的社交标识提供者的提示。 例如，如果应用程序指定 `domain_hint=facebook.com`，则登录会直接转到 Facebook 登录页。

![在 URL 中突出显示 domain_hint 查询参数时注册登录页](./media/direct-signin/domain-hint.png)

::: zone pivot="b2c-user-flow"

域提示查询字符串参数可以设置为以下域之一：

- amazon.com
- facebook.com
- github.com
- google.com
- linkedin.com
- microsoft.com
- qq.com
- twitter.com
- wechat.com
- weibo.com 
- 有关 [通用 OpenID connect](identity-provider-generic-openid-connect.md)，请参阅 [域提示](identity-provider-generic-openid-connect.md#response-mode)。

::: zone-end

::: zone pivot="b2c-custom-policy"

若要支持 domain hing 参数，可以使用 any 的 XML 元素来配置域名 `<Domain>domain name</Domain>` `<ClaimsProvider>` 。

```xml
<ClaimsProvider>
    <!-- Add the domain hint value to the claims provider -->
    <Domain>facebook.com</Domain>
    <DisplayName>Facebook</DisplayName>
    <TechnicalProfiles>
    ...
```

::: zone-end

