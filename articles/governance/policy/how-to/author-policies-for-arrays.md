---
title: 为资源上的数组属性创作策略
description: 了解如何使用数组参数和数组语言表达式，如何计算 [*] 别名，以及如何使用 Azure Policy 定义规则追加元素。
ms.date: 10/22/2020
ms.topic: how-to
ms.openlocfilehash: 60044d4a599c14088ea923a6a14cb46543646995
ms.sourcegitcommit: 03c0a713f602e671b278f5a6101c54c75d87658d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/19/2020
ms.locfileid: "94920451"
---
# <a name="author-policies-for-array-properties-on-azure-resources"></a>为 Azure 资源上的数组属性创作策略

Azure 资源管理器属性通常定义为字符串和布尔值。 存在一对多关系时，复杂属性将定义为数组。 在 Azure Policy 中，通过以下几种不同的方式来使用数组：

- [定义参数](../concepts/definition-structure.md#parameters)的类型，用于提供多个选项
- 使用条件 in 或 notIn 的[策略规则](../concepts/definition-structure.md#policy-rule)的一部分 
- 用于计算[\[\*\]别名](../concepts/definition-structure.md#understanding-the--alias)的策略规则的一部分：
  - 方案，如“无”、“任何”或“全部”  
  - 具有计数的复杂方案
- 在[追加效果](../concepts/effects.md#append)中，用于替换或添加到现有数组

本文介绍 Azure Policy 对每种方式的使用情况，并提供了几个示例定义。

## <a name="parameter-arrays"></a>参数数组

### <a name="define-a-parameter-array"></a>定义参数数组

需要多个值时，将参数定义为数组可以实现策略的灵活性。
此策略定义允许参数 allowedLocations 的任何单个位置，默认为 _eastus2_：

```json
"parameters": {
    "allowedLocations": {
        "type": "string",
        "metadata": {
            "description": "The list of allowed locations for resources.",
            "displayName": "Allowed locations",
            "strongType": "location"
        },
        "defaultValue": "eastus2"
    }
}
```

由于类型为字符串，因此在分配策略时只能设置一个值。 如果分配了此策略，则仅允许在单个 Azure 区域内使用范围内的资源。 大多数策略定义需要允许批准的选项列表，例如允许 eastus2、eastus 和 westus2。

若要创建策略定义以允许多个选项，请使用“数组”类型。 同一个策略可以重写，如下所示：

```json
"parameters": {
    "allowedLocations": {
        "type": "array",
        "metadata": {
            "description": "The list of allowed locations for resources.",
            "displayName": "Allowed locations",
            "strongType": "location"
        },
        "defaultValue": "eastus2",
        "allowedValues": [
            "eastus2",
            "eastus",
            "westus2"
        ]

    }
}
```

> [!NOTE]
> 保存策略定义后，无法更改参数上的属性。

在策略分配过程中，此新参数定义会使用多个值。 定义数组属性 allowedValues 后，分配期间可用的值将进一步限制为预定义的选项列表。 可以选择使用 allowedValues。

### <a name="pass-values-to-a-parameter-array-during-assignment"></a>在分配期间将值传递给参数数组

通过 Azure 门户分配策略时，数组类型的参数显示为单个文本框。 提示显示“使用 ; 来分隔值。 （例如，伦敦;纽约）”。 若要将 eastus2、eastus 和 westus2 的允许位置值传递到参数，请使用以下字符串：

`eastus2;eastus;westus2`

使用 Azure CLI、Azure PowerShell 或 REST API 时，参数值的格式不同。 这些值通过 JSON 字符串（还包括参数名称）传递。

```json
{
    "allowedLocations": {
        "value": [
            "eastus2",
            "eastus",
            "westus2"
        ]
    }
}
```

若要将此字符串与每个 SDK 一起使用，请使用以下命令：

- Azure CLI：带有参数 params 的命令 [az policy assignment create](/cli/azure/policy/assignment#az_policy_assignment_create)
- Azure PowerShell：带有参数 PolicyParameter 的 Cmdlet [New-AzPolicyAssignment](/powershell/module/az.resources/New-Azpolicyassignment)
- REST API：在 PUT [create](/rest/api/resources/policyassignments/create) 操作中，作为请求正文（作为 properties.parameters 属性的值）的一部分

## <a name="array-conditions"></a>数组条件

可与参数的数组
类型一起使用的策略规则[条件](../concepts/definition-structure.md#conditions)限制为 `in` 和 `notIn`。 以带有条件 `equals` 的以下策略定义为例：

```json
{
  "policyRule": {
    "if": {
      "not": {
        "field": "location",
        "equals": "[parameters('allowedLocations')]"
      }
    },
    "then": {
      "effect": "audit"
    }
  },
  "parameters": {
    "allowedLocations": {
      "type": "Array",
      "metadata": {
        "description": "The list of allowed locations for resources.",
        "displayName": "Allowed locations",
        "strongType": "location"
      }
    }
  }
}
```

尝试通过 Azure 门户创建此策略定义会导致类似如下的错误消息：

- “由于验证错误，无法对策略‘{GUID}’进行参数化。 请检查策略参数定义是否正确。 内部异常语言表达式‘[parameters('allowedLocations')]’的计算结果为‘数组’类型，预期类型为‘字符串’。”

条件 `equals` 的预期类型为 _字符串_。 由于 allowedLocations 被定义为数组类型，因此策略引擎会计算语言表达式并引发错误 。 在 `in` 和 `notIn` 条件下，策略引擎在语言表达式中应为“数组”类型。 若要解决此错误消息，请将 `equals` 更改为 `in` 或 `notIn`。

## <a name="referencing-array-resource-properties"></a>引用数组资源属性

许多用例要求使用计算资源中的数组属性。 某些方案需要引用整个数组 (例如，检查其长度) 。 其他人需要对每个单独的数组成员应用条件 (例如，确保所有防火墙规则阻止从 internet) 的访问。 了解 Azure 策略引用资源属性的不同方式，以及这些引用在引用数组属性时的行为方式，是用于编写涵盖这些方案的条件的关键。

### <a name="referencing-resource-properties"></a>引用资源属性
Azure 策略可以使用 [别名](../concepts/definition-structure.md#aliases) 引用资源属性可通过两种方法在 azure 策略中引用资源属性的值：

- 使用 [字段](../concepts/definition-structure.md#fields) 条件来检查 **所有** 选定的资源属性是否满足条件。 例如：

  ```json
  {
    "field" : "Microsoft.Test/resourceType/property",
    "equals": "value"
  }
  ```

- 使用 `field()` 函数访问属性的值。 例如：

  ```json
  {
    "value": "[take(field('Microsoft.Test/resourceType/property'), 7)]",
    "equals": "prefix_"
  }
  ```

字段条件具有隐式的 "所有" 行为。 如果别名表示值的集合，则它会检查是否所有个别值都满足条件。 函数按原样 `field()` 返回由别名表示的值，然后其他模板函数可以对其进行操作。

### <a name="referencing-array-fields"></a>引用数组字段

数组资源属性通常由两种不同类型的别名表示。 附加到它的一个 "常规" 别名和 [数组别名](../concepts/definition-structure.md#understanding-the--alias) `[*]` ：

- `Microsoft.Test/resourceType/stringArray`
- `Microsoft.Test/resourceType/stringArray[*]`

#### <a name="referencing-the-array"></a>引用数组

第一个别名表示单个值，即 `stringArray` 来自请求内容的属性的值。 由于该属性的值是一个数组，因此在策略条件中不太有用。 例如：

```json
{
  "field": "Microsoft.Test/resourceType/stringArray",
  "equals": "..."
}
```

此条件将整个 `stringArray` 数组与单个字符串值进行比较。 大多数情况下，包括 `equals` 仅接受字符串值，因此在将数组与字符串进行比较时没有多大用处。 引用 array 属性的主要方案是检查是否存在该属性：

```json
{
  "field": "Microsoft.Test/resourceType/stringArray",
  "exists": "true"
}
```

使用 `field()` 函数时，返回的值是请求内容中的数组，然后可以将其与接受数组参数的任何 [支持的模板函数](../concepts/definition-structure.md#policy-functions) 结合使用。 例如，以下条件检查的长度是否 `stringArray` 大于0：

```json
{
  "value": "[length(field('Microsoft.Test/resourceType/stringArray'))]",
  "greater": 0
}
```

#### <a name="referencing-the-array-members-collection"></a>引用数组成员集合

使用语法的别名 `[*]` 表示 **从数组属性中选择的属性值的集合**，这不同于选择数组属性本身。 对于 `Microsoft.Test/resourceType/stringArray[*]` ，它将返回一个集合，其中包含所有的成员 `stringArray` 。 如前所述， `field` 条件检查所有选定的资源属性是否满足条件，因此仅当的 **所有** 成员 `stringArray` 都等于 "" 值 "" 时，以下条件才为 true。

```json
{
  "field": "Microsoft.Test/resourceType/stringArray[*]",
  "equals": "value"
}
```

如果数组包含对象，则 `[*]` 可以使用别名从每个数组成员中选择特定属性的值。 例如：

```json
{
  "field": "Microsoft.Test/resourceType/objectArray[*].property",
  "equals": "value"
}
```

如果中所有属性的值都等于，则此条件为 true `property` `objectArray` `"value"` 。

使用 `field()` 函数引用数组别名时，返回的值是所有选定值的数组。 此行为意味着函数的常见用例，将 `field()` 模板函数应用于资源属性值的功能非常有限。 在这种情况下，唯一可以使用的模板函数是接受数组参数的模板函数。 例如，可以通过获取数组的长度 `[length(field('Microsoft.Test/resourceType/objectArray[*].property'))]` 。 但是，更复杂的方案（如将模板函数应用于每个数组成员，并将其与所需值进行比较）仅在使用表达式时才可行 `count` 。 有关详细信息，请参阅 [计数表达式](#count-expressions)。

概括而言，请参阅以下示例资源内容和各种别名返回的所选值：

```json
{
  "tags": {
    "env": "prod"
  },
  "properties":
  {
    "stringArray": [ "a", "b", "c" ],
    "objectArray": [
      {
        "property": "value1",
        "nestedArray": [ 1, 2 ]
      },
      {
        "property": "value2",
        "nestedArray": [ 3, 4 ]
      }
    ]
  }
}
```

对示例资源内容使用 "字段" 条件时，结果如下所示：

| Alias | 选定值 |
|:--- |:---|
| `Microsoft.Test/resourceType/missingArray` | `null` |
| `Microsoft.Test/resourceType/missingArray[*]` | 值的空集合。 |
| `Microsoft.Test/resourceType/missingArray[*].property` | 值的空集合。 |
| `Microsoft.Test/resourceType/stringArray` | `["a", "b", "c"]` |
| `Microsoft.Test/resourceType/stringArray[*]` | `"a"`, `"b"`, `"c"` |
| `Microsoft.Test/resourceType/objectArray[*]` |  `{ "property": "value1", "nestedArray": [ 1, 2 ] }`,<br/>`{ "property": "value2", "nestedArray": [ 3, 4 ] }`|
| `Microsoft.Test/resourceType/objectArray[*].property` | `"value1"`, `"value2"` |
| `Microsoft.Test/resourceType/objectArray[*].nestedArray` | `[ 1, 2 ]`, `[ 3, 4 ]` |
| `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` | `1`, `2`, `3`, `4` |

`field()`对示例资源内容使用函数时，结果如下所示：

| Expression | 返回值 |
|:--- |:---|
| `[field('Microsoft.Test/resourceType/missingArray')]` | `""` |
| `[field('Microsoft.Test/resourceType/missingArray[*]')]` | `[]` |
| `[field('Microsoft.Test/resourceType/missingArray[*].property')]` | `[]` |
| `[field('Microsoft.Test/resourceType/stringArray')]` | `["a", "b", "c"]` |
| `[field('Microsoft.Test/resourceType/stringArray[*]')]` | `["a", "b", "c"]` |
| `[field('Microsoft.Test/resourceType/objectArray[*]')]` |  `[{ "property": "value1", "nestedArray": [ 1, 2 ] }, { "property": "value2", "nestedArray": [ 3, 4 ] }]`|
| `[field('Microsoft.Test/resourceType/objectArray[*].property')]` | `["value1", "value2"]` |
| `[field('Microsoft.Test/resourceType/objectArray[*].nestedArray')]` | `[[ 1, 2 ], [ 3, 4 ]]` |
| `[field('Microsoft.Test/resourceType/objectArray[*].nestedArray[*]')]` | `[1, 2, 3, 4]` |

## <a name="count-expressions"></a>计数表达式

[计数](../concepts/definition-structure.md#count) 表达式计算满足某个条件的数组成员数量，并将该计数与目标值进行比较。 `Count` 与条件相比，计算数组更直观、更通用 `field` 。 语法为：

```json
{
  "count": {
    "field": <[*] alias>,
    "where": <optional policy condition expression>
  },
  "equals|greater|less|any other operator": <target value>
}
```

在不带 "where" 条件的情况下使用时， `count` 只返回数组的长度。 由于上一节中的示例资源内容，以下 `count` 表达式的计算结果为， `true` 因为 `stringArray` 有三个成员：

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/stringArray[*]"
  },
  "equals": 3
}
```

此行为也适用于嵌套数组。 例如，以下表达式的 `count` 计算结果为， `true` 因为数组中有四个数组成员 `nestedArray` ：

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/objectArray[*].nestedArray[*]"
  },
  "greaterOrEquals": 4
}
```

的幂 `count` `where` 。 指定时，Azure 策略将枚举数组成员，并根据条件评估每个成员，计算计算结果为多少数组成员 `true` 。 具体来说，在每次进行 `where` 条件评估时，Azure 策略都会选择单个数组成员 ***i** _，并根据条件计算资源 `where` 内容 _*，就好像 **_i_*_ 是 array_ * 的唯一成员。 在每个迭代中仅有一个数组成员可提供一种方法，用于对每个单个数组成员应用复杂的条件。

例如：
```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/stringArray[*]",
    "where": {
      "field": "Microsoft.Test/resourceType/stringArray[*]",
      "equals": "a"
    }
  },
  "equals": 1
}
```
为计算表达式的值 `count` ，Azure 策略将计算 `where` 条件3次，对的每个成员计算一次 `stringArray` ，计算计算结果的次数 `true` 。 如果 `where` 条件引用 `Microsoft.Test/resourceType/stringArray[*]` 数组成员，而不是选择的所有成员， `stringArray` 则每次只会选择单个数组成员：

| 迭代 | 选定 `Microsoft.Test/resourceType/stringArray[*]` 值 | `where` 评估结果 |
|:---|:---|:---|
| 1 | `"a"` | `true` |
| 2 | `"b"` | `false` |
| 3 | `"c"` | `false` |

因此， `count` 将返回 `1` 。

下面是一个更复杂的表达式：
```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/objectArray[*]",
    "where": {
      "allOf": [
        {
          "field": "Microsoft.Test/resourceType/objectArray[*].property",
          "equals": "value2"
        },
        {
          "field": "Microsoft.Test/resourceType/objectArray[*].nestedArray[*]",
          "greater": 2
        }
      ]
    }
  },
  "equals": 1
}
```

| 迭代 | 选定值 | `where` 评估结果 |
|:---|:---|:---|
| 1 | `Microsoft.Test/resourceType/objectArray[*].property` => `"value1"` </br> `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `1`, `2` | `false` |
| 2 | `Microsoft.Test/resourceType/objectArray[*].property` => `"value2"` </br> `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `3`, `4`| `true` |

因此，将 `count` 返回 `1` 。

`where`表达式是根据 **整个** 请求内容进行计算的， (仅对当前枚举的数组成员进行了更改) 这意味着该 `where` 条件还可以引用数组之外的字段：
```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/objectArray[*]",
    "where": {
      "field": "tags.env",
      "equals": "prod"
    }
  }
}
```

| 迭代 | 选定值 | `where` 评估结果 |
|:---|:---|:---|
| 1 | `tags.env` => `"prod"` | `true` |
| 2 | `tags.env` => `"prod"` | `true` |

还允许使用嵌套计数表达式：
```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/objectArray[*]",
    "where": {
      "allOf": [
        {
          "field": "Microsoft.Test/resourceType/objectArray[*].property",
          "equals": "value2"
        },
        {
          "count": {
            "field": "Microsoft.Test/resourceType/objectArray[*].nestedArray[*]",
            "where": {
              "field": "Microsoft.Test/resourceType/objectArray[*].nestedArray[*]",
              "equals": 3
            },
            "greater": 0
          }
        }
      ]
    }
  }
}
```
 
| 外部循环迭代 | 选定值 | 内部循环迭代 | 选定值 |
|:---|:---|:---|:---|
| 1 | `Microsoft.Test/resourceType/objectArray[*].property` => `"value1`</br> `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `1`, `2` | 1 | `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `1` |
| 1 | `Microsoft.Test/resourceType/objectArray[*].property` => `"value1`</br> `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `1`, `2` | 2 | `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `2` |
| 2 | `Microsoft.Test/resourceType/objectArray[*].property` => `"value2`</br> `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `3`, `4` | 1 | `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `3` |
| 2 | `Microsoft.Test/resourceType/objectArray[*].property` => `"value2`</br> `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `3`, `4` | 2 | `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `4` |

### <a name="the-field-function-inside-where-conditions"></a>`field()`条件中的函数 `where`

`field()`函数内的行为 `where` 取决于以下概念：
1. 数组别名解析为从所有数组成员选择的值的集合。
1. `field()` 引用数组别名的函数返回具有选定值的数组。
1. 引用条件中的计数数组别名将 `where` 返回一个集合，其中包含从当前迭代中计算的数组成员中选择的单个值。

此行为意味着，当使用条件内的函数引用计数数组成员时 `field()` `where` ，它将 **返回一个包含单个成员的数组**。 虽然这可能并不直观，但它与这一理念一致：数组别名始终返回选定属性的集合。 下面是一个示例：

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/stringArray[*]",
    "where": {
      "field": "Microsoft.Test/resourceType/stringArray[*]",
      "equals": "[field('Microsoft.Test/resourceType/stringArray[*]')]"
    }
  },
  "equals": 0
}
```

| 迭代 | 表达式值 | `where` 评估结果 |
|:---|:---|:---|
| 1 | `Microsoft.Test/resourceType/stringArray[*]` => `"a"` </br>  `[field('Microsoft.Test/resourceType/stringArray[*]')]` => `[ "a" ]` | `false` |
| 2 | `Microsoft.Test/resourceType/stringArray[*]` => `"b"` </br>  `[field('Microsoft.Test/resourceType/stringArray[*]')]` => `[ "b" ]` | `false` |
| 3 | `Microsoft.Test/resourceType/stringArray[*]` => `"c"` </br>  `[field('Microsoft.Test/resourceType/stringArray[*]')]` => `[ "c" ]` | `false` |

因此，当需要使用函数访问计数数组别名的值时 `field()` ，执行此操作的方法是使用模板函数来包装它 `first()` ：

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/stringArray[*]",
    "where": {
      "field": "Microsoft.Test/resourceType/stringArray[*]",
      "equals": "[first(field('Microsoft.Test/resourceType/stringArray[*]'))]"
    }
  }
}
```

| 迭代 | 表达式值 | `where` 评估结果 |
|:---|:---|:---|
| 1 | `Microsoft.Test/resourceType/stringArray[*]` => `"a"` </br>  `[first(field('Microsoft.Test/resourceType/stringArray[*]'))]` => `"a"` | `true` |
| 2 | `Microsoft.Test/resourceType/stringArray[*]` => `"b"` </br>  `[first(field('Microsoft.Test/resourceType/stringArray[*]'))]` => `"b"` | `true` |
| 3 | `Microsoft.Test/resourceType/stringArray[*]` => `"c"` </br>  `[first(field('Microsoft.Test/resourceType/stringArray[*]'))]` => `"c"` | `true` |

有关有用的示例，请参阅 [计数示例](../concepts/definition-structure.md#count-examples)。

## <a name="modifying-arrays"></a>修改数组

创建或更新期间，[追加](../concepts/effects.md#append)和[修改](../concepts/effects.md#modify)操作会更改资源的属性。 使用数组属性时，这些效果的行为取决于操作是否尝试修改 \[\*\] 别名：

> [!NOTE]
> 目前，预览版中可使用 `modify` 效果和别名。

|Alias |效果 | 业务成效 |
|-|-|-|
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules` | `append` | 如果丢失，Azure Policy 将追加效果详细信息中指定的整个数组。 |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules` | 使用 `add` 操作 `modify` | 如果丢失，Azure Policy 将追加效果详细信息中指定的整个数组。 |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules` | 使用 `addOrReplace` 操作 `modify` | 如果缺失，Azure Policy 将追加效果详细信息中指定的整个数组，或替换现有数组。 |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]` | `append` | Azure Policy 将追加效果详细信息中指定的数组成员。 |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]` | 使用 `add` 操作 `modify` | Azure Policy 将追加效果详细信息中指定的数组成员。 |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]` | 使用 `addOrReplace` 操作 `modify` | Azure Policy 删除所有现有的数组成员，并追加效果详细信息中指定的数组成员。 |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].action` | `append` | Azure Policy 为每个数组成员的 `action` 属性追加一个值。 |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].action` | 使用 `add` 操作 `modify` | Azure Policy 为每个数组成员的 `action` 属性追加一个值。 |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].action` | 使用 `addOrReplace` 操作 `modify` | Azure Policy 追加或替换每个数组成员的现有 `action` 属性。 |

有关详细信息，请参阅[追加示例](../concepts/effects.md#append-examples)。

## <a name="next-steps"></a>后续步骤

- 在 [Azure Policy 示例](../samples/index.md)中查看示例。
- 查看 [Azure Policy 定义结构](../concepts/definition-structure.md)。
- 查看[了解策略效果](../concepts/effects.md)。
- 了解如何[以编程方式创建策略](programmatically-create.md)。
- 了解如何[修正不符合的资源](remediate-resources.md)。
- 参阅[使用 Azure 管理组来组织资源](../../management-groups/overview.md)，了解什么是管理组。
