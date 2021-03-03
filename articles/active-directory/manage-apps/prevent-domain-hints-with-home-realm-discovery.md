---
title: 使用主领域发现策略阻止在 Azure AD 中登录自动加速
description: 了解如何防止 domain_hint 自动加速到联合 Idp。
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 02/12/2021
ms.author: hirsin
ms.openlocfilehash: 67cb1003e139a085d45d01617cd44647bad420f5
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101693007"
---
# <a name="disable-auto-acceleration-to-a-federated-idp-during-user-sign-in-with-home-realm-discovery-policy"></a>在使用主领域发现策略的用户登录过程中禁用到联合 IDP 的自动加速

[主领域发现策略](https://docs.microsoft.com/graph/api/resources/homeRealmDiscoveryPolicy) (HRD) 为管理员提供多种方式来控制其用户身份验证的方式和位置。 `domainHintPolicy`HRD 策略的部分用于帮助将联合用户迁移到云托管凭据（如[FIDO](../authentication/howto-authentication-passwordless-security-key.md)），方法是确保它们始终访问 Azure AD 登录页，且不会因域提示而自动加速到联合 IDP。

此策略在以下情况下需要：管理员无法在登录期间控制或更新添加域提示。  例如， `outlook.com/contoso.com` 将用户发送到追加了参数的登录页 `&domain_hint=contoso.com` ，以便将用户直接自动加速到域的联合 IDP `contoso.com` 。 使用发送到联合 IDP 的托管凭据的用户不能使用其托管凭据登录，从而降低安全性并使用户具有随机登录体验。 管理员推出托管凭据 [还应设置此策略](#suggested-use-within-a-tenant) ，以确保用户始终可以使用其托管凭据。

## <a name="domainhintpolicy-details"></a>DomainHintPolicy 详细信息

HRD 策略的 DomainHintPolicy 部分是一个 JSON 对象，它允许管理员从域提示使用中选择退出某些域和应用程序。  在功能上，这会告诉 Azure AD 登录页的行为就像 `domain_hint` 登录请求上的参数不存在一样。

### <a name="the-respect-and-ignore-policy-sections"></a>"尊重和忽略策略" 部分

|部分 | 含义 | 值 |
|--------|---------|--------|
|`IgnoreDomainHintForDomains` |如果在请求中发送此域提示，则忽略它。 |域地址的数组 (例如 `contoso.com`) 。 还支持 `all_domains`|
|`RespectDomainHintForDomains`| 如果此域提示是在请求中发送的，则对其进行尊重，即使 `IgnoreDomainHintForApps` 指示请求中的应用不应自动加速。 这用于减缓网络内的弃用域提示的推出–你可以指示某些域应仍为已加速。 | 域地址的数组 (例如 `contoso.com`) 。 还支持 `all_domains`|
|`IgnoreDomainHintForApps`| 如果来自此应用程序的请求附带了域提示，请将其忽略。 | Guid)  (的应用程序 Id 的数组。 还支持 `all_apps`|
|`RespectDomainHintForApps` |如果来自此应用程序的请求附带了域提示，则即使其中包含该域也是如此 `IgnoreDomainHintForDomains` 。 用于确保某些应用在没有域提示的情况下中断时继续工作。 | Guid)  (的应用程序 Id 的数组。 还支持 `all_apps`|

### <a name="policy-evaluation"></a>策略评估

DomainHintPolicy 逻辑在包含域提示的每个传入请求上运行，并基于请求中的两个数据片段（域提示中的域，以及应用)  (的客户端 ID 进行加速。 对于域或应用程序，"尊重" 优先于指定域或应用程序的 "忽略" 域提示。

1. 在没有任何域提示策略的情况下，或者如果4个部分都未引用所述的应用或域提示， [将评估其余的 HRD 策略](configure-authentication-for-federated-users-portal.md#priority-and-evaluation-of-hrd-policies)。
1. 如果或部分中有一项 (或全部) 都 `RespectDomainHintForApps` `RespectDomainHintForDomains` 包含请求中的应用或域提示，则用户将根据请求自动加速到联合 IDP。
1. 如果两个或其中一个 (或) 同时 `IgnoreDomainHintsForApps` `IgnoreDomainHintsForDomains` 引用请求中的应用或域提示，且未被 "尊重" 部分所引用，则该请求将不会自动加速，用户将保留在 Azure AD 登录页面以提供用户名。

用户在登录页上输入用户名后，他们可以使用其托管凭据（如果有）。  如果他们选择不使用托管凭据，或它们都未注册，则会将它们视为其联合 IDP 的凭据条目。

## <a name="suggested-use-within-a-tenant"></a>租户内的建议使用

联合域的管理员应在四阶段计划中设置 HRD 策略的此部分。 此计划的目标是最终让租户中的所有用户都有机会使用其托管凭据，而不考虑域或应用程序，请保存对使用情况很难依赖的应用 `domain_hint` 。  此计划可帮助管理员查找这些应用，将其从新策略中免除，并继续向租户的其余部分推出更改。

1. 选择最初要将此更改的滚动到的域。  这将是你的测试域，因此，请选择一个可能更 receptive 的 (更改（例如，查看不同的登录页面) ）。  这将忽略使用此域名的所有应用程序的所有域提示。 在租户中[设置](#configuring-policy-through-graph-explorer)此策略-默认 HRD 策略：

```json
 "DomainHintPolicy": { 
    "IgnoreDomainHintForDomains": [ "testDomain.com" ], 
    "RespectDomainHintForDomains": [], 
    "IgnoreDomainHintForApps": [], 
    "RespectDomainHintForApps": [] 
} 
```

2. 收集来自测试域用户的反馈。 收集由于此更改而中断的应用程序的详细信息-它们依赖于域提示用法，并且应进行更新。 现在，请将它们添加到 `RespectDomainHintForApps` 部分：

```json
 "DomainHintPolicy": { 
    "IgnoreDomainHintForDomains": [ "testDomain.com" ], 
    "RespectDomainHintForDomains": [], 
    "IgnoreDomainHintForApps": [], 
    "RespectDomainHintForApps": ["app1-clientID-Guid", "app2-clientID-Guid] 
} 
```

3. 继续将策略部署扩展到新域，并收集更多反馈。

```json
 "DomainHintPolicy": { 
    "IgnoreDomainHintForDomains": [ "testDomain.com", "otherDomain.com", "anotherDomain.com"], 
    "RespectDomainHintForDomains": [], 
    "IgnoreDomainHintForApps": [], 
    "RespectDomainHintForApps": ["app1-clientID-Guid", "app2-clientID-Guid] 
} 
```

4. 完成你的部署-面向所有域，豁免那些应该继续加快的域：

```json
 "DomainHintPolicy": { 
    "IgnoreDomainHintForDomains": [ "*" ], 
    "RespectDomainHintForDomains": ["guestHandlingDomain.com"], 
    "IgnoreDomainHintForApps": [], 
    "RespectDomainHintForApps": ["app1-clientID-Guid", "app2-clientID-Guid] 
} 
```

完成步骤4后，除了中的用户外，所有用户 `guestHandlingDomain.com` 都可以在 Azure AD 登录页登录，即使域提示会导致联合 IDP 自动加速。  这种情况的例外是，如果请求登录的应用是已免除的应用之一（对于这些应用），仍将接受所有域提示。

## <a name="configuring-policy-through-graph-explorer"></a>通过图形资源管理器配置策略

使用 Microsoft Graph 按常规方式设置 [HRD 策略](https://docs.microsoft.com/graph/api/resources/homeRealmDiscoveryPolicy) 。  

1. 在 [图形资源管理器](https://developer.microsoft.com/graph/graph-explorer)中授予 ApplicationConfiguration 权限。  
1. 使用 URL `https://graph.microsoft.com/v1.0/policies/homeRealmDiscoveryPolicies`
1. 将新策略发布到此 URL，或将其修补为 `/policies/homerealmdiscoveryPolicies/{policyID}` 覆盖现有策略。

POST 或 PATCH 内容：

```json
{
    "displayName":"Home Realm Discovery Domain Hint Exclusion Policy",
    "definition":[
        "{\"HomeRealmDiscoveryPolicy\" : {\"DomainHintPolicy\": { \"IgnoreDomainHintForDomains\": [ \"Contoso.com\" ], \"RespectDomainHintForDomains\": [], \"IgnoreDomainHintForApps\": [\"sample-guid-483c-9dea-7de4b5d0a54a\"], \"RespectDomainHintForApps\": [] } } }"
    ],
    "isOrganizationDefault":true
}
```

使用 "图形" 时，请务必使用斜杠来转义 `Definition` JSON 部分。  

`isOrganizationDefault` 必须为 true，但 displayName 和定义可能会更改。

## <a name="next-steps"></a>后续步骤

* [启用无密码安全密钥登录](../authentication/howto-authentication-passwordless-security-key.md)
* [启用 Microsoft Authenticator 应用的无密码登录](../authentication/howto-authentication-passwordless-phone.md)
