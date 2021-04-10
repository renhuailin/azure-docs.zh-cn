---
title: 可配置令牌生存期
titleSuffix: Microsoft identity platform
description: 了解如何为 Microsoft 标识平台颁发的访问令牌、SAML 令牌和 ID 令牌设置生存期。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 02/01/2021
ms.author: ryanwi
ms.custom: aaddev, identityplatformtop40, content-perf, FY21Q1, contperf-fy21q1
ms.reviewer: hirsin, jlu, annaba
ms.openlocfilehash: 374e8bb61886a78289fcf44ce9582df325a4e64c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "102549033"
---
# <a name="configurable-token-lifetimes-in-the-microsoft-identity-platform-preview"></a>Microsoft 标识平台中的可配置令牌生存期（预览版）

你可以指定由 Microsoft 标识平台颁发的访问令牌、ID 令牌或 SAML 令牌的生存期。 可以针对组织中的所有应用、多租户（多组织）应用程序或者组织中的特定服务主体设置生存期。 但是，我们目前不支持为[托管标识服务主体](../managed-identities-azure-resources/overview.md)配置令牌生存期。

在 Azure AD 中，策略对象表示针对组织中的单个应用程序或所有应用程序强制实施的一组规则。 每种策略类型都有一个唯一的结构，其中的一组属性将应用于它们所分配到的对象。

可将某个策略指定为组织的默认策略。 该策略将应用到组织中的任何应用程序，只要此策略不被更高优先级的策略覆盖即可。 此外，还可以将策略分配到特定的应用程序。 优先顺序根据策略类型的不同而异。

有关示例，请参阅[令牌生存期配置示例](configure-token-lifetimes.md)。

> [!NOTE]
> 可配置令牌生存期策略仅适用于访问 SharePoint Online 和 OneDrive for Business 资源的移动和桌面客户端，不适用于 Web 浏览器会话。
> 若要管理 SharePoint Online 和 OneDrive for Business 的 Web 浏览器会话的生存期，请使用[条件访问会话生存期](../conditional-access/howto-conditional-access-session-lifetime.md)功能。 了解有关配置空闲会话超时的详细信息，请参阅 [SharePoint Online 博客](https://techcommunity.microsoft.com/t5/SharePoint-Blog/Introducing-Idle-Session-Timeout-in-SharePoint-and-OneDrive/ba-p/119208)。

## <a name="license-requirements"></a>许可要求

使用此功能需要 Azure AD Premium P1 许可证。 若要根据需要查找合适的许可证，请参阅[比较免费版和高级版的正式发布功能](https://azure.microsoft.com/pricing/details/active-directory/)。

拥有 [Microsoft 365 商业版许可证](/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description)的客户也可以访问条件访问功能。

## <a name="token-lifetime-policies-for-access-saml-and-id-tokens"></a>访问令牌、SAML 令牌和 ID 令牌的令牌生存期策略

可为访问令牌、SAML 令牌和 ID 令牌设置令牌生存期策略。 

### <a name="access-tokens"></a>访问令牌

客户端使用访问令牌来访问受保护的资源。 访问令牌仅可用于用户、客户端和资源的特定组合。 访问令牌不能吊销，在过期日期之前保持有效。 获取了访问令牌的恶意行动者在访问令牌的生存期内一直可以使用它。 调整访问令牌生存期的利弊是可以提高系统性能，但也会增加客户端在用户帐户禁用后保留访问权限的时间。 通过减少客户端获取刷新访问令牌的次数可以实现系统性能的提升。  默认值为 1 小时 - 在 1 小时后，客户端必须使用刷新令牌（通常以无提示方式）获取新的刷新令牌和访问令牌。

### <a name="saml-tokens"></a>SAML 令牌

SAML 令牌由许多基于 Web 的 SaaS 应用程序使用，可通过 Azure Active Directory 的 SAML2 协议终结点获得。 使用 WS 联合身份验证的应用程序也使用它们。 令牌的默认生存期为 1 小时。 从应用程序的角度来看，令牌的有效期由令牌中 `<conditions …>` 元素的 NotOnOrAfter 值指定。 令牌有效期过后，客户端必须发起新的身份验证请求。在使用单一登录 (SSO) 会话令牌的情况下，通常不需要交互式登录就可以满足该请求。

可以使用 `TokenLifetimePolicy` 中的 `AccessTokenLifetime` 参数更改 NotOnOrAfter 的值。 它会被设置为策略中配置的生存期（如果有），再加上 5 分钟的时钟偏差系数。

`<SubjectConfirmationData>` 元素中指定的主题确认 NotOnOrAfter 不受令牌生存期配置的影响。 

### <a name="id-tokens"></a>ID 令牌

ID 令牌将传递给网站和本机客户端。 ID 令牌包含有关用户的配置文件信息。 ID 令牌绑定到用户和客户端的特定组合。 在过期日期之前，ID 令牌保持有效。 通常，Web 应用程序会将应用程序中用户的会话生存期与针对该用户颁发的 ID 令牌的生存期进行匹配。 可以调整 ID 令牌的生存期，以控制 Web 应用程序使应用程序会话过期的频率，以及要求用户在 Microsoft 标识平台上重新进行身份验证（以无提示方式或交互方式）的频率。

### <a name="token-lifetime-policy-properties"></a>令牌生存期策略属性

令牌生存期策略是一种策略对象，其中包含令牌生存期规则。 此策略控制该资源的访问令牌、SAML 令牌和 ID 令牌的有效期时间长短。 如果未设置策略，系统将强制实施默认生存期值。 

降低“访问令牌生存期”属性可以缓解恶意行动者长时间使用访问令牌或 ID 令牌的风险。 （这些令牌不可吊销。）弊端是对性能会造成不利影响，因为必须更频繁地更换令牌。

有关示例，请参阅[为 Web 登录创建策略](configure-token-lifetimes.md#create-a-policy-for-web-sign-in)。

| 属性 | 策略属性字符串 | 影响 | 默认 | 最小值 | 最大值 |
| --- | --- | --- | --- | --- | --- |
| 访问令牌生存期 |AccessTokenLifetime |访问令牌、ID 令牌、SAML2 令牌 |1 小时 |10 分钟 |1 天 |

> [!NOTE]
> 若要确保 Microsoft Teams Web 客户端正常工作，建议将 Microsoft Teams 的 AccessTokenLifetime 始终设置为大于 15 分钟。

## <a name="token-lifetime-policies-for-refresh-tokens-and-session-tokens"></a>刷新令牌和会话令牌的令牌生存期策略

你不能设置刷新令牌和会话令牌的令牌生存期策略。

> [!IMPORTANT]
> 从 2021 年 1 月 30 日起，无法再配置刷新和会话令牌生存期。 Azure Active Directory 不再支持现有策略中的刷新和会话令牌配置。  现有令牌过期后颁发的新令牌现已设置为[默认配置](#configurable-token-lifetime-properties-after-the-retirement)。 在刷新和会话令牌配置停用后，你仍可配置访问权限、SAML 和 ID 令牌生存期。
>
> 现有令牌的生存期不会更改。 在它们过期后，会根据默认值颁发新令牌。
>
> 如果需要继续定义要求用户再次登录之前的时间段，请配置条件访问中的登录频率。 若要详细了解条件访问，请参阅[使用条件访问配置身份验证会话管理](../conditional-access/howto-conditional-access-session-lifetime.md)。

:::image type="content" source="./media/active-directory-configurable-token-lifetimes/roadmap.svg" alt-text="停用信息":::

### <a name="refresh-tokens"></a>刷新令牌

当客户端获取访问令牌来访问受保护资源时，它同时还会收到一个刷新令牌。 当前访问令牌过期时，可以使用刷新令牌获取新的访问/刷新令牌对。 刷新令牌绑定到用户和客户端的组合。 刷新令牌可以[随时吊销](access-tokens.md#token-revocation)，每次使用该令牌时，系统都会检查其有效性。  使用刷新令牌获取新访问令牌时，不会撤销刷新令牌 - 但最佳做法是获取新令牌后安全删除旧令牌。

必须在机密客户端与公共客户端之间做出区分，因为这会影响刷新令牌可以使用的时长。 有关不同类型的客户端的详细信息，请参阅 [RFC 6749](https://tools.ietf.org/html/rfc6749#section-2.1)。

#### <a name="token-lifetimes-with-confidential-client-refresh-tokens"></a>机密客户端刷新令牌的令牌生存期
机密客户端是能够安全存储客户端密码（机密）的应用程序。 它们可证实请求来自安全的客户端应用程序而不是来自恶意行动者。 例如，Web 应用是机密客户端，因为它可以在 Web 服务器上存储客户端机密， 因此不会公开。 由于这些流程更加安全，因此颁发给这些流程的刷新令牌的默认生存期为 `until-revoked`，无法使用策略更改，并且不会在自愿密码重置时撤销。

#### <a name="token-lifetimes-with-public-client-refresh-tokens"></a>公共客户端刷新令牌的令牌生存期

公共客户端无法安全地存储客户端密码（机密）。 例如，iOS/Android 应用不能对资源所有者的机密进行模糊处理，因此被视为公共客户端。 可以针对资源设置策略，防止使用公共客户端中超过指定期限的刷新令牌获取新的访问权限/刷新令牌对。 为此，请使用[“刷新令牌最大非活动时间”属性](#refresh-token-max-inactive-time) (`MaxInactiveTime`)。 还可以使用策略设置一个时间段，超出该时间段后，刷新令牌不再被接受。 为此，请使用[单因素刷新令牌最大期限](#single-factor-session-token-max-age)或[多因素会话令牌最大期限](#multi-factor-refresh-token-max-age)属性。 可以调整刷新令牌的生存期，控制用户在使用公共客户端应用程序时，需要重新输入凭据而不是以无提示方式重新进行身份验证的时间和频率。

“最大期限”属性是可以使用单个令牌的时间长度。 

### <a name="single-sign-on-session-tokens"></a>单一登录会话令牌
当用户通过 Microsoft 标识平台进行身份验证时，系统会在用户的浏览器与 Microsoft 标识平台之间建立单一登录 (SSO) 会话。 SSO 令牌采用 Cookie 形式，代表此会话。 SSO 会话令牌不会绑定到特定的资源/客户端应用程序。 SSO 会话令牌可以吊销，每次使用它们时，系统都会检查其有效性。

Microsoft 标识平台使用两种 SSO 会话令牌：持久性和非持久性会话令牌。 浏览器将持久性会话令牌存储为持久性 Cookie， 将非持久性会话令牌存储为会话 Cookie。 （关闭浏览器会销毁会话 Cookie。）通常会存储一个非持久性会话令牌。 但如果用户在身份验证期间选择“使我保持登录状态”复选框，则存储的是持久性会话令牌。

非持久性会话令牌的生存期为 24 小时。 持久性令牌的生存期为 90 天。 每当在 SSO 会话令牌的有效期内使用它时，有效期会根据令牌类型再次延长 24 小时或 90 天。 如果 SSO 会话令牌在其有效期内未被使用，则将它视为过期，不再被系统接受。

颁发第一个会话令牌后，可以使用策略来设置一个时间段，超过该时间段后，该会话令牌不再被接受。 （为此，可使用“会话令牌最大期限”属性。）可以调整会话令牌的生存期，控制用户在使用 Web 应用程序时，需要重新输入凭据而不是以无提示方式进行身份验证的时间和频率。

### <a name="refresh-and-session-token-lifetime-policy-properties"></a>刷新令牌和会话令牌生存期策略属性
令牌生存期策略是一种策略对象，其中包含令牌生存期规则。 使用策略的属性控制指定的令牌生存期。 如果未设置策略，系统将强制实施默认生存期值。

#### <a name="configurable-token-lifetime-properties"></a>可配置的令牌生存期属性
| 属性 | 策略属性字符串 | 影响 | 默认 | 最小值 | 最大值 |
| --- | --- | --- | --- | --- | --- |
| 刷新令牌最大非活动时间 |MaxInactiveTime |刷新令牌 |90 天 |10 分钟 |90 天 |
| 单因素刷新令牌最大期限 |MaxAgeSingleFactor |刷新令牌（适用于任何用户） |直到吊销 |10 分钟 |直到吊销<sup>1</sup> |
| 多因素刷新令牌最大期限 |MaxAgeMultiFactor |刷新令牌（适用于任何用户） | 直到吊销 |10 分钟 |180 天<sup>1</sup> |
| 单因素会话令牌最大期限 |MaxAgeSessionSingleFactor |会话令牌（持久性和非持久性） |直到吊销 |10 分钟 |直到吊销<sup>1</sup> |
| 多因素会话令牌最大期限 |MaxAgeSessionMultiFactor |会话令牌（持久性和非持久性） | 直到吊销 |10 分钟 | 180 天<sup>1</sup> |

* <sup>1</sup>365 天是可针对这些属性设置的最大显式时间长短。

#### <a name="exceptions"></a>例外
| 属性 | 影响 | 默认 |
| --- | --- | --- |
| 刷新令牌最大期限（针对吊销信息不足的联合用户颁发<sup>1</sup>） |刷新令牌（针对吊销信息不足的联合用户颁发<sup>1</sup>） |12 小时 |
| 刷新令牌最大非活动时间（针对机密客户端颁发） |刷新令牌（针对机密客户端颁发） |90 天 |
| 刷新令牌最大期限（针对机密客户端颁发） |刷新令牌（针对机密客户端颁发） |直到吊销 |

* <sup>1</sup> 吊销信息不足的联合用户包括未同步“LastPasswordChangeTimestamp”属性的任何用户。 因为 Azure Active Directory 无法验证何时吊销绑定旧凭据（例如已更改的密码）的令牌，必须更频繁地重新检查以确保用户和关联的令牌状态仍然良好，所以为这些用户提供此短暂的最大期限。 若要改善此体验，租户管理员必须确保同步“LastPasswordChangeTimestamp”属性（这可以通过 PowerShell 或 AADSync 在用户对象上进行设置）。

### <a name="configurable-policy-property-details"></a>可配置的策略属性详细信息

#### <a name="refresh-token-max-inactive-time"></a>刷新令牌最大非活动时间
**字符串：** MaxInactiveTime

**影响：** 刷新令牌

**摘要：** 此策略控制在尝试访问资源时，在客户端无法再使用刷新令牌来检索新的访问/刷新令牌对之前的刷新令牌的生存期。 由于使用刷新令牌时通常会返回一个新的刷新令牌，因此，如果客户端在指定的时间段尝试使用当前刷新令牌访问任何资源，此策略会阻止访问。

此策略将强制客户端上处于非活动状态的用户重新进行身份验证，然后才能检索新的刷新令牌。

设置的“刷新令牌最大非活动时间”属性必须必须小于“单因素令牌最大期限”和“多因素刷新令牌最大期限”属性。

有关示例，请参阅[为调用 Web API 的本机应用创建策略](configure-token-lifetimes.md#create-a-policy-for-a-native-app-that-calls-a-web-api)。

#### <a name="single-factor-refresh-token-max-age"></a>单因素刷新令牌最大期限
**字符串：** MaxAgeSingleFactor

**影响：** 刷新令牌

**摘要：** 此策略控制用户在上次仅使用一个因素成功完成身份验证后，可以使用刷新令牌获取新访问/刷新令牌对的时间长短。 用户完成身份验证并收到新刷新令牌后，可在指定的时间段内使用刷新令牌流。 （前提是当前刷新令牌未吊销，并且未使用时间不超过非活动时间。）超过该时间后，用户必须重新完成身份验证才能接收新的会话令牌。

减少最大期限会强制用户更频繁地进行身份验证。 由于单重身份验证的安全性不如多重身份验证，因此我们建议为此属性设置一个小于“多因素刷新令牌最大期限”属性的值。

有关示例，请参阅[为调用 Web API 的本机应用创建策略](configure-token-lifetimes.md#create-a-policy-for-a-native-app-that-calls-a-web-api)。

#### <a name="multi-factor-refresh-token-max-age"></a>多因素刷新令牌最大期限
**字符串：** MaxAgeMultiFactor

**影响：** 刷新令牌

**摘要：** 此策略控制用户在上次使用多个因素成功完成身份验证后，可以使用刷新令牌获取新访问/刷新令牌对的时间长短。 用户完成身份验证并收到新刷新令牌后，可在指定的时间段内使用刷新令牌流。 （前提是当前刷新令牌未吊销，并且未使用时间不超过非活动时间。）超过该时间后，用户必须重新完成身份验证才能接收新的会话令牌。

减少最大期限会强制用户更频繁地进行身份验证。 由于单重身份验证的安全性不如多重身份验证，因此我们建议为此属性设置一个大于“单因素刷新令牌最大期限”属性的值。

有关示例，请参阅[为调用 Web API 的本机应用创建策略](configure-token-lifetimes.md#create-a-policy-for-a-native-app-that-calls-a-web-api)。

#### <a name="single-factor-session-token-max-age"></a>单因素会话令牌最大期限
**字符串：** MaxAgeSessionSingleFactor

**影响：** 会话令牌（持久性和非持久性）

**摘要：** 此策略控制用户在上次仅使用一个因素成功完成身份验证后，可以使用会话令牌获取新 ID 令牌和会话令牌的时间长短。 用户完成身份验证并收到新会话令牌后，可在指定的时间段内使用会话令牌流。 （前提是当前会话令牌未吊销或过期。）超过指定的时间段后，用户必须重新完成身份验证才能接收新的会话令牌。

减少最大期限会强制用户更频繁地进行身份验证。 由于单重身份验证的安全性不如多重身份验证，因此我们建议为此属性设置一个小于“多因素会话令牌最大期限”属性的值。

有关示例，请参阅[为 Web 登录创建策略](configure-token-lifetimes.md#create-a-policy-for-web-sign-in)。

#### <a name="multi-factor-session-token-max-age"></a>多因素会话令牌最大期限
**字符串：** MaxAgeSessionMultiFactor

**影响：** 会话令牌（持久性和非持久性）

**摘要：** 此策略控制用户在上次使用多个因素成功完成身份验证后，可以继续使用会话令牌获取新 ID 令牌和会话令牌的时间长短。 用户完成身份验证并收到新会话令牌后，可在指定的时间段内使用会话令牌流。 （前提是当前会话令牌未吊销或过期。）超过指定的时间段后，用户必须重新完成身份验证才能接收新的会话令牌。

减少最大期限会强制用户更频繁地进行身份验证。 由于单重身份验证的安全性不如多重身份验证，因此我们建议为此属性设置一个大于“单因素会话令牌最大期限”属性的值。

## <a name="configurable-token-lifetime-properties-after-the-retirement"></a>停用后的可配置令牌生存期属性
刷新令牌和会话令牌配置受以下属性及其各自的设置值影响。 在 2021 年 1 月 30 日停用刷新令牌和会话令牌配置后，Azure AD 将仅使用下述默认值。 如果你决定不使用条件访问来管理登录频率，则你的刷新令牌和会话令牌会被设置为该日期的默认配置，你将无法再更改其生存期。  

|属性   |策略属性字符串    |影响 |默认 |
|----------|-----------|------------|------------|
|访问令牌生存期 |AccessTokenLifetime |访问令牌、ID 令牌、SAML2 令牌 |1 小时 |
|刷新令牌最大非活动时间 |MaxInactiveTime  |刷新令牌 |90 天  |
|单因素刷新令牌最大期限  |MaxAgeSingleFactor  |刷新令牌（适用于任何用户）  |直到吊销  |
|多因素刷新令牌最大期限  |MaxAgeMultiFactor  |刷新令牌（适用于任何用户） |直到吊销  |
|单因素会话令牌最大期限  |MaxAgeSessionSingleFactor |会话令牌（持久性和非持久性）  |直到吊销 |
|多因素会话令牌最大期限  |MaxAgeSessionMultiFactor  |会话令牌（持久性和非持久性）  |直到吊销 |

你可以使用 PowerShell 查找会受这次停用影响的策略。  使用 [PowerShell cmdlet](configure-token-lifetimes.md#get-started) 可查看组织中创建的所有策略，或了解哪些应用和服务主体关联到特定策略。

## <a name="policy-evaluation-and-prioritization"></a>策略评估和优先级
可以创建令牌生存期策略并将其分配到特定的应用程序、组织和服务主体。 可将多个策略应用到特定的应用程序。 生效的令牌生存期策略遵循以下规则：

* 如果将某个策略显式分配到服务主体，将强制实施该策略。
* 如果未将策略显式分配到服务主体，则强制实施显式分配到服务主体的父组织的策略。
* 如果未将策略显式分配到服务主体或组织，则强制实施分配到应用程序的策略。
* 如果未将策略分配到服务主体、组织或应用程序对象，则会强制实施默认值。 （请参阅[可配置的令牌生存期属性](#configurable-token-lifetime-properties-after-the-retirement)中的表格。）

有关应用程序对象与服务主体对象之间的关系的详细信息，请参阅 [Azure Active Directory 中的应用程序对象和服务主体对象](app-objects-and-service-principals.md)。

使用令牌时，系统会评估其有效性。 所访问的应用程序中具有最高优先级的策略会生效。

此处使用的所有时间范围，将根据 C# [TimeSpan](/dotnet/api/system.timespan) 对象 - D.HH:MM:SS 设置格式。  因此，80 天和 30 分钟将表示为 `80.00:30:00`。  如果为零，则可删除前面的 D，因此，90 分钟将表示为 `00:90:00`。  

### <a name="example-scenario"></a>示例方案

某个用户想要访问两个 Web 应用程序：Web 应用程序 A 和 Web 应用程序 B。

因素：
* 这两个 Web 应用程序在同一个父组织中。
* 会话令牌最大期限为 8 小时的令牌生存期策略 1 设置为父组织的默认策略。
* Web 应用程序 A 是一个常规用途的 Web 应用程序，未链接到任何策略。
* Web 应用程序 B 用于高度敏感的流程。 该应用程序的服务主体已链接到会话令牌最大期限为 30 分钟的令牌生存期策略 2。

中午 12:00，用户启动新的浏览器会话，尝试访问 Web 应用程序 A。系统将用户重定向到 Microsoft 标识平台并要求其登录。 这会在浏览器中创建一个包含会话令牌的 Cookie。 用户使用可用于访问应用程序的 ID 令牌重定向回到 Web 应用程序 A。

中午 12:15，用户尝试访问 Web 应用程序 B。浏览器重定向到 Microsoft 标识平台并检测会话 Cookie。 Web 应用程序 B 的服务主体已链接到令牌生存期策略 2，但同时也属于使用默认令牌生存期策略 1 的父组织。 由于链接到服务主体的策略优先级高于组织默认策略，因此令牌生存期策略 2 生效。 会话令牌最初是在过去 30 分钟内颁发的，因此被视为有效。 用户使用授予权限的 ID 令牌重定向回到 Web 应用程序 B。

下午 1:00，用户尝试访问 Web 应用程序 A。系统将该用户重定向到 Microsoft 标识平台。 Web 应用程序 A 未链接到任何策略，但由于它在使用默认令牌生存期策略 1 的组织中，因此，该策略会生效。 已检测到最初在过去 8 小时内颁发的会话 Cookie。 用户已使用新 ID 令牌以无提示方式重定向回到 Web 应用程序 A。 用户不需要进行身份验证。

随后，用户立即尝试访问 Web 应用程序 B。系统将用户重定向到 Microsoft 标识平台。 如前所述，令牌生存期策略 2 生效。 由于颁发的令牌超过 30 分钟，系统会提示用户重新输入其凭据。 颁发新的会话令牌和 ID 令牌。 然后，用户可以访问 Web 应用程序 B。

## <a name="cmdlet-reference"></a>Cmdlet 参考

这些是 [Azure Active Directory PowerShell for Graph 预览版模块](/powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true#service-principals)中的 cmdlet。

### <a name="manage-policies"></a>管理策略

可以使用以下 cmdlet 来管理策略。

| Cmdlet | 说明 | 
| --- | --- |
| [New-AzureADPolicy](/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) | 创建新策略。 |
| [Get-AzureADPolicy](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) | 获取所有 Azure AD 策略或指定的策略。 |
| [Get-AzureADPolicyAppliedObject](/powershell/module/azuread/get-azureadpolicyappliedobject?view=azureadps-2.0-preview&preserve-view=true) | 获取已链接到策略的所有应用和服务主体。 |
| [Set-AzureADPolicy](/powershell/module/azuread/set-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) | 更新现有策略。 |
| [Remove-AzureADPolicy](/powershell/module/azuread/remove-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) | 删除指定的策略。 |

### <a name="application-policies"></a>应用程序策略
可以针对应用程序策略使用以下 cmdlet。</br></br>

| Cmdlet | 说明 | 
| --- | --- |
| [Add-AzureADApplicationPolicy](/powershell/module/azuread/add-azureadapplicationpolicy?view=azureadps-2.0-preview&preserve-view=true) | 将指定的策略链接到应用程序。 |
| [Get-AzureADApplicationPolicy](/powershell/module/azuread/get-azureadapplicationpolicy?view=azureadps-2.0-preview&preserve-view=true) | 获取已分配到应用程序的策略。 |
| [Remove-AzureADApplicationPolicy](/powershell/module/azuread/remove-azureadapplicationpolicy?view=azureadps-2.0-preview&preserve-view=true) | 从应用程序中删除策略。 |

### <a name="service-principal-policies"></a>服务主体策略
可以针对服务主体策略使用以下 cmdlet。

| Cmdlet | 说明 | 
| --- | --- |
| [Add-AzureADServicePrincipalPolicy](/powershell/module/azuread/add-azureadserviceprincipalpolicy?view=azureadps-2.0-preview&preserve-view=true) | 将指定的策略链接到服务主体。 |
| [Get-AzureADServicePrincipalPolicy](/powershell/module/azuread/get-azureadserviceprincipalpolicy?view=azureadps-2.0-preview&preserve-view=true) | 获取已链接到指定服务主体的任何策略。|
| [Remove-AzureADServicePrincipalPolicy](/powershell/module/azuread/remove-azureadserviceprincipalpolicy?view=azureadps-2.0-preview&preserve-view=true) | 从指定的服务主体中删除策略。|

## <a name="next-steps"></a>后续步骤

若要了解详细信息，请参阅[令牌生存期配置示例](configure-token-lifetimes.md)。
