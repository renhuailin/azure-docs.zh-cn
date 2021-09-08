---
title: 使用 Azure Active Directory B2C 启用 Android 移动应用程序选项
description: 本文讨论使用 Azure Active Directory B2C 启用 Android 移动应用程序选项的几种方法。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 07/05/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: c66d2faead9b14eb665622e1a710afd94b2ddd0f
ms.sourcegitcommit: ef448159e4a9a95231b75a8203ca6734746cd861
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2021
ms.locfileid: "123220677"
---
# <a name="configure-authentication-options-in-an-android-app-by-using-azure-ad-b2c"></a>使用 Azure AD B2C 在 Android 应用中配置验证选项 

本文介绍如何启用、自定义和增强 Android 应用程序的 Azure Active Directory B2C (Azure AD B2C) 身份验证体验。 

在开始之前，请先熟悉以下文章： 
* [使用 Azure AD B2C 在示例 Android 应用中配置身份验证](configure-authentication-sample-android-app.md)
* [使用 Azure AD B2C 在你自己的 Android 应用中启用身份验证](enable-authentication-android-app.md)

[!INCLUDE [active-directory-b2c-app-integration-custom-domain](../../includes/active-directory-b2c-app-integration-custom-domain.md)]

若要在身份验证 URL 中使用自定义域和租户 ID，请按照[启用自定义域](custom-domain.md)中的指南进行操作。 查找 Microsoft 身份验证库 (MSAL) 配置对象，然后使用自定义域名和租户 ID 来更新颁发机构。


#### <a name="kotlin"></a>[Kotlin](#tab/kotlin)

以下 Kotlin 代码显示更改前的 MSAL 配置对象：

```kotlin
val parameters = AcquireTokenParameters.Builder()
        .startAuthorizationFromActivity(activity)
        .fromAuthority("https://contoso.b2clogin.com/fabrikamb2c.contoso.com/B2C_1_susi")
        // More settings here
        .build()

b2cApp!!.acquireToken(parameters)
```

以下 Kotlin 代码显示更改后的 MSAL 配置对象：

```kotlin
val parameters = AcquireTokenParameters.Builder()
        .startAuthorizationFromActivity(activity)
        .fromAuthority("https://custom.domain.com/00000000-0000-0000-0000-000000000000/B2C_1_susi")
        // More settings here
        .build()

b2cApp!!.acquireToken(parameters)
```


#### <a name="java"></a>[Java](#tab/java)

以下 Kotlin 代码显示更改前的 MSAL 配置对象：

```java
AcquireTokenParameters parameters = new AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(getActivity())
    .fromAuthority("https://contoso.b2clogin.com/fabrikamb2c.contoso.com/B2C_1_susi")
    // More settings here
    .build();

b2cApp.acquireToken(parameters);
```
以下 Kotlin 代码显示更改后的 MSAL 配置对象：

```java
AcquireTokenParameters parameters = new AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(getActivity())
    .fromAuthority("https://custom.domain.com/00000000-0000-0000-0000-000000000000/B2C_1_susi")
    // More settings here
    .build();

b2cApp.acquireToken(parameters);
```

--- 

[!INCLUDE [active-directory-b2c-app-integration-login-hint](../../includes/active-directory-b2c-app-integration-login-hint.md)]

1. 如果使用的是自定义策略，请按照[设置直接登录](direct-signin.md#prepopulate-the-sign-in-name)中的说明添加所需的输入声明。 
1. 查找 MSAL 配置对象，然后添加带有登录提示的 `withLoginHint()` 方法。

#### <a name="kotlin"></a>[Kotlin](#tab/kotlin)


```kotlin
val parameters = AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(activity)
    .withLoginHint("bob@contoso.com") 
    // More settings here
    .build()

b2cApp!!.acquireToken(parameters)
```

#### <a name="java"></a>[Java](#tab/java)

```java
AcquireTokenParameters parameters = new AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(getActivity())
    .withLoginHint("bob@contoso.com")  
    // More settings here
    .build();

b2cApp.acquireToken(parameters);

```

--- 

[!INCLUDE [active-directory-b2c-app-integration-domain-hint](../../includes/active-directory-b2c-app-integration-domain-hint.md)]

1. 检查外部标识提供者的域名。 有关详细信息，请参阅[将登录重定向到社交服务提供商](direct-signin.md#redirect-sign-in-to-a-social-provider)。 
1. 创建或使用现有列表对象来存储额外的查询参数。
1. 将具有相应域名的 `domain_hint` 参数添加到列表中（如 `facebook.com`）。
1. 将额外的查询参数列表传递到 MSAL 配置对象的 `withAuthorizationQueryStringParameters` 方法中。

#### <a name="kotlin"></a>[Kotlin](#tab/kotlin)

```kotlin
val extraQueryParameters: MutableList<Pair<String, String>> = ArrayList()
extraQueryParameters.add(Pair("domain_hint", "facebook.com"))

val parameters = AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(activity)
    .withAuthorizationQueryStringParameters(extraQueryParameters) 
    // More settings here
    .build()

b2cApp!!.acquireToken(parameters)
```

#### <a name="java"></a>[Java](#tab/java)

```java
List<Pair<String, String>> extraQueryParameters = new ArrayList<>();
extraQueryParameters.add( new Pair<String, String>("domain_hint", "facebook.com"));

AcquireTokenParameters parameters = new AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(getActivity())
    .withAuthorizationQueryStringParameters(extraQueryParameters) 
    // More settings here
    .build();

b2cApp.acquireToken(parameters);
```

--- 

[!INCLUDE [active-directory-b2c-app-integration-ui-locales](../../includes/active-directory-b2c-app-integration-ui-locales.md)]

1. [配置语言自定义](language-customization.md)。
1. 创建或使用现有列表对象来存储额外的查询参数。
1. 将具有相应语言代码的 `ui_locales` 参数添加到列表中（如 `en-us`）。
1. 将额外的查询参数列表传递到 MSAL 配置对象的 `withAuthorizationQueryStringParameters` 方法中。

#### <a name="kotlin"></a>[Kotlin](#tab/kotlin)

```kotlin
val extraQueryParameters: MutableList<Pair<String, String>> = ArrayList()
extraQueryParameters.add(Pair("ui_locales", "en-us"))

val parameters = AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(activity)
    .withAuthorizationQueryStringParameters(extraQueryParameters) 
    // More settings here
    .build()

b2cApp!!.acquireToken(parameters)
```

#### <a name="java"></a>[Java](#tab/java)

```java
List<Pair<String, String>> extraQueryParameters = new ArrayList<>();
extraQueryParameters.add( new Pair<String, String>("ui_locales", "en-us"));

AcquireTokenParameters parameters = new AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(getActivity())
    .withAuthorizationQueryStringParameters(extraQueryParameters) 
    // More settings here
    .build();

b2cApp.acquireToken(parameters);
```

--- 

[!INCLUDE [active-directory-b2c-app-integration-custom-parameters](../../includes/active-directory-b2c-app-integration-custom-parameters.md)]

1. 配置 [ContentDefinitionParameters](customize-ui-with-html.md#configure-dynamic-custom-page-content-uri) 元素。
1. 创建或使用现有列表对象来存储额外的查询参数。
1. 添加自定义查询字符串参数（如 `campaignId`）。 设置参数值（如 `germany-promotion`）。
1. 将额外的查询参数列表传递到 MSAL 配置对象的 `withAuthorizationQueryStringParameters` 方法中。

#### <a name="kotlin"></a>[Kotlin](#tab/kotlin)

```kotlin
val extraQueryParameters: MutableList<Pair<String, String>> = ArrayList()
extraQueryParameters.add(Pair("campaignId", "germany-promotion"))

val parameters = AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(activity)
    .withAuthorizationQueryStringParameters(extraQueryParameters) 
    // More settings here
    .build()

b2cApp!!.acquireToken(parameters)
```

#### <a name="java"></a>[Java](#tab/java)

```java
List<Pair<String, String>> extraQueryParameters = new ArrayList<>();
extraQueryParameters.add( new Pair<String, String>("campaignId", "germany-promotion"));

AcquireTokenParameters parameters = new AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(getActivity())
    .withAuthorizationQueryStringParameters(extraQueryParameters) 
    // More settings here
    .build();

b2cApp.acquireToken(parameters);
```

--- 

[!INCLUDE [active-directory-b2c-app-integration-id-token-hint](../../includes/active-directory-b2c-app-integration-id-token-hint.md)]

1. 在自定义策略中，定义 [ID 令牌提示技术配置文件](id-token-hint.md)。
1. 在代码中，生成或获取 ID 令牌，然后将令牌设置为变量（如 `idToken`）。 
1. 创建或使用现有列表对象来存储额外的查询参数。
1. 使用可存储 ID 令牌的相应变量添加 `id_token_hint` 参数。
1. 将额外的查询参数列表传递到 MSAL 配置对象的 `withAuthorizationQueryStringParameters` 方法中。

#### <a name="kotlin"></a>[Kotlin](#tab/kotlin)

```kotlin
val extraQueryParameters: MutableList<Pair<String, String>> = ArrayList()
extraQueryParameters.add(Pair("id_token_hint", idToken))

val parameters = AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(activity)
    .withAuthorizationQueryStringParameters(extraQueryParameters) 
    // More settings here
    .build()

b2cApp!!.acquireToken(parameters)
```

#### <a name="java"></a>[Java](#tab/java)

```java
List<Pair<String, String>> extraQueryParameters = new ArrayList<>();
extraQueryParameters.add( new Pair<String, String>("id_token_hint", idToken));

AcquireTokenParameters parameters = new AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(getActivity())
    .withAuthorizationQueryStringParameters(extraQueryParameters) 
    // More settings here
    .build();

b2cApp.acquireToken(parameters);
```

--- 



## <a name="next-steps"></a>后续步骤

- 若要详细了解 Android 配置，请参阅[适用于 Android 的 MSAL 配置选项](https://github.com/AzureAD/microsoft-authentication-library-for-android/wiki)。
