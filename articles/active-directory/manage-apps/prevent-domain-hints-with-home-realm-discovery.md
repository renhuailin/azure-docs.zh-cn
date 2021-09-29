---
title: 使用“主领域发现”策略禁止登录自动加速
titleSuffix: Azure AD
description: 了解如何阻止到联合 IDP 的 domain_hint 自动加速。
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 02/12/2021
ms.author: davidmu
ms.reviewer: hirsin
ms.openlocfilehash: 9f8608330d5f74ef2e1262b7c3dff82be746c884
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/26/2021
ms.locfileid: "129059165"
---
# <a name="disable-auto-acceleration-to-a-federated-idp-during-user-sign-in-with-home-realm-discovery-policy"></a>在用户使用主领域发现策略登录过程中禁用到联合 IDP 的自动加速

[主领域发现策略](/graph/api/resources/homeRealmDiscoveryPolicy) (HRD) 为管理员提供多种方式来控制对用户进行身份验证的方式和位置。 HRD 策略的 `domainHintPolicy` 部分用于帮助将联合用户迁移到云托管凭据（如 [FIDO](../authentication/howto-authentication-passwordless-security-key.md)），方法是确保它们始终访问 Azure AD 登录页，且不会因域提示而自动加速到联合 IDP。

管理员无法控制应用程序或在登录期间更新添加域提示时，需要使用此策略。  例如，`outlook.com/contoso.com` 将用户发送到追加了 `&domain_hint=contoso.com` 参数的登录页，以便将用户直接自动加速到 `contoso.com` 域的联合 IDP。 使用发送到联合 IDP 的托管凭据的用户不能使用其托管凭据登录，这会降低安全性，并会使具有随机登录体验的用户感到沮丧。 推出托管凭据的管理员[还应设置此策略](#suggested-use-within-a-tenant)，以确保用户始终可以使用其托管凭据。

## <a name="domainhintpolicy-details"></a>DomainHintPolicy 详细信息

HRD 策略的 DomainHintPolicy 部分是一个 JSON 对象，它允许管理员从域提示使用情况中选择排除某些域和应用程序。  从功能上讲，这会告知 Azure AD 登录页按照登录请求上的参数 `domain_hint` 不存在那样行事。

### <a name="the-respect-and-ignore-policy-sections"></a>“遵循和忽略策略”部分

|部分 | 含义 | 值 |
|--------|---------|--------|
|`IgnoreDomainHintForDomains` |如果此域提示是在请求中发送的，请忽略它。 |域地址的数组（例如 `contoso.com`）。 它还支持 `all_domains`|
|`RespectDomainHintForDomains`| 如果此域提示是在请求中发送的，则遵循它，即使 `IgnoreDomainHintForApps` 指示请求中的应用不应自动加速。 这用于减缓在网络内推出弃用域提示的速度，你可以指出某些域应仍为已加速。 | 域地址的数组（例如 `contoso.com`）。 它还支持 `all_domains`|
|`IgnoreDomainHintForApps`| 如果来自此应用程序的请求附带了域提示，请忽略它。 | 应用程序 ID (GUID) 的数组。 它还支持 `all_apps`|
|`RespectDomainHintForApps` |如果来自此应用程序的请求附带了域提示，则遵循它，即使 `IgnoreDomainHintForDomains` 包含该域也是如此。 用于确保在发现某些应用没有域提示的情况下中断时继续工作。 | 应用程序 ID (GUID) 的数组。 它还支持 `all_apps`|

### <a name="policy-evaluation"></a>策略评估

DomainHintPolicy 逻辑在包含域提示的每个传入请求上运行，并基于请求中的两个数据片段 – 域提示中的域以及客户端 ID（应用）。 简而言之，对于域或应用，“遵循”优先于“忽略”给定域或应用程序的域提示的指令。

1. 在没有任何域提示策略的情况下，或者如果 4 个部分都未引用所述的应用或域提示，将[评估其余的 HRD 策略](home-realm-discovery-policy.md#priority-and-evaluation-of-hrd-policies)。
1. 如果 `RespectDomainHintForApps` 或 `RespectDomainHintForDomains` 部分中的任何一个（或两者）在请求中包含应用或域提示，则将根据请求将用户自动加速到联合 IDP。
1. 如果 `IgnoreDomainHintsForApps` 或 `IgnoreDomainHintsForDomains` 中的任何一个（或两者）在请求中引用应用或域提示，并且“遵循”部分未引用它们，则请求不会自动加速，并且用户将保留在 Azure AD 登录页以提供用户名。

用户在登录页上输入用户名后，他们可以使用托管凭据（如有注册）。  如果他们选择不使用托管凭据，或者他们没有注册任何凭据，则会像往常一样将他们转到联合 IDP 进行凭据输入。

## <a name="suggested-use-within-a-tenant"></a>租户内的建议用途

联合域的管理员应在包含四个阶段的计划中设置 HRD 策略的此部分。 此计划的目标是最终让租户中的所有用户都有机会使用托管凭据，而不考虑域或应用程序，并保存那些对 `domain_hint` 使用情况有硬依赖关系的应用。  此计划可帮助管理员查找这些应用，将其从新策略中免除，并继续向租户的其余部分推出更改。

1. 选择最初要向其推出此更改的域。  这将是你的测试域，因此，请选择一个可能更容易接受 UX （即查看不同的登录页）更改的域。  这将忽略使用此域名的所有应用程序的所有域提示。 在租户的默认 HRD 策略中[设置](#configuring-policy-through-graph-explorer)此策略：

```json
 "DomainHintPolicy": { 
    "IgnoreDomainHintForDomains": [ "testDomain.com" ], 
    "RespectDomainHintForDomains": [], 
    "IgnoreDomainHintForApps": [], 
    "RespectDomainHintForApps": [] 
} 
```

2. 收集来自测试域用户的反馈。 收集由于此更改而中断的应用程序的详细信息 - 它们依赖于域提示使用情况，并且应进行更新。 现在，请将它们添加到 `RespectDomainHintForApps` 部分：

```json
 "DomainHintPolicy": { 
    "IgnoreDomainHintForDomains": [ "testDomain.com" ], 
    "RespectDomainHintForDomains": [], 
    "IgnoreDomainHintForApps": [], 
    "RespectDomainHintForApps": ["app1-clientID-Guid", "app2-clientID-Guid] 
} 
```

3. 继续将策略的推出扩展到新域，并收集更多反馈。

```json
 "DomainHintPolicy": { 
    "IgnoreDomainHintForDomains": [ "testDomain.com", "otherDomain.com", "anotherDomain.com"], 
    "RespectDomainHintForDomains": [], 
    "IgnoreDomainHintForApps": [], 
    "RespectDomainHintForApps": ["app1-clientID-Guid", "app2-clientID-Guid] 
} 
```

4. 完成面向所有域的推出，豁免那些应该继续加速的域：

```json
 "DomainHintPolicy": { 
    "IgnoreDomainHintForDomains": [ "*" ], 
    "RespectDomainHintForDomains": ["guestHandlingDomain.com"], 
    "IgnoreDomainHintForApps": [], 
    "RespectDomainHintForApps": ["app1-clientID-Guid", "app2-clientID-Guid] 
} 
```

完成步骤 4 后，除了 `guestHandlingDomain.com` 中的用户外，所有用户都可以在 Azure AD 登录页登录，即使域提示会导致对联合 IDP 的自动加速。  这种情况的例外是，如果请求登录的应用是已豁免的应用之一，对于这些应用，仍将接受所有域提示。

## <a name="configuring-policy-through-graph-explorer"></a>通过 Graph 浏览器配置策略

使用 Microsoft Graph 按常规方式设置 [HRD 策略](/graph/api/resources/homeRealmDiscoveryPolicy)。  

1. 在 [Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) 中授予 Policy.ReadWrite.ApplicationConfiguration 权限。  
1. 使用 URL `https://graph.microsoft.com/v1.0/policies/homeRealmDiscoveryPolicies`
1. 将新策略发布到此 URL，或者如果要覆盖现有策略，请将其修补到 `/policies/homerealmdiscoveryPolicies/{policyID}`。

发布或修补内容：

```json
{
    "displayName":"Home Realm Discovery Domain Hint Exclusion Policy",
    "definition":[
        "{\"HomeRealmDiscoveryPolicy\" : {\"DomainHintPolicy\": { \"IgnoreDomainHintForDomains\": [ \"Contoso.com\" ], \"RespectDomainHintForDomains\": [], \"IgnoreDomainHintForApps\": [\"sample-guid-483c-9dea-7de4b5d0a54a\"], \"RespectDomainHintForApps\": [] } } }"
    ],
    "isOrganizationDefault":true
}
```

使用 Graph 时，请务必使用斜杠来转义 `Definition` JSON 部分。  

`isOrganizationDefault` 必须为 true，但 displayName 和定义可以更改。

## <a name="next-steps"></a>后续步骤

* [启用无密码安全密钥登录](../authentication/howto-authentication-passwordless-security-key.md)
* [启用 Microsoft Authenticator 应用的无密码登录](../authentication/howto-authentication-passwordless-phone.md)
