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
ms.date: 03/03/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 0477153b466702bec0fa2d5221fee1e054d06314
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "102033754"
---
# <a name="set-up-a-force-password-reset-flow-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中设置强制密码重置流

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

如果用户忘记了密码，作为管理员，你可以[重置用户的密码](manage-users-portal.md#reset-a-users-password)。 你也可以强制他们重置密码。 本文介绍如何在这些情况下强制密码重置。

## <a name="overview"></a>概述

在管理员通过 Azure 门户重置用户的密码时，[forceChangePasswordNextSignIn](user-profile-attributes.md#password-profile-property) 属性的值会被设置为 `true`。

[登录和注册旅程](add-sign-up-and-sign-in-policy.md)会检查此属性的值。 在用户完成登录后，如果该属性设置为 `true`，则用户必须重置其密码。 然后，该属性的值会被设置回 `false`。

![强制密码重置流](./media/force-password-reset/force-password-reset-flow.png)

密码重置流适用于 Azure AD B2C 中使用[电子邮件地址](identity-provider-local.md#email-sign-in)或[用户名](identity-provider-local.md#username-sign-in)和密码进行登录的本地帐户。

### <a name="force-a-password-reset-after-90-days"></a>强制在 90 天后进行密码重置

作为管理员，你可以使用 [MS Graph](microsoft-graph-operations.md) 将用户的密码过期时间设置为 90 天。 在 90 天后，[forceChangePasswordNextSignIn](user-profile-attributes.md#password-profile-property) 属性的值会自动设置为 `true`。 若要详细了解如何设置用户的密码过期策略，请参阅[密码策略属性](user-profile-attributes.md#password-policy-attribute)。

在设置了密码过期策略后，还必须按本文中的说明配置强制密码重置流。  

## <a name="prerequisites"></a>先决条件

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="configure-your-policy"></a>配置策略

::: zone pivot="b2c-user-flow"

若要在注册或登录用户流中启用“强制密码重置”设置，请执行以下操作：

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 在门户工具栏中选择“目录 + 订阅”图标，然后选择包含 Azure AD B2C 租户的目录。
1. 在 Azure 门户中，搜索并选择“Azure AD B2C”  。
1. 选择“用户流”。
1. 选择要自定义的“注册和登录”或“登录”用户流（“推荐”类型）。
1. 在左侧菜单中的“设置”下，选择“属性” 。
1. 在“密码复杂性”下，选择“强制密码重置” 。
1. 选择“保存”。

### <a name="test-the-user-flow"></a>测试用户流

1. 以用户管理员或密码管理员身份登录到 [Azure 门户](https://portal.azure.com)。 有关可用角色的详细信息，请参阅[在 Azure Active Directory 中分配管理员角色](../active-directory/roles/permissions-reference.md#all-roles)。
1. 在门户工具栏中选择“目录 + 订阅”图标，然后选择包含 Azure AD B2C 租户的目录。
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

::: zone-end

::: zone pivot="b2c-custom-policy"

1. 在 [GitHub](https://github.com/azure-ad-b2c/samples/tree/master/policies/force-password-reset) 上获取强制密码重置的示例。
1. 在每个文件中，将字符串 `yourtenant` 替换为 Azure AD B2C 租户的名称。 例如，如果 B2C 租户的名称为 contosob2c，则 `yourtenant.onmicrosoft.com` 的所有实例都将变为 `contosob2c.onmicrosoft.com`。
1. 按以下顺序上传策略文件：先是扩展策略 `TrustFrameworkExtensionsCustomForcePasswordReset.xml`，然后是信赖方策略 `SignUpOrSigninCustomForcePasswordReset.xml`。

### <a name="test-the-policy"></a>测试策略

1. 以用户管理员或密码管理员身份登录到 [Azure 门户](https://portal.azure.com)。 有关可用角色的详细信息，请参阅[在 Azure Active Directory 中分配管理员角色](../active-directory/roles/permissions-reference.md#all-roles)。
1. 在门户工具栏中选择“目录 + 订阅”图标，然后选择包含 Azure AD B2C 租户的目录。
1. 在 Azure 门户中，搜索并选择“Azure AD B2C”  。
1. 选择“用户”。 搜索并选择要用于测试密码重置的用户，然后选择“重置密码”。
1. 在 Azure 门户中，搜索并选择“Azure AD B2C”  。
1. 在“策略”下，选择“Identity Experience Framework”。 
1. 选择 `B2C_1A_signup_signin_Custom_ForcePasswordReset` 策略以将其打开。 
1. 对于“应用程序”，选择你[之前注册](troubleshoot-custom-policies.md#troubleshoot-the-runtime)的 Web 应用程序。 “回复 URL”应显示为 `https://jwt.ms`。
1. 选择“立即运行”按钮。
1. 使用为其重置密码的用户帐户登录。
1. 现在必须更改该用户的密码。 更改密码，然后选择“继续”。 令牌将返回到 `https://jwt.ms` 并显示出来。

::: zone-end

## <a name="next-steps"></a>后续步骤

设置[自助式密码重置](add-password-reset-policy.md)。
