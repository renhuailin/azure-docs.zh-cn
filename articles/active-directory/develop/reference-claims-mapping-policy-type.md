---
title: 声明映射策略
titleSuffix: Microsoft identity platform
description: 了解声明映射策略类型，它用于修改为特定应用程序颁发的令牌中发出的声明。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: reference
ms.date: 07/01/2021
ms.author: ryanwi
ms.reviewer: paulgarn, hirsin, jeedes, luleon
ms.openlocfilehash: 52de257461c796f7a477091801f4bdc52f46f5c8
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123101527"
---
# <a name="claims-mapping-policy-type"></a>声明映射策略类型

在 Azure AD 中，**策略** 对象表示针对组织中的单个应用程序或所有应用程序强制实施的一组规则。 每种类型的策略都有一个唯一的结构，其中的一组属性将应用于它们所分配到的对象。

声明映射策略是某种类型的策略对象，它[修改为特定应用程序颁发的令牌中发出的声明](active-directory-claims-mapping.md)。

## <a name="claim-sets"></a>声明集

有一些可定义如何以及何时在令牌中使用它们的特定声明集。

| 声明集 | 说明 |
|---|---|
| 核心声明集 | 存在于每个令牌中，与策略无关。 这些声明也被视为受限制的，无法修改。 |
| 基本声明集 | 包括默认情况下为令牌发出的声明（除了核心声明集之外）。 可以使用声明映射策略来[省略或修改基本声明](active-directory-claims-mapping.md#omit-the-basic-claims-from-tokens)。 |
| 受限声明集 | 无法使用策略进行修改。 无法更改数据源，并且在生成这些声明时不应用任何转换。 |

### <a name="table-1-json-web-token-jwt-restricted-claim-set"></a>表 1：JSON Web 令牌 (JWT) 受限制声明集

| 声明类型（名称） |
| ----- |
| _claim_names |
| _claim_sources |
| access_token |
| account_type |
| acr |
| actor |
| actortoken |
| aio |
| altsecid |
| amr |
| app_chain |
| app_displayname |
| app_res |
| appctx |
| appctxsender |
| appid |
| appidacr |
| assertion |
| at_hash |
| aud |
| auth_data |
| auth_time |
| authorization_code |
| azp |
| azpacr |
| c_hash |
| ca_enf |
| cc |
| cert_token_use |
| client_id |
| cloud_graph_host_name |
| cloud_instance_name |
| cnf |
| code |
| controls |
| credential_keys |
| csr |
| csr_type |
| deviceid |
| dns_names |
| domain_dns_name |
| domain_netbios_name |
| e_exp |
| 电子邮件 |
| endpoint |
| enfpolids |
| exp |
| expires_on |
| grant_type |
| graph |
| group_sids |
| groups |
| hasgroups |
| hash_alg |
| home_oid |
| `http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationinstant` |
| `http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod` |
| `http://schemas.microsoft.com/ws/2008/06/identity/claims/expiration` |
| `http://schemas.microsoft.com/ws/2008/06/identity/claims/expired` |
| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` |
| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` |
| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` |
| iat |
| identityprovider |
| idp |
| in_corp |
| instance |
| ipaddr |
| isbrowserhostedapp |
| iss |
| jwk |
| key_id |
| key_type |
| mam_compliance_url |
| mam_enrollment_url |
| mam_terms_of_use_url |
| mdm_compliance_url |
| mdm_enrollment_url |
| mdm_terms_of_use_url |
| nameid |
| nbf |
| netbios_name |
| nonce |
| oid |
| on_prem_id |
| onprem_sam_account_name |
| onprem_sid |
| openid2_id |
| password |
| polids |
| pop_jwk |
| preferred_username |
| previous_refresh_token |
| primary_sid |
| puid |
| pwd_exp |
| pwd_url |
| redirect_uri |
| refresh_token |
| refreshtoken |
| request_nonce |
| resource |
| 角色 (role) |
| 角色 |
| scope |
| scp |
| sid |
| 签名 |
| signin_state |
| src1 |
| src2 |
| sub |
| tbid |
| tenant_display_name |
| tenant_region_scope |
| thumbnail_photo |
| tid |
| tokenAutologonEnabled |
| trustedfordelegation |
| unique_name |
| upn |
| user_setting_sync_url |
| username |
| uti |
| ver |
| verified_primary_email |
| verified_secondary_email |
| wids |
| win_ver |

### <a name="table-2-saml-restricted-claim-set"></a>表 2：SAML 受限制声明集

| 声明类型 (URI) |
| ----- |
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/expiration`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/expired`|
|`http://schemas.microsoft.com/identity/claims/accesstoken`|
|`http://schemas.microsoft.com/identity/claims/openid2_id`|
|`http://schemas.microsoft.com/identity/claims/identityprovider`|
|`http://schemas.microsoft.com/identity/claims/objectidentifier`|
|`http://schemas.microsoft.com/identity/claims/puid`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier [MR1]`|
|`http://schemas.microsoft.com/identity/claims/tenantid`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationinstant`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod`|
|`http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/groups`|
|`http://schemas.microsoft.com/claims/groups.link`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/role`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/wids`|
|`http://schemas.microsoft.com/2014/09/devicecontext/claims/iscompliant`|
|`http://schemas.microsoft.com/2014/02/devicecontext/claims/isknown`|
|`http://schemas.microsoft.com/2012/01/devicecontext/claims/ismanaged`|
|`http://schemas.microsoft.com/2014/03/psso`|
|`http://schemas.microsoft.com/claims/authnmethodsreferences`|
|`http://schemas.xmlsoap.org/ws/2009/09/identity/claims/actor`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/samlissuername`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/confirmationkey`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/primarygroupsid`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/authorizationdecision`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/authentication`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/sid`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/denyonlyprimarygroupsid`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/denyonlyprimarysid`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/denyonlysid`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/denyonlywindowsdevicegroup`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsdeviceclaim`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsdevicegroup`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsfqbnversion`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/windowssubauthority`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsuserclaim`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/x500distinguishedname`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/ispersistent`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/privatepersonalidentifier`|
|`http://schemas.microsoft.com/identity/claims/scope`|

## <a name="claims-mapping-policy-properties"></a>声明映射策略属性

若要控制要发出的声明以及数据的来源，请使用声明映射策略的属性。 如果未设置策略，则系统将颁发包括核心声明集、基本声明集以及应用程序已选择接收的任何[可选声明](active-directory-optional-claims.md)的令牌。

> [!NOTE]
> 核心声明集中的声明存在于每个令牌中（与此属性的设置无关）。

### <a name="include-basic-claim-set"></a>包括基本声明集

**字符串：** IncludeBasicClaimSet

**数据类型：** 布尔值（True 或 False）

**摘要：** 此属性确定是否在受此策略影响的令牌中包含基本声明集。

- 如果设置为 True，则会在受策略影响的令牌中发出基本声明集中的所有声明。
- 如果设置为 False，基本声明集中的声明不包含在令牌中，除非在相同策略的声明架构属性中单独添加它们。



### <a name="claims-schema"></a>声明架构

**字符串：** ClaimsSchema

**数据类型：** 具有一个或多个声明架构条目的 JSON Blob

**摘要：** 此属性定义除了基本声明集与核心声明集之外，在受此策略影响的令牌中存在的声明。
对于此属性中定义的每个声明架构条目，都需要特定信息。 指定数据来源（“Value”、“Source/ID 对”或“Source/ExtensionID 对”）以及数据作为哪种声明发出（声明类型）。

### <a name="claim-schema-entry-elements"></a>声明架构条目元素

**Value：** Value 元素将静态值定义为要在声明中发出的数据。

**Source/ID 对：** Source 和 ID 元素定义声明中的数据的来源。

**Source/ExtensionID 对：** Source 元素和 ExtensionID 元素定义声明中的数据源自的目录架构扩展属性。 有关详细信息，请参阅[在声明中使用目录架构扩展属性](active-directory-schema-extensions.md)。

将 Source 元素设置为下列值之一：

- "user"：声明中的数据是 User 对象的属性。
- "application"：声明中的数据是应用程序（客户端）服务主体的属性。
- "resource"：声明中的数据是资源服务主体的属性。
- "audience"：声明中的数据是作为令牌受众的服务主体（客户端或资源服务主体）的属性。
- "company"：声明中的数据是资源租户的 Company 对象的属性。
- "transformation"：声明中的数据来自声明转换（请参阅本文后面的“声明转换”部分）。

如果 Source 是 transformation，则 **TransformationID** 元素也必须包含在此声明定义中。

ID 元素标识源中用于为声明提供值的属性。 下表列出对 Source 的每个值有效的 ID 值。

> [!WARNING]
> 目前，用户对象上唯一可用的多值声明源是已从 AADConnect 同步的多值扩展属性。  OtherMail 和 tags 等其他属性是多值属性，但选择作为源时仅发出一个值。

#### <a name="table-3-valid-id-values-per-source"></a>表 3：每个 Source 的有效 ID 值

| 源 | ID | 说明 |
|-----|-----|-----|
| 用户 | surname | 家族名称 |
| 用户 | givenname | 名 |
| 用户 | displayname | 显示名称 |
| 用户 | objectid | ObjectID |
| 用户 | mail | 电子邮件地址 |
| 用户 | userprincipalname | 用户主体名称 |
| 用户 | department|部门|
| 用户 | onpremisessamaccountname | 本地 SAM 帐户名称 |
| 用户 | netbiosname| NetBios 名称 |
| 用户 | dnsdomainname | DNS 域名 |
| 用户 | onpremisesecurityidentifier | 本地安全标识符 |
| 用户 | companyname| 组织名称 |
| 用户 | streetaddress | 街道地址 |
| 用户 | postalcode | 邮政编码 |
| 用户 | preferredlanguage | 首选语言 |
| 用户 | onpremisesuserprincipalname | 本地 UPN |
| 用户 | mailNickname | 邮件别名 |
| 用户 | extensionattribute1 | 扩展属性 1 |
| 用户 | extensionattribute2 | 扩展属性 2 |
| 用户 | extensionattribute3 | 扩展属性 3 |
| 用户 | extensionattribute4 | 扩展属性 4 |
| 用户 | extensionattribute5 | 扩展属性 5 |
| 用户 | extensionattribute6 | 扩展属性 6 |
| 用户 | extensionattribute7 | 扩展属性 7 |
| 用户 | extensionattribute8 | 扩展属性 8 |
| 用户 | extensionattribute9 | 扩展属性 9 |
| 用户 | extensionattribute10 | 扩展属性 10 |
| 用户 | extensionattribute11 | 扩展属性 11 |
| 用户 | extensionattribute12 | 扩展属性 12 |
| 用户 | extensionattribute13 | 扩展属性 13 |
| 用户 | extensionattribute14 | 扩展属性 14 |
| 用户 | extensionattribute15 | 扩展属性 15 |
| 用户 | othermail | 其他邮件 |
| 用户 | country | 国家/地区 |
| 用户 | city | 城市 |
| 用户 | state | 状态 |
| 用户 | jobtitle | 职务 |
| 用户 | employeeid | 员工 ID |
| 用户 | facsimiletelephonenumber | 传真电话号码 |
| User | assignedroles | 分配给用户的应用角色列表|
| application、resource、audience | displayname | 显示名称 |
| application、resource、audience | objectid | ObjectID |
| application、resource、audience | 标记 | 服务主体标记 |
| Company | tenantcountry | 租户的国家/地区 |

**TransformationID：** 仅当 Source 元素设置为“transformation”时，才必须提供 TransformationID 元素。

- 此元素必须与 **ClaimsTransformation** 属性（定义如何生成此声明的数据）中的转换条目的 ID 元素匹配。

**声明类型：** **JwtClaimType** 和 **SamlClaimType** 元素定义此声明架构条目引用的声明。

- JwtClaimType 必须包含要在 JWT 中发出的声明的名称。
- SamlClaimType 必须包含要在 SAML 令牌中发出的声明的 URI。

* **onPremisesUserPrincipalName attribute：** 使用替代 ID 时，本地属性 userPrincipalName 将与 Azure AD 属性 onPremisesUserPrincipalName 同步。 此属性仅在以下情况下可用：备用 ID 已配置，但也可通过 MS Graph Beta (https://graph.microsoft.com/beta/me/ ) 获取。

> [!NOTE]
> 受限声明集中的声明的 Name 和 URI 不能用于声明类型元素。 有关详细信息，请参阅本文后面的“例外和限制”部分。

### <a name="claims-transformation"></a>声明转换

**字符串：** ClaimsTransformation

**数据类型：** 具有一个或多个转换条目的 JSON Blob

**摘要：** 使用此属性可将常见转换应用于源数据，以便为声明架构中指定的声明生成输出数据。

**ID：** 使用 ID 元素可在 TransformationID 声明架构条目中引用此转换条目。 就此策略中的每个转换条目来说，该值必须唯一。

**TransformationMethod：** TransformationMethod 元素用于标识为生成声明的数据而执行的操作。

根据选择的方法，需要一组输入和输出。 使用 **InputClaims**、**InputParameters** 和 **OutputClaims** 元素定义输入和输出。

#### <a name="table-4-transformation-methods-and-expected-inputs-and-outputs"></a>表 4：转换方法以及预期输入和输出

|TransformationMethod|预期输入|预期输出|说明|
|-----|-----|-----|-----|
|联接|string1、string2、分隔符|outputClaim|联接输入字符串（之间使用分隔符）。 例如：string1：“foo@bar.com”、string2：“sandbox”、separator：“.”会生成 outputClaim：“foo@bar.com.sandbox”|
|ExtractMailPrefix|电子邮件或 UPN|提取的字符串|ExtensionAttributes 1-15 或为用户（例如 johndoe@contoso.com）存储 UPN 或电子邮件地址值的任何其他架构扩展。 提取电子邮件地址的本地部分。 例如：mail：“foo@bar.com”会生成 outputClaim：“foo”。 如果未提供 \@ 符号，则按原样返回原始输入字符串。|

**InputClaims：** 使用 InputClaims 元素可将数据从声明架构条目传递给转换。 它具有两个属性：**ClaimTypeReferenceId** 和 **TransformationClaimType**。

- **ClaimTypeReferenceId** 与声明架构条目的 ID 元素联接在一起可查找相应的输入声明。
- **TransformationClaimType** 用于向此输入提供唯一名称。 此名称必须与转换方法的预期输入之一匹配。

**InputParameters：** 使用 InputParameters 元素可将常数值传递给转换。 它具有两个属性：**Value** 和 **ID**。

- **Value** 是要传递的实际常数值。
- **ID** 用于为输入提供唯一名称。 此名称必须与转换方法的预期输入之一匹配。

**OutputClaims：** 使用 OutputClaims 元素可保存转换生成的数据，并将它绑定到声明架构条目。 它具有两个属性：**ClaimTypeReferenceId** 和 **TransformationClaimType**。

- **ClaimTypeReferenceId** 与声明架构条目的 ID 联接在一起可查找相应的输出声明。
- **TransformationClaimType** 用于为输出提供唯一名称。 此名称必须与转换方法的预期输出之一匹配。

### <a name="exceptions-and-restrictions"></a>例外和限制

**SAML NameID 和 UPN：** NameID 和 UPN 值所源自的属性以及允许使用的声明转换会受到限制。 请参阅表 5 和表 6 来查看允许的值。

#### <a name="table-5-attributes-allowed-as-a-data-source-for-saml-nameid"></a>表 5：允许作为 SAML NameID 数据源的属性

|源|ID|说明|
|-----|-----|-----|
| 用户 | mail|电子邮件地址|
| 用户 | userprincipalname|用户主体名称|
| 用户 | onpremisessamaccountname|本地 Sam 帐户名称|
| 用户 | employeeid|员工 ID|
| 用户 | extensionattribute1 | 扩展属性 1 |
| 用户 | extensionattribute2 | 扩展属性 2 |
| 用户 | extensionattribute3 | 扩展属性 3 |
| 用户 | extensionattribute4 | 扩展属性 4 |
| 用户 | extensionattribute5 | 扩展属性 5 |
| 用户 | extensionattribute6 | 扩展属性 6 |
| 用户 | extensionattribute7 | 扩展属性 7 |
| 用户 | extensionattribute8 | 扩展属性 8 |
| 用户 | extensionattribute9 | 扩展属性 9 |
| 用户 | extensionattribute10 | 扩展属性 10 |
| 用户 | extensionattribute11 | 扩展属性 11 |
| 用户 | extensionattribute12 | 扩展属性 12 |
| 用户 | extensionattribute13 | 扩展属性 13 |
| 用户 | extensionattribute14 | 扩展属性 14 |
| 用户 | extensionattribute15 | 扩展属性 15 |

#### <a name="table-6-transformation-methods-allowed-for-saml-nameid"></a>表 6：允许用于 SAML NameID 的转换方法

| TransformationMethod | 限制 |
| ----- | ----- |
| ExtractMailPrefix | 无 |
| 联接 | 要联接的后缀必须是资源租户的已验证域。 |

## <a name="next-steps"></a>后续步骤

- 若要了解如何使用 PowerShell 为租户中的特定应用程序自定义在令牌中发出的声明，请参阅[操作指南：为租户中的特定应用自定义在令牌中发出的声明](active-directory-claims-mapping.md)
- 若要了解如何通过 Azure 门户自定义 SAML 令牌中颁发的声明，请参阅[如何：为企业应用程序自定义 SAML 令牌中颁发的声明](active-directory-saml-claims-customization.md)
- 若要详细了解扩展属性，请参阅[在声明中使用目录架构扩展属性](active-directory-schema-extensions.md)。
