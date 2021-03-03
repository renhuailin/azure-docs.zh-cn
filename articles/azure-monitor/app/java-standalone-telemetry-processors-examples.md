---
title: 遥测处理器示例-Azure Monitor Java Application Insights
description: 探索在 Java Azure Monitor Application Insights 中显示遥测处理器的示例。
ms.topic: conceptual
ms.date: 12/29/2020
author: kryalama
ms.custom: devx-track-java
ms.author: kryalama
ms.openlocfilehash: 0978bd669855d264ed6dfa5eeddc45ad499aa2a5
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101734581"
---
# <a name="telemetry-processor-examples---azure-monitor-application-insights-for-java"></a>遥测处理器示例-Azure Monitor Java Application Insights

本文提供了 Application Insights for Java 中的遥测处理器的示例。 你将找到包含和排除配置的示例。 你还将找到属性处理器和跨越处理器的示例。
## <a name="include-and-exclude-samples"></a>包括和排除示例

在本部分中，你将了解如何包含和排除范围。 你还将了解如何排除多个范围和应用选择性处理。
### <a name="include-spans"></a>包含跨度

本部分说明如何为属性处理器包括范围。 不匹配属性的范围不由处理器处理。

匹配要求范围名称等于 `spanA` 或 `spanB` 。 

这些范围与 include 属性匹配，并且应用处理器操作：
* Span1 Name: 'spanA' Attributes: {env: dev, test_request:123, credit_card:1234}
* Span2 Name: 'spanB' Attributes: {env: dev, test_request: false}
* Span3 Name: 'spanA' Attributes: {env:1, test_request: dev, credit_card:1234}

此跨度与 include 属性不匹配，并且不应用处理器操作：
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

本部分演示如何排除特性处理器的范围。 与属性匹配的跨度不由此处理器处理。

匹配要求范围名称等于 `spanA` 或 `spanB` 。

以下范围与 exclude 属性匹配，并且不应用处理器操作：
* Span1 Name: 'spanA' Attributes: {env: dev, test_request:123, credit_card:1234}
* Span2 Name: 'spanB' Attributes: {env: dev, test_request: false}
* Span3 Name: 'spanA' Attributes: {env:1, test_request: dev, credit_card:1234}

此跨度与排除属性不匹配，并且应用处理器操作：
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

### <a name="exclude-spans-by-using-multiple-criteria"></a>通过使用多个条件排除范围

本部分演示如何排除特性处理器的范围。 与属性匹配的跨度不由此处理器处理。

匹配要求满足以下条件：
* 例如， `env` 或 `dev`) 的属性必须存在于跨度中 (。
* 范围必须具有具有键的特性 `test_request` 。

以下范围与 exclude 属性匹配，并且不应用处理器操作。
* Span1 Name: 'spanB' Attributes: {env: dev, test_request:123, credit_card:1234}
* Span2 Name: 'spanA' Attributes: {env: dev, test_request: false}

以下范围与排除属性不匹配，并且应用了处理器操作：
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

本部分说明如何指定范围属性，这些属性指示此处理器应应用到的范围。 Include 属性指示应处理的范围。 排除属性筛选掉不应处理的范围。

在下面的配置中，这些范围与属性匹配，并且应用处理器操作：

* Span1 Name: 'spanB' Attributes: {env: production, test_request:123, credit_card:1234, redact_trace: "false"}
* Span2 Name: 'spanA' Attributes: {env: staging, test_request: false, redact_trace: true}

这些范围与 include 属性不匹配，并且不应用处理器操作：
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
## <a name="attribute-processor-samples"></a>属性处理器示例

### <a name="insert"></a>插入

下面的示例将新属性插入 `{"attribute1": "attributeValue1"}` 到不存在键的范围中 `attribute1` 。

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

下面的示例使用属性中的值 `anotherkey` 将新属性插入 `{"newKey": "<value from attribute anotherkey>"}` 到不存在键的范围中 `newKey` 。 如果该属性 `anotherkey` 不存在，则不会在范围中插入新的属性。

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

下面的示例将属性更新为 `{"db.secret": "redacted"}` 。 它 `boo` 通过使用属性中的值更新属性 `foo` 。 未更改属性的范围 `boo` 。

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

下面的示例演示如何删除具有密钥的属性 `credit_card` 。

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

下面的示例演示如何对现有的属性值进行哈希处理。

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

下面的示例演示如何使用正则表达式 (regex) 基于另一个属性的值创建新属性。
例如，在给定 `http.url = http://example.com/path?queryParam1=value1,queryParam2=value2` 的情况下，插入以下属性：
* httpProtocol: `http`
* httpDomain: `example.com`
* HttpPath `path`
* httpQueryParams: `queryParam1=value1,queryParam2=value2`
* http .url： *无* 更改

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

下面的示例演示如何处理具有与正则表达式模式匹配的跨度的跨度。
此处理器将删除该 `token` 属性。 它 `password` 在 span 名称匹配的范围中进行模糊处理属性 `auth.*` ，并且范围名称不匹配 `login.*` 。

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


## <a name="span-processor-samples"></a>跨处理器示例

### <a name="name-a-span"></a>为范围命名

下面的示例指定属性 `db.svc` 、和的值 `operation` `id` 。 它通过使用这些属性（以该顺序分隔）来形成范围的新名称 `::` 。
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

### <a name="extract-attributes-from-a-span-name"></a>从范围名称提取属性

假设输入跨度的名称为 `/api/v1/document/12345678/update` 。 下面的示例将生成输出范围名称 `/api/v1/document/{documentId}/update` 。 它将新特性添加 `documentId=12345678` 到跨距。
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

### <a name="extract-attributes-from-a-span-name-by-using-include-and-exclude"></a>使用 include 和 exclude 从范围名称中提取属性

下面的示例演示如何将范围名称更改为 `{operation_website}` 。 `operation_website` `{oldSpanName}` 当跨度具有以下属性时，它将添加具有键和值的属性：
- 范围名称包含 `/` 在字符串中的任意位置。
- 跨度名称不为 `donot/change` 。
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
