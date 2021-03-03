---
title: 使用 Application Insights 跟踪用户行为
titleSuffix: Azure AD B2C
description: 了解如何在 Application Insights 中启用 Azure AD B2C 用户旅程中的事件日志。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.date: 01/29/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 92da0b12a3119b048866eef5b18f658916595294
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101645919"
---
# <a name="track-user-behavior-in-azure-ad-b2c-by-using-application-insights"></a>使用 Application Insights 跟踪 Azure AD B2C 中的用户行为

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

在 Azure Active Directory B2C (Azure AD B2C) 中，可以通过使用提供给 Application Insights 的检测密钥将事件数据直接发送到 [Azure AD B2C](../azure-monitor/app/app-insights-overview.md) 。 使用 Application Insights 技术配置文件，你可以获取用户旅程的详细自定义事件日志，以便执行以下操作：

- 洞察用户行为。
- 排查自己在开发或生产过程中的策略问题。
- 衡量性能。
- 通过 Application Insights 创建通知。

## <a name="overview"></a>概述

若要启用自定义事件日志，请添加 Application Insights 技术配置文件。 在技术配置文件中，定义要记录的 Application Insights 检测密钥、事件名称和声明。 若要发布事件，请将技术配置文件添加为 [用户旅程](userjourneys.md)中的业务流程步骤。

使用 Application Insights 时，请注意以下事项：

- 在 Application Insights 中提供新日志之前，会有短暂的延迟，通常不到五分钟。
- Azure AD B2C 允许选择要记录的声明。 不要包含包含个人数据的声明。
- 若要记录用户会话，可以使用相关 ID 统一事件。
- 直接从 [用户旅程](userjourneys.md) 或 [sub 旅程](subjourneys.md)调用 Application Insights 技术配置文件。 请勿使用 Application Insights 技术配置文件作为 [验证技术配置文件](validation-technical-profile.md)。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [active-directory-b2c-customization-prerequisites-custom-policy](../../includes/active-directory-b2c-customization-prerequisites-custom-policy.md)]

## <a name="create-an-application-insights-resource"></a>创建 Application Insights 资源

将 Application Insights 与 Azure AD B2C 结合使用时，只需创建资源并获取检测密钥即可。 有关信息，请参阅 [创建 Application Insights 资源](../azure-monitor/app/create-new-resource.md)。

1. 登录 [Azure 门户](https://portal.azure.com/)。
1. 请确保使用的是包含 Azure 订阅的目录。 在顶部菜单中选择 " **目录 + 订阅** " 筛选器，然后选择包含你的 Azure 订阅的目录。 此租户不是你的 Azure AD B2C 租户。
1. 在 Azure 门户的左上角选择 " **创建资源** "，然后搜索并选择 " **Application Insights**"。
1. 选择“创建”。
1. 对于 " **名称**"，请输入资源的名称。
1. 在“应用程序类型”下，选择“ASP.NET web 应用程序”。
1. 对于资源组，选择现有的组，或输入新组的名称。
1. 选择“创建”。
1. 打开新 Application Insights 资源，展开 " **Essentials**"，然后复制检测密钥。

![显示 Application Insights 概述 "选项卡上检测密钥的屏幕截图。](./media/analytics-with-application-insights/app-insights.png)

## <a name="define-claims"></a>定义声明

声明可在 Azure AD B2C 策略执行过程中提供数据的临时存储。 在 [ClaimsSchema 元素](claimsschema.md)中声明声明。

1. 打开策略的扩展文件， 文件看起来可能类似于 `SocialAndLocalAccounts/` **`TrustFrameworkExtensions.xml`** 。
1. 搜索 [BuildingBlocks](buildingblocks.md) 元素。 如果看不到该元素，请添加它。
1. 查找 **ClaimsSchema** 元素。 如果看不到该元素，请添加它。
1. 将以下声明添加到 **ClaimsSchema** 元素：

   ```xml
   <ClaimType Id="EventType">
     <DisplayName>Event type</DisplayName>
     <DataType>string</DataType>
   </ClaimType>
   <ClaimType Id="EventTimestamp">
     <DisplayName>Event timestamp</DisplayName>
     <DataType>string</DataType>
   </ClaimType>
   <ClaimType Id="PolicyId">
     <DisplayName>Policy Id</DisplayName>
     <DataType>string</DataType>
   </ClaimType>
   <ClaimType Id="Culture">
     <DisplayName>Culture ID</DisplayName>
     <DataType>string</DataType>
   </ClaimType>
   <ClaimType Id="CorrelationId">
     <DisplayName>Correlation Id</DisplayName>
     <DataType>string</DataType>
   </ClaimType>
   <ClaimType Id="federatedUser">
     <DisplayName>Federated user</DisplayName>
     <DataType>boolean</DataType>
   </ClaimType>
   <ClaimType Id="parsedDomain">
     <DisplayName>Domain name</DisplayName>
     <DataType>string</DataType>
     <UserHelpText>The domain portion of the email address.</UserHelpText>
   </ClaimType>
   <ClaimType Id="userInLocalDirectory">
     <DisplayName>userInLocalDirectory</DisplayName>
     <DataType>boolean</DataType>
   </ClaimType>
   ```

## <a name="add-new-technical-profiles"></a>新增技术配置文件

可以将技术配置文件视为自定义策略中的函数。 这些函数使用 [技术配置文件包含](technicalprofiles.md#include-technical-profile) 方法，其中技术配置文件包含其他技术配置文件并更改设置或添加新功能。 下表定义了用于打开会话和发布事件的技术配置文件。

| 技术配置文件 | 任务 |
| ----------------- | -----|
| AppInsights-Common | 常见的技术配置文件与典型配置。 它包括 Application Insights 检测密钥、要记录的声明的集合以及开发人员模式。 其他技术配置文件包括通用技术配置文件，并添加更多声明，如事件名称。 |
| AppInsights-SignInRequest | 收到登录请求时，记录包含一组声明的 **SignInRequest** 事件。 |
| AppInsights-UserSignUp | 当用户在注册或登录旅程中触发注册选项时，记录 **UserSignUp** 事件。 |
| AppInsights-SignInComplete | 当令牌被发送到信赖方应用程序时，在身份验证成功后记录 **SignInComplete** 事件。 |

打开初学者包中的 *TrustFrameworkExtensions.xml* 文件。 将技术配置文件添加到 **ClaimsProvider** 元素：

```xml
<ClaimsProvider>
  <DisplayName>Application Insights</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="AppInsights-Common">
      <DisplayName>Application Insights</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.Insights.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <!-- The ApplicationInsights instrumentation key, which you use for logging the events -->
        <Item Key="InstrumentationKey">xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx</Item>
        <Item Key="DeveloperMode">false</Item>
        <Item Key="DisableTelemetry ">false</Item>
      </Metadata>
      <InputClaims>
        <!-- Properties of an event are added through the syntax {property:NAME}, where NAME is the property being added to the event. DefaultValue can be either a static value or a value that's resolved by one of the supported DefaultClaimResolvers. -->
        <InputClaim ClaimTypeReferenceId="EventTimestamp" PartnerClaimType="{property:EventTimestamp}" DefaultValue="{Context:DateTimeInUtc}" />
        <InputClaim ClaimTypeReferenceId="tenantId" PartnerClaimType="{property:TenantId}" DefaultValue="{Policy:TrustFrameworkTenantId}" />
        <InputClaim ClaimTypeReferenceId="PolicyId" PartnerClaimType="{property:Policy}" DefaultValue="{Policy:PolicyId}" />
        <InputClaim ClaimTypeReferenceId="CorrelationId" PartnerClaimType="{property:CorrelationId}" DefaultValue="{Context:CorrelationId}" />
        <InputClaim ClaimTypeReferenceId="Culture" PartnerClaimType="{property:Culture}" DefaultValue="{Culture:RFC5646}" />
      </InputClaims>
    </TechnicalProfile>

    <TechnicalProfile Id="AppInsights-SignInRequest">
      <InputClaims>
        <!-- An input claim with a PartnerClaimType="eventName" is required. This is used by the AzureApplicationInsightsProvider to create an event with the specified value. -->
        <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="SignInRequest" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="AppInsights-Common" />
    </TechnicalProfile>

    <TechnicalProfile Id="AppInsights-UserSignUp">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="UserSignUp" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="AppInsights-Common" />
    </TechnicalProfile>
    
    <TechnicalProfile Id="AppInsights-SignInComplete">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="SignInComplete" />
        <InputClaim ClaimTypeReferenceId="federatedUser" PartnerClaimType="{property:FederatedUser}" DefaultValue="false" />
        <InputClaim ClaimTypeReferenceId="parsedDomain" PartnerClaimType="{property:FederationPartner}" DefaultValue="Not Applicable" />
        <InputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="{property:IDP}" DefaultValue="Local" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="AppInsights-Common" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

> [!IMPORTANT]
> 将 `AppInsights-Common` 技术配置文件中的检测密钥更改为 Application Insights 资源提供的 GUID。

## <a name="add-the-technical-profiles-as-orchestration-steps"></a>添加技术配置文件，作为业务流程步骤

添加引用技术配置文件的新的业务流程步骤。

> [!IMPORTANT]
> 添加新的业务流程步骤以后，请按顺序对这些步骤重新编号，不要跳过从 1 到 N 的任何整数。

1. `AppInsights-SignInRequest`作为第二个业务流程步骤调用。 此步骤将跟踪已收到注册或登录请求。

   ```xml
   <!-- Track that we have received a sign in request -->
   <OrchestrationStep Order="2" Type="ClaimsExchange">
     <ClaimsExchanges>
       <ClaimsExchange Id="TrackSignInRequest" TechnicalProfileReferenceId="AppInsights-SignInRequest" />
     </ClaimsExchanges>
   </OrchestrationStep>
   ```

1. 在 `SendClaims` 业务流程步骤之前，添加调用的新步骤 `AppInsights-UserSignup` 。 当用户在注册或登录旅程中选择 "注册" 按钮时，会触发此项。

   ```xml
   <!-- Handles the user selecting the sign-up link in the local account sign-in page -->
   <OrchestrationStep Order="8" Type="ClaimsExchange">
     <Preconditions>
       <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
         <Value>newUser</Value>
         <Action>SkipThisOrchestrationStep</Action>
       </Precondition>
       <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
         <Value>newUser</Value>
         <Value>false</Value>
         <Action>SkipThisOrchestrationStep</Action>
       </Precondition>
     </Preconditions>
     <ClaimsExchanges>
       <ClaimsExchange Id="TrackUserSignUp" TechnicalProfileReferenceId="AppInsights-UserSignup" />
     </ClaimsExchanges>
   </OrchestrationStep>
   ```

1. 在 `SendClaims` 业务流程步骤后，调用 `AppInsights-SignInComplete` 。 此步骤演示成功完成的过程。

   ```xml
   <!-- Track that we have successfully sent a token -->
   <OrchestrationStep Order="10" Type="ClaimsExchange">
     <ClaimsExchanges>
       <ClaimsExchange Id="TrackSignInComplete" TechnicalProfileReferenceId="AppInsights-SignInComplete" />
     </ClaimsExchanges>
   </OrchestrationStep>
   ```

## <a name="upload-your-file-run-the-policy-and-view-events"></a>上传文件，运行策略，并查看事件

保存并上传 TrustFrameworkExtensions.xml 文件。 然后从你的应用程序调用信赖方策略，或者使用 Azure 门户中的 " **立即运行** "。 等待事件在 Application Insights 中可用。

1. 在 Azure Active Directory 租户中打开 **Application Insights** 资源。
1. 选择 " **使用情况**"，然后选择 " **事件**"。
1. 将“期间”设置为“过去一小时”，将“截止时间”设置为“3 分钟”。 可能需要刷新窗口才能查看结果。

![显示 Application Insights 事件统计信息的屏幕截图。](./media/analytics-with-application-insights/app-ins-graphic.png)

## <a name="collect-more-data"></a>收集更多数据

为了满足你的业务需求，你可能需要记录更多的声明。 若要添加声明，请首先 [定义声明](#define-claims)，然后将声明添加到输入声明集合。 你添加到 **AppInsights-Common** 技术配置文件的声明显示在 "所有事件" 中。 你添加到特定技术配置文件的声明仅出现在该事件中。 输入声明元素包含以下属性：

- ClaimTypeReferenceId 是对声明类型的引用。
- PartnerClaimType 是 Azure Insights 中显示的属性的名称。 使用的语法 `{property:NAME}` ，其中 `NAME` 是要添加到事件中的属性。
- **DefaultValue** 是要记录的预定义值，例如事件名称。 如果用户旅程中使用的声明为空，则使用默认值。 例如， `identityProvider` 声明由联合技术配置文件（如 Facebook）设置。 如果声明为空，则表示用户使用本地帐户进行登录。 因此，默认值设置为 " **本地**"。 你还可以使用上下文值（例如应用程序 ID 或用户 IP 地址）来记录 [声明解析程序](claim-resolver-overview.md) 。

### <a name="manipulate-claims"></a>操作声明

在将输入声明发送到 Application Insights 之前，可以使用 [输入声明转换](custom-policy-trust-frameworks.md#manipulating-your-claims) 来修改输入声明或生成新声明。 在下面的示例中，技术配置文件包含 `CheckIsAdmin` 输入声明转换。

```xml
<TechnicalProfile Id="AppInsights-SignInComplete">
  <InputClaimsTransformations>  
    <InputClaimsTransformation ReferenceId="CheckIsAdmin" />
  </InputClaimsTransformations>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="isAdmin" PartnerClaimType="{property:IsAdmin}"  />
    ...
  </InputClaims>
  <IncludeTechnicalProfile ReferenceId="AppInsights-Common" />
</TechnicalProfile>
```

### <a name="add-events"></a>添加事件

若要添加事件，请创建包含技术配置文件的新技术配置文件 `AppInsights-Common` 。 然后，将新的技术配置文件作为业务流程步骤添加到 [用户旅程](custom-policy-trust-frameworks.md#orchestration-steps)。 准备就绪时，使用 [前置条件](userjourneys.md#preconditions) 元素触发事件。 例如，仅当用户通过多重身份验证运行时才报告事件。

```xml
<TechnicalProfile Id="AppInsights-MFA-Completed">
  <InputClaims>
     <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="MFA-Completed" />
  </InputClaims>
  <IncludeTechnicalProfile ReferenceId="AppInsights-Common" />
</TechnicalProfile>
```

>[!Important]
>向用户旅程添加事件时，请记得按顺序对业务流程步骤进行重新编号。

```xml
<OrchestrationStep Order="8" Type="ClaimsExchange">
  <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
    <Value>isActiveMFASession</Value>
    <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackUserMfaCompleted" TechnicalProfileReferenceId="AppInsights-MFA-Completed" />
  </ClaimsExchanges>
</OrchestrationStep>
```

## <a name="enable-developer-mode"></a>启用开发人员模式

使用 Application Insights 定义事件时，可以指示是否启用开发人员模式。 开发人员模式控制如何缓冲事件。 在事件量最少的开发环境中，启用开发人员模式会导致系统立即将事件发送到 Application Insights。 默认值为 `false`。 不要在生产环境中启用开发人员模式。

若要启用开发人员模式，请将 `DeveloperMode` `true` `AppInsights-Common` 技术配置文件中的元数据更改为：

```xml
<TechnicalProfile Id="AppInsights-Common">
  <Metadata>
    ...
    <Item Key="DeveloperMode">true</Item>
  </Metadata>
</TechnicalProfile>
```

## <a name="disable-telemetry"></a>禁用遥测

若要禁用 Application Insights 日志，请将 `DisableTelemetry` `true` 技术配置文件中的元数据更改为 `AppInsights-Common` ：

```xml
<TechnicalProfile Id="AppInsights-Common">
  <Metadata>
    ...
    <Item Key="DisableTelemetry">true</Item>
  </Metadata>
</TechnicalProfile>
```

## <a name="next-steps"></a>后续步骤

了解如何 [使用 Azure 应用程序 Insights 创建自定义 KPI 仪表板](../azure-monitor/app/tutorial-app-dashboards.md)。

::: zone-end