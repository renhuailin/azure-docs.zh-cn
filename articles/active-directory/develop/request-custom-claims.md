---
title: 请求自定义声明 (MSAL iOS/macOS) | Azure
titleSuffix: Microsoft identity platform
description: 了解如何请求自定义声明。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 08/26/2019
ms.author: marsma
ms.custom: aaddev
ms.openlocfilehash: 70a883fc1c2cd78124c9efe44a54cc528ee5426c
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123039913"
---
# <a name="request-custom-claims-using-msal-for-ios-and-macos"></a>使用适用于 iOS 和 macOS 的 MSAL 请求自定义声明

通过 OpenID Connect，可以选择请求从 UserInfo 终结点和/或 ID 令牌返回各个声明。 声明请求表示为一个 JSON 对象，该对象包含请求的声明的列表。 有关更多详细信息，请参阅 [OpenID Connect Core 1.0](https://openid.net/specs/openid-connect-core-1_0-final.html#ClaimsParameter)。

适用于 iOS 和 macOS 的 Microsoft 身份验证库 (MSAL) 允许在交互式和无提示令牌获取方案中请求特定声明。 它通过 `claimsRequest` 参数实现此目的。

在多种情况下需要这样做。 例如：

- 在应用程序的标准集外请求声明。
- 请求无法使用应用程序的作用域指定的标准声明的特定组合。 例如，如果访问令牌由于缺少声明而被拒绝，则应用程序可以使用 MSAL 请求缺少的声明。

> [!NOTE]
> 每当指定声明请求时，MSAL 都会绕过访问令牌缓存。 重要的是，仅在需要其他声明时才提供 `claimsRequest` 参数（而不是在每个 MSAL API 调用中始终提供相同的 `claimsRequest` 参数）。

`claimsRequest` 可在 `MSALSilentTokenParameters` 和 `MSALInteractiveTokenParameters`中指定：

```objc
/*!
 MSALTokenParameters is the base abstract class for all types of token parameters (silent and interactive).
 */
@interface MSALTokenParameters : NSObject

/*!
 The claims parameter that needs to be sent to authorization or token endpoint.
 If claims parameter is passed in silent flow, access token will be skipped and refresh token will be tried.
 */
@property (nonatomic, nullable) MSALClaimsRequest *claimsRequest;

@end
```
`MSALClaimsRequest` 可从 JSON 声明请求的 NSString 表示形式构造。 

Objective-C：

```objc
NSError *claimsError = nil;
MSALClaimsRequest *request = [[MSALClaimsRequest alloc] initWithJsonString:@"{\"id_token\":{\"auth_time\":{\"essential\":true},\"acr\":{\"values\":[\"urn:mace:incommon:iap:silver\"]}}}" error:&claimsError];
```

Swift：

```swift
var requestError: NSError? = nil
let request = MSALClaimsRequest(jsonString: "{\"id_token\":{\"auth_time\":{\"essential\":true},\"acr\":{\"values\":[\"urn:mace:incommon:iap:silver\"]}}}",
                                        error: &requestError)
```



也可以通过请求其他特定声明来修改它：

Objective-C：

```objc
MSALIndividualClaimRequest *individualClaimRequest = [[MSALIndividualClaimRequest alloc] initWithName:@"custom_claim"];
individualClaimRequest.additionalInfo = [MSALIndividualClaimRequestAdditionalInfo new];
individualClaimRequest.additionalInfo.essential = @1;
individualClaimRequest.additionalInfo.value = @"myvalue";
[request requestClaim:individualClaimRequest forTarget:MSALClaimsRequestTargetIdToken error:&claimsError];
```

Swift：

```swift
let individualClaimRequest = MSALIndividualClaimRequest(name: "custom-claim")
let additionalInfo = MSALIndividualClaimRequestAdditionalInfo()
additionalInfo.essential = 1
additionalInfo.value = "myvalue"
individualClaimRequest.additionalInfo = additionalInfo
do {
  try request.requestClaim(individualClaimRequest, for: .idToken)
} catch let error as NSError {
  // handle error here  
}
        
```



然后应在令牌参数中设置 `MSALClaimsRequest`，并将其提供给 MSAL 令牌获取 API 之一：

Objective-C：

```objc
MSALPublicClientApplication *application = ...;
MSALWebviewParameters *webParameters = ...;

MSALInteractiveTokenParameters *parameters = [[MSALInteractiveTokenParameters alloc] initWithScopes:@[@"user.read"]
                                                                                  webviewParameters:webParameters];
parameters.claimsRequest = request;
    
[application acquireTokenWithParameters:parameters completionBlock:completionBlock];
```

Swift：

```swift
let application: MSALPublicClientApplication!
let webParameters: MSALWebviewParameters!
        
let parameters = MSALInteractiveTokenParameters(scopes: ["user.read"], webviewParameters: webParameters)
parameters.claimsRequest = request
        
application.acquireToken(with: parameters) { (result: MSALResult?, error: Error?) in            ...

```



## <a name="next-steps"></a>后续步骤

详细了解[身份验证流和应用程序方案](authentication-flows-app-scenarios.md)
