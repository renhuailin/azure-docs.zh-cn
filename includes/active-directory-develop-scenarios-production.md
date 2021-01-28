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
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/25/2021
ms.locfileid: "98954947"
---
## <a name="enable-logging"></a>启用日志记录

为了帮助调试和身份验证故障排除方案，Microsoft 身份验证库提供了内置日志记录支持。 日志记录是以下文章中介绍的每个库：

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

以下是有关数据收集的一些建议：

- 用户可能会在遇到问题时询问帮助。 最佳做法是捕获并临时存储日志。 提供用户可在其中上传日志的位置。 MSAL 提供日志记录扩展来捕获有关身份验证的详细信息。

- 如果遥测可用，请通过 MSAL 启用该功能，收集有关用户如何登录到应用的数据。


## <a name="validate-your-integration"></a>验证你的集成

按照 [Microsoft 标识平台集成清单](../articles/active-directory/develop/identity-platform-integration-checklist.md)测试你的集成。
