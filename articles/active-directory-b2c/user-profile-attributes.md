---
title: Azure Active Directory B2C 中的用户配置文件属性
description: 了解 Azure AD B2C 目录用户配置文件支持的用户资源类型属性。 了解内置属性、扩展以及属性如何映射到 Microsoft Graph。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 06/16/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 76a7fa3cfeafa20dd81f043d67388bdfb281b6d4
ms.sourcegitcommit: f3b930eeacdaebe5a5f25471bc10014a36e52e5e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/16/2021
ms.locfileid: "112232514"
---
# <a name="user-profile-attributes"></a>用户配置文件属性

Azure Active Directory (Azure AD) B2C 目录用户配置文件附带了一组内置属性，例如名字、姓氏、城市、邮政编码和电话号码。 可以使用自己的应用程序数据来扩展用户配置文件，而无需外部数据存储。

可用于 Azure AD B2C 用户配置文件的大多数属性也受 Microsoft Graph 的支持。 本文介绍了支持的 Azure AD B2C 用户配置文件属性。 本文还说明了 Microsoft Graph 不支持的属性，以及不应该用于 Azure AD B2C 的 Microsoft Graph 属性。

> [!IMPORTANT]
> 不应使用内置或扩展属性来存储敏感的个人数据，例如帐户凭据、政府身份证号、持卡人数据、财务帐户数据、医疗保健信息或敏感背景信息。

还可以与外部系统相集成。 例如，可以使用 Azure AD B2C 进行身份验证，但将权限委托给用作客户数据的权威来源的外部客户关系管理 (CRM) 或客户忠诚度数据库。 有关详细信息，请参阅[远程配置文件](https://github.com/azure-ad-b2c/samples/tree/master/policies/remote-profile)解决方案。

## <a name="azure-ad-user-resource-type"></a>Azure AD 用户资源类型

下表列出了 Azure AD B2C 目录用户配置文件支持的[用户资源类型](/graph/api/resources/user)属性。 其中提供了有关每个属性的以下信息：

- Azure AD B2C 使用的属性名称（如果不同，后面的括号中会包含 Microsoft Graph 名称）
- 属性数据类型
- 属性说明
- 该属性是否可在 Azure 门户中可用
- 该属性是否可在用户流中使用
- 该属性是否可在自定义策略 [Azure AD 技术配置文件](active-directory-technical-profile.md)中使用，以及可在哪个部分（&lt;InputClaims&gt;、&lt;OutputClaims&gt; 或 &lt;PersistedClaims&gt;）中使用

|名称     |类型     |说明|Azure 门户|用户流|自定义策略|
|---------|---------|----------|------------|----------|-------------|
|accountEnabled  |Boolean|用户帐户是已启用还是已禁用：如果已启用帐户，则为“true”，否则为“false” 。|是|否|持久化、输出|
|ageGroup        |字符串|用户的年龄组。 可能的值：null、Undefined、Minor、Adult、NotAdult。|是|否|持久化、输出|
|alternativeSecurityId（[标识](#identities-attribute)）|字符串|来自外部标识提供者的单个用户标识。|否|否|输入、持久化、输出|
|alternativeSecurityIds（[标识](#identities-attribute)）|备用 securityId 集合|来自外部标识提供者的用户标识集合。|否|否|持久化、输出|
|city            |字符串|用户所在的城市。 最大长度为 128。|是|是|持久化、输出|
|consentProvidedForMinor|字符串|是否为未成年人提供了许可。 允许的值：null、granted、denied 或 notRequired。|是|否|持久化、输出|
|country         |字符串|用户所在的国家/地区。 示例：“US”或“UK”。 最大长度为 128。|是|是|持久化、输出|
|createdDateTime|DateTime|创建用户对象的日期。 只读。|否|否|持久化、输出|
|creationType    |字符串|如果用户帐户是作为 Azure Active Directory B2C 租户的本地帐户创建的，则值为 LocalAccount 或 nameCoexistence。 只读。|否|否|持久化、输出|
|dateOfBirth     |Date|出生日期。|否|否|持久化、输出|
|department      |字符串|用户所在部门的名称。 最大长度为 64。|是|否|持久化、输出|
|displayName     |字符串|用户的显示名称。 最大长度为 256。|是|是|持久化、输出|
|facsimileTelephoneNumber<sup>1</sup>|字符串|用户的业务传真号码。|是|否|持久化、输出|
|givenName       |字符串|用户的名字（名）。 最大长度为 64。|是|是|持久化、输出|
|jobTitle        |字符串|用户的职务。 最大长度为 128。|是|是|持久化、输出|
|immutableId     |字符串|通常用于从本地 Active Directory 迁移的用户的标识符。|否|否|持久化、输出|
|legalAgeGroupClassification|字符串|法定年龄组分类。 基于 ageGroup 和 consentProvidedForMinor 属性计算的只读属性。 允许的值：null、minorWithOutParentalConsent、minorWithParentalConsent、minorNoParentalConsentRequired、notAdult 和 adult。|是|否|持久化、输出|
|legalCountry<sup>1</sup>  |字符串|用于法律目的的国家/地区。|否|否|持久化、输出|
|mailNickName    |字符串|用户的邮件别名。 最大长度为 64。|否|否|持久化、输出|
|mobile (mobilePhone) |字符串|用户的主要手机号码。 最大长度为 64。|是|否|持久化、输出|
|netId           |字符串|网络 ID。|否|否|持久化、输出|
|objectId        |字符串|全局唯一标识符 (GUID)（用户的唯一标识符）。 示例：12345678-9abc-def0-1234-56789abcde。 只读，不可变。|只读|是|输入、持久化、输出|
|otherMails      |字符串集合|用户的其他电子邮件地址的列表。 示例：[“bob@contoso.com”、“Robert@fabrikam.com”]。 注意：不允许使用重音字符。|是（备用电子邮件）|否|持久化、输出|
|password        |字符串|创建用户期间用于本地帐户的密码。|否|否|持久化|
|passwordPolicies     |字符串|密码策略。 它是由逗号分隔的不同策略名称构成的字符串。 例如，“DisablePasswordExpiration, DisableStrongPassword”。|否|否|持久化、输出|
|physicalDeliveryOfficeName (officeLocation)|字符串|用户营业地点的办公室位置。 最大长度为 128。|是|否|持久化、输出|
|postalCode      |字符串|用户邮政地址的邮政编码。 该邮政编码特定于用户所在的国家/地区。 在美国，此属性包含邮政编码。 最大长度为 40。|是|否|持久化、输出|
|preferredLanguage    |字符串|用户的首选语言。 首选语言格式基于 RFC 4646。 名称是一个与语言相关的 ISO 639 双小写字母的区域性代码和一个与国家或地区相关的 ISO 3166 双大写字母子区域性代码的组合。 例如：“en-US”或“es-ES”。|否|否|持久化、输出|
|refreshTokensValidFromDateTime (signInSessionsValidFromDateTime)|DateTime|在此时间之前颁发的所有刷新令牌无效，使用无效刷新令牌获取新的访问令牌时，应用程序会收到错误。 如果发生这种情况，应用程序需要通过向授权终结点发出请求来获取新的刷新令牌。 只读。|否|否|输出|
|signInNames（[标识](#identities-attribute)） |字符串|目录中任何类型的本地帐户用户的唯一登录名。 使用此属性可以获取具有登录值的用户，而无需指定本地帐户类型。|否|否|输入|
|signInNames.userName（[标识](#identities-attribute)） |字符串|目录中本地帐户用户的唯一用户名。 使用此属性可以创建或获取具有特定登录用户名的用户。 在执行修补操作期间，在 PersistedClaims 中单独指定此属性会删除其他 signInNames 类型。 若要添加新的 signInNames 类型，还需要保存现有的 signInNames。 注意：用户名中不允许使用重音字符。|否|否|输入、持久化、输出|
|signInNames.phoneNumber（[标识](#identities-attribute)） |字符串|目录中本地帐户用户的唯一电话号码。 使用此属性可以创建或获取具有特定登录电话号码的用户。 在执行 Patch 操作期间，在 PersistedClaims 中单独指定此属性会删除其他类型的 signInNames。 若要添加新的 signInNames 类型，还需要保存现有的 signInNames。|否|否|输入、持久化、输出|
|signInNames.emailAddress（[标识](#identities-attribute)）|字符串|目录中本地帐户用户的唯一电子邮件地址。 使用此属性可以创建或获取具有特定登录电子邮件地址的用户。 在执行 Patch 操作期间，在 PersistedClaims 中单独指定此属性会删除其他类型的 signInNames。 若要添加新的 signInNames 类型，还需要保存现有的 signInNames。|否|否|输入、持久化、输出|
|state           |字符串|用户地址中的省/自治区/直辖市。 最大长度为 128。|是|是|持久化、输出|
|streetAddress   |字符串|用户营业地点的街道地址。 最大长度为 1024。|是|是|持久化、输出|
|strongAuthentication AlternativePhoneNumber<sup>1</sup>|字符串|用户的次要电话号码，用于多重身份验证。|是|否|持久化、输出|
|strongAuthenticationEmailAddress<sup>1</sup>|字符串|用户的 SMTP 地址。 示例：“bob@contoso.com”。此属性在通过用户名策略进行登录时用于存储用户电子邮件地址。 然后，该电子邮件地址将在密码重置流中使用。 此属性中不允许使用重音字符。|是|否|持久化、输出|
|strongAuthenticationPhoneNumber<sup>2</sup>|字符串|用户的主要电话号码，用于多重身份验证。|是|否|持久化、输出|
|surname         |字符串|用户的姓（家族名或姓氏）。 最大长度为 64。|是|是|持久化、输出|
|telephoneNumber（businessPhones 的第一个条目）|字符串|用户营业地点的主要电话号码。|是|否|持久化、输出|
|userPrincipalName    |字符串|用户的用户主体名称 (UPN)。 UPN 是用户的 Internet 样式登录名，基于 Internet 标准 RFC 822。 域必须存在于租户的已验证域集合中。 创建帐户时必须使用此属性。 不可变。|否|否|输入、持久化、输出|
|usageLocation   |字符串|这对由于法律要求要向其分配许可证的用户而言为必填项，以检查服务在国家/地区中的可用性。 不可为 Null。 双字母国家/地区代码（ISO 标准 3166）。 示例：示例:“US”、“JP”和“GB”。|是|否|持久化、输出|
|userType        |字符串|一个字符串值，可用于分类目录中的用户类型。 值必须为 Member。 只读。|只读|否|持久化、输出|
|userState (externalUserState)<sup>3</sup>|字符串|仅适用于 Azure AD B2B 帐户，指示邀请是 PendingAcceptance 还是 Accepted。|否|否|持久化、输出|
|userStateChangedOn (externalUserStateChangeDateTime)<sup>2</sup>|DateTime|显示 UserState 属性最新更改的时间戳。|否|否|持久化、输出|

<sup>1 </sup>不受 Microsoft Graph 支持<br><sup>2 </sup>有关详细信息，请参阅 [MFA 电话号码属性](#mfa-phone-number-attribute)<br><sup>3</sup> 不应该用于 Azure AD B2C

## <a name="required-attributes"></a>必需属性

若要在 Azure AD B2C 目录中创建用户帐户，请提供以下必需属性： 

- [显示名称](#display-name-attribute)

- [标识](#display-name-attribute) - 至少有一个实体（本地帐户或联合帐户）。

- [密码配置文件](#password-policy-attribute) - 如果创建本地帐户，请提供密码配置文件。

## <a name="display-name-attribute"></a>显示名称属性

`displayName` 是要在用户的 Azure 门户用户管理界面中显示的，以及要在 Azure AD B2C 返回给应用程序的访问令牌中显示的名称。 此属性是必需项。

## <a name="identities-attribute"></a>标识属性

客户帐户（可以是使用者、合作伙伴或居民）可与以下标识类型相关联：

- **本地** 标识 - 将用户名和密码存储在 Azure AD B2C 目录本地。 我们通常将此类标识称为“本地帐户”。
- 联合标识 - 也称为社交或企业帐户 ，该用户标识由 Facebook、Microsoft、ADFS 或 Salesforce 等联合标识提供者进行管理。

具有客户帐户的用户可以使用多个标识进行登录。 例如，使用用户名、电子邮件、员工 ID、政府 ID 等。 一个帐户可以有多个密码相同的本地和社交标识。

在 Microsoft Graph API 中，本地标识和联合标识都存储在 [objectIdentity](/graph/api/resources/objectidentity) 类型的用户 `identities` 特性中。 `identities` 集合表示用于登录到用户帐户的一组标识。 此集合使用户能够使用其关联的任何标识登录到用户帐户。 identities 属性最多可以包含十个 [objectIdentity](/graph/api/resources/objectidentity) 对象。 每个对象包含以下属性：

| 名称   | 类型 |说明|
|:---------------|:--------|:----------|
|signInType|string| 指定目录中的用户登录类型。 对于本地帐户：`emailAddress`、`emailAddress1`、`emailAddress2`、`emailAddress3`、`userName`，或所需的任何其他类型。 社交帐户必须设置为 `federated`。|
|颁发者|string|指定标识的颁发者。 对于本地帐户（其 **signInType** 不是 `federated`），此属性是本地 B2C 租户的默认域名，例如 `contoso.onmicrosoft.com`。 对于社交标识（其中 signInType 为 `federated`），该值是颁发者的名称，例如 `facebook.com`|
|issuerAssignedId|string|指定由颁发者分配给用户的唯一标识符。 **issuer** 和 **issuerAssignedId** 的组合在租户中必须唯一。 对于本地帐户，当 **signInType** 设置为 `emailAddress` 或 `userName` 时，它表示用户的登录名。<br>如果 **signInType** 设置为： <ul><li>`emailAddress`（或以 `emailAddress` 开头，例如 `emailAddress1`），则 **issuerAssignedId** 必须是有效的电子邮件地址</li><li>`userName`（或任何其他值），则 **issuerAssignedId** 必须是有效的 [电子邮件地址本地部分](https://tools.ietf.org/html/rfc3696#section-3)</li><li>`federated`，则 **issuerAssignedId** 表示联合帐户唯一标识符</li></ul>|

以下 Identities 属性包含一个本地帐户标识、一个电子邮件地址和一个社交标识，它们均可用作登录名。

```json
"identities": [
  {
    "signInType": "userName",
    "issuer": "contoso.onmicrosoft.com",
    "issuerAssignedId": "johnsmith"
  },
  {
    "signInType": "emailAddress",
    "issuer": "contoso.onmicrosoft.com",
    "issuerAssignedId": "jsmith@yahoo.com"
  },
  {
    "signInType": "federated",
    "issuer": "facebook.com",
    "issuerAssignedId": "5eecb0cd"
  }
]
```

对于联合标识，根据标识提供者，**issuerAssignedId** 是每个应用程序的给定用户或开发帐户的唯一值。 使用社交网络提供商以前分配的相同应用程序 ID 或者同一开发帐户中的另一应用程序配置 Azure AD B2C 策略。

## <a name="password-profile-property"></a>密码配置文件属性

对于本地标识，passwordProfile 属性是必需的，其中包含用户的密码。 `forceChangePasswordNextSignIn` 属性指示用户在下次登录时是否必须重置密码。 若要处理强制密码重置，请[设置强制密码重置流](force-password-reset.md)。

对于联合（社交）标识，passwordProfile 属性不是必需的。

```json
"passwordProfile" : {
    "password": "password-value",
    "forceChangePasswordNextSignIn": false
  }
```

## <a name="password-policy-attribute"></a>密码策略属性

Azure AD B2C 密码策略（对于本地帐户）基于 Azure Active Directory [强密码强度](../active-directory/authentication/concept-sspr-policy.md)策略。 Azure AD B2C 的注册或登录和密码重置策略要求实施此强密码强度，并且不能让密码过期。

在用户迁移方案中，如果与 Azure AD B2C 强制实施的[强密码强度](../active-directory/authentication/concept-sspr-policy.md)相比，要迁移的帐户的密码强度更弱，则你可以禁用强密码要求。 若要更改默认密码策略，请将 `passwordPolicies` 属性设置为 `DisableStrongPassword`。 例如，可按如下所示修改创建用户请求：

```json
"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"
```

## <a name="mfa-phone-number-attribute"></a>MFA 电话号码属性

在使用电话进行多重身份验证 (MFA) 时，移动电话会用来验证用户标识。 若要采用编程方式[添加](/graph/api/authentication-post-phonemethods)新电话号码，或者要[更新](/graph/api/b2cauthenticationmethodspolicy-update)、[获取](/graph/api/b2cauthenticationmethodspolicy-get)或[删除](/graph/api/phoneauthenticationmethod-delete)电话号码，请使用 MS Graph API [电话身份验证方法](/graph/api/resources/phoneauthenticationmethod)。

在 Azure AD B2C [自定义策略](custom-policy-overview.md)中，可通过 `strongAuthenticationPhoneNumber` 声明类型获取电话号码。

## <a name="extension-attributes"></a>扩展属性

每个面向客户的应用程序对要收集的信息都有独特的要求。 Azure AD B2C 租户附带了一组存储在属性中的内置信息，例如名字、姓氏和邮政编码。 使用 Azure AD B2C 可以扩展存储在每个客户帐户中的属性集。 有关详细信息，请参阅[在 Azure Active Directory B2C 中添加用户属性和自定义用户输入](configure-user-input.md)

扩展属性[扩展](/graph/extensibility-overview#schema-extensions)目录中用户对象的架构。 尽管扩展属性可以包含用户的数据，但它们只能在应用程序对象中注册。 扩展属性会附加到名为 `b2c-extensions-app` 的应用程序。 请不要修改此应用程序，因为 Azure AD B2C 使用它来存储用户数据。 可在 Azure Active Directory 应用注册下找到此应用程序。

> [!NOTE]
> - 最多可将 100 个扩展属性写入任一用户帐户。
> - 如果删除 b2c-extensions-app 应用程序，则将删除所有用户的这些扩展属性及其包含的任何数据。
> - 如果应用程序删除了某个扩展属性，则将从所有用户帐户中删除该属性，同时会删除值。

图形 API 中的扩展属性使用约定 `extension_ApplicationClientID_AttributeName` 命名，其中 `ApplicationClientID` 是 `b2c-extensions-app` 应用程序的应用程序（客户端）ID（可在 Azure 门户中的“应用注册” > “所有应用”中找到）  。 请注意，应用程序（客户端）ID 以扩展属性名称表示时不包含连字符。 例如：

```json
"extension_831374b3bd5041bfaa54263ec9e050fc_loyaltyNumber": "212342"
```

在架构扩展中定义属性时，支持以下数据类型：

|类型 |备注  |
|--------------|---------|
|Boolean    | 可能的值：“true”或“false” 。 |
|DateTime   | 必须以 ISO 8601 格式指定。 将以 UTC 格式存储。   |
|Integer    | 32 位值。               |
|字符串     | 最多 256 个字符。     |

## <a name="next-steps"></a>后续步骤

了解有关扩展属性的详细信息：

- [架构扩展](/graph/extensibility-overview#schema-extensions)
- [定义自定义属性](user-flow-custom-attributes.md)
