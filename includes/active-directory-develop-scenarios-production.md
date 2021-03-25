---
title: include 文件
description: include 文件
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: 75650d7ff0ac647aeb6dace76c270680b1b89347
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "98954947"
---
## <a name="enable-logging"></a>启用日志记录

为了帮助对调试和身份验证失败进行故障排除，Microsoft 身份验证库提供了内置的日志记录支持。 以下文章介绍了如何记录每个库：

:::row:::
    :::column:::
        - [在 MSAL.NET 中进行日志记录](../articles/active-directory/develop/msal-logging-dotnet.md)
        - [MSAL for Android 中的日志记录](../articles/active-directory/develop/msal-logging-android.md)
        - [MSAL.js 中的日志记录](../articles/active-directory/develop/msal-logging-js.md)
    :::column-end:::
    :::column:::
        - [MSAL for iOS/macOS 中的日志记录](../articles/active-directory/develop/msal-logging-ios.md)
        - [MSAL for Java 中的日志记录](../articles/active-directory/develop/msal-logging-java.md)
        - [MSAL for Python 中的日志记录](../articles/active-directory/develop/msal-logging-python.md)
    :::column-end:::
:::row-end:::

一些数据收集建议如下：

- 用户在有问题时可能会寻求帮助。 最佳做法是捕获日志并将其临时存储。 提供一个供用户上传日志的位置。 MSAL 提供日志记录扩展来捕获有关身份验证的详细信息。

- 如果遥测可用，请通过 MSAL 启用它，以收集有关用户如何登录应用的数据。


## <a name="validate-your-integration"></a>验证你的集成

按照 [Microsoft 标识平台集成清单](../articles/active-directory/develop/identity-platform-integration-checklist.md)测试你的集成。
