---
title: 记录适用于 Android 的 MSAL 中的错误和异常。
titleSuffix: Microsoft identity platform
description: 了解如何记录适用于 Android 的 MSAL 中的错误和异常。
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
ms.openlocfilehash: b5d11c1bd091e3d7fbe5be87004b91f242e807f5
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/25/2021
ms.locfileid: "98763357"
---
# <a name="logging-in-msal-for-android"></a>MSAL for Android 中的日志记录

[!INCLUDE [MSAL logging introduction](../../../includes/active-directory-develop-error-logging-introduction.md)]

## <a name="logging-in-msal-for-android-using-java"></a>使用 Java 的 MSAL for Android 中的日志记录

创建应用时通过创建日志记录回调来启用日志记录。 该回调采用以下参数：

- `tag` 由库传递给回调的字符串。 它与日志条目相关联，可用于对日志记录消息排序。
- `logLevel` 用于确定所需的日志记录级别。 支持的日志级别为：`Error`、`Warning`、`Info` 和 `Verbose`。
- `message` 是日志条目的内容。
- `containsPII` 指定是要记录包含个人数据还是组织数据的消息。 默认情况下，此项设置为 false，不允许应用程序记录个人数据。 如果 `containsPII` 为 `true`，则此方法会接收消息两次：第一次时 `containsPII` 参数设置为 `false`，`message` 不包含个人数据；第二次时 `containsPii` 参数设置为 `true`，消息可能包含个人数据。 在某些情况下（消息不含个人数据），消息是相同的。

```java
private StringBuilder mLogs;

mLogs = new StringBuilder();
Logger.getInstance().setExternalLogger(new ILoggerCallback()
{
   @Override
   public void log(String tag, Logger.LogLevel logLevel, String message, boolean containsPII)
   {
      mLogs.append(message).append('\n');
   }
});
```

默认情况下，MSAL 记录器将不会捕获任何个人身份信息或组织身份信息。
若要记录个人身份信息或组织身份信息：

```java
Logger.getInstance().setEnablePII(true);
```

若要禁止记录个人数据和组织数据：

```java
Logger.getInstance().setEnablePII(false);
```

默认情况下，将禁用记录到 logcat。 若要启用：

```java
Logger.getInstance().setEnableLogcatLog(true);
```

## <a name="next-steps"></a>后续步骤

有关更多代码示例，请参阅 [Microsoft 标识平台代码示例) ](sample-v2-code.md)。