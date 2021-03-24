---
title: 处理 MSAL for iOS/macOS 中的错误和异常
titleSuffix: Microsoft identity platform
description: 了解如何处理 MSAL for iOS/macOS 应用程序中的错误和异常、条件访问声明质询以及重试。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/26/2020
ms.author: marsma
ms.reviewer: saeeda, oldalton
ms.custom: aaddev
ms.openlocfilehash: f7f2abadb7586e1b19168eb46a54bad53caa05cc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "98761103"
---
# <a name="handle-errors-and-exceptions-in-msal-for-iosmacos"></a>处理 MSAL for iOS/macOS 中的错误和异常

[!INCLUDE [Active directory error handling introduction](../../../includes/active-directory-develop-error-handling-introduction.md)]

## <a name="error-handling-in-msal-for-iosmacos"></a>MSAL for iOS/macOS 中的错误处理

[MSALError 枚举](https://github.com/AzureAD/microsoft-authentication-library-for-objc/blob/master/MSAL/src/public/MSALError.h#L128)中列出了适用于 iOS 和 macOS 的 MSAL 完整错误列表。

生成的所有 MSAL 错误都以 `MSALErrorDomain` 域返回。

对于系统错误，MSAL 会从系统 API 返回原始 `NSError`。 例如，如果由于缺少网络连接，令牌获取失败，则 MSAL 会返回包含 `NSURLErrorDomain` 域和 `NSURLErrorNotConnectedToInternet` 代码的错误。

建议在客户端上至少处理以下两个 MSAL 错误：

- `MSALErrorInteractionRequired`：用户必须执行交互式请求。 很多条件都可能会导致此错误，例如身份验证会话过期或需要额外的身份验证要求。 调用 MSAL 交互式令牌获取 API 以进行恢复。 

- `MSALErrorServerDeclinedScopes`：部分或全部作用域已被拒绝。 决定是继续使用唯一允许的作用域，还是停止登录过程。

> [!NOTE]
> `MSALInternalError` 枚举应仅用于引用和调试。 请勿尝试在运行时自动处理这些错误。 如果应用遇到属于 `MSALInternalError` 的任何错误，则可能希望显示一条面向用户的通用消息，说明所发生的情况。

例如，`MSALInternalErrorBrokerResponseNotReceived` 表示用户未完成身份验证并已手动返回到应用。 在这种情况下，应用应显示一条通用错误消息，说明身份验证未完成，并建议他们尝试再次进行身份验证。

以下 Objective-C 示例代码演示了处理某些常见错误状况的最佳做法。

```objc
    MSALInteractiveTokenParameters *interactiveParameters = ...;
    MSALSilentTokenParameters *silentParameters = ...;
    
    MSALCompletionBlock completionBlock;
    __block __weak MSALCompletionBlock weakCompletionBlock;
    
    weakCompletionBlock = completionBlock = ^(MSALResult *result, NSError *error)
    {
        if (!error)
        {
            // Use result.accessToken
            NSString *accessToken = result.accessToken;
            return;
        }
        
        if ([error.domain isEqualToString:MSALErrorDomain])
        {
            switch (error.code)
            {
                case MSALErrorInteractionRequired:
                {
                    // Interactive auth will be required
                    [application acquireTokenWithParameters:interactiveParameters
                                            completionBlock:weakCompletionBlock];
                    
                    break;
                }
                    
                case MSALErrorServerDeclinedScopes:
                {
                    // These are list of granted and declined scopes.
                    NSArray *grantedScopes = error.userInfo[MSALGrantedScopesKey];
                    NSArray *declinedScopes = error.userInfo[MSALDeclinedScopesKey];
                    
                    // To continue acquiring token for granted scopes only, do the following
                    silentParameters.scopes = grantedScopes;
                    [application acquireTokenSilentWithParameters:silentParameters
                                                  completionBlock:weakCompletionBlock];
                    
                    // Otherwise, instead, handle error fittingly to the application context
                    break;
                }
                    
                case MSALErrorServerProtectionPoliciesRequired:
                {
                    // Integrate the Intune SDK and call the
                    // remediateComplianceForIdentity:silent: API.
                    // Handle this error only if you integrated Intune SDK.
                    // See more info here: https://aka.ms/intuneMAMSDK
                    
                    break;
                }
                    
                case MSALErrorUserCanceled:
                {
                    // The user cancelled the web auth session.
                    // You may want to ask the user to try again.
                    // Handling of this error is optional.
                    
                    break;
                }
                    
                case MSALErrorInternal:
                {
                    // Log the error, then inspect the MSALInternalErrorCodeKey
                    // in the userInfo dictionary.
                    // Display generic error message to the end user
                    // More detailed information about the specific error
                    // under MSALInternalErrorCodeKey can be found in MSALInternalError enum.
                    NSLog(@"Failed with error %@", error);
                    
                    break;
                }
                    
                default:
                    NSLog(@"Failed with unknown MSAL error %@", error);
                    
                    break;
            }
            
            return;
        }
        
        // Handle no internet connection.
        if ([error.domain isEqualToString:NSURLErrorDomain] && error.code == NSURLErrorNotConnectedToInternet)
        {
            NSLog(@"No internet connection.");
            return;
        }
        
        // Other errors may require trying again later,
        // or reporting authentication problems to the user.
        NSLog(@"Failed with error %@", error);
    };
    
    // Acquire token silently
    [application acquireTokenSilentWithParameters:silentParameters
                                  completionBlock:completionBlock];

     // or acquire it interactively.
     [application acquireTokenWithParameters:interactiveParameters
                             completionBlock:completionBlock];
```

```swift
    let interactiveParameters: MSALInteractiveTokenParameters = ...
    let silentParameters: MSALSilentTokenParameters = ...
            
    var completionBlock: MSALCompletionBlock!
    completionBlock = { (result: MSALResult?, error: Error?) in
                
        if let result = result
        {
            // Use result.accessToken
            let accessToken = result.accessToken
            return
        }

        guard let error = error as NSError? else { return }

        if error.domain == MSALErrorDomain, let errorCode = MSALError(rawValue: error.code)
        {
            switch errorCode
            {
                case .interactionRequired:
                    // Interactive auth will be required
                    application.acquireToken(with: interactiveParameters, completionBlock: completionBlock)

                case .serverDeclinedScopes:
                    let grantedScopes = error.userInfo[MSALGrantedScopesKey]
                    let declinedScopes = error.userInfo[MSALDeclinedScopesKey]

                    if let scopes = grantedScopes as? [String] {
                        silentParameters.scopes = scopes
                        application.acquireTokenSilent(with: silentParameters, completionBlock: completionBlock)
                    }
                        
                    case .serverProtectionPoliciesRequired:
                        // Integrate the Intune SDK and call the
                        // remediateComplianceForIdentity:silent: API.
                        // Handle this error only if you integrated Intune SDK.
                        // See more info here: https://aka.ms/intuneMAMSDK
                        break
                        
                    case .userCanceled:
                       // The user cancelled the web auth session.
                       // You may want to ask the user to try again.
                       // Handling of this error is optional.
                       break
                        
                    case .internal:
                        // Log the error, then inspect the MSALInternalErrorCodeKey
                        // in the userInfo dictionary.
                        // Display generic error message to the end user
                        // More detailed information about the specific error
                        // under MSALInternalErrorCodeKey can be found in MSALInternalError enum.
                        print("Failed with error \(error)");
                        
                    default:
                        print("Failed with unknown MSAL error \(error)")
            }
        }
                
        // Handle no internet connection.
        if error.domain == NSURLErrorDomain && error.code == NSURLErrorNotConnectedToInternet
        {
            print("No internet connection.")
            return
        }
                
        // Other errors may require trying again later,
        // or reporting authentication problems to the user.
        print("Failed with error \(error)");    
    }
   
    // Acquire token silently
    application.acquireToken(with: interactiveParameters, completionBlock: completionBlock)
 
    // or acquire it interactively.
    application.acquireTokenSilent(with: silentParameters, completionBlock: completionBlock)
```

---

[!INCLUDE [Active directory error handling claims challenges](../../../includes/active-directory-develop-error-handling-claims-challenges.md)]

借助适用于 iOS 和 macOS 的 MSAL，你可以在交互式和静默令牌获取方案中请求特定的声明。

要请求自定义声明，请在 `MSALSilentTokenParameters` 或 `MSALInteractiveTokenParameters` 中指定 `claimsRequest`。

有关详细信息，请参阅[使用适用于 iOS 和 macOS 的 MSAL 请求自定义声明](request-custom-claims.md)。

[!INCLUDE [Active directory error handling retries](../../../includes/active-directory-develop-error-handling-retries.md)]

## <a name="next-steps"></a>后续步骤

请考虑启用 [ MSAL for iOS/macOS 中的日志记录](msal-logging-ios.md)，以帮助你诊断和调试问题。
