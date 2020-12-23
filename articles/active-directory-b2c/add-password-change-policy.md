---
title: 使用自定义策略配置密码更改
titleSuffix: Azure AD B2C
description: 了解如何在 Azure Active Directory B2C 中使用自定义策略让用户更改其密码。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/17/2020
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: a42cb97d123d0943dab02bf1f70fcf306d6bcd96
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/17/2020
ms.locfileid: "97629114"
---
# <a name="configure-password-change-using-custom-policies-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中使用自定义策略配置密码更改

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

在 Azure Active Directory B2C (Azure AD B2C) 中，可以让使用本地帐户登录的用户更改其密码，而不必通过电子邮件验证来证明其真实性。 密码更改流涉及以下步骤：

1. 使用本地帐户登录。 如果会话仍处于活动状态，Azure AD B2C 会授权用户，并跳到下一步。
1. 用户必须验证 **旧密码**，然后创建并确认 **新密码**。

![密码更改流](./media/add-password-change-policy/password-change-flow.png)

## <a name="prerequisites"></a>先决条件

* 完成 [Active Directory B2C 中的自定义策略入门](custom-policy-get-started.md)中的步骤。
* 如果尚未这样做，请 [在 Azure Active Directory B2C 中注册 web 应用程序](tutorial-register-applications.md)。

## <a name="add-the-elements"></a>添加元素

1. 打开 *TrustframeworkExtensions.xml* 文件，向 [ClaimsSchema](claimsschema.md) 元素中添加标识符为 `oldPassword` 的以下 **ClaimType** 元素：

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

2. [ClaimsProvider](claimsproviders.md) 元素包含对用户进行身份验证的技术配置文件。 将以下声明提供程序添加到 **ClaimsProviders** 元素：

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

3. [UserJourney](userjourneys.md) 元素定义用户与应用程序进行交互时使用的路径。 如果 **UserJourneys** 元素不具有标识为 `PasswordChange` 的 **UserJourney**，则添加一个：

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

4. 保存 *TrustFrameworkExtensions.xml* 策略文件。
5. 复制随初学者包下载的 *ProfileEdit.xml* 文件，并将其命名为 *ProfileEditPasswordChange.xml*。
6. 打开新文件，并使用唯一值更新 **PolicyId** 属性。 此值是策略的名称。 例如，*B2C_1A_profile_edit_password_change*。
7. 修改 `<DefaultUserJourney>` 中的 **ReferenceId** 属性，使其与创建的新用户旅程的 ID 匹配。 例如，*PasswordChange*。
8. 保存所做更改。

## <a name="upload-and-test-the-policy"></a>上传并测试策略

1. 登录 [Azure 门户](https://portal.azure.com/)。
2. 请确保使用包含 Azure AD B2C 租户的目录，方法是选择顶部菜单中的“目录 + 订阅”筛选器，然后选择包含租户的目录。
3. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“Azure AD B2C” 。
4. 选择“标识体验框架”。
5. 在“自定义策略”页上，单击“上传策略”。
6. 选择“覆盖策略(若存在)”，然后搜索并选择 *TrustframeworkExtensions.xml* 文件。
7. 单击“上载” 。
8. 针对信赖方文件（例如 *ProfileEditPasswordChange.xml*）重复步骤 5 到 7。

### <a name="run-the-policy"></a>运行策略

1. 打开你更改的策略。 例如，*B2C_1A_profile_edit_password_change*。
2. 对于“应用程序”  ，选择你之前注册的应用程序。 若要查看令牌，“回复 URL”应当显示 `https://jwt.ms`。
3. 单击 " **立即运行**"。 用先前创建的帐户登录。 现在应该有机会更改密码。

## <a name="next-steps"></a>后续步骤

- 在 [GitHub](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/password-change)上查找示例策略。
- 了解如何 [在 Azure AD B2C 中配置密码复杂性](password-complexity.md)。
- 设置 [密码重置流](add-password-reset-policy.md)。

::: zone-end
