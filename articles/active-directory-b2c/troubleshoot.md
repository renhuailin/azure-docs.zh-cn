---
title: 排查 Azure Active Directory B2C 中的自定义策略和用户流故障
description: 了解一些方法，用于解决使用 Azure Active Directory B2C 中的自定义策略时出现的错误。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: troubleshooting
ms.date: 10/08/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 1977a4bbd72890557f7b01c2998c73a1437f0e55
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2021
ms.locfileid: "129661532"
---
# <a name="troubleshoot-azure-ad-b2c-custom-policies-and-user-flows"></a>排查 Azure AD B2C 自定义策略和用户流故障

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

你的应用程序需要处理来自 Azure B2C 服务的某些错误。 本文重点介绍了一些常见错误及其处理方式。

::: zone pivot="b2c-user-flow"

## <a name="password-reset-error"></a>密码重置错误

当用户流中未启用[自助式密码重置体验](add-password-reset-policy.md#self-service-password-reset-recommended)时，会发生此错误。 因此，选择“忘记密码?”链接不会触发密码重置用户流， 而是将错误代码 `AADB2C90118` 返回给应用程序。

此问题有两种解决方案：  
  - 使用 Azure AD B2C 密码重置用户流以新的身份验证请求进行响应。
  - 使用建议的[自助式密码重置 (SSPR) 体验](add-password-reset-policy.md#self-service-password-reset-recommended)。  


## <a name="user-canceled-the-operation"></a>用户取消操作
当用户取消操作时，Azure AD B2C 服务也会向应用程序返回错误。 下面是用户执行取消操作的场景示例： 
-  用户策略对使用者本地帐户使用建议的[自助式密码重置 (SSPR) 体验](add-password-reset-policy.md#self-service-password-reset-recommended)。 用户选择“忘记密码?”链接，然后在用户流体验完成之前选择“取消”按钮。 在这种情况下，Azure AD B2C 服务会向应用程序返回错误代码 `AADB2C90091`。 
- 用户选择向外部标识提供者（例如 [LinkedIn](identity-provider-linkedin.md)）进行身份验证。 用户在向标识提供者本身进行身份验证之前选择“取消”按钮。 在这种情况下，Azure AD B2C 服务会向应用程序返回错误代码 `AADB2C90273`。 详细了解 [Azure Active Directory B2C 服务返回的错误代码](error-codes.md)。

若要处理此错误，请提取用户的错误说明，并使用相同的用户流以新的身份验证请求进行响应。 

::: zone-end

::: zone pivot="b2c-custom-policy"

如果使用 Azure Active Directory B2C (Azure AD B2C) [自定义策略](custom-policy-overview.md)，可能会遇到策略语言 XML 格式或运行时问题方面的难题。 本文介绍了一些有助于发现和解决问题的工具和技巧。 

本文侧重于排查 Azure AD B2C 自定义策略配置问题， 而不是排查信赖方应用程序或其标识库问题。

## <a name="azure-ad-b2c-correlation-id-overview"></a>Azure AD B2C 相关 ID 概述

Azure AD B2C 相关 ID 是附加到授权请求的唯一标识符值。 它用于用户所采用的所有业务流程步骤。 你可以使用相关 ID：

- 识别应用程序中的登录活动，并跟踪策略的性能。
- 查找登录请求的 Azure Application Insights 日志。
- 将相关 ID 传递到 REST API，并使用它来标识登录流。 

每次建立新会话时，相关 ID 都会更改。 调试策略时，请确保关闭现有浏览器标签页或打开新的私密模式浏览器。

### <a name="get-the-azure-ad-b2c-correlation-id"></a>获取 Azure AD B2C 相关 ID

你可以在 Azure AD B2C 注册或登录页中找到相关 ID。 在浏览器中，选择“查看源”。 相关显示为页面顶部的注释。

![Azure AD B2C 登录页面“查看源”的屏幕截图。](./media/troubleshoot-custom-policies/find-azure-ad-b2c-correlation-id.png)

复制相关 ID，然后继续登录流程。 使用相关 ID 观察登录行为。 有关详细信息，请参阅[排查 Application Insights 的问题](#troubleshooting-with-application-insights)。

### <a name="echo-the-azure-ad-b2c-correlation-id"></a>回送 Azure AD B2C 相关 ID

你可以将相关 ID 包括在 Azure AD B2C 令牌中。 相关 ID 的包括步骤：

1. 打开策略的扩展文件， 例如，<em>`SocialAndLocalAccounts/``TrustFrameworkExtensions.xml`</em>。
1. 搜索 [BuildingBlocks](buildingblocks.md) 元素。 如果该元素不存在，请添加该元素。
1. 找到 [ClaimsSchema](claimsschema.md) 元素。 如果该元素不存在，请添加该元素。
1. 将相关 ID 声明添加到“ClaimsSchema”元素中。  

    ```xml
    <!-- 
    <BuildingBlocks>
      <ClaimsSchema> -->
        <ClaimType Id="correlationId">
          <DisplayName>correlation ID</DisplayName>
          <DataType>string</DataType>
        </ClaimType>
      <!-- 
      </ClaimsSchema>
    </BuildingBlocks>-->
    ```

1. 打开策略的信赖方文件。 例如 <em>`SocialAndLocalAccounts/`**`SignUpOrSignIn.xml`**</em> 文件。 系统会在用户旅程成功后将输出声明添加到令牌，并发送到应用程序。 修改信赖方部分中的技术配置文件元素，以将 `correlationId` 添加为输出声明。
 
    ```xml
    <RelyingParty>
      <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
      <TechnicalProfile Id="PolicyProfile">
        <DisplayName>PolicyProfile</DisplayName>
        <Protocol Name="OpenIdConnect" />
        <OutputClaims>
          ...
          <OutputClaim ClaimTypeReferenceId="correlationId" DefaultValue="{Context:CorrelationId}" />
        </OutputClaims>
        <SubjectNamingInfo ClaimType="sub" />
      </TechnicalProfile>
    </RelyingParty>
    ```


## <a name="troubleshooting-with-application-insights"></a>排除 Application Insights 的故障

若要诊断自定义策略的问题，请使用 [Application Insights](troubleshoot-with-application-insights.md)。 Application Insights 跟踪自定义策略用户旅程的活动。 它提供一种方法来诊断异常，并观察 Azure AD B2C 和技术配置文件定义的各种声明提供程序（例如标识提供者、基于 API 的服务、Azure AD B2C 用户目录和其他服务）之间的声明交换。  

建议为 [VS Code](https://code.visualstudio.com/) 安装 [Azure AD B2C 扩展](https://marketplace.visualstudio.com/items?itemName=AzureADB2CTools.aadb2c)。 使用 Azure AD B2C 扩展时，日志是按策略名称、相关 ID（Application Insights 显示相关 ID 的第一位数字）和日志时间戳组织的。 此功能可帮助你根据本地时间戳查找相关日志，并查看 Azure AD B2C 执行的用户旅程。 

> [!NOTE]
> - 在 Application Insights 中看到新日志之前会有一小段延迟（通常不到 5 分钟）。
> - 社区已开发适用于 Azure AD B2C 的 Visual Studio Code 扩展来帮助标识开发人员。 Microsoft 不支持此扩展，它严格按原样提供。

一个登录流可以发出多个 Azure Application Insights 跟踪。 在下面的屏幕截图中，B2C_1A_signup_signin 策略有三个日志。 每个日志表示部分登录流。

以下屏幕截图显示了 Azure Application Insights 跟踪资源管理器中 VS Code 的 Azure AD B2C 扩展。

![适用于 VS Code 的 Azure AD B2C 扩展的屏幕截图，其中显示了 Azure Application Insights 跟踪。](./media/troubleshoot-custom-policies/vscode-extension-application-insights-trace.png)

### <a name="filter-the-trace-log"></a>筛选跟踪日志

将焦点放在 Azure AD B2C 跟踪资源管理器上，开始键入相关 ID 的第一个数字或要查找的时间。 你将在 Azure AD B2C 跟踪资源管理器的右上方看到一个筛选器框，其中显示到目前已键入的内容，还会突出显示匹配的跟踪日志。  

![Azure AD B2C 扩展 Azure AD B2C 跟踪资源管理器中突出显示筛选内容的屏幕截图。](./media/troubleshoot-custom-policies/vscode-extension-application-insights-highlight.png)

将鼠标悬停在筛选器框上，选择“启用键入时筛选”将仅显示匹配的跟踪日志。 使用“X”清除按钮清除筛选。

![Azure AD B2C 扩展 Azure AD B2C 跟踪资源管理器筛选器的屏幕截图。](./media/troubleshoot-custom-policies/vscode-extension-application-insights-filter.png)

### <a name="application-insights-trace-log-details"></a>Application Insights 跟踪日志详细信息

选择 Azure Application Insights 跟踪后，扩展将打开“Application Insights 详细信息”窗口，其中包含以下信息：

- **Application Insights** - 有关跟踪日志的一般信息，包括策略名称、相关 ID、Azure Application Insights 跟踪 ID 和跟踪时间戳。
- **技术配置文件** - 跟踪日志中显示的技术配置文件的列表。
- **声明** - 在跟踪日志显示的声明及其值的按字母顺序排列的列表。 如果一个声明（带不同的值）多次显示在跟踪日志中，由 `=>` 符号指定最新的值。 你可以查看这些声明以确定是否正确设置了预期的声明值。 例如，如果你有一个检查声明值的先决条件，声明部分可帮助你确定预期流出现不同行为的原因。
- **声明转换** - 跟踪日志中显示的声明转换的列表。 每个声明转换包含输入声明、输入参数和输出声明。 声明转换部分提供了对发送来的数据的见解以及声明转换的结果。
- **令牌** - 跟踪日志中出现的令牌的列表。 令牌包括基础联合 OAuth 和 OpenId Connect 标识提供者的令牌。 联合标识提供者的令牌提供有关标识提供者如何将声明返回到 Azure AD B2C 以便映射标识提供者技术配置文件输出声明的详细信息。 
- **异常** - 跟踪日志中显示的异常或严重错误的列表。
- **Application Insights JSON** - 从 Application Insights 返回的原始数据。

以下屏幕截图显示了 Application Insights 跟踪日志详细信息窗口的示例。  

![Azure AD B2C 扩展 Azure AD B2C 跟踪报表的屏幕截图。](./media/troubleshoot-custom-policies/vscode-extension-application-insights-report.png)

## <a name="troubleshoot-jwt-tokens"></a>对 JWT 令牌进行故障排除

在 JWT 令牌验证和调试时，你可以使用 [https://jwt.ms](https://jwt.ms) 等站点来解码 JWT。 创建可重定向到 `https://jwt.ms` 进行令牌检查的测试应用程序。 如果尚未创建，可以[注册 Web 应用](tutorial-register-applications.md)并[启用 ID 令牌隐式授权](tutorial-register-applications.md#enable-id-token-implicit-grant)。 

![JWT 令牌预览的屏幕截图。](./media/troubleshoot-custom-policies/jwt-token-preview.png)

使用“立即运行”和 `https://jwt.ms` 独立于 Web 应用程序或移动应用程序测试策略。 此网站的作用类似于信赖方应用程序。 它显示 Azure AD B2C 策略生成的 JSON Web 令牌 (JWT) 的内容。

## <a name="troubleshoot-saml-protocol"></a>对 SAML 协议进行故障排除

若要帮助配置和调试与服务提供程序的集成，可以使用 SAML 协议的浏览器扩展，例如 Chrome 的 [SAML DevTools 扩展](https://chrome.google.com/webstore/detail/saml-devtools-extension/jndllhgbinhiiddokbeoeepbppdnhhio)、FireFox 的 [SAML 跟踪程序](https://addons.mozilla.org/es/firefox/addon/saml-tracer/)或 [Edge 或 IE 开发人员工具](https://techcommunity.microsoft.com/t5/microsoft-sharepoint-blog/gathering-a-saml-token-using-edge-or-ie-developer-tools/ba-p/320957)。

以下屏幕截图演示 SAML DevTools 扩展如何呈现 Azure AD B2C 发送到标识提供者的 SAML 请求以及 SAML 响应。

![SAML 协议跟踪日志的屏幕截图。](./media/troubleshoot-custom-policies/saml-protocol-trace.png)

使用这些工具，可以检查应用程序和 Azure AD B2C 之间的集成。 例如：

- 检查 SAML 请求是否包含签名，并确定用于登录授权请求的算法。
- 检查 Azure AD B2C 是否返回错误消息。
- 检查断言部分是否已加密。
- 获取返回给标识提供者的声明名称。

你还可以通过 [Fiddler](https://www.telerik.com/fiddler) 跟踪客户端浏览器与 Azure AD B2C 之间的消息交换。 通过它可以了解用户旅程在业务流程步骤中的哪个环节失败。

## <a name="troubleshoot-policy-validity"></a>对策略有效性进行故障排除

完成策略开发后，将策略上传到 Azure AD B2C。 你的策略可能存在一些问题。 使用以下方法确保策略的完整性/有效性。

设置自定义策略时最常见的错误是设置了不正确的 XML 格式。 好的 XML 编辑器可以说是必不可少。 它会原生显示 XML、对内容进行色彩编码、预先填充常用字词、保留带索引的 XML 元素，并可以针对 XML 架构进行验证。

建议使用[ Visual Studio Code](https://code.visualstudio.com/)。 然后安装 XML 扩展，如 [Red Hat 的 XML 语言支持](https://marketplace.visualstudio.com/items?itemName=redhat.vscode-xml)。 XML 扩展使你可以在上传 XML 文件之前，使用自定义策略 [XSD](https://raw.githubusercontent.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/master/TrustFrameworkPolicy_0.3.0.0.xsd) 架构定义来验证 XML 架构。

将以下设置添加到 VS Code `settings.json` 文件中，即可使用 XML 文件关联策略将 XML 文件绑定到 XSD。 为此，请执行以下操作：

1. 在 VS Code 中，单击“设置”。 有关详细信息，请参阅[用户和工作区设置](https://code.visualstudio.com/docs/getstarted/settings)。
1. 搜索“fileAssociations”，然后在“扩展”下选择“XML”  。
1. 选择“在 settings.json 中编辑”。

    ![VS Code XML 架构验证的屏幕截图。](./media/troubleshoot-custom-policies/xml-validation.png)
1. 在 settings.json 中，添加以下 JSON 代码：

    ```json
    "xml.fileAssociations": [
      {
        "pattern": "**.xml",
        "systemId": "https://raw.githubusercontent.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/master/TrustFrameworkPolicy_0.3.0.0.xsd"
      }
    ]
    ```

下面的示例显示了一个 XML 验证错误。 将鼠标移到元素名称上时，扩展会列出预期的元素。

![VS Code XML 架构验证错误指示器的屏幕截图。](./media/troubleshoot-custom-policies/xml-validation-error.png)

在下面的示例中，`DisplayName` 元素正确。 但顺序不正确。 `DisplayName` 应位于 `Protocol` 元素之前。 若要解决此问题，请将 `DisplayName` 元素用鼠标移动为正确的元素顺序。

![VS Code XML 架构验证顺序错误的屏幕截图。](./media/troubleshoot-custom-policies/xml-validation-order-error.png)

## <a name="upload-policies-and-policy-validation"></a>上传策略和策略验证

上传时会自动执行 XML 策略文件的验证。 大多数错误会导致上传失败。 验证包括正在上传的策略文件。 它还包括上传文件引用的文件链（信赖方策略文件、扩展文件和基本文件）。

> [!TIP]
> Azure AD B2C 为信赖方策略运行其他验证。 策略出现问题时，即使仅编辑扩展策略，最好也要上传信赖方策略。 

本部分包含常见验证错误和可能的解决方案。

### <a name="schema-validation-error-found-has-invalid-child-element-name"></a>发现架构验证错误…具有无效的子元素“{name}”

策略包含无效的 XML 元素；或 XML 元素有效，但似乎顺序不正确。 若要解决此类错误，请查看[对策略有效性进行故障排除](#troubleshoot-policy-validity)部分。

### <a name="there-is-a-duplicate-key-sequence-number"></a>存在重复的键序列“{number}” 

用户[旅程](userjourneys.md)或[子旅程](subjourneys.md)包含按顺序执行的业务流程步骤的有序列表。 更改旅程后，请按顺序对这些步骤重新编号，不要跳过任何整数（1 到 N）。

> [!TIP]
> 你可以使用适用于 [VS Code](https://code.visualstudio.com/) `(Shift+Ctrl+r)` 的 [Azure AD B2C 扩展](https://marketplace.visualstudio.com/items?itemName=AzureADB2CTools.aadb2c)命令，对策略中的所有用户旅程和子旅程业务流程步骤重新编号。

### <a name="was-expected-to-have-step-with-order-number-but-it-was-not-found"></a>…应有顺序为“{number}”的步骤，但找不到该步骤…

查看前一个错误的相关内容。

### <a name="orchestration-step-order-number-in-user-journey-name-is-followed-by-a-claims-provider-selection-step-and-must-be-a-claims-exchange-but-it-is-of-type"></a>用户旅程“{name}”中的业务流程步骤“{number} …”后跟声明提供程序选择步骤，并且必须是声明交换，但类型为…

`ClaimsProviderSelection` 和 `CombinedSignInAndSignUp` 的业务流程步骤类型包含可供用户选择的选项列表。 它必须后跟带有一个或多个声明交换的 `ClaimsExchange` 类型。

下面的业务流程步骤导致此类错误。 第二个业务流程步骤的类型必须是 `ClaimsExchange`，而不是 `ClaimsProviderSelection`。

```xml
<!-- 
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
    <OrchestrationSteps>-->
      <OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
        <ClaimsProviderSelections>
          <ClaimsProviderSelection TargetClaimsExchangeId="FacebookExchange"/>
          <ClaimsProviderSelection ValidationClaimsExchangeId="LocalAccountSigninEmailExchange"/>
        </ClaimsProviderSelections>
        <ClaimsExchanges>
          <ClaimsExchange Id="LocalAccountSigninEmailExchange" TechnicalProfileReferenceId="SelfAsserted-LocalAccountSignin-Email"/>
        </ClaimsExchanges>
      </OrchestrationStep> 
      
      <OrchestrationStep Order="2" Type="ClaimsProviderSelection">
        ...
      </OrchestrationStep>
      ...
    <!--
    </OrchestrationSteps>
  </UserJourney>
</UserJourneys> -->
```

### <a name="step-number-with-2-claims-exchanges-it-must-be-preceded-by-a-claims-provider-selection-in-order-to-determine-which-claims-exchange-can-be-used"></a>…步骤 {number}，含 2 个声明交换。 它前面必须是声明提供程序选择，才能确定可以使用哪些声明交换

业务流程步骤类型 `ClaimsExchange` 必须只有一个 `ClaimsExchange`，除非上一步的类型是 `ClaimsProviderSelection` 或 `CombinedSignInAndSignUp`。 下面的业务流程步骤导致此类错误。 第六个步骤包含两个声明交换。 

```xml
<!-- 
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
    <OrchestrationSteps>-->
      ...
      <OrchestrationStep Order="5" Type="ClaimsExchange">
        ...
        <ClaimsExchanges>
          <ClaimsExchange Id="SelfAsserted-Social" TechnicalProfileReferenceId="SelfAsserted-Social"/>
        </ClaimsExchanges>
      </OrchestrationStep>
      <OrchestrationStep Order="6" Type="ClaimsExchange">
        <ClaimsExchanges>
          <ClaimsExchange Id="Call-REST-First-API" TechnicalProfileReferenceId="Call-REST-First-API"/>
          <ClaimsExchange Id="Call-REST-Second-API" TechnicalProfileReferenceId="Call-REST-Second-API"/>
        </ClaimsExchanges>
      </OrchestrationStep>
      ...
    <!--
    </OrchestrationSteps>
  </UserJourney>
</UserJourneys> -->
```

若要修复此类错误，请使用两个业务流程步骤。 每个业务流程步骤都有一个声明交换。

```xml
<!-- 
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
    <OrchestrationSteps>-->
      ...
      <OrchestrationStep Order="5" Type="ClaimsExchange">
        ...
        <ClaimsExchanges>
          <ClaimsExchange Id="SelfAsserted-Social" TechnicalProfileReferenceId="SelfAsserted-Social"/>
        </ClaimsExchanges>
      </OrchestrationStep>
      <OrchestrationStep Order="6" Type="ClaimsExchange">
        <ClaimsExchanges>
          <ClaimsExchange Id="Call-REST-First-API" TechnicalProfileReferenceId="Call-REST-First-API"/>
        </ClaimsExchanges>
      </OrchestrationStep>
      <OrchestrationStep Order="7" Type="ClaimsExchange">
        <ClaimsExchanges>
          <ClaimsExchange Id="Call-REST-Second-API" TechnicalProfileReferenceId="Call-REST-Second-API"/>
        </ClaimsExchanges>
      </OrchestrationStep>
      ...
    <!--
    </OrchestrationSteps>
  </UserJourney>
</UserJourneys> -->
```

### <a name="there-is-a-duplicate-key-sequence-name"></a>存在重复的键序列“{name}”

旅程有多个具有相同 `Id` 的 `ClaimsExchange`。 以下步骤导致此类错误。 ID“AADUserWrite”在用户旅程中出现两次。

```xml
<!-- 
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
    <OrchestrationSteps>-->
      ...
      <OrchestrationStep Order="7" Type="ClaimsExchange">
        <ClaimsExchanges>
          <ClaimsExchange Id="AADUserWrite" TechnicalProfileReferenceId="AAD-UserWriteUsingAlternativeSecurityId"/>
        </ClaimsExchanges>
      </OrchestrationStep>
      <OrchestrationStep Order="8" Type="ClaimsExchange">
        <ClaimsExchanges>
          <ClaimsExchange Id="AADUserWrite" TechnicalProfileReferenceId="Call-REST-API"/>
        </ClaimsExchanges>
      </OrchestrationStep>
      ...
    <!--
    </OrchestrationSteps>
  </UserJourney>
</UserJourneys> -->
```

若要修复此类错误，请将第八个业务流程步骤的声明交换更改为唯一名称，如 Call-REST-API。

```xml
<!-- 
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
    <OrchestrationSteps>-->
      ...
      <OrchestrationStep Order="7" Type="ClaimsExchange">
        <ClaimsExchanges>
          <ClaimsExchange Id="AADUserWrite" TechnicalProfileReferenceId="AAD-UserWriteUsingAlternativeSecurityId"/>
        </ClaimsExchanges>
      </OrchestrationStep>
      <OrchestrationStep Order="8" Type="ClaimsExchange">
        <ClaimsExchanges>
          <ClaimsExchange Id="Call-REST-API" TechnicalProfileReferenceId="Call-REST-API"/>
        </ClaimsExchanges>
      </OrchestrationStep>
      ...
    <!--
    </OrchestrationSteps>
  </UserJourney>
</UserJourneys> -->
```

### <a name="makes-a-reference-to-claimtype-with-id-claim-name-but-neither-the-policy-nor-any-of-its-base-policies-contain-such-an-element"></a>…引用 ID 为“{claim name}”的 ClaimType，但策略及其任何基本策略均不包含这样的元素

当策略引用未在[声明架构](claimsschema.md)中进行声明的声明时，将发生此类错误。 必须至少在策略中的一个文件内定义声明。 

例如，具有 schoolId 输出声明的技术配置文件。 但从不会在策略或上级策略中声明 schoolId 输出声明。

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="schoolId" />
  ...
</OutputClaims>
```

若要修复此类错误，请检查 `ClaimTypeReferenceId` 值是否拼写错误，或者是否不存在于架构中。 如果声明是在扩展策略中定义的，但也用在基本策略中。 请确保声明在使用它的策略中或在更高一级的策略中定义。

将声明添加到声明架构可以解决此类错误。

```xml
<!--
<BuildingBlocks>
  <ClaimsSchema> -->
    <ClaimType Id="schoolId">
      <DisplayName>School name</DisplayName>
      <DataType>string</DataType>
      <UserHelpText>Enter your school name</UserHelpText>
      <UserInputType>TextBox</UserInputType>
    </ClaimType>
  <!-- 
  </ClaimsSchema>
</BuildingBlocks> -->
```

### <a name="makes-a-reference-to-a-claimstransformation-with-id"></a>…引用 ID 为…的 ClaimsTransformation

此错误的原因类似于声明错误的原因。 查看前一个错误的相关内容。

### <a name="user-is-currently-logged-as-a-user-of-yourtenantonmicrosoftcom-tenant"></a>用户当前登录所用的身份是“yourtenant.onmicrosoft.com”租户…的用户

你登录使用的帐户位于与尝试上传的策略不同的租户中。 例如，通过 admin@contoso.onmicrosoft.com 进行登录，而策略 `TenantId` 设置为 `fabrikam.onmicrosoft.com`。

```xml
<TrustFrameworkPolicy ...
  TenantId="fabrikam.onmicrosoft.com"
  PolicyId="B2C_1A_signup_signin"
  PublicPolicyUri="http://fabrikam.onmicrosoft.com/B2C_1A_signup_signin">

  <BasePolicy>
    <TenantId>fabrikam.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId>
  </BasePolicy>
  ...
</TrustFrameworkPolicy>
```

- 检查 `<TrustFrameworkPolicy\>` 和 `<BasePolicy\>` 元素中的 `TenantId` 值是否与目标 Azure AD B2C 租户匹配。

### <a name="claim-type-name-is-the-output-claim-of-the-relying-partys-technical-profile-but-it-is-not-an-output-claim-in-any-of-the-steps-of-user-journey"></a>声明类型“{name}”是信赖方技术配置文件的输出声明，但它不是用户旅程的任何步骤中的输出声明...

在信赖方策略中，你添加了一个输出声明，但此输出声明不是用户旅程的任何步骤中的输出声明。 Azure AD B2C 无法从声明包读取声明值。

在下面的示例中，schoolId 声明是信赖方的技术配置文件的输出声明，但不是 SignUpOrSignIn 用户旅程的任何步骤中的输出声明 。

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="OpenIdConnect" />
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="schoolId" />
      ...
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
``` 

若要修复此类错误，请确保输出声明至少出现在一个业务流程步骤的技术配置文件输出声明集合中。 如果用户旅程无法输出声明，请在信赖方技术配置文件中设置默认值，例如空字符串。  

```xml
<OutputClaim ClaimTypeReferenceId="schoolId" DefaultValue="" />
```

### <a name="input-string-was-not-in-a-correct-format"></a>输入字符串的格式不正确

对声明设置的值类型错误，它是属于另一种类型的。 例如，你定义了一个整数声明。

```xml
<!--
<BuildingBlocks>
  <ClaimsSchema> -->
    <ClaimType Id="age">
      <DisplayName>Age</DisplayName>
      <DataType>int</DataType>
    </ClaimType>
  <!--
  </ClaimsSchema>
</BuildingBlocks> -->
```

然后，尝试设置一个字符串值：

```xml
<OutputClaim ClaimTypeReferenceId="age" DefaultValue="ABCD" />
```

若要修复此类错误，请确保设置了正确的值，例如 `DefaultValue="0"`。


### <a name="tenant-name-already-has-a-policy-with-id-name-another-policy-with-the-same-id-cannot-be-stored"></a>租户“{name}”已具有一个 ID 为“{name}”的策略。 无法存储具有相同 ID 的另一个策略

你尝试向租户上传策略，但一个同名的策略已被上传到该租户中。 

若要修复此类错误，请在上传策略时，选择“覆盖自定义策略(若已有)”复选框。

![演示如何覆盖自定义策略（若已有）的屏幕截图。](./media/troubleshoot-custom-policies/overwrite-custom-policy-if-exists.png)

::: zone-end



## <a name="next-steps"></a>后续步骤

- 了解如何[使用 Application Insights 收集 Azure Active Directory B2C 日志](troubleshoot-with-application-insights.md)。

