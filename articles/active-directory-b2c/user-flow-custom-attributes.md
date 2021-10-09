---
title: 在 Azure Active Directory B2C 中定义自定义属性 | Microsoft Docs
description: 在 Azure Active Directory B2C 中定义应用程序的自定义属性以收集有关客户的信息。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/20/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 3e3cb266e9da128e9379d6868d189ac72408c4f7
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128605822"
---
# <a name="define-custom-attributes-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中定义自定义属性

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

在[使用自定义策略添加声明和自定义用户输入](configure-user-input.md)一文中，你将了解如何使用内置[用户配置文件属性](user-profile-attributes.md)。 在本文中，你将在 Azure Active Directory B2C (Azure AD B2C) 目录中启用一个自定义属性。 稍后，你可以将该新属性同时用作多个[用户流](user-flow-overview.md)或[自定义策略](user-flow-overview.md)中的自定义声明。

Azure AD B2C 目录附带了[一组内置属性](user-profile-attributes.md)。 但是，你通常需要创建自己的属性来管理你的特定方案，例如，在以下情况下：

* 面向客户的应用程序需要持久保存 loyaltyId 属性。
* 标识提供者具有必须持久保存的唯一用户标识符 **uniqueUserGUID**。
* 自定义用户旅程需要持久保存用户的状态 **migrationStatus**，以便其他逻辑基于该状态运行。

术语扩展属性、自定义属性和自定义声明在本文的上下文中引用相同的内容。 名称会因上下文（应用程序、对象、策略）而异。

Azure AD B2C 允许你扩展存储在每个用户帐户中的属性集。 还可以使用 [Microsoft Graph API](microsoft-graph-operations.md) 读写这些属性。

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

现在自定义属性在“用户属性”列表中显示，以便在用户流中使用。 自定义属性只有在任何用户流中第一次使用时才创建，而不是将其添加到“用户属性”列表时创建。

::: zone pivot="b2c-user-flow"

## <a name="use-a-custom-attribute-in-your-user-flow"></a>在用户流中使用自定义属性

1. 在 Azure AD B2C 租户中，选择“用户流”  。
1. 选择策略（例如，“B2C_1_SignupSignin”）将其打开。
1. 选择“用户属性”，然后选择自定义属性（例如，“ShoeSize”）。 单击“保存”  。
1. 选择“应用程序声明”，然后选择自定义属性。
1. 单击“保存”。

使用用户流（其中使用了新创建的自定义属性）创建新用户后，可以在 [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) 中查询该对象。 或者，可以对用户流使用[运行用户流](./tutorial-create-user-flows.md)功能来验证客户体验。 现在可以在注册期间收集的属性的列表中看到“ShoeSize”，并在发送回应用程序的令牌中看到它。

::: zone-end

## <a name="azure-ad-b2c-extensions-app"></a>Azure AD B2C 扩展应用

尽管扩展属性可以包含用户的数据，但它们只能在应用程序对象中注册。 扩展属性会附加到名为 `b2c-extensions-app` 的应用程序。 请不要修改此应用程序，因为 Azure AD B2C 使用它来存储用户数据。 可以在 Azure AD B2C 应用注册下找到此应用程序。 

::: zone pivot="b2c-user-flow"

获取应用程序 ID：

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 请确保使用的是包含 Azure AD B2C 租户的目录。 在门户工具栏中选择“目录 + 订阅”图标。
1. 在“门户设置 | 目录+订阅”页上的“目录名称”列表中找到你的 Azure AD B2C 目录，然后选择“切换”。
1. 在左侧菜单中，选择“Azure AD B2C”。 或者，选择“所有服务”并搜索并选择“Azure AD B2C”。
1. 选择“应用注册”，然后选择“所有应用程序” 。
1. 选择 `b2c-extensions-app. Do not modify. Used by AADB2C for storing user data.` 应用程序。
1. 复制“应用程序 ID”。 示例：`11111111-1111-1111-1111-111111111111`。
 
::: zone-end

::: zone pivot="b2c-custom-policy"

获取应用程序属性：

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 请确保使用的是包含 Azure AD B2C 租户的目录。 在门户工具栏中选择“目录 + 订阅”图标。
1. 在“门户设置 | 目录+订阅”页上的“目录名称”列表中找到你的 Azure AD B2C 目录，然后选择“切换”。
1. 在左侧菜单中，选择“Azure AD B2C”。 或者，选择“所有服务”并搜索并选择“Azure AD B2C”。
1. 选择“应用注册”，然后选择“所有应用程序” 。
1. 选择 `b2c-extensions-app. Do not modify. Used by AADB2C for storing user data.` 应用程序。
1. 将以下标识符复制到剪贴板并保存：
    * **应用程序 ID**。 示例：`11111111-1111-1111-1111-111111111111`。
    * **对象 ID**。 示例：`22222222-2222-2222-2222-222222222222`。

## <a name="modify-your-custom-policy"></a>修改自定义策略

若要在策略中启用自定义属性，请在 AAD-Common 技术配置文件元数据中提供应用程序 ID 和应用程序对象 ID。 AAD-Common 技术配置文件位于基础 [Azure Active Directory](active-directory-technical-profile.md) 配置文件中，为 Azure AD 用户管理提供支持。 其他 Azure AD 技术配置文件包括了可利用其配置的 AAD-Common。 请替代扩展文件中的 AAD-Common 技术配置文件。

1. 打开策略的扩展文件， 例如，<em>`SocialAndLocalAccounts/``TrustFrameworkExtensions.xml`</em>。
1. 找到 ClaimsProviders 元素。 向 ClaimsProviders 元素添加一个新的 ClaimsProvider。
1. 在开头的 `<Item Key="ClientId">` 和结尾的 `</Item>` 元素之间插入之前记录的“应用程序 ID”。
1. 在开头的 `<Item Key="ApplicationObjectId">` 和结尾的 `</Item>` 元素之间插入之前记录的“应用程序 ObjectID”。

    ```xml
    <!-- 
    <ClaimsProviders> -->
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
    <!-- 
    </ClaimsProviders> -->
    ```

## <a name="upload-your-custom-policy"></a>上传你的自定义策略

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 确保你正在使用包含 Azure AD B2C 租户的目录。 在门户工具栏中选择“目录 + 订阅”图标。
1. 在“门户设置 | 目录+订阅”页上的“目录名称”列表中找到你的 Azure AD B2C 目录，然后选择“切换”。
1. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“应用注册” 。
1. 选择“标识体验框架”。
1. 选择“上传自定义策略”，然后上传你已更改的 TrustFrameworkExtensions.xml 策略文件。

> [!NOTE]
> Azure AD 技术配置文件首次将声明保存到目录时，它会检查自定义属性是否存在。 如果不存在，它会创建自定义属性。  

## <a name="create-a-custom-attribute-through-azure-portal"></a>通过 Azure 门户创建自定义属性

内置策略和自定义策略共享相同的扩展属性。 通过门户体验添加自定义属性时，使用每个 B2C 租户中都存在的 b2c-extensions-app 注册这些属性。

在自定义策略中使用这些属性之前或之后，你可以使用门户 UI 创建它们。 在门户中创建属性 **loyaltyId** 时，必须对其进行引用，如下所示：

|名称     |适用范围 |
|---------|---------|
|`extension_loyaltyId`  | 自定义策略|
|`extension_<b2c-extensions-app-guid>_loyaltyId`  | [Microsoft 图形 API](microsoft-graph-operations.md)|

下面的示例演示了如何在 Azure AD B2C 自定义策略声明定义中使用自定义属性。

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

下面的示例演示了如何在 Azure AD B2C 自定义策略的技术配置文件、输入声明、输出声明和持久性声明中使用自定义属性。

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

## <a name="using-custom-attribute-with-ms-graph-api"></a>将自定义属性与 MS Graph API 一起使用

Microsoft Graph API 支持使用扩展特性创建和更新用户。 Graph API 中的扩展属性使用约定 `extension_ApplicationClientID_attributename` 命名，其中的 `ApplicationClientID` 是 `b2c-extensions-app` 应用程序的应用程序（客户端）ID。 请注意，应用程序（客户端）ID 以扩展属性名称表示时不包含连字符。 例如：

```json
"extension_831374b3bd5041bfaa54263ec9e050fc_loyaltyId": "212342" 
``` 

## <a name="next-steps"></a>后续步骤

按照有关如何[使用自定义策略添加声明和自定义用户输入](configure-user-input.md)的指导进行操作。 此示例使用内置声明“city”。 若要使用自定义属性，请将“city”替换为你自己的自定义属性。
