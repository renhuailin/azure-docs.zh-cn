---
title: 在 MSAL.js 中记录错误和异常
titleSuffix: Microsoft identity platform
description: 了解如何在 MSAL.js 中记录错误和异常
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
ms.openlocfilehash: d7463e3376847a219750254548c25ca79f4cdfb6
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2021
ms.locfileid: "98954811"
---
# <a name="logging-in-msaljs"></a>MSAL.js 中的日志记录

[!INCLUDE [MSAL logging introduction](../../../includes/active-directory-develop-error-logging-introduction.md)]

## <a name="configure-logging-in-msaljs"></a>在 MSAL.js 中配置日志记录

可以在 MSAL.js (JavaScript) 中启用日志记录，方法是在配置过程中传递一个记录器对象，以便创建 `UserAgentApplication` 实例。 此记录器对象具有以下属性：

- `localCallback`：一个回调实例，可以由开发人员提供，目的是以自定义方式使用并发布日志。 根据所需要的重定向日志的方式，实现 localCallback 方法。
- `level`（可选）：可配置的日志级别。 支持的日志级别为：`Error`、`Warning`、`Info` 和 `Verbose`。 默认为 `Info`。
- `piiLoggingEnabled`（可选）：如果设置为 true，则记录个人数据和组织数据。 默认情况下，此项设置为 false，不允许应用程序记录个人数据。 个人数据日志不会写入到默认的输出（例如控制台、Logcat 或 NSLog）中。
- `correlationId`（可选）：唯一标识符，用于映射请求与响应，以便进行调试。 默认为 RFC4122 版本 4 guid（128 位）。

```javascript
function loggerCallback(logLevel, message, containsPii) {
   console.log(message);
}

var msalConfig = {
    auth: {
        clientId: "<Enter your client id>",
    },
    system: {
        logger: new Msal.Logger(
            loggerCallback , {
                level: Msal.LogLevel.Verbose,
                piiLoggingEnabled: false,
                correlationId: '1234'
            }
        )
    }
}

var UserAgentApplication = new Msal.UserAgentApplication(msalConfig);
```

## <a name="next-steps"></a>后续步骤

有关更多代码示例，请参阅 [Microsoft 标识平台代码示例](sample-v2-code.md)。