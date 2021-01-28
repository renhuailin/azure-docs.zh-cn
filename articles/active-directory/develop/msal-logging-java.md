---
title: 记录 MSAL for Java 中的错误和异常
titleSuffix: Microsoft identity platform
description: 了解如何记录 MSAL for Java 中的错误和异常
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
ms.openlocfilehash: d3fa13a6751b2d8acf1fc99aecbf174f1823fb0b
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2021
ms.locfileid: "98954912"
---
# <a name="logging-in-msal-for-java"></a>MSAL for Java 中的日志记录

[!INCLUDE [MSAL logging introduction](../../../includes/active-directory-develop-error-logging-introduction.md)]

## <a name="msal-for-java-logging"></a>适用于 Java 的 MSAL 日志记录

适用于 Java 的 MSAL 允许使用已在应用中使用的日志记录库，只要该库与 SLF4J 兼容即可。 适用于 Java 的 MSAL 使用[适用于 Java 的简单日志记录结构](http://www.slf4j.org/) (SLF4J) 作为各种日志记录框架（例如 [java.util.logging](https://docs.oracle.com/javase/7/docs/api/java/util/logging/package-summary.html)、[Logback](http://logback.qos.ch/) 和 [Log4j](https://logging.apache.org/log4j/2.x/)）的简单结构或抽象层。 SLF4J 允许用户在部署时插入所需的日志记录框架。

例如，若要使用 Logback 作为应用程序中的日志记录框架，请将 Logback 依赖项添加到应用程序的 Maven pom 文件中：

```xml
<dependency>
    <groupId>ch.qos.logback</groupId>
    <artifactId>logback-classic</artifactId>
    <version>1.2.3</version>
</dependency>
```

然后添加 Logback 配置文件：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration debug="true">

</configuration>
```

在部署时，SLF4J 会自动绑定到 Logback。 MSAL 日志将写入控制台。

有关如何绑定到其他日志记录框架的说明，请参阅 [SLF4J 手册](http://www.slf4j.org/manual.html)。

### <a name="personal-and-organization-information"></a>个人和组织信息

默认情况下，MSAL 日志记录不会捕获或者记录任何个人或组织数据。 在以下示例中，默认已禁用个人或组织数据的日志记录：

```java
    PublicClientApplication app2 = PublicClientApplication.builder(PUBLIC_CLIENT_ID)
            .authority(AUTHORITY)
            .build();
```

可以通过在客户端应用程序生成器中设置 `logPii()` 来启用个人和组织数据的日志记录。 如果启用个人或组织数据的日志记录，则应用必须负责安全处理高度敏感的数据，并遵守任何法规要求。

在以下示例中，默认已启用个人或组织数据的日志记录：

```java
PublicClientApplication app2 = PublicClientApplication.builder(PUBLIC_CLIENT_ID)
        .authority(AUTHORITY)
        .logPii(true)
        .build();
```

## <a name="next-steps"></a>后续步骤

有关更多代码示例，请参阅 [Microsoft 标识平台代码示例](sample-v2-code.md)。