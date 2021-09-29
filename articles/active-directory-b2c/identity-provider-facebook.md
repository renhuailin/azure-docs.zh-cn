---
title: 设置使用 Facebook 帐户注册和登录
titleSuffix: Azure AD B2C
description: 使用 Azure Active Directory B2C，为应用程序中的客户提供通过 Facebook 帐户注册与登录的功能。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/16/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 6d7180b92ba4f4dbcc23f19bdc2581072446a15f
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128574912"
---
# <a name="set-up-sign-up-and-sign-in-with-a-facebook-account-using-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 设置通过 Facebook 帐户注册与登录

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>先决条件

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-a-facebook-application"></a>创建 Facebook 应用程序

若要在 Azure Active Directory B2C (Azure AD B2C) 中为用户启用使用 Facebook 帐户登录的功能，需要在 [Facebook 应用仪表板](https://developers.facebook.com/)中创建应用程序。 有关详细信息，请参阅[应用开发](https://developers.facebook.com/docs/development)。

如果还没有 Facebook 帐户，可通过 [https://www.facebook.com](https://www.facebook.com) 注册。 注册或登录 Facebook 帐户后，启动 [Facebook 开发人员帐户注册过程](https://developers.facebook.com/async/registration)。 有关详细信息，请参阅[注册为 Facebook 开发人员](https://developers.facebook.com/docs/development/register)。

1. 使用 Facebook 开发人员帐户凭据登录[面向开发人员的 Facebook](https://developers.facebook.com/apps)。
1. 选择“创建应用”。
1. 在“选择应用类型”部分，选择“使用者”，然后选择“继续”  。
1. 输入“应用显示名称”以及有效的“应用联系人电子邮件” 。
1. 选择“创建应用”。 此步骤要求你接受 Facebook 平台政策并完成在线安全检查。
1. 选择“设置” > “基本”。
    1. 复制“应用程序 ID”的值。
    1. 选择“显示”，然后复制“应用密码”的值。 使用这两个值将 Facebook 配置为租户中的标识提供者。 “应用程序密码”是一个非常重要的安全凭据。
    1. 为“隐私策略 URL”输入一个 URL，例如 `https://www.contoso.com/privacy`。 策略 URL 是继续提供应用程序的隐私信息的页面。
    1. 为“服务条款 URL”输入一个 URL，例如 `https://www.contoso.com/tos`。 策略 URL 是你为应用程序继续提供条款和条件的页面。
    1. 输入用户数据删除 URL，例如 `https://www.contoso.com/delete_my_data`。 用户数据删除 URL 是为了让用户能够请求删除其数据而保留的页面。 
    1. 选择“类别”，例如 `Business and Pages`。 此值是 Facebook 必需的，但不用于 Azure AD B2C。
1. 在页面底部，选择“添加平台”，然后选择“网站”。
1. 在“网站 URL”中，输入网站的地址，例如 `https://contoso.com`。 
1. 选择“保存更改”。
1. 从菜单中选择“产品”旁边的加号 。 在“将产品添加到应用”下，选择“Facebook 登录”下的“设置”  。
1. 从菜单中选择“Facebook 登录”，然后选择“设置” 。
1. 在“有效的 OAuth 重定向 URL”中输入 `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`。 如果使用[自定义域](custom-domain.md)，请输入 `https://your-domain-name/your-tenant-name.onmicrosoft.com/oauth2/authresp`。 将 `your-tenant-name` 替换为租户的名称，将 `your-domain-name` 替换为你的自定义域。 
1. 选择页面底部的“保存更改”。
1. 若要让 Facebook 应用程序可用于 Azure AD B2C，请选择页面右上角的“状态”选择器，将它设置为“开”，使应用程序公开，然后选择“切换模式”。 此时，“状态”应从“开发”变为“实时”。 有关详细信息，请参阅 [Facebook 应用开发](https://developers.facebook.com/docs/development/release)。

::: zone pivot="b2c-user-flow"

## <a name="configure-facebook-as-an-identity-provider"></a>将 Facebook 配置为标识提供程序

1. 以 Azure AD B2C 租户的全局管理员身份登录 [Azure 门户](https://portal.azure.com/)。
1. 请确保使用的是包含 Azure AD B2C 租户的目录。 在门户工具栏中选择“目录 + 订阅”图标。
1. 在“门户设置 | 目录+订阅”页上的“目录名称”列表中找到你的 Azure AD B2C 目录，然后选择“切换”。
1. 选择 Azure 门户左上角的“所有服务”，搜索并选择 **Azure AD B2C**。
1. 依次选择“标识提供程序”和“Facebook” 。
1. 输入“名称”。 例如 Facebook。
1. 对于“客户端 ID”，输入之前创建的 Facebook 应用程序的“应用 ID”。
1. 对于“客户端密码”，输入已记录的“应用机密”。
1. 选择“保存”。

## <a name="add-facebook-identity-provider-to-a-user-flow"></a>将 Facebook 标识提供程序添加到用户流 

此时，Facebook 标识提供者已设置，但还不能在任何登录页中使用。 若要将 Facebook 标识提供者添加到用户流，请执行以下操作：

1. 在 Azure AD B2C 租户中，选择“用户流”  。
1. 单击要添加 Facebook 标识提供程序的用户流。
1. 在“社交标识提供程序”下，选择“Facebook”。
1. 选择“保存”。
1. 若要测试策略，请选择“运行用户流”。
1. 对于“应用程序”，请选择前面已注册的名为“testapp1”的 Web 应用程序。 “回复 URL”应显示为 `https://jwt.ms`。
1. 选择“运行用户流”按钮。
1. 在注册或登录页上，选择“Facebook”以通过 Facebook 帐户登录。

如果登录过程成功，浏览器将重定向到 `https://jwt.ms`，后者显示 Azure AD B2C 返回的令牌内容。


::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-policy-key"></a>创建策略密钥

你需要存储前面在 Azure AD B2C 租户中记录的应用密码。

1. 登录 [Azure 门户](https://portal.azure.com/)。
1. 请确保使用的是包含 Azure AD B2C 租户的目录。 在门户工具栏中选择“目录 + 订阅”图标。
1. 在“门户设置 | 目录+订阅”页上的“目录名称”列表中找到你的 Azure AD B2C 目录，然后选择“切换”。
1. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“Azure AD B2C” 。
1. 在“概述”页上选择“标识体验框架”。
1. 选择“策略密钥”，然后选择“添加”。
1. 对于“选项”，请选择 `Manual`。
1. 输入策略密钥的 **名称**。 例如，`FacebookSecret`。 前缀 `B2C_1A_` 会自动添加到密钥名称。
1. 在“密码”中，输入前面记录的应用密码。
1. 在“密钥用法”处选择 `Signature`。
1. 单击“创建”。

## <a name="configure-a-facebook-account-as-an-identity-provider"></a>将 Facebook 帐户配置为标识提供者

1. 在 `SocialAndLocalAccounts/``TrustFrameworkExtensions.xml` 文件中，将 `client_id` 的值替换为 Facebook 应用程序 ID：

   ```xml
   <TechnicalProfile Id="Facebook-OAUTH">
     <Metadata>
     <!--Replace the value of client_id in this technical profile with the Facebook app ID"-->
       <Item Key="client_id">00000000000000</Item>
   ```

## <a name="upload-and-test-the-policy"></a>上传并测试策略

更新用于启动创建的用户旅程的信赖方 (RP) 文件。

1. 将 TrustFrameworkExtensions.xml 文件上传到租户。
1. 在“自定义策略”下，选择“B2C_1A_signup_signin” 。
1. 对于“选择应用程序”，选择前面已注册的名为“testapp1”的 Web 应用程序。 “回复 URL”应显示为 `https://jwt.ms`。
1. 选择“立即运行”按钮。
1. 在注册或登录页上，选择“Facebook”以通过 Facebook 帐户登录。

如果登录过程成功，浏览器将重定向到 `https://jwt.ms`，后者显示 Azure AD B2C 返回的令牌内容。

::: zone-end

## <a name="next-steps"></a>后续步骤

了解如何[将 Facebook 令牌传递给你的应用程序](idp-pass-through-user-flow.md)。
