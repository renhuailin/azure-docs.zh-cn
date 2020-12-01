---
title: 从 Azure Monitor Application Insights Java 升级
description: 从 Azure Monitor 升级 Application Insights Java 2。x
ms.topic: conceptual
ms.date: 11/25/2020
ms.openlocfilehash: d1d09c09afbabd40a32cbb80f1901112c37ac3da
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2020
ms.locfileid: "96354984"
---
# <a name="upgrading-from-application-insights-java-sdk-2x"></a>从 Application Insights Java SDK 2.x 升级

如果已在应用程序中使用 Application Insights Java SDK 2.x，则无需将其删除。
Java 3.0 代理将检测它，并捕获并关联通过 Java SDK 2.x 发送的任何自定义遥测，同时禁止 Java SDK 2.x 执行的任何自动收集来阻止重复的遥测。

如果你使用的是 Application Insights 2.x 代理，则需要删除指向 2.x 代理的 `-javaagent:` JVM 参数。

## <a name="telemetryinitializers-and-telemetryprocessors"></a>TelemetryInitializers 和 TelemetryProcessors

使用3.0 代理时，Java SDK 2.x TelemetryInitializers 和 TelemetryProcessors 将不会运行。
许多以前需要这些用例，可以通过配置 [自定义维度](./java-standalone-config.md#custom-dimensions) 或配置 [遥测处理器](./java-standalone-telemetry-processors.md)来在3.0 中解决。

## <a name="multiple-applications-in-a-single-jvm"></a>单个 JVM 中的多个应用程序

目前，3.0 仅支持每个正在运行的进程使用一个 [连接字符串和角色名称](./java-standalone-config.md#connection-string-and-role-name) 。 特别是，不能在同一 tomcat 部署中使用不同的连接字符串或不同的角色名称来使用多个 tomcat web 应用。

## <a name="http-request-telemetry-names"></a>HTTP 请求遥测名称

3.0 中的 HTTP 请求遥测名称已更改为通常在 Application Insights 门户中提供更好的聚合视图： U/X。

但是，对于某些应用程序，你可能仍更喜欢前面的遥测名称提供的 U/X 中的聚合视图，在这种情况下，你可以使用3.0 中的遥测处理器预览功能返回到以前的名称。

### <a name="to-prefix-the-telemetry-name-with-the-http-method-get-post-etc"></a>若要通过 http 方法为遥测名称加上前缀 (`GET` 、等 `POST` ) ：

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

### <a name="to-set-the-telemetry-name-to-the-full-url-path"></a>将遥测名称设置为完整 URL 路径

```
{
  "preview": {
    "processors": [
      {
        "type": "span",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "http.method" },
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
            { "key": "http.method" },
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
