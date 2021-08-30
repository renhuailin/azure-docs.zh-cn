---
title: 为资源上的数组属性创作策略
description: 了解如何使用数组参数和数组语言表达式，如何计算 [*] 别名，以及如何使用 Azure Policy 定义规则追加元素。
ms.date: 08/17/2021
ms.topic: how-to
ms.openlocfilehash: aefa5439e7176457494d6c21ffb9aa79acc4fd3c
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122323867"
---
# <a name="author-policies-for-array-properties-on-azure-resources"></a>为 Azure 资源上的数组属性创作策略

Azure 资源管理器属性通常定义为字符串和布尔值。 存在一对多关系时，复杂属性将定义为数组。 在 Azure Policy 中，通过以下几种不同的方式来使用数组：

- [定义参数](../concepts/definition-structure.md#parameters)的类型，用于提供多个选项
- 使用条件 in 或 notIn 的[策略规则](../concepts/definition-structure.md#policy-rule)的一部分 
- 用作统计有多少数组成员符合条件的策略规则的一部分
- 在 [append](../concepts/effects.md#append) 和 [modify](../concepts/effects.md#modify) 效果中，用于更新现有数组

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
- REST API：在 PUT [create](/rest/api/policy/policyassignments/create) 操作中，作为请求正文（作为 properties.parameters 属性的值）的一部分

## <a name="using-arrays-in-conditions"></a>在条件中使用数组

### <a name="in-and-notin"></a>In 和 notIn

`in` 和 `notIn` 条件仅适用于数组值。 它们检查数组中是否存在某个值。 数组可以是文本 JSON 数组，也可以是对数组参数的引用。 例如：

```json
{
      "field": "tags.environment",
      "in": [ "dev", "test" ]
}
```

```json
{
      "field": "location",
      "notIn": "[parameters('allowedLocations')]"
}
```

### <a name="value-count"></a>值计数

[值计数](../concepts/definition-structure.md#value-count)表达式统计有多少数组成员满足条件。 它提供了一种方法来多次评估同一条件，每次迭代都使用不同的值。 例如，以下条件检查资源名称是否与模式数组中的任何模式匹配：

```json
{
    "count": {
        "value": [ "test*", "dev*", "prod*" ],
        "name": "pattern",
        "where": {
            "field": "name",
            "like": "[current('pattern')]"
        }
    },
    "greater": 0
}
```

为了对表达式求值，Azure Policy 将计算 `where` 条件三次（每个 `[ "test*", "dev*", "prod*" ]` 成员一次），统计其计算结果为 `true` 的次数。 每次迭代时，会将当前数组成员的值与 `count.name` 所定义的 `pattern` 索引名称进行配对。 然后，可通过调用特殊模板函数 `current('pattern')` 在 `where` 条件内引用此值。

| 迭代 | `current('pattern')` 返回的值 |
|:---|:---|
| 1 | `"test*"` |
| 2 | `"dev*"` |
| 3 | `"prod*"` |

仅当得到的计数大于 0 时，条件才为 true。

若要使上述条件更通用，请使用参数引用而非文本数组：

```json
{
    "count": {
        "value": "[parameters('patterns')]",
        "name": "pattern",
        "where": {
            "field": "name",
            "like": "[current('pattern')]"
        }
    },
    "greater": 0
}
```

如果值计数表达式不在任何其他 count 表达式下，则 `count.name` 是可选的，可以不带任何参数使用 `current()` 函数 ：

```json
{
    "count": {
        "value": "[parameters('patterns')]",
        "where": {
            "field": "name",
            "like": "[current()]"
        }
    },
    "greater": 0
}
```

“值计数”还支持复杂对象的数组，因而允许更复杂的条件。 例如，下面的条件定义了每个名称模式所需的标记值，并检查资源名称是否与模式匹配但没有所需的标记值：

```json
{
    "count": {
        "value": [
            { "pattern": "test*", "envTag": "dev" },
            { "pattern": "dev*", "envTag": "dev" },
            { "pattern": "prod*", "envTag": "prod" },
        ],
        "name": "namePatternRequiredTag",
        "where": {
            "allOf": [
                {
                    "field": "name",
                    "like": "[current('namePatternRequiredTag').pattern]"
                },
                {
                    "field": "tags.env",
                    "notEquals": "[current('namePatternRequiredTag').envTag]"
                }
            ]
        }
    },
    "greater": 0
}
```

如需有用的示例，请参阅[值计数示例](../concepts/definition-structure.md#value-count-examples)。

## <a name="referencing-array-resource-properties"></a>引用数组资源属性

许多用例需要使用所评估资源中的数组属性。 某些方案需要引用整个数组（例如，检查它的长度）。 其他方案需要对每个单独的数组成员应用条件（例如，确保所有防火墙规则阻止从 Internet 进行访问）。 了解 Azure Policy 引用资源属性的不同方式，以及这些引用在引用数组属性时的行为方式，是编写用于涵盖这些方案的条件的关键。

### <a name="referencing-resource-properties"></a>引用资源属性

Azure Policy 可以使用[别名](../concepts/definition-structure.md#aliases)引用资源属性。有两种方法可用于在 Azure Policy 中引用资源属性的值：

- 使用[字段](../concepts/definition-structure.md#fields)条件来检查是否所有选定的资源属性都满足某个条件。 示例：

  ```json
  {
    "field" : "Microsoft.Test/resourceType/property",
    "equals": "value"
  }
  ```

- 使用 `field()` 函数访问属性的值。 示例：

  ```json
  {
    "value": "[take(field('Microsoft.Test/resourceType/property'), 7)]",
    "equals": "prefix_"
  }
  ```

字段条件具有隐式的“所有成员”行为。 如果别名表示值的集合，则它会检查是否所有单个值都满足该条件。 `field()` 函数按原样返回别名所表示的值，这些值随后可由其他模板函数操作。

### <a name="referencing-array-fields"></a>引用数组字段

数组资源属性由两种不同类型的别名表示。 一种是一个“普通”别名，另一种是附加了 `[*]` 的[数组别名](../concepts/definition-structure.md#understanding-the--alias)：

- `Microsoft.Test/resourceType/stringArray`
- `Microsoft.Test/resourceType/stringArray[*]`

#### <a name="referencing-the-array"></a>引用数组

第一种别名表示单个值，即请求内容中 `stringArray` 属性的值。 由于该属性的值是一个数组，因此在策略条件中并没有用。 例如：

```json
{
  "field": "Microsoft.Test/resourceType/stringArray",
  "equals": "..."
}
```

此条件将整个 `stringArray` 数组与单个字符串值进行比较。 大多数条件（包括 `equals`）仅接受字符串值，因此在将数组与字符串进行比较时没有多大用处。 引用数组属性的主要方案在检查该属性是否存在时十分有用：

```json
{
  "field": "Microsoft.Test/resourceType/stringArray",
  "exists": "true"
}
```

在使用 `field()` 函数的情况下，返回的值是请求内容中的数组，该数组随后可与接受数组参数的任何[受支持模板](../concepts/definition-structure.md#policy-functions)函数结合使用。 例如，以下条件检查 `stringArray` 的长度是否大于 0：

```json
{
  "value": "[length(field('Microsoft.Test/resourceType/stringArray'))]",
  "greater": 0
}
```

#### <a name="referencing-the-array-members-collection"></a>引用数组成员集合

使用 `[*]` 语法的别名表示从数组属性中选择的属性值的集合，这不同于选择数组属性本身。 如果使用的是 `Microsoft.Test/resourceType/stringArray[*]`，它将返回一个包含 `stringArray` 的所有成员的集合。 如前所述，`field` 条件会检查所有选定的资源属性是否满足该条件，因此仅当 `stringArray` 的所有成员均等于 "value" 时，以下条件才为 true。

```json
{
  "field": "Microsoft.Test/resourceType/stringArray[*]",
  "equals": "value"
}
```

如果数组包含对象，则可以使用 `[*]` 别名从每个数组成员选择特定属性的值。 示例：

```json
{
  "field": "Microsoft.Test/resourceType/objectArray[*].property",
  "equals": "value"
}
```

如果 `objectArray` 中的所有 `property` 属性的值均等于 `"value"`，则此条件为 true。 有关更多示例，请参阅[更多 \[\*\] 别名示例](#additional--alias-examples)。

使用 `field()` 函数引用数组别名时，返回的值是全部所选值的数组。 此行为意味着 `field()` 函数的常见用例（将模板函数应用于资源属性值的功能）非常有限。 在这种情况下可使用的模板函数只能是接受数组参数的模板函数。 例如，可以通过 `[length(field('Microsoft.Test/resourceType/objectArray[*].property'))]` 获取数组的长度。 但是，更复杂的方案（例如，将模板函数应用于每个数组成员，并将其与所需的值进行比较）仅在使用 `count` 表达式时才可行。 有关详细信息，请参阅[字段计数表达式](#field-count-expressions)。

概括而言，请参阅以下示例资源内容和通过各种别名返回的所选值：

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

对示例资源内容使用字段条件时，结果如下所示：

| Alias | 所选值 |
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

对示例资源内容使用 `field()` 函数时，结果如下所示：

| 表达式 | 返回的值 |
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

### <a name="field-count-expressions"></a>字段计数表达式

[字段计数](../concepts/definition-structure.md#field-count)表达式统计有多少数组成员满足某个条件，并将计数与目标值进行比较。 与 `field` 条件相比，`Count` 对于评估数组而言更直观、更通用。 语法为：

```json
{
  "count": {
    "field": <[*] alias>,
    "where": <optional policy condition expression>
  },
  "equals|greater|less|any other operator": <target value>
}
```

在未带 `where` 条件的情况下使用时，`count` 只返回数组的长度。 在使用上一节中的示例资源内容的情况下，以下 `count` 表达式的求值结果为 `true`，因为 `stringArray` 包含三个成员：

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/stringArray[*]"
  },
  "equals": 3
}
```

此行为也适用于嵌套数组。 例如，以下 `count` 表达式的计算结果为 `true`，因为在 `nestedArray` 数组中有四个数组成员：

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/objectArray[*].nestedArray[*]"
  },
  "greaterOrEquals": 4
}
```

`count` 是在 `where` 条件中发挥作用的。 如果指定了它，Azure Policy 将枚举数组成员，并会根据条件计算每个成员，统计有多少个数组成员的计算结果为 `true`。 具体而言，在 `where` 条件计算的每次迭代中，Azure Policy 将选择单个数组成员 i，并根据 `where` 条件计算资源内容，就像 i 是该数组的唯一成员一样 。 在每次迭代中仅有一个数组成员可用，这提供了将复杂条件应用于每一单个数组成员的方法。

示例：

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

为了对 `count` 表达式求值，Azure Policy 将计算 `where` 条件 3 次（每个 `stringArray` 成员一次），统计其计算结果为 `true` 的次数。
如果 `where` 条件引用 `Microsoft.Test/resourceType/stringArray[*]` 数组成员，而不是选择 `stringArray` 的所有成员，则它每次只选择单个数组成员：

| 迭代 | 所选 `Microsoft.Test/resourceType/stringArray[*]` 值 | `where` 求值结果 |
|:---|:---|:---|
| 1 | `"a"` | `true` |
| 2 | `"b"` | `false` |
| 3 | `"c"` | `false` |

`count` 返回 `1`。

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

| 迭代 | 所选值 | `where` 求值结果 |
|:---|:---|:---|
| 1 | `Microsoft.Test/resourceType/objectArray[*].property` => `"value1"` </br> `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `1`, `2` | `false` |
| 2 | `Microsoft.Test/resourceType/objectArray[*].property` => `"value2"` </br> `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `3`, `4`| `true` |

`count` 返回 `1`。

根据整个请求内容（包含仅对当前枚举的数组成员进行的更改）对 `where` 表达式求值意味着，`where` 条件还可以引用数组之外的字段：

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/objectArray[*]",
    "where": {
      "field": "tags.env",
      "equals": "prod"
    }
  },
  "equals": 0
}
```

| 迭代 | 所选值 | `where` 求值结果 |
|:---|:---|:---|
| 1 | `tags.env` => `"prod"` | `true` |
| 2 | `tags.env` => `"prod"` | `true` |

嵌套 count 表达式可用于将条件应用于嵌套数组字段。 例如，下面的条件检查 `objectArray[*]` 数组是否恰好有两个成员，且 `nestedArray[*]` 包含一个或多个成员：

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/objectArray[*]",
    "where": {
      "count": {
        "field": "Microsoft.Test/resourceType/objectArray[*].nestedArray[*]"
      },
      "greaterOrEquals": 1
    }
  },
  "equals": 2
}
```

| 迭代 | 所选值 | 嵌套 count 计算结果 |
|:---|:---|:---|
| 1 | `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `1`, `2` | `nestedArray[*]` 有两个成员 => `true` |
| 2 | `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `3`, `4` | `nestedArray[*]` 有两个成员 => `true` |

由于 `objectArray[*]` 的两个成员都有一个子数组 `nestedArray[*]`，而子数组有两个成员，因此外层 count 表达式返回 `2`。

更复杂的示例：检查 `objectArray[*]` 数组是否正好有两个成员，而 `nestedArray[*]` 具有等于 `2` 或 `3` 的成员：

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/objectArray[*]",
    "where": {
      "count": {
        "field": "Microsoft.Test/resourceType/objectArray[*].nestedArray[*]",
        "where": {
            "field": "Microsoft.Test/resourceType/objectArray[*].nestedArray[*]",
            "in": [ 2, 3 ]
        }
      },
      "greaterOrEquals": 1
    }
  },
  "equals": 2
}
```

| 迭代 | 所选值 | 嵌套 count 计算结果
|:---|:---|:---|
| 1 | `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `1`, `2` | `nestedArray[*]` 包含 `2` => `true` |
| 2 | `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `3`, `4` | `nestedArray[*]` 包含 `3` => `true` |

由于 `objectArray[*]` 的两个成员都有一个包含 `2` 或 `3` 的子数组 `nestedArray[*]`，因此外层 count 表达式将返回 `2`。

> [!NOTE]
> 嵌套字段计数表达式只能引用嵌套数组。 例如，引用 `Microsoft.Test/resourceType/objectArray[*]` 的 count 表达式可以具有以嵌套数组 `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` 为目标的嵌套 count，但它不能具有以 `Microsoft.Test/resourceType/stringArray[*]` 为目标的嵌套 count 表达式。

#### <a name="accessing-current-array-member-with-template-functions"></a>使用模板函数访问当前数组成员

使用模板函数时，请使用 `current()` 函数访问当前数组成员的值或其任何属性的值。 若要访问当前数组成员的值，请将 `count.field` 中定义的别名或其任何子级别名作为参数传递给 `current()` 函数。 例如：

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/objectArray[*]",
    "where": {
        "value": "[current('Microsoft.Test/resourceType/objectArray[*].property')]",
        "like": "value*"
    }
  },
  "equals": 2
}

```

| 迭代 | `current()` 返回的值 | `where` 求值结果 |
|:---|:---|:---|
| 1 | `objectArray[*]` 的第一个成员中的 `property` 的值：`value1` | `true` |
| 2 | `objectArray[*]` 的第一个成员中的 `property` 的值：`value2` | `true` |

#### <a name="the-field-function-inside-where-conditions"></a>where 条件中的 field 函数

还可以使用 `field()` 函数来访问当前数组成员的值，前提是 count 表达式不在某个存在条件中（`field()` 函数始终引用 if 条件中计算的资源）  。 引用评估的数组时 `field()` 的行为基于以下概念：

1. 数组别名解析为从所有数组成员选择的值的集合。
1. 引用数组别名的 `field()` 函数会返回具有所选值的数组。
1. 在 `where` 条件内引用计数数组别名将返回一个集合，该集合中包含从当前迭代中评估的数组成员中选择的单个值。

此行为意味着，当使用 `field()` 函数在 `where` 条件中引用计数数组成员时，它将返回一个具有单个成员的数组。 虽然此行为可能并不直观，但它与下面的理念一致：数组别名始终返回所选属性的集合。 下面是一个示例：

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

| 迭代 | 表达式值 | `where` 求值结果 |
|:---|:---|:---|
| 1 | `Microsoft.Test/resourceType/stringArray[*]` => `"a"` </br>  `[field('Microsoft.Test/resourceType/stringArray[*]')]` => `[ "a" ]` | `false` |
| 2 | `Microsoft.Test/resourceType/stringArray[*]` => `"b"` </br>  `[field('Microsoft.Test/resourceType/stringArray[*]')]` => `[ "b" ]` | `false` |
| 3 | `Microsoft.Test/resourceType/stringArray[*]` => `"c"` </br>  `[field('Microsoft.Test/resourceType/stringArray[*]')]` => `[ "c" ]` | `false` |

因此，当需要使用 `field()` 函数访问计数数组别名的值时，执行此操作的方法是使用 `first()` 模板函数来包装它：

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

| 迭代 | 表达式值 | `where` 求值结果 |
|:---|:---|:---|
| 1 | `Microsoft.Test/resourceType/stringArray[*]` => `"a"` </br>  `[first(field('Microsoft.Test/resourceType/stringArray[*]'))]` => `"a"` | `true` |
| 2 | `Microsoft.Test/resourceType/stringArray[*]` => `"b"` </br>  `[first(field('Microsoft.Test/resourceType/stringArray[*]'))]` => `"b"` | `true` |
| 3 | `Microsoft.Test/resourceType/stringArray[*]` => `"c"` </br>  `[first(field('Microsoft.Test/resourceType/stringArray[*]'))]` => `"c"` | `true` |

如需有用的示例，请参阅[字段计数示例](../concepts/definition-structure.md#field-count-examples)。

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

## <a name="additional--alias-examples"></a>其他 [*] 别名示例

建议使用[字段计数表达式](#field-count-expressions)来检查请求内容中某个数组的“所有”成员或“任何”成员是否符合条件。 但对于一些简单的情况，可以通过将字段访问器与数组别名结合使用来实现相同的结果，如[引用数组成员集合](#referencing-the-array-members-collection)中所述。 对于超出允许的 count 表达式限制的策略规则，此模式可能非常有用。 下面是常见用例的示例：

方案表的示例策略规则如下：

```json
"policyRule": {
    "if": {
        "allOf": [
            {
                "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules",
                "exists": "true"
            },
            <-- Condition (see table below) -->
        ]
    },
    "then": {
        "effect": "[parameters('effectType')]"
    }
}
```

对于以下方案表，ipRules 数组如下所示：

```json
"ipRules": [
    {
        "value": "127.0.0.1",
        "action": "Allow"
    },
    {
        "value": "192.168.1.1",
        "action": "Allow"
    }
]
```

对于下面的每个条件示例，请将 `<field>` 替换为 `"field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].value"`。

以下结果是条件和示例策略规则与上述现有值的数组的组合带来的结果：

|条件 |业务成效 | 场景 |说明 |
|-|-|-|-|
|`{<field>,"notEquals":"127.0.0.1"}` |无 |无匹配 |一个数组元素的计算结果为 false (127.0.0.1 != 127.0.0.1)，另一个的计算结果为 true (127.0.0.1 != 192.168.1.1)，因此 notEquals 条件为 false，不会触发该效果。 |
|`{<field>,"notEquals":"10.0.4.1"}` |策略效果 |无匹配 |两个数组元素的计算结果均为 true（10.0.4.1 != 127.0.0.1 和 10.0.4.1 != 192.168.1.1），因此 notEquals 条件为 true，会触发该效果。 |
|`"not":{<field>,"notEquals":"127.0.0.1" }` |策略效果 |一个或多个匹配 |一个数组元素的计算结果为 false (127.0.0.1 != 127.0.0.1)，另一个的计算结果为 true (127.0.0.1 != 192.168.1.1)，因此 notEquals 条件为 false。 逻辑运算符的计算结果为 true（不为 false），因此会触发该效果。 |
|`"not":{<field>,"notEquals":"10.0.4.1"}` |无 |一个或多个匹配 |两个数组元素的计算结果均为 true（10.0.4.1 != 127.0.0.1 和 10.0.4.1 != 192.168.1.1），因此 notEquals 条件为 true。 逻辑运算符的计算结果为 false（不为 true），因此不会触发该效果。 |
|`"not":{<field>,"Equals":"127.0.0.1"}` |策略效果 |并非全部匹配 |一个数组元素的计算结果为 true (127.0.0.1 == 127.0.0.1)，另一个的计算结果为 false (127.0.0.1 == 192.168.1.1)，因此 Equals 条件为 false。 逻辑运算符的计算结果为 true（不为 false），因此会触发该效果。 |
|`"not":{<field>,"Equals":"10.0.4.1"}` |策略效果 |并非全部匹配 |两个数组元素的计算结果均为 false（10.0.4.1 == 127.0.0.1 和 10.0.4.1 == 192.168.1.1），因此 Equals 条件为 false。 逻辑运算符的计算结果为 true（不为 false），因此会触发该效果。 |
|`{<field>,"Equals":"127.0.0.1"}` |无 |全部匹配 |一个数组元素的计算结果为 true (127.0.0.1 == 127.0.0.1)，另一个的计算结果为 false (127.0.0.1 == 192.168.1.1)，因此 Equals 条件为 false，不会触发该效果。 |
|`{<field>,"Equals":"10.0.4.1"}` |无 |全部匹配 |两个数组元素的计算结果均为 false（10.0.4.1 == 127.0.0.1 和 10.0.4.1 == 192.168.1.1），因此 Equals 条件为 false，不会触发该效果。 |

## <a name="next-steps"></a>后续步骤

- 在 [Azure Policy 示例](../samples/index.md)中查看示例。
- 查看 [Azure Policy 定义结构](../concepts/definition-structure.md)。
- 查看[了解策略效果](../concepts/effects.md)。
- 了解如何[以编程方式创建策略](programmatically-create.md)。
- 了解如何[修正不符合的资源](remediate-resources.md)。
- 参阅[使用 Azure 管理组来组织资源](../../management-groups/overview.md)，了解什么是管理组。
