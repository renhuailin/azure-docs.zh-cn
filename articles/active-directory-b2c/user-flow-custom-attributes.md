---
title: 在 Azure Active Directory B2C 中定义自定义属性 | Microsoft Docs
description: 在 Azure Active Directory B2C 中定义应用程序的自定义属性以收集有关客户的信息。
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
ms.openlocfilehash: 5552c93c1c65f08f70ed8929d81126035aa2a357
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/21/2021
ms.locfileid: "98661198"
---
# <a name="define-custom-attributes-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中定义自定义属性

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

在 [添加声明和使用自定义策略自定义用户输入](configure-user-input.md) 一文中，你将了解如何使用内置 [用户配置文件属性](user-profile-attributes.md)。 在本文中，将在 Azure Active Directory B2C (Azure AD B2C) 目录中启用自定义属性。 稍后，你可以将新属性同时用作 [用户流](user-flow-overview.md) 或 [自定义策略](custom-policy-get-started.md) 中的自定义声明。

Azure AD B2C 目录附带了一 [组内置属性](user-profile-attributes.md)。 但是，您通常需要创建自己的属性以管理您的特定方案，例如：

* 面向客户的应用程序需要保留 **LoyaltyId** 属性。
* 标识提供程序具有必须保持的唯一用户标识符 **uniqueUserGUID**。
* 自定义用户旅程需要保留用户的状态 **migrationStatus**，以供其他逻辑操作。

Azure AD B2C 允许扩展存储在每个用户帐户上的属性集。 还可以使用 [Microsoft Graph API](microsoft-graph-operations.md) 读写这些属性。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-a-custom-attribute"></a>创建自定义属性

1. 以 Azure AD B2C 租户的全局管理员身份登录 [Azure 门户](https://portal.azure.com/)。
1. 通过在 Azure 门户的右上角切换到包含 Azure AD B2C 租户的目录，确保你正在使用该目录。 选择订阅信息，然后选择“切换目录”。
1. 选择 Azure 门户左上角的“所有服务”  ，搜索并选择 **Azure AD B2C**。
1. 选择“用户属性”，然后选择“添加”。
1. 提供自定义属性的“名称”（例如，“ShoeSize”）
1. 选择“数据类型”。 仅“String”、“Boolean”和“Int”可供选择。
1. （可选）输入“说明”以供参考。
1. 单击“创建”。

自定义属性现已在 **用户属性** 列表中提供，并可用于用户流。 自定义属性仅在任何用户流中首次使用时创建，而不是在将其添加到 **用户属性** 列表中时创建。

::: zone pivot="b2c-user-flow"

## <a name="use-a-custom-attribute-in-your-user-flow"></a>在用户流中使用自定义属性

1. 在 Azure AD B2C 租户中，选择“用户流”  。
1. 选择策略（例如，“B2C_1_SignupSignin”）将其打开。
1. 选择“用户属性”，然后选择自定义属性（例如，“ShoeSize”）。 单击“保存”。
1. 选择“应用程序声明”，然后选择自定义属性。
1. 单击“保存”。

使用新创建的自定义属性创建新用户后，可在 [Microsoft Graph 资源管理器](https://developer.microsoft.com/graph/graph-explorer)中查询该对象。 或者，您也可以使用用户流上的 " [运行用户流](./tutorial-create-user-flows.md) " 功能来验证客户体验。 现在可以在注册期间收集的属性的列表中看到“ShoeSize”，并在发送回应用程序的令牌中看到它。

::: zone-end

## <a name="azure-ad-b2c-extensions-app"></a>Azure AD B2C 扩展应用

扩展属性只能在应用程序对象上注册，即使它们可能包含用户的数据也是如此。 扩展属性附加到名为 b2c-extensions-app 的应用程序。 请不要修改此应用程序，因为 Azure AD B2C 使用它来存储用户数据。 可以在 Azure AD B2C 应用注册下找到此应用程序。

术语扩展属性、自定义属性和自定义声明在本文的上下文中引用相同的内容。 名称会因上下文（应用程序、对象、策略）而异。

## <a name="get-the-application-properties"></a>获取应用程序属性

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 在顶部菜单中选择“目录 + 订阅”筛选器，然后选择包含Azure AD B2C 租户的目录。
1. 在左侧菜单中，选择“Azure AD B2C”。 或者，选择“所有服务”并搜索并选择“Azure AD B2C”。
1. 选择 " **应用注册**"，然后选择 " **所有应用程序**"。
1. 选择 `b2c-extensions-app. Do not modify. Used by AADB2C for storing user data.` 应用程序。
1. 将以下标识符复制到剪贴板并保存：
    * **应用程序 ID**。 示例：`11111111-1111-1111-1111-111111111111`。
    * **对象 ID**。 示例：`22222222-2222-2222-2222-222222222222`。

## <a name="using-custom-attribute-with-ms-graph-api"></a>将自定义特性用于 MS 图形 API

Microsoft Graph API 支持使用扩展特性创建和更新用户。 图形 API 中的扩展属性是使用约定命名的 `extension_ApplicationClientID_attributename` ，其中是应用程序 `ApplicationClientID` **(客户端) ID** `b2c-extensions-app` 。 请注意，应用程序（客户端）ID 以扩展属性名称表示时不包含连字符。 例如：

```json
"extension_831374b3bd5041bfaa54263ec9e050fc_loyaltyNumber": "212342"
``` 

::: zone pivot="b2c-custom-policy"

## <a name="modify-your-custom-policy"></a>修改自定义策略

若要在策略中启用自定义属性，请在 AAD-Common 技术配置文件元数据中提供 **应用程序 id** 和应用程序 **对象 id** 。 *AAD 通用* 技术配置文件位于基本 [Azure Active Directory](active-directory-technical-profile.md)技术配置文件中，并提供对 Azure AD 用户管理的支持。 其他 Azure AD 技术配置文件包含 AAD-Common 利用其配置。 覆盖扩展文件中的 AAD-Common 技术配置文件。

1. 打开策略的扩展文件， 例如，<em>`SocialAndLocalAccounts/``TrustFrameworkExtensions.xml`</em>。
1. 找到 ClaimsProviders 元素。 将新的 ClaimsProvider 添加到 ClaimsProviders 元素。
1. 替换 `ApplicationObjectId` 为之前记录的对象 ID。 然后，将替换 `ClientId` 为你之前在以下代码片段中记录的应用程序 ID。

    ```xml
    <ClaimsProvider>
      <DisplayName>Azure Active Directory</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="AAD-Common">
          <Metadata>
            <!--Insert b2c-extensions-app application ID here, for example: 11111111-1111-1111-1111-111111111111-->  
            <Item Key="ClientId"></Item>
            <!--Insert b2c-extensions-app application ObjectId here, for example: 22222222-2222-2222-2222-222222222222-->
            <Item Key="ApplicationObjectId"></Item>
          </Metadata>
        </TechnicalProfile>
      </TechnicalProfiles> 
    </ClaimsProvider>
    ```

## <a name="upload-your-custom-policy"></a>上传自定义策略

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 在顶部菜单中选择 " **目录 + 订阅** " 筛选器并选择包含 Azure AD B2C 租户的目录，确保使用的是包含 Azure AD 租户的目录。
3. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“应用注册” 。
4. 选择“标识体验框架”。
5. 选择 " **上载自定义策略**"，然后上传所更改的 TrustFrameworkExtensions.xml 策略文件。

> [!NOTE]
> 第一次 Azure AD 技术配置文件将声明保留在目录中时，它会检查自定义属性是否存在。 如果不是，则创建自定义属性。  

## <a name="create-a-custom-attribute-through-azure-portal"></a>通过 Azure 门户创建自定义属性

在内置策略和自定义策略之间共享相同的扩展属性。 通过门户体验添加自定义属性时，这些属性是使用每个 B2C 租户中存在的 **b2c 扩展应用** 注册的。

你可以通过在自定义策略中使用门户 UI 前后使用门户 UI 来创建这些属性。 在门户中创建属性 **loyaltyId** 时，必须按如下所示进行引用：

|名称     |适用范围 |
|---------|---------|
|`extension_loyaltyId`  | 自定义策略|
|`extension_<b2c-extensions-app-guid>_loyaltyId`  | [Microsoft 图形 API](microsoft-graph-operations.md)|

下面的示例演示如何在 Azure AD B2C 自定义策略声明定义中使用自定义特性。

```xml
<BuildingBlocks>
  <ClaimsSchema>
    <ClaimType Id="extension_loyaltyId">
      <DisplayName>Loyalty Identification</DisplayName>
      <DataType>string</DataType>
      <UserHelpText>Your loyalty number from your membership card</UserHelpText>
      <UserInputType>TextBox</UserInputType>
    </ClaimType>
  </ClaimsSchema>
</BuildingBlocks>
```

下面的示例演示如何使用自定义属性，该属性在技术配置文件、输入、输出和持久化声明中 Azure AD B2C 自定义策略中。

```xml
<InputClaims>
  <InputClaim ClaimTypeReferenceId="extension_loyaltyId"  />
</InputClaims>
<PersistedClaims>
  <PersistedClaim ClaimTypeReferenceId="extension_loyaltyId" />
</PersistedClaims>
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="extension_loyaltyId" />
</OutputClaims>
```

::: zone-end

## <a name="next-steps"></a>后续步骤

按照有关如何 [使用自定义策略添加声明和自定义用户输入](configure-user-input.md)的指导进行操作。 此示例使用内置声明 "city"。 若要使用自定义属性，请将 "city" 替换为你自己的自定义属性。