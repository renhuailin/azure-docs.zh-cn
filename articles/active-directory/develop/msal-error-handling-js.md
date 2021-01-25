---
title: 处理 MSAL.js 中的错误和异常
titleSuffix: Microsoft identity platform
description: 了解如何处理 MSAL.js 应用程序中的错误和异常、条件性访问声明质询和重试。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/26/2020
ms.author: marsma
ms.reviewer: saeeda, hahamil
ms.custom: aaddev
ms.openlocfilehash: 20d276aba2ee3260911748cbee0a16020270059a
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/25/2021
ms.locfileid: "98761332"
---
# <a name="handle-errors-and-exceptions-in-msaljs"></a>处理 MSAL.js 中的错误和异常

[!INCLUDE [Active directory error handling introduction](../../../includes/active-directory-develop-error-handling-introduction.md)]

## <a name="error-handling-in-msaljs"></a>MSAL.js 中的错误处理

MSAL.js 提供了一些错误对象，这些对象可对不同类型的常见错误进行抽象和分类。 它还提供了一个接口用于访问错误的特定详细信息，例如错误消息处理适当的错误消息。

### <a name="error-object"></a>错误对象

```javascript
export class AuthError extends Error {
    // This is a short code describing the error
    errorCode: string;
    // This is a descriptive string of the error,
    // and may also contain the mitigation strategy
    errorMessage: string;
    // Name of the error class
    this.name = "AuthError";
}
```

通过扩展 error 类可以访问以下属性：
- `AuthError.message`：与 `errorMessage` 相同。
- `AuthError.stack`：引发的错误的堆栈跟踪。

### <a name="error-types"></a>错误类型

可用的错误类型如下：

- `AuthError`：MSAL.js 库的基本 error 类，也可用于意外错误。

- `ClientAuthError`：指示客户端身份验证存在问题的错误类。 来自库的大多数错误都是 ClientAuthError。 这些错误是由于在登录过程中调用 login 方法、用户取消登录等原因造成的。

- `ClientConfigurationError`：当给定的用户配置参数格式不当或缺失时，扩展发出请求之前所引发 `ClientAuthError` 的错误类。

- `ServerError`：表示身份验证服务器所发送错误字符串的错误类。 这些错误可能包括：请求格式或参数无效，或者阻止服务器对用户进行身份验证或授权的任何其他错误。

- `InteractionRequiredAuthError`：扩展 `ServerError` 以表示需要交互式调用的服务器错误的错误类。 如果用户必须与服务器交互才能提供用于进行身份验证/授权的凭据或许可，则 `acquireTokenSilent` 会引发此错误。 错误代码包括 `"interaction_required"`、`"login_required"` 和 `"consent_required"`。

要使用重定向方法（`loginRedirect`、`acquireTokenRedirect`）在身份验证流中处理错误，需要按如下所示注册回调，重定向后，系统会使用 `handleRedirectCallback()` 方法结合成功或失败状态调用该回调：

```javascript
function authCallback(error, response) {
    //handle redirect response
}

var myMSALObj = new Msal.UserAgentApplication(msalConfig);

// Register Callbacks for redirect flow
myMSALObj.handleRedirectCallback(authCallback);
myMSALObj.acquireTokenRedirect(request);
```

弹出体验的方法（`loginPopup`、`acquireTokenPopup`）会返回约定，因此你可以使用约定模式（.then 和 .catch）来处理这些错误，如下所示：

```javascript
myMSALObj.acquireTokenPopup(request).then(
    function (response) {
        // success response
    }).catch(function (error) {
        console.log(error);
    });
```

### <a name="errors-that-require-interaction"></a>需要交互的错误

如果尝试使用一种非交互式方法来获取令牌（如 `acquireTokenSilent`），则将返回错误，但 MSAL 无法以静默方式执行该操作。

可能的原因包括：

- 需要登录
- 需要许可
- 需要经历多重身份验证体验。

补救措施是调用 `acquireTokenPopup` 或 `acquireTokenRedirect` 等交互式方法：

```javascript
// Request for Access Token
myMSALObj.acquireTokenSilent(request).then(function (response) {
    // call API
}).catch( function (error) {
    // call acquireTokenPopup in case of acquireTokenSilent failure
    // due to consent or interaction required
    if (error.errorCode === "consent_required"
    || error.errorCode === "interaction_required"
    || error.errorCode === "login_required") {
        myMSALObj.acquireTokenPopup(request).then(
            function (response) {
                // call API
            }).catch(function (error) {
                console.log(error);
            });
    }
});
```

[!INCLUDE [Active directory error handling claims challenges](../../../includes/active-directory-develop-error-handling-claims-challenges.md)]

使用 MSAL.js 以静默方式获取令牌时（使用 `acquireTokenSilent`），如果你尝试访问的 API 需要[条件访问声明质询](../azuread-dev/conditional-access-dev-guide.md)（例如 MFA 策略），则应用程序可能会收到错误。

处理此错误的模式是发出交互式调用（例如 `acquireTokenPopup` 或 `acquireTokenRedirect`）以获取 MSAL.js 中的令牌，如以下示例所示：

```javascript
myMSALObj.acquireTokenSilent(accessTokenRequest).then(function(accessTokenResponse) {
    // call API
}).catch(function(error) {
    if (error instanceof InteractionRequiredAuthError) {
    
        // extract, if exists, claims from error message
        if (error.ErrorMessage.claims) {
            accessTokenRequest.claimsRequest = JSON.stringify(error.ErrorMessage.claims);
        }
        
        // call acquireTokenPopup in case of InteractionRequiredAuthError failure
        myMSALObj.acquireTokenPopup(accessTokenRequest).then(function(accessTokenResponse) {
            // call API
        }).catch(function(error) {
            console.log(error);
        });
    }
});
```

以交互方式获取令牌会提示用户，并使他们能够满足所需的条件访问策略。

调用需要条件访问的 API 时，API 返回的错误中可能会包含声明质询。 在这种情况下，可以将错误中返回的声明传递给 `AuthenticationParameters.ts` 类的 `claimsRequest` 字段，以满足相应的策略。 

有关更多详细信息，请参阅[请求其他声明](active-directory-optional-claims.md)。


[!INCLUDE [Active directory error handling retries](../../../includes/active-directory-develop-error-handling-retries.md)]

## <a name="next-steps"></a>后续步骤

请考虑启用 [MSAL.js日志记录 ](msal-logging-js.md) ，以帮助你诊断和调试问题。
