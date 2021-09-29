---
title: 向应用传递标识提供者访问令牌
titleSuffix: Azure AD B2C
description: 了解如何在 Azure Active Directory B2C 中以用户流中声明的方式传递 OAuth2.0 标识提供者的访问令牌。
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
ms.openlocfilehash: ae5f9409811e182bcc64dd9a6ea3ec12d46cfefd
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128574004"
---
# <a name="pass-an-identity-provider-access-token-to-your-application-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中，向应用程序传递标识提供者访问令牌

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Azure Active Directory B2C (Azure AD B2C) 中的[用户流](user-flow-overview.md)允许应用程序的用户通过标识提供者进行注册或登录。 此过程开始时，Azure AD B2C 会从标识提供者处收到一个[访问令牌](tokens-overview.md)。 Azure AD B2C 使用该令牌来检索有关用户的信息。 在用户流中启用声明即可将该令牌传递给你在 Azure AD B2C 中注册的应用程序。

::: zone pivot="b2c-user-flow"

Azure AD B2C 支持传递 [OAuth 2.0](add-identity-provider.md) 标识提供者（包括 [Facebook](identity-provider-facebook.md) 和 [Google](identity-provider-google.md)）的访问令牌。 对于所有其他标识提供者，声明将返回空白。

::: zone-end

::: zone pivot="b2c-custom-policy"

Azure AD B2C 支持传递 [OAuth 2.0](authorization-code-flow.md) 和 [OpenID Connect](openid-connect.md) 标识提供者的访问令牌。 对于所有其他标识提供者，声明将返回空白。

::: zone-end

下图显示了标识提供者令牌如何返回到你的应用： 

![标识提供者传递流程](./media/idp-pass-through-user-flow/identity-provider-pass-through-flow.png)

## <a name="prerequisites"></a>先决条件

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

::: zone pivot="b2c-user-flow"

## <a name="enable-the-claim"></a>启用声明

1. 以 Azure AD B2C 租户的全局管理员身份登录 [Azure 门户](https://portal.azure.com/)。
1. 请确保使用的是包含 Azure AD B2C 租户的目录。 在门户工具栏中选择“目录 + 订阅”图标。
1. 在“门户设置 | 目录+订阅”页上的“目录名称”列表中找到你的 Azure AD B2C 目录，然后选择“切换”。
1. 选择 Azure 门户左上角的“所有服务”  ，搜索并选择 **Azure AD B2C**。
1. 选择“用户流(策略)”  ，然后选择用户流。 例如 B2C_1_signupsignin1。 
1. 选择“应用程序声明”  。
1. 启用“标识提供者访问令牌”  声明。

    ![启用“标识提供者访问令牌”声明](./media/idp-pass-through-user-flow/identity-provider-pass-through-app-claim.png)

1. 单击“保存”，保存用户流  。

## <a name="test-the-user-flow"></a>测试用户流

在 Azure AD B2C 中测试应用程序时，可以使 Azure AD B2C 令牌返回到 `https://jwt.ms`，以便在其中查看声明，这可能很有用处。

1. 在用户流的“概览”页上，选择“运行用户流”  。
1. 对于“应用程序”  ，选择你之前注册的应用程序。 “回复 URL”  应当显示 `https://jwt.ms` 才能看到以下示例中的令牌。
1. 单击“运行用户流”，然后使用帐户凭据登录。  应该会在 **idp_access_token** 声明中看到标识提供者的访问令牌。

    应会看到类似于以下示例的内容：

    ![jwt.ms 中突出显示了 idp_access_token 块的已解码令牌](./media/idp-pass-through-user-flow/identity-provider-pass-through-token.png)

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="add-the-claim-elements"></a>添加声明元素

1. 打开 *TrustframeworkExtensions.xml* 文件，向 **ClaimsSchema** 元素中添加标识符为 `identityProviderAccessToken` 的以下 **ClaimType** 元素：

    ```xml
    <BuildingBlocks>
      <ClaimsSchema>
        <ClaimType Id="identityProviderAccessToken">
          <DisplayName>Identity Provider Access Token</DisplayName>
          <DataType>string</DataType>
          <AdminHelpText>Stores the access token of the identity provider.</AdminHelpText>
        </ClaimType>
        ...
      </ClaimsSchema>
    </BuildingBlocks>
    ```

1. 针对你需要其访问令牌的每个 OAuth 2.0 标识提供者，向 **TechnicalProfile** 元素中添加 **OutputClaim** 元素。 下面的示例显示了添加到 Facebook 技术配置文件的该元素：

    ```xml
    <ClaimsProvider>
      <DisplayName>Facebook</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Facebook-OAUTH">
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="identityProviderAccessToken" PartnerClaimType="{oauth2:access_token}" />
          </OutputClaims>
          ...
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. 保存 *TrustframeworkExtensions.xml* 文件。
1. 打开你的信赖方策略文件，例如 *SignUpOrSignIn.xml*，向 **TechnicalProfile** 中添加 **OutputClaim** 元素：

    ```xml
    <RelyingParty>
      <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
      <TechnicalProfile Id="PolicyProfile">
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="identityProviderAccessToken" PartnerClaimType="idp_access_token"/>
        </OutputClaims>
        ...
      </TechnicalProfile>
    </RelyingParty>
    ```

1. 保存策略文件。

## <a name="test-your-policy"></a>测试策略

在 Azure AD B2C 中测试应用程序时，可以使 Azure AD B2C 令牌返回到 `https://jwt.ms` 以便能够在其中查看声明，这可能很有用处。

### <a name="upload-the-files"></a>上传文件

1. 登录 [Azure 门户](https://portal.azure.com/)。
1. 请确保使用的是包含 Azure AD B2C 租户的目录。 在门户工具栏中选择“目录 + 订阅”图标。
1. 在“门户设置 | 目录+订阅”页上的“目录名称”列表中找到你的 Azure AD B2C 目录，然后选择“切换”。
1. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“Azure AD B2C” 。
1. 选择“标识体验框架”。
1. 在“自定义策略”页上，单击“上传策略”。
1. 选择“覆盖策略(若存在)”，然后搜索并选择 *TrustframeworkExtensions.xml* 文件。
1. 选择“上传”。
1. 针对信赖方文件（例如 *SignUpOrSignIn.xml*）重复步骤 5 到 7。

### <a name="run-the-policy"></a>运行策略

1. 打开你更改的策略。 例如，*B2C_1A_signup_signin*。
1. 对于“应用程序”  ，选择你之前注册的应用程序。 “回复 URL”  应当显示 `https://jwt.ms` 才能看到以下示例中的令牌。
1. 选择“立即运行”。

    应会看到类似于以下示例的内容：

    ![jwt.ms 中突出显示了 idp_access_token 块的已解码令牌](./media/idp-pass-through-user-flow/identity-provider-pass-through-token-custom.png)

::: zone-end

## <a name="next-steps"></a>后续步骤

在 [Azure AD B2C 令牌概述](tokens-overview.md)中了解详细信息。
