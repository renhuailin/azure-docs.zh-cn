---
title: 记录 MSAL.NET 中的错误和异常
titleSuffix: Microsoft identity platform
description: 了解如何记录 MSAL.NET 中的错误和异常
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 01/25/2021
ms.author: marsma
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ms.openlocfilehash: 518fc85aff920ffece511e383b2322d8e81266ee
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/28/2021
ms.locfileid: "129091856"
---
# <a name="logging-in-msalnet"></a>在 MSAL.NET 中进行日志记录

[!INCLUDE [MSAL logging introduction](../../../includes/active-directory-develop-error-logging-introduction.md)]

## <a name="configure-logging-in-msalnet"></a>在 MSAL.NET 中配置日志记录

在 MSAL 中，使用 `.WithLogging` 生成器修饰符在创建应用程序时设置日志记录。 该方法采用以下可选参数：

- `Level` 用于确定你需要哪种级别的日志记录。 将其设置为“Errors”时，就只会获得错误
- 如果设置为 true，`PiiLoggingEnabled` 用于记录个人和组织数据 (PII)。 默认情况下，此项设置为 false，不允许应用程序记录个人数据。
- `LogCallback` 设置为一个执行日志记录的委托。 如果 `PiiLoggingEnabled` 为 true，此方法将接收可能包含 PII 的消息，在这种情况下 `containsPii` 标志将设置为 true。
- `DefaultLoggingEnabled` 为平台启用默认日志记录。 默认为 false。 如果将它设置为 true，它会在桌面/UWP 应用程序中使用事件跟踪，在 iOS 上使用 NSLog，在 Android 上使用 logcat。

```csharp
class Program
{
  private static void Log(LogLevel level, string message, bool containsPii)
  {
     if (containsPii)
     {
        Console.ForegroundColor = ConsoleColor.Red;
     }
     Console.WriteLine($"{level} {message}");
     Console.ResetColor();
  }

  static void Main(string[] args)
  {
    var scopes = new string[] { "User.Read" };

    var application = PublicClientApplicationBuilder.Create("<clientID>")
                      .WithLogging(Log, LogLevel.Info, true)
                      .Build();

    AuthenticationResult result = application.AcquireTokenInteractive(scopes)
                                             .ExecuteAsync().Result;
  }
}
```

> [!TIP]
 > 有关 MSAL.NET 日志记录的示例和其他信息，请参阅 [MSAL.NET Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki)。

## <a name="next-steps"></a>后续步骤

有关更多代码示例，请参阅 [Microsoft 标识平台代码示例](sample-v2-code.md)。
