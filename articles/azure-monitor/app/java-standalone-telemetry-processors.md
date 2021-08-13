---
title: 遥测处理器（预览版）- 适用于 Java 的 Azure Monitor Application Insights
description: 了解如何在适用于 Java 的 Azure Monitor Application Insights 中配置遥测处理器。
ms.topic: conceptual
ms.date: 10/29/2020
author: kryalama
ms.custom: devx-track-java
ms.author: kryalama
ms.openlocfilehash: 79d46d7f967ec5d6a223b5ec8690536b3a84162a
ms.sourcegitcommit: 23040f695dd0785409ab964613fabca1645cef90
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/14/2021
ms.locfileid: "112061535"
---
# <a name="telemetry-processors-preview---azure-monitor-application-insights-for-java"></a>遥测处理器（预览版）- 适用于 Java 的 Azure Monitor Application Insights

> [!NOTE]
> 遥测处理器功能为预览版。

Application Insights Java 3.x 可以在导出遥测数据之前处理该数据。

下面是遥测处理器的几个用例：
 * 屏蔽敏感数据。
 * 有条件地添加自定义维度。
 * 更新用于在 Azure 门户中聚合类似遥测数据的范围名称。
 * 删除特定范围属性以控制引入成本。
 * 筛选出某些指标以控制引入成本。

> [!NOTE]
> 如果希望删除特定（整体）范围来控制引入成本，请参阅[采样替代](./java-standalone-sampling-overrides.md)。

## <a name="terminology"></a>术语

在了解遥测处理器之前，你应当理解术语“跨度”和“日志”。

跨度是一种遥测类型，它代表的是：

* 传入请求。
* 传出依赖项（例如，对另一个服务的远程调用）。
* 进程内依赖项（例如，由服务的子组件所做的工作）。

日志是代表以下内容的遥测类型：

* 从 log4j、logback 和 java util 记录中捕获的日志数据 

对于遥测处理器而言，以下跨度/日志组件非常重要：

* 名称
* 正文
* 特性

范围名称是 Azure 门户中的请求和依赖项的主显示名称。 范围属性表示给定请求或依赖项的标准属性和自定义属性。

跟踪消息或正文是 Azure 门户中日志的主要显示。 日志属性表示给定日志的标准属性和自定义属性

## <a name="telemetry-processor-types"></a>遥测处理器类型

目前，四种类型的遥测处理器为属性处理器、跨越处理器、日志处理器和指标筛选器。

属性处理器可以插入、更新、删除或摘要遥测项（`span` 或 `log`）的属性。
它还可以使用正则表达式从现有属性中提取一个或多个新属性。

跨度处理器可以更新请求和依赖项的遥测名称。
它还可以使用正则表达式从范围名称中提取一个或多个新属性。

日志处理器可以更新日志的遥测名称。
它还可以使用正则表达式从日志名称中提取一个或多个新属性。

指标筛选器可以筛选出指标，以帮助控制引入成本。

> [!NOTE]
> 目前，遥测处理器仅处理字符串类型的属性， 不处理布尔或数字类型的属性。

## <a name="getting-started"></a>入门

首先，创建一个名为“applicationinsights.json”的配置文件。 将其保存在与“applicationinsights-agent-\*.jar”相同的目录中。 请使用以下模版。

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
      },
      {
        "type": "log",
        ...
      },
      {
        "type": "metric-filter",
        ...
      }
    ]
  }
}
```

## <a name="attribute-processor"></a>属性处理器

属性处理器修改 `span` 或 `log` 的属性。 它可以支持包括或排除 `span` 或 `log` 的功能。 它接受一个操作列表，这些操作按配置文件指定的顺序执行。 处理器支持以下操作：

- `insert`
- `update`
- `delete`
- `hash`
- `extract`

### `insert`

`insert` 操作在遥测项中插入一个新属性（如果 `key` 尚不存在）。   

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
`insert` 操作需要以下设置：
* `key`
* `value` 或 `fromAttribute`
* `action`: `insert`

### `update`

`update` 操作更新遥测项中的属性（如果 `key` 已经存在）。

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
`update` 操作需要以下设置：
* `key`
* `value` 或 `fromAttribute`
* `action`: `update`


### `delete` 

`delete` 操作从遥测项中删除属性。

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
`delete` 操作需要以下设置：
* `key`
* `action`: `delete`

### `hash`

`hash` 操作哈希化 (SHA1) 现有属性值。

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
`hash` 操作需要以下设置：
* `key`
* `action`: `hash`

### `extract`

> [!NOTE]
> 只有 3.0.2 及更高版本中提供了 `extract` 功能。

`extract` 操作使用正则表达式规则将值从输入键提取到规则指定的目标键。 如果某个目标键已存在，则会覆盖该目标键。 此操作的行为类似于[范围处理器](#extract-attributes-from-the-span-name) `toAttributes` 设置，其中，现有属性是源。

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
`extract` 操作需要以下设置：
* `key`
* `pattern`
* `action`: `extract`

### <a name="include-criteria-and-exclude-criteria"></a>包括条件和排除条件

属性处理器支持可选的 `include` 和 `exclude` 条件。
属性处理器仅应用于与其 `include` 条件（如果有）匹配，且与其 `exclude` 条件（如果有）不匹配的那些遥测。

若要配置此选项，请在 `include` 和/或 `exclude` 下指定至少一个 `matchType` 以及 `spanNames` 或 `attributes`。
include-exclude 配置允许多个指定的条件。
所有指定条件的评估结果都必须为 true 才会被视为匹配。 

* 必填字段：`matchType` 控制 `spanNames` 数组和 `attributes` 数组中的项的解释方式。 可能值为 `regexp` 和 `strict`。 

* 可选字段： 
    * `spanNames` 必须至少与一个项匹配。 
    * `attributes` 指定要匹配的属性的列表。 所有这些属性必须完全匹配才会被视为匹配。
    
> [!NOTE]
> 如果同时指定了 `include` 和 `exclude`，则会在检查 `exclude` 属性之前检查 `include` 属性。

> [!NOTE]
> 如果 `include` 或 `exclude` 配置未指定 `spanNames`，则将匹配条件应用于 `spans` 和 `logs`。

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
有关详细信息，请参阅[遥测处理器示例](./java-standalone-telemetry-processors-examples.md)。

## <a name="span-processor"></a>范围处理器

范围处理器修改范围名称或根据范围名称修改范围的属性。 它可以支持包括或排除范围的功能。

### <a name="name-a-span"></a>为范围命名

`name` 节需要 `fromAttributes` 设置。 这些属性中的值用于创建新名称，并按配置指定的顺序进行连接。 仅当所有这些属性都存在于范围中时，处理器才会更改范围名称。

`separator` 设置是可选的。 此设置是一个字符串。 指定它是为了对值进行拆分。
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

### <a name="extract-attributes-from-the-span-name"></a>从范围名称中提取属性

`toAttributes` 节列出了要根据其来匹配范围名称的正则表达式。 它基于子表达式提取属性。

`rules` 设置是必需的。 此设置列出了用于从范围名称中提取属性值的规则。 

范围名称中的值将替换为提取的属性名称。 列表中的每个规则都是一个正则表达式 (regex) 模式字符串。 

下面介绍了如何通过提取的属性名称替换值：

1. 依据正则表达式来检查范围名称。 
2. 如果正则表达式匹配，则正则表达式的所有已命名子表达式都会被提取为属性。 
3. 提取的属性被添加到范围中。 
4. 每个子表达式名称成为一个属性名称。 
5. 子表达式匹配部分成为属性值。 
6. 范围名称中的匹配部分被替换为提取的属性名称。 如果属性已存在于范围中，则会被覆盖。 
 
将按指定顺序针对所有规则重复此过程。 每个后续规则都应用于处理上一规则后输出的范围名称。

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

## <a name="common-span-attributes"></a>常见范围属性

本部分列出了遥测处理器可以使用的一些常见范围属性。

### <a name="http-spans"></a>HTTP 范围

| 属性  | 类型 | 说明 | 
|---|---|---|
| `http.method` | string | HTTP 请求方法。|
| `http.url` | string | 完整的 HTTP 请求 URL（采用 `scheme://host[:port]/path?query[#fragment]` 格式）。 片段通常不通过 HTTP 传输。 但如果片段是已知的，则应将其包含在内。|
| `http.status_code` | 数值 | [HTTP 响应状态代码](https://tools.ietf.org/html/rfc7231#section-6)。|
| `http.flavor` | string | HTTP 协议的类型。 |
| `http.user_agent` | string | 客户端发送的 [HTTP User-Agent](https://tools.ietf.org/html/rfc7231#section-5.5.3) 标头的值。 |

### <a name="jdbc-spans"></a>JDBC 范围

| 属性  | 类型 | 说明  |
|---|---|---|
| `db.system` | 字符串 | 正在使用的数据库管理系统 (DBMS) 产品的标识符。 |
| `db.connection_string` | 字符串 | 用于连接到数据库的连接字符串。 建议删除嵌入的凭据。|
| `db.user` | string | 用于访问数据库的用户名。 |
| `db.name` | string | 用于报告正在访问的数据库的名称的字符串。 对于用于切换数据库的命令，应当将此字符串设置为目标数据库（即使该命令失败）。|
| `db.statement` | 字符串 | 正在运行的数据库语句。|

### <a name="include-criteria-and-exclude-criteria"></a>包括条件和排除条件

跨度处理器支持可选的 `include` 和 `exclude` 条件。
跨度处理器仅应用于与其 `include` 条件（如果有）匹配且与其 `exclude` 条件（如果有）不匹配的那些遥测。

若要配置此选项，请在 `include` 和/或 `exclude` 下指定至少一个 `matchType` 以及 `spanNames` 或 `attributes`。
include-exclude 配置允许多个指定的条件。
所有指定条件的评估结果都必须为 true 才会被视为匹配。 

* 必填字段：`matchType` 控制 `spanNames` 数组和 `attributes` 数组中的项的解释方式。 可能值为 `regexp` 和 `strict`。 

* 可选字段： 
    * `spanNames` 必须至少与一个项匹配。 
    * `attributes` 指定要匹配的属性的列表。 所有这些属性必须完全匹配才会被视为匹配。
    
> [!NOTE]
> 如果同时指定了 `include` 和 `exclude`，则会在检查 `exclude` 属性之前检查 `include` 属性。

### <a name="sample-usage"></a>示例用法

```json
"processors": [
  {
    "type": "span",
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
          "key": "attribute1",
          "value": "attributeValue1"
        }
      ]
    },
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
有关详细信息，请参阅[遥测处理器示例](./java-standalone-telemetry-processors-examples.md)。

## <a name="log-processor"></a>日志处理器

> [!NOTE]
> 从版本 3.1.1 开始提供日志处理器。

日志处理器根据日志消息正文修改日志消息正文或日志属性。 它可以支持包括或排除日志的功能。

### <a name="update-log-message-body"></a>更新日志消息正文

`body` 节需要 `fromAttributes` 设置。 利用这些属性中的值来创建新的正文，并按配置指定的顺序进行连接。 仅当所有这些属性都存在于日志中时，处理器才会更改日志正文。

`separator` 设置是可选的。 此设置是一个字符串。 指定它是为了对值进行拆分。
> [!NOTE]
> 如果重命名依赖于属性处理器来修改属性，请确保在管道规范中的属性处理器之后指定日志处理器。

```json
"processors": [
  {
    "type": "log",
    "body": {
      "fromAttributes": [
        "attributeKey1",
        "attributeKey2",
      ],
      "separator": "::"
    }
  }
] 
```

### <a name="extract-attributes-from-the-log-message-body"></a>从日志消息正文中提取属性

`toAttributes` 部分列出了匹配日志消息正文的正则表达式。 它基于子表达式提取属性。

`rules` 设置是必需的。 此设置列出了用于从正文中提取属性值的规则。 

日志消息正文中的值将替换为提取的属性名称。 列表中的每个规则都是一个正则表达式 (regex) 模式字符串。 

下面介绍了如何通过提取的属性名称替换值：

1. 根据 regex 检查日志消息正文。 
2. 如果正则表达式匹配，则正则表达式的所有已命名子表达式都会被提取为属性。 
3. 提取的属性被添加到日志中。 
4. 每个子表达式名称成为一个属性名称。 
5. 子表达式匹配部分成为属性值。 
6. 日志名称中的匹配部分被替换为提取的属性名称。 如果属性已存在于日志中，则会被覆盖。 
 
将按指定顺序针对所有规则重复此过程。 每个后续规则都应用于处理上一规则后输出的日志名称。

```json
"processors": [
  {
    "type": "log",
    "body": {
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

### <a name="include-criteria-and-exclude-criteria"></a>包括条件和排除条件

日志处理器支持可选的 `include` 和 `exclude` 条件。
日志处理器仅应用于与其 `include` 条件（如果有）匹配且与其 `exclude` 条件（如果有）不匹配的那些遥测。

若要配置此选项，请在 `include` 和/或 `exclude` 下指定 `matchType` 和 `attributes`。
include-exclude 配置允许多个指定的条件。
所有指定条件的评估结果都必须为 true 才会被视为匹配。 

* 必填字段： 
  * `matchType` 控制如何解释 `attributes` 阵列中的项。 可能值为 `regexp` 和 `strict`。 
  * `attributes` 指定要匹配的属性的列表。 所有这些属性必须完全匹配才会被视为匹配。
    
> [!NOTE]
> 如果同时指定了 `include` 和 `exclude`，则会在检查 `exclude` 属性之前检查 `include` 属性。

> [!NOTE]
> 日志处理器不支持 `spanNames`。

### <a name="sample-usage"></a>示例用法

```json
"processors": [
  {
    "type": "log",
    "include": {
      "matchType": "strict",
      "attributes": [
        {
          "key": "attribute1",
          "value": "value1"
        }
      ]
    },
    "exclude": {
      "matchType": "strict",
      "attributes": [
        {
          "key": "attribute2",
          "value": "value2"
        }
      ]
    },
    "body": {
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
有关详细信息，请参阅[遥测处理器示例](./java-standalone-telemetry-processors-examples.md)。

## <a name="metric-filter"></a>指标筛选器

> [!NOTE]
> 从版本 3.1.1 开始，可以使用指标筛选器。

指标筛选器用于排除某些指标，以帮助控制引入成本。

指标筛选器仅支持 `exclude` 条件。 不会导出与其条件 `exclude` 匹配的指标。

若要配置此选项，在 `exclude` 下指定 `matchType` 一个或多个 `metricNames`。

* 必填字段：
  * `matchType` 控制如何匹配 `metricNames` 中的项。 可能值为 `regexp` 和 `strict`。
  * `metricNames` 必须至少与一个项匹配。

### <a name="sample-usage"></a>示例用法

```json
"processors": [
  {
    "type": "metric-filter",
    "exclude": {
      "matchType": "strict",
      "metricNames": [
        "metricA",
        "metricB"
      ]
    }
  }
]
```
