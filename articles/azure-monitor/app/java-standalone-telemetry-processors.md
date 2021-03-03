---
title: 遥测处理器（预览版）- 适用于 Java 的 Azure Monitor Application Insights
description: 了解如何在 Azure Monitor Application Insights 中为 Java 配置遥测处理器。
ms.topic: conceptual
ms.date: 10/29/2020
author: kryalama
ms.custom: devx-track-java
ms.author: kryalama
ms.openlocfilehash: 35e53454e5b2c6265082bbedb4a8b60e82df7191
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101734564"
---
# <a name="telemetry-processors-preview---azure-monitor-application-insights-for-java"></a>遥测处理器（预览版）- 适用于 Java 的 Azure Monitor Application Insights

> [!NOTE]
> 遥测处理器功能处于预览阶段。

用于 Application Insights 的 Java 3.0 代理可以在导出数据之前处理遥测数据。

下面是遥测处理器的一些用例：
 * 创建敏感数据。
 * 有条件地添加自定义维度。
 * 更新用于聚合 Azure 门户中相似遥测的范围名称。
 * 用于控制引入成本的删除范围属性。

## <a name="terminology"></a>术语

在了解遥测处理器之前，应了解术语 *跨度*。 跨度是的一般术语：

* 传入的请求。
* 传出依赖关系 (例如，对另一个服务) 的远程调用。
* 例如，进程内依赖关系 (，它由服务) 的子组件完成。

对于遥测处理器，这些范围组件非常重要：

* 名称
* 特性

范围名称是 Azure 门户中的请求和依赖项的主要显示。 范围属性表示给定请求或依赖项的标准属性和自定义属性。

## <a name="telemetry-processor-types"></a>遥测处理器类型

目前，两种类型的遥测处理器都是属性处理器和跨越处理器。

特性处理器可以插入、更新、删除或哈希特性。
它还可以使用正则表达式从现有属性中提取一个或多个新属性。

Span 处理器可以更新遥测名称。
它还可以使用正则表达式从范围名称中提取一个或多个新属性。

> [!NOTE]
> 目前，遥测处理器仅处理类型字符串的属性。 它们不处理布尔值或数字类型的属性。

## <a name="getting-started"></a>入门

首先， *在上* 创建一个名为applicationinsights.js的配置文件。 将其保存在与 *applicationinsights.config \** 相同的目录中。 请使用以下模版。

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

## <a name="include-criteria-and-exclude-criteria"></a>包含条件和排除条件

属性处理器和范围处理器都支持可选的 `include` 和 `exclude` 条件。
处理器仅适用于符合条件的范围 `include` (如果提供了该处理器) _并且_ 不符合其 `exclude` 条件 (如果) 提供。

若要配置此选项，请在 "" `include` 或 " (" 或 "两者" `exclude`) ，指定至少一个 `matchType` 和 `spanNames` 或 `attributes` 。
包含-exclude 配置允许多个指定的条件。
所有指定的条件的计算结果都必须为 true，才能生成匹配项。 

* **必填字段**： `matchType` 控制如何 `spanNames` 解释数组和数组中的项 `attributes` 。 可能值为 `regexp` 和 `strict`。 

* 可选字段： 
    * `spanNames` 必须至少与一个项匹配。 
    * `attributes` 指定要匹配的属性的列表。 所有这些属性都必须完全匹配才能生成匹配项。
    
> [!NOTE]
> 如果同时 `include` 指定了和 `exclude` ，则将检查属性， `include` 然后 `exclude` 检查属性。

### <a name="sample-usage"></a>示例用法

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
有关详细信息，请参阅 [遥测处理器示例](./java-standalone-telemetry-processors-examples.md)。

## <a name="attribute-processor"></a>属性处理器

属性处理器修改范围的属性。 它可以支持包括或排除范围。 它将使用配置文件指定的顺序执行的操作列表。 处理器支持以下操作：

- `insert`
- `update`
- `delete`
- `hash`
- `extract`
### `insert`

`insert`操作在不存在键的范围内插入新属性。   

```json
"processors": [
  {
    "type": "attribute",
    "actions": [
      {
        "key": "attribute1",
        "value": "value1",
        "action": "insert"
      }
    ]
  }
]
```
`insert`操作需要以下设置：
* `key`
* `value`或`fromAttribute`
* `action`: `insert`

### `update`

`update`操作更新范围中的属性，该键已存在。

```json
"processors": [
  {
    "type": "attribute",
    "actions": [
      {
        "key": "attribute1",
        "value": "newValue",
        "action": "update"
      }
    ]
  }
]
```
`update`操作需要以下设置：
* `key`
* `value`或`fromAttribute`
* `action`: `update`


### `delete` 

`delete`操作删除范围中的属性。

```json
"processors": [
  {
    "type": "attribute",
    "actions": [
      {
        "key": "attribute1",
        "action": "delete"
      }
    ]
  }
]
```
`delete`操作需要以下设置：
* `key`
* `action`: `delete`

### `hash`

`hash`操作 (SHA1) 现有属性值的哈希值。

```json
"processors": [
  {
    "type": "attribute",
    "actions": [
      {
        "key": "attribute1",
        "action": "hash"
      }
    ]
  }
]
```
`hash`操作需要以下设置：
* `key`
* `action`: `hash`

### `extract`

> [!NOTE]
> 此 `extract` 功能仅在版本3.0.2 和更高版本中可用。

`extract`操作通过使用从输入键到规则指定的目标键的正则表达式规则来提取值。 如果目标密钥已存在，则将其重写。 此操作的行为类似于 " [span 处理器](#extract-attributes-from-the-span-name)" `toAttributes` 设置，其中现有的属性是 "源"。

```json
"processors": [
  {
    "type": "attribute",
    "actions": [
      {
        "key": "attribute1",
        "pattern": "<regular pattern with named matchers>",
        "action": "extract"
      }
    ]
  }
]
```
`extract`操作需要以下设置：
* `key`
* `pattern`
* `action`: `extract`

有关详细信息，请参阅 [遥测处理器示例](./java-standalone-telemetry-processors-examples.md)。

## <a name="span-processor"></a>范围处理器

范围处理器修改范围名称或根据范围名称修改范围的属性。 它可以支持包括或排除范围。

### <a name="name-a-span"></a>为范围命名

`name`节需要 `fromAttributes` 设置。 这些属性中的值用于创建新名称，并按配置指定的顺序进行连接。 仅当所有这些属性都存在于范围中时，处理器才会更改范围名称。

此 `separator` 设置是可选的。 此设置是一个字符串。 它指定了拆分值。
> [!NOTE]
> 如果重命名依赖于属性处理器来修改属性，请确保在管道规范中的属性处理器之后指定范围处理器。

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

### <a name="extract-attributes-from-the-span-name"></a>从范围名称提取属性

`toAttributes`部分列出了要与范围名称匹配的正则表达式。 它基于子表达式提取特性。

此 `rules` 设置是必需的。 此设置列出了用于从范围名称中提取属性值的规则。 

范围名称中的值将替换为提取的属性名称。 列表中的每个规则都是 (regex) 模式字符串的正则表达式。 

下面介绍了如何通过提取的属性名称替换值：

1. 对照 regex 检查范围名称。 
1. 如果正则表达式匹配，则 regex 的所有命名子表达式都将作为特性提取。 
1. 提取的属性将添加到范围中。 
1. 每个子表达式名称将成为属性名称。 
1. 子表达式匹配部分将成为特性值。 
1. 范围名称中的匹配部分被提取的属性名称替换。 如果这些属性已在范围中存在，则会被覆盖。 
 
按指定顺序对所有规则重复此过程。 每个后续规则都适用于作为上一规则输出的范围名称。

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

## <a name="common-span-attributes"></a>公共范围属性

本部分列出了遥测处理器可以使用的一些常见范围属性。

### <a name="http-spans"></a>HTTP 范围

| 属性  | 类型 | 说明 | 
|---|---|---|
| `http.method` | string | HTTP 请求方法。|
| `http.url` | string | 完整的 HTTP 请求 URL（采用 `scheme://host[:port]/path?query[#fragment]` 格式）。 片段通常不通过 HTTP 传输。 但如果该片段是已知的，则应将其包含在内。|
| `http.status_code` | 数值 | [HTTP 响应状态代码](https://tools.ietf.org/html/rfc7231#section-6)。|
| `http.flavor` | string | HTTP 协议的类型。 |
| `http.user_agent` | string | 客户端发送的 [HTTP User-Agent](https://tools.ietf.org/html/rfc7231#section-5.5.3) 标头的值。 |

### <a name="jdbc-spans"></a>JDBC 跨越

| 属性  | 类型 | 说明  |
|---|---|---|
| `db.system` | 字符串 | 数据库管理系统 (DBMS) 使用的产品的标识符。 |
| `db.connection_string` | 字符串 | 用于连接到数据库的连接字符串。 建议删除嵌入的凭据。|
| `db.user` | string | 用于访问数据库的用户名。 |
| `db.name` | string | 用于报告正在访问的数据库的名称的字符串。 对于用于切换数据库的命令，应将此字符串设置为目标数据库，即使该命令失败也是如此。|
| `db.statement` | 字符串 | 正在运行的数据库语句。|
