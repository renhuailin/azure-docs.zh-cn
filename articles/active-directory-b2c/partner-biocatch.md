---
title: 将 BioCatch 和 Azure Active Directory B2C 一起配置的教程
titleSuffix: Azure AD B2C
description: 将 Azure Active Directory B2C 和 BioCatch 一起配置以识别风险用户和欺诈用户的教程
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/20/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: c6b3802add796184714f389b813765945a8d20a4
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2021
ms.locfileid: "122183905"
---
# <a name="tutorial-configure-biocatch-with-azure-active-directory-b2c"></a>教程：将 BioCatch 和 Azure Active Directory B2C 一起配置

在此示例教程中，了解如何将 Azure Active Directory (AD) B2C 身份验证与 [BioCatch](https://www.biocatch.com/) 集成，以进一步增强客户标识和访问管理 (CIAM) 安全状况。 BioCatch 分析用户的物理和认知数字行为，以生成可区分合法客户和网络罪犯的见解。

## <a name="prerequisites"></a>先决条件

若要开始，需要：

- Azure 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。

- 一个已链接到 Azure 订阅的 [Azure AD B2C 租户](tutorial-create-tenant.md)。

- [BioCatch](https://www.biocatch.com/contact-us) 帐户。

## <a name="scenario-description"></a>方案描述

BioCatch 集成包括以下组件：

- **Web 应用或 Web 服务** - 用户先浏览到此 Web 服务。 此 Web 服务实例化要发送到 BioCatch 的唯一客户端会话 ID。 然后，客户端会话 ID 立即开始将用户行为特征传输到 BioCatch。

- **方法** - 将唯一客户端会话 ID 发送到 Azure AD B2C。 在提供的示例中，JavaScript 用于将值输入隐藏的 HTML 字段中。

- **Azure AD B2C 自定义 UI** - 如果使用以上方法，则会为 JavaScript 中的客户端会话 ID 输入隐藏 HTML 字段

- **Azure AD B2C 自定义策略**

  - 以声明形式从 UI 获取自定义客户端会话 ID。 这可通过自断言技术配置文件实现

  - 通过 REST API 声明提供程序与 BioCatch 集成，并将客户端会话 ID 传递到 BioCatch 平台

  - 对于自定义策略逻辑，会从 BioCatch 返回多个自定义声明，然后对其执行操作

  - UserJourney，用于评估返回的声明（如会话风险），并有条件地执行操作（如调用多重身份验证 (MFA)）。

![Bio Catch 体系结构示意图。](media/partner-biocatch/biocatch-architecture-diagram.png)

| 步骤  | 说明 |
|:---|:-----------------------|
|1a     | 用户浏览 Web 服务。 Web 服务随后返回 HTML、CSS 或 JavaScript 值，并将其配置为加载 BioCatch JavaScript SDK。 客户端 JavaScript 配置/设置 BioCatch SDK 的客户端会话 ID。 此外，Web 服务还可以预配置客户端会话 ID 并发送到客户端。        |
|1b    |  针对 BioCatch 平台配置实例化的 BioCatch JavaScript SDK。 使用步骤 1a 中的客户端会话 ID 立即开始将用户行为特征从客户端设备发送到 BioCatch。    |
|2     |  用户尝试注册/登录，并重定向到 Azure AD B2C。      |
|3a    | Userjourney 的一部分是自断言 ClaimsProvider，可将客户端会话 ID 作为输入。 此字段会在屏幕上隐藏。 可以使用 JavaScript 在字段中输入会话 ID。 选择“下一步”按钮，继续注册/登录过程。|
|3b     | 将客户端会话 ID 提交到 BioCatch 平台，以确定风险评分。 |
|3c     | BioCatch 返回有关会话的信息（如风险评分），以及要执行操作的建议：允许或阻止 |
|3D    |Userjourney 有一个条件检查步骤，这一步对返回的声明进行操作|
| 4   | 基于条件检查结果，调用了升级 MFA 等操作|
|5    | 用户首次点击 Web 服务页面时，Web 服务随时可以使用客户端会话 ID 查询 BioCatch API，实时确定风险评分和会话信息。 |

## <a name="onboard-with-biocatch"></a>加入 BioCatch

联系 [BioCatch](https://www.biocatch.com/contact-us) 并创建帐户。

## <a name="configure-the-custom-ui"></a>配置自定义 UI

建议隐藏客户端会话 ID 字段。 使用 CSS、JavaScript 或任何其他方法隐藏字段。 出于测试目的，你可以取消隐藏字段。 例如，JavaScript 用于隐藏输入字段，如下所示：

```JavaScript
document.getElementById("clientSessionId").style.display = 'none';
```

## <a name="configure--azure-ad-b2c-identity-experience-framework-policies"></a>配置 Azure AD B2C Identity Experience Framework 策略

1. 配置初始[自定义策略配置](./tutorial-create-user-flows.md?pivots=b2c-custom-policy)。

2. 创建一个从扩展文件继承的新文件。

    ```XML
    <BasePolicy> 

        <TenantId>tenant.onmicrosoft.com</TenantId> 

        <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId> 

      </BasePolicy> 
    ```

3. 在 BuildingBlocks 资源下创建对自定义 UI 的引用以隐藏输入框。

    ```XML
    <ContentDefinitions> 

        <ContentDefinition Id="api.selfasserted"> 

            <LoadUri>https://domain.com/path/to/selfAsserted.cshtml</LoadUri> 

            <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.1.0</DataUri> 

          </ContentDefinition> 

        </ContentDefinitions>
    ```

4. 在 BuildingBlocks 资源下添加以下声明。

    ```XML
    <ClaimsSchema> 

          <ClaimType Id="riskLevel"> 

            <DisplayName>Session risk level</DisplayName> 

            <DataType>string</DataType>       

          </ClaimType> 

          <ClaimType Id="score"> 

            <DisplayName>Session risk score</DisplayName> 

            <DataType>int</DataType>       

          </ClaimType> 

          <ClaimType Id="clientSessionId"> 

            <DisplayName>The ID of the client session</DisplayName> 

            <DataType>string</DataType> 

            <UserInputType>TextBox</UserInputType> 

          </ClaimType> 

    </ClaimsSchema> 
    ```

5. 为客户端会话 ID 字段配置自断言声明提供程序。

    ```XML
    <ClaimsProvider> 

          <DisplayName>Client Session ID Claims Provider</DisplayName> 

          <TechnicalProfiles> 

            <TechnicalProfile Id="login-NonInteractive-clientSessionId"> 

              <DisplayName>Client Session ID TP</DisplayName> 

              <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" /> 

              <Metadata> 

                <Item Key="ContentDefinitionReferenceId">api.selfasserted</Item> 

              </Metadata> 

              <CryptographicKeys> 

                <Key Id="issuer_secret" StorageReferenceId="B2C_1A_TokenSigningKeyContainer" /> 

              </CryptographicKeys> 

            <!—Claim we created earlier --> 

              <OutputClaims> 

                <OutputClaim ClaimTypeReferenceId="clientSessionId" Required="false" DefaultValue="100"/> 

              </OutputClaims> 

            <UseTechnicalProfileForSessionManagement ReferenceId="SM-AAD" /> 

            </TechnicalProfile> 

          </TechnicalProfiles> 

        </ClaimsProvider> 
    ```

6. 为 BioCatch 配置 REST API 声明提供程序。 

    ```XML
    <TechnicalProfile Id="BioCatch-API-GETSCORE"> 

          <DisplayName>Technical profile for BioCatch API to return session information</DisplayName> 

          <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />

          <Metadata> 

            <Item Key="ServiceUrl">https://biocatch-url.com/api/v6/score?customerID=<customerid>&amp;action=getScore&amp;uuid=<uuid>&amp;customerSessionID={clientSessionId}&amp;solution=ATO&amp;activtyType=<activity_type>&amp;brand=<brand></Item>

            <Item Key="SendClaimsIn">Url</Item> 

            <Item Key="IncludeClaimResolvingInClaimsHandling">true</Item> 

            <!-- Set AuthenticationType to Basic or ClientCertificate in production environments --> 

            <Item Key="AuthenticationType">None</Item> 

            <!-- REMOVE the following line in production environments --> 

            <Item Key="AllowInsecureAuthInProduction">true</Item> 

          </Metadata> 

          <InputClaims> 

            <InputClaim ClaimTypeReferenceId="clientsessionId" /> 

          </InputClaims> 

          <OutputClaims> 

            <OutputClaim ClaimTypeReferenceId="riskLevel" /> 

            <OutputClaim ClaimTypeReferenceId="score" /> 

          </OutputClaims> 

          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" /> 

        </TechnicalProfile> 

      </TechnicalProfiles>
    ```

    > [!Note]
    > BioCatch 将为你提供 URL、客户 ID 和唯一用户 ID (uuID) 来进行配置。 客户 SessionID 声明作为 querystring 参数传递到 BioCatch。 可以选择活动类型，例如 MAKE_PAYMENT。

7. 配置 Userjourney；按照示例进行操作

   1. 获取 clientSessionID 作为声明

   1. 调用 BioCatch API 以获取会话信息

   1. 如果返回的声明风险较低，则跳过 MFA 的步骤，否则强制执行用户 MFA 

    ```XML
    <OrchestrationStep Order="8" Type="ClaimsExchange"> 

          <ClaimsExchanges> 

            <ClaimsExchange Id="clientSessionIdInput" TechnicalProfileReferenceId="login-NonInteractive-clientSessionId" /> 

          </ClaimsExchanges> 

        </OrchestrationStep> 

        <OrchestrationStep Order="9" Type="ClaimsExchange"> 

          <ClaimsExchanges> 

            <ClaimsExchange Id="BcGetScore" TechnicalProfileReferenceId=" BioCatch-API-GETSCORE" /> 

          </ClaimsExchanges> 

        </OrchestrationStep> 

        <OrchestrationStep Order="10" Type="ClaimsExchange"> 

          <Preconditions> 

            <Precondition Type="ClaimEquals" ExecuteActionsIf="true"> 

              <Value>riskLevel</Value> 

              <Value>LOW</Value> 

              <Action>SkipThisOrchestrationStep</Action> 

            </Precondition> 

          </Preconditions> 

          <ClaimsExchanges> 

            <ClaimsExchange Id="PhoneFactor-Verify" TechnicalProfileReferenceId="PhoneFactor-InputOrVerify" /> 

          </ClaimsExchanges>
    ```

8. 在信赖方配置上进行配置（可选）

    将 BioCatch 返回的信息（特别是 risklevel 和评分）作为令牌中的声明传递给应用程序非常有用 。

    ```XML
    <RelyingParty> 

    <DefaultUserJourney ReferenceId="SignUpOrSignInMfa" /> 

    <UserJourneyBehaviors> 

      <SingleSignOn Scope="Tenant" KeepAliveInDays="30" /> 

      <SessionExpiryType>Absolute</SessionExpiryType> 

      <SessionExpiryInSeconds>1200</SessionExpiryInSeconds> 

      <ScriptExecution>Allow</ScriptExecution> 

    </UserJourneyBehaviors> 

    <TechnicalProfile Id="PolicyProfile"> 

      <DisplayName>PolicyProfile</DisplayName> 

      <Protocol Name="OpenIdConnect" /> 

      <OutputClaims> 

        <OutputClaim ClaimTypeReferenceId="displayName" /> 

        <OutputClaim ClaimTypeReferenceId="givenName" /> 

        <OutputClaim ClaimTypeReferenceId="surname" /> 

        <OutputClaim ClaimTypeReferenceId="email" /> 

        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub" /> 

        <OutputClaim ClaimTypeReferenceId="identityProvider" />                 

        <OutputClaim ClaimTypeReferenceId="riskLevel" /> 

        <OutputClaim ClaimTypeReferenceId="score" /> 

        <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" /> 

      </OutputClaims> 

      <SubjectNamingInfo ClaimType="sub" /> 

    </TechnicalProfile> 

  </RelyingParty>
    ```

## <a name="integrate-with-azure-ad-b2c"></a>与 Azure AD B2C 集成

按照以下步骤将策略文件添加到 Azure AD B2C

1. 以 Azure AD B2C 租户的全局管理员身份登录  [Azure 门户](https://portal.azure.com/)。

2. 请确保使用的是包含 Azure AD B2C 租户的目录。 选择顶部菜单中的“目录 + 订阅” 筛选器，然后选择包含租户的目录。

3. 选择 Azure 门户左上角的“所有服务” ，搜索并选择 Azure AD B2C **** 。

4. 导航到“Azure AD B2C” >“Identity Experience Framework” ****  ****

3. 将所有策略文件上传到租户。

## <a name="test-the-solution"></a>测试解决方案

1. [注册一个将重定向到 JWT.MS 的虚拟应用程序](./tutorial-register-applications.md?tabs=app-reg-ga)  

2. 在“Identity Experience Framework”下，选择所创建的策略

3. 在策略窗口中，选择虚拟 JWT.MS 应用程序，然后选择“立即运行”

4. 完成注册流程并创建帐户。 返回到 JWT.MS 的令牌应具有 2 倍数量的 riskLevel 和评分声明。 遵循该示例。  

    ```JavaScript
    { 

      "typ": "JWT", 

      "alg": "RS256", 

      "kid": "_keyid" 

    }.{ 

      "exp": 1615872580, 

      "nbf": 1615868980, 

      "ver": "1.0", 

      "iss": "https://tenant.b2clogin.com/12345678-1234-1234-1234-123456789012/v2.0/", 

      "sub": "12345678-1234-1234-1234-123456789012", 

      "aud": "12345678-1234-1234-1234-123456789012", 

      "acr": "b2c_1a_signup_signin_biocatch_policy", 

      "nonce": "defaultNonce", 

      "iat": 1615868980, 

      "auth_time": 1615868980, 

      "name": "John Smith", 

      "email": "john.smith@contoso.com", 

      "given_name": "John", 

      "family_name": "Smith", 

      "riskLevel": "LOW", 

      "score": 275, 

      "tid": "12345678-1234-1234-1234-123456789012" 

    }.[Signature]  

    ```

## <a name="additional-resources"></a>其他资源

- [Azure AD B2C 中的自定义策略](./custom-policy-overview.md)

- [Azure AD B2C 中的自定义策略入门](./tutorial-create-user-flows.md?pivots=b2c-custom-policy&tabs=applications)
