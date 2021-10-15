---
title: 使用自定义策略设置密码更改
titleSuffix: Azure AD B2C
description: 了解如何设置自定义策略，以便用户可以在 Azure Active Directory B2C 中更改其密码。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/24/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 16295eb707968a606c74813f9f6b7585aaf59546
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128570614"
---
# <a name="set-up-password-change-by-using-custom-policies-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中使用自定义策略设置密码更改

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

可以配置 Azure Active Directory B2C (Azure AD B2C)，以便使用本地帐户登录的用户无需使用电子邮件验证来证明其身份即可更改密码。 

密码更改流程涉及以下步骤：

1. 用户登录到其本地帐户。 如果会话仍处于活动状态，Azure AD B2C 会向用户授权，并跳到下一步。
1. 在“旧密码”中，用户验证旧密码。 在“新密码”中，创建并确认新密码。

   ![显示用于更改密码的两个带编号对话框的屏幕截图。](./media/add-password-change-policy/password-change-flow.png)  

> [!TIP]
> 用户只有在知道密码并且想要更改其密码时，才能使用本文中所述的密码更改流程。 建议同时启用[自助式密码重置](add-password-reset-policy.md)，以便在用户忘记密码时提供支持。

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="prerequisites"></a>先决条件

* 完成 [Active Directory B2C 中的自定义策略入门](tutorial-create-user-flows.md?pivots=b2c-custom-policy)中的步骤。
* [在 Azure Active Directory B2C 中注册 Web 应用程序](tutorial-register-applications.md)。

## <a name="add-the-elements"></a>添加元素

1. 打开 TrustFrameworkExtensions.xml 文件。 向 [ClaimsSchema](claimsschema.md) 元素添加标识符为 `oldPassword` 的以下 ClaimType 元素：

    ```xml
    <BuildingBlocks>
      <ClaimsSchema>
        <ClaimType Id="oldPassword">
          <DisplayName>Old Password</DisplayName>
          <DataType>string</DataType>
          <UserHelpText>Enter your old password</UserHelpText>
          <UserInputType>Password</UserInputType>
        </ClaimType>
      </ClaimsSchema>
    </BuildingBlocks>
    ```

1. [ClaimsProvider](claimsproviders.md) 元素包含对用户进行身份验证的技术配置文件。 将以下声明提供程序添加到 **ClaimsProviders** 元素：

    ```xml
    <ClaimsProviders>
      <ClaimsProvider>
        <DisplayName>Local Account SignIn</DisplayName>
        <TechnicalProfiles>
          <TechnicalProfile Id="login-NonInteractive-PasswordChange">
            <DisplayName>Local Account SignIn</DisplayName>
            <InputClaims>
              <InputClaim ClaimTypeReferenceId="oldPassword" PartnerClaimType="password" Required="true" />
              </InputClaims>
            <IncludeTechnicalProfile ReferenceId="login-NonInteractive" />
          </TechnicalProfile>
        </TechnicalProfiles>
      </ClaimsProvider>
      <ClaimsProvider>
        <DisplayName>Local Account Password Change</DisplayName>
        <TechnicalProfiles>
          <TechnicalProfile Id="LocalAccountWritePasswordChangeUsingObjectId">
            <DisplayName>Change password (username)</DisplayName>
            <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
            <Metadata>
              <Item Key="ContentDefinitionReferenceId">api.selfasserted</Item>
            </Metadata>
            <InputClaims>
              <InputClaim ClaimTypeReferenceId="objectId" />
            </InputClaims>
            <OutputClaims>
              <OutputClaim ClaimTypeReferenceId="oldPassword" Required="true" />
              <OutputClaim ClaimTypeReferenceId="newPassword" Required="true" />
              <OutputClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
            </OutputClaims>
            <ValidationTechnicalProfiles>
              <ValidationTechnicalProfile ReferenceId="login-NonInteractive-PasswordChange" />
              <ValidationTechnicalProfile ReferenceId="AAD-UserWritePasswordUsingObjectId" />
            </ValidationTechnicalProfiles>
          </TechnicalProfile>
        </TechnicalProfiles>
      </ClaimsProvider>
    </ClaimsProviders>
    ```

1. [UserJourney](userjourneys.md) 元素定义用户与应用程序进行交互时使用的路径。 如果不存在 UserJourneys 元素，则添加一个标识符为 `PasswordChange` 的 UserJourney：

    ```xml
    <UserJourneys>
      <UserJourney Id="PasswordChange">
        <OrchestrationSteps>
          <OrchestrationStep Order="1" Type="ClaimsProviderSelection" ContentDefinitionReferenceId="api.signuporsignin">
            <ClaimsProviderSelections>
              <ClaimsProviderSelection TargetClaimsExchangeId="LocalAccountSigninEmailExchange" />
            </ClaimsProviderSelections>
          </OrchestrationStep>
          <OrchestrationStep Order="2" Type="ClaimsExchange">
            <ClaimsExchanges>
              <ClaimsExchange Id="LocalAccountSigninEmailExchange" TechnicalProfileReferenceId="SelfAsserted-LocalAccountSignin-Email" />
            </ClaimsExchanges>
          </OrchestrationStep>
          <OrchestrationStep Order="3" Type="ClaimsExchange">
            <ClaimsExchanges>
              <ClaimsExchange Id="NewCredentials" TechnicalProfileReferenceId="LocalAccountWritePasswordChangeUsingObjectId" />
            </ClaimsExchanges>
          </OrchestrationStep>
          <OrchestrationStep Order="4" Type="ClaimsExchange">
            <ClaimsExchanges>
              <ClaimsExchange Id="AADUserReadWithObjectId" TechnicalProfileReferenceId="AAD-UserReadUsingObjectId" />
            </ClaimsExchanges>
          </OrchestrationStep>
          <OrchestrationStep Order="5" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
        </OrchestrationSteps>
        <ClientDefinition ReferenceId="DefaultWeb" />
      </UserJourney>
    </UserJourneys>
    ```

1. 保存 *TrustFrameworkExtensions.xml* 策略文件。
1. 复制随初学者包下载的 *ProfileEdit.xml* 文件，并将其命名为 *ProfileEditPasswordChange.xml*。
1. 打开新文件，并使用唯一值更新 **PolicyId** 属性。 此值是策略的名称。 例如，*B2C_1A_profile_edit_password_change*。
1. 修改 DefaultUserJourney 中的 ReferenceId 属性，使其与创建的新用户旅程的 ID 匹配。 例如，*PasswordChange*。
1. 保存所做更改。

## <a name="upload-and-test-the-policy"></a>上传并测试策略

1. 登录 [Azure 门户](https://portal.azure.com/)。
1. 在门户工具栏中选择“目录 + 订阅”图标，确保使用包含 Azure AD B2C 租户的目录。
1. 在“门户设置 | 目录+订阅”页上的“目录名称”列表中找到你的 Azure AD B2C 目录，然后选择“切换”。
1. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“Azure AD B2C” 。
1. 选择“标识体验框架”。
1. 在“自定义策略”中，选择“上传策略”。
1. 选择“覆盖策略(若存在)”，然后搜索并选择 TrustFrameworkExtensions.xml 文件。
1. 选择“上传”。 
1. 针对信赖方文件（例如 *ProfileEditPasswordChange.xml*）重复步骤 5 到 7。

## <a name="run-the-policy"></a>运行策略

1. 打开你更改的策略。 例如，*B2C_1A_profile_edit_password_change*。
1. 对于“应用程序”，选择在前面注册的应用程序。 若要查看令牌，“回复 URL”应当显示 `https://jwt.ms`。
1. 选择“立即运行”。 在打开的新选项卡中，从 URL 中删除“&prompt=login”并刷新该选项卡，然后用之前创建的帐户登录。 密码更改对话框将提供更改密码的选项。

## <a name="next-steps"></a>后续步骤

* 在 GitHub 上查找[示例策略](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/password-change)。
* 了解如何 [在 Azure AD B2C 中配置密码复杂性](password-complexity.md)。
* 创建[密码重置流](add-password-reset-policy.md)。

::: zone-end
