---
title: 记录 MSAL for iOS/macOS 中的错误和异常
titleSuffix: Microsoft identity platform
description: 了解如何记录 MSAL for iOS/macOS 中的错误和异常
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
ms.openlocfilehash: ee3837b75d586238e7ca6ac85434cc56f592929d
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/25/2021
ms.locfileid: "98763338"
---
# <a name="logging-in-msal-for-iosmacos"></a>MSAL for iOS/macOS 中的日志记录

[!INCLUDE [MSAL logging introduction](../../../includes/active-directory-develop-error-logging-introduction.md)]

## <a name="objective-c"></a>[Objective-C](#tab/objc)

## <a name="msal-for-ios-and-macos-logging-objc"></a>适用于 iOS 和 macOS 的 MSAL 日志记录 - ObjC

设置一个回调来捕获 MSAL 日志记录，并将其合并到自己应用程序的日志记录中。 回调的签名如下所示：

```objc
/*!
    The LogCallback block for the MSAL logger
 
    @param  level           The level of the log message
    @param  message         The message being logged
    @param  containsPII     If the message might contain Personally Identifiable Information (PII)
                            this will be true. Log messages possibly containing PII will not be
                            sent to the callback unless PIllLoggingEnabled is set to YES on the
                            logger.

 */
typedef void (^MSALLogCallback)(MSALLogLevel level, NSString *message, BOOL containsPII);
```

例如：

```objc
[MSALGlobalConfig.loggerConfig setLogCallback:^(MSALLogLevel level, NSString *message, BOOL containsPII)
    {
        if (!containsPII)
        {
#if DEBUG
            // IMPORTANT: MSAL logs may contain sensitive information. Never output MSAL logs with NSLog, or print, directly unless you're running your application in debug mode. If you're writing MSAL logs to file, you must store the file securely.
            NSLog(@"MSAL log: %@", message);
#endif
        }
    }];
```

### <a name="personal-data"></a>个人数据

默认情况下，MSAL 不会捕获或记录任何个人数据。 库允许应用开发人员通过 MSALLogger 类中的某个属性启用该功能。 启用 `pii.Enabled` 日志记录后，应用负责安全地处理高度敏感的数据并遵守任何法规要求。

```objc
// By default, the `MSALLogger` doesn't capture any PII

// PII will be logged
MSALGlobalConfig.loggerConfig.piiEnabled = YES;

// PII will NOT be logged
MSALGlobalConfig.loggerConfig.piiEnabled = NO;
```

### <a name="logging-levels"></a>日志记录级别

若要在使用适用于 iOS 和 macOS 的 MSAL 进行日志记录时设置日志记录级别，请使用以下值之一：

|Level  |说明 |
|---------|---------|
| `MSALLogLevelNothing`| 禁用所有日志记录 |
| `MSALLogLevelError` | 默认级别，仅在发生错误时输出信息 |
| `MSALLogLevelWarning` | 警告 |
| `MSALLogLevelInfo` |  库入口点，包含参数和各种密钥链操作 |
|`MSALLogLevelVerbose`     |  API 跟踪 |

例如：

```objc
MSALGlobalConfig.loggerConfig.logLevel = MSALLogLevelVerbose;
 ```

 ### <a name="log-message-format"></a>日志消息格式

MSAL 日志消息的消息部分采用 `TID = <thread_id> MSAL <sdk_ver> <OS> <OS_ver> [timestamp - correlation_id] message` 格式

例如：

`TID = 551563 MSAL 0.2.0 iOS Sim 12.0 [2018-09-24 00:36:38 - 36764181-EF53-4E4E-B3E5-16FE362CFC44] acquireToken returning with error: (MSALErrorDomain, -42400) User cancelled the authorization session.`

提供关联 ID 和时间戳有助于跟踪问题。 时间戳和关联 ID 信息在日志消息中提供。 只能从 MSAL 日志记录消息中可靠地检索这些信息。

## <a name="swift"></a>[Swift](#tab/swift)

## <a name="msal-for-ios-and-macos-logging-swift"></a>适用于 iOS 和 macOS 的 MSAL 日志记录 - Swift

设置一个回调来捕获 MSAL 日志记录，并将其合并到自己应用程序的日志记录中。 回调的签名（以 Objective-C 表示）如下所示：

```objc
/*!
    The LogCallback block for the MSAL logger
 
    @param  level           The level of the log message
    @param  message         The message being logged
    @param  containsPII     If the message might contain Personally Identifiable Information (PII)
                            this will be true. Log messages possibly containing PII will not be
                            sent to the callback unless PIllLoggingEnabled is set to YES on the
                            logger.

 */
typedef void (^MSALLogCallback)(MSALLogLevel level, NSString *message, BOOL containsPII);
```

例如：

```swift
MSALGlobalConfig.loggerConfig.setLogCallback { (level, message, containsPII) in
    if let message = message, !containsPII
    {
#if DEBUG
    // IMPORTANT: MSAL logs may contain sensitive information. Never output MSAL logs with NSLog, or print, directly unless you're running your application in debug mode. If you're writing MSAL logs to file, you must store the file securely.
    print("MSAL log: \(message)")
#endif
    }
}
```

### <a name="personal-data"></a>个人数据

默认情况下，MSAL 不会捕获或记录任何个人数据。 库允许应用开发人员通过 MSALLogger 类中的某个属性启用该功能。 启用 `pii.Enabled` 日志记录后，应用负责安全地处理高度敏感的数据并遵守任何法规要求。

```swift
// By default, the `MSALLogger` doesn't capture any PII

// PII will be logged
MSALGlobalConfig.loggerConfig.piiEnabled = true

// PII will NOT be logged
MSALGlobalConfig.loggerConfig.piiEnabled = false
```

### <a name="logging-levels"></a>日志记录级别

若要在使用适用于 iOS 和 macOS 的 MSAL 进行日志记录时设置日志记录级别，请使用以下值之一：

|Level  |说明 |
|---------|---------|
| `MSALLogLevelNothing`| 禁用所有日志记录 |
| `MSALLogLevelError` | 默认级别，仅在发生错误时输出信息 |
| `MSALLogLevelWarning` | 警告 |
| `MSALLogLevelInfo` |  库入口点，包含参数和各种密钥链操作 |
|`MSALLogLevelVerbose`     |  API 跟踪 |

例如：

```swift
MSALGlobalConfig.loggerConfig.logLevel = .verbose
 ```

### <a name="log-message-format"></a>日志消息格式

MSAL 日志消息的消息部分采用 `TID = <thread_id> MSAL <sdk_ver> <OS> <OS_ver> [timestamp - correlation_id] message` 格式

例如：

`TID = 551563 MSAL 0.2.0 iOS Sim 12.0 [2018-09-24 00:36:38 - 36764181-EF53-4E4E-B3E5-16FE362CFC44] acquireToken returning with error: (MSALErrorDomain, -42400) User cancelled the authorization session.`

提供关联 ID 和时间戳有助于跟踪问题。 时间戳和关联 ID 信息在日志消息中提供。 只能从 MSAL 日志记录消息中可靠地检索这些信息。

---

## <a name="next-steps"></a>后续步骤

有关更多代码示例，请参阅 [Microsoft 标识平台代码示例](sample-v2-code.md)。