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
ms.date: 06/01/2021
ms.author: ryanwi
ms.custom: aaddev, identityplatformtop40, content-perf, FY21Q1, contperf-fy21q1
ms.reviewer: hirsin, jlu, annaba
ms.openlocfilehash: f67e02d0da3d7f7c1083cdb96436c8b82cae2cef
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2021
ms.locfileid: "113728059"
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

使用此功能需要 Azure AD Premium P1 许可证。 若要根据需要查找合适的许可证，请参阅[比较免费版和高级版的正式发布功能](https://www.microsoft.com/security/business/identity-access-management/azure-ad-pricing)。

拥有 [Microsoft 365 商业版许可证](/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description)的客户也可以访问条件访问功能。

## <a name="token-lifetime-policies-for-access-saml-and-id-tokens"></a>访问令牌、SAML 令牌和 ID 令牌的令牌生存期策略

可为访问令牌、SAML 令牌和 ID 令牌设置令牌生存期策略。

### <a name="access-tokens"></a>访问令牌

客户端使用访问令牌来访问受保护的资源。 访问令牌仅可用于用户、客户端和资源的特定组合。 访问令牌不能吊销，在过期日期之前保持有效。 获取了访问令牌的恶意行动者在访问令牌的生存期内一直可以使用它。 调整访问令牌生存期的利弊是可以提高系统性能，但也会增加客户端在用户帐户禁用后保留访问权限的时间。 通过减少客户端获取刷新访问令牌的次数可以实现系统性能的提升。  根据请求令牌的客户端应用程序，默认值会有所不同。 例如，对于支持协商 CAE 感知会话客户端的连续访问评估 (CAE)，令牌生存期时间将会很长（最多 28 小时）。 令牌过期后，客户端必须使用刷新令牌（通常以无提示方式）获取新的刷新令牌和访问令牌。

### <a name="saml-tokens"></a>SAML 令牌

SAML 令牌由许多基于 Web 的 SaaS 应用程序使用，可通过 Azure Active Directory 的 SAML2 协议终结点获得。 使用 WS 联合身份验证的应用程序也使用它们。 令牌的默认生存期为 1 小时。 从应用程序的角度来看，令牌的有效期由令牌中 `<conditions …>` 元素的 NotOnOrAfter 值指定。 令牌有效期过后，客户端必须发起新的身份验证请求。在使用单一登录 (SSO) 会话令牌的情况下，通常不需要交互式登录就可以满足该请求。

可以使用 `TokenLifetimePolicy` 中的 `AccessTokenLifetime` 参数更改 NotOnOrAfter 的值。 它会被设置为策略中配置的生存期（如果有），再加上 5 分钟的时钟偏差系数。

`<SubjectConfirmationData>` 元素中指定的主题确认 NotOnOrAfter 不受令牌生存期配置的影响。 

### <a name="id-tokens"></a>ID 令牌

ID 令牌将传递给网站和本机客户端。 ID 令牌包含有关用户的配置文件信息。 ID 令牌绑定到用户和客户端的特定组合。 在过期日期之前，ID 令牌保持有效。 通常，Web 应用程序会将应用程序中用户的会话生存期与针对该用户颁发的 ID 令牌的生存期进行匹配。 可以调整 ID 令牌的生存期，以控制 Web 应用程序使应用程序会话过期的频率，以及要求用户在 Microsoft 标识平台上重新进行身份验证（以无提示方式或交互方式）的频率。

## <a name="token-lifetime-policies-for-refresh-tokens-and-session-tokens"></a>刷新令牌和会话令牌的令牌生存期策略

你不能设置刷新令牌和会话令牌的令牌生存期策略。 有关刷新令牌的生存期、超时和吊销信息，请参阅[刷新令牌](refresh-tokens.md)。

> [!IMPORTANT]
> 从 2021 年 1 月 30 日起，无法再配置刷新和会话令牌生存期。 Azure Active Directory 不再支持现有策略中的刷新和会话令牌配置。  现有令牌过期后颁发的新令牌现已设置为[默认配置](#configurable-token-lifetime-properties)。 在刷新和会话令牌配置停用后，你仍可配置访问权限、SAML 和 ID 令牌生存期。
>
> 现有令牌的生存期不会更改。 在它们过期后，会根据默认值颁发新令牌。
>
> 如果需要继续定义要求用户再次登录之前的时间段，请配置条件访问中的登录频率。 若要详细了解条件访问，请参阅[使用条件访问配置身份验证会话管理](../conditional-access/howto-conditional-access-session-lifetime.md)。

## <a name="configurable-token-lifetime-properties"></a>可配置的令牌生存期属性
令牌生存期策略是一种策略对象，其中包含令牌生存期规则。 此策略控制该资源的访问令牌、SAML 令牌和 ID 令牌的有效期时间长短。 无法为刷新和会话令牌创建令牌生存期策略。 如果未设置策略，系统将强制实施默认生存期值。

### <a name="access-id-and-saml2-token-lifetime-policy-properties"></a>访问令牌、ID 令牌和 SAML2 令牌生存期策略属性

降低“访问令牌生存期”属性可以缓解恶意行动者长时间使用访问令牌或 ID 令牌的风险。 （这些令牌不可吊销。）弊端是对性能会造成不利影响，因为必须更频繁地更换令牌。

有关示例，请参阅[为 Web 登录创建策略](configure-token-lifetimes.md#create-a-policy-for-web-sign-in)。

访问令牌、ID 令牌和 SAML2 令牌配置受以下属性及其相应设置值的影响：

- **属性**：访问令牌生存期
- **策略属性字符串**：AccessTokenLifetime
- **影响**：访问令牌、ID 令牌、SAML2 令牌
- **默认**：
    - 访问令牌：根据请求令牌的客户端应用程序，此令牌会有所不同。 例如，对于支持协商 CAE 感知会话客户端的连续访问评估 (CAE)，令牌生存期时间将会很长（最多 28 小时）。
    - ID 令牌，SAML2 令牌：1 小时
- **最小值**：10 分钟
- **最大值**：1 天

### <a name="refresh-and-session-token-lifetime-policy-properties"></a>刷新令牌和会话令牌生存期策略属性

刷新令牌和会话令牌配置受以下属性及其各自的设置值影响。 在 2021 年 1 月 30 日停用刷新令牌和会话令牌配置后，Azure AD 将仅使用下述默认值。 如果你决定不使用[条件访问](../conditional-access/howto-conditional-access-session-lifetime.md)来管理登录频率，则会将刷新令牌和会话令牌设置为该日期的默认配置，并且无法再更改其生存期。  

|属性   |策略属性字符串    |影响 |默认 |
|----------|-----------|------------|------------|
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
* 如果未将策略分配到服务主体、组织或应用程序对象，则会强制实施默认值。 （请参阅[可配置的令牌生存期属性](#configurable-token-lifetime-properties)中的表格。）

有关应用程序对象与服务主体对象之间的关系的详细信息，请参阅 [Azure Active Directory 中的应用程序对象和服务主体对象](app-objects-and-service-principals.md)。

使用令牌时，系统会评估其有效性。 所访问的应用程序中具有最高优先级的策略会生效。

此处使用的所有时间范围，将根据 C# [TimeSpan](/dotnet/api/system.timespan) 对象 - D.HH:MM:SS 设置格式。  因此，80 天和 30 分钟将表示为 `80.00:30:00`。  如果为零，则可删除前面的 D，因此，90 分钟将表示为 `00:90:00`。  

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
