---
title: " (预览版的遥测处理器) -Azure Monitor Application Insights Java"
description: 如何在 Azure Monitor Application Insights 中为 Java 配置遥测处理器
ms.topic: conceptual
ms.date: 10/29/2020
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.openlocfilehash: 7fd53c77b64e028ffad25c8fa7a9eefd95439513
ms.sourcegitcommit: ea17e3a6219f0f01330cf7610e54f033a394b459
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/14/2020
ms.locfileid: "97387150"
---
# <a name="telemetry-processors-preview---azure-monitor-application-insights-for-java"></a> (预览版的遥测处理器) -Azure Monitor Application Insights Java

> [!NOTE]
> 此功能仍处于预览阶段。

适用于 Application Insights 的 Java 3.0 代理现在具有在导出数据之前处理遥测数据的功能。

### <a name="some-use-cases"></a>一些用例：
 * 屏蔽敏感数据
 * 有条件地添加自定义维度
 * 更新用于聚合和显示的遥测名称

### <a name="supported-processors"></a>支持的处理器：
 * 特性处理器
 * 跨越处理器

## <a name="to-get-started"></a>入门指南

创建一个名为的配置文件 `applicationinsights.json` ，并 `applicationinsights-agent-***.jar` 使用以下模板将其放在与相同的目录中。

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

特性处理器和跨度处理器公开选项以提供一组要匹配的范围的属性，以确定是否应在处理器中包含或排除范围。 若要配置此选项，请在 `include` 和/下 `exclude` 至少 `matchType` 需要一个和一个 `spanNames` 或 `attributes` 。 支持包含/排除配置具有多个指定的条件。 所有指定的条件的计算结果都必须为 true，才能发生匹配。 

**必填字段**： 
* `matchType` 控制如何 `spanNames` 解释和数组中的项 `attributes` 。 可能的值包括 `regexp` 或 `strict`。 

**可选字段**： 
* `spanNames` 必须至少与一个项匹配。 
* `attributes` 指定要匹配的属性的列表。 所有这些属性必须完全匹配才能出现匹配项。

> [!NOTE]
> 如果同时 `include` 指定了和 `exclude` ，则 `include` 先检查属性，然后再检查 `exclude` 属性。

#### <a name="sample-usage"></a>示例用法

下面的示例演示如何指定范围属性，以指示此处理器应应用到的范围。 `include`属性的假设应该包含哪些属性，而 `exclude` 属性进一步筛选掉不应处理的范围。

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
            "svcA",
            "svcB"
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

对于上述配置，将应用以下范围来匹配属性和处理器操作：

* Span1 Name： ' svcB ' 特性： {env：生产 test_request：123，credit_card：1234，redact_trace： "false"}

* Span2 Name： ' svcA ' 特性： {env：暂存、test_request： false、redact_trace： true}

以下范围与 include 属性不匹配，并且不应用处理器操作：

* Span3 Name： ' svcB ' 属性： {env：生产，test_request： true，credit_card：1234，redact_trace： false}

* Span4 Name： ' svcC ' 特性： {env： dev，test_request： false}

## <a name="attribute-processor"></a>特性处理器 

属性处理器修改范围的属性。 它还可以支持包含/排除范围的功能。
它采用在配置文件中指定的顺序执行的操作列表。 支持的操作包括：

* `insert` ：在不存在键的范围内插入新属性
* `update` ：更新范围中存在键的属性
* `delete` ：从范围中删除属性
* `hash`   ：哈希 (SHA1) 现有属性值

对于操作 `insert` 和 `update`
* `key` 是必需的
* 需要或中的一个 `value` `fromAttribute`
* 需要 `action`。

对于 `delete` 操作，
* `key` 是必需的
* `action`： `delete` 是必需的。

对于 `hash` 操作，
* `key` 是必需的
* `action` ： `hash` 是必需的。

可以编写操作列表来创建丰富的方案，如后退填充属性，将值复制到新的密钥，编校敏感信息。

#### <a name="sample-usage"></a>示例用法

下面的示例演示如何在范围中插入键/值：

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
            "value": "value1",
            "action": "insert"
          },
          {
            "key": "key1",
            "fromAttribute": "anotherkey",
            "action": "insert"
          }
        ]
      }
    ]
  }
}
```

下面的示例演示如何将处理器配置为仅更新属性中的现有密钥：

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "actions": [
          {
            "key": "piiattribute",
            "value": "redacted",
            "action": "update"
          },
          {
            "key": "credit_card",
            "action": "delete"
          },
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

下面的示例演示如何处理其跨度名称与 regexp 模式相匹配的跨度。
此处理器将删除 "token" 属性，并将在范围名称与 "auth" 匹配的范围内模糊处理 "password" \* 特性。和，其中 span 名称与 "login." 不匹配 \* 。

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

## <a name="span-processors"></a>跨处理器

范围处理器根据范围名称修改范围的范围名称或属性。 它还可以支持包含/排除范围的功能。

### <a name="name-a-span"></a>命名范围

以下设置是 name 部分的一部分必需的：

* `fromAttributes`：键的特性值用于按配置中指定的顺序创建新名称。 需要在范围中指定所有属性键，处理器才能对其进行重命名。

可以选择配置以下设置：

* `separator`：指定的字符串，将用于拆分值
> [!NOTE]
> 如果重命名依赖于属性处理器修改的属性，请确保在管道规范中的属性处理器之后指定范围内的处理器。

#### <a name="sample-usage"></a>示例用法

下面的示例指定属性 "db-library"、"操作" 和 "id" 的值将形成范围的新名称，该名称由值 "：：" 分隔。
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

### <a name="extract-attributes-from-span-name"></a>从范围名称提取属性

获取正则表达式列表，使其与范围名称匹配，并根据子表达式从该列表中提取属性。 必须在 `toAttributes` 部分中指定。

以下设置是必需的：

`rules` ：用于从范围名称中提取属性值的规则的列表。 范围名称中的值由提取的属性名称替换。 列表中的每个规则都是 regex 模式字符串。 对照 regex 检查范围名称。 如果正则表达式匹配，则 regex 的所有命名子表达式都作为属性提取，并添加到范围中。 每个子表达式名称成为属性名称，子表达式匹配部分将成为属性值。 范围名称中的匹配部分被提取的属性名称替换。 如果该属性已在跨度中存在，则将覆盖这些属性。 按指定顺序对所有规则重复此过程。 每个后续规则都适用于处理上一规则后输出的范围名称。

#### <a name="sample-usage"></a>示例用法

假设输入范围名称为/api/v1/document/12345678/update。 将以下结果应用于输出范围名称/api/v1/document/{documentId}/update 会将一个新属性 "documentId" = "12345678" 添加到跨距。
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

下面演示了将范围名称重命名为 "{operation_website}"，并在跨距具有以下属性时添加属性 {Key： operation_website，Value： oldSpanName}：
- 范围名称包含字符串中任何位置的 "/"。
- 跨度名称不是 "不/change"。
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