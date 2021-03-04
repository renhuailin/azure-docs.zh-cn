---
title: 从 2.x 升级 - Azure Monitor Application Insights Java
description: 从 Azure Monitor Application Insights Java 2.x 升级
ms.topic: conceptual
ms.date: 11/25/2020
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.openlocfilehash: 6e1c7e15ff77fd75ff2fb70a6741ea2dd9a4cab8
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102040237"
---
# <a name="upgrading-from-application-insights-java-2x-sdk"></a>从 Application Insights Java 2.x SDK 升级

如果已在应用程序中使用 Application Insights Java 2.x SDK，则无需将其删除。
Java 3.0 代理会检测到它，并捕获和关联你通过 2.x SDK 发送的任何自定义遥测，同时禁止 2.x SDK 执行的任何自动收集以防止重复遥测。

如果你使用的是 Application Insights 2.x 代理，则需要删除指向 2.x 代理的 `-javaagent:` JVM 参数。

本文档的其余部分介绍了从 2.x 升级到 3.0 时可能遇到的限制和更改，以及你可能认为有帮助的一些解决方法。

## <a name="telemetryinitializers-and-telemetryprocessors"></a>TelemetryInitializers 和 TelemetryProcessors

使用 3.0 代理时，2.x SDK TelemetryInitializer 和 TelemetryProcessor 不会运行。
许多以前需要这些项的用例可以在 3.0 中通过配置[自定义维度](./java-standalone-config.md#custom-dimensions)或配置[遥测处理器](./java-standalone-telemetry-processors.md)来解决。

## <a name="multiple-applications-in-a-single-jvm"></a>单个 JVM 中的多个应用程序

目前，3.0 仅支持一个正在运行的进程使用一个[连接字符串和角色名称](./java-standalone-config.md#connection-string-and-role-name)。 特别是，你不能使用不同的连接字符串或不同的角色名称在相同的 tomcat 部署中拥有多个 tomcat Web 应用。

## <a name="operation-names"></a>操作名称

3\.0 中的操作名称已更改，通常在 Application Insights 门户 U/X 中提供更好的聚合视图。

:::image type="content" source="media/java-ipa/upgrade-from-2x/operation-names-3-0.png" alt-text="3.0 中的操作名称":::

但是，对于某些应用程序，你可能仍然更喜欢由以前的操作名称提供的 U/X 中的聚合视图；这样的话，你可使用 3.0 中的[遥测处理器](./java-standalone-telemetry-processors.md)（预览版）功能来复制以前的行为。

### <a name="prefix-the-operation-name-with-the-http-method-get-post-etc"></a>使用 http 方法（`GET`、`POST` 等）为操作名称添加前缀

在 2.x SDK 中，操作名称由 http 方法（`GET`、`POST` 等）添加前缀，例如

:::image type="content" source="media/java-ipa/upgrade-from-2x/operation-names-prefixed-by-http-method.png" alt-text="由 http 方法添加前缀的操作名称":::

下面的片段配置了 3 个遥测处理器，它们组合在一起来复制以前的行为。
遥测处理器会（按顺序）执行以下操作：

1. 第一个遥测处理器是范围处理器（具有类型 `span`），这意味着它适用于 `requests` 和 `dependencies`。

   它将匹配具有 `http.method` 属性且范围名称以 `/` 开头的任何范围。

   然后，它会将范围名称提取到一个名为 `tempName` 的属性中。

2. 第二个遥测处理器也是范围处理器。

   它将匹配具有 `tempName` 属性的任何范围。

   然后，它将串联由空格分隔的两个属性（`http.method` 和 `tempName`）来更新范围名称。

3. 最后一个遥测处理器是属性处理器（具有类型 `attribute`），这意味着它适用于所有具有属性（当前是 `requests`、`dependencies` 和 `traces`）的遥测。

   它将匹配具有 `tempName` 属性的任何遥测。

   然后，它将删除名为 `tempName` 的属性，使其不被报告为自定义维度。

```
{
  "preview": {
    "processors": [
      {
        "type": "span",
        "include": {
          "matchType": "regexp",
          "attributes": [
            { "key": "http.method", "value": "" }
          ],
          "spanNames": [ "^/" ]
        },
        "name": {
          "toAttributes": {
            "rules": [ "^(?<tempName>.*)$" ]
          }
        }
      },
      {
        "type": "span",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "tempName" }
          ]
        },
        "name": {
          "fromAttributes": [ "http.method", "tempName" ],
          "separator": " "
        }
      },
      {
        "type": "attribute",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "tempName" }
          ]
        },
        "actions": [
          { "key": "tempName", "action": "delete" }
        ]
      }
    ]
  }
}
```

### <a name="set-the-operation-name-to-the-full-path"></a>将操作名称设置为完整路径

此外在 2.x SDK 中，某些情况下操作名称包含完整路径，例如

:::image type="content" source="media/java-ipa/upgrade-from-2x/operation-names-with-full-path.png" alt-text="带有完整路径的操作名称":::

下面的片段配置了 4 个遥测处理器，它们组合在一起来复制以前的行为。
遥测处理器会（按顺序）执行以下操作：

1. 第一个遥测处理器是范围处理器（具有类型 `span`），这意味着它适用于 `requests` 和 `dependencies`。

   它将匹配具有 `http.url` 属性的任何范围。

   然后，它将更新包含 `http.url` 属性值的范围名称。

   这将是它的末尾部分，只是 `http.url` 看起来像 `http://host:port/path`，而你可能只想要 `/path` 部分。

2. 第二个遥测处理器也是范围处理器。

   它将匹配具有 `http.url` 属性的任何范围（也就是第一个处理器匹配的任何范围）。

   然后，它将范围名称的路径部分提取到名为 `tempName` 的属性中。

3. 第三个遥测处理器也是范围处理器。

   它将匹配具有 `tempPath` 属性的任何范围。

   然后，它将更新属性 `tempPath` 中的范围名称。

4. 最后一个遥测处理器是属性处理器（具有类型 `attribute`），这意味着它适用于所有具有属性（当前是 `requests`、`dependencies` 和 `traces`）的遥测。

   它将匹配具有 `tempPath` 属性的任何遥测。

   然后，它将删除名为 `tempPath` 的属性，使其不被报告为自定义维度。

```
{
  "preview": {
    "processors": [
      {
        "type": "span",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "http.url" }
          ]
        },
        "name": {
          "fromAttributes": [ "http.url" ]
        }
      },
      {
        "type": "span",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "http.url" }
          ]
        },
        "name": {
          "toAttributes": {
            "rules": [ "https?://[^/]+(?<tempPath>/[^?]*)" ]
          }
        }
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
          "fromAttributes": [ "tempPath" ]
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

## <a name="dependency-names"></a>依赖项名称

3\.0 中的依赖项名称也已更改，同样通常在 Application Insights 门户 U/X 中提供更好的聚合视图。

同样地，对于某些应用程序，你可能仍然更喜欢由以前的依赖项名称提供的 U/X 中的聚合视图；这样的话，你可使用与上述类似技术来复制以前的行为。

## <a name="operation-name-on-dependencies"></a>依赖项的操作名称

以前在 2.x SDK 中，请求遥测中的操作名称也设置在依赖项遥测上。
Application Insights Java 3.0 不再在依赖项遥测上填充操作名称。
若要查看作为依赖项遥测的父项的请求的操作名称，可以编写一个日志 (Kusto) 查询，以从依赖关系表联接到请求表，例如

```
let start = datetime('...');
let end = datetime('...');
dependencies
| where timestamp between (start .. end)
| project timestamp, type, name, operation_Id
| join (requests
    | where timestamp between (start .. end)
    | project operation_Name, operation_Id)
    on $left.operation_Id == $right.operation_Id
| summarize count() by operation_Name, type, name
```

## <a name="2x-sdk-logging-appenders"></a>2.x SDK 日志记录追加器

3.0 代理 [自动收集日志记录](./java-standalone-config.md#auto-collected-logging) ，无需配置任何日志记录追加器。
如果使用的是 2.x SDK 日志记录追加器，则可以将其删除，因为3.0 代理仍会将其取消。

## <a name="2x-sdk-spring-boot-starter"></a>2.x SDK 春季 boot 入门

没有3.0 春季 boot 入门版。
无论你使用的是春季 boot，3.0 代理安装和配置都遵循相同的 [简单步骤](./java-in-process-agent.md#quickstart) 。

从 1.x SDK 弹簧 boot 入门升级时，请注意，云角色名称将不再默认为 `spring.application.name` 。
有关通过 json config 或环境变量设置云角色3.0 名称，请参阅 [3.0 配置文档](./java-standalone-config.md#cloud-role-name) 。
