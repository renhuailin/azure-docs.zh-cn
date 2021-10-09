---
title: 采样替代（预览版）- 适用于 Java 的 Azure Monitor Application Insights
description: 了解在适用于 Java 的 Azure Monitor Application Insights 中配置采样替代。
ms.topic: conceptual
ms.date: 03/22/2021
author: trask
ms.custom: devx-track-java
ms.author: trstalna
ms.openlocfilehash: c0f6c1b0fce97bc835cb63a47d8827d7fab8ed56
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128599822"
---
# <a name="sampling-overrides-preview---azure-monitor-application-insights-for-java"></a>采样替代（预览版）- 适用于 Java 的 Azure Monitor Application Insights

> [!NOTE]
> 从 3.0.3 开始，提供采样替代功能预览。

采样替代允许你替代[默认采样百分比](./java-standalone-config.md#sampling)，例如：
 * 将采样百分比设置为 0（或某个小值）以检查干扰运行状况。
 * 将采样百分比设置为 0（或一些小值）以调用干扰依赖项。
 * 针对某个重要请求类型（例如，`/login`），将采样百分比设置为 100，即使你将默认采样配置为低于此值也是如此。

## <a name="terminology"></a>术语

在了解采样替代之前，应了解术语“范围”。 范围是一个表示下列事项的通用术语：

* 传入请求。
* 传出依赖项（例如，对另一个服务的远程调用）。
* 进程内依赖项（例如，由服务的子组件所做的工作）。

对于采样替代，这些范围组件非常重要：

* 属性

范围属性表示给定请求或依赖项的标准属性和自定义属性。

## <a name="getting-started"></a>入门

首先，创建一个名为“applicationinsights.json”的配置文件。 将其保存在与“applicationinsights-agent-\*.jar”相同的目录中。 请使用以下模版。

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "sampling": {
    "percentage": 10
  },
  "preview": {
    "sampling": {
      "overrides": [
        {
          "attributes": [
            ...
          ],
          "percentage": 0
        },
        {
          "attributes": [
            ...
          ],
          "percentage": 100
        }
      ]
    }
  }
}
```

## <a name="how-it-works"></a>工作原理

当某个范围开始时，在该时间范围上出现的属性将用于检查是否有任何采样替代是匹配的。

如果其中一个采样替代匹配，则使用其采样百分比来确定是否采样该范围。

只使用第一个匹配的采样替代。

如果没有匹配的采样替代：

* 如果这是跟踪中的第一个范围，则使用[默认采样百分比](./java-standalone-config.md#sampling)。
* 如果不是跟踪中的第一个范围，则使用父级采样决定。

> [!WARNING]
> 决定不收集范围时，即使存在与下游范围匹配的采样替代，也不会收集所有下游范围。
> 此行为是必需的，因为不然会出现损坏的跟踪，即下游范围被收集，但却成为未收集范围的父级范围。

> [!NOTE]
> 采样决策基于将 traceId（也称为 operationId）哈希处理为介于 0 到 100 之间的一个数字，然后将该哈希与采样百分比进行比较。
> 由于给定跟踪中的所有范围都将具有相同的 traceId，它们将具有相同的哈希，因此整个跟踪中的采样决策将是一致的。

## <a name="example-suppress-collecting-telemetry-for-health-checks"></a>示例：取消收集运行状况检查的遥测数据

这将取消收集所有对 `/health-checks` 的请求的遥测数据。

这也会取消收集通常在 `/health-checks` 下收集的任何下游范围（依赖项）。

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "sampling": {
      "overrides": [
        {
          "attributes": [
            {
              "key": "http.url",
              "value": "https?://[^/]+/health-check",
              "matchType": "regexp"
            }
          ],
          "percentage": 0
        }
      ]
    }
  }
}
```

## <a name="example-suppress-collecting-telemetry-for-a-noisy-dependency-call"></a>示例：取消收集干扰依赖项调用的遥测数据

这将取消收集所有 `GET my-noisy-key` redis 调用的遥测数据。

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "sampling": {
      "overrides": [
        {
          "attributes": [
            {
              "key": "db.system",
              "value": "redis",
              "matchType": "strict"
            },
            {
              "key": "db.statement",
              "value": "GET my-noisy-key",
              "matchType": "strict"
            }
          ],
          "percentage": 0
        }
      ]
    }
  }
}
```

## <a name="example-collect-100-of-telemetry-for-an-important-request-type"></a>示例：收集重要请求类型的 100% 遥测数据

这将收集 `/login` 的 100% 遥测数据。

由于下游范围 (依赖项) 遵循父级采样决策（不存在该下游范围的任何采样替代），因此也将为所有“/login”请求收集这些项。

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "sampling": {
    "percentage": 10
  },
  "preview": {
    "sampling": {
      "overrides": [
        {
          "attributes": [
            {
              "key": "http.url",
              "value": "https?://[^/]+/login",
              "matchType": "regexp"
            }
          ],
          "percentage": 100
        }
      ]
    }
  }
}
```

## <a name="common-span-attributes"></a>常见范围属性

此部分列出了采样替代可以使用的一些常见范围属性。

### <a name="http-spans"></a>HTTP 范围

| 属性  | 类型 | 说明 | 
|---|---|---|
| `http.method` | string | HTTP 请求方法。|
| `http.url` | string | 完整的 HTTP 请求 URL（采用 `scheme://host[:port]/path?query[#fragment]` 格式）。 片段通常不通过 HTTP 传输。 但如果片段是已知的，则应将其包含在内。|
| `http.flavor` | string | HTTP 协议的类型。 |
| `http.user_agent` | string | 客户端发送的 [HTTP User-Agent](https://tools.ietf.org/html/rfc7231#section-5.5.3) 标头的值。 |

请注意，`http.status_code` 不能用于采样决策，因为它不能在范围开始时使用。

### <a name="jdbc-spans"></a>JDBC 范围

| 属性  | 类型 | 说明  |
|---|---|---|
| `db.system` | string | 正在使用的数据库管理系统 (DBMS) 产品的标识符。 |
| `db.connection_string` | string | 用于连接到数据库的连接字符串。 建议删除嵌入的凭据。|
| `db.user` | string | 用于访问数据库的用户名。 |
| `db.name` | string | 用于报告正在访问的数据库的名称的字符串。 对于用于切换数据库的命令，应当将此字符串设置为目标数据库（即使该命令失败）。|
| `db.statement` | string | 正在运行的数据库语句。|
