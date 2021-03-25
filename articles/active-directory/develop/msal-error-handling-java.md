---
title: 处理 MSAL4J 中的错误和异常
titleSuffix: Microsoft identity platform
description: 了解如何处理 MSAL4J 应用程序中的错误和异常、条件访问声明质询以及重试。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/27/2020
ms.author: marsma
ms.reviewer: saeeda, nacanuma
ms.custom: aaddev
ms.openlocfilehash: 8563804a736c37acc9a96eb4a186933507f34200
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "98760672"
---
# <a name="handle-errors-and-exceptions-in-msal-for-java"></a>处理 MSAL for Java 中的错误和异常

[!INCLUDE [Active directory error handling introduction](../../../includes/active-directory-develop-error-handling-introduction.md)]

## <a name="error-handling-in-msal-for-java"></a>MSAL for Java 中的错误处理

在适用于 Java 的 MSAL 中，有三种类型的异常：`MsalClientException`、`MsalServiceException` 和 `MsalInteractionRequiredException`；这些异常均继承自 `MsalException`。

- 当库或设备发生本地错误时，将引发 `MsalClientException`。
- 当安全令牌服务 (STS) 返回错误响应或发生其他网络错误时，将引发 `MsalServiceException`。
- 当需要 UI 交互才能成功进行身份验证时，将引发 `MsalInteractionRequiredException`。

### <a name="msalserviceexception"></a>MsalServiceException

`MsalServiceException` 公开在对 STS 的请求中返回的 HTTP 标头。 通过 `MsalServiceException.headers()` 访问它们

### <a name="msalinteractionrequiredexception"></a>MsalInteractionRequiredException

调用 `AcquireTokenSilently()` 时，从适用于 Java 的 MSAL 返回的其中一个常见状态代码是 `InvalidGrantError`。 这表示在颁发身份验证令牌之前，需要进行其他用户交互。 应用程序应再次调用身份验证库，但应在交互模式下，通过向公共客户端应用程序发送 `AuthorizationCodeParameters` 或 `DeviceCodeParameters` 来实现。

大多数情况下，`AcquireTokenSilently` 失败的原因是，令牌缓存没有与请求匹配的令牌。 访问令牌在 1 小时后过期，`AcquireTokenSilently` 将尝试基于刷新令牌获取新的令牌。 在 OAuth2 术语中，这是刷新令牌流。 此流也可能因各种原因而失败，例如，当租户管理员配置了更严格的登录策略时。

导致此错误的某些条件对于用户来说很容易解决。 例如，他们可能需要接受使用条款，或者无法使用当前配置来实现请求，因为计算机需要连接到特定的企业网络。

MSAL 公开了一个 `reason` 字段，可用于提供更好的用户体验。 例如，你可以使用 `reason` 字段来告知用户其密码已过期，或者告知他们需要提供许可才能使用某些资源。 支持的值是 `InteractionRequiredExceptionReason` 枚举的一部分：

| 原因 | 含义 | 建议的处理方式 |
|---------|-----------|-----------------------------|
| `BasicAction` | 在交互式身份验证流中，条件可以通过用户交互来解决。 | 使用交互式参数调用 `acquireToken`。 |
| `AdditionalAction` | 在交互式身份验证流之外，条件可以通过与系统进行其他补救交互来解决。 | 通过交互式参数调用 `acquireToken`，以显示一条说明要采取的补救操作的消息。 如果用户不太可能完成补救操作，则调用应用可能会选择隐藏需要其他操作的流。 |
| `MessageOnly` | 条件目前无法解决。 启动交互式身份验证流以显示一条说明条件的消息。 | 通过交互式参数调用 `acquireToken` 以显示一条说明条件的消息。 用户读取该消息并关闭窗口后，`acquireToken` 将返回 `UserCanceled` 错误。 如果用户不太可能从该消息中获益，则应用可能会选择隐藏导致该消息的流。 |
| `ConsentRequired`| 用户许可缺失或已撤销。 |通过交互式参数调用 `acquireToken`，以获得用户许可。 |
| `UserPasswordExpired` | 用户的密码已过期。 | 使用交互式参数调用 `acquireToken`，使用户能够重置其密码。 |
| `None` |  已提供更多详细信息。 在交互式身份验证流中，条件可以通过用户交互来解决。 | 使用交互式参数调用 `acquireToken`。 |

### <a name="code-example"></a>代码示例

```java
        IAuthenticationResult result;
        try {
            PublicClientApplication application = PublicClientApplication
                    .builder("clientId")
                    .b2cAuthority("authority")
                    .build();

            SilentParameters parameters = SilentParameters
                    .builder(Collections.singleton("scope"))
                    .build();

            result = application.acquireTokenSilently(parameters).join();
        }
        catch (Exception ex){
            if(ex instanceof MsalInteractionRequiredException){
                // AcquireToken by either AuthorizationCodeParameters or DeviceCodeParameters
            } else{
                // Log and handle exception accordingly
            }
        }
```

[!INCLUDE [Active directory error handling claims challenges](../../../includes/active-directory-develop-error-handling-claims-challenges.md)]

[!INCLUDE [Active directory error handling retries](../../../includes/active-directory-develop-error-handling-retries.md)]

## <a name="next-steps"></a>后续步骤

请考虑[在 MSAL for Java 中启用日志记录](msal-logging-java.md)，以帮助你诊断和调试问题。
