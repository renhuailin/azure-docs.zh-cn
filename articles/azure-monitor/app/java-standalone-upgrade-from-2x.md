---
title: 从 2.x 升级 - Azure Monitor Application Insights Java
description: 从 Azure Monitor Application Insights Java 2.x 升级
ms.topic: conceptual
ms.date: 11/25/2020
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.openlocfilehash: cbfdc8c7e07a68335083c529e545143a513b1808
ms.sourcegitcommit: d2875bdbcf1bbd7c06834f0e71d9b98cea7c6652
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/12/2021
ms.locfileid: "129858265"
---
# <a name="upgrading-from-application-insights-java-2x-sdk"></a>从 Application Insights Java 2.x SDK 升级

如果你已在应用程序中使用 Application Insights Java 2.x SDK，则可以继续使用它。
Application Insights Java 3.x 代理会检测到它，并捕获和关联你通过 2.x SDK 发送的任何自定义遥测，同时禁止 2.x SDK 执行的任何自动收集以防止重复遥测。

如果你使用的是 Application Insights 2.x 代理，则需要删除指向 2.x 代理的 `-javaagent:` JVM 参数。

本文档的其余部分介绍了从 2.x 升级到 3.x 时可能遇到的限制和更改，以及你可能认为有帮助的一些解决方法。

## <a name="telemetryinitializers-and-telemetryprocessors"></a>TelemetryInitializers 和 TelemetryProcessors

使用 3.x 代理时，2.x SDK TelemetryInitializer 和 TelemetryProcessor 不会运行。
许多以前需要这些项的用例可以在 Application Insights Java 3.x 中通过配置[自定义维度](./java-standalone-config.md#custom-dimensions)或配置[遥测处理器](./java-standalone-telemetry-processors.md)来解决。

## <a name="multiple-applications-in-a-single-jvm"></a>单个 JVM 中的多个应用程序

目前，Application Insights Java 3.x 仅支持一个正在运行的进程使用一个[连接字符串和角色名称](./java-standalone-config.md#connection-string-and-role-name)。 特别是，你不能使用不同的连接字符串或不同的角色名称在相同的 tomcat 部署中拥有多个 tomcat Web 应用。

## <a name="operation-names"></a>操作名称

在 Application Insights Java 2.x SDK 中，某些情况下操作名称包含完整路径，例如

:::image type="content" source="media/java-ipa/upgrade-from-2x/operation-names-with-full-path.png" alt-text="显示带完整路径的操作名称的屏幕截图":::

Application Insights Java 3.x 中的操作名称已更改，通常在 Application Insights 门户 U/X 中提供更好的聚合视图，例如

:::image type="content" source="media/java-ipa/upgrade-from-2x/operation-names-parameterized.png" alt-text="显示参数化操作名称的屏幕截图":::

但是，对于某些应用程序，你可能仍然更喜欢由以前的操作名称提供的 U/X 中的聚合视图；这样的话，你可使用 3.x 中的[遥测处理器](./java-standalone-telemetry-processors.md)（预览版）功能来复制以前的行为。

下面的片段配置了 3 个遥测处理器，它们组合在一起来复制以前的行为。
遥测处理器会（按顺序）执行以下操作：

1. 第一个遥测处理器是属性处理器（具有类型 `attribute`），这意味着它适用于所有具有属性（当前是 `requests`、`dependencies`，但不久后还包括 `traces`）的遥测。

   它将匹配具有 `http.method` 和 `http.url` 属性的任何遥测。

   然后，它将 `http.url` 属性的路径部分提取到名为 `tempName` 的属性中。

2. 第二个遥测处理器是范围处理器（具有类型 `span`），这意味着它适用于 `requests` 和 `dependencies`。

   它将匹配具有 `tempPath` 属性的任何范围。

   然后，它将更新属性 `tempPath` 中的范围名称。

3. 最后一个遥测处理器是属性处理器，其类型与第一个遥测处理器相同。

   它将匹配具有 `tempPath` 属性的任何遥测。

   然后，它将删除名为 `tempPath` 的属性，使其不被报告为自定义维度。

```
{
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "http.method" },
            { "key": "http.url" }
          ]
        },
        "actions": [
          {
            "key": "http.url",
            "pattern": "https?://[^/]+(?<tempPath>/[^?]*)",
            "action": "extract"
          }
        ]
      },
      {
        "type": "span",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "tempPath" }
          ]
        },
        "name": {
          "fromAttributes": [ "http.method", "tempPath" ],
          "separator": " "
        }
      },
      {
        "type": "attribute",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "tempPath" }
          ]
        },
        "actions": [
          { "key": "tempPath", "action": "delete" }
        ]
      }
    ]
  }
}
```

## <a name="2x-sdk-logging-appenders"></a>2.x SDK 日志记录追加器

Application Insights Java 3.x [自动收集日志记录](./java-standalone-config.md#auto-collected-logging)，无需配置任何日志记录追加器。
如果使用的是 2.x SDK 日志记录追加器，则这些可以被删除，因为 Application Insights Java 3.x 仍会抑制它们。

## <a name="2x-sdk-spring-boot-starter"></a>2.x SDK Spring Boot Starter

没有 Application Insights Java 3.x Spring Boot Starter。
无论你是否使用 Spring Boot，3.x 安装和配置都会遵循相同的[简单步骤](./java-in-process-agent.md#get-started)。

当从 Application Insights Java 2.x SDK Spring Boot Starter 升级时，请注意，云角色名称将不再默认为 `spring.application.name`。
请参阅 [3.x 配置文档](./java-standalone-config.md#cloud-role-name)，了解如何通过 json config 或环境变量来设置 3.x 中的云角色名称。
