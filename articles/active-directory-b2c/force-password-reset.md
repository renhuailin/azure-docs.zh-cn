---
title: 在 Azure AD B2C 中配置强制密码重置流
titleSuffix: Azure AD B2C
description: 了解如何在 Azure Active Directory B2C 中设置强制密码重置流。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/16/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: bff08cb924e33090b80f44dd908e2b9964290d5c
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128572396"
---
# <a name="set-up-a-force-password-reset-flow-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中设置强制密码重置流

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

## <a name="overview"></a>概述

如果用户忘记了密码，作为管理员，你可以[重置用户的密码](manage-users-portal.md#reset-a-users-password)。 你也可以强制他们重置密码。 本文介绍如何在这些情况下强制密码重置。

在管理员通过 Azure 门户重置用户的密码时，[forceChangePasswordNextSignIn](user-profile-attributes.md#password-profile-property) 属性的值会被设置为 `true`。 [登录和注册旅程](add-sign-up-and-sign-in-policy.md)会检查此属性的值。 在用户完成登录后，如果该属性设置为 `true`，则用户必须重置其密码。 然后，该属性的值会被设置回 `false`。

![强制密码重置流](./media/force-password-reset/force-password-reset-flow.png)

密码重置流适用于 Azure AD B2C 中使用[电子邮件地址](sign-in-options.md#email-sign-in)或[用户名](sign-in-options.md#username-sign-in)和密码进行登录的本地帐户。

::: zone pivot="b2c-user-flow"

## <a name="prerequisites"></a>先决条件

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="configure-your-user-flow"></a>配置用户流

若要在注册或登录用户流中启用“强制密码重置”设置，请执行以下操作：

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 请确保使用的是包含 Azure AD B2C 租户的目录。 在门户工具栏中选择“目录 + 订阅”图标。
1. 在“门户设置 | 目录+订阅”页上的“目录名称”列表中找到你的 Azure AD B2C 目录，然后选择“切换”。
1. 在 Azure 门户中，搜索并选择“Azure AD B2C”  。
1. 选择“用户流”。
1. 选择要自定义的“注册和登录”或“登录”用户流（“推荐”类型）。
1. 在左侧菜单中的“设置”下，选择“属性” 。
1. 在“密码配置”下，选择“强制密码重置” 。
1. 选择“保存”。

## <a name="test-the-user-flow"></a>测试用户流

1. 以用户管理员或密码管理员身份登录到 [Azure 门户](https://portal.azure.com)。 有关可用角色的详细信息，请参阅[在 Azure Active Directory 中分配管理员角色](../active-directory/roles/permissions-reference.md#all-roles)。
1. 请确保使用的是包含 Azure AD B2C 租户的目录。 在门户工具栏中选择“目录 + 订阅”图标。
1. 在“门户设置 | 目录+订阅”页上的“目录名称”列表中找到你的 Azure AD B2C 目录，然后选择“切换”。
1. 在 Azure 门户中，搜索并选择“Azure AD B2C”  。
1. 选择“用户”。 搜索并选择要用于测试密码重置的用户，然后选择“重置密码”。
1. 在 Azure 门户中，搜索并选择“Azure AD B2C”  。
1. 选择“用户流”。
1. 选择想要测试的注册或登录用户流（“推荐”类型）。
1. 选择“运行用户流”。
1. 对于“应用程序”，请选择前面已注册的名为 *webapp1* 的 Web 应用程序。 “回复 URL”应显示为 `https://jwt.ms`。
1. 选择“运行用户流”。
1. 使用为其重置密码的用户帐户登录。
1. 现在必须更改该用户的密码。 更改密码，然后选择“继续”。 令牌将返回到 `https://jwt.ms` 并显示出来。

## <a name="force-password-reset-on-next-login"></a>下次登录时强制密码重置

若要在下次登录时强制重置密码，请通过 MS Graph [更新用户](/graph/api/user-update)操作更新帐户密码配置文件。 以下示例将密码配置文件 [forceChangePasswordNextSignIn](user-profile-attributes.md#password-profile-property) 属性更新为 `true`，这将强制用户在下次登录时重置密码。

```http
PATCH https://graph.microsoft.com/v1.0/users/<user-object-ID>
Content-type: application/json

{
"passwordProfile": {
  "forceChangePasswordNextSignIn": true
}
```

在设置了帐户密码配置文件后，还必须按本文中的说明配置强制密码重置流。

## <a name="force-a-password-reset-after-90-days"></a>强制在 90 天后进行密码重置

作为管理员，你可以使用 [MS Graph](microsoft-graph-operations.md) 将用户的密码过期时间设置为 90 天。 在 90 天后，[forceChangePasswordNextSignIn](user-profile-attributes.md#password-profile-property) 属性的值会自动设置为 `true`。 若要在 90 天后强制密码重置，请从用户的配置文件[密码策略](user-profile-attributes.md#password-policy-attribute)属性中删除 `DisablePasswordExpiration` 值。

以下示例将密码策略更新为 `None`，这将在 90 天后强制密码重置：

```http
PATCH https://graph.microsoft.com/v1.0/users/<user-object-ID>
Content-type: application/json

{
  "passwordPolicies": "None"
}
```

如果禁用了强[密码复杂性](password-complexity.md)，则将密码策略更新为 [DisableStrongPassword](user-profile-attributes.md#password-policy-attribute)：

```http
PATCH https://graph.microsoft.com/v1.0/users/<user-object-ID>
Content-type: application/json

{
  "passwordPolicies": "DisableStrongPassword"
}
```

在设置了密码过期策略后，还必须按本文中的说明配置强制密码重置流。

### <a name="password-expiry-duration"></a>密码过期期限

密码过期期限默认值为 90 天。 可通过使用 Windows PowerShell 的 Azure Active Directory 模块中的 [Set-MsolPasswordPolicy](/powershell/module/msonline/set-msolpasswordpolicy) cmdlet 来配置该值。 此命令更新租户，使所有用户的密码在配置的天数后过期。

::: zone-end

::: zone pivot="b2c-custom-policy"

此功能目前仅适用于用户流。 对于设置步骤，请选择上面的“用户流”。 对于自定义策略，请使用强制密码重置首次登录 [GitHub 示例](https://github.com/azure-ad-b2c/samples/tree/master/policies/force-password-reset-first-logon)。

::: zone-end

## <a name="next-steps"></a>后续步骤

设置[自助式密码重置](add-password-reset-policy.md)。
