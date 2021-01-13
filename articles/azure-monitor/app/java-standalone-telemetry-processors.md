---
title: " (预览版的遥测处理器) -Azure Monitor Application Insights Java"
description: 如何在 Azure Monitor Application Insights 中为 Java 配置遥测处理器
ms.topic: conceptual
ms.date: 10/29/2020
author: kryalama
ms.custom: devx-track-java
ms.author: kryalama
ms.openlocfilehash: ba4e6b8b5e9db494ab4c0c372c2086087a2d58cb
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/12/2021
ms.locfileid: "98133168"
---
# <a name="telemetry-processors-preview---azure-monitor-application-insights-for-java"></a> (预览版的遥测处理器) -Azure Monitor Application Insights Java

> [!NOTE]
> 此功能目前仍为预览版。

适用于 Application Insights 的 Java 3.0 代理现在具有在导出遥测数据之前处理该数据的功能。

下面是遥测处理器的一些用例：
 * 屏蔽敏感数据
 * 有条件地添加自定义维度
 * 更新用于聚合和显示的遥测名称
 * 删除或筛选跨越属性以控制引入成本

## <a name="terminology"></a>术语

在转到遥测处理器之前，请务必了解跟踪和跨越的内容。

### <a name="traces"></a>跟踪

跟踪跟踪单个请求的进度（称为 `trace` ），因为它是由构成应用程序的服务来处理的。 请求可能由用户或应用程序启动。 中的每个工作单元 `trace` 均称为 `span` ; 是 `trace` 范围的树。 由 `trace` 单个根范围和任意数量的子范围组成。

### <a name="span"></a>格

跨越是对象，表示由请求中涉及的单个服务或组件在系统中流动时所做的工作。 `span`包含一个 `span context` ，它是一组全局唯一标识符，表示每个跨度所属的唯一请求。 

跨度封装：

* 范围名称
* `SpanContext`唯一标识范围的不可变的
* `Span`、或 null 形式的父范围 `SpanContext`
* 一个 `SpanKind`
* 开始时间戳
* 结束时间戳
* [`Attributes`](#attributes)
* 带时间戳的事件的列表
* `Status`。

通常，范围的生命周期如下所示：

* 服务收到请求。 范围上下文是从请求标头（如果存在）中提取的。
* 新范围将创建为提取的范围上下文的子元素;如果不存在，则创建一个新的根跨度。
* 服务处理请求。 其他属性和事件将添加到可用于了解请求上下文的范围，例如处理请求的计算机的主机名或客户标识符。
* 可以创建新的范围来表示服务的子组件完成的工作。
* 当服务对另一服务进行远程调用时，会将范围上下文注入标头或消息信封，从而序列化当前范围上下文并将其转发到下一个服务。
* 服务完成的工作已成功完成。 范围状态已适当设置，跨度标记为 "已完成"。

### <a name="attributes"></a>属性

`Attributes` 是在中封装的零个或多个键/值对的列表 `span` 。 特性必须具有以下属性：

特性键，必须为非 null 和非空字符串。
属性值，可以是：
* 基元类型： string、boolean、双精度浮点 (IEEE 754-1985) 或带符号64位整数。
* 基元类型值的数组。 数组必须为同类类型，即不能包含不同类型的值。 对于不支持数组值的本机协议，应将这些值表示为 JSON 字符串。

## <a name="supported-processors"></a>支持的处理器：
 * 属性处理器
 * 范围处理器

## <a name="to-get-started"></a>入门指南

使用以下模板创建一个名为 `applicationinsights.json` 的配置文件，并将其置于 `applicationinsights-agent-***.jar` 所在的目录中。

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        ...
      },
      {
        "type": "attribute",
        ...
      },
      {
        "type": "span",
        ...
      }
    ]
  }
}
```

## <a name="includeexclude-spans"></a>包含/排除范围

特性处理器和跨度处理器公开选项以提供一组要匹配的范围的属性，以确定是否应在遥测处理器中包含或排除跨距。 若要配置此选项，则在 `include` 和/或 `exclude` 下至少必须有一个 `matchType` 以及 `spanNames` 和 `attributes` 中的一个。 允许包括/排除配置有多个指定的条件。 所有指定条件的评估结果都必须为 true 才会被视为匹配。 

必填字段： 
* `matchType` 控制如何解释 `spanNames` 和 `attributes` 数组中的项。 可能的值包括 `regexp` 或 `strict`。 

可选字段： 
* `spanNames` 必须至少与一个项匹配。 
* `attributes` 指定要用作匹配依据的属性列表。 所有这些属性必须完全匹配才会被视为匹配。

> [!NOTE]
> 如果同时指定了 `include` 和 `exclude`，则会在 `exclude` 属性之前检查 `include` 属性。

#### <a name="sample-usage"></a>示例用法

```json

"processors": [
  {
    "type": "attribute",
    "include": {
      "matchType": "strict",
      "spanNames": [
        "spanA",
        "spanB"
      ]
    },
    "exclude": {
      "matchType": "strict",
      "attributes": [
        {
          "key": "redact_trace",
          "value": "false"
        }
      ]
    },
    "actions": [
      {
        "key": "credit_card",
        "action": "delete"
      },
      {
        "key": "duplicate_key",
        "action": "delete"
      }
    ]
  }
]
```
有关详细信息，请查看 [遥测处理器示例](./java-standalone-telemetry-processors-examples.md) 文档。

## <a name="attribute-processor"></a>属性处理器 

属性处理器修改范围的属性。 它还可以支持用于包括/排除范围的功能。 它接受一个操作列表，这些操作按配置文件中指定的顺序执行。 支持的操作有：

### `insert`

在不存在键的范围内插入新属性。   

```json
"processors": [
  {
    "type": "attribute",
    "actions": [
      {
        "key": "attribute1",
        "value": "value1",
        "action": "insert"
      },
    ]
  }
]
```
对于 `insert` 操作，以下项是必需的
  * `key`
  * 之一 `value` 或 `fromAttribute`
  * `action`:`insert`

### `update`

更新范围中存在键的属性

```json
"processors": [
  {
    "type": "attribute",
    "actions": [
      {
        "key": "attribute1",
        "value": "newValue",
        "action": "update"
      },
    ]
  }
]
```
对于 `update` 操作，以下项是必需的
  * `key`
  * 之一 `value` 或 `fromAttribute`
  * `action`:`update`


### `delete` 

删除范围中的属性

```json
"processors": [
  {
    "type": "attribute",
    "actions": [
      {
        "key": "attribute1",
        "action": "delete"
      },
    ]
  }
]
```
对于 `delete` 操作，以下项是必需的
  * `key`
  * `action`: `delete`

### `hash`

哈希 (SHA1) 现有属性值

```json
"processors": [
  {
    "type": "attribute",
    "actions": [
      {
        "key": "attribute1",
        "action": "hash"
      },
    ]
  }
]
```
对于 `hash` 操作，以下项是必需的
* `key`
* `action` : `hash`

### `extract`

> [!NOTE]
> 此功能仅在3.0.1 和更高版本中

使用正则表达式规则从输入键到规则中指定的目标键提取值。 如果目标密钥已存在，则将其重写。 它的行为类似于 " [Span 处理器](#extract-attributes-from-span-name)" 设置，其 `toAttributes` 现有属性作为 "源"。

```json
"processors": [
  {
    "type": "attribute",
    "actions": [
      {
        "key": "attribute1",
        "pattern": "<regular pattern with named matchers>",
        "action": "extract"
      },
    ]
  }
]
```
对于 `extract` 操作，以下项是必需的
* `key`
* `pattern`
* `action` : `extract`

有关详细信息，请查看 [遥测处理器示例](./java-standalone-telemetry-processors-examples.md) 文档。

## <a name="span-processors"></a>范围处理器

范围处理器修改范围名称或根据范围名称修改范围的属性。 它还可以支持用于包括/排除范围的功能。

### <a name="name-a-span"></a>为范围命名

作为 name 节的一部分，以下设置是必需的：

* `fromAttributes`：键的属性值用于按配置中指定的顺序创建新名称。 需要在范围中指定所有属性键，处理器才能对其进行重命名。

还可以配置以下设置：

* `separator`：一个指定的字符串，将用来拆分值
> [!NOTE]
> 如果重命名依赖于属性处理器修改的属性，请确保在管道规范中的属性处理器之后指定范围处理器。

```json
"processors": [
  {
    "type": "span",
    "name": {
      "fromAttributes": [
        "attributeKey1",
        "attributeKey2",
      ],
      "separator": "::"
    }
  }
] 
```

### <a name="extract-attributes-from-span-name"></a>从范围名称中提取属性

接受正则表达式的列表，以据其匹配范围名称，并根据子表达式从中提取属性。 必须在 `toAttributes` 节下指定。

以下设置是必需的：

`rules`：用于从范围名称中提取属性值的规则列表。 范围名称中的值将替换为提取的属性名称。 列表中的每项规则都是一个正则表达式模式字符串。 将依据正则表达式来检查范围名称。 如果正则表达式匹配，则正则表达式的所有已命名子表达式都会被提取为属性并添加到范围中。 每个子表达式名称会成为一个属性名称，子表达式匹配部分会成为属性值。 范围名称中的匹配部分会替换为提取的属性名称。 如果属性已存在于范围中，则会被覆盖。 将按指定顺序为所有规则重复此过程。 每项后续规则都应用于处理上一规则后输出的范围名称。

```json

"processors": [
  {
    "type": "span",
    "name": {
      "toAttributes": {
        "rules": [
          "rule1",
          "rule2",
          "rule3"
        ]
      }
    }
  }
]

```

## <a name="list-of-attributes"></a>属性列表

下面列出了一些可在遥测处理程序中使用的常见范围属性。

### <a name="http-spans"></a>HTTP 范围

| Attribute  | 类型 | 说明 | 
|---|---|---|
| `http.method` | 字符串 | HTTP 请求方法。|
| `http.url` | 字符串 | 完整的 HTTP 请求 URL，格式为 `scheme://host[:port]/path?query[#fragment]` 。 通常情况下，片段不通过 HTTP 传输，但如果已知，则它应包括在内。|
| `http.status_code` | 数字 | [HTTP 响应状态代码](https://tools.ietf.org/html/rfc7231#section-6)。|
| `http.flavor` | 字符串 | 使用的 HTTP 协议类型 |
| `http.user_agent` | 字符串 | 客户端发送的 [HTTP 用户代理](https://tools.ietf.org/html/rfc7231#section-5.5.3) 标头的值。 |

### <a name="jdbc-spans"></a>JDBC 跨越

| Attribute  | 类型 | 说明  |
|---|---|---|
| `db.system` | 字符串 | 数据库管理系统的标识符 (要使用的 DBMS) 产品。 |
| `db.connection_string` | 字符串 | 用于连接到数据库的连接字符串。 建议删除嵌入的凭据。|
| `db.user` | 字符串 | 用于访问数据库的用户名。 |
| `db.name` | 字符串 | 此属性用于报告正在访问的数据库的名称。 对于用于切换数据库的命令，应将其设置为目标数据库 (即使命令失败) 也是如此。|
| `db.statement` | 字符串 | 正在执行的数据库语句。|