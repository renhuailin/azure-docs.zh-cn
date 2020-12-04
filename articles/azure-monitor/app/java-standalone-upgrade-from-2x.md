---
title: 从 Azure Monitor Application Insights Java 升级
description: 从 Azure Monitor 升级 Application Insights Java 2。x
ms.topic: conceptual
ms.date: 11/25/2020
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.openlocfilehash: d815c919c2b2d63b093c4290a661cbf508c56012
ms.sourcegitcommit: c4246c2b986c6f53b20b94d4e75ccc49ec768a9a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/04/2020
ms.locfileid: "96601061"
---
# <a name="upgrading-from-application-insights-java-2x-sdk"></a>从 Application Insights Java 2.x SDK 升级

如果已在应用程序中使用 Application Insights Java 2.x SDK，则无需删除它。
Java 3.0 代理将检测到该代理，并捕获并关联通过 1.x SDK 发送的任何自定义遥测，同时禁止使用由 1.x SDK 执行的任何自动收集来阻止重复的遥测。

如果你使用的是 Application Insights 2.x 代理，则需要删除指向 2.x 代理的 `-javaagent:` JVM 参数。

本文档的其余部分介绍了从1.x 升级到3.0 时可能会遇到的限制和更改，以及可能会对你有所帮助的一些解决方法。

## <a name="telemetryinitializers-and-telemetryprocessors"></a>TelemetryInitializers 和 TelemetryProcessors

使用3.0 代理时，不会运行 TelemetryInitializers 和 TelemetryProcessors。
许多以前需要这些用例，可以通过配置 [自定义维度](./java-standalone-config.md#custom-dimensions) 或配置 [遥测处理器](./java-standalone-telemetry-processors.md)来在3.0 中解决。

## <a name="multiple-applications-in-a-single-jvm"></a>单个 JVM 中的多个应用程序

目前，3.0 仅支持每个正在运行的进程使用一个 [连接字符串和角色名称](./java-standalone-config.md#connection-string-and-role-name) 。 特别是，不能在同一 tomcat 部署中使用不同的连接字符串或不同的角色名称来使用多个 tomcat web 应用。

## <a name="operation-names"></a>操作名称

3.0 中的操作名称已更改为通常在 Application Insights 门户中提供更好的聚合视图： U/X。

:::image type="content" source="media/java-ipa/upgrade-from-2x/operation-names-3-0.png" alt-text="3.0 中的操作名称":::

但是，对于某些应用程序，你可能仍倾向于先前操作名称提供的 U/X 中的聚合视图，在这种情况下，你可以使用3.0 中 (预览) 功能的 [遥测处理器](./java-standalone-telemetry-processors.md) 来复制以前的行为。

### <a name="prefix-the-operation-name-with-the-http-method-get-post-etc"></a>通过 http 方法 (、等 ) 为操作名称加上前缀 `GET` `POST` 。

在 2.x SDK 中，操作名称以 http 方法作为前缀 (`GET` ，等等 `POST` ) ，例如

:::image type="content" source="media/java-ipa/upgrade-from-2x/operation-names-prefixed-by-http-method.png" alt-text="以 http 方法为前缀的操作名称":::

下面的代码片段配置了三个遥测处理器，它们组合在一起以复制以前的行为。
遥测处理器 (按顺序执行以下操作) ：

1. 第一个遥测处理器是具有类型)  (范围 `span` ，这意味着它适用于 `requests` 和 `dependencies` 。

   它将匹配所有具有名为的属性 `http.method` ，并且具有以开头的范围名称 `/` 。

   然后，它会将范围名称提取到名为的属性中 `tempName` 。

2. 第二个遥测处理器也是一个跨处理器。

   它将匹配具有名为的属性的所有跨度 `tempName` 。

   然后，它将通过串联两个属性 `http.method` 和 `tempName` （由空格分隔）来更新范围名称。

3. 最后一个遥测处理器是 (具有类型) 的属性处理器 `attribute` ，这意味着它适用于所有具有 (当前和) 特性的遥测 `requests` `dependencies` `traces` 。

   它将匹配所有具有名为的属性的遥测 `tempName` 。

   然后删除名为的属性 `tempName` ，以便不会将其报告为自定义维度。

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

此外，在 2.x SDK 中，某些情况下，操作名称包含完整路径，例如

:::image type="content" source="media/java-ipa/upgrade-from-2x/operation-names-with-full-path.png" alt-text="带有完整路径的操作名称":::

下面的代码片段配置结合的、用于复制以前行为的遥测处理器。
遥测处理器 (按顺序执行以下操作) ：

1. 第一个遥测处理器是具有类型)  (范围 `span` ，这意味着它适用于 `requests` 和 `dependencies` 。

   它将匹配具有名为的属性的所有跨度 `http.url` 。

   然后，它将更新包含特性值的范围名称 `http.url` 。

   这会是它的末尾，只不过 `http.url` 看起来像这样 `http://host:port/path` ，但您可能只需要该 `/path` 部分。

2. 第二个遥测处理器也是一个跨处理器。

   它将匹配具有名为 (的属性的所有跨度 `http.url` ，即首个处理器匹配) 的任何跨度。

   然后，它会将范围名称的路径部分提取到名为的属性中 `tempName` 。

3. 第三个遥测处理器也是一个跨处理器。

   它将匹配具有名为的属性的所有跨度 `tempPath` 。

   然后，它将从属性更新范围名称 `tempPath` 。

4. 最后一个遥测处理器是 (具有类型) 的属性处理器 `attribute` ，这意味着它适用于所有具有 (当前和) 特性的遥测 `requests` `dependencies` `traces` 。

   它将匹配所有具有名为的属性的遥测 `tempPath` 。

   然后删除名为的属性 `tempPath` ，以便不会将其报告为自定义维度。

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

3.0 中的依赖项名称也发生了变化，这种情况下，通常会在 Application Insights 门户中提供更好的聚合视图： U/X。

同样，对于某些应用程序，你可能仍更喜欢前面的依赖项名称提供的 U/X 中的聚合视图，在这种情况下，你可以使用上面类似的技术来复制以前的行为。

## <a name="operation-name-on-dependencies"></a>依赖项的操作名称

以前在 2.x SDK 中，请求遥测的操作名称也是在依赖项遥测上设置的。
Application Insights Java 3.0 不再填充依赖项遥测的操作名称。
若要查看作为依赖项遥测的父项的请求的操作名称，可以编写日志 (Kusto) 查询，以从依赖关系表联接到请求表。
