---
title: 遥测处理器示例 - 适用于 Java 的 Azure Monitor Application Insights
description: 学习用于演示适用于 Java 的 Azure Monitor Application Insights 中的遥测处理器的示例。
ms.topic: conceptual
ms.date: 12/29/2020
author: kryalama
ms.custom: devx-track-java
ms.author: kryalama
ms.openlocfilehash: 0978bd669855d264ed6dfa5eeddc45ad499aa2a5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "101734581"
---
# <a name="telemetry-processor-examples---azure-monitor-application-insights-for-java"></a>遥测处理器示例 - 适用于 Java 的 Azure Monitor Application Insights

本文提供适用于 Java 的 Application Insights 中的遥测处理器示例。 其中提供了包含和排除配置的示例。 此外，还提供了特性处理器和范围处理器的示例。
## <a name="include-and-exclude-samples"></a>包含和排除示例

本部分介绍如何包含和排除范围。 此外，介绍如何排除多个范围和应用选择性处理。
### <a name="include-spans"></a>包含范围

本部分介绍如何包含特性处理器的范围。 处理器不会处理与属性不匹配的范围。

范围名称必须等于 `spanA` 或 `spanB` 才能视为匹配。 

以下范围与包含属性匹配，将应用处理器操作：
* Span1 Name: 'spanA' Attributes: {env: dev, test_request:123, credit_card:1234}
* Span2 Name: 'spanB' Attributes: {env: dev, test_request: false}
* Span3 Name: 'spanA' Attributes: {env:1, test_request: dev, credit_card:1234}

此范围与包含属性不匹配，将不应用处理器操作：
* Span4 Name: 'spanC' Attributes: {env: dev, test_request: false}

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
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
        "actions": [
          {
            "key": "credit_card",
            "action": "delete"
          }
        ]
      }
    ]
  }
}
```

### <a name="exclude-spans"></a>排除范围

本部分演示如何排除特性处理器的范围。 此处理器不会处理与属性匹配的范围。

范围名称必须等于 `spanA` 或 `spanB` 才能视为匹配。

以下范围与排除属性匹配，将不应用处理器操作：
* Span1 Name: 'spanA' Attributes: {env: dev, test_request:123, credit_card:1234}
* Span2 Name: 'spanB' Attributes: {env: dev, test_request: false}
* Span3 Name: 'spanA' Attributes: {env:1, test_request: dev, credit_card:1234}

此范围与排除属性不匹配，将应用处理器操作：
* Span4 Name: 'spanC' Attributes: {env: dev, test_request: false}

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "exclude": {
          "matchType": "strict",
          "spanNames": [
            "spanA",
            "spanB"
          ]
        },
        "actions": [
          {
            "key": "credit_card",
            "action": "delete"
          }
        ]
      }
    ]
  }
}
```

### <a name="exclude-spans-by-using-multiple-criteria"></a>使用多个条件来排除范围

本部分演示如何排除特性处理器的范围。 此处理器不会处理与属性匹配的范围。

必须满足以下条件才视为匹配：
* 某个特性（例如 `env` 或 `dev`）必须存在于范围中。
* 范围必须具有一个键为 `test_request` 的特性。

以下范围与排除属性匹配，将不应用处理器操作。
* Span1 Name: 'spanB' Attributes: {env: dev, test_request:123, credit_card:1234}
* Span2 Name: 'spanA' Attributes: {env: dev, test_request: false}

以下范围与排除属性不匹配，将应用处理器操作：
* Span3 Name: 'spanB' Attributes: {env:1, test_request: dev, credit_card:1234}
* Span4 Name: 'spanC' Attributes: {env: dev, dev_request: false}


```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "exclude": {
          "matchType": "strict",
          "spanNames": [
            "spanA",
            "spanB"
          ],
          "attributes": [
            {
              "key": "env",
              "value": "dev"
            },
            {
              "key": "test_request"
            }
          ]
        },
        "actions": [
          {
            "key": "credit_card",
            "action": "delete"
          }
        ]
      }
    ]
  }
}
```

### <a name="selective-processing"></a>选择性处理

本部分介绍如何指定范围属性集，用于指示此处理器要应用到的范围。 包含属性指示应处理的范围。 排除属性筛选掉不应处理的范围。

在以下配置中，这些范围与属性匹配，将应用处理器操作：

* Span1 Name: 'spanB' Attributes: {env: production, test_request:123, credit_card:1234, redact_trace: "false"}
* Span2 Name: 'spanA' Attributes: {env: staging, test_request: false, redact_trace: true}

这些范围与包含属性不匹配，将不应用处理器操作：
* Span3 Name: 'spanB' Attributes: {env: production, test_request: true, credit_card:1234, redact_trace: false}
* Span4 Name: 'spanC' Attributes: {env: dev, test_request: false}

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
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
  }
}
```
## <a name="attribute-processor-samples"></a>特性处理器示例

### <a name="insert"></a>插入

以下示例将新特性 `{"attribute1": "attributeValue1"}` 插入到不存在键 `attribute1` 的范围中。

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "actions": [
          {
            "key": "attribute1",
            "value": "attributeValue1",
            "action": "insert"
          }
        ]
      }
    ]
  }
}
```

### <a name="insert-from-another-key"></a>从另一个键插入

以下示例使用 `anotherkey` 特性中的值将新特性 `{"newKey": "<value from attribute anotherkey>"}` 插入到不存在键 `newKey` 的范围中。 如果特性 `anotherkey` 不存在，则不会将新特性插入到范围中。

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "actions": [
          {
            "key": "newKey",
            "fromAttribute": "anotherKey",
            "action": "insert"
          }
        ]
      }
    ]
  }
}
```

### <a name="update"></a>更新

以下示例将特性更新为 `{"db.secret": "redacted"}`。 它使用特性 `foo` 中的值更新特性 `boo`。 没有特性 `boo` 的范围不会更改。

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "actions": [
          {
            "key": "db.secret",
            "value": "redacted",
            "action": "update"
          },
          {
            "key": "boo",
            "fromAttribute": "foo",
            "action": "update" 
          }
        ]
      }
    ]
  }
}
```

### <a name="delete"></a>删除

以下示例演示如何删除具有键 `credit_card` 的特性。

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "actions": [
          {
            "key": "credit_card",
            "action": "delete"
          }
        ]
      }
    ]
  }
}
```

### <a name="hash"></a>哈希

以下示例演示如何哈希化现有的特性值。

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "actions": [
          {
            "key": "user.email",
            "action": "hash"
          }
        ]
      }
    ]
  }
}
```

### <a name="extract"></a>Extract

以下示例演示如何使用正则表达式 (regex) 基于另一个特性的值创建新特性。
例如，在指定 `http.url = http://example.com/path?queryParam1=value1,queryParam2=value2` 的情况下，将插入以下特性：
* httpProtocol：`http`
* httpDomain：`example.com`
* httpPath：`path`
* httpQueryParams：`queryParam1=value1,queryParam2=value2`
* http.url：无变化

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "actions": [
          {
            "key": "http.url",
            "pattern": "^(?<httpProtocol>.*):\\/\\/(?<httpDomain>.*)\\/(?<httpPath>.*)(\\?|\\&)(?<httpQueryParams>.*)",
            "action": "extract"
          }
        ]
      }
    ]
  }
}
```

以下示例演示如何处理其范围名称与正则表达式模式匹配的范围。
此处理器将删除 `token` 特性。 它将模糊化范围名称与 `auth.*` 匹配、但不与 `login.*` 匹配的范围中的 `password` 特性。

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "include": {
          "matchType": "regexp",
          "spanNames": [
            "auth.*"
          ]
        },
        "exclude": {
          "matchType": "regexp",
          "spanNames": [
            "login.*"
          ]
        },
        "actions": [
          {
            "key": "password",
            "value": "obfuscated",
            "action": "update"
          },
          {
            "key": "token",
            "action": "delete"
          }
        ]
      }
    ]
  }
}
```


## <a name="span-processor-samples"></a>范围处理器示例

### <a name="name-a-span"></a>为范围命名

以下示例指定特性 `db.svc`、`operation` 和 `id` 的值。 它按照这种顺序使用这些特性（以值 `::` 分隔）构成了范围的新名称。
```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "span",
        "name": {
          "fromAttributes": [
            "db.svc",
            "operation",
            "id"
          ],
          "separator": "::"
        }
      }
    ]
  }
}
```

### <a name="extract-attributes-from-a-span-name"></a>从范围名称中提取特性

假设输入范围名称为 `/api/v1/document/12345678/update`。 以下示例将生成输出范围名称 `/api/v1/document/{documentId}/update`。 它将新特性 `documentId=12345678` 添加到范围。
```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "span",
        "name": {
          "toAttributes": {
            "rules": [
              "^/api/v1/document/(?<documentId>.*)/update$"
            ]
          }
        }
      }
    ]
  }
}
```

### <a name="extract-attributes-from-a-span-name-by-using-include-and-exclude"></a>使用 include 和 exclude 从范围名称中提取特性

以下示例演示如何将范围名称更改为 `{operation_website}`。 当范围具有以下属性时，此示例将添加键为 `operation_website`、值为 `{oldSpanName}` 的特性：
- 范围名称在字符串中的任意位置包含 `/`。
- 范围名称不是 `donot/change`。
```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "span",
        "include": {
          "matchType": "regexp",
          "spanNames": [
            "^(.*?)/(.*?)$"
          ]
        },
        "exclude": {
          "matchType": "strict",
          "spanNames": [
            "donot/change"
          ]
        },
        "name": {
          "toAttributes": {
            "rules": [
              "(?<operation_website>.*?)$"
            ]
          }
        }
      }
    ]
  }
}
```
